# The Unity GUI
### Intro
If you want to make a mod that requires complex user input, a GUI Window is usually the way to go, so I'm going to cover the usage of [the Unity GUI System](https://docs.unity3d.com/ScriptReference/GUI.html).

### Examples
**FPV:**

![Example 1: FPV Mod](https://i.imgur.com/yvJzhpF.png)

**BuilderTools:**

![Example 2: BT Mod](https://i.imgur.com/N6MTNi0.png)

***

### Code Walkthrough

First thing we need is a [Rect](https://docs.unity3d.com/ScriptReference/Rect.html) that indicates where and how big the window is going to be:
```lua
function ExampleGUI:Start()
  local height = 200
  local width = 150
  local margin = 25
  
  self.rect = Rect(margin, margin, width, height)
end
```
I usually make ``height``, ``width`` and ``margin`` local, then create the rectangle (``self.rect`` in this case).

Next we need to setup the window through [GUILayout](https://docs.unity3d.com/ScriptReference/GUILayout.html). This is done in the ``OnGUI`` function:
```lua
function ExampleGUI:OnGUI()
  self.rect = GUILayout.Window(1807, self.rect, self.UpdateWindow, "Example")
end
```
Where ``1807`` is the window ID and ``"Example"`` is the Title of the Window. ``self.UpdateWindow`` directs to a new function we need to set up right now:
```lua
function ExampleGUI.UpdateWindow(id)
  GUILayout.BeginVertical()
  
  GUILayout.Space(15)
  
  GUILayout.Label("Here's one label.")
  
  if (GUILayout.Button("Click me please.")) then
    print("Button clicked")
  end
  
  GUILayout.EndVertical()
end
```
``UpdateWindow`` manages all of the UI stuff. Also note that it's ``ExampleGUI.UpdateWindow`` instead of ``ExampleGUI:UpdateWindow``. This means that you can not use ``self.``, instead you can use ``ExampleGUI.`` to access variables declared with ``self.`` before (e.g. If you want to access ``self.rect`` you need ``ExampleGUI.rect`` instead).

First UI-related thing to do is a "Begin", either ``GUILayout.BeginHorizontal()`` or ``GUILayout.BeginVertical()``. Now, the Elements usually start right over the title, so if you want to not cover up your title with elements, you need a spacer (``GUILayout.Space(15)``). Then you can start adding elements:

***

* **Labels**

You can add Labels with ``GUILayout.Label("Text")``. Using a variable to store the text is not a problem, since ``UpdateWindow`` is called continously:
```lua
function ExampleGUI:Start()
  self.number = 0 --setup a number
end

function ExampleGUI:Update()
  self.number = self.number + 1 --increase the number every frame
end

function ExampleGUI.UpdateWindow(id)
  GUILayout.BeginVertical()
  GUILayout.Label(ExampleGUI.number) --print the number to the window
  GUILayout.EndVertical()
end
```
(Incomplete Code, just to highlight the changing text)

* **Buttons**

Buttons are initialized through ``GUILayout.Button("Text")``, which also returns a ``boolean`` indicating whether or not it was pressed, so you can use an ``if`` statement:

```lua
function ExampleGUI.UpdateWindow(id)
  GUILayout.BeginVertical()
  
  if (GUILayout.Button("Click me")) then
    print("The Button was clicked.")
  end
  
  GUILayout.EndVertical()
end
```
(Incomplete Code, just to highlight the usage of Buttons)

* **Other Elements**

You can find the whole list of elements and Layouts [in the GUILayout Docs](https://docs.unity3d.com/ScriptReference/GUILayout.html).

***

After you are done with the Layout (In this case "Vertical", initialized by ``GUILayout.BeginVertical()``), you need to end it like this:
```lua
GUILayout.EndVertical()
```

If you want to make a List-like Layout you can use something like this:
```lua
GUILayout.BeginVertical() --init root layout

GUILayout.BeginHorizontal() --init line 1

GUILayout.Label("Text")
GUILayout.Button("A")

GUILayout.EndHorizontal() --end line 1

GUILayout.BeginHorizontal() --init line 2

GUILayout.Label("Line 2")
GUILayout.Button("B")

GUILayout.EndHorizontal() --end line 2

GUILayout.EndVertical() --end root
```

### Result
If we now put everything together we should see something like this:

![Resulting Window](https://i.imgur.com/CsC6ctR.png)

And clicking the button with the Console opened should result in something like this:
![Console](https://i.imgur.com/iuruOxP.jpg)

### Code
ExampleGUI.lua

```lua
local ExampleGUI = {}

function ExampleGUI:Start()
  local height = 200
  local width = 150
  local margin = 25
  
  self.rect = Rect(margin, margin, width, height)
end

function ExampleGUI:Update()
end

function ExampleGUI:OnGUI()
  --you can add an if-statement here to show/hide the window
  self.rect = GUILayout.Window(1807, self.rect, self.UpdateWindow, "GUI Example")
end

function ExampleGUI.UpdateWindow(id)
  GUILayout.BeginVertical()
  
  GUILayout.Space(15)
  
  GUILayout.Label("Here's one label.")
  
  if (GUILayout.Button("Click me please.")) then
    print("Button clicked")
  end
  
  GUILayout.EndVertical()
end

return ExampleGUI
```

_Author: Ryz_
