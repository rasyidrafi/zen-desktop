# Zen Browser Components Documentation

This directory contains detailed documentation for individual Zen Browser components and features.

## Available Component Guides

### Core Features
- [Workspaces](./workspaces.md) - Tab organization and workspace management
- [Vertical Tabs](./vertical-tabs.md) - Sidebar tab management and organization
- [Split View](./split-view.md) - Side-by-side tab viewing
- [Compact Mode](./compact-mode.md) - Space-efficient browser interface

### Productivity Features
- [Glance Mode](./glance.md) - Quick page preview and overview
- [Tab Folders](./folders.md) - Hierarchical tab organization
- [Pinned Tabs](./pinned-tabs.md) - Persistent tab management
- [Media Controls](./media-controls.md) - Enhanced media playback controls

### UI Components
- [Theme System](./theming.md) - Zen's theming and styling system
- [Icon System](./icons.md) - Zen's icon management and customization
- [Welcome Experience](./welcome.md) - First-run and onboarding flows

### Developer Components
- [Actor System](./actors.md) - Process communication architecture
- [Storage System](./storage.md) - Data persistence and management
- [Preference System](./preferences.md) - Settings and configuration

## Component Architecture Overview

All Zen components follow consistent architectural patterns:

### Manager Pattern
```javascript
const gZen[Component]Manager = {
  init() {
    // Initialize component
    this.setupUI();
    this.bindEvents();
    this.loadPreferences();
  },
  
  setupUI() {
    // Create/modify DOM elements
  },
  
  bindEvents() {
    // Event listeners and handlers
  },
  
  loadPreferences() {
    // Load user preferences
  }
};
```

### Storage Pattern
```javascript
const Zen[Component]Storage = {
  async save(data) {
    // Persist data to Firefox storage
  },
  
  async load() {
    // Load data from storage
  },
  
  async clear() {
    // Clear stored data
  }
};
```

### Engine Pattern
```javascript
const Zen[Component]Engine = {
  // Core business logic
  // Data manipulation
  // State management
};
```

## Integration Points

### Global Registration
All components are registered in `src/zen/zen.globals.js`:

```javascript
export default [
  'gZen[Component]Manager',
  'Zen[Component]Storage',
  'Zen[Component]Engine',
  // ...
];
```

### Build Integration
Components are included in the build via `src/zen/moz.build`:

```python
DIRS += [
    "component-name",
    # ...
]
```

### Startup Integration
Components are initialized during browser startup in the appropriate manager's `init()` method.

## Common Component APIs

### Event System
```javascript
// Custom events for component communication
const event = new CustomEvent('ZenComponentEvent', {
  detail: { data: 'component-specific-data' }
});
window.dispatchEvent(event);
```

### Preference Integration
```javascript
// Component preferences use zen.* namespace
Services.prefs.setBoolPref('zen.component.enabled', true);
const enabled = Services.prefs.getBoolPref('zen.component.enabled', false);
```

### UI Integration
```javascript
// Components integrate with Firefox's UI system
const element = document.getElementById('zen-component-container');
element.setAttribute('zen-component-state', 'active');
```

## Testing Components

Each component should include:

1. **Unit tests**: Test individual functions and methods
2. **Integration tests**: Test component interaction with Firefox
3. **UI tests**: Test user interface behavior
4. **Performance tests**: Ensure component doesn't degrade performance

Test files are located in `src/zen/tests/[component-name]/`.

## Contributing to Components

When adding or modifying components:

1. Follow the established patterns (Manager/Storage/Engine)
2. Update the appropriate documentation in this directory
3. Add comprehensive tests
4. Ensure Firefox compatibility
5. Consider performance implications
6. Maintain accessibility standards

## Component Dependencies

Many components depend on shared utilities:

- `src/zen/common/` - Shared utilities and helpers
- `src/zen/toolkit/` - Zen toolkit extensions
- Firefox APIs - Extensive use of Firefox's internal APIs

Understanding these dependencies is crucial for component development and debugging.