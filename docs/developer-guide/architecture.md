# Zen Browser Architecture Overview

This document provides a comprehensive overview of how Zen Browser extends and modifies Firefox to create a unique browsing experience focused on productivity and customization.

## High-Level Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                     Zen Browser                             │
│  ┌─────────────────┐  ┌─────────────────┐  ┌──────────────┐ │
│  │   Zen UI/UX     │  │  Zen Features   │  │ Zen Branding │ │
│  │   Components    │  │   & Logic       │  │   & Assets   │ │
│  └─────────────────┘  └─────────────────┘  └──────────────┘ │
│              │                │                    │        │
│              └────────────────┼────────────────────┘        │
│                               │                             │
│  ┌─────────────────────────────┴─────────────────────────┐   │
│  │              Firefox Engine (Gecko)                  │   │
│  │  ┌──────────┐ ┌──────────┐ ┌──────────┐ ┌──────────┐ │   │
│  │  │   DOM    │ │ Layout   │ │ Rendering│ │ Networking│ │   │
│  │  │  Engine  │ │ Engine   │ │  Engine  │ │  Stack   │ │   │
│  │  └──────────┘ └──────────┘ └──────────┘ └──────────┘ │   │
│  └─────────────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────────────┘
```

## Core Components

### 1. Firefox Foundation

Zen Browser is built on top of **Firefox Gecko Engine**, currently version `141.0`. This provides:

- **Web rendering**: HTML, CSS, JavaScript execution
- **Security**: Sandboxing, content policies, secure networking
- **Standards compliance**: Latest web standards and APIs
- **Cross-platform support**: Windows, macOS, Linux compatibility
- **Extension system**: WebExtensions API support

### 2. Zen Customizations

Zen adds its own layer of functionality through several key systems:

#### A. Zen UI Framework
- **Custom browser chrome**: Modified Firefox UI with new layouts
- **Zen-specific components**: Workspaces, vertical tabs, compact mode
- **Theming system**: Enhanced theme engine with Zen-specific styling
- **Icon system**: Custom icon sets and branding elements

#### B. Zen Feature Engine
- **Workspace management**: Tab organization and session management
- **Productivity tools**: Split view, glance mode, folders
- **Media controls**: Enhanced media playback controls
- **Search enhancements**: Improved search and navigation

#### C. Integration Layer
- **Firefox patches**: Temporary modifications to Firefox behavior
- **Custom actors**: Communication between UI and backend processes
- **Preference system**: Zen-specific settings and configurations

## Build System Architecture

### Surfer Build System

Zen uses a custom build system called **Surfer** that manages the complex process of integrating Zen modifications with Firefox:

```
┌─────────────────────────────────────────────────────────────┐
│                    Surfer Build Pipeline                   │
│                                                             │
│  1. Download    2. Import     3. Patch      4. Build       │
│  ┌─────────┐   ┌─────────┐   ┌─────────┐   ┌─────────┐     │
│  │Firefox  │──▶│   Zen   │──▶│ Apply   │──▶│Compile &│     │
│  │ Source  │   │Changes &│   │ Patches │   │ Package │     │
│  │         │   │ Assets  │   │         │   │         │     │
│  └─────────┘   └─────────┘   └─────────┘   └─────────┘     │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

#### Key Surfer Operations:

1. **Download** (`surfer download`): Fetches Firefox source code
2. **Import** (`surfer import`): Applies Zen modifications to Firefox
3. **Bootstrap** (`surfer bootstrap`): Sets up build environment
4. **Build** (`surfer build`): Compiles the modified browser
5. **Package** (`surfer package`): Creates distribution packages
6. **Export** (`surfer export`): Extracts changes back to Zen repository

## Directory Structure & Responsibilities

### Core Firefox Areas (Modified by Zen)

```
src/
├── browser/                 # Firefox browser UI and components
│   ├── components/         # Main browser functionality
│   ├── themes/            # Browser themes and styling
│   └── locales/           # Internationalization
├── toolkit/               # Firefox toolkit components
│   ├── components/        # Shared toolkit functionality
│   └── modules/           # JavaScript modules
└── zen/                   # Zen-specific code (main focus)
    ├── common/            # Shared Zen utilities
    ├── workspaces/        # Workspace management
    ├── tabs/              # Tab and sidebar modifications
    ├── glance/            # Glance mode functionality
    ├── split-view/        # Split view implementation
    ├── compact-mode/      # Compact mode features
    ├── mods/              # Core modifications and managers
    └── toolkit/           # Zen toolkit extensions
```

### Zen-Specific Components

#### `/src/zen/` - The Heart of Zen

This directory contains all Zen-specific functionality:

- **`zen.globals.js`**: Global variable definitions and Firefox API access
- **`common/`**: Shared utilities and helper functions
- **Component directories**: Each major feature has its own directory

#### Key Component Architecture:

```javascript
// Example: Workspace Management Architecture
ZenWorkspacesEngine          // Core workspace logic
├── ZenWorkspacesStorage     // Data persistence
├── ZenWorkspaceBookmarksStorage  // Bookmark integration
└── gZenWorkspaces          // UI management

// Tab Management Architecture  
gZenVerticalTabsManager     // Vertical tab functionality
├── gZenPinnedTabManager    // Pinned tab handling
├── ZenPinnedTabsStorage    // Pinned tab persistence
└── gZenFolders            // Tab folder organization
```

## Communication Architecture

### Actor System

Zen uses Firefox's Actor system for communication between processes:

```
┌─────────────────┐    Messages    ┌─────────────────┐
│   Parent        │◄──────────────►│     Child       │
│   Process       │                │    Process      │
│                 │                │                 │
│ • Window        │                │ • Content       │
│   Management    │                │   Scripts       │
│ • Preferences   │                │ • Page          │
│ • Storage       │                │   Interaction   │
│ • Zen Features  │                │ • Zen Actors    │
└─────────────────┘                └─────────────────┘
```

### Key Actors:

- **`gZenActorsManager`**: Manages communication between processes
- **`JSWindowActorParent`**: Parent-side actor functionality
- **`JSWindowActorChild`**: Child-side actor functionality

## Feature Integration Patterns

### 1. UI Component Pattern

```javascript
// Zen components follow this pattern:
const gZenFeatureManager = {
  init() {
    // Initialize feature
    this.setupUI();
    this.bindEvents();
  },
  
  setupUI() {
    // Create/modify DOM elements
  },
  
  bindEvents() {
    // Set up event listeners
  }
};
```

### 2. Storage Pattern

```javascript
// Data persistence follows this pattern:
const ZenFeatureStorage = {
  async save(data) {
    // Save to Firefox preferences or storage
  },
  
  async load() {
    // Load from storage
  }
};
```

### 3. Preference Integration

Zen preferences are integrated with Firefox's preference system:

```javascript
// Zen preferences use 'zen.' prefix
Services.prefs.setBoolPref("zen.workspaces.enabled", true);
Services.prefs.getStringPref("zen.theme.name", "default");
```

## Theming and Styling Architecture

### CSS Architecture

```
themes/
├── shared/                # Shared theme resources
│   ├── zen-icons/        # Zen icon sets
│   └── zen-styles/       # Common Zen styles
├── zen-alpha/            # Zen Alpha theme
├── zen-beta/             # Zen Beta theme  
└── zen-cohort/           # Zen Cohort theme
```

### Theme System:

1. **Base Firefox themes**: Standard Firefox theme system
2. **Zen theme extensions**: Additional CSS for Zen features
3. **Dynamic theming**: Runtime theme switching and customization
4. **Icon system**: SVG-based icon system with theme variants

## Patch System

### Firefox Patches (`src/firefox-patches/`)

Zen applies temporary patches to Firefox for:

- Bug fixes not yet in Firefox stable
- Experimental features from Firefox nightly
- Zen-specific behavior modifications

**Important**: Patches are temporary and should be removed as Firefox evolves.

### Patch Categories:

1. **Temporal patches**: Future Firefox features pulled early
2. **Bug fixes**: Critical fixes for Zen's use cases
3. **Compatibility patches**: Ensure Zen features work with Firefox

## Security Model

Zen inherits Firefox's robust security architecture:

### Security Layers:

1. **Process isolation**: Content runs in separate processes
2. **Sandboxing**: Restricted system access for content
3. **Content Security Policy**: Prevention of XSS and injection
4. **Certificate validation**: Secure connections and updates
5. **Zen security additions**: Additional privacy and security features

### Zen-Specific Security:

- **Workspace isolation**: Separate workspace data
- **Enhanced privacy controls**: Additional tracking protection
- **Secure preferences**: Encrypted preference storage

## Update and Release Architecture

### Release Channels:

```
┌─────────────┐    ┌─────────────┐    ┌─────────────┐
│   Twilight  │───▶│   Release   │───▶│   Stable    │
│   (Nightly) │    │    (RC)     │    │  (Public)   │
└─────────────┘    └─────────────┘    └─────────────┘
```

1. **Twilight**: Development builds with latest features
2. **Release**: Release candidate builds for testing
3. **Stable**: Production builds for end users

### Update System:

- **Base updates**: Firefox engine updates
- **Zen updates**: Zen feature and UI updates
- **Security updates**: Critical security patches
- **Incremental updates**: Efficient update delivery

## Performance Considerations

### Optimization Strategies:

1. **Incremental builds**: Only rebuild changed components
2. **UI-only builds**: Fast builds for frontend changes
3. **Lazy loading**: Load features on demand
4. **Memory management**: Efficient workspace and tab handling
5. **Startup optimization**: Fast browser initialization

### Build Performance:

- **First build**: 1-3 hours (complete compilation)
- **Incremental builds**: 5-15 minutes (changed components only)
- **UI builds**: 2-5 minutes (frontend changes only)

## Extension Points

Zen provides several ways to extend functionality:

### 1. WebExtensions
- Standard Firefox extension APIs
- Zen-specific extension APIs (planned)

### 2. Themes
- Custom CSS themes
- Icon pack system
- Dynamic theme creation

### 3. Configuration
- Preference-based customization
- User scripts and styles
- Workspace templates

## Development Philosophy

Zen follows these architectural principles:

1. **Firefox compatibility**: Maintain compatibility with Firefox updates
2. **Minimal patching**: Prefer extending over modifying Firefox code
3. **Modular design**: Keep features independent and testable
4. **Performance first**: Ensure modifications don't degrade performance
5. **User choice**: Provide options and customization
6. **Security aware**: Never compromise Firefox's security model

## Future Architecture Plans

### Planned Improvements:

1. **Enhanced actor system**: Better process communication
2. **Plugin architecture**: More extensible feature system
3. **Advanced theming**: More powerful theme capabilities
4. **Performance optimization**: Continued build and runtime improvements
5. **Mobile support**: Architecture for mobile Zen versions

This architecture enables Zen to provide powerful productivity features while maintaining the stability, security, and performance of the Firefox foundation.