# Zen Browser Developer Documentation

Complete deep-dive documentation for contributing to and understanding Zen Browser - a Firefox-based browser focused on productivity and customization.

## 🚀 Quick Start

New to Zen development? Start here:

1. **[Getting Started Guide](./developer-guide/getting-started.md)** - Complete setup and first build walkthrough
2. **[Architecture Overview](./developer-guide/architecture.md)** - Understand how Zen extends Firefox
3. **[Codebase Structure](./developer-guide/codebase-structure.md)** - Navigate the codebase effectively
4. **[Contributing Guidelines](./contribute.md)** - Development workflow and best practices

## 📖 Documentation Overview

### Development Guides

| Guide | Description | When to Read |
|-------|-------------|--------------|
| [Getting Started](./developer-guide/getting-started.md) | Setup development environment and build Zen | First time setup |
| [Architecture](./developer-guide/architecture.md) | How Zen modifies and extends Firefox | Understanding the system |
| [Codebase Structure](./developer-guide/codebase-structure.md) | Directory layout and file organization | Navigating the code |
| [Testing](./developer-guide/testing.md) | Writing and running tests | Adding features/fixes |
| [Contributing](./contribute.md) | Contribution workflow and guidelines | Before contributing |

### Component Documentation

| Component | Description | Key Features |
|-----------|-------------|--------------|
| [Workspaces](./developer-guide/components/workspaces.md) | Tab organization and session management | Multiple browsing contexts |
| [Vertical Tabs](./developer-guide/components/README.md) | Sidebar tab management | Space-efficient tab organization |
| [Split View](./developer-guide/components/README.md) | Side-by-side tab viewing | Productivity enhancement |
| [Compact Mode](./developer-guide/components/README.md) | Space-efficient interface | UI density options |

*Additional component documentation available in [Components README](./developer-guide/components/README.md)*

### User Documentation

| Document | Description | Audience |
|----------|-------------|----------|
| [Workspaces Usage](./workspaces.md) | How to use workspace features | End users |
| [Keyboard Shortcuts](./key-shortcuts.md) | Available keyboard shortcuts | Power users |
| [Changes](./changes.md) | Change log and version history | All users |

## 🏗️ What is Zen Browser?

Zen Browser is a **Firefox-based browser** that enhances productivity through:

- **Workspaces**: Organize tabs by project or context
- **Vertical Tabs**: Sidebar-based tab management
- **Split View**: View multiple tabs simultaneously
- **Compact Mode**: Space-efficient interface options
- **Enhanced Theming**: Customizable appearance and branding
- **Productivity Features**: Glance mode, tab folders, media controls

### Core Philosophy

Zen maintains **Firefox's security and compatibility** while adding powerful productivity features that don't compromise on:

- **Performance**: Modifications are optimized for speed
- **Security**: Built on Firefox's robust security model  
- **Compatibility**: Works with Firefox updates and extensions
- **Choice**: Users control which features to enable

## 🔧 Development Overview

### Architecture Summary

```
┌─────────────────────────────────────────────────────────┐
│                   Zen Browser                           │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────────┐  │
│  │   Zen UI    │  │ Zen Features │  │ Zen Components  │  │
│  │ Components  │  │   & Logic    │  │   & Storage     │  │
│  └─────────────┘  └─────────────┘  └─────────────────┘  │
│         │               │                    │          │
│         └───────────────┼────────────────────┘          │
│                         │                               │
│  ┌─────────────────────┴─────────────────────────────┐  │
│  │            Firefox Engine (Gecko)                 │  │
│  └───────────────────────────────────────────────────┘  │
└─────────────────────────────────────────────────────────┘
```

### Key Technologies

- **Base**: Firefox 141.0 (Gecko engine)
- **Build System**: Surfer (custom Firefox build manager)
- **Languages**: JavaScript, TypeScript, CSS, Python, Rust
- **UI Framework**: XUL/HTML with CSS (Firefox's UI system)
- **Storage**: Firefox's Places database and preferences
- **Testing**: Firefox's testing infrastructure (Mochitest)

### Development Workflow

1. **Setup**: Download Firefox source and apply Zen modifications
2. **Development**: Make changes in `src/zen/` directory
3. **Building**: Use incremental builds for faster iteration
4. **Testing**: Run automated tests and manual verification
5. **Contribution**: Submit PRs following established workflow

## 🧭 Navigation Guide

### For New Contributors

**Start Here** → [Getting Started Guide](./developer-guide/getting-started.md)

1. Set up development environment
2. Build Zen for the first time
3. Make a small test change
4. Run tests and verify build

**Then Continue** → [Contributing Guidelines](./contribute.md)

### For Feature Development

**Plan** → [Architecture Overview](./developer-guide/architecture.md)
- Understand how Zen extends Firefox
- Learn component patterns and integration points

**Navigate** → [Codebase Structure](./developer-guide/codebase-structure.md)
- Find where different types of code live
- Understand file organization and naming

**Implement** → [Component Documentation](./developer-guide/components/)
- Follow established patterns
- Use existing components as examples

**Test** → [Testing Guide](./developer-guide/testing.md)
- Write comprehensive tests
- Verify functionality and performance

### For Bug Fixes

1. **Reproduce**: Set up development environment
2. **Locate**: Use codebase structure guide to find relevant code
3. **Understand**: Read component documentation for the affected feature
4. **Fix**: Make minimal, targeted changes
5. **Test**: Add tests to prevent regression

### For UI/UX Changes

1. **Themes**: Check `src/browser/themes/` for styling
2. **Components**: Look in `src/zen/` for UI logic
3. **Icons**: Modify icon system in `src/browser/themes/shared/zen-icons/`
4. **Testing**: Manual testing across platforms

## 🔍 Finding Information

### By Topic

| Looking for... | Check here... |
|----------------|---------------|
| Setup help | [Getting Started](./developer-guide/getting-started.md) |
| How Zen works | [Architecture](./developer-guide/architecture.md) |
| Where code lives | [Codebase Structure](./developer-guide/codebase-structure.md) |
| Specific features | [Components](./developer-guide/components/) |
| Testing help | [Testing Guide](./developer-guide/testing.md) |
| Contribution process | [Contributing](./contribute.md) |
| User features | [User Documentation](#user-documentation) |

### By File Type

| File Type | Location | Purpose |
|-----------|----------|---------|
| `*.js`, `*.mjs` | `src/zen/` | Zen feature implementation |
| `*.css` | `src/browser/themes/` | UI styling and themes |
| `*.py` | `scripts/` | Build and maintenance scripts |
| `*.toml` | `src/zen/tests/` | Test configuration |
| `*.json` | Root directory | Project configuration |

### By Development Task

| Task | Primary Documentation |
|------|----------------------|
| First-time setup | [Getting Started](./developer-guide/getting-started.md) |
| Understanding codebase | [Architecture](./developer-guide/architecture.md) + [Codebase Structure](./developer-guide/codebase-structure.md) |
| Adding features | [Components](./developer-guide/components/) + [Contributing](./contribute.md) |
| Fixing bugs | [Testing](./developer-guide/testing.md) + [Codebase Structure](./developer-guide/codebase-structure.md) |
| UI changes | [Codebase Structure](./developer-guide/codebase-structure.md) + Component docs |

## 🤝 Community and Support

### Getting Help

- **Documentation Issues**: Create issue in this repository
- **Development Questions**: [GitHub Discussions](https://github.com/zen-browser/desktop/discussions)
- **Bug Reports**: [GitHub Issues](https://github.com/zen-browser/desktop/issues)
- **Real-time Chat**: [Discord Community](https://discord.gg/zen-browser)

### Contributing

All contributions are welcome! Whether you're:

- **New to development**: Start with documentation improvements
- **Experienced developer**: Tackle features and architectural improvements  
- **Designer**: Help with UI/UX and theming
- **Tester**: Improve test coverage and quality
- **Writer**: Enhance documentation and guides

See our [Contributing Guidelines](./contribute.md) for detailed information.

## 📋 Quick Reference

### Essential Commands

```bash
# Setup
npm install && npm run download && npm run import && npm run bootstrap

# Development
npm run build:ui    # Fast UI-only build
npm run build      # Full build
npm start          # Run Zen

# Quality
npm test           # Run tests
npm run lint       # Check code style
npm run lint:fix   # Fix code style
```

### Key Directories

```
zen-desktop/
├── src/zen/           # Zen-specific features
├── src/browser/       # Browser UI modifications  
├── src/toolkit/       # Cross-platform components
├── docs/              # Documentation (you are here)
├── scripts/           # Build and utility scripts
└── configs/           # Build configurations
```

### Important Files

- `package.json` - Node.js dependencies and scripts
- `surfer.json` - Build system configuration
- `src/zen/zen.globals.js` - Global variable definitions
- `src/zen/moz.build` - Build system integration

---

This documentation is continuously updated as Zen Browser evolves. For the latest information, always refer to the current documentation in the repository.

**Ready to contribute?** Start with the [Getting Started Guide](./developer-guide/getting-started.md)! 🚀