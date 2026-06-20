# ios-skills

A personal Claude Code plugin marketplace.

## Plugins
- **swiftui-app-architecture** — conventions and architecture for native SwiftUI iOS apps.
- **hormozi-grand-slam-offer** — craft or critique a Grand Slam Offer using Alex Hormozi's $100M Offers frameworks.

## Install (on each machine, once)
```
/plugin marketplace add CrazyBoy621/ios-skills-marketplace
/plugin install swiftui-app-architecture@ios-skills
/plugin install hormozi-grand-slam-offer@ios-skills
```
Then ask Claude Code about the relevant work and the skill loads automatically, or invoke explicitly
with `/swiftui-app-architecture:swiftui-app-architecture` or `/hormozi-grand-slam-offer:hormozi-grand-slam-offer`.

## Update everywhere
Edit the skill, bump `version` in `plugins/swiftui-app-architecture/.claude-plugin/plugin.json`,
commit and push. On each machine: `/plugin marketplace update`.
