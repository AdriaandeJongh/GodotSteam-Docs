# How-To Servers

Here we provide a, hopefully, thorough explanation of how to set-up, build, and use GodotSteam Server. You can, of course, skip all this and just download our pre-compiles or plug-in.

In the event you want to build a GDExtension or GDNative plug-in of the servers, please follow the [GDExtension](gdextension.md) or [GDNative](gdnative.md) guides but use the [GodotSteam Server source code](https://github.com/CoaguCo-Industries/GodotSteam-Server) where you would normally use the regular GodotSteam source.

---
## 1a. Downloading

By far the easiest way to use GodotSteam Server is to download our pre-compiled editors and templates; especially good for folks who don't want to set up the tools for compiling and just want to get going.

- [x] Download the [pre-compiled editor from the Release section](https://github.com/Gramps/GodotSteam/releases){ target="_blank" } and unpack it.
- [x] Everything you need should be included.

At this point you can skip all the following steps and check our our tutorials to learn more about integrating Steamworks or just explore the SDK!

---
## 1b. Compile Yourself

For those of you who are comfortable compiling or want to give it a shot, here are some steps to follow.

- [x] Set your system up for [compiling based on Godot's recommendations / required tools.](https://docs.godotengine.org/en/stable/development/compiling/index.html){ target="_blank" }
- [x] Download and unpack the [Godot source 3.x](https://github.com/godotengine/godot){ target="_blank" }
- [x] Acquire the GodotSteam Server source either by downloading it here or cloning the repo:
  * [Download the server3 or server4 branch from our repository](https://github.com/CoaguCo-Industries/GodotSteam){ target="_blank" } then unpack it into a folder called **godotsteam_server** inside your Godot Engine source code **/modules** folder.
    * You will have to create the **godotsteam_server** folder and it must be named exactly this.
  * Alternatively, clone the server3 or server4 branch from our repository into your Godot Engine source code **/modules** folder
    * Use ````git clone -b server3 https://github.com/Gramps/GodotSteam.git godotsteam_server```` for the Godot 3.x version
    * Use ````git clone -b server4 https://github.com/Gramps/GodotSteam.git godotsteam_server```` for the Godot 4.x version
- [x] Download and unpack the [Steamworks SDK](https://partner.steamgames.com).
  - This requires a Steam developer account.

---
## 2. Setting Up the SDK

Move the following from the unzipped Steamworks SDK to the **/modules/godotsteam_server/sdk/** folder:
````
    sdk/public/
    sdk/redistributable_bin/
````

---
## 3. Double-Checking Folder / File Structure

The repo's directory contents should now look like this:
````
    godotsteam_server/sdk/public/*
    godotsteam_server/sdk/redistributable_bin/*
    godotsteam_server/SCsub
    godotsteam_server/config.py
    godotsteam_server/godotsteam.cpp
    godotsteam_server/godotsteam.h
    godotsteam_server/register_types.cpp
    godotsteam_server/register_types.h
````

You can also just put the godotsteam_server directory where ever you like and just apply the ````custom_modules=.../path/to/dir/godotsteam_server```` flag in SCONS when compiling. Make sure the ````custom_modules=```` flag points to where the godotsteam_server folder is located.

---
## 4. Compiling Time

Recompile for your platform:

!!! godotsteam "For headless with editor functionality"
    === "Godot 3.x"
        ````scons platform=server production=yes tools=yes target=release_debug````
    === "Godot 4.x"
        ````scons platform=<your platform> target=editor````

!!! godotsteam "For debug server"
    === "Godot 3.x"
        ````scons platform=server production=yes tools=no target=release_debug````
    === "Godot 4.x"
        ````scons platform=<your platform> target=template_debug````

!!! godotsteam "For optimized server"
    === "Godot 3.x"
        ````scons platform=server production=yes tools=no target=release````
    === "Godot 4.x"
        ````scons platform=<your platform> target=template_release  production=yes````

**Note:** Use the ```--headless``` command when running the headless server.

Some things to be aware of:

- If using Ubuntu 16.10 or higher and having issues with PIE security in GCC, use ````LINKFLAGS='-no-pie'```` to get an executable instead of a shared library.
- When creating templates for OSX, [please refer to this post for assistance as the documentation is a bit lacking.]( http://steamcommunity.com/app/404790/discussions/0/364042703865087202/){ target="_blank" }

---
## 5. All Together Now

When recompiling the engine is finished, do the following before running it the first time:

- [x] Copy the shared library (steam_api), for your OS, from sdk/redistributable_bin/ folders to the Godot binary location (by default in the godot source /bin/ file but you can move them to a new folder).
    - These files are called **steam_api.dll, steam_api64.dll, libsteam_api.so, or libsteam_api.dylib**; no other files are needed.

The finished hierarchy should look like this (if you downloaded the pre-compiles, the editor files go in place of these tools files, which are the same thing):

!!! godotsteam "Linux 32/64-bit"
  ```
  libsteam_api.so
  ./godot.linux.tools.32 or ./godot.linux.tools.64
  ```

!!! godotsteam "MacOS"
  ```
  libsteam_api.dylib
  ./godot.osx.tools.32 or ./godot.osx.tools.64
  ```

!!! godotsteam "Windows 32-bit"
  ```
  steam_api.dll
  ./godot.windows.tools.32.exe
  ```
!!! godotsteam "Windows 64-bit"
  ```
  steam_api64.dll
  ./godot.windows.tools.64.exe
  ```

Lack of the **Steam API .dll/.so/.dylib**, for your respective OS will cause the editor or game fail and crash when testing or running the game _outside_ of the Steam client.

- **NOTE:** Some people report putting the Steam API file inside their project folder fixes some issues.
- **NOTE:** For MacOS, the libsteam_api.dylib must be in the Content/MacOS/ folder in your application zip or the game will crash.
- **NOTE:** For Linux, you may have to load the overlay library for Steam overlay to work:
  ```
  export LD_PRELOAD=~/.local/share/Steam/ubuntu12_32/gameoverlayrenderer.so;~/.local/share/Steam/ubuntu12_64/gameoverlayrenderer.so
  
  or 
  
  export LD_PRELOAD=~/.local/share/Steam/ubuntu12_32/gameoverlayrenderer.so;
  export LD_PRELOAD=~/.local/share/Steam/ubuntu12_64/gameoverlayrenderer.so;
  ```
  This can be done in an .sh file that runs these before running your executable. This issue may not arise for all users and can also just be done by the user in a terminal separately. You can [read more about it in our Linux Caveats doc](../tutorials/linux_caveats.md).

---
## 6. Good To Go

From here you should be able to call various functions of Steamworks. You should be able to look up the functions in Godot itself under the search section. In addition, you should be able to [read the Steamworks API documentation](https://partner.steamgames.com/doc/){ target="_blank" } to see what all is available and cross-reference with GodotSteam's documentation.

---
## 7. Shipping Your Game

For a full explanation of exporting and shipping your game with GodotSteam, [please refer to our Export and Shipping tutorial.](../tutorials/exporting_shipping.md)

That being said, when uploading your game to Steam, you _**must**_ upload your game's executable and **Steam API .dll/.so/.dylb** (steam_api.dll, steam_api64.dll, libsteam_api.dylib, and/or libsteam_api.so). *Do not* include any .lib files as they are unnecessary; however, they won't hurt anything.
