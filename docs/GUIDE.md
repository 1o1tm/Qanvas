# Qanvas guide

A walk through everything Qanvas does, in the order you'll want it. For the short version, see the [README](../README.md).

## 1. Open a plugin

Open any `.qplug` file and click the **layout icon** in the editor's title bar (or press **Ctrl+K Q**). The preview opens beside the code and draws the plugin the way Q-SYS Designer does. Type in the code and the preview follows; drag in the preview and the code follows.

The toolbar, left to right:

| | |
|---|---|
| **Panel / Pins** | The control panel, or the schematic block with its pins |
| **− 100% + Fit** | Zoom |
| **Snap, grid size, Guides** | Snap to the grid; snap to other items' edges, centers and equal gaps |
| **Live / All on / Design** | How the plugin looks once the design runs (values and startup state), with every button and LED on, or blank as Designer shows a block that hasn't run |
| **+ Add** | Shows the left panel: **Add** (controls, graphics, presets) and **Layers** |
| **▶ Test** | Runs your runtime code in the preview |
| **⇩ Export** | The plugin as a PNG, or a Markdown datasheet |
| **🎨 Theme** | Restyle the whole plugin |
| **✨** | The design check |
| **✓ / ⚠** | Problems in the code |

## 2. A new plugin in two minutes

1. Run **Qanvas: New Q-SYS Plugin…** from the command palette.
2. Pick a starter. **Meeting room** gives you room mode, display power and inputs, a camera PTZ pad, a projector screen, volume, mic mute and lighting scenes, laid out and with working Lua. **Room control**, **Device driver** and **Four-channel mixer** are the other shapes; **Blank** is just the functions Designer expects.
3. Name it (`My Company~Room 1` puts it in a *My Company* folder in Designer) and choose where to save it. The code and the preview open side by side.
4. Press **▶ Test** and press some buttons: the interlocks, the camera arrows and the volume ramp all work, and `print()` lines show in the console.
5. To use it in Designer, the `.qplug` has to be in `Documents\QSC\Q-SYS Designer\Plugins` (save it there, or keep it with your code and link it in). Designer picks it up in its plugin list.

![The Meeting room starter in Designer and in Qanvas](../media/readme/meeting-room.png)

## 3. Moving things around

- **Click** to select, **Shift/Ctrl+click** to add, **drag on empty space** to select an area, **Ctrl+A** for everything, **Tab** for the next item.
- **Drag** to move. It snaps to the grid, to other items' edges and centers (blue lines), and to **equal gaps**: midway between two neighbors, or repeating a gap that's already in the row (pink markers with the size in pixels). **Alt** drags without snapping, **Shift** keeps to one axis, **Ctrl** drops a copy.
- **Handles** resize. **Arrow keys** nudge 1 px, Shift+arrow 10 px.
- With several selected: align edges or centers, space evenly, same width or height, arrange in a grid, and **center in the plugin** (also there for a single item, under its X/Y/W/H).
- **Layers** (left panel) lists everything on the page in stacking order. Click to select, even things hidden under others; double-click to jump to the code.

## 4. Changing how things look

Select something and the inspector on the right shows every property it has: colors (with your plugin's colors and an eyedropper), font, size, bold, alignment, stroke, corners, margin and padding, button look, value box, read-only.

- **Several at once:** select several items and the inspector shows the properties they share. Change one and it changes on all of them; "mixed" means they differ.
- **Format painter:** **Ctrl+Alt+C** copies an item's look (colors, font, stroke, corners, text layout) and **Ctrl+Alt+V** gives it to the selection. With several selected, **Match X's look** does it in one click.
- **Themes:** 🎨 restyles the background, text, group boxes, displays and plain buttons in one undoable edit. Hover a theme to preview it. Colors that mean something, like a red mute, stay.
- **Design check:** ✨ finds legends Designer will cut off, labels that don't fit, text that's hard to read, edges 1 or 2 px off from their neighbors, near-miss sizes, buttons too small to touch and items outside the background. Each has a fix, or **Fix all**.
- **Double-click** a button or label to edit its text in place. **F2** renames a control everywhere: GetControls, the layout, the pin name and `Controls.Name` in your runtime code.

## 5. Adding things

The **Add** tab has every Q-SYS control (buttons, LED, text, number, knob, fader, meter, status, combo and list boxes), every graphic (label, group box, header, image) and **presets**: groups that look right together and come with working code where they need it. Type in **Search** to find one. Drag onto the plugin, or click to add.

| Presets | |
|---|---|
| Audio | Volume strip, Volume knob, Volume buttons (hold to ramp), Mic mute |
| Video and displays | Display (power, inputs), Camera PTZ, Source selector, Projector screen |
| Room | Room mode, Lighting scenes, Power on/off, Presets (store/recall), Transport |
| Devices | IP connection (a working TCP client), Status bar, Dial pad |
| Layout | Title bar, Clock |

**Button icons:** choose Q-SYS's built-in icons (drawn by Designer, nothing embedded), an AV icon from the library (embedded as SVG), or your own PNG/JPG/SVG. Designer names its built-in icons **with spaces**: `"arrow up"`, `"zoom in"`. A hyphenated name like `"arrow-up"` draws nothing in Designer; Qanvas shows it blank too, flags it, and fixes it in one click. In the code, typing `Icon = "` lists them all.

**Copy and paste between plugins:** Ctrl+C in one plugin's preview, Ctrl+V in another's. The clipboard also gets the items as Lua (GetControls entries, layout entries, graphics) for pasting into code by hand.

## 6. Pages

Multi-page plugins show Designer's page tabs inside the plugin window. Click one to switch; **+** adds a page; double-click the current tab to rename it (or use **Pages** in the plugin panel, which works for a plugin with no pages yet). New controls and graphics go on the page you're looking at.

Adding a page to a plugin without pages sets up the standard structure for you: a list of page names, `GetPages`, and an `if CurrentPage == "..."` in GetControlLayout. What the plugin drew becomes the first page; locals and helper functions at the top stay shared.

## 7. The plugin itself

With nothing selected, the inspector shows the plugin: name, version (with a ↑ to bump it), author, description, block color, pages, **Properties** (change a default, add a number, choice, on/off or text property, remove one), and the background color and exact size.

**Pins view** draws the schematic block as it comes out of Designer's library: the always-on pins, plus the Disable pin every block has. Tick optional pins in the plugin panel to see them, and add control or audio pins from the block.

## 8. Testing

**▶ Test** runs the runtime part of your plugin (`if Controls then … end`) in the preview. Buttons (toggle, momentary, trigger), faders, knobs, text boxes and combo boxes drive your EventHandlers; timers run; `print()` and errors (with their line) show in the console. Change the code and the test restarts. A control's RampTime glides values your code sets, as on a Core. Serial ports, SSH and named components are stand-ins that leave a note in the console. **Esc** stops.

**TcpSocket talks to a pretend device.** When your code connects, the console shows it, everything your code writes appears there (`→ PWR?\r`), and a second input lets you play the device: type `PWR=ON` and it arrives as Data with a CR LF (or type exact bytes with `\r`, `\n`, `\xNN`). Your parsing runs as it would against the real thing, whether you use `sock.EventHandler` or the callbacks (`sock.Connected`, `sock.Data`…). A write before the socket says Connected is pointed out. **Drop link** makes the device hang up: your code gets Closed, then Reconnect and Connected after the socket's ReconnectTimeout, so you can watch your recovery work.

**UdpSocket** talks to the same pretend device: what you Send shows in the console, and what you type arrives as a packet from the address you last sent to.

**HttpClient talks to a pretend server.** A Download or Upload shows in the console (`GET http://10.0.0.9/api/status`) and waits: type the reply body and it reaches your EventHandler with status 200, or start with a status (`404 Not found`) to send that instead. `require("rapidjson")` and `require("json")` behave like the Core's, `rapidjson.null` and all.

The console has a **Lua prompt** into the running plugin: type an expression to see its value (`Controls.Mute.Boolean`, `Properties["Mode"].Value`), or a statement to run it (`Controls.Mute.Boolean = true` lights the button, `Controls.Source[2].EventHandler()` presses the second source). Up and Down bring back earlier lines.

## 9. Sharing

**⇩ Export → Image** saves the plugin window as a 2x PNG. **⇩ Export → Datasheet** writes a Markdown file next to the plugin: a picture of every page, then the controls (type, pin and page), the properties and the audio pins. It's ready for a GitHub README or a forum post.

## 10. In the code

- Completions: `Controls.` lists your controls, `Controls.Name.` what that control has (Boolean, Value, Legend, an EventHandler snippet…), `Properties["` your properties, `Icon = "` the built-in icons.
- Hover a control name for what it is, where it sits and where your code uses it.
- Problems with quick fixes: Lua errors on the right line, duplicate names after copy and paste, a missing GetProperties (Designer refuses the plugin), an empty Name or Id, icon names Designer won't draw, and runtime code that names a control that no longer exists.
- `{ r, g, b }` colors get swatches and VS Code's color picker.

## 11. Keyboard

| Keys | |
|---|---|
| Click, Shift/Ctrl+click | Select, add to the selection |
| Drag on empty space | Select by area |
| Ctrl+A, Tab | Select everything, next item |
| Drag, Alt+drag, Shift+drag, Ctrl+drag | Move (snapping), without snapping, along one axis, drop a copy |
| Arrow keys, Shift+arrows | Nudge 1 px, 10 px |
| Double-click, F2 | Edit text in place, rename |
| Ctrl+C, Ctrl+V | Copy, paste where the mouse is (any open plugin) |
| Ctrl+Alt+C, Ctrl+Alt+V | Copy a look, give it to the selection |
| Ctrl+D, Delete | Duplicate, delete |
| Ctrl+Z, Ctrl+Y | Undo, redo (in the code too) |
| Enter | Show it in the code |
| Ctrl+wheel, Ctrl+1, Ctrl+0 | Zoom, actual size, fit |
| Space+drag, middle-drag | Pan |
| Esc | Clear the selection, stop a test |
| ? | All of these, in the preview |
