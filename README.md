# Demon-Smart-Bed-Fans-Monitor-System-For-Voron-2.4-Chamber-Circulation-

This BED_FAN_MONITOR system is configured to be smart & adaptive, it monitors what the system is doing & also what you are doing during the print! The system knows if you pause or cancel the print, it knows if you change 
the Chamber Fan target, or if you change the Bed Fans speed! It even knows if the system changed things or if you did!

It has numerous methods of monitoring your print & settings & can react to them all fully autonomously, you can override the system with a click of a button at any point while printing, you can modify the high & low preset speeds used for the entire print live during the print! You can change the fan speeds while the Monitor is running directly with the BED_FAN_SET_SPEED macro or by using the BED_FANS slider in the GUI!

If you set the slider during a print to a value below 50% then that value is automatically stored for use as your LOW_SPEED setting. If you move the slider to any number over 50% it will be stored & used as your high speed setting.
This can be done at any point during actual printing, even when the BED_FANS are not running! This adjustment is reset after the print finishes, returning to file defaults.

INSTRUCTIONS:
For this macro to work you need....
A Voron 2.4 Printer (or similar!)
Some installed & correctly set up bed fans.
A printer with a simple chamber temp sensor or a temp controlled chamber cooling fan.
You will need to create a dummy output pin on a spare/unused pin on your mainboard.
This can be anything, even a pin on the EXP display socket/s if you're NOT using them.

This dummy pin is used as a manual override within Mainsail to disable/enable the bed fans at any point during print once it 
has finished the PRINT_START macro. The system will monitor the position of this switch during the print.
It is automatically reset once the current print has ended.

#####################################################################################################
Paste this into your printer.cfg file:
#####################################################################################################

[fan_generic Bed_Fans]
pin: *** <<<< Insert your own pin HERE!
max_power: 1.0
shutdown_speed: 0
kick_start_time: 0.5
off_below: 0.19

[output_pin DISABLE_BED_FANS]
pin: *** <<<< Insert your own pin HERE!

#####################################################################################################
Be sure to remove the hashes (#) & enter your chosen pin numbers or this will not work!
#####################################################################################################

CHAMBER TEMP SENSOR OR TEMP CONTROLLED CHAMBER COOLING FAN

You can use either, the macro's operation will be the same.
If you have only a chamber temp sensor & not a chamber fan you'll need to "ctrl+F" "temperature_fan chamber"
& replace all instances EXCEPT the two in the _BED_FANS_HELPER macro with "temperature_sensor Chamber_Temp"
Also make sure "variable_chamber_fan_enable" is set False!

You'll then need to go to your printer.cfg & set up the sensor there.

Be sure to call it: [temperature_sensor Chamber_Temp]
Then add the rest of the settings for your sensor there so it will work & the macro can read chamber temps.
See this link for help: https://www.klipper3d.org/Config_Reference.html#temperature-sensors

For a Chamber fan you need to call it [temperature_fan chamber]
Also make sure "variable_chamber_fan_enable" is set True!
Then add the rest of the settings for your sensor there so it will work & the macro can monitor chamber temps.
See this link for help: https://www.klipper3d.org/Config_Reference.html#temperature_fan

#####################################################################################################

This Macro set has been designed to be used with the Demon Essentials Voron 2.4 Macro pack
Downloading & installing the whole pack is recommended as it provides a fuller experience.

https://github.com/3DPrintDemon/Demon_Essentials_Voron_2.4_Edition

#####################################################################################################
To install the system:
#####################################################################################################
Place the file into your printer's config folder & add [include ./demon_bed_fans_v1.1.cfg] in your printer.cfg file.
Or as mentioned install the full Demon macro pack!
Create the required printer.cfg sections as described above.
Add your chosen pins to it.
Save & Restart.
You may need to PID tune the bed with the fans on if you get heating errors while in use.

#####################################################################################################
USAGE OPTION 1: Inside the Demon Essentials Macro Pack
#####################################################################################################
Its all set up, just set your sensors & pins in the printer.cfg
Then set your choices in the demon_user_settings.cfg file
You're done!

#####################################################################################################
USAGE OPTION 2: Stand-alone Macro - NOT used inside the Demon Essentials Macro Pack
#####################################################################################################
Set your sensors & pins in the printer.cfg as shown above.
Add the below command to anywhere in your start macro (after your bed temp has been set) where you 
want your macro to set the fans. This can be used more than once.

_BED_FAN_SET 

VERY IMPORTANT! Be sure to enable the Bed Fans Monitor at the very end of your start macro by
adding the below command to the last line of your start macro:

UPDATE_DELAYED_GCODE ID=_BED_FAN_MONITOR DURATION=1
RESPOND TYPE=COMMAND MSG="Bed Fans Monitor Active"

The monitor will do the rest during the print.

#####################################################################################################
Be sure to remove the hashes (#) & enter your chosen pin numbers or this will not work!
#####################################################################################################

#####################################################################################################
SETUP:
#####################################################################################################
There's 2 ways to this, if using the full Demon macro pack then these settings are set in the 
demon_user_settings.cfg file.

If using this as a stand-alone cfg use the variables in the _BED_FAN_VARS macro below.
DO NOT CHANGE the last 5 variables or you'll confuse the macro & it will NOT work correctly.

Set "chamber_threshold" to your minimum chamber printing temp for your filament.
This is the temp at which the Bed Fans will shift down from "high speed" to "low speed".
Set "chamber_fan" even if you dont have one to your maximum chamber printing temp for your filament. 
This is the temp at which the bed fans will turn off.
Set the "variable_high" for high speed setting to around 0.50-0.70 DO NOT SET 1!! Thats too much!
Set the "variable_low" for low speed setting to around 0.2-0.3, this is good.
Be sure to set "variable_enable" to True if you wish to use the system!! <<<<<<<<<<<<<<<<<< IMPORTANT!!
Set the "varibale_cool" to True if you wish to use the bed fans to cool the bed & chamber when finished
Also set the "variable_cool_temp" to the temperature to cool down to.

#####################################################################################################
TESTING:
#####################################################################################################
To test the one-time _BED_FAN_SET macro system:
In the Console paste: _BED_FAN_READ
It will return the default settings.
To enable the system paste in the Console: SET_GCODE_VARIABLE MACRO=_BED_FAN_VARS VARIABLE=enable VALUE=True
Set a bed target temp to begin heating - if you do NOT do this it will not work.
Then paste in: _BED_FAN_SET
This will activate the portion of the system used in the PRINT_START macro one time.
When up to temp &/or over temp run it again to see/test different outcomes.

#####################################################################################################

To test the _BED_FAN_MONITOR system:
Enable the system by making sure the "variable_enable" is set True
If using the full Demon Essentials Macro Pack set the above options in the demon_user_settings.cfg
Be sure you have UPDATE_DELAYED_GCODE ID=_BED_FAN_MONITOR DURATION=1 at the end of your start code.
If using the full Demon Essentials Macro Pack this is already done.
Start a quick test print & wait for your start macro to finish & for the printer to start the print proper. 
This is when the monitor comes online & it will modify the bed fans according to print conditions.

#####################################################################################################

MONITORED CONDITIONS:
Bed target temp
Bed Fans speed settings
Manual speed change
Live adjustment macro use
Live fans slider use
Chamber heat Threshold - low temp
Chamber cooling fan - high temp
Manual temperature change
Printer's current state
[output_pin DISABLE_BED_FANS]

##################################################################################################### 
PRINTING & MANUAL ADJUSTMENTS
#####################################################################################################
There are 2 ways to control this system, by using the BED_FANS_SET_SPEED macro or BED_FAN slider!
Adjusting the CHAMBER_FAN target during the print also effects the macro, as it is also monitored.





If cooling is on the printer will use the bed fans to gently cool the bed & chamber until the cooling temp is reached or 
you change the printer's state back to standby by resetting the print status in Mainsail web interface.

NOTE THE MONITOR ONLY RUNS AFTER YOUR START MACRO HAS FINISHED! 
This why we use the _BED_FAN_SET command in the Start macro itself.
