# Tutorials - Mac Exporting

This comes up sometimes so I figured I would cover it. However, as you can notice by scanning the page, it is short and sweet. When exporting for macOS, there are a few minor modifications you must make to the app's ZIP file.

{==
## Beware Codesign
==}

For Godot 3.x users, one option you need to make sure is disabled is Codesign. For some users, it makes the game unable to talk to Steam and results in the "Steam not running" error. Just check for this during export:

![Codesign Fail](../assets/images/mac-caveats1.png){ loading=lazy }

Apparently, for Godot 4.x users, codesign is fine but you may want to keep the notarizing stuff empty.

{==
## Disabling Library Validation
==}

This one is apparently still valid though Valve says it is only needed on Catalina 10.15, but you will want to disable library validation to allow Steamworks to load and the overlay to work properly.

![Disable Validation](../assets/images/mac-caveats2.png){ loading=lazy }

{==
## App Icon
==}

There is, by default, a Godot Engine icon already inside the `{your game}.app/Contents/Resources` folder. You will most likely want to replace it with a fancy version of your game's icon. All you have to do is export your icon and save it as `icon.icns` then replace it in the same folder, overwriting the original.

You should be able to create it online or with a Photoshop plug-in. Unfortunately I don't have any great resources or links for this at the time of writing, but a quick DuckDuckGo search should sort you out.

{==
## Steamworks Components
==}

Second, you need to add the `libsteam_api.dylib` to the `{your game}.app/Contents/MacOS` folder:

The `libsteam_api.dylib` should be in the `redistributable_bin/osx` folder inside the Steamworks SDK. Make sure it matches the Steamworks API version you are using to compile the editor/template or it will crash.

Alternatively, if you are using the pre-compiled one from GodotSteam's GitHub release section, you can thank **SapphireMH** for already including this file for you!