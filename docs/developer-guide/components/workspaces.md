# Workspaces Component Documentation

The Workspaces component is one of Zen Browser's flagship features, providing tab organization and session management capabilities that allow users to separate their browsing contexts into distinct workspaces.

## Overview

Workspaces in Zen Browser function like virtual desktops for your tabs, enabling users to:

- Organize tabs by project, context, or purpose
- Switch between different browsing sessions instantly
- Maintain separate bookmark folders for each workspace
- Isolate different types of work or personal browsing

## Architecture

The Workspaces system consists of several interconnected components:

```
ZenWorkspaces (Manager)
├── ZenWorkspacesStorage (Data Layer)
├── ZenWorkspaceBookmarksStorage (Bookmark Integration)
├── ZenWorkspace (Individual Workspace Logic)
├── ZenWorkspaceCreation (Creation UI)
├── ZenWorkspaceIcons (Icon Management)
└── ZenWorkspacesSync (Sync Integration)
```

## Core Components

### `gZenWorkspaces` (Main Manager)

Located in `src/zen/workspaces/ZenWorkspaces.mjs`

The central manager class that extends `nsZenMultiWindowFeature` and handles:

- Workspace switching and management
- Tab organization within workspaces
- UI updates and event handling
- Integration with Firefox's session management

```javascript
var gZenWorkspaces = new (class extends nsZenMultiWindowFeature {
  // Core workspace management
  _lastSelectedWorkspaceTabs = {};
  _inChangingWorkspace = false;
  
  async init() {
    // Initialize workspace system
  },
  
  async createWorkspace(name, icon) {
    // Create new workspace
  },
  
  async switchToWorkspace(uuid) {
    // Switch active workspace
  }
});
```

#### Key Properties:

- **`_lastSelectedWorkspaceTabs`**: Tracks the last selected tab in each workspace
- **`_inChangingWorkspace`**: Prevents recursive workspace changes
- **`draggedElement`**: Handles drag-and-drop functionality
- **`_swipeState`**: Manages gesture navigation between workspaces

#### Key Methods:

- **`init()`**: Initializes the workspace system
- **`createWorkspace(name, icon, containerId)`**: Creates a new workspace
- **`switchToWorkspace(uuid)`**: Switches to a specific workspace
- **`deleteWorkspace(uuid)`**: Removes a workspace
- **`updateWorkspace(uuid, data)`**: Updates workspace properties

### `ZenWorkspacesStorage` (Data Persistence)

Located in `src/zen/workspaces/ZenWorkspacesStorage.mjs`

Handles data persistence using Firefox's Places database:

```javascript
var ZenWorkspacesStorage = {
  async init() {
    await this._ensureTable();
  },
  
  async _ensureTable() {
    // Creates database tables for workspaces
  },
  
  async save(workspace) {
    // Saves workspace to database
  },
  
  async load() {
    // Loads all workspaces from database
  }
};
```

#### Database Schema:

```sql
CREATE TABLE zen_workspaces (
  id INTEGER PRIMARY KEY,
  uuid TEXT UNIQUE NOT NULL,
  name TEXT NOT NULL,
  icon TEXT,
  container_id INTEGER,
  theme_id INTEGER DEFAULT 0,
  position INTEGER DEFAULT 0,
  created_at INTEGER DEFAULT (strftime('%s', 'now')),
  updated_at INTEGER DEFAULT (strftime('%s', 'now'))
);
```

### `ZenWorkspaceBookmarksStorage` (Bookmark Integration)

Manages workspace-specific bookmark folders:

```javascript
var ZenWorkspaceBookmarksStorage = {
  async init() {
    // Initialize bookmark integration
  },
  
  async createWorkspaceFolder(workspaceUuid, name) {
    // Creates bookmark folder for workspace
  },
  
  async getWorkspaceFolder(workspaceUuid) {
    // Gets workspace bookmark folder
  }
};
```

### `ZenWorkspace` (Individual Workspace Logic)

Located in `src/zen/workspaces/ZenWorkspace.mjs`

Represents individual workspace instances:

```javascript
class ZenWorkspace {
  constructor(data) {
    this.uuid = data.uuid;
    this.name = data.name;
    this.icon = data.icon;
    this.containerId = data.containerId;
  }
  
  async activate() {
    // Activate this workspace
  }
  
  async deactivate() {
    // Deactivate this workspace
  }
}
```

## Data Structure

### Workspace Object

```javascript
const workspace = {
  uuid: "550e8400-e29b-41d4-a716-446655440000", // Unique identifier
  name: "Development",                            // User-defined name
  icon: "developer",                             // Icon identifier
  containerId: 1,                                // Firefox container ID
  themeId: 0,                                    // Theme identifier
  position: 0,                                   // Display order
  createdAt: 1640995200,                        // Creation timestamp
  updatedAt: 1640995200,                        // Last update timestamp
  isDefault: false                              // Default workspace flag
};
```

### Tab Association

Tabs are associated with workspaces through the `zen-workspace-uuid` attribute:

```javascript
// Tab gets workspace attribute
tab.setAttribute('zen-workspace-uuid', workspace.uuid);

// Retrieve tab's workspace
const workspaceUuid = tab.getAttribute('zen-workspace-uuid');
```

## API Reference

### Creating Workspaces

```javascript
// Create a new workspace
const workspace = await gZenWorkspaces.createWorkspace("My Workspace", "work");

// Create with specific container
const workspace = await gZenWorkspaces.createWorkspace(
  "Banking", 
  "finance", 
  containerId
);
```

### Managing Workspaces

```javascript
// Switch to workspace
await gZenWorkspaces.switchToWorkspace(workspaceUuid);

// Update workspace
await gZenWorkspaces.updateWorkspace(workspaceUuid, {
  name: "New Name",
  icon: "new-icon"
});

// Delete workspace
await gZenWorkspaces.deleteWorkspace(workspaceUuid);

// Get all workspaces
const workspaces = await gZenWorkspaces.getWorkspaces();

// Get active workspace
const activeWorkspace = gZenWorkspaces.getActiveWorkspace();
```

### Tab Management

```javascript
// Move tab to workspace
await gZenWorkspaces.moveTabToWorkspace(tab, workspaceUuid);

// Get tabs in workspace
const tabs = gZenWorkspaces.getTabsInWorkspace(workspaceUuid);

// Get tab's workspace
const workspaceUuid = gZenWorkspaces.getTabWorkspace(tab);
```

### Events

```javascript
// Listen for workspace changes
window.addEventListener('ZenWorkspaceChanged', (event) => {
  const { oldWorkspace, newWorkspace } = event.detail;
  console.log(`Switched from ${oldWorkspace} to ${newWorkspace}`);
});

// Listen for workspace creation
window.addEventListener('ZenWorkspaceCreated', (event) => {
  const { workspace } = event.detail;
  console.log(`Created workspace: ${workspace.name}`);
});
```

## UI Integration

### Workspace Sidebar

The workspace UI is integrated into Firefox's sidebar:

```css
/* Workspace sidebar styling */
#zen-workspaces-sidebar {
  /* Sidebar container */
}

.zen-workspace-item {
  /* Individual workspace item */
}

.zen-workspace-item.active {
  /* Active workspace styling */
}
```

### Workspace Creation UI

Located in `src/zen/workspaces/ZenWorkspaceCreation.mjs`:

- Modal dialog for creating new workspaces
- Icon selection interface
- Name validation
- Container integration

### Icon System

Located in `src/zen/workspaces/ZenWorkspaceIcons.mjs`:

```javascript
const ZenWorkspaceIcons = {
  // Available icons for workspaces
  icons: [
    'general', 'work', 'personal', 'entertainment',
    'shopping', 'social', 'development', 'finance'
  ],
  
  getIconUrl(iconName) {
    // Returns URL for icon
  }
};
```

## Storage and Persistence

### Session Management

Workspaces integrate with Firefox's SessionStore to persist:

- Tab associations with workspaces
- Active workspace state
- Tab positions within workspaces

```javascript
// Session data structure
const sessionData = {
  zenWorkspaces: {
    activeWorkspace: "uuid",
    workspaces: {
      "uuid": {
        tabs: [/* tab data */],
        lastSelectedTab: 0
      }
    }
  }
};
```

### Container Integration

Workspaces can be associated with Firefox containers for enhanced privacy:

```javascript
// Create workspace with container
const containerId = await gZenWorkspaces.createContainer("Work Container");
const workspace = await gZenWorkspaces.createWorkspace(
  "Work",
  "briefcase", 
  containerId
);
```

## Performance Considerations

### Lazy Loading

- Workspaces are loaded on-demand
- Tab switching is optimized for performance
- Database queries are batched when possible

### Memory Management

- Inactive workspace tabs can be suspended
- Tab session data is stored efficiently
- Workspace metadata is cached in memory

## Testing

Test files are located in `src/zen/tests/workspaces/`:

```javascript
// Example test
add_task(async function test_workspace_creation() {
  const workspace = await gZenWorkspaces.createWorkspace("Test", "test");
  Assert.ok(workspace.uuid, "Workspace has UUID");
  Assert.equal(workspace.name, "Test", "Workspace name is correct");
});
```

### Test Categories:

1. **Unit Tests**: Individual component functionality
2. **Integration Tests**: Workspace system integration
3. **UI Tests**: User interface behavior
4. **Performance Tests**: Switching and loading performance

## Preferences

Workspace behavior is controlled by preferences:

```javascript
// Enable/disable workspaces
"zen.workspaces.enabled" = true

// Workspace container behavior
"zen.workspaces.autoCreateContainer" = false

// Debugging
"zen.workspaces.debug" = false

// Sync integration
"zen.workspaces.sync.enabled" = true
```

## Synchronization

The `ZenWorkspacesSync.mjs` component handles syncing workspace data:

- Integrates with Firefox Sync
- Syncs workspace metadata across devices
- Handles conflict resolution
- Maintains bookmark folder sync

## Troubleshooting

### Common Issues:

1. **Workspace not switching**: Check console for errors, verify database integrity
2. **Tabs not moving**: Ensure tab has proper workspace attribute
3. **Sync issues**: Check Firefox Sync status and network connectivity

### Debug Mode:

Enable debug mode for verbose logging:

```javascript
Services.prefs.setBoolPref('zen.workspaces.debug', true);
```

### Database Recovery:

```javascript
// Reset workspaces database
await ZenWorkspacesStorage.reset();
await ZenWorkspacesStorage.init();
```

## Future Enhancements

Planned improvements for the Workspaces system:

1. **Workspace templates**: Pre-configured workspace setups
2. **Advanced theming**: Per-workspace themes and customization
3. **Improved sync**: Enhanced synchronization capabilities
4. **Automation**: Rules for automatic tab organization
5. **Collaboration**: Shared workspace capabilities

The Workspaces component represents a core differentiator for Zen Browser, providing powerful tab organization that enhances productivity and browsing organization.