# Blue Filter Feature - Architecture Diagram

```
┌─────────────────────────────────────────────────────────────────┐
│                         USER INTERACTION                         │
└─────────────────────────────────────────────────────────────────┘
                                 │
                                 ▼
┌─────────────────────────────────────────────────────────────────┐
│                          popup.html                              │
│  ┌───────────────────────────────────────────────────────────┐  │
│  │  Quick Profiles Section                                   │  │
│  │  ┌──────────┐ ┌──────────┐ ┌──────────┐ ┌──────────────┐ │  │
│  │  │Dyslexia  │ │Low Vision│ │  Motor   │ │ Blue Filter  │ │  │
│  │  └──────────┘ └──────────┘ └──────────┘ └──────────────┘ │  │
│  │                                               ▲            │  │
│  │                                               │ Click      │  │
│  └───────────────────────────────────────────────┼───────────┘  │
└───────────────────────────────────────────────────┼──────────────┘
                                                    │
                                                    ▼
┌─────────────────────────────────────────────────────────────────┐
│                          popup.js                                │
│                                                                   │
│  1. setupProfileButtons()                                        │
│     └─> Listens for button clicks                               │
│                                                                   │
│  2. applyProfile('blue-filter')                                  │
│     ├─> Resets all settings to defaults                         │
│     ├─> Applies profile settings (bgColor, textColor)           │
│     ├─> Gets current settings from UI                           │
│     ├─> Adds blueFilterEnabled: true                            │
│     └─> Saves to chrome.storage.sync                            │
│                                                                   │
│  3. chrome.scripting.executeScript()                             │
│     └─> Injects applyAccessibilityStyles() into active tab      │
│                                                                   │
│  Profile Configuration:                                          │
│  ┌─────────────────────────────────────────────────────────┐   │
│  │ 'blue-filter': {                                        │   │
│  │   bgColor: '#FFF6D9',          // Warm yellow bg        │   │
│  │   textColor: '#4A3B00',        // Dark brown text       │   │
│  │   blueFilterEnabled: true      // Enable flag           │   │
│  │ }                                                        │   │
│  └─────────────────────────────────────────────────────────┘   │
└───────────────────────────────────────────────────────────────────┘
                                 │
                                 ▼
┌─────────────────────────────────────────────────────────────────┐
│                      chrome.storage.sync                         │
│  ┌─────────────────────────────────────────────────────────┐   │
│  │ {                                                        │   │
│  │   bgColor: '#FFF6D9',                                    │   │
│  │   textColor: '#4A3B00',                                  │   │
│  │   blueFilterEnabled: true,                               │   │
│  │   fontSize: 16,                                          │   │
│  │   ... other settings ...                                 │   │
│  │ }                                                        │   │
│  └─────────────────────────────────────────────────────────┘   │
└───────────────────────────────────────────────────────────────────┘
                                 │
                                 ▼
┌─────────────────────────────────────────────────────────────────┐
│                         content.js                               │
│                     (Injected into webpage)                      │
│                                                                   │
│  1. applyAccessibilityStyles(settings)                           │
│     ├─> Creates <style id="eduadapt-accessibility-style">       │
│     ├─> Applies bgColor: #FFF6D9                                │
│     ├─> Applies textColor: #4A3B00                              │
│     ├─> Checks if settings.blueFilterEnabled === true           │
│     └─> Calls enableBlueFilter()                                │
│                                                                   │
│  2. enableBlueFilter()                                           │
│     ├─> Calls disableBlueFilter() (cleanup first)               │
│     ├─> Creates overlay div:                                    │
│     │   ┌─────────────────────────────────────────────────┐    │
│     │   │ <div id="betterweb-bluefilter-overlay">         │    │
│     │   │   background: rgba(255, 220, 120, 0.15)         │    │
│     │   │   pointer-events: none                          │    │
│     │   │   z-index: 999999                               │    │
│     │   └─────────────────────────────────────────────────┘    │
│     │                                                            │
│     └─> Creates style block:                                    │
│         ┌─────────────────────────────────────────────────┐    │
│         │ <style id="betterweb-bluefilter">               │    │
│         │   header, nav {                                 │    │
│         │     background-color: #FFF8E1 !important;       │    │
│         │     color: #4A3B00 !important;                  │    │
│         │   }                                              │    │
│         │   button {                                       │    │
│         │     background-color: #FFE082 !important;       │    │
│         │     color: #4A3B00 !important;                  │    │
│         │   }                                              │    │
│         │   .card, .panel {                                │    │
│         │     background-color: #FFF9C4 !important;       │    │
│         │   }                                              │    │
│         └─────────────────────────────────────────────────┘    │
│                                                                   │
│  3. disableBlueFilter()                                          │
│     ├─> Removes <div id="betterweb-bluefilter-overlay">         │
│     └─> Removes <style id="betterweb-bluefilter">               │
└───────────────────────────────────────────────────────────────────┘
                                 │
                                 ▼
┌─────────────────────────────────────────────────────────────────┐
│                       WEBPAGE DOM                                │
│  ┌─────────────────────────────────────────────────────────┐   │
│  │ <html>                                                   │   │
│  │   <head>                                                 │   │
│  │     <style id="eduadapt-accessibility-style">           │   │
│  │       html, body {                                       │   │
│  │         background-color: #FFF6D9 !important;           │   │
│  │         color: #4A3B00 !important;                      │   │
│  │       }                                                  │   │
│  │     </style>                                             │   │
│  │     <style id="betterweb-bluefilter">                   │   │
│  │       header { background-color: #FFF8E1 !important; }  │   │
│  │       button { background-color: #FFE082 !important; }  │   │
│  │     </style>                                             │   │
│  │   </head>                                                │   │
│  │   <body>                                                 │   │
│  │     <div id="betterweb-bluefilter-overlay"></div>       │   │
│  │     <!-- Original page content -->                       │   │
│  │   </body>                                                │   │
│  │ </html>                                                  │   │
│  └─────────────────────────────────────────────────────────┘   │
└───────────────────────────────────────────────────────────────────┘
                                 │
                                 ▼
┌─────────────────────────────────────────────────────────────────┐
│                      VISUAL RESULT                               │
│  ┌─────────────────────────────────────────────────────────┐   │
│  │  ┌─────────────────────────────────────────────────┐    │   │
│  │  │ Header (Warm Yellow #FFF8E1)                    │    │   │
│  │  └─────────────────────────────────────────────────┘    │   │
│  │                                                          │   │
│  │  Page Background: Soft Warm Yellow (#FFF6D9)            │   │
│  │  Text Color: Dark Brown (#4A3B00)                       │   │
│  │                                                          │   │
│  │  ┌──────────────┐  ┌──────────────┐                    │   │
│  │  │ Button       │  │ Button       │                    │   │
│  │  │ (#FFE082)    │  │ (#FFE082)    │                    │   │
│  │  └──────────────┘  └──────────────┘                    │   │
│  │                                                          │   │
│  │  ┌─────────────────────────────────────────────────┐   │   │
│  │  │ Card/Panel (Warm Yellow #FFF9C4)                │   │   │
│  │  │ Content with dark brown text...                 │   │   │
│  │  └─────────────────────────────────────────────────┘   │   │
│  │                                                          │   │
│  │  [15% warm overlay tint across entire page]             │   │
│  └─────────────────────────────────────────────────────────┘   │
└───────────────────────────────────────────────────────────────────┘

═══════════════════════════════════════════════════════════════════

DEACTIVATION FLOW:

User clicks "Reset All" or another profile
                │
                ▼
        popup.js resets settings
                │
                ▼
        chrome.storage.sync.clear()
                │
                ▼
        content.js: removeAccessibilityStyles()
                │
                ├─> Removes <style id="eduadapt-accessibility-style">
                └─> Calls disableBlueFilter()
                    ├─> Removes <div id="betterweb-bluefilter-overlay">
                    └─> Removes <style id="betterweb-bluefilter">
                │
                ▼
        Page returns to normal appearance

═══════════════════════════════════════════════════════════════════

KEY DESIGN DECISIONS:

1. Separate Flag (blueFilterEnabled)
   - No UI element needed
   - Handled programmatically in applyProfile()
   - Stored in chrome.storage.sync

2. Two-Layer Approach
   - Base styles via applyAccessibilityStyles()
   - Additional overlay + styles via enableBlueFilter()
   - Clean separation of concerns

3. Unique IDs
   - betterweb-bluefilter-overlay (overlay div)
   - betterweb-bluefilter (style block)
   - Prevents conflicts with other features

4. Non-Intrusive Overlay
   - pointer-events: none
   - Doesn't block user interaction
   - Pure visual effect

5. !important Styles
   - Ensures styles override page CSS
   - Necessary for consistent appearance
   - Applied to all Blue Filter styles

═══════════════════════════════════════════════════════════════════
