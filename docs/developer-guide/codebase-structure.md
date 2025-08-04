# Zen Browser Codebase Structure Guide

This guide provides a detailed walkthrough of the Zen Browser codebase, explaining where different types of code live and how to navigate the project effectively.

## Repository Overview

```
zen-desktop/
├── .github/              # GitHub workflows and templates
├── .husky/              # Git hooks for development
├── build/               # Build artifacts and configurations
├── configs/             # Platform-specific build configurations
├── docs/                # Documentation (you are here!)
├── locales/             # Internationalization files
├── prefs/               # Default preference files
├── scripts/             # Build and maintenance scripts
├── src/                 # Source code (main development area)
├── tools/               # Development tools and utilities
├── package.json         # Node.js dependencies and scripts
├── surfer.json          # Surfer build system configuration
└── tsconfig.json        # TypeScript configuration
```

## Core Source Directory (`src/`)

The `src/` directory contains all the browser source code, organized by Firefox's structure with Zen additions:

### Firefox Core Areas (Modified by Zen)

```
src/
├── browser/             # Browser UI and frontend components
├── devtools/            # Developer tools modifications
├── docshell/            # Document shell and navigation
├── dom/                 # DOM implementation extensions
├── gfx/                 # Graphics and rendering
├── image/               # Image handling
├── layout/              # Layout engine modifications
├── modules/             # Shared JavaScript modules
├── netwerk/             # Networking stack
├── security/            # Security implementations
├── testing/             # Test infrastructure
├── third_party/         # Third-party dependencies
├── toolkit/             # Cross-platform toolkit components
├── widget/              # Native widget implementations
├── xpfe/                # Cross-platform frontend
└── zen/                 # Zen-specific code ⭐
```

## Zen-Specific Code (`src/zen/`)

This is where most Zen development happens:

```
src/zen/
├── @types/              # TypeScript type definitions
├── common/              # Shared utilities and helpers
├── compact-mode/        # Compact mode functionality
├── downloads/           # Download management enhancements
├── folders/             # Tab folder organization
├── fonts/               # Font-related features
├── glance/              # Glance mode implementation
├── images/              # Zen-specific images and icons
├── kbs/                 # Keyboard shortcuts
├── media/               # Media control enhancements
├── mods/                # Core browser modifications
├── split-view/          # Split view functionality
├── tabs/                # Tab management enhancements
├── tests/               # Zen-specific tests
├── toolkit/             # Zen toolkit extensions
├── vendor/              # Vendored third-party libraries
├── welcome/             # Welcome/onboarding screens
├── workspaces/          # Workspace management
├── moz.build           # Mozilla build system integration
└── zen.globals.js      # Global definitions and exports
```

### Key Zen Files

#### `zen.globals.js`
The central hub for Zen's global variables and API access:

```javascript
// Exports all Zen managers and utilities
export default [
  'gZenUIManager',
  'gZenWorkspaces',
  'gZenVerticalTabsManager',
  // ... hundreds more globals
];
```

#### `common/` Directory
Shared utilities used across Zen components:

```
common/
├── zen-sets.js           # Data structure utilities
├── ZenPreloadedScripts.js # Script preloading system
└── zenThemeModifier.js   # Theme modification utilities
```

## Browser Frontend (`src/browser/`)

Firefox's main browser interface, enhanced by Zen:

```
src/browser/
├── app/                 # Application startup and configuration
├── base/                # Core browser functionality
├── components/          # Browser feature components
│   ├── newtab/         # New tab page (Zen-enhanced)
│   ├── preferences/    # Settings UI (Zen additions)
│   ├── sessionstore/   # Session management
│   └── ...
├── extensions/          # Extension management
├── locales/            # Browser UI translations
├── modules/            # Browser JavaScript modules
└── themes/             # Browser themes and styling
    ├── shared/         # Cross-platform theme resources
    │   ├── zen-icons/  # Zen icon system
    │   └── ...
    ├── zen-alpha/      # Zen Alpha theme
    ├── zen-beta/       # Zen Beta theme
    └── zen-cohort/     # Zen Cohort theme
```

### Important Browser Files

#### Theme Files
```
src/browser/themes/shared/zen-icons/
├── mac/                # macOS-specific icons
├── windows/            # Windows-specific icons
├── linux/              # Linux-specific icons
└── common/             # Cross-platform icons
```

#### New Tab Integration
```
src/browser/components/newtab/
├── content-src/        # React components for new tab
├── lib/                # Supporting libraries
└── README.md          # New tab documentation
```

## Toolkit Components (`src/toolkit/`)

Cross-platform components shared across the browser:

```
src/toolkit/
├── actors/             # Actor system implementations
├── components/         # Shared components
│   ├── downloads/     # Download system
│   ├── places/        # Bookmarks and history
│   ├── preferences/   # Preference system
│   └── ...
├── content/           # Content scripts and resources
├── locales/           # Toolkit translations
├── modules/           # Toolkit JavaScript modules
└── themes/            # Toolkit themes
```

## Configuration Files

### Root Configuration

#### `package.json`
Node.js project configuration with build scripts:

```json
{
  "scripts": {
    "build": "npm run ffprefs && surfer build",
    "start": "cd engine && ./mach run --noprofile",
    "test": "python3 scripts/run_tests.py",
    // ... more scripts
  },
  "dependencies": {
    "@zen-browser/surfer": "^1.11.16"
  }
}
```

#### `surfer.json`
Surfer build system configuration:

```json
{
  "name": "Zen Browser",
  "version": {
    "product": "firefox",
    "version": "141.0"
  },
  "brands": {
    "release": { /* release configuration */ },
    "twilight": { /* development configuration */ }
  }
}
```

#### `tsconfig.json`
TypeScript configuration for the project:

```json
{
  "compilerOptions": {
    "target": "ES2020",
    "module": "ESNext",
    // ... TypeScript settings
  }
}
```

### Build Configurations (`configs/`)

Platform-specific build configurations:

```
configs/
├── branding/           # Brand assets and configurations
│   ├── release/       # Release brand assets
│   └── twilight/      # Development brand assets
├── common/            # Cross-platform configurations
├── linux/             # Linux-specific configurations
├── macos/             # macOS-specific configurations
└── windows/           # Windows-specific configurations
```

## Scripts Directory (`scripts/`)

Build and maintenance scripts:

```
scripts/
├── update_ff.py           # Firefox version update script
├── run_tests.py          # Test runner
├── update_ts_types.py    # TypeScript type updates
├── copy_language_pack.py # Localization utilities
└── ...
```

### Key Scripts

#### `update_ff.py`
Manages Firefox version updates:

```python
def update_ff(is_rc: bool = False, last_version: str = ""):
    """Updates Firefox to the latest version"""
    # Downloads new Firefox source
    # Updates surfer.json configuration
    # Handles release vs. development versions
```

#### `run_tests.py`
Test execution script:

```python
# Runs Zen-specific tests
# Integrates with Firefox's test infrastructure
# Supports debug modes and specific test targeting
```

## Documentation Structure (`docs/`)

```
docs/
├── developer-guide/        # Developer documentation (this guide)
│   ├── components/        # Component-specific documentation
│   ├── getting-started.md # Setup and first build
│   ├── architecture.md   # Architecture overview
│   ├── codebase-structure.md # This file
│   └── ...
├── assets/               # Documentation assets (images, etc.)
├── contribute.md         # Contribution guidelines
├── workspaces.md        # Workspace documentation
├── changes.md           # Change log
└── key-shortcuts.md     # Keyboard shortcuts
```

## File Types and Conventions

### JavaScript/TypeScript Files

#### Zen Components (`.js`)
```javascript
// src/zen/feature/zenFeature.js
const gZenFeatureManager = {
  init() {
    // Component initialization
  },
  
  // Component methods
};
```

#### Module Definitions (`.jsm`)
```javascript
// Firefox module system
var EXPORTED_SYMBOLS = ["ZenFeature"];

const ZenFeature = {
  // Module implementation
};
```

#### Type Definitions (`.d.ts`)
```typescript
// src/zen/@types/zen.d.ts
interface ZenWorkspace {
  uuid: string;
  name: string;
  icon: string;
  default: boolean;
}
```

### Styling Files

#### CSS Styles (`.css`)
```css
/* Browser chrome styling */
#zen-workspace-sidebar {
  /* Zen-specific styles */
}
```

#### Theme Files (`.properties`)
```properties
# Theme color definitions
zen.color.primary=#007ACC
zen.color.secondary=#F0F0F0
```

### Build Files

#### Mozilla Build (`.moz.build`)
```python
# Build system integration
DIRS += [
    "glance",
    "mods", 
    "tests",
]
```

#### Makefiles (`Makefile`)
```makefile
# Build targets and rules
build-zen:
	npm run build
```

## Navigation Tips

### Finding Code by Feature

1. **UI Changes**: Start in `src/browser/` for interface modifications
2. **Zen Features**: Look in `src/zen/[feature-name]/` for Zen-specific functionality
3. **Themes**: Check `src/browser/themes/` for styling and visual changes
4. **Preferences**: Look in `src/toolkit/components/preferences/` and `prefs/`
5. **Tests**: Find in `src/zen/tests/` or `src/testing/`

### Common File Patterns

#### Manager Classes
```
gZen[Feature]Manager     # UI management and coordination
Zen[Feature]Storage      # Data persistence
Zen[Feature]Engine       # Core logic implementation
```

#### File Naming
```
zen[Feature].js          # Main implementation
zen[Feature]Storage.js   # Storage layer
zen[Feature]Utils.js     # Utility functions
zen[Feature].css         # Styling
```

### Search Strategies

#### Finding Components
```bash
# Find all workspace-related files
find src/ -name "*workspace*" -type f

# Find all manager classes
grep -r "Manager.*=" src/zen/

# Find CSS selectors
grep -r "#zen-" src/browser/themes/
```

#### Understanding Dependencies
```bash
# Find imports of a component
grep -r "gZenWorkspaces" src/

# Find all global definitions
cat src/zen/zen.globals.js
```

## Build Artifacts (`build/`)

Generated during the build process:

```
build/
├── obj-*/              # Compiled object files
├── dist/               # Distribution packages
├── symbols/            # Debug symbols
└── logs/               # Build logs
```

**Note**: Build artifacts are not committed to the repository and are generated during compilation.

## Development Workflow by Directory

### For UI Changes
1. Start in `src/browser/themes/` for styling
2. Modify components in `src/browser/components/`
3. Add Zen-specific logic in `src/zen/`

### For New Features
1. Create directory in `src/zen/[feature-name]/`
2. Add build integration in `src/zen/moz.build`
3. Export globals in `src/zen/zen.globals.js`
4. Add tests in `src/zen/tests/`

### For Bug Fixes
1. Identify component in `src/zen/`
2. Check for related Firefox patches in `src/firefox-patches/`
3. Update tests in `src/zen/tests/`

## Code Organization Principles

Zen follows these organization principles:

1. **Separation of concerns**: UI, logic, and storage are separated
2. **Firefox compatibility**: Modifications are cleanly separated from Firefox code
3. **Feature modularity**: Each feature has its own directory and namespace
4. **Test colocation**: Tests are near the code they test
5. **Documentation proximity**: README files explain complex components

This structure enables efficient development while maintaining clear boundaries between Zen additions and the underlying Firefox codebase.