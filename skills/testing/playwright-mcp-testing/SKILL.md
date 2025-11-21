---
name: Playwright MCP Testing
description: Use Playwright MCP server for UI testing with Storybook components
when_to_use: when testing UI components, verifying visual changes, automating Storybook interactions, building component libraries, updating design systems, or testing component variations
version: 1.0.0
languages: typescript, javascript
---

# Playwright MCP Testing

## Overview

Use the Playwright MCP (Model Context Protocol) server to automate UI testing with Storybook. The MCP server provides tools to navigate, interact with, and capture screenshots of components for visual regression testing and component verification.

**Core principle:** Automate visual testing and component interaction through MCP tools rather than manual Storybook navigation.

## When to Use

**Primary use cases:**
- After making UI component changes (styles, props, behavior)
- Verifying component states and variations work correctly
- Running visual regression tests against Storybook stories
- Generating new test cases for components
- Accessibility testing with automated tools

**When NOT to use:**
- Initial exploratory testing (use Storybook UI directly)
- Complex user workflows spanning multiple pages (use full Playwright tests)
- When MCP server is not configured or unavailable

## Core Workflows

### 1. Running Visual Tests Against Storybook

The primary workflow for visual regression testing:

```bash
# Run all visual tests against Storybook
npm run test:storybook:visual

# Run tests for specific component
npm run test:storybook:visual -- ComponentName
```

**What this does:**
- Starts Storybook server automatically
- Navigates to each story
- Captures screenshots
- Compares against baseline snapshots
- Reports differences

### 2. Verifying Component Changes

After modifying a component, verify all its states:

**Step 1:** Use MCP to navigate to component story
```typescript
playwright_navigate({
  url: "http://localhost:6006/?path=/story/components-button--primary"
})
```

**Step 2:** Capture current state
```typescript
playwright_screenshot({
  name: "button-primary-current",
  fullPage: false
})
```

**Step 3:** Interact with component to test states
```typescript
playwright_click({
  selector: "button[data-testid='primary-button']"
})
```

**Step 4:** Capture after interaction
```typescript
playwright_screenshot({
  name: "button-primary-clicked",
  fullPage: false
})
```

### 3. Generating New Tests

When adding new components, generate test structure:

1. Identify component stories in Storybook
2. Use MCP to navigate and capture baseline screenshots
3. Create test file referencing captured states
4. Commit baseline screenshots to repository

### 4. Accessibility Testing

Use Playwright's built-in accessibility tools via MCP:

```typescript
// Navigate to story
playwright_navigate({
  url: "http://localhost:6006/?path=/story/components-form--login"
})

// Run accessibility checks (example with axe)
// Note: Specific MCP tool may vary by server implementation
playwright_evaluate({
  expression: "() => axe.run()"
})
```

## MCP Tools Reference

### playwright_navigate
Navigate to a URL (typically a Storybook story).

**Parameters:**
- `url` (string): Full URL to navigate to

**Example:**
```typescript
playwright_navigate({
  url: "http://localhost:6006/?path=/story/components-card--default"
})
```

### playwright_screenshot
Capture a screenshot of current page or element.

**Parameters:**
- `name` (string): Filename for screenshot (without extension)
- `fullPage` (boolean): Capture entire page vs viewport
- `selector` (string, optional): Specific element to capture

**Example:**
```typescript
playwright_screenshot({
  name: "card-default-state",
  fullPage: false,
  selector: "[data-testid='card-component']"
})
```

### playwright_click
Click an element on the page.

**Parameters:**
- `selector` (string): CSS selector for element to click

**Example:**
```typescript
playwright_click({
  selector: "button[data-testid='toggle-button']"
})
```

### playwright_fill
Fill input field with text.

**Parameters:**
- `selector` (string): CSS selector for input element
- `value` (string): Text to fill

**Example:**
```typescript
playwright_fill({
  selector: "input[name='username']",
  value: "testuser"
})
```

### playwright_evaluate
Execute JavaScript in browser context.

**Parameters:**
- `expression` (string): JavaScript code to execute

**Example:**
```typescript
playwright_evaluate({
  expression: "() => document.querySelector('.theme-toggle').click()"
})
```

## Integration Guidance

### When to Use Playwright MCP vs Other Testing Approaches

**Use Playwright MCP when:**
- Testing individual component variations in Storybook
- Verifying visual changes after CSS/style updates
- Capturing component screenshots for documentation
- Quick interactive testing during development
- Automated visual regression on component library

**Use Manual Testing when:**
- Initial component exploration and design review
- Complex interactions requiring human judgment
- Testing "feel" and animations subjectively
- Stakeholder demos and presentations

**Use Full Playwright E2E when:**
- Testing complete user workflows across pages
- Integration testing with real APIs
- Performance testing and network conditions
- Cross-browser compatibility testing

### Interpreting Test Results

**All tests pass:** Component changes are visually consistent with baselines.

**Screenshot differences detected:**
1. Review diff images in test output directory
2. Determine if differences are intentional (new design) or bugs
3. If intentional: Update snapshots
4. If bugs: Fix component and re-run tests

**Tests fail to run:**
- Check Storybook server is starting correctly
- Verify MCP server connection (check VS Code output)
- Ensure story paths are correct

### Updating Snapshots

When component changes are intentional:

```bash
# Update all snapshots
npm run test:storybook:visual -- --update-snapshots

# Update specific component snapshots
npm run test:storybook:visual -- ComponentName --update-snapshots
```

**Important:** Review changes before committing updated snapshots to ensure they reflect intended changes only.

## Repository Structure References

### Unily.App Test Structure

```
apps/portal-e2e/
  src/
    storybook.visual.spec.ts    # Visual test configurations
    
.storybook/
  storybook.visual.config.ts    # Visual testing setup
  
snapshots/                       # Baseline screenshots
  components/
    Button/
      primary.png
      secondary.png
```

### Test Configuration

The `storybook.visual.config.ts` typically includes:
- Viewport sizes for responsive testing
- Screenshot comparison thresholds
- Story selection patterns
- Browser configurations

## Common Patterns

### Testing All Component Variants

```typescript
const variants = ['primary', 'secondary', 'disabled'];
for (const variant of variants) {
  await playwright_navigate({
    url: `http://localhost:6006/?path=/story/button--${variant}`
  });
  await playwright_screenshot({
    name: `button-${variant}`,
    fullPage: false
  });
}
```

### Testing Responsive Designs

```typescript
const viewports = [
  { width: 375, height: 667, name: 'mobile' },
  { width: 1024, height: 768, name: 'tablet' },
  { width: 1920, height: 1080, name: 'desktop' }
];

for (const viewport of viewports) {
  // Set viewport via MCP (method depends on server implementation)
  await playwright_screenshot({
    name: `component-${viewport.name}`,
    fullPage: false
  });
}
```

### Testing Dark/Light Themes

```typescript
// Navigate to story
await playwright_navigate({
  url: "http://localhost:6006/?path=/story/components-card--default"
});

// Capture light theme
await playwright_screenshot({
  name: "card-light-theme",
  fullPage: false
});

// Switch to dark theme (example)
await playwright_evaluate({
  expression: "() => document.documentElement.setAttribute('data-theme', 'dark')"
});

// Capture dark theme
await playwright_screenshot({
  name: "card-dark-theme",
  fullPage: false
});
```

## Troubleshooting

### MCP Server Not Connecting

**Symptoms:** MCP tools not available in VS Code

**Solutions:**
1. Check MCP server is running: Check VS Code Output panel → Model Context Protocol
2. Verify MCP server configuration in VS Code settings
3. Restart VS Code to reinitialize MCP connection
4. Check MCP server logs for errors

### Storybook Not Starting

**Symptoms:** Tests fail with "Cannot connect to Storybook"

**Solutions:**
1. Start Storybook manually first: `npm run storybook`
2. Verify Storybook port (default 6006) is not in use
3. Check for build errors in Storybook console
4. Clear Storybook cache: `npm run storybook -- --no-manager-cache`

### Windows Path Problems

**Symptoms:** Screenshot paths fail or tests can't find files

**Solutions:**
1. Use forward slashes in paths: `snapshots/component.png` not `snapshots\component.png`
2. Check for spaces in paths (quote paths if needed)
3. Verify screenshot output directory exists and is writable
4. Use absolute paths when relative paths fail

### Screenshot Comparison Failures

**Symptoms:** Tests report differences but images look identical

**Solutions:**
1. Check screenshot comparison threshold in config (too strict?)
2. Verify system font rendering hasn't changed
3. Check for anti-aliasing differences between environments
4. Regenerate baselines on consistent environment (CI or specific dev machine)

### Test Timeouts

**Symptoms:** Tests hang or timeout waiting for elements

**Solutions:**
1. Increase timeout values in test configuration
2. Check if component is waiting for async data loads
3. Use explicit waits for dynamic content
4. Verify network requests complete in Storybook

## Best Practices

### 1. Organize Screenshots by Component

Keep screenshot baselines organized:
```
snapshots/
  Button/
    primary.png
    secondary.png
  Card/
    default.png
    with-image.png
```

### 2. Use Descriptive Screenshot Names

Good: `button-primary-hover-state.png`
Bad: `test1.png`

### 3. Test One Thing at a Time

Each test should verify a specific component state or interaction, not multiple behaviors.

### 4. Keep Baselines Updated

Regularly review and update screenshot baselines as design evolves. Don't let baselines become stale.

### 5. Run Tests Before Committing

Always run visual tests before pushing UI changes to catch unintended regressions.

### 6. Document Test Intent

Add comments explaining what each test verifies, especially for complex interactions.

### 7. Use Data Test IDs

Prefer `data-testid` selectors over class names for stability:
```typescript
playwright_click({
  selector: "[data-testid='submit-button']"  // Good
  // vs
  selector: ".btn.btn-primary.submit"        // Fragile
})
```

## Related Skills

- **test-driven-development**: Apply TDD principles to UI component testing
- **systematic-debugging**: Debug failing visual tests methodically
- **verification-before-completion**: Always verify changes with tests before marking work complete

## Further Reading

- [Playwright Documentation](https://playwright.dev/)
- [Storybook Test Runner](https://storybook.js.org/docs/writing-tests/test-runner)
- [Visual Regression Testing Best Practices](https://playwright.dev/docs/test-snapshots)
- [Model Context Protocol Specification](https://modelcontextprotocol.io/)
