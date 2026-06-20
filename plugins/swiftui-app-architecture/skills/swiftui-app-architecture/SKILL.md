---
name: swiftui-app-architecture
description: "Conventions for building and extending a native iOS app in SwiftUI — project structure, a token-based design system, Observable state management, a generic networking layer, type-erased navigation, Codable models, runtime localization, xcconfig-based secrets, and a Foundation-clean testable core package. Use this whenever working in or scaffolding a SwiftUI iOS codebase: adding a screen, a network call or API manager, a view model or app-wide store, a design token or styled component, a navigation route, or a Codable model — even if the user never says the word architecture. Apply it when deciding where a new file belongs, how to style a view, how to structure state with the Observation framework, or how to keep logic unit-testable in a SwiftUI app."
---

# SwiftUI App Architecture

Opinionated, reusable conventions for a native SwiftUI iOS app: how the codebase is structured and the patterns to follow when adding a screen, a network call, a model, or a styled component. Read this before writing UI, networking, or state code so new code matches the existing shape instead of inventing a parallel one.

**Baseline:** SwiftUI only. Minimum deployment iOS 17 (raise the floor per project if you like, but keep 17 as the minimum so the Observation framework and modern SwiftUI APIs are always available). Dependencies via Swift Package Manager — no CocoaPods unless a single SDK genuinely requires it. Newer-OS APIs are adopted behind `if #available` checks rather than raising the global floor.

Names used below (`App*`, `AppCore`, `ItemsAPIManager`, etc.) are neutral placeholders for whatever prefix and domain the project uses — substitute the real ones; the *patterns* are the point.

---

## 1. Two build surfaces

Split the codebase so pure logic is unit-testable without a simulator:

- **The app target** — all UI, SwiftUI views, and SDK glue (anything that touches UIKit, CoreLocation, push, maps, or any vendor SDK).
- **A Foundation-clean SPM package** (call it `AppCore`) whose `Package.swift` globs only Foundation-only shared code — models, JSON coders, pure transforms, pure parsers — so it builds and tests with `swift test`, no device needed.

Rule of thumb: **to make new logic testable, keep it Foundation-only and place it under a path the package globs** (e.g. `Models/`). UIKit/SDK code stays app-target-only because it cannot compile headless. Put the testable seam at the pure boundary (decode/transform), with thin I/O wrappers above it.

---

## 2. Directory map

```
App/            # @main entry, AppDelegate (push/SDK init), RootView (auth/content gate)
DesignSystem/   # Tokens (Colors, Typography, Theme) + styled App* components
Views/          # Screens grouped by feature (Auth/, Home/, Settings/, …)
Components/     # Small cross-feature UI helpers
Models/         # Codable DTOs + payloads (package-shared, unit-tested)
Services/       # API/ (transport + *APIManagers) and non-API SDK wrappers (each behind a protocol)
Manager/        # App-wide @Observable singletons (stores), Keychain, caches, routers
Utils/          # Navigatable protocol, LocalizationManager, AppStorageKeys, Constants, Log
Extensions/     # Small View/Foundation extensions
Resources/      # Asset catalogs, Localizable.xcstrings, plist
```

**Placement guide:** data type → `Models/`; a network call → an `*APIManager` in `Services/API/`; app-wide mutable state → an `@Observable` singleton in `Manager/`; a reusable visual primitive → `DesignSystem/`; a feature screen → `Views/<Feature>/`.

---

## 3. App shell & lifecycle

The `@main` `App` wires the app delegate (push/SDK init), provisions SDK keys, and injects the app-wide store once at the root. A `RootView` "gate" branches on auth/account status to pick the top-level UI, and applies global environment (theme, locale) in one place.

```swift
@main struct MyApp: App {
    @UIApplicationDelegateAdaptor(AppDelegate.self) private var appDelegate
    init() {
        // Provision SDK keys here; e.g. if tokens live in the Keychain, disable the cookie store:
        // HTTPCookieStorage.shared.cookieAcceptPolicy = .never
    }
    var body: some Scene {
        WindowGroup { RootView().environment(AppState.shared) }
    }
}

struct RootView: View {
    @Environment(AppState.self) private var app
    @AppStorage(AppStorageKeys.theme) private var theme: AppTheme = .system
    @Environment(LocalizationManager.self) private var localization

    var body: some View {
        gate
            .preferredColorScheme(theme.colorScheme)
            .environment(\.locale, localization.language.locale)
    }

    @ViewBuilder private var gate: some View {
        if let account = app.activeAccount {
            switch account.status {
            case .active:  TabBarView()
            case .pending: PendingApprovalView()
            default:       LoginView()
            }
        } else {
            LoginView()
        }
    }
}
```

---

## 4. Design system — tokens first

Lives in `DesignSystem/`. **Every color and font goes through a token — never a literal.** Colors come from an asset catalog with Swift symbol generation enabled, so they flip light/dark automatically and stay compile-checked.

```swift
enum AppColor {
    // Static (same in light & dark) — uses the asset catalog's generated ColorResource symbol
    static let brand = Color(.brand)
    // Semantic (flip light <-> dark via the asset catalog)
    static let bg     = Color(.bg)
    static let card   = Color(.card)
    static let ink    = Color(.ink)
    static let muted  = Color(.muted)
    static let border = Color(.border)
}

enum AppFont {           // one named style per use; pick one font family and stick to it
    static let titleBar    = Font.system(.title2, weight: .semibold)
    static let sectionTitle = Font.system(.headline)
    static let rowTitle    = Font.system(.body, weight: .medium)
    static let body        = Font.system(.body)
    static let caption     = Font.system(.caption)
}

enum AppTheme: String, Codable, CaseIterable, Sendable {
    case system, light, dark
    var colorScheme: ColorScheme? {
        switch self { case .system: nil; case .light: .light; case .dark: .dark }
    }
}
```

Add a color by adding a color set (with a dark variant) to the asset catalog — keep "Generate Swift symbols" on for the catalog so the typed `.brand` / `.card` / … symbols exist — then expose it as a token on `AppColor`. **Never** hardcode `Color(red:…)` or hex in a view — go through a token so dark mode and theming stay correct.

Styled components (`AppButton`, `AppCard`, `AppPill`, grouped settings rows, status pills, etc.) exist but are **not mandatory** — see §5.

---

## 5. View & styling conventions

**Style with tokens, not components.** Most feature views compose raw tokens directly; reach for container components only where they genuinely cut repetition. Don't wrap a screen in cards just because the component exists.

```swift
Text(item.title)
    .font(AppFont.rowTitle)
    .foregroundStyle(AppColor.ink)
    .padding(.horizontal, 16)
    .background(AppColor.card, in: .rect(cornerRadius: 12))
```

**Top/bottom control bars go through one helper** (e.g. `smartSafeAreaBar`) that gives newer OS a floating glass bar and older OS a material-backed `safeAreaInset` from a single call — concentrate `if #available` adoption there, not scattered through screens.

**Extract view-builder closures into named functions and pass them by reference.** Bodies stay flat; `@ViewBuilder` methods and plain functions are handed to `.task`/`.onChange`/`.navigationDestination` by name, not inline.

```swift
var body: some View {
    NavigationStack(path: $model.path) {
        List { ProfileSection(); SettingsSection() }
            .navigationDestination(for: Navigation.self, destination: navigationView)
            .onChange(of: app.activeAccountId, onAccountChange)
            .task(model.load)
    }
}
@ViewBuilder private func ProfileSection() -> some View { Section { /* … */ } }
private func onAccountChange() { Task { await model.load() } }
```

---

## 6. Navigation

Each `NavigationStack` is driven by a typed path on its screen model. Destinations are an enum conforming to a **`Navigatable`** protocol (a `key` plus a `@ViewBuilder` returning the destination). A Codable type-erased `Navigation` wrapper (key + payload) lets paths persist.

```swift
enum SettingsRoute: Navigatable {
    case profile, language, appearance
    var key: String {
        switch self { case .profile: "profile"; case .language: "language"; case .appearance: "appearance" }
    }
    @ViewBuilder func destination(path: Binding<[Navigation]>) -> some View {
        switch self {
        case .profile:    ProfileView()
        case .language:   LanguageView()
        case .appearance: AppearanceView()
        }
    }
}

// Drive it:
NavigationStack(path: $model.path) {
    List { /* rows */ }.navigationDestination(for: Navigation.self, destination: navigationView)
}
// Push:  model.path.navigate(SettingsRoute.profile)
// Pop:   model.path.removeLast()
```

To add a screen: add a case to the relevant route enum, give it a `key`, return its view, and push with `path.navigate(...)`.

---

## 7. State management — Observation framework everywhere

Use `@Observable` for **both** app-wide state and screen state. Do not use `ObservableObject` / `@Published` / `@StateObject`.

**App-wide mutable state → an `@Observable` singleton in `Manager/`.** Expose `.shared` for non-View access and inject it into the environment once at the root for views.

```swift
@Observable @MainActor final class AppState {
    static let shared = AppState()
    private(set) var accounts: [Account] = []
    var activeAccountId: String? { didSet { persistActiveId() } }
    var activeAccount: Account? { accounts.first { $0.id == activeAccountId } }
    var isReadOnlyMode: Bool { activeAccount?.role == .viewer }   // gate privileged UI everywhere off one flag
    private init() { load() }
}
```

**Screen state → an `@Observable` model owned by the view via `@State`** (not `@StateObject`). Async methods surface failures into an `errorMessage` property.

```swift
@Observable @MainActor final class ItemsModel {
    var items: [Item] = []
    var isLoading = false
    var errorMessage: String?

    func load() async {
        isLoading = true; errorMessage = nil
        defer { isLoading = false }
        do { items = try await ItemsAPIManager.shared.getItems() }
        catch { errorMessage = error.localizedDescription }
    }
}

struct ItemsView: View {
    @State private var model = ItemsModel()
    var body: some View {
        List(model.items) { ItemRow($0) }
            .overlay { if model.isLoading { ProgressView() } }
            .task(model.load)
    }
}
```

**Bindings** to an `@Observable` model's properties come from `@Bindable` — a local in the body, or a `@Bindable` property in a child view:

```swift
var body: some View {
    @Bindable var model = model
    TextField("Search", text: $model.query)
        .onChange(of: model.query, model.search)
}
```

**Injection across the tree:** `.environment(model)` to provide, `@Environment(ItemsModel.self) private var model` to read.

**Small persisted prefs → `@AppStorage`,** keyed by string constants in one file (`Utils/AppStorageKeys.swift`). Non-View code reads the same key via `UserDefaults.standard`.

**Debounced search → the standard `Task` cancel pattern:** cancel the prior task, sleep, and check `Task.isCancelled` both before and after the await.

```swift
private var searchTask: Task<Void, Never>?
func search() {
    searchTask?.cancel()
    searchTask = Task {
        try? await Task.sleep(for: .milliseconds(300))
        if Task.isCancelled { return }
        let hits = (try? await ItemsAPIManager.shared.search(query)) ?? []
        if Task.isCancelled { return }
        results = hits
    }
}
```

---

## 8. Networking

One generic transport plus an `Endpoint` describing each request. **Never network from a view or a model directly** — go through an API manager (§9).

```swift
func call<T: Decodable>(_ endpoint: Endpoint,
                        allowAutoRefresh: Bool = true,
                        didRetry: Bool = false,
                        decodeTo type: T.Type) async throws -> T

protocol Endpoint {
    var baseURL: URL { get }            // defaults to Constants.baseURL
    var path: String { get }
    var method: HTTPMethod { get }      // default .get
    var headers: [String: String]? { get }
    var body: Data? { get }
}
enum HTTPMethod: String { case get, post, patch, put, delete }
```

Responses share one envelope, unwrapped by the transport (adapt the shape to the backend):

```swift
struct Response<T: Decodable>: Decodable {
    var success: Bool?
    var message: String?
    var data: T?
}
// if response.success == true, let data = response.data { return data }
// else { throw AppError.server(response.message) }
```

- **Auth:** attach a bearer token from the active session in each endpoint's `headers`. On `401` with `allowAutoRefresh`, refresh once (serialize refreshes per account with an `actor` coordinator) and replay the call once; a `didRetry` flag guards against loops.
- **Offline:** cache GET responses per account and serve them as a fallback on transport failure.
- **Errors:** wrap server messages in a presentable error type (an `AppError` enum or an `NSError.localized` helper) so `error.localizedDescription` is user-facing.
- **Logging:** gate verbose cURL/JSON logging to `DEBUG`.

---

## 9. API managers

One singleton per backend area — a thin wrapper that builds an `Endpoint`, calls the transport, and unwraps `Response<T>`. Add a new area by following this shape.

```swift
final class ItemsAPIManager {
    static let shared = ItemsAPIManager()
    private let network = DefaultNetworkManager()

    func getItems(query: String? = nil) async throws -> [Item] {
        let res = try await network.call(Endpoint.list(query: query),
                                         decodeTo: Response<[Item]>.self)
        if res.success == true { return res.data ?? [] }
        throw AppError.server(res.message)
    }

    enum Endpoint: Endpoint { /* cases compute path / method / body */ }
}
```

---

## 10. Models & JSON

DTOs live in `Models/`, are `Codable` and `Sendable`, and are **package-shared** so they're unit-tested (§14). Write PATCH payloads with **optional fields** so an omitted key is left out instead of sent as `null`:

```swift
struct ItemPatch: Encodable, Sendable {
    var title: String?     // nil -> omitted, not null
    var done: Bool?
}
```

Route **all** encode/decode through shared coders configured once for the backend's date format (e.g. ISO-8601 with a fractional-seconds-then-plain fallback) — `JSONDecoder.app` / `JSONEncoder.app`, never a fresh `JSONDecoder()` — so date handling matches everywhere.

---

## 11. Services (non-API)

**Wrap each external SDK behind a protocol, and resolve the active implementation at runtime** (e.g. from a provider toggle). This keeps vendors swappable and call sites testable.

```swift
protocol PlaceSearchProviding { func search(_ query: String) async throws -> [Place] }
struct VendorAPlaceSearch: PlaceSearchProviding { /* wraps SDK A */ }
struct VendorBPlaceSearch: PlaceSearchProviding { /* HTTP */ }
enum PlaceSearch { static func current() -> any PlaceSearchProviding { /* resolve from toggle */ } }
```

Pull **pure, testable logic out of SDK glue** (a registration diff, a deep-link parser) into Foundation-only types under the package, leaving a thin I/O wrapper above. `CLLocationManager`-style wrappers publish their state via `@Observable` properties.

---

## 12. Localization

Resolve strings from the chosen language **at runtime** by overriding the main bundle's class, so a language switch applies without relaunch. Strings live in a string catalog (`Localizable.xcstrings`). **Every user-facing string must exist in every supported language.**

```swift
enum AppLanguage: String, CaseIterable, Identifiable {
    case english = "en", spanish = "es"   // add as needed
    var id: String { rawValue }
    var locale: Locale { Locale(identifier: rawValue) }
}
// In views: Text("greeting.hello")  — a LocalizedStringKey looks the key up.
// When you need a resolved String: key.string (a LocalizedStringKey extension).
```

If language is a server-synced preference, PATCH it on change so it follows the account across devices.

---

## 13. Build, config & secrets

- **Dependencies: SPM only.** Adding a CocoaPods-only SDK is a deliberate, sizeable change.
- **Config flows xcconfig → Info.plist → runtime.** A gitignored `Secrets.xcconfig` is `#include?`-ed by `Debug.xcconfig` / `Release.xcconfig`; `Info.plist` maps each var via `$(VAR_NAME)`; code reads it with `Bundle.main.object(forInfoDictionaryKey:)`. **Never commit a real secret.**
- **Keep the `AppCore` package Foundation-clean:** glob only `Models/`, the JSON coders, and pure helpers. UIKit/SDK code stays in the app target.

---

## 14. Testing

The package's test target uses `XCTest` + `@testable import AppCore`, exercising contract-critical **pure** logic: DTO decode/encode (through the shared coders), pure diffs, deep-link parsing, geometry. UI and SDK paths (maps, network I/O, push) are **build- and simulator-verified**, not unit-tested. Mirror that split: pure + tested in the package, thin + simulator-verified in the app target.

```swift
func testPatchOmitsNilFields() throws {
    var patch = ItemPatch(); patch.done = true
    let obj = try XCTUnwrap(
        JSONSerialization.jsonObject(with: try JSONEncoder.app.encode(patch)) as? [String: Any]
    )
    XCTAssertEqual(obj["done"] as? Bool, true)
    XCTAssertNil(obj["title"], "nil PATCH fields must be omitted, not sent as null")
}
```

---

## 15. Conventions cheat-sheet

- **Color / font** → always a token (`AppColor` / `AppFont`), never a literal.
- **Style views with raw tokens + a button component;** use heavier container components only where they earn it.
- **Top/bottom bars** → one safe-area-bar helper. **Big bodies** → extract `@ViewBuilder` methods and pass functions by reference to `.task` / `.onChange` / `.navigationDestination`.
- **A screen** → a `Navigatable` route case + a typed path. **Screen state** → an `@Observable` model owned via `@State`. **App state** → an `@Observable` singleton. **Small prefs** → `@AppStorage` from a keys file.
- **A network call** → an `*APIManager` + `Endpoint` returning the unwrapped envelope; never network from a view or model.
- **JSON** → the shared coders; payloads use optional fields.
- **Strings** → string catalog in every language; `Text("key")`.
- **Secrets** → xcconfig only, never committed. **Deps** → SPM.
- **Testable logic** → Foundation-only, under a package-globbed path, covered by the package's tests.
