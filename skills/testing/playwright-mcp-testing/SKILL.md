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

**Development cycle:** Implement → Test → Refine → Re-test iteratively until no regressions.

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

### 2. Iterative Development with Visual Testing

**The core workflow for UI component development:**

UI development is inherently iterative. Expect to cycle through implementation → testing → refinement multiple times.

#### Iteration Cycle

**Step 1: Make Initial Changes**
- Implement component changes (styles, props, behavior)
- Review changes in Storybook manually
- Verify basic functionality works

**Step 2: Run Visual Tests**
```bash
npm run test:storybook:visual -- ComponentName
```

Or use MCP directly:
```typescript
playwright_navigate({
  url: "http://localhost:6006/?path=/story/components-button--primary"
})

playwright_screenshot({
  name: "button-primary-current",
  fullPage: false
})
```

**Step 3: Analyze Results**

Three possible outcomes:

**A. No regressions detected** ✓
- All visual tests pass
- Screenshots match baselines
- **Action:** Proceed to next component or complete work

**B. Intentional changes detected** ⚠️
- Tests fail because design intentionally changed
- Differences are expected and correct
- **Action:** Review diff images, update snapshots if correct:
  ```bash
  npm run test:storybook:visual -- ComponentName --update-snapshots
  ```
- **Important:** Commit updated snapshots with clear message explaining design change

**C. Unintentional regressions detected** ❌
- Tests fail due to bugs or side effects
- Visual differences are NOT intended
- **Action:** Proceed to Step 4

**Step 4: Refine Implementation**
- Analyze what caused the regression
- Identify specific styles/properties that need adjustment
- Make targeted fixes to component code
- **Do not update snapshots yet** - fix the code instead

**Step 5: Re-test**
- Run visual tests again
- Compare new results against baseline
- **If regressions remain:** Return to Step 4
- **If regressions resolved:** Tests should now pass

**Step 6: Verify All States**

Once base case passes, verify component states systematically:

```typescript
// Test hover state
playwright_hover({
  selector: "[data-testid='button']"
})
playwright_screenshot({
  name: "button-primary-hover",
  fullPage: false
})

// Test focus state
playwright_focus({
  selector: "[data-testid='button']"
})
playwright_screenshot({
  name: "button-primary-focus",
  fullPage: false
})

// Test disabled state
playwright_navigate({
  url: "http://localhost:6006/?path=/story/components-button--disabled"
})
playwright_screenshot({
  name: "button-disabled",
  fullPage: false
})
```

**If any state shows regression:** Return to Step 4 and refine

#### Iteration Mindset

**Expect multiple iterations:**
- First attempt rarely perfect
- CSS changes have cascading effects
- Different states may reveal new issues
- Cross-browser differences emerge

**Good practice:**
- Test frequently during development (after each logical change)
- Keep iterations small (fix one issue at a time)
- Document unexpected regressions for learning
- Don't update snapshots just to make tests pass - fix the code

**Red flags:**
- Updating snapshots without reviewing diffs
- Skipping re-testing after fixes
- Making multiple changes before testing
- Assuming first implementation is correct
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

### 5. Multi-Browser Testing

When verifying cross-browser compatibility, test systematically across browsers:

**Browser Testing Protocol:**

1. **Check MCP Capabilities First**
   - Verify if MCP server supports browser selection
   - Check available browsers: Chromium, Firefox, WebKit
   - If limited to single browser, document this limitation clearly

2. **Test Across All Available Browsers**
   ```typescript
   // If MCP supports browser selection (check server docs)
   const browsers = ['chromium', 'firefox', 'webkit'];
   
   for (const browser of browsers) {
     // Navigate with browser-specific context
     await playwright_navigate({
       url: "http://localhost:6006/?path=/story/button--primary",
       browser: browser  // If supported by MCP
     });
     
     await playwright_screenshot({
       name: `button-primary-${browser}`,
       fullPage: false
     });
   }
   ```

3. **Compare Cross-Browser Results**
   - Review screenshots side-by-side
   - Document browser-specific rendering differences
   - Note any browser-specific bugs or inconsistencies

4. **Fallback Strategy**
   - If MCP only supports single browser: Test in primary browser (Chromium)
   - Compare against existing multi-browser snapshots if available
   - Clearly state testing limitations in report
   - Suggest running full test suite with native Playwright for complete coverage

### 6. Comprehensive Visual Verification

When verifying components, systematically check ALL visual properties:

**Visual Property Checklist:**

- **Colors**
  - Foreground/text colors
  - Background colors
  - Border colors
  - Shadow colors
  - Gradient stops

- **Typography**
  - Font family
  - Font size
  - Font weight
  - Line height
  - Text alignment
  - Letter spacing

- **Spacing**
  - Padding (all sides)
  - Margin (all sides)
  - Gap between elements
  - Internal spacing in composite components

- **Sizing**
  - Width
  - Height
  - Min/max constraints
  - Size variations (small, medium, large)

- **Shapes**
  - Border radius
  - Border width
  - Border style
  - Outline properties

- **Effects**
  - Box shadows
  - Text shadows
  - Opacity
  - Transitions
  - Animations

- **States**
  - Default/idle
  - Hover
  - Focus
  - Active/pressed
  - Disabled
  - Loading
  - Error

- **Icons**
  - Size
  - Alignment
  - Spacing from text
  - Rendering quality
  - Color consistency

- **Layout**
  - Flexbox/grid alignment
  - Positioning (relative, absolute, fixed)
  - Z-index stacking
  - Overflow behavior

**Use `playwright_evaluate` to extract computed styles:**
```typescript
playwright_evaluate({
  expression: `() => {
    const el = document.querySelector('[data-testid="button"]');
    const styles = window.getComputedStyle(el);
    return {
      color: styles.color,
      backgroundColor: styles.backgroundColor,
      padding: styles.padding,
      fontSize: styles.fontSize,
      fontWeight: styles.fontWeight,
      borderRadius: styles.borderRadius
    };
  }`
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

## Systematic Testing Workflow

When testing components comprehensively, follow this structured approach:

### Step 1: Identify Test Scope

1. List all component stories/variants to test
2. Identify which browsers to test (if multi-browser supported)
3. Determine which visual properties are critical
4. Check for existing baseline snapshots:
   - `test/snapshots/`, `__snapshots__/`, `.playwright/snapshots/`
   - Look for browser-specific: `*-chromium.png`, `*-firefox.png`, `*-webkit.png`

### Step 2: Create Todo List

Use manage_todo_list to track testing progress:

```
1. Test Button component - Chromium
2. Test Button component - Firefox
3. Test Button component - WebKit
4. Test Card component - Chromium
5. Test Card component - Firefox
6. Test Card component - WebKit
7. Cross-browser comparison
8. Generate test report
```

### Step 3: Test Each Browser (if supported)

For EACH browser:
1. Launch browser context (if MCP supports)
2. Navigate to each story
3. Capture screenshot
4. Compare against baseline (if exists)
5. Verify visual properties using checklist
6. Document any differences
7. Update todo as complete

### Step 4: Cross-Browser Comparison

1. Compare screenshots between browsers
2. Identify browser-specific rendering issues
3. Document consistency/inconsistencies
4. Note any browser-specific bugs

### Step 5: Generate Comprehensive Report

Provide structured findings using the reporting format below.

## Testing Report Format

Always provide a comprehensive report after testing:

```markdown
## Testing Summary
- **Browsers Tested**: [Chromium / Firefox / WebKit / Chromium only (MCP limitation)]
- **Testing Method**: [Live MCP testing / Snapshot comparison / Both]
- **Stories Verified**: [Count and list]
- **Baseline Snapshots**: [Found / Not found / Partially available]

## Visual Properties Verified
- **Colors**: ✓/✗ [Details: foreground, background, borders, shadows]
- **Typography**: ✓/✗ [Details: font-size, weight, line-height, alignment]
- **Spacing**: ✓/✗ [Details: padding, margin, gaps]
- **Sizing**: ✓/✗ [Details: width, height, size variants]
- **Shapes**: ✓/✗ [Details: border-radius, borders, outlines]
- **Effects**: ✓/✗ [Details: shadows, opacity, transitions]
- **States**: ✓/✗ [Details: hover, focus, active, disabled, loading]
- **Icons**: ✓/✗ [Details: size, alignment, rendering]
- **Layout**: ✓/✗ [Details: flexbox/grid, positioning]

## Cross-Browser Consistency
- **Chromium vs Firefox**: [Findings or "Not tested"]
- **Chromium vs WebKit**: [Findings or "Not tested"]
- **Firefox vs WebKit**: [Findings or "Not tested"]

## Issues Found
[List specific issues with severity and affected browsers]

1. [Issue description] - [Browser(s)] - [Severity: Critical/High/Medium/Low]
2. [Issue description] - [Browser(s)] - [Severity: Critical/High/Medium/Low]

## Baseline Comparisons
[If snapshots exist]
- **Matches baseline**: [List components]
- **Differs from baseline**: [List components with description of differences]
- **New components (no baseline)**: [List components]

## Testing Limitations
[Be transparent about what couldn't be tested]

- MCP server only supports Chromium - Firefox/WebKit not tested
- Specific property X could not be verified due to [reason]
- Animation testing limited to static frames
- [Any other limitations]

## Recommendations
[Actionable next steps]

- Update snapshots for intentional changes
- Fix issues found in [component names]
- Run full Playwright test suite for complete multi-browser coverage
- Add missing test coverage for [scenarios]
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

## Systematic Refactoring with Visual Testing

When refactoring UI components (e.g., migrating styling approaches, updating component structure), visual testing ensures you haven't introduced regressions. Follow a systematic approach to guarantee completeness.

### Common Refactoring Scenarios

- Migrating styling systems (CSS-in-JS → CSS Modules, Tailwind → custom CSS)
- Updating component APIs (prop changes, composition patterns)
- Replacing UI libraries (switching icon libraries, design systems)
- Restructuring component hierarchies

### The Systematic Refactoring Workflow

**Critical principle:** Visual inspection misses things. Use tools to verify completeness.

#### Step 1: Identify All Occurrences

**Don't rely on reading code.** Use systematic searches:

```bash
# Search for all occurrences of pattern to replace
grep -n "pattern" path/to/component.tsx

# For styling refactors, find ALL className usages
grep -n "className" path/to/component.tsx

# Search across multiple files
grep -rn "pattern" src/components/ComponentName/

# Use regex for complex patterns
grep -E "(pattern1|pattern2)" path/to/component.tsx
```

**Check conditional JSX carefully:**
- Loading states: `{loading && <Icon className="..." />}`
- Error states: `{error && <span className="...">...</span>}`
- Conditional rendering: `{condition ? <A className="..." /> : <B />}`
- Portal content, tooltips, modals

**Context7 MCP for semantic search:**
When grep isn't enough (finding usage patterns, architectural understanding):
- Use Context7 to search codebase semantically
- Find similar patterns across files
- Understand component relationships
- Discover indirect usages

#### Step 2: Create Complete Replacement Plan

List EVERY occurrence found:
```
Component: Button.tsx
Occurrences to replace:
1. Line 45: className="size-4 animate-spin" (loading spinner)
2. Line 67: className="sr-only" (accessibility text)
3. Line 23-30: CVA variants (main styling system)
4. Line 102: className="flex gap-2" (conditional icon layout)
```

For each occurrence, plan the replacement:
```
1. size-4 animate-spin → .loadingSpinner in CSS Module
2. sr-only → .srOnly in CSS Module  
3. CVA variants → CSS Module variant classes
4. flex gap-2 → .iconLayout in CSS Module
```

#### Step 3: Implement Replacements Systematically

Work through the list one by one:
- Replace occurrence
- Mark as complete in checklist
- Don't skip ahead or assume anything is "similar enough"

**For styling refactors:**
Create replacement styles BEFORE removing old ones:
```css
/* button.module.scss */
.loadingSpinner {
  width: var(--spinner-size);
  height: var(--spinner-size);
  animation: spin 1s linear infinite;
}

.srOnly {
  position: absolute;
  width: 1px;
  height: 1px;
  /* ... full sr-only implementation */
}
```

Then replace in component:
```tsx
// Before
<Icon className="size-4 animate-spin" />

// After
<Icon className={styles.loadingSpinner} />
```

#### Step 4: Verify Complete Removal

**Search again** to confirm nothing was missed:

```bash
# For styling refactor - should find NO matches
grep -E "className=\"[a-z]+-" component.tsx

# For API changes - should find NO old prop usage
grep -n "oldPropName" component.tsx

# Verify all conditional branches
grep -n "className" component.tsx  # Should only show new styles
```

**Zero tolerance:** If search finds ANY remaining occurrences, you're not done.

#### Step 5: Run Visual Tests

```bash
npm run test:storybook:visual -- --grep "ComponentName"
```

**Baseline snapshots are source of truth:**
- DO NOT update snapshots to make tests pass
- Differences indicate your refactor changed visual output
- Fix implementation until tests pass

**If tests fail:**
1. Review diff images carefully
2. Identify which replacement caused the visual change
3. Adjust CSS/implementation to match baseline
4. Re-run tests
5. Repeat until tests pass

**Common visual regression causes:**
- Missing CSS properties in replacement
- Incorrect CSS variable usage
- Box model differences (margin vs padding)
- Specificity issues in CSS
- Missing pseudo-states (:hover, :focus)

#### Step 6: Test All Component States

Don't just test default state:
- All variants (primary, secondary, etc.)
- All sizes (small, medium, large)
- All states (default, hover, focus, active, disabled, loading)
- Dark/light themes
- Responsive breakpoints

```typescript
const states = ['default', 'hover', 'focus', 'disabled', 'loading'];
const variants = ['primary', 'secondary', 'tertiary'];

for (const variant of variants) {
  for (const state of states) {
    await playwright_navigate({
      url: `http://localhost:6006/?path=/story/button--${variant}-${state}`
    });
    await playwright_screenshot({
      name: `button-${variant}-${state}`,
      fullPage: false
    });
  }
}
```

### Using Context7 MCP for Refactoring

**When to use Context7:**

1. **Understanding component patterns:**
   - "How is this component used across the codebase?"
   - "What other components follow this pattern?"
   - "Are there similar styling approaches I should know about?"

2. **Finding indirect usages:**
   - Components that import and re-export
   - Higher-order components wrapping target
   - Composition patterns that aren't obvious from grep

3. **Learning project conventions:**
   - "What's the standard way to handle accessibility classes?"
   - "How do other components structure their CSS modules?"
   - "What design tokens are available?"

**Example Context7 queries:**
```
"Show me all components using the sr-only pattern"
"Find components with loading spinner animations"
"What's the pattern for CSS module variants in this project?"
```

### Refactoring Checklist

Use this checklist for every refactoring task:

- [ ] Used grep/search to find ALL occurrences (not visual inspection)
- [ ] Checked conditional JSX (loading, error, edge cases)
- [ ] Created complete replacement plan listing every occurrence
- [ ] Implemented replacements systematically (one by one)
- [ ] Verified complete removal with grep (zero remaining)
- [ ] Ran visual tests against baseline snapshots
- [ ] Tests pass without updating snapshots
- [ ] Tested all component variants
- [ ] Tested all component states (hover, focus, disabled, loading)
- [ ] Tested responsive/theme variations if applicable
- [ ] Verified component behavior unchanged (props, events, logic)
- [ ] Built project successfully
- [ ] No console errors in Storybook

**If you can't check all boxes, the refactor is incomplete.**

### Common Refactoring Mistakes

❌ **Visual inspection only** - Missing occurrences in conditional code
❌ **Incomplete search** - Not checking all file types or patterns
❌ **Assuming similarity** - "This is the same as the last one" (it's not)
❌ **Updating snapshots** - Making tests pass instead of fixing code
❌ **Testing one state** - Missing regressions in hover/focus/disabled
❌ **Ignoring accessibility** - Forgetting sr-only, aria attributes
❌ **Skipping edge cases** - Not testing error/loading/empty states

### Success Criteria for Refactoring

✅ Zero occurrences of old pattern (verified with grep)
✅ All visual regression tests pass (no snapshot updates)
✅ All component states tested and pass
✅ Component behavior unchanged (props, events work identically)
✅ Project builds successfully
✅ No new console errors or warnings
✅ Storybook renders correctly
✅ Code follows project conventions

## Related Skills

- **test-driven-development**: Apply TDD principles to UI component testing
- **systematic-debugging**: Debug failing visual tests methodically
- **verification-before-completion**: Always verify changes with tests before marking work complete

## Further Reading

- [Playwright Documentation](https://playwright.dev/)
- [Storybook Test Runner](https://storybook.js.org/docs/writing-tests/test-runner)
- [Visual Regression Testing Best Practices](https://playwright.dev/docs/test-snapshots)
- [Model Context Protocol Specification](https://modelcontextprotocol.io/)
