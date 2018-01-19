# OctoPrint-Octolapse

** This plugin is in pre-alpha.  Install at your own risk!

## Description

Octolapse is made for making stabilized timelapses of your prints!  No more jerky movies, and no custom gcode/bash scripts required!  Every effort has been made to maintain print quality when using Octolapse, and it's extremely configurable.

Octolapse monitors the gcode sent to your printer and takes snapshots either at height increments/layer changes, after a period of time has elapsed, when certain gcodes are detected, or a combination of the three.  Octolapse monitors the position of each axis and attempts to determine the extruder state in order to detect the optimal time to take a snapshot (fully configurable).

Once it's time to take a snapshot, octolapse optionally ensures that the extruder is retracted, then makes a zhop, if possible, in order to reduce stringing and oozing.  It then pauses the print and sends commands to move the extruder and bed to the proper position. It then takes a snapshot and returns to the previous position and continues printing.

Once printing is finished, a timelapse is rendered according to your specifications.  Timelapses can be either Fixed Framerate, or Fixed Length.  Multiple formats are currently supported, but if you choose MP4, Octolapse will synchronize its output with the default timelapse plugin, making it easy and convenient to view, sort, and download your timelapses!  Currently Octolapse has no native file browser/viewer, so it is HIGHLY recommended that you use the MP4 format AND make sure that the 'Sync Output with the default Timelapse plugin' is enabled.  Both of these settings are used in all of the default settings.

Octolapse also allows you to animate your timelapses by providing a series of coordinates, either fixed or 'Bed Relative'.  Bed relative coordinates allow you to express a position relative to the print bed itself, where 0,0 is the origin and 100,100 is on the opposite corner.  This allows you to use/share your stabilizations on printers with different bed sizes.  Each axis can be controlled individually, or stabilization can be disabled entirely for an axis.

Octolapse also allows you to define camera settings that can be applied at the start of a print.  This allows you to ensure consistent images by controlling things like focus, zoom, pan, and exposure, making your snapshots as crisp and clear as possible.  You can also use multiple cameras, though currently Octolapse only allows one camera to be used at a time.  Additionally, any extra cameras will need to be configured prior to use.  Does anyone have instructions on how to add a camera to MJPEG Streamer?

Printer settings such as movement speed, retraction speed and length, detraction speed, etc, can be customized.  Octolapse will use these settings when retracting/detracting, lifting, moving, and detecting the extruder state.

## Installation

Can only be installed manually or via the bundled [Plugin Manager](https://github.com/foosel/OctoPrint/wiki/Plugin:-Plugin-Manager).

### Steps prior to installation
1.  Make sure your printer profile within the Octoprint setup is correctly configured, specifically the 'Print bed and build volume'.  If the bed size or custom box is not correctly configured, snapshots may not work.  For example, if you have 250mm configured on the X axis, but it is really 249.37 mm, snapshots taken from a relative position of 100 may fail because octolapse detects the extruder at an out-of-bounds position.  Note:  Octolapse will detect changes to these settings before every print.
2.  Make sure your FFMpeg path is correct in the Octoprint 'Timelapse Recordings' settings.  Octolapse will use this path to generate timelapses.  Note:  Octolapse will detect new ffmpeg changes before every print.
3.  Optionally, make sure your Webcam settings are correct, specifically the snapshot url and the bitrate (under timelapse recordings, advanced options).  You can change these options at any time via the octolapse 'Rendering' and 'Camera' profile settings

### Steps to install via the plugin manager
1.  Open and sign into Octoprint
2.  Click the settings icon (wrench) at the top of the page
3.  Select the 'Plugin Manager' link on the left side of the settings popup
4.  Click on the 'Get More..' button towards the bottom of the Plugin Manager page
5.  Paste the following link into the '... from URL' box and click 'Install':  https://github.com/FormerLurker/Octolapse/archive/master.zip
6.  After installation is complete, follow the prompts to restart Octoprint.
7.  Refresh your browser (usually ctrl+f5) to make sure your cache is reset.

### Steps after installation
1.  Make sure to select the appropriate printer profile (only Prusa Mk2S and MK2S with multi material have been tested).  The retraction length is of particular importance here.  Check your slicer settings to get the appropriate values for your prints.  Note that all axis speeds are in Millimeters per minute, while many slicers use MM/Sec.  To convert, just multiply MM/m by 60.
2.  Make sure your webcam is working by editing the default camera profile and clicking the 'Test Camera Snapshot' button.
3.  Run a short print in 'Test Mode' to make sure things are working without printing anything.  See 'Test Mode' below.

### Test Mode
Test mode must be activated BEFORE starting a print.  Changing this setting mid-print won't alter any current prints.

1.  Navigate to the Debug tab and select 'Test Mode'.  Note:  You shouldn't have to use any of the profiles marked 'Diagnostic' unless you suspect a bug.
2.  UNLOAD YOUR FILAMENT!  Even though I attempt to strip all extruder commands, there's a chance that I missed some cases.  Please let me know if you see any 'cold extrusion prevented' errors in the terminal window.  If you do, please let me know and send me your gcode!
3.  Select your gcode file and click print.

Notes:  Pay attention to your printer temps during the test print.  Everything should remain cool!  If not, please let me know, send me the gcode, and I'll try to remedy the situation.

## Usage

Once Octolapse is enabled (see settings below) start your print as usual and Octolapse will do it's magic.  You'll notice that the printer pauses itself occasionally, this is normal and occurs when Octolapse takes a snapshot.  There is sometimes a slight delay when pausing the print, but I'm currently working on that!

### Settings
To edit Octolapse settings, sign into Octoprint and click on the settings (wrench) icon at the top of the page.  Click on 'Octolapse Plugin' under 'Plugins' on the left-hand side.  You should now see the Octolapse setup screen.  You can disable or enable octolapse by checking 'Enable Octolapse'.  You can also restore all of the default settings here by clicking the 'restore all default settings' button.

You can add/edit/delete/copy profiles on any of the tabs.  You can set the current profile by clicking the Star icon.  The current profile will be in bold with a star icon in front of it.

Octolapse comes with several useful presets that should accommodate typical use.

Important Notes:  Most setting changes will only affect the next print.  Active prints are only affected by the logging settings inside the debug profile, and by the 'Apply Settings' button in the Camera Profile Add/Edit screen.  All other changes will take place during your next print, though while we're still in Alpha mode I don't suggest you do any massive settings changes during a print!

#### Printer
Here you can configure retraction length, speed, the printer snapshot command, as well as other settings.  The retraction length setting is especially important, since it's used to detect retraction and zhop.  Also of note is the Printer position confirmation tolerance.  Positions can be off by 0.0051MM on my MK2S due to mechanical constraints.  It can be difficult to tell what this setting should be, but if you have problems I would not go too much over 0.011 MM here.

#### Stabilization
Here you can control the position of the bed and extruder before each snapshot.  Positions can be fixed or relative.  Fixed coordinates are just like any absolute printer coordinates.  If your print bed is 200mmx200mm, coordinate 100,100 would represent the center of the bed.  When using relative coordinates, the center of the bed is at 50,50 and 0,0 is at the origin (home position).  100,100 would be the corner opposite the origin.

You can also provide a stabilization path.  In this case each point in the path is used for each subsequent snapshot.  The points can be either fixed or bed relative coordinates.  Once each coordinate is used, the final coordinate will be used for each snapshot.  Selecting the ‘Loop’ option will cause the path to start over.  You can also invert the loop so that once it reaches the end the path will continue in reverse.  Take a look at the ‘animated’ stabilizations for examples.

#### Rendering
These profiles determine how your series of snapshots will be turned into a timelapse.  You can set the timelapse to have a fixed length (say 5 seconds), or use a constant FPS.  The default output is MP4, which is required in order to (optionally) synchronize with the default timelapse plugin.  When synchronizing with the default plugin, your timelapses will appear in the native timelapse plugin tab after rendering is complete.

#### Camera
Here you can configure and fine tune your camera.  Octolapse allows you to control the camera settings (if supported by your camera and streamer) before each print, giving you complete control of how your snapshots look.

#### Debug

#####Test Mode
If you would like to test out a timelapse without actually printing anything, use the 'Test Mode' settings.  Test mode will only work if you select the option before starting a print.  This prevents the extruder from moving and suppresses any temperature setting commands so that you can quickly and easily make sure your stabilizations are working according to plan.

Even though I attempt to strip all extruder and temperature commands, there's a chance that I missed some cases. Please let me know if you see any 'cold extrusion prevented' errors in the terminal window or if either your bed, extruder or enclosure heats up during a test print. If you do, please let me know and send me your gcode!

For safety's sake, even when using test mode DO NOT LEAVE YOUR PRINTER UNATTENDED!  This program is in alpha and hasn't been tested with all printers/slicers/custom gcode, so bad things could happen.

#####Logging Settings
If you are having any issues please select one of the existing diagnostic profiles to turn on octoprint logging.  You can find the log in the main settings page on the left hand side under (Logs).  The log file for Octoprint is named plugin_octolapse.log.  The log file is useful to help me track down issues, so make sure you download a copy after any problems.

You can change the logging settings or default debug profile at any time, and the logging settings will take effect immediately.  The only setting in debug that will NOT take effect until the next print is 'Test Mode'.

Please note that the log file can grow quickly and might affect performance, so only use it when necessary.
## Known issues

### Restore All Default Settings
If you use the 'Restore All Default Settings' button, make sure you reboot octoprint.  I'm working on this bug.

### Original Prusa Firmware 3.0.12

If you are using an Original Prusa printer, there is a [known issue](https://github.com/prusa3d/Prusa-Firmware/issues/331) in the firmware 3.0.12 (linear advance update).  Here is the relevent workaround:

Note from [foosel](https://github.com/foosel): There does exist a workaround in OctoPrint for this (since it's happened in the past with other firmware variants), users can tell OctoPrint to "simulate" an ok after resends by enabling Settings > Serial communication > Advanced options > Simulate an additional ok for resend requests, but that has to be done manually.

Unfortunately this workaround may cause some print quality issues during the resend since I had some seemingly random overextursion in a test print after implementing the workaround.  I've not verified that it's due to the workaround, but I will try to do this soon.  I expect this bug to be fixed in the next firmware release.

## Notes about Octolapse
I got the idea for octolapse when I attempted to manually make a [stabilized timelapse](https://youtu.be/xZlP4vpAKNc) by hand editing my gcode files.  To accomplish this I used the excellent and simple [Gcode System Commands](https://github.com/kantlivelong/OctoPrint-GCodeSystemCommands) plugin.  The timelapse worked great, but it required a lot of effort, which I didn't want to put in every time.  I received several requests for instructions on how to create a stabilized timelapse, but decided to give plugin development a go.  I've never done one before (or programmed python, or knockout, or anything open source either), but figured I could contribute something good to the community.  This is my gift to all of the makers out there who have contributed your time and effort to the community!

