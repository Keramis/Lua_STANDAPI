# LUA for the Stand Mod Menu

---

### Hello! It's your friendly neighborhood scriptcat.
I made this in the hopes to stop getting people to stop asking ***very stupid*** questions in the `#programming` channel.
Hopefully this guide serves you somewhat well. Of course, if you have any questions, pop it into `#programming`, but I swear to god if even a single person asks "what is a boolean?" I will shit myself.

## So, what the hell is LUA?

Not going to lie, you should probably go through some YouTube tutorials on LUA. This will teach you a few things, nicknacks, and mostly Stand's API and the Natives for GTAV. I'm not going to go into too much depth on how to actually use the things that LUA has to offer because, frankly, other people have done it way better.

## You should probably watch a YouTube tutorial to get the general gist of the language.

Got your basic knowledge? Good. You should've learned:

- Variables (local and global)
    - These store values. Any values, since LUA is dynamically-typed, meaning you do not need to define types (other languages, like C++, require you to say *what* your variable is going to be, whether an `integer`, `string`, `float`, etc.)
- Tables (also known as `arrays` or `matrixes` in other programming languages)
    - These group together values, and can group together variables as well, since... those are just values.
- Functions
    - These are bits of code that can take parameters (things that you input), and can be ran. This makes it so that you don't have to copy-paste a shitton of code every single time you want to do ***`the same thing but a bit different`*** somewhere else in your script.
- Maybe something else? These are just the basics, after all.

## Time for the interesting portion!

Make sure you read everything beforehand, and followed the steps (watch the tutorial already dammit). If you haven't, this will be... let's say... a bit difficult.

### Anyways....

- First, open up your `Stand` folder. This can be done through the launchpad, or through `C:\Users\<user>\Appdata\Roaming\Stand`.
- Next, open the `Lua Scripts` folder. Self explanatory, right?
- In here is where you will place all of your scripts. So go ahead and make a new text document, name it something, and add a `.lua` extension instead of the default `.txt` (if you don't have file extensions enabled, why are you even trying to bother with programming?).

## Cool! So now what?

Well... anything that comes across your mind, really! Take a gander through the [Stand Lua Api](https://stand.gg/help/lua-api-documentation). This might seem archaic and hard to understand at first, but I'll walk you through it:

Let's take a menu function. For example, `menu.action()`. The entire action is:

```lua
int menu.action(int parent, string menu_name, table<any, string> command_names, string help_text, function on_click, ?function on_command = nil, ?string syntax = nil)
```

Seems daunting, no? Here's the human version, listed on a by-parameter basis.

- `int` parent
    - This is the variable that you put in for where this function (in this case, button), should appear. This either could be the `root`, which is where everything in your script starts from, or a `menu.list`, which is a list of functions. What this means is that you can either:
        1. Put `menu.my_root()` (or a variable of it, doesn't matter, since variables store values) in the parent
        2. Put a variable that has your `menu.list` value in it.
- `string` menu_name
    - This is just the name that will appear as your button.
- `table`<any, string> command_names
    - This is a `table` of valid commands that you can type into the Stand Command Box that will activate this function. Since it is a `table`, you can make multiple commands for one function.
- `string` help_text
    - This is the small print that appears at the bottom of the menu explaining what the hell this function actually does.
- `funciton` on_click
    - This is the function (code) that will be executed if you click this action button.
- `?function` on_command `= nil`
    - Basically, if you set this, this will run different code if you used the function from the command box or not. Notice how it says `=nil` at the end, and a `?` at the beginning? This is because this ***isn't required; if you have nothing there, it will do the same code if you click it, or use it from the command box***.
- `?string` syntax `= nil`
    - This changes what shows up in help text of the menu, like `Command: <string>`. If you put `"aaa"` in here, it will display `Command: aaa` instead of `Command: <commandname>`.

---

## Pro tip: always use `util.keep_running()` at the top of your script to keep it running.

---

### Let's make a quick button that does a notification, or a `toast`, that says "hi!".

I will comment almost everything that I write, so you can get a semi-comprehensive understanding of what the hell I am doing.

```lua
util.keep_running() --this makes the script not stop running after it has done its job

menu.action(menu.my_root(), "Hi Button!", {}, "This is help text!", function()
    util.toast("Hi!") --the util.toast() function makes a notification of a string that you pass it.
end)
--menu.my_root() makes it appear in the main script section. you could replace it with:
local menuroot = menu.my_root()
--and then do:
menu.action(menuroot, ...)
```

So what this does is:
- Makes a button (action)
    - `menu.action()`
- That is in the `root` (main) script section
    - `menu.my_root()` or, renamed (variable) `menuroot`
- That has the name of `Hi Button!`
- That has **no** commands that we can use for it (since the table is empty, nothing in the curly braces `{}`).
- That has help text of `This is help text!`
- That `toasts`, or *notifies*, the user with text `Hi!`.

Notice how we have a function **inside of the menu.action**? Basically, we can make the function inside of it instead of having to make it ouside of our menu function. This can be either seen as lazy or resourceful, depending on who you ask.

We could've done:

```lua
local function notificationHi() --it's a LOCAL function because it's only used here, not GLOBAL to be used everywhere.
    util.toast("Hi!")
end
menu.action(menu.my_root(), "Hi Button!", {}, "Help text", sayHi)
```

It does the same thing.

If you wanted to make this button inside some sort of list, you would do:

```lua
local myList = menu.list(menu.my_root(), "My List!", {}, "")

menu.action(myList, "Button inside list!", {}, "", function()
    --code here
end)
```

And you can do lists inside of lists. Imagine lists as folders.

### Make sure to play around with this stuff, and get a somewhat decent understanding of how to write menu functions. Remember to look at [the API](https://stand.gg/help/lua-api-documentation) for functions.

---

## Now onto GTAV stuff!

---

What you first have to do is <ins>look at your options</ins>. Please, take a gander through [the native documentation](https://nativedb.dotindustries.dev/natives). **Natives** are functions that are native to GTAV, or, in actuality, the RAGE game engine that the game is based on. Just know that we can use these (well most) functions without any concerns for safety whatsoever.

Please also remember to take a look at what `namespace`, or `category`, the functions are in. You cannot just call, for example, `GET_PLAYER_PED`, you have to do `PED.GET_PLAYER_PED`, with the `PED.` at the beginning being the category that it is in. You can find these categories by just scrolling up until you find it. They are BIG, in all caps.

### Remember that, as with anything in LUA, you can **rename these functions**. Whenever someone wants to `get entity coords`, it would be a hassle to write out, in ALL CAPS, `ENTITY.GET_ENTITY_COORDS()`. You could, instead:

```lua
local getEntityCoords = ENTITY.GET_ENTITY_COORDS
```

And this lets you use the expression `getEntityCoords` instead of `ENTITY.GET_ENTITY_COORDS` every time you want to use `ENTITY.GET_ENTITY_COORDS`.

Actually, while we're on the topic of `ENTITY.GET_ENTITY_COORDS`, take a look at the full function:

```cpp
Vector3 GET_ENTITY_COORDS(​Entity entity, Bool alive)
```

The `Vector3` at the beginning is what the function *returns*. So, for example, let's just say I had an entity named `entity1`. What I would do to save the coordinates would be:

```lua
local entCoords = ENTITY.GET_ENTITY_COORDS(entity1)
--notice how I didn't do anything for Bool alive.
--That's because it says: `alive` = Unused by the game, potentially used by debug builds of GTA in order 
--to assert whether or not an entity was alive.

--So.. it's not needed :). Always check the comments/descriptions on the native documentation. They help.
```

And now I have a `Vector3` variable named `entCoords`. But.. what exactly is a `Vector3` variable?

Basically? A vector3 is just a table that contains x, y, and z values. (x and y are horizontal, z is vertical)

```lua
Vector3 = {x = xPos, y = yPos, z = zPos}
--so, to use it...
local somePosition = --get position with code here
--now we have local variable somePosition as a table of x, y, z.
```

Use these coordinates in other functions as inputs. Since it's a table, you can do `somePosition.x` for x value, and so on for the `y` and `z` values.

## So.. how the fuck do you use these?

Well, to use natives, you will need to include the `natives-xxxx` file. This can be done with:
`util.require_natives(xxxx)`. The most recent version is `1640181023`, so you would do
`util.require_natives(1640181023)` at the top of your script.

```lua
util.keep_running()
util.require_natives(1640181023)
--now we do our code here!
```

## Challenge!

Using your resources (Native Database, Stand API), make a `menu.action` that will teleport you to coordinates (0, 100, 100).

### The solution is here; but don't look if you haven't tried. 90% of the learning is trying, failing, and trying again.

---
---

```lua
util.keep_running() --make the script keep running
util.require_natives(1640181023) --need this to use the native functions

local ourPlayer = PLAYER.GET_PLAYER_PED(players.user())
--this gets the player PED (or pedestrian) of a specified player. We specified players.user(), which is OUR player ID.
--So this gets OUR PED, which we can do lots with.

ENTITY.SET_ENTITY_COORDS(ourPlayer, 0, 100, 100, false, false, false, false)
--void SET_ENTITY_COORDS(​Entity entity, float xPos, float yPos, float zPos, BOOL xAxis, BOOL yAxis, BOOL zAxis, BOOL clearArea)
--Axis - Invert Axis Flags

--The first 3 false statements are to NOT invert the axis, meaning that we don't invert our position (why would we ever
--want this?)

--The last false statement is to NOT clear the area when we teleport; we don't want to delete cars, or try to
--delete other people, which is a FREEZE event, and can get you kicked by other modders :)
