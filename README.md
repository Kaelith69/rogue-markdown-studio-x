# Rogue Markdown Studio X

![Rogue Markdown Studio X Banner](banner.svg)

> A zero-build, single-file, offline-first Markdown Editor and Live Preview Studio. Because you shouldn't need a 300MB node_modules folder just to write some text, render a flowchart, and format some equations.

### [⚡ Launch Live Demo](https://kaelith69.github.io/rogue-markdown-studio-x/preview.html)

---

## What This Actually Does

**Rogue Markdown Studio X (RMS X)** is an in-browser Markdown editing workspace contained entirely within a single static HTML file (`preview.html`). It provides a split-screen dashboard to write Github Flavored Markdown and see it compiled in real-time, complete with syntax highlighting, LaTeX mathematical typesetting, and Mermaid diagram visualization.

If you have an internet connection, it dynamically hooks into lightweight CDNs to load advanced parsers (`marked.js`), sanitizers (`DOMPurify`), math systems (`KaTeX`), and flowchart engines (`Mermaid`). If you lose connection or host it locally offline, it gracefully falls back to a built-in custom line-by-line block parser so you never lose the ability to write and view your work.

It stores your draft, layout mode, typography settings, and theme choices directly in your browser's local storage. No server, no databases, no tracking.

---

## Actual Features

These are the features currently implemented in the codebase:

*   **Dual-Engine Pipeline:** 
    *   *Online Mode (Enhanced):* Compiles markdown using `marked.js` with full GFM compliance, sanitized via `DOMPurify` to block XSS injections.
    *   *Offline Mode (Standard):* Employs a custom block-level parser built in pure Javascript to translate headers, code blocks, lists, quotes, horizontal lines, images, and links if CDNs are unreachable.
*   **Interactive Task Lists:** Clicking checkbox items (`- [ ]` / `- [x]`) in the visual preview window automatically updates the corresponding markdown source line inside the text editor and syncs the changes to storage.
*   **Prevent-Lock Scroll Sync:** Editor and preview panels scroll together bi-directionally based on scroll-height percentages, equipped with scroll-source tracking to prevent recursive synchronization loop locks.
*   **Curated Aesthetics (5 Themes):**
    *   *Midnight Slate (Default):* Deep navy tones with glowing indigo highlights.
    *   *Rogue Brutalist:* High-contrast pure black and white styling.
    *   *Cyberpunk Neon:* A neon-drenched dark purple workspace with hot pink boundaries and cyan glow.
    *   *Earthy Forest:* A dark green, organic palette.
    *   *Solarized Light:* A clean, high-readability light theme.
*   **Typography Controls:** Instantly swap the workspace between Geometric Sans (*Plus Jakarta Sans*), Modern Tech (*Space Grotesk*), and Code-optimized (*JetBrains Mono*).
*   **Sidebar Quick-Insert Tools:** One-click buttons to inject headings, bold/italic markup, lists, code fences, blockquotes, links, images, tables, math blocks, and Mermaid graph templates.
*   **Auto-Saving State:** Auto-saves your content, theme selection, font layout, and split-screen mode to `localStorage` on every keystroke (debounced at 200ms).
*   **Document Statistics:** Dynamic footer calculating raw word count, character count, and estimated reading time.
*   **Print and PDF Engine:** Custom CSS override media queries optimized for browser print pipelines (`Ctrl+P` / Save as PDF) which cleanly hides the dashboard shell and formats the document into a professional, multi-page layout.
*   **Drag-and-Drop Loader:** Drag any `.md` or `.txt` file from your desktop directly onto the browser window to instantly load it into the editor.

---

## System Architecture

The application is architected as a clean vertical pipeline contained inside a single document. It avoids external bundlers, pre-compilers, or server runtimes.

![Architecture Diagram](architecture-diagram.svg)

### Technical Components:
1.  **UI Component Layer:** Header controls, tool sidebars, and split panels styled using responsive CSS grid layouts and custom CSS variables.
2.  **Event Pipeline:** Synchronizes interactions (input changes, panel scroll events, file drops) and handles the 200ms debounce throttle window.
3.  **Engine Selector:** Inspects external script loading states and routes raw text to either CDN compilers (`marked`, `DOMPurify`, `KaTeX`, `Mermaid`) or the local regex fallback compiler.
4.  **Local Storage Interface:** Maintains state values persistently across page reloads.

---

## Rendering Data Flow

Here is the exact lifecycle mapping of what happens when you type or load text:

![Data Flow Diagram](data-flow-diagram.svg)

1.  **Tokenization:** The script extracts math blocks (`$$...$$` and `$...$`) to protect special mathematical characters from being destroyed or altered during HTML parsing.
2.  **Compilation:** The text is parsed to HTML using either the CDN engine or the regex line compiler.
3.  **Sanitization:** The HTML output is sanitized via DOMPurify to keep the application safe from XSS.
4.  **Restoration:** Math placeholders are swapped back and typeset into beautiful equations via KaTeX.
5.  **Diagram Generation:** Any Mermaid flowchart syntax is parsed asynchronously using Mermaid's rendering API and injected as SVG nodes.
6.  **Persistence & UI update:** The DOM is updated, storage is written to, and reading statistics are computed.

---

## Folder Structure

Behold the absolute complexity of the workspace folder layout:

```text
.
├── preview.html               # The entire application (HTML, CSS styling, Javascript logic)
├── README.md                  # This file
├── LICENSE                    # Permissive project license
├── banner.svg                 # Project banner asset
├── architecture-diagram.svg   # System architecture diagram
└── data-flow-diagram.svg      # Ingestion pipeline flow diagram
```

Yes, that is really it. No `node_modules`, no `src/`, no config files.

---

## Installation & Local Development

No installer required. Simply clone the repository or download `preview.html` and open it:

```bash
# Clone the repository
git clone https://github.com/Kaelith69/rogue-markdown-studio-x.git

# Move into the folder
cd rogue-markdown-studio-x

# Open the editor directly in your default browser (macOS/Linux)
open preview.html

# On Windows (PowerShell)
Start-Process preview.html
```

---

## Developer Operations (Scripts)

Since there is no bundler or local server framework, there are no scripts to run. 

*   To run the app: Double-click `preview.html`.
*   To edit the source code: Open `preview.html` in your favorite IDE (like VS Code), make changes, and refresh the browser.
*   To test offline capabilities: Disable your internet connection or run the browser in Offline Mode via Chrome DevTools and reload the page.

---

## Configuration

The application is completely zero-configuration. Custom values are auto-saved to these local storage keys:
*   `rmsx`: Stores the active markdown text buffer.
*   `rmsx-theme`: Remembers the active UI theme (`default`, `brutalist`, `cyberpunk`, `forest`, `light`).
*   `rmsx-font`: Stores the typographic layout selection (`sans`, `serif`, `mono`).
*   `rmsx-layout`: Remembers the editor pane visualization (`split`, `editor`, `preview`).

---

## GitHub Pages Deployment

Rogue Markdown Studio X can be hosted on GitHub Pages in under a minute:

1.  Push the repository to GitHub.
2.  Navigate to your repository **Settings** tab.
3.  Scroll down to **Pages** in the left sidebar.
4.  Under **Build and deployment**, select **Deploy from a branch** as the source, choose your primary branch (e.g., `main`), select the root directory `/`, and click **Save**.
5.  Your page will be live at `https://<your-username>.github.io/<your-repository-name>/preview.html`.

*Tip: If you want the editor to load directly at the root URL (without needing `/preview.html` in the path), simply rename or copy `preview.html` to `index.html` and push it to your repository.*

---

## Technical Notes

### Marked Overrides (Token API Handling)
For Markdown parsing online, the application hooks custom overrides into `marked.js` using `marked.use()`. The custom parser is written to dynamically handle both older marked positional parameters and the newer `marked` v10+ token object representation:
*   **Codeblock Rendering (`renderer.code`):** Intercepts `.mermaid` code blocks and wraps them inside `<div class="mermaid-container">` blocks to defer execution to Mermaid's async renderer. All other languages are passed to `highlight.js`.
*   **Checklist Item Mapping (`renderer.listitem`):** Intercepts standard GFM list tokens, strips default checkboxes, assigns a sequential `data-idx` identifier, and injects interactive checkboxes that support line-index mapping back to the editor text.

### Synchronized Scrolling Algorithm
Scroll synchronization tracks the ratio of active scroll height to avoid compounding loops:
```javascript
const pct = editor.scrollTop / (editor.scrollHeight - editor.clientHeight);
preview.scrollTop = pct * (preview.scrollHeight - preview.clientHeight);
```
To prevent infinite scroll event feedback (where scroll on the editor triggers scroll on preview, which then triggers editor scroll), the handler locks execution to a single active source pointer (`scrollSource`), releasing the lock via a debounced timeout once scroll momentum finishes.

---

## Troubleshooting

### Equations/Diagrams not rendering, or says "Checking Engine..."
*   **Cause:** The application relies on CDN endpoints (`cdn.jsdelivr.net` and `cdnjs.cloudflare.com`) to load KaTeX, Mermaid, Highlight.js, and Marked. If you have no internet access, or if these CDNs are blocked by your network/firewall, the browser cannot run the enhanced engine.
*   **Fix:** The application will automatically degrade to **Offline (Standard)** mode. Basic markdown formatting will still render via the custom javascript parser, but math blocks and mermaid flowcharts will render as code blocks. To fix, restore your network connection or verify that CDN scripts can be downloaded.

### Exporting PDF results in a cut-off single page
*   **Cause:** The print layout requires the view panels to lose their absolute height bounds to let text flow naturally. If a script error or styling override forces `overflow: hidden` during printing, the browser will only print the visible fold.
*   **Fix:** Ensure your browser is updated. The print media query overrides in RMS X automatically strip out scroll wrappers, layout panels, sidebars, and headers during a print command. Make sure to use the "Export PDF" sidebar button or run standard browser print (`Ctrl+P`).

---

## FAQ

#### Can I use this app completely offline?
Yes! You can download `preview.html` to a local folder or flash drive. If you open it without internet access, the application detects CDN loading failures and starts the **Offline (Standard)** parsing engine. Your notes are kept safely inside local storage.

#### Is my data sent to any third-party servers?
Never. Rogue Markdown Studio X runs entirely inside your client browser sandbox. There is no back-end API or telemetric tracker. Even CDNs are requested as read-only static script assets.

#### How does the checklist interaction synchronize back to the raw markdown?
When you click a checkbox in the preview area, an event listener catches the click, resolves the sequential checkbox index from its `data-idx` property, search-replaces the corresponding markdown checkbox indicator (`[ ]` or `[x]`) in the editor textarea, and triggers a document redraw.

---

## License

This project is licensed under the [MIT License](LICENSE).

---

## Credits

*   Markdown Parser: [marked](https://github.com/markedjs/marked)
*   Typesetting: [KaTeX](https://github.com/KaTeX/KaTeX)
*   Diagrams: [Mermaid.js](https://github.com/mermaid-js/mermaid)
*   Code Highlighting: [highlight.js](https://github.com/highlightjs/highlight.js)
*   HTML Sanitizer: [DOMPurify](https://github.com/cure53/DOMPurify)

---

*Made with minimal dependencies and zero node_modules. Write freely.*
