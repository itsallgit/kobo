# Kobo Customisation

## Components

TLDR
* NickelMenu is worth the QoL customisations and plays nice with native Kobo (no trade-offs)
* My personal requirements don't warrant fiddling with KOReader:
    * Bluetooth remote doesn't play nice with KOReader
    * Don't need to read cross-platform
    * Very rarely need to use Calibre (don't need wireless or Google Drive)
    * Native Kobo reading is snappier and doesn't need to be customised

### NickelMenu

This app creates a bottom navbar menu item that can be customised to trigger different commands. It can be used to launch other apps such as KOReader. This is preferred over KFMon that launches apps by creating a custom book the user needs to click on.

https://pgaskin.net/NickelMenu/

### KOReader

This app replaces the native Kobo OS (Nickel) for managing and reading ebook content. It has its own database for tracking reading progress and statistics. You essentially must choose between native Kobo and KOReader.

KOReader has the obvious choice if you want cross-platform reading (requires plugins and centralised KOReader server) as native Kobo is locked down to Kobo and the specific device.

KOReader provides additional features over the native Kobo such as:
* Wireless Calibre integration
* Google Drive support
* Folder-based content navigation
* Richer reading statistics
* Deeper file support and reading customisation
* Full darkmode (Kobo doesn't darkmode the menus, only book content)
* Adds device rotation support for unsupported devices (e.g. Kobo Clara BW)

https://github.com/koreader/koreader/wiki/Installation-on-Kobo-devices

### Kobo Plugin

The Kobo Plugin for KOReader enables seamless integration with your Kobo device’s native library. This plugin creates a virtual library that allows you to browse and read kepub books from your Kobo’s Nickel OS directly within KOReader, while maintaining synchronization of reading progress between both applications.

This plugin is needed to make the Kobo Remote work within KOReader ([reddit post](https://www.reddit.com/r/koreader/comments/1px46v6/release_v030_kobokoplugin/))

https://github.com/OGKevin/kobo.koplugin
https://ogkevin.github.io/kobo.koplugin/


## Setup Guide

High-level steps:

1. Install NickelMenu
1. Configure NickelMenu
1. Install KOReader
1. Install Kobo Plugin

### 1. Install NickelMenu

1. Download `KoboRoot.tgz` from latest release: https://github.com/pgaskin/NickelMenu/releases
1. Copy `KoboRoot.tgz` into `.kobo` from Kobo root directory
1. Safely eject Kobo

Kobo will automatically restart and show update screen while it installs NickelMenu. When completed, you will see the Kobo homescreen with a new NickelMenu button on the bottom navbar (note: older versions will show button in top right).

Next steps are to configure menu items in NickelMenu.

### 2. Configure NickelMenu

1. Create new file `nmconfig` in `.adds/nm` folder from Kobo root folder.
    * Must have no file extension
    * Can be called anything
1. Add settings based on `.adds/nm/docs` instructions and optionally copy settings from `nmconfig` file in this repo

To change the bottom navbar button Icon and Text (E.g. "Nickel"):

1. Put icon file in `.adds/nm/.icon.png`
1. Add following lines to config file.

```
experimental : menu_main_15505_label        : Nickel
experimental : menu_main_15505_icon	        : /mnt/onboard/.adds/nm/.icon.png
```

> Note: There are many people online with custom

### 3. Install KOReader

Quick context: KOReader installs a bunch of files that appear in your "My Books" section. Looks messy but they're needed. You can hide them by edding the Kobo eReader conf file. This must be done BEFORE installing KOReader as it essentially deletes the files if they already exist but ignores them if they are added afterwards (weird).

1. Directly from the [docs](https://github.com/koreader/koreader/wiki/Installation-on-Kobo-devices):
    > On FW >= 4.17, Nickel will attempt to index content found in hidden directories, and KOReader happens to live in one of those. Therefore you have to prevent Nickel from scanning our custom directories. Double-check that your Kobo configuration file `.kobo/Kobo/Kobo eReader.conf` contains:

    ```
    [FeatureSettings]
    ExcludeSyncFolders=(\\.(?!kobo|adobe).+|([^.][^/]*/)+\\..+)
    ```
    *Note: These lines can be anywhere in the file and there should only be one `[FeatureSettings]` section so you may need to add it or just add the second line under the existing section.*
1. May not be required but just to be sure... Safely eject, reboot Kobo (using helpful NickelMenu command), reconnect to PC (also using helpful NickelMenu command), continue...
1. Download `koreader-kobo-<version>.zip` from latest release: https://github.com/koreader/koreader/releases
1. Extract zip locally then copy `koreader` folder to `.adds` from Kobo root directory.
1. Ensure following lines are in NickelMenu `nmconfig` file (or whatever you called it) - this will give you an option to launch KOReader.
    ```
    menu_item : main : KOReader	: cmd_spawn : quiet : exec /mnt/onboard/.adds/koreader/koreader.sh
    ```
1. Safely eject and reboot - you should now be able to launch KOReader from the new option in NickelMenu

*Sidenote: I had to go through these steps multiple times for it to work. There are multiple guides that provide their own files to install. Ultimately, following these steps with the official files listed above worked.*

### 4. Install Kobo Plugin

https://ogkevin.github.io/kobo.koplugin/installation.html

1. Download the latest release
    * Go to the [latest release page](https://github.com/OGKevin/kobo.koplugin/releases)
    * Download `kobo.koplugin.zip` and `kobo-patches.zip`

1. Extract and install the plugin
    * Extract `kobo.koplugin.zip` to obtain the `kobo.koplugin/` folder
    * Copy the entire `kobo.koplugin/` folder to your KOReader plugins directory on the Kobo device
    * The final path should be: `[KOReader]/plugins/kobo.koplugin/`
    
1. Extract and install the patches
    * Extract `kobo-patches.zip` to get the patch files (e.g., 2-*.lua)
    * Copy these patch files directly into your KOReader patches folder on the Kobo device
    * Final location: `[KOReader]/patches/2-*.lua` (patch files directly in the patches folder)
    * Note: The patches folder may be missing; create `[KOReader]/patches/` if needed.

1. Restart KOReader
    * Restart KOReader on your Kobo device for the plugin to load and become active

## Setup Notes

* Factory reseting the Kobo does **NOT** cleanup custom installations. Best you can do after a factory reset is delete the `.adds` folder. I believe this should get as close to stock as possible without digging into the weeds.
* If NickelMenu randomly disappears, following the [Install NickelMenu](#1.-Install-NickelMenu) again will fix it and retain the custom settings.
* This repo was written after a successful setup of NickelMenu and KOReader (although decided not to use KOReader) on a Kobo Clara BW running firmware `4.44.23552 (5ec6792843, 11/13/25)`. All files used have been snapshotted in this repo.