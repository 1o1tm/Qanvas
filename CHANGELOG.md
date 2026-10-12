# Changelog

## 1.0.0 (2026-10-12)
1.0: a release of fixes and speed. Renames and deletes reach every page, plugins split into files undo and edit safely, Test mode is closer to a Core, and big plugins are quicker to type in and drag.

Renames, deletes and names
- **Renaming a control finds every place the layout names it:** a layout written as one table (`{ Go = {...} }`), the same control on every page, and later lines such as `layout["Go"].Legend = "Go!"`. Every page is checked afterwards, and a rename that would leave a page with something it can't place is refused (one of those was left with the old name: Designer dropped the control and its wires, or the plugin stopped running)
- **Deleting a control takes it off every page** (another page kept its place)
- **"Make names unique" only flags real duplicates:** two places on the same page. A control placed on two pages, or a helper table such as `place.Mute = { Position = ... }`, isn't a duplicate (it was flagged, and the fix moved a page's control or left a pinned one with no place)
- **Renaming a page whose branches test it in a way Qanvas can't follow is refused** (the page was left empty)
- **A property the code still reads can't be deleted** (`props.Channels.Value` and `Properties.Model.Value` count too; it was allowed, and GetControls then stopped with an error), and an assistant can't rename a property's Name while code reads it
- **The inspector and the datasheet show a property's default from the code**, not the value you're previewing it with (a switch showed on when the code said off, and clicking it couldn't set it)
- **Compare and the Pins view leave out controls with no place on any page:** Designer doesn't make them, so they have no pin. A pinned control that loses its last place is a break (it said nothing), and deleting one that never had a place isn't (it said "breaks"). One placed only with some property values still counts in Compare: blocks with those values have it
- The release check looks at every page, not only the first

Plugins split into files
- **Ctrl+Z in the preview undoes the preview's last edit in every file it changed**, and Ctrl+Shift+Z does it again (it ran VS Code's undo in the file last shown, which could undo your own typing there)
- **Edits are made one at a time**, each worked out on the files as the last one left them (two quick edits into a file that wasn't open could land in the wrong place)
- **A lone CR counts as a line break everywhere**, as in VS Code, Lua and QSC's compiler (a file with stray CRs got edits on the wrong line)
- **An edit across the #include of an empty file is refused** (the #include line went), and **text added after a file with no line break at its end starts on a line of its own** (it was joined to the last line)
- **A new handler goes into runtime.lua while it's still empty** (it went into plugin.lua)
- **Renaming or deleting plugin.lua:** its other files find the plugin again (every command failed until VS Code was reloaded). With no plugin.lua, the plugin is found from a file two #includes down (a file in the middle was taken for it), and a root not called plugin.lua is named for its own file (two in one folder shared a name)
- **Compare with the last commit** finds files whose #include spells them in another case, and a plugin.lua that starts with a byte-order mark
- **Send on save waits** while another file of the plugin has unsaved changes (it saved them all to send). A file deleted from the disk while it's open counts as missing until it's back, as it does for the compiler
- An #encode image can be deleted with its graphic, `sub/../a.lua` is the same file as `a.lua`, and color swatches show in the plugin's .lua files

Test mode
- **A TcpSocket that disconnects while it's still connecting doesn't connect** (it connected anyway, on the real network too)
- **A meter's Position is where its Value sits between its Min and Max**, and setting its Position sets its Value (Position stayed 0, and setting it put the Value at 1). A recorded test that reads a meter's Position sees the new numbers: **Qanvas: Accept Recorded Test Results** takes them
- **A toggle that starts on goes off on its first press** (it sent on again)
- **`os.time()` and `os.date()` move with Test mode's clock**, ⏩ too, and `os.date("!%H:%M")` gives UTC. **`math.random`** is each session's own and gives the same numbers every start until `math.randomseed`, as Lua does on a Core. So a recorded test that shows the time or a random number replays the same every time (it failed a second later)
- **JSON:** a whole number past 32 bits encodes (`rapidjson.encode` of a time in milliseconds gave an error), and a character written as two `\u` escapes (an emoji) decodes to one
- A runtime section guarded with `if Controls ~= nil then` gets new handlers and presets too (a second section was added)

Also fixed
- **The Live look updates when you preview other property values** (it kept the startup of the values before)
- **The design check counts what loops and helpers place** once Qanvas has worked out their numbers (the ✨ count left them out until the next change)
- **Design check: Fix all goes through.** A fix the code can't take (the text color of a label a loop makes) isn't offered, so it doesn't stop the others
- **The inspector's Pin shows the way an always-on pin goes** (UserPin with no PinStyle showed "None"), and clicking that way keeps it always on
- A resize of something the code works out below zero size is refused with why (the code then made it another size than the preview showed)
- An assistant's new control with several elements needs a Count; one element is its place
- An edit that was refused or called off no longer selects something later

Faster
- **Typing in a big plugin:** completions and hovers answer at once (each worked the whole plugin out again, holding up VS Code), and the Live look reruns the plugin's startup after a pause in typing, not on every key
- **Test mode on a big plugin:** the preview draws once a frame, and the Values list's flash no longer lays the page out for every value (a fast timer froze the preview)
- **Dragging many things at once**, and drops on plugins with many pages (about twice as fast)
- **Working out a big plugin:** the code Qanvas runs is put together in one pass (a 135 KB plugin with 600 items takes about 70 ms a change, from 150; tracing a 187 KB plugin's layout about 75, from 180)
- **Send to Designer with a big Plugins folder** reads only the plugins that changed since the last send
- Plugins split into files: only their own files' changes are followed, and opening other .lua files doesn't look for a plugin each time

Safer
- **Compare with the last commit runs git from a folder of its own**, so a program called git in your project's folder can't run; Designer's folder is looked up with Windows' own reg.exe
- **Files that #include each other over and over are cut off** at half a million lines, with a problem saying so (VS Code froze)
- **An assistant's Lua at the Test console asks first** while the real network is on, and assistants are told the Test mode tools can reach outside

## 0.9.0 (2026-10-11)
Plugins split into files the way QSC's plugin compiler builds them: preview, edit, test and send them as you do a .qplug.

Plugins split into files
- **Open any file of the plugin.** A plugin.lua whose `--[[ #include "file.lua" ]]` lines pull in the others (and `--[[ #encode "logo.png" ]]` for an image) previews from any of its files, joined exactly as QSC's compiler joins it: byte for byte what its build makes of real plugins. Typing in any of the files, or a file changed on the disk, redraws it
- **Edits go into the file they belong in.** A drag changes the number in the file it's in; a new control goes into controls.lua as one more table.insert, and its place into layout.lua; a handler into runtime.lua. Each keeps the file's line ends (CR LF or LF) and its own indent, and Ctrl+Z undoes it as one step
- **Lines say their file.** Problems show in the file and on the line they're in, and the preview, the console, the Core tab, the release check and Qanvas's messages say "layout.lua:23". An #include of a file that isn't there, one that would include itself, or one the compiler passes over, is a problem on its line
- **Send to Designer** sends the plugin as the compiler writes it, where its build puts it (Plugins\room-control\room-control.qplug for a plugin in a folder called room-control), after saving its files; with a file it #includes missing, it asks first (and doesn't send on save). Plugins sent this way load and place in Designer's own plugin loader (10.0.3 and 10.5). Send on save works from any of its files
- **The rest work on the whole plugin:** the release check, Compare, the datasheet, Test mode, recorded tests (room-control.qtest.json, beside plugin.lua) and the AI assistants, which name its plugin.lua and get every line with its file
- Open the .qplug the compiler made beside plugin.lua and Qanvas offers plugin.lua instead (what's changed in the build goes with the next build)

Also
- **A new control comes last, in the code's own style.** Where GetControls fills its list with table.insert (as QSC's template does), a new control is one more table.insert after the last, so it's last in Designer's order too (it went first, inside `local ctrls = {}`)
- **Page branches that test a page number kept in a local** (`local page = props["page_index"].Value`, then `if page == 2 then`) are found, so a new control goes on the page you're on (it went on every page)
- An error in your plugin's code from what you type at the Test console shows on its line, as a link, like other errors; so do the Core's findings in the console

Fixes
- **Arrow-key nudges are written before you switch pages.** Switching page within a moment of the last nudge lost it
- **A preset or handler that would take a big plugin past Lua's limit of 200 local variables in one function isn't added**, and the message says how to make room (the code was written, and Lua couldn't load it)
- Two things added at the same place in one change keep their order, as VS Code writes them

## 0.8.0 (2026-10-10)
AI assistants can see your plugins, change them, check them and test them: GitHub Copilot in VS Code, Claude Code, Claude Desktop, Cursor and any app that supports MCP.

AI assistants
- **An assistant works the way you do in the preview.** It sees the layout as data (pages, controls, graphics, places and sizes, and what placed each one: plain numbers, a loop, a helper or a lookup table, with the number a move changes and what moves with it) and as a picture, drawn by the preview itself. It runs the design check (with its fixes), the release check and Compare. It changes the plugin through the preview's own engine: moves and resizes (things loops and lookup tables place too, checked on every page), fields, controls, graphics, presets, pins, pages, properties, EventHandlers and runtime code, renames, the background, themes and finishes, stacking, looks and scaling, each call one undo step, with a dry run that says what would change (the lines, and what else moves). It starts new plugins from the starters, and exports the datasheet
- **And it tests what it builds.** Test mode: press buttons, set faders, knobs and text, play the pretend device (what it sends, HTTP replies, a dropped link, its rules), run Lua, skip ahead in time, and read the console, the Values list and the Core tab, then record the session as a test and run the recorded tests
- **GitHub Copilot in VS Code** (agent mode) has the tools once you have a plugin, with nothing to set up. VS Code asks before each change and says what it will do
- **Claude Code, Claude Desktop, Cursor and other apps:** **Qanvas: Connect an Assistant Outside VS Code** lets them in while VS Code is open, and gives the settings to add (the guide has the steps for each). It runs on VS Code's own program, so nothing else needs installing. The status bar says when one is connected
- **Safe by design.** Nothing listens on the network: outside assistants come in through a private pipe on this computer, only with a secret, and only once you've turned it on. Send to Designer always asks you first, and so does Test mode's real network (once for each plugin, until VS Code closes). An assistant works on plugins in your project, and a change that would leave the code with an error isn't written. A change is saved when the file had nothing unsaved, so assistants that read the file see it; your own unsaved typing is never saved for you, and an assistant's save never sends to Designer
- Needs VS Code 1.106 or later (pictures in tool results). Older VS Code keeps Qanvas 0.7

Closer to Designer: the preview (measured with Designer's own plugin loader, 10.5 and 10.0.3)
- **Each design function runs on its own, as in Designer.** GetControls, GetControlLayout, GetPrettyName and the rest each get a fresh Lua where the plugin's code has just run, so what one of them sets in a global isn't there in another. A layout that only worked in the preview because of one now looks as it will in Designer
- **props is what Designer hands the functions:** each property's Name, Type (Designer's: "bool", and "string" for an enum), Value, Min and Max, and Designer's own properties (Show Debug, Script Access...). QSC's plugin framework, which reads props.plugin_show_debug, now previews. With PluginInfo.ShowDebug, Show Debug is in the property values you can try, and properties RectifyProperties hides are hidden there and in the datasheet
- **Type names are exact, as in Designer.** Designer leaves out a control whose ControlType, ButtonType, ControlUnit or IndicatorType is spelled any other way (case too), and a Button without a ButtonType: the problems say so, and the preview shows only a hint of where it was. QSC's own spellings (Style "LED", IndicatorType "LED", Style "Indicator") draw as Designer draws them
- **A control with no place on any page isn't made, pin or not**: the problem says so, Test mode has no Controls entry for it, and the datasheet leaves it out (it said "pin only")
- **json, require, bit32 and Lua 5.2's math functions** (pow, atan2, cosh, sinh, tanh, frexp, ldexp, log10) are there, as in Designer and on a Core, and QSC's eight extra icon names (Telephone In, Play Pause, Circle...) aren't flagged
- **Numbers print as a Core prints them:** 1e+15, 5e-05, inf, and string.format rounds an exact half to even ("%.0f" of 2.5 is 2), as C does

Closer to a Core: Test mode
- **Controls:** Boolean is true when the position is above 0.5 (a knob at -100 dB is false), a Value of true or false is 1 or 0, MinValue, MaxValue, MinString, MaxString, IsIndeterminate and CssClass are there, a NaN is refused, and setting a misspelled property is the Core's error ("Property 'Vaule' does not exist on Control") instead of a quiet new field
- **require("json") makes the global json**, as on a Core, and require gives the standard libraries
- **TcpSocket:** Write while not connected is an error, as on a Core; EOL.Any takes any run of CR and LF as one line end; Close, which a Core doesn't have, is gone
- **HttpClient.Get, Post, Put, Patch and Delete**, and User and Password log in on the real network
- **System and Design.GetStatus** have the fields QSC lists (Version "10.5.0", BuildVersion, LockingId), and no made-up ones
- **Files:** io.open, os.remove, os.rename, os.getenv and dir have stand-ins that touch nothing and fail the way a missing file does, so the plugin's own failure path runs (they were missing)
- **Colors set from code** draw in "#RGB", "#AARRGGBB" and color names too
- The Volume buttons and IP connection presets keep their timer and socket in globals, as QSC advises

Fixes
- **The Background color always works on the one background box.** Picking it again recolors that box: it never adds a second one, however small the plugin, and it still finds the box after the plugin grows past it. A new background goes under everything, even under something sent to the back in Layers. Themes add and recolor it the same way

## 0.7.0 (2026-10-09)
Drag what your code places, test against the real device and record the tests, finish a theme, and send to Designer without restarting it.

Drag what loops, helpers and lookup tables place
- **Things your code places with loops, helper functions and lookup tables can be dragged and resized.** Qanvas works out which numbers in the code each one is made from, and a drag changes the one that moves the fewest other things: a helper's argument, a lookup table's entry, the gap above a row, a loop's step or its start. Everything made from the same numbers moves with it while you drag (outlined, dashed), and the inspector says which number, on which line, a drag changes
- **It lands where the preview shows.** Where a number is multiplied (the third strip of a loop moves by twice the step), the drag snaps to the places that number can reach, and a whole number stays whole. Resizing from one edge keeps the other edge where it is where the code allows it (cells grow into their gaps). Moving several things together changes a number they share once. Arrow keys move such things by at least one step of their number
- **It writes only what the preview showed.** The edited code is run on every page before it's written, and the edit is refused if anything else would change: something a branch in the layout code moves (a `math.max`, an `if`, a loop that runs once more), a font size, color or legend made from the same number, a property's default, an item added or gone, or a size below a pixel. When the code can't do what a drag asks, the preview says so and nothing moves. Things on another page made from the same numbers (or by the same line of code) move with them, and a note says so
- **What can't be followed exactly stays locked:** a position worked out with a division and then rounded, `%`, an index into a table, a loop's count, a number that went through a library call, or one that also sets a font size, a color or a legend. Property defaults (however they're written), PluginInfo, Min/Max and Counts are never what a drag changes, nor is the 1 in `i - 1`. A number that becomes negative is written so it still reads as one

Scale a selection as one
- **Select several things and a box with handles goes around them all:** drag a handle to scale the whole selection, a keypad down or a row of LEDs up. Places and sizes scale from the opposite side, and text sizes and corners scale with them (by the smaller of the two directions, so a row stretched wider keeps its text). **Shift** keeps the proportions, **Alt** scales from the center, **Ctrl** keeps the text sizes. The inspector's **Scale %** does it by an exact amount
- Things a loop or a lookup table places scale through the numbers they're made from: scaling a keypad built from column and row tables rewrites the tables and the key size

Layers and what's on top
- **Drag rows in Layers** (one, or several selected) to change what's on top. It's written as ZOrder, the layout property Designer stacks by, on as few items as it can (often just the one you moved). Controls always draw above graphics in Designer, so a control can't go below a graphic: Layers says so instead. There's no grouping: a plugin's layout has no groups for it to write
- **Bring Forward** and **Send Backward** join Bring to Front and Send to Back in the right-click menu, with **Ctrl+]** and **Ctrl+[** (Shift: to the front or back). Each selected item moves one step, as in Designer

Design kits
- **A finish goes with a theme** (in the 🎨 Theme menu): **corners** (square, soft or round, on buttons, displays, cards and filled labels), **text sizes** by role (one for titles, one for sections, one for the rest: Compact 16 / 12 / 10 or Comfortable 20 / 14 / 12), **one font** (Roboto, Lato, Montserrat, Open Sans or Poppins), **group boxes as cards** (filled, no outline, rounded; a box with a color of its own, like a dark title bar, keeps it) and **positions on the grid**. Hover a theme to see it all on the plugin before applying; **Keep colors** applies the finish alone. It's all layout properties Designer draws, so the plugin looks the same there and a Core runs it no slower

Test mode behaves like a Core
- **Core tab:** the console's **Core** button shows the busiest call as a share of what a Core allows each call into a plugin (200,000 Lua instructions: startup, each EventHandler, each timer tick, each socket event), and turns orange or red. Its tab lists each kind of call with its heaviest run, and what the code ran into that a Core does differently, with links to the lines. Only the plugin's own code counts, not Qanvas's stand-ins for Q-SYS's libraries
- **A call past the budget stops** with the Core's own message ("Max execution limits exceeded") on its line. One that a pcall swallows is pointed out: on a Core its work is left half done, and anything more the call does stops at the limit again
- **A plugin's own `:Trigger()`** pulses the control but doesn't run its EventHandler, as on a Core (Emulate does), and **`os.clock()` is CPU time**, as on a Core. Both are pointed out where they happen
- **Where Test mode's whole numbers part ways with a Core's, it says so.** Its Lua keeps whole numbers in 32 bits, a Core's go to 64: a result past 2,147,483,647 (a millisecond timestamp), a hex number past `0x7FFFFFFF`, or a 4-byte number from `string.unpack` past it, comes out different. Test mode points out each one, with its line and what a Core would give, and an error about it ("number has no integer representation") says why. Bit work (a CRC-32, an IP address made with `<<` and `|`) keeps its bits and isn't flagged
- **⏩ 10 s** runs what the next 10 seconds (Shift: a minute) would bring, now: timers, polling, reconnects and timeouts, with Timer.Now() moving along
- New problems in the code: `os.clock()`, a Timer kept only in a local (it can be garbage-collected while it runs, QSC's advice), and a plugin's own `Trigger()` on a control with an EventHandler. The Clock preset keeps its timer in a global

Test mode on the real network, and the rest of the design
- **Real network** (in the Test console's bar): TcpSocket (and `NewTls`), UdpSocket, WebSocket and HttpClient connect for real, from your computer, to the device on your desk or the service you're writing a driver for. What goes each way shows in the console, byte for byte, and your code's reconnects, timeouts and errors happen for real. Certificates aren't checked, so devices with self-signed ones work. Serial ports stay pretend. Off, everything talks to the pretend device you play from the console, as before
- **Pretend components:** `Component.New("Name")` gives a component whose controls keep what your code sets on them, and so do `NamedControl.SetValue` and the rest. They show in Values, under Components, by component; click one and type a value to change it the way the design would, and its EventHandler runs. `Component.GetComponents()` and `Component.GetControls()` list them
- A TcpSocket that can't connect (refused, unreachable) gets an Error, and tries again after its ReconnectTimeout, as on a Core. A port that isn't one (70000) is an Error too. A WebSocket message over 16 MB is cut off, and each test session's connections are its own

Recorded tests and device rules
- **● Record** (in the Test console) starts the test over and keeps everything you do: presses, what the device sends, lines you type, time passing (⏩ too), what the real network sent back. **■ Save test** names it and puts it in a `.qtest.json` next to the plugin (Local-Bypass.qplug gets Local-Bypass.qtest.json), so it can be committed with it
- **Recorded tests run in VS Code's Testing panel.** Each one replays on a clock that only moves when told to, so it runs the same way every time and in a moment, on any computer, and checks what the plugin did: its controls at the end, what it sent and connected to, and its errors. When the plugin starts doing something else, the test fails and says what, in words ("Message 4 it sent: was tcp 1 → PWR OFF\r\n, now tcp 1 → POWER OFF\r\n"). **Qanvas: Accept Recorded Test Results** makes what it does now the expected result. A test recorded on the real network replays what the device sent, without the network
- **Device rules** (the console's Device tab): the pretend device answers by itself. "When it receives PWR? it replies PWR=ON", "every 5 s it sends STATUS OK", "a request to GET */status gets 200 {...}". A `*` matches anything (on the line, for text), and `$1` in the reply puts it back ("PWR *" → "PWR=$1"). Rules remember: "PWR *" keeps what came after PWR, and `PWR=$PWR` says it back. Plain text is a line, `\r \n \xNN` are exact bytes. Rules are kept for each plugin, change while the test runs, and are replayed with a recorded test

Driver building blocks
- **Device driver** (a preset, under Devices) drops in a TCP device's controls (IP, port, Connect, an online LED, status, Power, Volume) and the code a driver needs, wired together and explained: a **command queue** (one command at a time, each waits for its reply, times out, is tried again, then given up; the latest of a kind replaces what's waiting, so a volume moved five times goes once), **polling** while connected, a **watchdog** that reconnects a device that goes quiet, **reply handlers** for what the device says (one that fails says so, and the queue goes on), and a fader that sends **once it settles**. Its timers live in a global table, as QSC advises, and it passes every Core check. Change the example commands for your device's
- It comes with **device rules** that play its example device in Test mode: type an IP address, turn Connect on, and the pretend device answers

Test mode: the driver test kit
- **Binary protocols work byte for byte:** what your code writes shows exactly (`→ \x81\x01\x04\x00\x02\xff`), bytes you type with `\xNN` arrive exactly (before, bytes from 0x80 up arrived as two), and printing binary data, or putting it in a control, no longer stops Test mode
- **Serial ports:** a plugin's serial pins become `SerialPorts[1]`, `[2]`… on the pretend device. Open, Write (an error while closed, as on a Core), ReadLine, Data and EventHandler all work, and what you type in the console arrives as a line
- **Crypto computes for real:** Base64, `MD5Compute`, `Digest` and `HMAC` (MD5, SHA-1, SHA-256, SHA-512), AES-128/256 in CBC and ECB, PBKDF2 and random bytes, checked byte for byte against OpenSSL, so device logins and signed requests can be tested. (`CRC16Compute` leaves a note: QSC doesn't say which CRC-16 it is)
- **WebSocket** talks to a pretend server: connect, write, ping and pong, messages you type arrive as typed, and Drop link closes it
- What you type for a UDP or WebSocket device is sent as typed; TcpSocket and serial get a line (CR LF added)
- **Values** in the console lists every control's Value, String and Position as your code sees them, updated live; a change flashes and **ƒ** marks a control whose EventHandler is set. Hover a row to point the control out, click it to see its EventHandler in the code, filter by name. Drag the console's top edge to make it taller (remembered)

Send to Designer, without restarting Designer
- **Qanvas: Send to Designer** (also in the preview's Export menu) puts the plugin in Q-SYS Designer's Plugins folder, and **Designer's library has it straight away, under the same name: no restart.** Designer reads a plugin file the moment one appears in its Plugins folder, but one changed in place only when it starts, so Qanvas writes the new version beside the old one and swaps it in, in one step (the plugin is never missing or half-written there). Blocks already in a design keep the version they were made with until you take the update Designer offers when the design opens. Turn on `qanvas.sendToDesignerOnSave` to send on every save
- It never replaces something that isn't this plugin: the Id is read from the code (not from a comment), a different plugin with the same file name, or one whose Id can't be read, is only replaced if you say so, and another copy with the same Id, or an encrypted `.qplugx` build next to it, is pointed out. A link in the Plugins folder is left alone (restart Designer for a plugin linked there); a plugin opened from the folder itself reloads too
- **A plugin with a Lua error isn't sent on save** (the status bar says which line), and Send to Designer asks first: Designer would show a Plugin Error and drop it from its library until a version without the error arrives. A Plugins folder set in Settings is the only one used: if it isn't there, nothing is sent

Don't break designs that already use the plugin
- **Renaming a control that has a pin** in the version in Designer's Plugins folder asks first: designs wired to it lose the wire when its Name changes. **Change the pin name** changes only what the pin shows (PrettyName), which keeps the wires
- **Qanvas: Compare With Another Version** (also in the Export menu) lists what designs that use another version would notice: Designer's copy, the last git commit, or any file. It shows wires that break (a renamed or removed control or audio pin, fewer elements, one control made into several or several into one, a pin that lost a direction, a new Id), controls that behave differently (another type, gone without a pin), properties that are gone or changed, and what's new. The code follows as you go through the list

Release check
- **Qanvas: Release Check** (also in the Export menu) lists what to look at before sharing a plugin, and the code follows as you arrow through: errors and warnings from the Problems panel, a placeholder or non-GUID Id, no Version, ShowDebug on, leftover `print()` calls, IP addresses and passwords written into the code, another plugin in Designer's Plugins folder with the same Id (or this one under its old name, after a rename), extra copies of this one there, Designer's copy having the same Version with different code, and what would break since Designer's copy

Pins view
- **+ Control in** and **+ Control out** show the new pin on the block right away. It was added to the code, but a pin with a PinStyle starts unticked in Designer, so the block left it off and it looked like nothing happened
- The block says how many optional pins it leaves off (as a block dropped into a design does) and **show them all** ticks them
- The strip of controls that aren't on the panel says what they are (GetControls makes them, GetControlLayout gives them no place) and stays out of the Pins view

Starting a plugin, checked against Designer's own loader
- **An empty `.qplug` becomes a plugin in one click:** open the preview on it and Qanvas offers to start a new plugin there (PluginInfo with a new Id, and the functions Designer needs). The lightbulb on its first problem does the same, and a file missing only its PluginInfo gets one added
- New checks, measured with Designer 10.0.3's and 10.5's own plugin loader: without GetControls, Designer lists the plugin but can't place a block of it (an error, with a quick fix). Without GetControlLayout, Designer adds a script's own controls to the block (a warning, with a quick fix)
- Every New Plugin starter, and a plugin built only in the preview, was loaded with that loader: each loads, and its block builds all its controls

The Q-SYS libraries in the code
- **Completions and hovers for the Q-SYS Lua libraries:** `TcpSocket.`, `Timer.`, `HttpClient.`, `Crypto.` and the rest list what they offer, each with a short description. A variable you made with one (`sock = TcpSocket.New()`, `SerialPorts[1]`, `require("rapidjson")`) completes too: `sock:` lists its methods, `sock.` its properties and handlers. So does the first parameter of a handler you set on it. Event and line-ending names complete after `TcpSocket.Events.` and `.EOL.`. Requests and handlers come as fill-in snippets
- They work even while the plugin has an error, and everything they list is something Test mode runs

Trace an image
- With nothing selected, **Trace an image** (in the plugin panel) lays a mockup or a screenshot over the plugin to build to: set its opacity, scale (100% = one image pixel to one plugin pixel, or Fit to the plugin's width) and position, hide it for a moment, or remove it. Only you see it: it isn't written to the code, Save image and the datasheet leave it out, and clicks go through it. Qanvas remembers it for each plugin

Fixed
- **Graphics drew in the wrong order when helper functions add them:** they were stacked by where their table is written in the code instead of the order they go into the graphics table, as Designer draws them. A background added after a `label()` helper was defined covered every label and card. Controls now always draw above graphics, as measured in Designer, whatever their ZOrder
- **An integer property's Value is an integer**, as on a Core, in Test mode and when you set a property's value in the preview: a port from a property joins a URL as `8080`, not `8080.0`, and a label made from a count reads `Ch 8`
- Arrow-key nudges waiting to be written could be written in the middle of a drag that followed straight away, with the drag's positions. Nudges are now written before a drag starts
- Saving a recorded test, accepting results and comparing with a file say what went wrong instead of nothing; a theme's corners go under the spelling a table already uses (`Radius`)

## 0.6.0 (2026-10-06)

First public release, on the VS Code Marketplace.

Getting started
- `.qplug` files open as Lua, highlighted, with no setup
- A first-run tip says how to open the preview; later, bug-fix updates install quietly and only a new feature release says what's new

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
