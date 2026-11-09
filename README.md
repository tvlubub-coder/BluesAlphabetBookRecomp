Native Windows x64 port of "Blue's Clues: Blue's Alphabet Book" (Game Boy Color, USA),
statically recompiled with GBRecomp, plus a mod loader that applies ROM-data patches.

THIS PACKAGE DOES NOT INCLUDE THE ROM. You must provide your own original ROM.

--------------------------------------------------------------------------
 CONTENTS
--------------------------------------------------------------------------
 1. Quick start (2 minutes)
 2. Folder layout
 3. Using the loader
 4. Installing a mod
 5. Tutorial: creating your own mod
 6. Tutorial: a Minigames mod
 7. mod.ini reference
 8. Limitations
 9. Troubleshooting
10. Building from source
11. Notes / testing status

--------------------------------------------------------------------------
 1. QUICK START
--------------------------------------------------------------------------
 1. Extract the WHOLE zip and keep its subfolders (do not run from inside the zip).
 2. Copy your original ROM (.gbc) into the "roms" folder.
 3. Run BluesAlphabetBookModLoader.exe.
 4. Wait for the green/OK status: "Original ROM verified (SHA-256 matches). Ready to play."
 5. Click "Play Without Mods" to play the untouched game, or tick mods and click "Play With Mods".

 Expected ROM SHA-256:
 b0ff175ce873d458a32d6c11dc1684902c7ffd4b25ad2229fb96185307fd9482
 Any other file (other region, already patched, overdump) is rejected.

--------------------------------------------------------------------------
 2. FOLDER LAYOUT
--------------------------------------------------------------------------
 BluesAlphabetBookModLoader.exe         the launcher (3 tabs)
 BluesAlphabetBookModLoader_Classic.exe previous build, 2 tabs only (fallback)
 loader.ini                             saved ROM path and ticked mods (auto-managed)
 roms\                                  put your original ROM here
 game\                                  the recompiled game and its DLLs (do not edit)
 mods\Characters\                       character mods
 mods\Scenarios\                        scenario / background / scene mods
 mods\Minigames\                        minigame scene mods
 cache\modded.gbc                       created on "Play With Mods" (safe to delete)
 source\                                generated game source + loader source

 Your original ROM is never modified. Save files (SRAM) are shared between
 the modded and unmodded game.

--------------------------------------------------------------------------
 3. USING THE LOADER
--------------------------------------------------------------------------
 Browse ROM...        pick your ROM if it was not found automatically in "roms".
 Tabs                 Characters / Scenarios / Minigames. Tick the box next to each mod.
                      Click a mod name to read its description at the bottom.
 Open Mods Folder     opens the "mods" folder in Explorer.
 Refresh List         rescans the folders after you add or remove mods.
 Play Without Mods    launches the original ROM.
 Play With Mods       builds cache\modded.gbc from your ROM + ticked mods, then launches.

 Patch order: Characters, then Scenarios, then Minigames; alphabetical inside each tab.
 If two mods change the same bytes, the one applied last wins.
 Your selection is remembered in loader.ini.

--------------------------------------------------------------------------
 4. INSTALLING A MOD
--------------------------------------------------------------------------
 A mod is a folder with a mod.ini and one or more .ips / .bps patches.

 1. Pick the category the mod belongs to (Characters, Scenarios or Minigames).
 2. Copy the mod folder into mods\<Category>\
        mods\Minigames\My Cool Minigame\mod.ini
        mods\Minigames\My Cool Minigame\my_mod.ips
 3. In the loader click "Refresh List", tick the mod, click "Play With Mods".

 Shortcut: you can drop a bare .ips or .bps file straight into a category folder.
 The file name becomes the mod name (no author/description in that case).
 Folders starting with "_" (such as _template) are ignored.

--------------------------------------------------------------------------
 5. TUTORIAL: CREATING YOUR OWN MOD
--------------------------------------------------------------------------
 What you need (any equivalent tools work):
   - A Game Boy Color tile editor (e.g. Tile Molester) and/or a hex editor (e.g. HxD)
   - An emulator with a VRAM/tile viewer, to find where graphics live (e.g. BGB)
   - A patch creator: Flips (BPS) or Lunar IPS (IPS)
   - Your original ROM (b0ff175c...9482)

 Steps:
 1. Copy your original ROM to a work folder and keep an untouched backup.
 2. Find the data you want to change (see below), edit it in the COPY.
    Keep the file size at exactly 1 MB: overwrite bytes, never insert or delete.
 3. Create a patch comparing the ORIGINAL ROM to your edited copy:
      Flips:      "Create Patch" -> original ROM, modified ROM -> save as my_mod.bps
      Lunar IPS:  "Create IPS Patch" -> modified ROM, original ROM -> my_mod.ips
 4. Make the mod folder:
      mods\Characters\My Mod\            (or Scenarios / Minigames)
      copy mods\<Category>\_template\mod.ini into it, edit it, add your patch.
 5. Click "Refresh List", tick your mod, "Play With Mods".
 6. If the game looks wrong, untick the mod and test "Play Without Mods" to compare.

 Finding data (general method):
   - Run the original ROM in an emulator, open the tile/VRAM viewer on the screen
     you want to change, and note which tiles are used.
   - Search the ROM for those tile bytes (hex editor "find") to locate the offset,
     or use the emulator's debugger to see which ROM bank/address is copied to VRAM.
   - Remember bank addressing: ROM offset = bank * 0x4000 + (address - 0x4000)
     for banks 1 and up.
   - Palettes (GBC) are 15-bit color values in tables; text and map tables can be
     edited the same way, as long as the length stays the same.

--------------------------------------------------------------------------
 6. TUTORIAL: A MINIGAMES MOD
--------------------------------------------------------------------------
 Minigame scenes are ordinary ROM data (tiles, tilemaps, palettes, text), so they
 are modded exactly like Scenarios, but live in their own tab so players can mix them.

 Example: recolor a minigame background.
 1. Run the minigame in the emulator, open the palette viewer and note the colors.
 2. Locate the palette table in the ROM (search for the color bytes) and edit it
    in your copy of the ROM.
 3. Create my_minigame.ips / .bps against the original ROM.
 4. Create mods\Minigames\Recolored Minigame\ with:
        mod.ini      (copy from mods\Minigames\_template\mod.ini)
        my_minigame.ips
 5. Refresh List -> Minigames tab -> tick it -> Play With Mods.

 Tip: keep each mod small and focused (one scene or one asset set per mod) so mods
 conflict less and players can combine them.

--------------------------------------------------------------------------
 7. mod.ini REFERENCE
--------------------------------------------------------------------------
 [Mod]
 Name=Mod name                   shown in the list (defaults to the folder name)
 Author=Your name
 Version=1.0
 Description=What the mod changes (shown below the list when selected).
 Patch=part1.ips;part2.bps       optional; applied in order. If omitted, all .bps and
                                 .ips files in the folder are used.

 Lines starting with ";" or "#" are comments. Encoding: UTF-8 or ANSI.

--------------------------------------------------------------------------
 8. LIMITATIONS (IMPORTANT)
--------------------------------------------------------------------------
 - The game is a STATIC RECOMPILATION: the game CODE is fixed inside game\BluesAlphabetBook.exe.
   Mods only work when they change ROM DATA (graphics/tiles, palettes, maps, text, tables).
   Changes to game code in a patch have no effect.
 - A patch must not change the ROM size (1 MB). The loader recalculates the header checksums.
 - Overlapping mods overwrite each other; the last applied patch wins.
 - BPS patches must be made against the original ROM (the loader checks the source CRC).
 - Only the original USA ROM is accepted as the base.

--------------------------------------------------------------------------
 9. TROUBLESHOOTING
--------------------------------------------------------------------------
 "No ROM selected" / "This is not the expected ROM"
     Use the original, unmodified USA .gbc (SHA-256 above). Do not select a patched ROM.
 "game\BluesAlphabetBook.exe not found"
     You ran the loader outside the extracted folder. Extract the full package.
 "No mods found"
     Check the folder (mods\Characters, mods\Scenarios or mods\Minigames), that each mod
     has a patch, and click Refresh List.
 "Failed to apply mod ... the patch changes the ROM size"
     Your patch grew or shrank the ROM. Re-create it keeping exactly 1 MB.
 "BPS patch does not match this ROM"
     The patch was made against a different ROM. Create it against the original.
 The mod loads but nothing changes
     You probably edited code, not data. See Limitations.
 Game looks corrupted
     Untick mods one by one to find the culprit; check for overlapping edits.
 Antivirus warning
     The package contains unsigned executables; add an exception if you trust the source.

--------------------------------------------------------------------------
10. BUILDING FROM SOURCE
--------------------------------------------------------------------------
 Loader: source\loader\loader.c (+ gb_sha256.h, bps_patch.h, app.rc, app.manifest).
   With MinGW-w64:
     x86_64-w64-mingw32-windres app.rc -O coff -o app.res.o
     x86_64-w64-mingw32-gcc -O2 -municode -mwindows -o BluesAlphabetBookModLoader.exe \
         loader.c app.res.o -lcomctl32 -lcomdlg32 -lshell32 -lgdi32 -luser32
   Without MinGW (clang + lld): run source\loader\build_clang.sh
 Game: source\recompiled (CMake, see source\mingw.cmake).

--------------------------------------------------------------------------
11. NOTES / TESTING STATUS
--------------------------------------------------------------------------
 The in-game content of the USA release is already English; this English edition
 translates the loader interface, source comments/messages, folder names, templates
 and documentation.
 The original package was tested under Wine (Linux) with a virtual display: the game
 launched and rendered its opening scene with the original and a modified ROM.
 The Minigames tab build has not been tested on real Windows hardware yet. Audio was
 not tested. If something misbehaves, use BluesAlphabetBookModLoader_Classic.exe.
