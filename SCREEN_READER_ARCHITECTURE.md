# Screen Reader Feature - Architecture Diagram

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
│  │  [Dyslexia] [Low Vision] [Motor] [Cognitive] [Blue Filter]│  │
│  └───────────────────────────────────────────────────────────┘  │
│  ┌───────────────────────────────────────────────────────────┐  │
│  │  Screen Reader Section                    ← NEW FEATURE   │  │
│  │  ┌─────────────────────────────────────────────────────┐  │  │
│  │  │ Enable Screen Reader  [Toggle Checkbox]             │  │  │
│  │  │ Click any element on the page to hear it read aloud │  │  │
│  │  └─────────────────────────────────────────────────────┘  │  │
│  └───────────────────────────────────────────────────────────┘  │
└─────────────────────────────────────────────────────────────────┘
                                 │
                                 ▼ User toggles checkbox
┌─────────────────────────────────────────────────────────────────┐
│                          popup.js                                │
│                                                                   │
│  1. Event Listener: screenReaderToggle.change                   │
│     └─> handleScreenReaderToggle()                              │
│                                                                   │
│  2. handleScreenReaderToggle()                                   │
│     ├─> Get checkbox state (enabled/disabled)                   │
│     ├─> Save to chrome.storage.sync                             │
│     │   { screenReaderEnabled: true/false }                     │
│     └─> Inject function into active tab                         │
│         ├─> If enabled: inject enableScreenReader()             │
│         └─> If disabled: inject disableScreenReader()           │
│                                                                   │
│  3. Placeholder Functions (call global functions in content.js) │
│     ├─> enableScreenReader()                                    │
│     │   └─> window.betterWebEnableScreenReader()                │
│     └─> disableScreenReader()                                   │
│         └─> window.betterWebDisableScreenReader()               │
└───────────────────────────────────────────────────────────────────┘
                                 │
                                 ▼
┌─────────────────────────────────────────────────────────────────┐
│                      chrome.storage.sync                         │
│  ┌─────────────────────────────────────────────────────────┐   │
│  │ {                                                        │   │
│  │   screenReaderEnabled: true,                             │   │
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
│  Global State Variables:                                         │
│  ├─> screenReaderActive = false                                 │
│  ├─> screenReaderClickHandler = null                            │
│  └─> currentHighlightedElement = null                           │
│                                                                   │
│  1. enableScreenReader()                                         │
│     ├─> Set screenReaderActive = true                           │
│     ├─> Create click handler function                           │
│     │   └─> screenReaderClickHandler(event)                     │
│     ├─> Add event listener:                                     │
│     │   document.addEventListener('click', handler, true)       │
│     ├─> Show notification: "Screen Reader Enabled"              │
│     └─> Add visual indicator badge                              │
│                                                                   │
│  2. screenReaderClickHandler(event)                              │
│     ├─> event.preventDefault()                                  │
│     ├─> event.stopPropagation()                                 │
│     ├─> Remove previous highlight                               │
│     ├─> Highlight current element:                              │
│     │   ├─> outline: 3px solid #4CAF50                          │
│     │   └─> outlineOffset: 2px                                  │
│     ├─> Extract text: getElementText(element)                   │
│     ├─> Speak text: speakText(text)                             │
│     └─> Show notification with preview                          │
│                                                                   │
│  3. getElementText(element)                                      │
│     ├─> Check aria-label                                        │
│     ├─> Check alt text (images)                                 │
│     ├─> Check title attribute                                   │
│     ├─> Check placeholder (inputs)                              │
│     ├─> Check value (inputs)                                    │
│     ├─> Check textContent                                       │
│     ├─> Check innerText                                         │
│     ├─> Clean whitespace                                        │
│     └─> Limit to 500 characters                                 │
│                                                                   │
│  4. speakText(text)                                              │
│     ├─> Check if speechSynthesis is supported                   │
│     ├─> Cancel any ongoing speech                               │
│     ├─> Create SpeechSynthesisUtterance                         │
│     ├─> Configure settings:                                     │
│     │   ├─> rate: 1.0 (normal speed)                            │
│     │   ├─> pitch: 1.0 (normal pitch)                           │
│     │   └─> volume: 1.0 (full volume)                           │
│     ├─> Select English voice if available                       │
│     └─> window.speechSynthesis.speak(utterance)                 │
│                                                                   │
│  5. addScreenReaderIndicator()                                   │
│     ├─> Create div element                                      │
│     ├─> Set ID: betterweb-screenreader-indicator                │
│     ├─> Set content: "🔊 Screen Reader Active"                  │
│     ├─> Style:                                                   │
│     │   ├─> position: fixed; bottom: 20px; right: 20px          │
│     │   ├─> background: linear-gradient(#4CAF50, #45a049)       │
│     │   ├─> border-radius: 25px                                 │
│     │   └─> animation: betterwebPulse 2s infinite               │
│     └─> Append to document.body                                 │
│                                                                   │
│  6. disableScreenReader()                                        │
│     ├─> Set screenReaderActive = false                          │
│     ├─> Remove event listener                                   │
│     ├─> Remove highlight from current element                   │
│     ├─> Cancel ongoing speech                                   │
│     ├─> Remove visual indicator                                 │
│     └─> Show notification: "Screen Reader Disabled"             │
│                                                                   │
│  7. Auto-Enable on Page Load                                     │
│     ├─> chrome.storage.sync.get(settings)                       │
│     ├─> Check if settings.screenReaderEnabled === true          │
│     └─> If true: setTimeout(() => enableScreenReader(), 600)    │
│                                                                   │
│  8. Global Function Exports                                      │
│     ├─> window.betterWebEnableScreenReader = enableScreenReader │
│     └─> window.betterWebDisableScreenReader = disableScreenReader│
└───────────────────────────────────────────────────────────────────┘
                                 │
                                 ▼
┌─────────────────────────────────────────────────────────────────┐
│                       WEBPAGE DOM                                │
│  ┌─────────────────────────────────────────────────────────┐   │
│  │ <body>                                                   │   │
│  │   <!-- Original page content -->                         │   │
│  │   <h1>Welcome</h1>                                       │   │
│  │   <p>This is some text</p>                               │   │
│  │   <button>Click me</button>                              │   │
│  │                                                          │   │
│  │   <!-- BetterWeb Screen Reader Indicator -->             │   │
│  │   <div id="betterweb-screenreader-indicator">           │   │
│  │     🔊 Screen Reader Active                              │   │
│  │   </div>                                                 │   │
│  │ </body>                                                  │   │
│  └─────────────────────────────────────────────────────────┘   │
└───────────────────────────────────────────────────────────────────┘
                                 │
                                 ▼ User clicks element
┌─────────────────────────────────────────────────────────────────┐
│                      USER EXPERIENCE                             │
│  ┌─────────────────────────────────────────────────────────┐   │
│  │  1. Element gets green highlight                        │   │
│  │     outline: 3px solid #4CAF50                           │   │
│  │                                                          │   │
│  │  2. Text is extracted from element                       │   │
│  │     (aria-label, alt, title, placeholder, value, text)   │   │
│  │                                                          │   │
│  │  3. Text is spoken aloud                                 │   │
│  │     Using browser's text-to-speech                       │   │
│  │                                                          │   │
│  │  4. Notification appears                                 │   │
│  │     "Reading: [text preview]"                            │   │
│  └─────────────────────────────────────────────────────────┘   │
└───────────────────────────────────────────────────────────────────┘

═══════════════════════════════════════════════════════════════════

CLICK EVENT FLOW:

User clicks element on page
        │
        ▼
screenReaderClickHandler() fires (capture phase)
        │
        ├─> event.preventDefault() ← Prevents normal click
        ├─> event.stopPropagation() ← Stops event bubbling
        │
        ▼
Remove previous highlight (if any)
        │
        ▼
Highlight clicked element
        │
        ▼
Extract text from element
        │
        ├─> Check aria-label
        ├─> Check alt
        ├─> Check title
        ├─> Check placeholder
        ├─> Check value
        ├─> Check textContent
        └─> Check innerText
        │
        ▼
Clean and limit text (max 500 chars)
        │
        ▼
Speak text using speechSynthesis
        │
        ├─> Cancel previous speech
        ├─> Create utterance
        ├─> Set rate, pitch, volume
        ├─> Select voice
        └─> Speak
        │
        ▼
Show notification with text preview

═══════════════════════════════════════════════════════════════════

KEY DESIGN DECISIONS:

1. Event Capture Phase
   - Uses capture: true in addEventListener
   - Intercepts clicks before they reach elements
   - Allows preventDefault() to work effectively

2. Global Function Exports
   - window.betterWebEnableScreenReader
   - window.betterWebDisableScreenReader
   - Allows popup.js to call functions in content.js

3. State Management
   - screenReaderActive flag prevents duplicate listeners
   - currentHighlightedElement tracks what's highlighted
   - screenReaderClickHandler stored for removal

4. Text Extraction Priority
   - Accessibility attributes first (aria-label)
   - Visual alternatives second (alt, title)
   - Form values third (placeholder, value)
   - Content last (textContent, innerText)

5. Visual Feedback
   - Green highlight (accessibility color)
   - Floating indicator badge
   - Toast notifications
   - Pulse animation for attention

6. Clean Disable
   - Remove event listeners
   - Clear highlights
   - Cancel speech
   - Remove indicator
   - Reset state variables

═══════════════════════════════════════════════════════════════════
