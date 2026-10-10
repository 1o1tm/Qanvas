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
| **⇩ Export** | The plugin as a PNG, a Markdown datasheet, the release check, Compare with another version, and Send to Designer |
| **🎨 Theme** | Restyle the whole plugin |
| **✨** | The design check |
| **✓ / ⚠** | Problems in the code |

## 2. A new plugin in two minutes

1. Run **Qanvas: New Q-SYS Plugin…** from the command palette.
2. Pick a starter. **Meeting room** gives you room mode, display power and inputs, a camera PTZ pad, a projector screen, volume, mic mute and lighting scenes, laid out and with working Lua. **Room control**, **Device driver** and **Four-channel mixer** are the other shapes; **Blank** is just the functions Designer expects.
3. Name it (`My Company~Room 1` puts it in a *My Company* folder in Designer) and choose where to save it. The code and the preview open side by side.
4. Press **▶ Test** and press some buttons: the interlocks, the camera arrows and the volume ramp all work, and `print()` lines show in the console.
5. To use it in Designer: **⇩ Export → Send to Designer** puts it in `Documents\QSC\Q-SYS Designer\Plugins`, and it's in Designer's plugin list straight away (no restart), ready to drag into a design.

Made an empty `.qplug` yourself? Open the preview on it and Qanvas offers to start a new plugin in it (also the lightbulb on its first problem): PluginInfo with a new Id, and the functions Designer needs. Every starter, and a plugin built only in the preview, was checked with Designer's own plugin loader (10.0.3 and 10.5): each one loads and its block builds all its controls.

![The Meeting room starter in Designer and in Qanvas](../media/readme/meeting-room.png)

## 3. Moving things around

- **Click** to select, **Shift/Ctrl+click** to add, **drag on empty space** to select an area, **Ctrl+A** for everything, **Tab** for the next item.
- **Drag** to move. It snaps to the grid, to other items' edges and centers (blue lines), and to **equal gaps**: midway between two neighbors, or repeating a gap that's already in the row (pink markers with the size in pixels). **Alt** drags without snapping, **Shift** keeps to one axis, **Ctrl** drops a copy.
- **Handles** resize. **Arrow keys** nudge 1 px, Shift+arrow 10 px.
- **Scale several at once:** with more than one selected, a dashed box with handles goes around them all. Drag a handle to scale the lot from the opposite side: places, sizes, text sizes and corners. **Shift** keeps the proportions, **Alt** scales from the center, **Ctrl** keeps the text sizes. For an exact amount, type it in the inspector's **Scale %**. Text follows the smaller of the two directions, so stretching a row wider doesn't make its text bigger.
- **Things a loop, a helper function or a lookup table places** move and resize the same way. Qanvas changes the number in the code they're made from: a helper's argument, the entry in the table, the gap above, a loop's step (drag the second strip and they all spread) or its start (drag the first and they all move). Everything made from that number moves with it while you drag, and it's outlined (dashed) when you select it; the inspector says which number on which line a drag changes. Where the number is multiplied, the drag snaps to the places it can reach. Something whose position can't be followed back to a number exactly shows a lock.
- With several selected: align edges or centers, space evenly, same width or height, arrange in a grid, and **center in the plugin** (also there for a single item, under its X/Y/W/H).
- **Layers** (left panel) lists everything on the page in stacking order, top first. Click to select, even things hidden under others; double-click to jump to the code; drag a row (or several selected) up or down to change what's on top. Qanvas writes ZOrder, the property Designer stacks by, on as few items as it can. Controls always draw above graphics in Designer, so a control stays above every graphic. **Ctrl+]** and **Ctrl+[** bring the selection forward or send it backward a step; with **Shift**, to the front or the back.

## 4. Changing how things look

Select something and the inspector on the right shows every property it has: colors (with your plugin's colors and an eyedropper), font, size, bold, alignment, stroke, corners, margin and padding, button look, value box, read-only.

- **Several at once:** select several items and the inspector shows the properties they share. Change one and it changes on all of them; "mixed" means they differ.
- **Format painter:** **Ctrl+Alt+C** copies an item's look (colors, font, stroke, corners, text layout) and **Ctrl+Alt+V** gives it to the selection. With several selected, **Match X's look** does it in one click.
- **Themes:** 🎨 restyles the background, text, group boxes, displays and plain buttons in one undoable edit. Hover a theme to preview it. Colors that mean something, like a red mute, stay.
- **A finish** (above the themes) goes with the theme you pick: **Corners** (square, soft, round), **Text sizes** (one size for titles, one for sections such as headers and group box titles, one for the rest), **Font** (one font for all the text), **Group boxes as cards** (filled, no outline, rounded; a box with its own color keeps it) and **Positions on the grid**. **Keep colors** applies the finish without a theme. Everything it changes is a layout property Designer draws, so the plugin looks the same in Designer and costs a Core nothing.
- **Design check:** ✨ finds legends Designer will cut off, labels that don't fit, text that's hard to read, edges 1 or 2 px off from their neighbors, near-miss sizes, buttons too small to touch and items outside the background. Each has a fix, or **Fix all**.
- **Double-click** a button or label to edit its text in place. **F2** renames a control everywhere: GetControls, the layout, the pin name and `Controls.Name` in your runtime code.

## 5. Adding things

The **Add** tab has every Q-SYS control (buttons, LED, text, number, knob, fader, meter, status, combo and list boxes), every graphic (label, group box, header, image) and **presets**: groups that look right together and come with working code where they need it. Type in **Search** to find one. Drag onto the plugin, or click to add.

| Presets | |
|---|---|
| Audio | Volume strip, Volume knob, Volume buttons (hold to ramp), Mic mute |
| Video and displays | Display (power, inputs), Camera PTZ, Source selector, Projector screen |
| Room | Room mode, Lighting scenes, Power on/off, Presets (store/recall), Transport |
| Devices | IP connection (a working TCP client), Device driver (below), Status bar, Dial pad |
| Layout | Title bar, Clock |

**Button icons:** choose Q-SYS's built-in icons (drawn by Designer, nothing embedded), an AV icon from the library (embedded as SVG), or your own PNG/JPG/SVG. Designer names its built-in icons **with spaces**: `"arrow up"`, `"zoom in"`. A hyphenated name like `"arrow-up"` draws nothing in Designer; Qanvas shows it blank too, flags it, and fixes it in one click. In the code, typing `Icon = "` lists them all.

**Copy and paste between plugins:** Ctrl+C in one plugin's preview, Ctrl+V in another's. The clipboard also gets the items as Lua (GetControls entries, layout entries, graphics) for pasting into code by hand.

**Device driver** (Presets → Devices) is a starting point for a driver: a TCP device's controls and the code a driver needs, each part explained in the code. A command queue sends one command at a time, waits for its reply (a Lua pattern), tries again after a timeout and then gives up, and keeps only the latest of a kind waiting (`{ key = "volume" }`); polling asks how the device is while connected; a watchdog reconnects a device that goes quiet; `D.on(pattern, fn)` handles what the device says; `D.settled(control, wait, fn)` sends a fader's value once it stops moving. It comes with device rules that play its example device, so in Test mode, once you type an IP address and turn Connect on, the pretend device answers it: change the commands in "This device" for yours.

## 6. Pages

Multi-page plugins show Designer's page tabs inside the plugin window. Click one to switch; **+** adds a page; double-click the current tab to rename it (or use **Pages** in the plugin panel, which works for a plugin with no pages yet). New controls and graphics go on the page you're looking at.

Adding a page to a plugin without pages sets up the standard structure for you: a list of page names, `GetPages`, and an `if CurrentPage == "..."` in GetControlLayout. What the plugin drew becomes the first page; locals and helper functions at the top stay shared.

## 7. The plugin itself

With nothing selected, the inspector shows the plugin: name, version (with a ↑ to bump it), author, description, block color, pages, **Properties** (change a default, add a number, choice, on/off or text property, remove one), and the background color and exact size.

The **background** is one box Qanvas keeps for you: Q-SYS has no background setting, so it's a GroupBox with a Fill at the plugin's top-left, under everything. Pick the color again and that box changes; **Remove** takes it away. Themes use the same box. Other group boxes come from **Add**, like any graphic.

**Trace an image**, at the bottom of that panel, lays a mockup or a screenshot over the plugin so you can build to it. **Choose image…** picks a PNG, JPEG, GIF, WebP, BMP or SVG. Opacity sets how much of it shows. Scale 100% puts one image pixel on one plugin pixel (right for a screenshot of Designer at 100%), and **Fit** scales it to the plugin's width. X and Y move it. **Hide** puts it away for a moment, and **Remove** stops tracing. Clicks go through the image to the plugin. Only you see it: it isn't written to the code, Save image and the datasheet leave it out, and Qanvas remembers it for each plugin on this PC.

**Pins view** draws the schematic block as it comes out of Designer's library: its always-on pins (UserPin = true and no PinStyle). A pin with a PinStyle is optional, and so is the Disable pin every block offers: Designer drops the block with those unticked (you tick them under Properties → Control Pins), so the block leaves them off too, and says how many. Tick them in the plugin panel's **Control pins** list, or click **show them all**. **+ Control in** and **+ Control out** add a pin-only control (Style "None": a pin with no place on the panel) and show it on the block; **+ Audio in** and **+ Audio out** add an audio pin (GetPins).

The **Not on the panel** strip (in the Panel view) lists controls your GetControls makes but GetControlLayout gives no place on any page: Designer doesn't make them at all, so they have no pin and no `Controls.Name` when the plugin runs. Drag one onto the plugin to place it (a control that's only there for its pin still needs a place: put it on a page people don't use).

## 8. Testing

**▶ Test** runs the runtime part of your plugin (`if Controls then … end`) in the preview. Buttons (toggle, momentary, trigger), faders, knobs, text boxes and combo boxes drive your EventHandlers; timers run; `print()` and errors (with their line) show in the console. Change the code and the test restarts. A control's RampTime glides values your code sets, as on a Core. SSH is a stand-in that leaves a note in the console. **Esc** stops.

**TcpSocket talks to a pretend device.** When your code connects, the console shows it, everything your code writes appears there (`→ PWR?\r`), and a second input lets you play the device: type `PWR=ON` and it arrives as Data with a CR LF (or type exact bytes with `\r`, `\n`, `\xNN`). Binary protocols work byte for byte: a write of `"\x81\x01\x04\x00\x02\xFF"` shows as `→ \x81\x01\x04\x00\x02\xff`, and `\x90\x41\xFF` typed for the device arrives as those three bytes. Your parsing runs as it would against the real thing, whether you use `sock.EventHandler` or the callbacks (`sock.Connected`, `sock.Data`…). A write before the socket says Connected is an error, as on a Core. **Drop link** makes the device hang up: your code gets Closed, then Reconnect and Connected after the socket's ReconnectTimeout, so you can watch your recovery work.

**UdpSocket** talks to the same pretend device: what you Send shows in the console, and what you type arrives as a packet from the address you last sent to.

**Serial ports** too: each serial pin your plugin declares is a port (`SerialPorts[1]`, `[2]`…). Open, Write, Read, ReadLine, Data and EventHandler work, a Write while the port is closed is an error (as on a Core), and what you type arrives as a line.

**WebSocket** talks to a pretend server: Connect, Write, Ping and Pong work, a message you type arrives as typed, and **Drop link** closes it.

**Device rules** (the **Device** tab in the console's bar) make the pretend device answer by itself, so you don't type every reply: **+ Reply** ("when it receives `PWR?` it replies `PWR=ON`"), **+ Every…** ("every 5 s it sends `STATUS OK`", while your code is connected) and **+ HTTP** ("a request to `GET */status` gets `200 {"power":"on"}`"). A `*` matches anything (to the end of the line, for text), and `$1` in the reply puts back what it matched: "when it receives `VOL *` it replies `VOL=$1`" answers `VOL 35` with `VOL=35`. As in the console, plain text is a line and `\r`, `\n`, `\xNN` make exact bytes. The first rule that matches answers, on the socket or port that sent it. The device remembers what it's told: "when it receives `PWR *`" keeps what came after PWR, so a rule "when it receives `PWR?` it replies `PWR=$PWR`" answers with what was last set. Rules are kept for each plugin and apply as you change them.

**● Record** turns a test session into a test you can run again. It starts the test over and keeps everything you do from then on: presses, faders, text, what you type for the device or at the Lua prompt, rules you change, components you change in Values, time passing (⏩ too), and what the real network sent back. **■ Save test** asks for a name and puts it in a `.qtest.json` file next to the plugin, ready to commit with it. It runs in VS Code's **Testing** panel: in a moment, on a clock that only moves when told to, so the same way every time. A test checks what the plugin did: its controls at the end, what it sent and connected to, and any errors. When the plugin starts doing something else, the test fails and says what ("Status String: was "Connected", now "Online""). If that's what you meant, **Qanvas: Accept Recorded Test Results** makes it the expected result. A test recorded on the real network replays what the device sent, with no network needed.

**Real network** (in the console's bar) makes TcpSocket (and `NewTls`), UdpSocket, WebSocket and HttpClient connect for real, from your computer: point the plugin's IP property at the device on your desk and watch your driver talk to it. What goes each way shows in the console, byte for byte; reconnects, timeouts and errors are the real ones. Certificates aren't checked, so a device with a self-signed one connects. Serial ports stay pretend. Turning it on or off restarts the test.

**Components and named controls** are pretend ones: `Component.New("Room Mixer")` gives a component whose controls keep what your code sets on them (`mixer["gain"].Value = -12`), and so do `NamedControl.SetValue` and the rest. **Values** lists them under Components, by component. Click one and type a value to change it the way the rest of the design would: a number sets its Value, anything else its String, and its EventHandler runs.

**Crypto computes for real:** Base64, `MD5Compute`, `Digest` and `HMAC` (MD5, SHA-1, SHA-256, SHA-512), AES-128 and AES-256 (CBC and ECB), PBKDF2 and random bytes, so a device login or a signed request can be tested end to end.

**Values** (in the console's title bar) lists every control's Value, String and Position as your code sees them, updated as they change (a change flashes). **ƒ** marks a control whose EventHandler is set. Hover a row to point the control out on the plugin, click it to see its EventHandler in the code, and type in the filter to find one. Drag the console's top edge to make it taller.

**Test mode does what a Core does**, not what Emulate does, where the two differ:
- A Core gives each call into a plugin 200,000 Lua instructions: startup, each EventHandler, each timer tick, each socket event. A call that goes past it stops with the Core's own message, "Max execution limits exceeded", on its line. If a pcall swallows that error, the work it was doing is left half done, and anything more the call does stops at the limit again, as on a Core; Qanvas points it out.
- Only your plugin's own code is counted, not Qanvas's stand-ins for Q-SYS's libraries (on a Core those are built in).
- A plugin's own `Controls.X:Trigger()` pulses the control but doesn't run its EventHandler (Emulate runs it): call the function directly.
- `os.clock()` is CPU time, not the time of day: use `Timer.Now()`.
- Setting a property a control doesn't have (`Controls.Gain.Vaule = 5`) is an error, and `.Boolean` is true when the position is above 0.5 (a knob at -100 dB is false).
- Files aren't simulated: `io.open`, `os.remove` and `dir` fail as they do for a missing file, so your plugin's own failure path runs.

One thing Test mode can't do as a Core does: its Lua keeps whole numbers in 32 bits, where a Core's go to 64. A result past 2,147,483,647 wraps around in Test mode (`1791234567 * 1000`, a millisecond timestamp), a hex number past `0x7FFFFFFF` is negative (`0xDEADBEEF`), and `string.unpack` reads a 4-byte number past 2,147,483,647 as a negative one. So Test mode says so wherever it happens, with the line and what a Core would give, in the console and the Core tab, and points out such numbers written in the code when the test starts. Bit work like a CRC-32 or an IP address made with `<<` and `|` keeps its bits and comes out the same.

The **Core** button in the console bar shows the busiest call as a share of that budget. It turns orange at half, or when the code holds something a Core does differently, and red when a call went past. Its tab lists each kind of call with its heaviest run, and every finding, with links to the lines. The same findings show as problems in the code before anything runs: `os.clock()`, a Timer kept only in a local (it can be garbage-collected while it runs; QSC advises globals), and a plugin's own `Trigger()` on a control with an EventHandler.

**⏩ 10 s** runs what the next 10 seconds would bring, right now: timers, polling, reconnect waits and timeouts happen in order, and `Timer.Now()` moves along. Shift-click for a minute.

**HttpClient talks to a pretend server.** A Download or Upload shows in the console (`GET http://10.0.0.9/api/status`) and waits: type the reply body and it reaches your EventHandler with status 200, or start with a status (`404 Not found`) to send that instead. `require("rapidjson")` and `require("json")` behave like the Core's, `rapidjson.null` and all.

The console has a **Lua prompt** into the running plugin: type an expression to see its value (`Controls.Mute.Boolean`, `Properties["Mode"].Value`), or a statement to run it (`Controls.Mute.Boolean = true` lights the button, `Controls.Source[2].EventHandler()` presses the second source). Up and Down bring back earlier lines.

## 9. Sharing

**⇩ Export → Image** saves the plugin window as a 2x PNG. **⇩ Export → Datasheet** writes a Markdown file next to the plugin: a picture of every page, then the controls (type, pin and page), the properties and the audio pins. It's ready for a GitHub README or a forum post.

**⇩ Export → Release check** (or *Qanvas: Release Check*) lists what to look at before you share the plugin. Arrow through the list and the code follows; Enter goes there. It includes:
- the errors and warnings from the Problems panel;
- a placeholder Id (`dev-id`) or one that isn't a GUID;
- a missing Version, ShowDebug left on, and leftover `print()` calls;
- IP addresses and passwords written into the runtime code (each install wants its own: a property or a text control);
- in Designer's Plugins folder: another plugin with the same Id, extra copies of this one, and a copy with the same Version but different code (bump Version so designs that use it are offered the update).

**Don't break designs that already use the plugin.** Designer ties a design to a plugin by its Id, and each wire to a control's pin by the control's Name. Changing what the pin shows (PrettyName) is safe.
- Renaming a control that has a pin in the version in Designer's Plugins folder asks first. **Change the pin name** keeps the wires.
- **⇩ Export → Compare with another version** (or *Qanvas: Compare With Another Version*) checks against Designer's copy, the last git commit, or any file. It lists:
  - wires that would break: a renamed or removed control or audio pin, fewer elements in an array, a pin that lost its input or output side, a new Id;
  - controls that behave differently, and properties that are gone or changed;
  - what's new.
- The release check includes what would break since Designer's copy.

**⇩ Export → Send to Designer** (or *Qanvas: Send to Designer* in the Command Palette) puts the plugin in Designer's Plugins folder (`Documents\QSC\Q-SYS Designer\Plugins`), and it's in the Schematic Library under Plugins straight away, with no restart. Sending again updates it there the same way, under the same name: Designer reads a plugin file the moment one appears in that folder, so Qanvas swaps the new version in as a new file, in one step. Blocks already in a design keep the version they were made with; when the design opens, Designer offers the newer one. (If something keeps the old file open, it's written over in place instead, and Qanvas tells you to restart Designer.) A plugin opened from the Plugins folder itself is put back as a new file when you send it, so Designer reloads it too; one linked into the folder is left alone, so restart Designer to load it. A folder set in `qanvas.designerPluginsFolder` is the only one used: if it isn't there, nothing is sent. A plugin with a Lua syntax error isn't sent on save, and **Send to Designer** asks first: Designer would show a Plugin Error for it and drop it from the library until a version without the error arrives. It won't replace a different plugin that happens to have the same file name unless you say so, it leaves a link in that folder alone, and it points out another copy with the same Id, or an encrypted `.qplugx` build next to it, since Designer would see both. Turn on `qanvas.sendToDesignerOnSave` to send on every save, or set `qanvas.designerPluginsFolder` if your Plugins folder is somewhere else.

## 10. In the code

- Completions: `Controls.` lists your controls, `Controls.Name.` what that control has (Boolean, Value, Legend, an EventHandler snippet…), `Properties["` your properties, `Icon = "` the built-in icons.
- Hover a control name for what it is, where it sits and where your code uses it.
- The Q-SYS libraries complete and explain themselves:
  - `TcpSocket.`, `UdpSocket.`, `SerialPorts.`, `WebSocket.`, `HttpClient.`, `Timer.`, `Crypto.`, `Component.`, `NamedControl.`, `Notifications.`, `Log.`, `Design.`, `System.` and `Network.` list what they offer.
  - A variable you made with one (`sock = TcpSocket.New()`, `local port = SerialPorts[1]`, `local rj = require("rapidjson")`) completes too: `sock:` lists its methods, and `sock.` its properties and handlers (EventHandler, Connected, Data…).
  - So does the first parameter of a handler set on it.
  - `TcpSocket.Events.`, `.EOL.`, `Crypto.Hash.` and `Crypto.Cipher.` list their names.
  - `HttpClient.Download`, handlers and `ReadLine` come as fill-in snippets.
  - Hover any of these for a short description.
  - They work even while the plugin has an error, and they list only what Test mode runs.
- Problems with quick fixes: Lua errors on the right line, duplicate names after copy and paste, a missing PluginInfo, GetProperties or GetControls (Designer won't load or place the plugin), a missing GetControlLayout (Designer adds a script's own controls to the block), an empty Name or Id, icon names Designer won't draw, controls Designer leaves out (a ControlType, ButtonType, ControlUnit or IndicatorType spelled any other way, case included, or a Button without a ButtonType), and runtime code that names a control that no longer exists.
- The preview runs your plugin the way Designer does: each design function (GetControls, GetControlLayout, GetPrettyName…) in a Lua of its own where your code has just run, so what one sets in a global isn't there in another, with `props` as Designer hands it (each property's Name, Type, Value, Min and Max, and Designer's own, such as Show Debug), and with `json`, `require`, `bit32` and `math.pow` there as in Designer.
- `{ r, g, b }` colors get swatches and VS Code's color picker.

## 11. AI assistants

An AI assistant can work on your plugins the way you do in the preview: look at the layout (as data, and as a picture the preview draws), change it, check it, and run it in Test mode. It sees what you see, and you see what it does: its changes appear in the code and the preview as it makes them.

Ask for a plugin from scratch ("a projector driver with power, inputs and a status display"), a fix ("the legends on the source buttons are cut off"), or a test ("connect to the pretend device and check the power feedback"). The assistant can look at the picture after each change and try again.

### Setting one up

**GitHub Copilot in VS Code:** nothing to set up. Open the Chat view, choose **Agent**, and ask: the Qanvas tools are in its tools list (the tools button) once a plugin is open. VS Code asks before each change and says what it will do; allow them for the session to let it work on its own.

**Claude Code, Claude Desktop, Cursor and other apps that support MCP servers:** in VS Code, run **Qanvas: Connect an Assistant Outside VS Code** once. It asks whether to let them in, then shows the settings to give the assistant: a server called `qanvas` that runs on VS Code's own program (`command`), with Qanvas's relay as its argument (`args`) and `ELECTRON_RUN_AS_NODE=1` in its environment, so nothing else needs installing. **Copy** puts them on the clipboard. Then:

- **Claude Code:** in a terminal, with the two paths from those settings:
  ```
  claude mcp add --env ELECTRON_RUN_AS_NODE=1 --transport stdio --scope user qanvas -- "<command>" "<the relay>"
  ```
  Or paste the `qanvas` entry under `mcpServers` in a `.mcp.json` in the project's folder.
- **Claude Desktop:** Settings, **Developer**, **Edit Config**: paste the `qanvas` entry under `mcpServers` in that file, save it, and restart Claude Desktop.
- **Cursor:** Cursor Settings, **MCP**, add a new global MCP server: paste the `qanvas` entry under `mcpServers` in the `mcp.json` it opens.
- **Any other app** that runs MCP servers: the same command, argument and environment variable, in its MCP settings.

It works while VS Code is open, with the plugin's folder (or the plugin) open in it. The status bar shows **Assistant** while one is connected; click it to see the settings again or to turn this off (or turn off `qanvas.assistantsOutsideVSCode`). An assistant that works in a terminal can also edit the `.qplug` itself: the preview follows, and the tools see the change.

What an assistant can do:

| | |
|---|---|
| **See** | The plugins open in VS Code; the layout as data: pages, properties, controls, graphics, places and sizes, and what placed each one (plain numbers, a loop, a helper or a lookup table, with the number a move changes); a picture of the plugin or its block, drawn by the preview, live values included in Test mode; the page, property values, look and traced image the preview shows |
| **Check** | The design check (each finding with its fix), the release check, and what designs using another version would notice |
| **Change** | What the preview does, through the same engine: move and resize (things a loop or lookup table places too, checked on every page), set fields, add controls, graphics, presets, pins, pages, properties, EventHandlers and runtime code, rename, delete, themes and finishes, stacking, looks and scaling. A new plugin from a starter. Each change is one undo step, and a dry run says what would change (the lines, and what else moves) without writing anything |
| **Run** | Test mode: press buttons, set faders, knobs and text, play the pretend device (what it sends, HTTP replies, a dropped link, its rules), run Lua, skip ahead in time, and read the console, the Values list and the Core tab. Record a session as a test, run the recorded tests. The real network too, once you allow it |

What keeps it safe:
- **Nothing listens on the network.** In VS Code the tools run inside Qanvas. Outside VS Code, the assistant reaches Qanvas through a private pipe on this computer, which only your account can use, and only with a secret that changes every time VS Code starts. It's off until you turn it on.
- **Changes are normal edits:** you see them in the code, Ctrl+Z undoes each one, and they go through the preview's engine, so an assistant can't do anything a drag couldn't. A change that would leave the code with an error isn't written. Renaming a control that designs wire to asks you first, as it does when you do it. A change is saved when the file had nothing unsaved, so an assistant that reads the file sees it; your own unsaved typing is never saved for you, and an assistant's save never sends to Designer.
- **It asks you first** before sending to Designer (every time), and before Test mode uses the real network (once for each plugin, until VS Code closes): that's when real equipment can do what the plugin tells it.
- **It stays in your project.** An assistant works on plugins open in VS Code or in the folders open there, and reads or writes files only there (or beside the plugin). Pictures go only to the assistant. Qanvas writes no other files unless the assistant asks for a datasheet, a recorded test or a new plugin, and it never writes over a plugin or a datasheet that's there.

## 12. Keyboard

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
