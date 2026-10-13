<div align="center">

# Azure Library

A UI library for Roblox (Luau) that works on PC and mobile.

<img src="https://raw.githubusercontent.com/azurion-dev/AzureUI/refs/heads/main/images/Sem%20t%C3%ADtulo.png" alt="Azure Library preview" width="700">

</div>

---

## Contents

- [What you get](#what-you-get)
- [Install](#install)
- [Quick start](#quick-start)
- [Window](#window)
- [Tabs and sections](#tabs-and-sections)
- [Elements](#elements)
- [Notifications and dialogs](#notifications-and-dialogs)
- [Configs and flags](#configs-and-flags)
- [Settings tab](#settings-tab)
- [Theming](#theming)
- [Icons](#icons)
- [Key system](#key-system)
- [Library API](#library-api)
- [Full example](#full-example)
- [Good to know](#good-to-know)

---

## What you get

- Draggable, resizable window that minimizes into a floating logo button
- Tabs with icons and collapsible sections
- Label, Paragraph, Divider, Button, Toggle, Slider, Input, Keybind, Dropdown and ColorPicker
- Sliders where you can click the value and type it, with an optional color swatch
- Keybinds with Press, Toggle and Hold modes
- Notifications and dialogs
- Config saving, loading and autoload
- A key system with Luarmor, Platoboost, Static, HTTP and Custom providers
- Touch support for mobile

---

## Install

```lua
local Library = loadstring(game:HttpGet("https://azurion.pythonanywhere.com/go/api/UI/AzureUI.lua"))()
```

If you run your script again, the old UI is removed first, so you won't end up with two windows.

---

## Quick start

```lua
local Library = loadstring(game:HttpGet("https://azurion.pythonanywhere.com/go/api/UI/AzureUI.lua"))()

local Window = Library:CreateWindow({
    Title = "Azure Library",
    Icon = 127041730095194,                 -- optional logo
    ToggleKey = Enum.KeyCode.LeftControl,   -- show / hide the window
})

Window:AddTabSection("Main")

local Tab = Window:AddTab("Player", "user")   -- name, icon (Lucide name or asset id)
local Section = Tab:AddSection("Movement")

Section:AddToggle({
    Name = "Full Bright",
    Default = false,
    Callback = function(value)
        print("Full Bright:", value)
    end,
})

Section:AddSlider({
    Name = "WalkSpeed",
    Min = 16, Max = 200, Default = 16,
    Suffix = " st",
    Callback = function(value)
        print("WalkSpeed:", value)
    end,
})

Window:Notify({ Title = "Azure", Content = "Ready!", Type = "success" })
Window:AddSettingsTab()
Window:TryAutoLoad()
```

---

## Window

```lua
local Window = Library:CreateWindow({ ... })
```

### Options

| Option | Type | Default | What it does |
|---|---|---|---|
| `Title` | `string` | `"Azure Library"` | Window title. |
| `Subtitle` | `string` | `""` | Small text next to the title. |
| `Icon` | `number \| string \| false` | none | Logo for the top bar and the floating button. Asset id or `rbxassetid://` string. `false` hides it. |
| `IconUrl` | `string` | none | Load the logo from a URL (needs `writefile` and `getcustomasset`). Falls back to `Icon`. |
| `ToggleKey` | `Enum.KeyCode` | `LeftControl` | Key that shows or hides the window. |
| `FloatButton` | `boolean` | `true` | Show a draggable logo button while minimized. |
| `Width` / `Height` | `number` | 620 x 440 on PC | Starting size. Always clamped to the screen. |
| `Profile` | `boolean` | `true` | Player card at the bottom of the sidebar. |
| `ProfileName` | `string` | display name | Custom name in the profile card. |
| `Theme` | `table` | none | Color overrides, see [Theming](#theming). |
| `KeySystem` | `table` | none | Runs the [key system](#key-system) first. `CreateWindow` returns `nil` if the key fails. |
| `OnClose` | `function` | none | Called when the user hits the close button. |

### Methods

| Method | Description |
|---|---|
| `Window:AddTabSection(text)` | Small label that groups tabs in the sidebar. |
| `Window:AddTab(name, icon)` | Creates a tab. Also accepts `{ Name, Icon, Closeable }`. |
| `Window:AddSettingsTab()` | Adds the ready-made Settings tab. |
| `Window:RemoveTab(nameOrTab)` | Removes a tab. |
| `Window:SelectTab(nameOrTab)` | Switches to a tab. |
| `Window:Notify(options)` | Shows a notification. |
| `Window:Dialog(options)` | Shows a dialog. |
| `Window:SetTitle(title, subtitle)` | Changes the title. |
| `Window:SetSize(w, h)` | Resizes the window. |
| `Window:GetState()` / `Window:SetState(state)` | Reads or sets `{ x, y, w, h }`. |
| `Window:Toggle()` | Minimize or restore. |
| `Window:Minimize()` / `Window:Restore()` | Same thing, but explicit. |
| `Window:SetToggleKey(key)` | Changes the show/hide key. |
| `Window:SetFloatButton(bool)` | Turns the floating logo button on or off. |
| `Window:SaveConfig(name)` | Saves all flags plus window position and size. |
| `Window:LoadConfig(name)` | Loads a config. |
| `Window:DeleteConfig(name)` | Deletes a config. |
| `Window:TryAutoLoad()` | Loads the autoload config, if there is one. |
| `Window:Destroy()` | Unloads the whole library. |

Each tab remembers its scroll position when you minimize and restore the window.

---

## Tabs and sections

```lua
local Tab = Window:AddTab("Combat", "sword")
```

The icon can be a Lucide name (see [Icons](#icons)), a number, or an `rbxassetid://` string.

You can add elements straight to a tab, or group them in sections:

```lua
local Section = Tab:AddSection("Aimbot", {
    Collapsed = false,   -- start collapsed
    Closeable = false,   -- show an x to remove the section
})
```

**Tab methods**

| Method | Description |
|---|---|
| `Tab:AddSection(name, opts)` | Creates a section. |
| `Tab:Select()` | Opens the tab. |
| `Tab:SetName(name)` | Renames it. |
| `Tab:SetIcon(icon)` / `Tab:ClearIcon()` | Changes or removes the icon. |
| `Tab:Destroy()` | Removes the tab and everything in it. |

**Section methods**

| Method | Description |
|---|---|
| `Section:SetName(name)` | Renames it. |
| `Section:Collapse()` / `Section:Expand()` | Collapse or expand. |
| `Section:Destroy()` | Removes the section and its elements. |

Tabs and sections both have every `Add...` method listed below.

---

## Elements

### Label

```lua
local lbl = Section:AddLabel("Some text")
lbl:SetText("Changed text")
```

### Paragraph

```lua
local p = Section:AddParagraph({
    Title = "Info",
    Content = "A longer block of text that wraps.",
})

p:SetTitle("New title")
p:SetContent("New content")
```

### Divider

```lua
Section:AddDivider()
Section:AddDivider("Visuals")   -- with centered text
```

### Button

```lua
local btn = Section:AddButton({
    Name = "Teleport",
    Description = "Optional second line",
    Icon = "mousepointerclick",   -- optional
    Callback = function()
        print("Clicked")
    end,
})

btn:SetText("New name")
```

### Toggle

```lua
local toggle = Section:AddToggle({
    Name = "ESP",
    Description = "Optional second line",
    Default = false,
    FireDefault = false,   -- if true and Default is true, fires the callback on creation
    Flag = "ESP",          -- config key, defaults to Name
    Callback = function(value) print(value) end,
})

toggle:Set(true)          -- fires the callback
toggle:Set(true, true)    -- silent, no callback
print(toggle:Get())
toggle:SetText("ESP Enabled")
```

### Slider

The value box is also an input: click it and type a number.

```lua
local slider = Section:AddSlider({
    Name = "WalkSpeed",
    Min = 16,
    Max = 200,
    Default = 16,
    Increment = 1,
    Suffix = " st",
    Color = Color3.fromRGB(255, 80, 80),   -- starting bar color (follows the accent by default)
    Colorable = true,                      -- adds a swatch so the user can change the bar color
    Callback = function(value) end,
    ColorCallback = function(color) end,
})

slider:Set(50)
slider:Set(50, true)        -- silent
print(slider:Get())
slider:SetColor(Color3.fromRGB(0, 255, 120))
print(slider:GetColor())
```

With `Colorable = true`, the color is saved in configs as `<Flag>_Color`.

### Input

```lua
local input = Section:AddInput({
    Name = "Target name",
    Default = "",
    Placeholder = "type here...",
    ClearOnFocus = false,
    Numeric = false,   -- only digits, "." and "-"
    Callback = function(text, enterPressed) end,
})

input:Set("hello")
print(input:Get())
```

`AddTextbox` does the same thing as `AddInput`.

### Keybind

```lua
local bind = Section:AddKeybind({
    Name = "Fly",
    Default = Enum.KeyCode.F,
    Mode = "Toggle",   -- "Press" | "Toggle" | "Hold"
    Callback = function(state)
        print("Fly:", state)
    end,
    Changed = function(newKey)
        print("Bound to", newKey and newKey.Name or "None")
    end,
})
```

| Mode | What `Callback` gets |
|---|---|
| `Press` (default) | The `Enum.KeyCode`, every time the key is pressed. |
| `Toggle` | `true` or `false`, flipped on each press. |
| `Hold` | `true` while the key is held, `false` when released. |

Click the button to rebind. Press `Escape` while it's listening to clear the key.

| Method | Description |
|---|---|
| `bind:Set(key, silent)` | Sets the key (`Enum.KeyCode` or its name as a string). |
| `bind:Get()` | Returns the current key. |
| `bind:SetState(bool, silent)` | Sets the Toggle/Hold state. |
| `bind:GetState()` | Returns the current state. |

`AddBind` is the same as `AddKeybind`.

### Dropdown

```lua
local dd = Section:AddDropdown({
    Name = "Mode",
    Options = { "A", "B", "C" },
    Default = "A",
    Multi = false,    -- multi-select, the callback gets a table
    Search = false,   -- search box (shows up on its own with more than 8 options)
    Callback = function(value) print(value) end,
})

dd:Set("B")
print(dd:Get())                  -- a string, or a table when Multi is true
dd:Refresh({ "X", "Y" }, false)  -- swap the options (second arg: keep current value)
```

### ColorPicker

```lua
local cp = Section:AddColorPicker({
    Name = "ESP Color",
    Default = Color3.fromRGB(255, 0, 0),
    Callback = function(color) print(color) end,
})

cp:Set(Color3.fromRGB(0, 255, 0))
cp:Set("#00FF00")   -- hex works too
print(cp:Get())
```

### Things every element has

| Member | Description |
|---|---|
| `element:Destroy()` | Removes the element and its flag. |
| `element.Flag` | The name used by the config system (not set if `NoSave = true`). |

Toggle, Slider, Input, Keybind, Dropdown and ColorPicker also take:

| Option | Description |
|---|---|
| `Flag` | Custom config key. Defaults to `Name`. Duplicates get `_2`, `_3`, and so on. |
| `NoSave` | Set to `true` to leave the element out of configs. |

Button, Toggle and Keybind show `Description` as a second line.

---

## Notifications and dialogs

```lua
Window:Notify({
    Title = "Azure",
    Content = "Everything loaded.",
    Type = "success",   -- "info" | "success" | "warning" | "error"
    Duration = 4,       -- seconds
    Icon = "check",     -- optional
})

-- Shortcut that uses the latest window
Library:Notify({ Title = "Hello", Content = "World" })
```

```lua
Window:Dialog({
    Title = "Confirm",
    Content = "Are you sure?",
    Buttons = {
        { Name = "Cancel" },
        { Name = "Yes", Callback = function() print("Confirmed") end },
    },
})
```

The last button is the highlighted one. If you leave out `Buttons`, you get a single OK button.

---

## Configs and flags

Every interactive element registers a flag in `Library.Flags`. A config stores all of them plus the window position and size.

```lua
Window:SaveConfig("legit")
Window:LoadConfig("legit")
Window:DeleteConfig("legit")

Library:ListConfigs()           -- { "default", "legit", ... }
Library:SetAutoload("legit")    -- load this one on the next run
Library:GetAutoload()
Window:TryAutoLoad()
```

- Files go in the `AzureUI/` folder as `<name>.json`.
- Names are cleaned to letters, numbers and `-`.
- No `writefile` / `readfile`? Configs stay in memory until the game closes.
- Color3 and `Enum.KeyCode` values are handled for you.
- Call `Window:TryAutoLoad()` after you've created all your elements. Flags that don't exist yet can't be loaded.

You can also read and write values directly:

```lua
local value = Library.Flags["ESP"]:Get()
Library.Flags["WalkSpeed"]:Set(100)
```

`Library:Serialize()` gives you a table of all values and `Library:Apply(values)` puts them back.

---

## Settings tab

```lua
Window:AddSettingsTab()
```

Gives you a Settings tab with:

- **Config**: name, saved configs list, save / load / delete, autoload toggle
- **Menu**: menu key, logo button on minimize, accent color, unload button
- **Window**: reset window size

---

## Theming

Pass a theme table to `CreateWindow`, or call `Library:SetTheme` before you create the window:

```lua
local Window = Library:CreateWindow({
    Title = "Custom",
    Theme = {
        Accent = Color3.fromRGB(255, 80, 120),
        Background = Color3.fromRGB(15, 15, 18),
    },
})
```

| Key | Default (RGB) | Used for |
|---|---|---|
| `Background` | 20, 20, 22 | Inputs and panels |
| `Main` | 26, 26, 29 | Window background |
| `Section` | 30, 30, 33 | Section surfaces |
| `Element` | 36, 36, 40 | Element rows |
| `Hover` | 46, 46, 52 | Hover state |
| `Outline` | 58, 58, 66 | Borders |
| `Accent` | 32, 110, 255 | Checkbox, highlights, scrollbars |
| `Text` | 245, 245, 248 | Main text |
| `SubText` | 150, 150, 160 | Secondary text |
| `Success` | 52, 211, 153 | Success notifications |
| `Warning` | 251, 191, 36 | Warning notifications |
| `Danger` | 240, 82, 82 | Errors and the close button |

`Library:SetAccent(color)` changes the accent color live, on everything that already exists. The other keys only apply to elements created after you set them.

---

## Icons

Anywhere an icon is accepted, you can use:

- a Lucide name (ignores case, spaces and dashes, optional `lucide:` prefix): `"user"`, `"Shield Check"`, `"lucide:settings"`
- a number: `10734950309`
- a string: `"rbxassetid://10734950309"`

Want your own names?

```lua
Library:AddIcon("myicon", 123456789)
Window:AddTab("Mine", "myicon")
```

<details>
<summary>Built-in icon names</summary>

`activity`, `alertcircle`, `alerttriangle`, `anchor`, `archive`, `arrowdown`, `arrowleft`, `arrowright`, `arrowup`, `award`, `backpack`, `battery`, `bell`, `bomb`, `book`, `bot`, `box`, `briefcase`, `brush`, `bug`, `calculator`, `calendar`, `camera`, `car`, `check`, `checkcircle`, `chevrondown`, `chevronleft`, `chevronright`, `chevronup`, `circle`, `clipboard`, `clock`, `cloud`, `code`, `coffee`, `cog`, `coins`, `compass`, `copy`, `cpu`, `crosshair`, `crown`, `database`, `diamond`, `dollarsign`, `download`, `droplet`, `edit`, `expand`, `externallink`, `eye`, `eyeoff`, `file`, `filetext`, `filter`, `fingerprint`, `flag`, `flame`, `flashlight`, `folder`, `gamepad2`, `gauge`, `gem`, `ghost`, `gift`, `globe`, `grid`, `hammer`, `hand`, `harddrive`, `hash`, `headphones`, `heart`, `helpcircle`, `history`, `home`, `image`, `info`, `key`, `keyboard`, `layers`, `layoutdashboard`, `leaf`, `lightbulb`, `link`, `list`, `loader`, `locate`, `lock`, `login`, `logout`, `mail`, `map`, `mappin`, `maximize`, `megaphone`, `menu`, `messagesquare`, `mic`, `minimize`, `minus`, `monitor`, `moon`, `mountain`, `mouse`, `mousepointer`, `mousepointerclick`, `move`, `music`, `navigation`, `network`, `package`, `paintbucket`, `palette`, `pause`, `pencil`, `phone`, `pin`, `plane`, `play`, `plus`, `power`, `puzzle`, `radio`, `redo`, `refreshcw`, `rocket`, `ruler`, `save`, `scale`, `scan`, `scissors`, `scroll`, `search`, `send`, `server`, `settings`, `settings2`, `share`, `shield`, `shieldcheck`, `shirt`, `shoppingcart`, `shuffle`, `sidebar`, `skull`, `sliders`, `smartphone`, `smile`, `snowflake`, `sparkles`, `sprout`, `square`, `star`, `sun`, `sword`, `swords`, `tag`, `target`, `terminal`, `thermometer`, `thumbsup`, `timer`, `trash`, `trash2`, `treepine`, `trendingup`, `trophy`, `truck`, `tv`, `type`, `umbrella`, `undo`, `unlock`, `upload`, `user`, `usercheck`, `userplus`, `users`, `video`, `volume2`, `wallet`, `wand2`, `watch`, `wifi`, `wind`, `wrench`, `x`, `xcircle`, `zoomin`, `zoomout`

</details>

---

## Key system

Shows a key window before your script loads. You can use it on its own or straight from `CreateWindow`.

```lua
-- Inside CreateWindow (returns nil if the key fails)
local Window = Library:CreateWindow({
    Title = "My Hub",
    KeySystem = {
        Provider = "Static",
        Keys = { "MY-SECRET-KEY" },
    },
})
if not Window then return end

-- Or on its own (waits until the user is done)
local ok, key, info = Library:KeySystem({
    Provider = "Static",
    Keys = { "MY-SECRET-KEY" },
})
if not ok then return end
```

Keys are saved after a successful check and verified quietly the next time. Set `SaveKey = false` to turn that off.

### Providers

**Static**

```lua
Library:KeySystem({
    Provider = "Static",
    Keys = { "KEY-1", "KEY-2" },
})
```

**Luarmor**

```lua
Library:KeySystem({
    Provider = "Luarmor",
    ScriptId = "your_script_id",
    AutoLoad = false,   -- run api.load_script() automatically on success
})
```

On success `script_key` is set globally, and `info` has `Api`, `Status` and a `Load()` function.

**Platoboost**

```lua
Library:KeySystem({
    Provider = "Platoboost",
    Service = 1234,
    Identifier = nil,        -- defaults to the player's HWID
    UseNonce = false,        -- verify the response signature (executor needs SHA-256)
    Secret = "your_secret",  -- needed when UseNonce is true
    Host = "https://api.platoboost.net",
})
```

The Get Key button creates a key link and copies it for the user.

**HTTP**

Use your own API. `{key}`, `{hwid}`, `{service}`, `{userid}` and `{username}` get replaced in `Url` and `Body`.

```lua
Library:KeySystem({
    Provider = "Http",
    Url = "https://example.com/check?key={key}&hwid={hwid}",
    Method = "GET",
    -- Pick one way to detect success:
    SuccessText = "valid",             -- the response contains this text
    -- SuccessField = "data.valid",    -- a JSON field (dot path)
    -- SuccessValue = true,            -- expected value of that field (default true)
    -- If you set none of them, HTTP 200 counts as success.
    MessageField = "message",          -- optional JSON field to show the user
})
```

**Custom**

```lua
Library:KeySystem({
    Provider = "Custom",
    Validate = function(key, hwid)
        if key == "hello" then
            return true, "Welcome!"
        end
        return false, "Wrong key."
    end,
})
```

`Validate` returns `ok, message, info`.

### Options

| Option | Type | Description |
|---|---|---|
| `Provider` | `string` | `"Static"`, `"Luarmor"`, `"Platoboost"`, `"Http"`, `"Custom"`, or one you registered. |
| `Title` / `Subtitle` | `string` | Header text. |
| `Note` | `string` | Extra text under the header. |
| `Placeholder` | `string` | Placeholder in the key box. |
| `Icon` / `IconUrl` | `number \| string` | Logo in the key window. |
| `Identifier` | `string` | Replaces the HWID sent to providers. |
| `SaveKey` | `boolean` | Remember the key between runs. Default `true`. |
| `Discord` | `string` | Adds a Discord button that copies the link. |
| `Buttons` | `table` | Extra buttons: `{ { Name = "Website", Link = "https://...", Callback = fn } }`. |
| `GetKeyLink` | `string \| function` | Link for the Get Key button, or a function that returns `link, err`. |
| `Callback` / `OnSuccess` | `function` | `function(key, info)` on success. Makes the call non-blocking. |
| `OnFail` | `function` | `function(key, message)` on a failed attempt. |
| `OnClose` | `function` | Called when the window is closed without a valid key. |
| `Async` | `boolean` | Return right away instead of waiting. Returns `{ Close, IsDone }`. |

To forget a saved key:

```lua
Library:ClearSavedKey("static", "My Hub")   -- provider, then ScriptId / Service / Id / Title
```

### Your own provider

```lua
Library:RegisterKeyProvider("myprovider", {
    -- optional, enables the Get Key button
    GetLink = function(cfg)
        return "https://example.com/getkey"
    end,
    Validate = function(cfg, key, hwid)
        -- cfg is the table you passed to KeySystem
        return key == "abc", "message", { any = "info" }
    end,
})

Library:KeySystem({ Provider = "myprovider" })
```

---

## Library API

| Member | Description |
|---|---|
| `Library.Theme` | Current theme table. |
| `Library.Flags` | All registered flags (`name -> element`). |
| `Library:CreateWindow(opts)` | Creates a window. |
| `Library:KeySystem(opts)` | Shows the key window. |
| `Library:RegisterKeyProvider(name, provider)` | Adds a key provider. |
| `Library:ClearSavedKey(provider, id)` | Forgets a saved key. |
| `Library:Notify(opts)` | Notification on the latest window. |
| `Library:SetAccent(color)` | Changes the accent color live. |
| `Library:SetTheme(table)` | Applies theme values. |
| `Library:AddIcon(name, id)` | Adds an icon name. |
| `Library.ResolveIcon(icon)` | Turns a name or id into an `rbxassetid://` string. |
| `Library:SaveConfig(name, windowState)` | Saves flags to a config. |
| `Library:LoadConfig(name)` | Loads a config. Returns `ok, payload`. |
| `Library:DeleteConfig(name)` | Deletes a config. |
| `Library:ListConfigs()` | Lists saved configs. |
| `Library:SetAutoload(name)` / `Library:GetAutoload()` | Autoload config. |
| `Library:Serialize()` / `Library:Apply(values)` | Raw flag values in and out. |
| `Library.GetHwid()` | Returns the client HWID. |
| `Library.CopyText(text)` | Copies text to the clipboard. Returns whether it worked. |
| `Library:Unload()` | Destroys every window and disconnects everything. |

---

## Full example

```lua
local Library = loadstring(game:HttpGet("https://azurion.pythonanywhere.com/go/api/UI/AzureUI.lua"))()

local Window = Library:CreateWindow({
    Title = "Azure Hub",
    Icon = 127041730095194,
    ToggleKey = Enum.KeyCode.RightShift,
    KeySystem = {
        Provider = "Static",
        Keys = { "AZURE-DEMO" },
        Discord = "https://discord.gg/yourserver",
    },
})
if not Window then return end

-- Player
Window:AddTabSection("Main")
local Player = Window:AddTab("Player", "user")
local Movement = Player:AddSection("Movement")

Movement:AddSlider({
    Name = "WalkSpeed",
    Min = 16, Max = 200, Default = 16,
    Colorable = true,
    Callback = function(v)
        local char = game.Players.LocalPlayer.Character
        local hum = char and char:FindFirstChildOfClass("Humanoid")
        if hum then hum.WalkSpeed = v end
    end,
})

Movement:AddKeybind({
    Name = "Fly",
    Default = Enum.KeyCode.F,
    Mode = "Toggle",
    Callback = function(state) print("Fly:", state) end,
})

Movement:AddToggle({
    Name = "Infinite Jump",
    Description = "Jump as many times as you want",
    Callback = function(v) print("Infinite Jump:", v) end,
})

-- Visuals
local Visuals = Window:AddTab("Visuals", "eye")
local ESP = Visuals:AddSection("ESP")

ESP:AddToggle({ Name = "Enable ESP", Callback = print })
ESP:AddColorPicker({ Name = "ESP Color", Default = Color3.fromRGB(255, 0, 0), Callback = print })
ESP:AddDropdown({
    Name = "Targets",
    Options = { "Players", "NPCs", "Items" },
    Default = { "Players" },
    Multi = true,
    Callback = function(list) print(table.concat(list, ", ")) end,
})

-- Misc
local Misc = Window:AddTab("Misc", "wrench")
local Tools = Misc:AddSection("Tools")

Tools:AddInput({
    Name = "Target name",
    Placeholder = "type a name...",
    Callback = function(text, enter) print(text, enter) end,
})

Tools:AddButton({
    Name = "Show dialog",
    Callback = function()
        Window:Dialog({
            Title = "Hello",
            Content = "This is a dialog.",
            Buttons = { { Name = "Close" } },
        })
    end,
})

Window:Notify({ Title = "Azure Hub", Content = "Loaded!", Type = "success" })

Window:AddSettingsTab()
Window:TryAutoLoad()
```

---

## Good to know

- **Where the UI lives:** `gethui()` if your executor has it, otherwise `CoreGui`, otherwise `PlayerGui`. `syn.protect_gui` is used when available.
- **HTTP:** the key system uses whichever of `request`, `http_request`, `syn.request`, `http.request` or `fluxus.request` exists, and falls back to `game:HttpGet`.
- **Files:** configs, saved keys and downloaded logos need `writefile` / `readfile`. Without them, everything stays in memory.
- **Mobile:** touch dragging, resizing and bigger buttons turn on automatically on touch-only devices.
- **Aliases:** `AddTextbox` = `AddInput`, `AddBind` = `AddKeybind`.
- **Callbacks** run in their own thread inside a `pcall`, so an error in your code won't break the UI. It just prints a warning.
