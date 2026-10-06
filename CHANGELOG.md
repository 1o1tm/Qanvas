# Changelog

## 0.6.0 (2026-10-05)

Test mode, checked against QSC's Lua reference
- **Sockets written the callback way work:** `sock.Connected = function…`, `sock.Data = function…` (and Closed, Error, Reconnect, Timeout) are called, not only `EventHandler`. `TcpSocket.NewTls()` works and sockets have `PeerAddress`
- **Drop link:** the pretend device hangs up, and your code gets Closed, then Reconnect and Connected after the socket's ReconnectTimeout, as on a Core
- A **write before the socket is connected** is pointed out in the console (QSC's advice: wait for Connected)
- **UdpSocket** talks to the pretend device too: what you send shows, and what you type arrives as a packet
- **RampTime:** a value set from code glides there, as on a Core
- **JSON like the Core's:** `rapidjson` decodes null as `rapidjson.null`, encodes an empty table as `{}`, and takes `pretty`, `sort_keys` and `empty_table_as_array`; `json` has its own `json.null`
- `Timer:IsRunning()`; `ReadLine` with `TcpSocket.EOL.CrLf` takes a bare LF too

Code Qanvas writes
- A field added to an existing entry goes in order (name, style, text and place first, then the look) instead of at the top
- Themes don't write an outline color on boxes drawn without an outline

## 0.5.2 (2026-10-05)

- **Test mode:** dragging a knob that hasn't been changed yet starts from where it is, as in Designer (it used to jump to the bottom of its range first)

## 0.5.1 (2026-10-05)

Fixes from a sweep that drops every preset, starter and page into every test plugin and runs it, and from names with quotes, backslashes, line breaks and Lua keywords in them:
- **Renaming** a control with quotes in its name now updates the code that uses it (`Controls["Room \"A\""]` used to be left behind), and renaming back to a plain name goes back to `Controls.Name`. A Lua keyword as a name (`end`) is written `Controls["end"]`, and a `layout.Name = {…}` entry can take a name with spaces
- **Code completion** writes names the way Lua needs them: after `Controls.`, "Input Gain" becomes `Controls["Input Gain"]`; inside quotes the whole name replaces what you typed (it used to give `Controls["Input Input Gain`), with quotes in names escaped
- A **new plugin** named with a `]` at the end ("Matrix [8x8]") didn't load: its Description was written inside `[[ ]]`
- An **EventHandler** for a control with a line break in its name broke the code
- No false "unknown control" warning for `Controls["…"]` when the name has escapes in it; carriage returns and other control characters in text are written as escapes
- The **datasheet** shows control names with backslashes or backticks as they are

## 0.5.0 (2026-10-05)

Test mode
- **A pretend device behind TcpSocket:** Connect succeeds, what the plugin writes shows in the console, and what you type as the device arrives as Data (ReadLine, Read, Search and BufferLength work), so a driver's parsing can be tested without hardware
- **A pretend web server behind HttpClient:** Download and Upload requests show in the console and wait for the reply you type (status optional: `404 Not found`), which reaches the EventHandler; CreateUrl, EncodeParams, EncodeString and DecodeString work
- **Lua prompt** in the test console: an expression shows its value (`Controls.Mute.Boolean`), a statement runs in the plugin (`Controls.Mute.Boolean = true` lights the button); Up/Down for earlier lines

Layout and styling
- **Bulk editing:** with several items selected, every layout field they share is editable at once ("mixed" where they differ)
- **Format painter:** Ctrl+Alt+C copies an item's look (colors, font, stroke, corners, text layout), Ctrl+Alt+V gives it to the selection; "Match X's look" for a selection
- **Equal-spacing snaps** while dragging, like Figma: midway between neighbors or repeating an existing gap, with the gap shown in pixels
- **Center in the plugin** for one item or a selection
- **Layers** tab in the left panel: everything on the page in stacking order; click to select (even hidden things), double-click for the code
- **Theme preview:** hover a theme to see the plugin in it before applying

Pages
- **Add and rename pages** from the + after the page tabs, a double-click on a tab, or the plugin panel. A plugin without pages gets the standard setup (a list of page names, GetPages, and an if/elseif in GetControlLayout); what it drew becomes the first page. Checked in Designer 10.5
- New controls and graphics go on the page you're looking at (before, a control went on every page and a graphic into the last page's table); controls laid out on another page are no longer listed as not placed

Presets and starters
- **New presets:** Camera PTZ, Display, Projector screen, Lighting scenes, Transport, Volume buttons (hold to ramp), Dial pad, Clock; **IP connection** is now a working TCP client. All checked in Designer 10.5
- Presets are grouped (Audio, Video and displays, Room, Devices, Layout) and the Add tab has a **search** box
- Presets share one rhythm (16 px titles, buttons from y 20, 32 px tall) so they line up with each other
- **Meeting room** starter; every starter passes the design check with nothing to fix

Designer accuracy
- **Built-in icon names:** Designer names its icons with spaces ("arrow up", "zoom in"); hyphenated names draw nothing there. The picker writes Designer's spelling, the preview draws exactly what Designer draws, a lint with a quick fix catches the rest, and `Icon = "` completes the names. Measured with 72 names in Designer 10.5
- **Pins view** starts like a block fresh from the library: always-on pins plus the Disable pin every block has; tick the optional ones

Sharing
- **Export** menu: the plugin as a 2x PNG, or a **datasheet** (Markdown with a picture of every page and tables of the controls, properties and audio pins)
- **Copy and paste between plugins:** Ctrl+C in one preview, Ctrl+V in another; the clipboard also gets the items as Lua to paste into code

Plugin panel and code
- **Description** field and a **version bump** button (1.2.9 → 1.2.10)
- New lints: runtime code naming a control that no longer exists; removing a property your code reads is refused with the line that reads it
- **Keyboard shortcuts** list: press ?
- [docs/GUIDE.md](docs/GUIDE.md): a walk through everything

Reliability
- A fuzz test throws seeded random edits (and batches of them, presets, themes, the format painter) at nine real plugins and checks the code always still runs. It found, and this release fixes, a batch editing the same property twice producing broken Lua
- A UI fuzz drives the real preview in a browser (clicks, drags, keys, inspector edits): 1,200 steps, no script errors, no broken code

## 0.4.0 (2026-10-05)

- **Test mode:** run the plugin's runtime code in the preview. Buttons (toggle, momentary, trigger), faders, knobs, text boxes and combo boxes drive your EventHandlers; Timer.CallAfter / Timer.New, Properties, JSON, and stand-ins for sockets, HTTP and named components; a console with print output and errors by line; restarts when the code changes; Esc stops
- **Live shows startup values:** what the runtime code sets when the design starts (status texts, colors, log lines) is drawn, as Designer Emulate does
- **Page tabs** inside the plugin window for multi-page plugins, like Designer
- **Properties editor** (GetProperties) and **block color** (GetColor) in the plugin panel
- **Starters** for New Q-SYS Plugin: Room control, Device driver, Four-channel mixer
- **Grid arrange** for a selection, and **Ctrl-drag** to drop copies
- **Completions and hovers** in the code: `Controls.` names and members, `Properties["`, hover a control for what and where it is
- **Save image:** the plugin window as a 2x PNG
- Checked against the author's real plugins in Designer 10.5 Emulate: text boxes pick black/white text for contrast, knobs with no DefaultValue start at Min, labels ignore Padding and wrap like Designer, default graphic text is 11 px, titled group boxes clamp big corner radii

## 0.3.0 (2026-10-05)

- **Live / All on / Design** views: controls show their DefaultValue the way Designer draws a running plugin, measured in Emulate: value boxes fill to the value in their unit's colors, Designer's number formats, knob domes with pie fill and pointer, faders at their value, meters on Designer's meter scale, lit LEDs, green OK status
- Gloss buttons, LEDs, faders, knobs and meters re-measured; text sits on the same pixel rows as Designer; default label/group box/header text is 11 px
- **Presets** in the Add palette (volume strip, source selector, room mode, power, status bar, IP connection, presets, mic mute, volume knob, title bar), with working Lua where needed
- **Themes:** Dark, Light, Midnight, Classic, with accent colors
- **Design check** with one-click fixes
- **Q-SYS built-in icons** in the icon picker and preview (Foundation Icon Fonts 3, the set Designer uses)
- Lints and quick fixes: missing GetProperties, missing PluginInfo, empty Name/Id, unknown built-in icon names
- Fixed: several graphics added at once to an empty graphics table, two handlers added to a plugin with no runtime section, new code splitting an earlier block from its comment

## 0.2.0 (2026-10-04)

- **Add panel:** drag any Q-SYS control (buttons, LED, text, number, knob, fader, meter, status, combo, list) or graphic (label, group box, header, image) onto the plugin; Qanvas writes both the GetControls entry and the layout entry
- **Images:** pick a PNG, JPG or SVG; it is embedded as base64 the way Q-SYS expects
- **Rename** a control (updates GetControls, the layout key, the pin name and `Controls.Name` in your runtime), **Duplicate** (Ctrl+D) and **Delete**
- Control settings in the inspector: button type, indicator type, unit, min/max, pin direction, show pin
- **Plugin panel** (nothing selected): name, version, author, background color and exact size (a full-size GroupBox drawn first), fit to content
- Double-click the title to rename the plugin
- **Pins view:** the schematic block with control and audio pins; add control or audio pins
- **Qanvas: New Q-SYS Plugin…** starts a clean plugin and opens the preview
- LEDs now stretch into ellipses like Designer
- New code matches the file's indentation (tabs, 2 or 4 spaces)

## 0.1.0 (2026-10-04)

First version.

- Live preview of `.qplug` layouts, drawn like Q-SYS Designer
- Drag, resize, nudge, smart guides, grid snapping, align and distribute, all written back into the code
- Inspector with color picker, fonts, alignment and scrubbable numbers
- Tray for placing controls that have no layout entry
- Add Label, Box and Header graphics
- Two-way selection sync with the editor, Problems tab, inline color swatches
- Pages and Properties preview
