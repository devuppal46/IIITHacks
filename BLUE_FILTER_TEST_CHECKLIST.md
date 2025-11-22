# Blue Filter Feature - Test Checklist

## Pre-Testing Setup
- [ ] Load the extension in Chrome (chrome://extensions, enable Developer mode, Load unpacked)
- [ ] Open a test webpage (e.g., https://example.com, news site, or documentation page)

## Basic Functionality Tests

### 1. Blue Filter Activation
- [ ] Open extension popup
- [ ] Verify "Blue Filter" button is visible in Quick Profiles section
- [ ] Click "Blue Filter" button
- [ ] Verify button shows active state (highlighted)
- [ ] Check that page background changes to warm yellowish tone (#FFF6D9)
- [ ] Check that text color changes to dark brown (#4A3B00)
- [ ] Verify warm overlay is visible (subtle yellowish tint)

### 2. Landmark Elements Styling
- [ ] Check headers have warm background (#FFF8E1)
- [ ] Check navigation elements have warm background
- [ ] Check buttons have yellowish background (#FFE082)
- [ ] Check cards/panels have warm background (#FFF9C4)

### 3. Overlay Behavior
- [ ] Verify overlay doesn't block mouse clicks
- [ ] Verify overlay doesn't block text selection
- [ ] Verify overlay stays on top of page content
- [ ] Check that scrolling works normally

### 4. Persistence
- [ ] Apply Blue Filter
- [ ] Reload the page
- [ ] Verify Blue Filter is still active
- [ ] Navigate to a different page on the same site
- [ ] Verify Blue Filter applies to the new page

### 5. Deactivation
- [ ] Click "Reset All" button
- [ ] Verify Blue Filter is removed
- [ ] Check that overlay is gone
- [ ] Check that warm colors are removed
- [ ] Verify page returns to normal appearance

### 6. Profile Switching
- [ ] Apply Blue Filter
- [ ] Click "Dyslexia" profile button
- [ ] Verify Blue Filter is removed
- [ ] Verify Dyslexia profile is applied
- [ ] Switch back to Blue Filter
- [ ] Verify it reapplies correctly

### 7. Multiple Tabs
- [ ] Apply Blue Filter in Tab 1
- [ ] Open Tab 2 with a different website
- [ ] Verify Blue Filter applies to Tab 2
- [ ] Switch between tabs
- [ ] Verify both tabs have Blue Filter active

## Edge Cases

### 8. Dynamic Content
- [ ] Apply Blue Filter
- [ ] Trigger dynamic content loading (scroll, click buttons)
- [ ] Verify new content also has warm tones
- [ ] Check that overlay remains intact

### 9. Restricted Pages
- [ ] Try applying Blue Filter on chrome://extensions
- [ ] Verify extension handles gracefully (no errors)
- [ ] Check console for error messages (should be none or handled)

### 10. Storage
- [ ] Apply Blue Filter
- [ ] Close browser completely
- [ ] Reopen browser
- [ ] Navigate to a webpage
- [ ] Verify Blue Filter is still active

## Visual Quality Checks

### 11. Color Contrast
- [ ] Verify text is readable against warm background
- [ ] Check that links are distinguishable
- [ ] Verify buttons are clearly visible
- [ ] Check form inputs are usable

### 12. No Conflicts
- [ ] Apply Blue Filter
- [ ] Manually adjust font size slider
- [ ] Verify both features work together
- [ ] Try enabling "Highlight Links"
- [ ] Verify both features coexist properly

## Performance

### 13. Speed
- [ ] Measure time to apply Blue Filter (should be instant)
- [ ] Check for any lag when scrolling
- [ ] Verify no performance degradation

### 14. Memory
- [ ] Open DevTools > Performance
- [ ] Apply and remove Blue Filter multiple times
- [ ] Check for memory leaks
- [ ] Verify clean removal of DOM elements

## Console Checks

### 15. No Errors
- [ ] Open DevTools Console
- [ ] Apply Blue Filter
- [ ] Verify no JavaScript errors
- [ ] Remove Blue Filter
- [ ] Verify no errors during removal

## Expected Results Summary

✅ **Success Criteria:**
- Blue Filter button appears in popup
- Clicking applies warm yellowish tones immediately
- Background: #FFF6D9, Text: #4A3B00
- Warm overlay visible but doesn't block interaction
- Headers, buttons, cards get warm color variants
- Settings persist across page loads and browser restarts
- Clean removal when switching profiles or resetting
- No JavaScript errors in console
- Works on all regular web pages (http/https)

❌ **Known Limitations:**
- Does not work on chrome:// pages (expected)
- Does not work on extension pages (expected)
- May not affect some heavily styled components (acceptable)

## Bug Reporting Template

If issues are found:
```
**Issue:** [Brief description]
**Steps to Reproduce:**
1. [Step 1]
2. [Step 2]
3. [Step 3]

**Expected:** [What should happen]
**Actual:** [What actually happened]
**Browser:** Chrome [version]
**Console Errors:** [Any errors from DevTools]
**Screenshot:** [If applicable]
```
