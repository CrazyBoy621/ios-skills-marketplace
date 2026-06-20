# ios-skills

A personal Claude Code plugin marketplace.

## Plugins
- **swiftui-app-architecture** — conventions and architecture for native SwiftUI iOS apps.

## Install (on each machine, once)
```
/plugin marketplace add CrazyBoy621/ios-skills-marketplace
/plugin install swiftui-app-architecture@ios-skills
```
Then ask Claude Code about SwiftUI work and it loads automatically, or invoke explicitly with
`/swiftui-app-architecture:swiftui-app-architecture`.

## Update everywhere
Edit the skill, bump `version` in `plugins/swiftui-app-architecture/.claude-plugin/plugin.json`,
commit and push. On each machine: `/plugin marketplace update`.
