# Qanvas

**Live layout preview for Q-SYS plugins.** Your `.qplug` code is on one side and the plugin, drawn the way Q-SYS Designer draws it, is on the other. Edit the numbers and the preview moves. Drag in the preview and the numbers change.

No more restarting Designer to see whether a button moved 5 pixels.

![A display control plugin built from presets, themed, edited in the code, then run in Test mode against a pretend display](media/readme/qanvas-demo.gif)

**Drawn the way Designer draws it.** The same plugin in Designer 10.5 Emulate and in Qanvas:

![The same plugin in Q-SYS Designer and in Qanvas](media/readme/designer-vs-qanvas.png)

**Test mode** runs your runtime code in the preview: buttons, faders and timers drive your EventHandlers, the console shows `print()`, errors and a Lua prompt, and your sockets talk to a pretend device you play from the console.

**Starters and presets** lay out a finished plugin in one click. This is the Meeting room starter, built entirely from presets, running in Designer and drawn by Qanvas:

![The Meeting room starter in Designer and in Qanvas](media/readme/meeting-room.png)

## Install

In VS Code, open the Extensions view (**Ctrl+Shift+X**), search for **Qanvas** and click **Install**. Or download the `.vsix` from the [releases](https://github.com/1o1tm/Qanvas/releases) and run **Extensions: Install from VSIX…**.

## How to use it

1. Open a `.qplug` file.
2. Click the **layout icon** in the editor's title bar, or press **Ctrl+K Q**.
3. Write code, or drag things around. Both stay in sync.

The [guide](docs/GUIDE.md) walks through everything, step by step.

## What it does

- **Runs your real plugin code.** `GetProperties`, `GetControls`, `GetControlLayout`, `GetPins` and `GetPrettyName` run in an embedded Lua 5.3, the same version Q-SYS uses, so layouts built with loops, variables, pages and Properties all work.
- **Test mode: run it right here.** Press **▶ Test** and your runtime code runs in the preview: press buttons, drag faders and knobs, type into text boxes, pick from combo boxes, and watch your EventHandlers, interlocks and timers react. `print()` output and errors (with their line) show in a console with a Lua prompt into the running plugin, editing the code restarts the test, TcpSocket and UdpSocket talk to a pretend device and HttpClient to a pretend server, all played from the console (type what they send back, see what your code sends, drop the link to test your reconnect), and the rest are harmless stand-ins that leave a note. RampTime glides, and `rapidjson` and `json` behave like the Core's.
- **Looks like Designer, measured to the pixel.** Every control style was compared with Designer 10.5 screenshots: gloss and flat buttons, LEDs, value boxes, faders, knobs, meters, status, labels, group boxes, headers and Q-SYS's built-in icons. Switch the toolbar between **Live** (the way the plugin looks once the design runs: DefaultValues, plus whatever your runtime code sets at startup, in Designer's number formats like `-6.00dB`, `1.23kHz`, `L50`), **All on** and **Design** (blank, the way Designer shows a block that hasn't run). Multi-page plugins show Designer's page tabs; click one to switch pages. Checked against real plugins in Designer 10.5 Emulate.
- **Two-way editing:**
  - Drag to move (Ctrl-drag drops a copy), use the handles to resize, arrow keys nudge by 1 px (Shift: 10 px).
  - Double-click to edit text in place; F2 or right-click to rename.
  - Edits change only the numbers and fields involved. Formatting, comments and line endings are untouched, and Ctrl+Z undoes it.
- **Add anything from the palette:** every Q-SYS control and graphic, images (embedded the way Q-SYS expects), and **presets**: ready-made groups that look good together, like a volume strip, volume buttons that ramp while held, a source selector, room mode, power on/off, a display (power and inputs), a camera PTZ pad, a projector screen, lighting scenes, a transport, a dial pad, a status bar, an IP connection (a working TCP client), preset store/recall, a mic mute, a clock and a title bar. Icons are Designer's own built-ins. Presets get unique names (Ch1Gain, Ch2Gain), pins grouped in folders, and working Lua where they need it (interlocked selectors, store/recall).
- **Themes:** Dark, Light, Midnight or back to Classic, with an accent color, in one undoable edit. Colors that mean something (a red mute) are kept.
- **Design check:** finds legends Designer will cut off, labels that don't fit, text that is hard to read on its background, edges 1 or 2 px off from their neighbours, sizes that almost match, buttons too small for a touch panel, and items sticking out of the background, each with a one-click fix (or Fix all).
- **Button icons:** Q-SYS's built-in icons (nothing embedded, tinted by IconColor), a searchable library of AV icons (embedded as SVG), or your own PNG/JPG/SVG.
- **Smart guides, snapping, align and distribute**, and an inspector with a color picker (your plugin's colors, recent colors, eyedropper), fonts, alignment and scrubbable numbers. Dragging snaps to edges and centers, and to **equal gaps**: midway between two neighbors, or repeating a gap already in the row, with the gap shown in pixels.
- **Layers:** the left panel's Layers tab lists everything on the page, top of the stack first: click to select (even things hidden under others), Shift/Ctrl-click to add, double-click to jump to the code.
- **Edit many at once:** select several items and change their color, font, stroke, corners or alignment together ("mixed" shows where they differ). **Format painter:** Ctrl+Alt+C copies one item's look, Ctrl+Alt+V gives it to the selection, or click "Match X's look".
- **Copy and paste between plugins:** Ctrl+C in one plugin's preview, Ctrl+V in another's. The clipboard also gets the items as Lua, ready to paste into code.
- **Completions and hovers in the code:** type `Controls.` for your control names, then `.` for what that control has (`Boolean`, `Value`, `Legend`, `Choices`, an `EventHandler` snippet...), `Properties["` for your properties; hover a control name to see its type, pins and where it sits on the plugin.
- **Linked to your code:** click a control to jump to its code, put your cursor in a layout entry to select it, add an EventHandler stub or insert snippets for a control, and see where your runtime code reads or writes it.
- **Pins view:** the schematic block as it comes out of Designer's library (always-on pins, plus the Disable pin every block has); tick optional pins to see them, and add pins from there.
- **Properties and block color:** with nothing selected, edit GetProperties as a form (change a default, add a number, choice, on/off or text property, remove one) and pick the block's color (GetColor, including QSC's `ConvertColor("#...")` template).
- **Grid arrange:** select several items and lay them out in N columns with a gap, in one step.
- **Problems and quick fixes:** Lua errors with the right line, controls never placed, duplicate names after copy and paste (one-click "make names unique"), a missing GetProperties (Designer refuses the plugin), an empty Name or Id (generate a GUID), and built-in icon names Designer doesn't have.
- **New Q-SYS Plugin…** starts from a clean plugin with all the functions Designer expects, or from a starter: Meeting room, Device driver or a Four-channel mixer, already laid out and working.
- **Colors in the editor:** `{ r, g, b }` colors and `"#rrggbb"` strings get inline swatches and VS Code's color picker.
- **Pages:** click a page tab to switch, **+** to add a page, double-click a tab to rename it (or use the Pages section of the plugin panel). New controls and graphics go on the page you're looking at. A plugin without pages gets the standard setup (a list of page names, GetPages, and an if/elseif in GetControlLayout), checked in Designer 10.5.
- **Datasheet:** one click (📄 in the toolbar, or *Qanvas: Export Plugin Datasheet*) writes a Markdown datasheet next to the plugin: a picture of every page, then its controls (type, pin, page), properties and audio pins. Ready for a GitHub README or a forum post.
- **Preview property values** without changing your code.
- **Keyboard shortcuts:** press **?** for the list.

## What it can't do (yet)

- If a position is **computed** (`Position = { x + i * 40, 10 }` or made in a loop), Qanvas shows it but can't drag it, because there's no single number to change. It shows a lock and points you to the line.
- Test mode simulates the Q-SYS runtime APIs a panel needs (controls, timers, properties, JSON, and TcpSocket, UdpSocket and HttpClient against a pretend device and server you play); SSH, serial ports and named components don't talk to anything.
- Designer draws text with ClearType; the preview uses the same font and positions but grayscale smoothing, so edges can differ by a pixel.

## Settings

| Setting | Default | |
|---|---|---|
| `qanvas.gridSize` | 5 | Grid size for snapping |
| `qanvas.snapToGrid` | on | Snap to the grid while dragging |
| `qanvas.smartGuides` | on | Snap to other controls' edges and centers |
| `qanvas.colorDecorators` | on | Color swatches in `.qplug` files |

## Feedback

Found a bug, or missing something? [Open an issue](https://github.com/1o1tm/Qanvas/issues).

---

© 2026 Timothy McKinney. Free to use; see LICENSE. Q-SYS is a trademark of QSC, LLC. Qanvas is not affiliated with or endorsed by QSC.
