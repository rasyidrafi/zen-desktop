# Testing Guide

This guide covers how to write, run, and maintain tests for Zen Browser. Zen uses Firefox's testing infrastructure with Zen-specific additions.

## Overview

Zen Browser testing consists of:

1. **Browser Chrome Tests**: Test browser UI and Zen-specific functionality
2. **Unit Tests**: Test individual JavaScript functions and components
3. **Integration Tests**: Test component interactions and Firefox integration
4. **Performance Tests**: Ensure Zen modifications don't degrade performance

## Test Structure

### Test Directory Organization

```
src/zen/tests/
├── compact_mode/          # Compact mode functionality tests
├── container_essentials/  # Container integration tests
├── glance/               # Glance mode tests
├── pinned/               # Pinned tab tests
├── split_view/           # Split view tests
├── tabs/                 # Tab management tests
├── urlbar/               # URL bar enhancement tests
├── welcome/              # Welcome screen tests
├── workspaces/           # Workspace functionality tests
└── moz.build            # Build configuration for tests
```

### Test File Naming

Test files follow Firefox conventions:

- **Browser tests**: `browser_[feature]_[scenario].js`
- **Test manifests**: `browser.toml` (defines which tests to run)

Example:
```
pinned/
├── browser.toml                    # Test manifest
├── browser_pinned_created.js       # Test pinned tab creation
├── browser_pinned_removed.js       # Test pinned tab removal
├── browser_pinned_switch.js        # Test switching between pinned tabs
└── browser_issue_8726.js          # Test for specific issue fix
```

## Running Tests

### Prerequisites

Ensure you have a built Zen browser:

```bash
# Build Zen first
npm run build
```

### Running All Tests

```bash
# Run all Zen tests
npm test

# Alternative: Run with Python script directly
python3 scripts/run_tests.py
```

### Running Specific Test Categories

```bash
# Run specific test directory
python3 scripts/run_tests.py pinned

# Run workspace tests
python3 scripts/run_tests.py workspaces

# Run multiple test categories
python3 scripts/run_tests.py pinned workspaces
```

### Running Individual Tests

```bash
# Run specific test file (from engine directory)
cd engine
./mach mochitest zen/tests/pinned/browser_pinned_created.js
```

### Debug Mode Testing

```bash
# Run tests with debugger support
npm run test:dbg

# Run with debug-on-failure
python3 scripts/run_tests.py --jsdebugger --debug-on-failure
```

### Test Options

Common test options (passed to underlying mach command):

```bash
# Run in headless mode
python3 scripts/run_tests.py --headless

# Verbose output
python3 scripts/run_tests.py --verbose

# Run specific number of times
python3 scripts/run_tests.py --repeat 5

# Stop on first failure
python3 scripts/run_tests.py --stop-on-fail
```

## Writing Tests

### Basic Test Structure

Zen tests use Firefox's `add_task` framework:

```javascript
/* Any copyright is dedicated to the Public Domain.
   https://creativecommons.org/publicdomain/zero/1.0/ */

'use strict';

add_task(async function test_feature_name() {
  // Test setup
  await BrowserTestUtils.openNewForegroundTab(
    window.gBrowser, 
    'https://example.com/', 
    true
  );

  // Test logic
  const tab = gBrowser.selectedTab;
  
  // Assertions
  ok(tab, 'Tab should exist');
  Assert.equal(tab.getAttribute('src'), 'https://example.com/', 'URL should match');
  
  // Cleanup
  BrowserTestUtils.removeTab(tab);
});
```

### Testing Zen Components

#### Testing Workspace Creation

```javascript
add_task(async function test_workspace_creation() {
  // Setup
  const workspaceName = "Test Workspace";
  const workspaceIcon = "test";
  
  // Create workspace
  const workspace = await gZenWorkspaces.createWorkspace(workspaceName, workspaceIcon);
  
  // Assertions
  ok(workspace, 'Workspace should be created');
  ok(workspace.uuid, 'Workspace should have UUID');
  Assert.equal(workspace.name, workspaceName, 'Workspace name should match');
  Assert.equal(workspace.icon, workspaceIcon, 'Workspace icon should match');
  
  // Verify storage
  const savedWorkspaces = await ZenWorkspacesStorage.getWorkspaces();
  const savedWorkspace = savedWorkspaces.find(w => w.uuid === workspace.uuid);
  ok(savedWorkspace, 'Workspace should be saved to storage');
  
  // Cleanup
  await gZenWorkspaces.deleteWorkspace(workspace.uuid);
});
```

#### Testing Tab Management

```javascript
add_task(async function test_tab_workspace_assignment() {
  // Create test workspace
  const workspace = await gZenWorkspaces.createWorkspace("Test", "test");
  
  // Open new tab
  const tab = await BrowserTestUtils.openNewForegroundTab(
    window.gBrowser, 
    'https://example.com/'
  );
  
  // Move tab to workspace
  await gZenWorkspaces.moveTabToWorkspace(tab, workspace.uuid);
  
  // Verify assignment
  const tabWorkspace = tab.getAttribute('zen-workspace-uuid');
  Assert.equal(tabWorkspace, workspace.uuid, 'Tab should be assigned to workspace');
  
  // Cleanup
  BrowserTestUtils.removeTab(tab);
  await gZenWorkspaces.deleteWorkspace(workspace.uuid);
});
```

#### Testing Pinned Tabs

```javascript
add_task(async function test_pinned_tab_creation() {
  let resolvePromise;
  const promise = new Promise((resolve) => {
    resolvePromise = resolve;
  });

  // Open tab
  const tab = await BrowserTestUtils.openNewForegroundTab(
    window.gBrowser, 
    'https://example.com/'
  );

  // Listen for pinned event
  tab.addEventListener('ZenPinnedTabCreated', async function(event) {
    ok(tab.pinned, 'Tab should be pinned');
    
    const pinTabID = tab.getAttribute('zen-pin-id');
    ok(pinTabID, 'Tab should have zen-pin-id attribute');
    
    // Verify storage
    const pins = await ZenPinnedTabsStorage.getPins();
    const pinObject = pins.find(pin => pin.uuid === pinTabID);
    ok(pinObject, 'Pin should exist in storage');
    Assert.equal(pinObject.url, 'https://example.com/', 'Pin URL should match');
    
    resolvePromise();
  }, { once: true });

  // Pin the tab
  gBrowser.pinTab(tab);
  
  await promise;
  
  // Cleanup
  gBrowser.unpinTab(tab);
  BrowserTestUtils.removeTab(tab);
});
```

### Testing UI Components

#### Testing UI Element Visibility

```javascript
add_task(async function test_workspace_sidebar_visibility() {
  // Ensure workspaces are enabled
  await SpecialPowers.pushPrefEnv({
    set: [['zen.workspaces.enabled', true]]
  });
  
  // Check sidebar visibility
  const sidebar = document.getElementById('zen-workspaces-sidebar');
  ok(sidebar, 'Workspace sidebar should exist');
  is(sidebar.hidden, false, 'Workspace sidebar should be visible');
  
  // Disable workspaces
  await SpecialPowers.pushPrefEnv({
    set: [['zen.workspaces.enabled', false]]
  });
  
  // Check sidebar is hidden
  is(sidebar.hidden, true, 'Workspace sidebar should be hidden when disabled');
});
```

#### Testing Event Handling

```javascript
add_task(async function test_workspace_switch_event() {
  let eventFired = false;
  let eventWorkspace = null;
  
  // Listen for workspace change event
  const eventListener = (event) => {
    eventFired = true;
    eventWorkspace = event.detail.newWorkspace;
  };
  
  window.addEventListener('ZenWorkspaceChanged', eventListener);
  
  // Create and switch to workspace
  const workspace = await gZenWorkspaces.createWorkspace("Test", "test");
  await gZenWorkspaces.switchToWorkspace(workspace.uuid);
  
  // Verify event
  ok(eventFired, 'Workspace change event should fire');
  Assert.equal(eventWorkspace, workspace.uuid, 'Event should contain correct workspace');
  
  // Cleanup
  window.removeEventListener('ZenWorkspaceChanged', eventListener);
  await gZenWorkspaces.deleteWorkspace(workspace.uuid);
});
```

### Testing Preferences

```javascript
add_task(async function test_preference_integration() {
  // Test preference getting/setting
  const prefKey = 'zen.workspaces.enabled';
  const originalValue = Services.prefs.getBoolPref(prefKey, true);
  
  // Set preference
  Services.prefs.setBoolPref(prefKey, false);
  Assert.equal(Services.prefs.getBoolPref(prefKey), false, 'Preference should be set');
  
  // Test component respects preference
  const isEnabled = gZenWorkspaces.enabled;
  Assert.equal(isEnabled, false, 'Component should respect preference');
  
  // Restore original value
  Services.prefs.setBoolPref(prefKey, originalValue);
});
```

## Test Configuration

### Test Manifests (`browser.toml`)

Each test directory has a `browser.toml` file that defines test configuration:

```toml
[DEFAULT]
prefs = [
  "zen.workspaces.enabled=true",
  "zen.workspaces.debug=true"
]
support-files = [
  "head.js",
  "test_data.json"
]

["browser_workspace_creation.js"]
["browser_workspace_switching.js"]
["browser_workspace_deletion.js"]
```

### Test Support Files

#### `head.js` - Common Test Setup

```javascript
/* Test setup code shared across tests in this directory */

"use strict";

// Common test utilities
function createTestWorkspace(name = "Test Workspace", icon = "test") {
  return gZenWorkspaces.createWorkspace(name, icon);
}

function waitForWorkspaceEvent(eventName) {
  return new Promise(resolve => {
    window.addEventListener(eventName, resolve, { once: true });
  });
}

// Setup/teardown helpers
async function setupTestEnvironment() {
  // Common setup code
}

async function cleanupTestEnvironment() {
  // Common cleanup code
}
```

## Assertion Methods

### Firefox Test Assertions

```javascript
// Basic assertions
ok(condition, "Description");
is(actual, expected, "Description");
isnot(actual, notExpected, "Description");

// Advanced assertions
Assert.equal(actual, expected, "Description");
Assert.notEqual(actual, notExpected, "Description");
Assert.ok(condition, "Description");
Assert.throws(() => { /* code */ }, /Error/, "Should throw error");

// Async assertions
await Assert.rejects(promise, /Error/, "Promise should reject");
```

### Zen-Specific Assertions

```javascript
// Test workspace existence
function assertWorkspaceExists(uuid, message) {
  const workspace = gZenWorkspaces.getWorkspace(uuid);
  ok(workspace, message || `Workspace ${uuid} should exist`);
  return workspace;
}

// Test tab assignment
function assertTabInWorkspace(tab, workspaceUuid, message) {
  const tabWorkspace = tab.getAttribute('zen-workspace-uuid');
  Assert.equal(tabWorkspace, workspaceUuid, message || "Tab should be in correct workspace");
}
```

## Performance Testing

### Testing Performance Impact

```javascript
add_task(async function test_workspace_switch_performance() {
  // Create multiple workspaces with tabs
  const workspaces = [];
  for (let i = 0; i < 5; i++) {
    const workspace = await gZenWorkspaces.createWorkspace(`Workspace ${i}`, "test");
    workspaces.push(workspace);
    
    // Add tabs to workspace
    for (let j = 0; j < 10; j++) {
      const tab = await BrowserTestUtils.openNewForegroundTab(
        window.gBrowser, 
        `https://example.com/${j}`
      );
      await gZenWorkspaces.moveTabToWorkspace(tab, workspace.uuid);
    }
  }
  
  // Measure workspace switching time
  const startTime = performance.now();
  for (const workspace of workspaces) {
    await gZenWorkspaces.switchToWorkspace(workspace.uuid);
  }
  const endTime = performance.now();
  
  const switchTime = (endTime - startTime) / workspaces.length;
  ok(switchTime < 100, `Workspace switching should be fast (${switchTime}ms per switch)`);
  
  // Cleanup
  for (const workspace of workspaces) {
    await gZenWorkspaces.deleteWorkspace(workspace.uuid);
  }
});
```

## Common Test Patterns

### Setup and Cleanup

```javascript
add_task(async function test_with_cleanup() {
  let workspace, tab;
  
  try {
    // Setup
    workspace = await gZenWorkspaces.createWorkspace("Test", "test");
    tab = await BrowserTestUtils.openNewForegroundTab(window.gBrowser, 'https://example.com/');
    
    // Test logic
    await gZenWorkspaces.moveTabToWorkspace(tab, workspace.uuid);
    
    // Assertions
    const tabWorkspace = tab.getAttribute('zen-workspace-uuid');
    Assert.equal(tabWorkspace, workspace.uuid, "Tab should be in workspace");
    
  } finally {
    // Cleanup - always runs
    if (tab) {
      BrowserTestUtils.removeTab(tab);
    }
    if (workspace) {
      await gZenWorkspaces.deleteWorkspace(workspace.uuid);
    }
  }
});
```

### Testing Async Operations

```javascript
add_task(async function test_async_operation() {
  // Test async workspace creation
  const workspacePromise = gZenWorkspaces.createWorkspace("Async Test", "test");
  
  // Verify it's a promise
  ok(workspacePromise instanceof Promise, "Should return promise");
  
  // Wait for completion
  const workspace = await workspacePromise;
  ok(workspace, "Workspace should be created");
  
  // Cleanup
  await gZenWorkspaces.deleteWorkspace(workspace.uuid);
});
```

### Testing Error Conditions

```javascript
add_task(async function test_error_handling() {
  // Test invalid workspace creation
  await Assert.rejects(
    gZenWorkspaces.createWorkspace("", ""), 
    /Invalid workspace name/, 
    "Should reject empty workspace name"
  );
  
  // Test operation on non-existent workspace
  await Assert.rejects(
    gZenWorkspaces.switchToWorkspace("invalid-uuid"),
    /Workspace not found/,
    "Should reject invalid workspace UUID"
  );
});
```

## Debugging Tests

### Running Tests with Debug Output

```bash
# Enable debug logging
python3 scripts/run_tests.py --setpref zen.workspaces.debug=true

# Run with browser console visible
python3 scripts/run_tests.py --jsconsole

# Run with JavaScript debugger
python3 scripts/run_tests.py --jsdebugger
```

### Adding Debug Output

```javascript
add_task(async function test_with_debug() {
  // Use info() for debug output
  info("Starting workspace creation test");
  
  const workspace = await gZenWorkspaces.createWorkspace("Debug Test", "test");
  info(`Created workspace: ${workspace.uuid}`);
  
  // Use dump() for immediate output
  dump(`Workspace name: ${workspace.name}\n`);
  
  // Cleanup
  await gZenWorkspaces.deleteWorkspace(workspace.uuid);
  info("Test completed");
});
```

### Test Isolation

Ensure tests don't interfere with each other:

```javascript
add_task(async function test_isolated() {
  // Save initial state
  const initialWorkspaces = await gZenWorkspaces.getWorkspaces();
  const initialTabs = Array.from(gBrowser.tabs);
  
  try {
    // Test logic
    // ...
  } finally {
    // Restore initial state
    const currentWorkspaces = await gZenWorkspaces.getWorkspaces();
    for (const workspace of currentWorkspaces) {
      if (!initialWorkspaces.find(w => w.uuid === workspace.uuid)) {
        await gZenWorkspaces.deleteWorkspace(workspace.uuid);
      }
    }
    
    const currentTabs = Array.from(gBrowser.tabs);
    for (const tab of currentTabs) {
      if (!initialTabs.includes(tab)) {
        BrowserTestUtils.removeTab(tab);
      }
    }
  }
});
```

## Continuous Integration

Tests run automatically on:

- Pull requests
- Commits to main branches
- Nightly builds

### Test Report Generation

Test results are collected and reported in CI pipelines, including:

- Pass/fail status for each test
- Performance metrics
- Code coverage reports
- Test execution time

## Best Practices

1. **Write descriptive test names**: Clearly indicate what is being tested
2. **Test one thing per test**: Keep tests focused and simple
3. **Use appropriate assertions**: Choose the right assertion method for the test
4. **Clean up resources**: Always clean up tabs, workspaces, and other resources
5. **Test error conditions**: Don't just test the happy path
6. **Mock external dependencies**: Use mocks for network requests or external services
7. **Keep tests fast**: Avoid unnecessary delays or complex setup
8. **Document complex tests**: Add comments explaining non-obvious test logic

This testing guide ensures Zen Browser maintains high quality and reliability while enabling confident development of new features.