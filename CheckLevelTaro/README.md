# CheckLevelTaro

This project has been done to fetch data from the web measuring the level of the local river Tarò (Certesa) in Meda-MB-Italy, and trigger an alarm when it gets to a critical threshold, possibly resulting in floods.
Main application is the activation during the night to wake up in case of emergencies (phone needs to be connected to the internet).

Many thanks to our fellow citizen that [publicly shares data from its meteo station](https://www.stefanocolombo.com/public/meteo/webtaro.php).

## Prerequisites
+ Download [Tasker](https://play.google.com/store/apps/details?id=net.dinglisch.android.taskerm&hl=it) and [AutoTools](https://play.google.com/store/search?q=autotools&c=apps&hl=it).
> [!WARNING]
> These tools are not free (around 5€) and need access and privileges to your phone to change system settings and run tasks.

## Usage
+ Create a Tasker Widget-v2 and place it on one of your Android desktops, and name it `CheckLevelTaroWidget`. It is best if size is 5x1.
+ Download [`CheckLevelTaro.prj.xml`](CheckLevelTaro.prj.xml) project file from this repository and import it in Tasker.
+ Manually run `CheckLevelTaro` task in Tasker/tasks page to render the widget for the first time.

Your widget will look like this:

<img src="CheckLevelTaroScreenshot.jpg" alt="CheckLevelTaroScreenshot" width="500"/>

The main part is composed by 3 banners:
+ ![](https://placehold.co/15x15/F1B7C6/F1B7C6.png) Data fetched from web: river water level [m], time when data was last updated, date when data was last updated.
+ ![](https://placehold.co/15x15/D1BCFD/D1BCFD.png) Data fetched from web: current rainfall [mm], today rainfall [mm], last 1h rainfall [mm], last 24h rainfall [mm].
+ ![](https://placehold.co/15x15/5F4D89/5F4D89.png) Water level threshold [m] currently set by the user to trigger the alarm (interactive: by clicking here you can set a new threshold value).

On the left, there are 3 icons:
+ ![](https://material-icons.github.io/material-icons/svg/autorenew/outline.svg) Refresh data manually.
  + ![](https://placehold.co/15x15/B6083D/B6083D.png) Always.
+ ![](https://material-icons.github.io/material-icons/svg/looks_5/outline.svg) Enable automatic data fetching every 5 minutes.
  + ![](https://placehold.co/15x15/B6083D/B6083D.png) Active.
  + ![](https://placehold.co/15x15/5F4D89/5F4D89.png) Inactive.
+ ![](https://material-icons.github.io/material-icons/svg/alarm/outline.svg) Enable audio alarm if current river level is higher than programmed threshold, and if automatic data fetching is active.
  + ![](https://placehold.co/15x15/B6083D/B6083D.png) Active.
  + ![](https://placehold.co/15x15/5F4D89/5F4D89.png) Inactive.

> [!IMPORTANT]
> Edit `AlarmON` task, and make sure that `Music Play` action is pointing to an available `.mp3` file in your phone.
>
> Alarm will trigger at maximum volume level even if the phone is in silent mode.

## Development notes
This is my first Tasker application so it might not be perfect, but it works.

It is composed by:
+ 1 Profile:
  + `CheckLevelTaro5minProfile`: every 5 min, it triggers the `CheckLevelTaro` task.
    + Icon ![](https://material-icons.github.io/material-icons/svg/looks_5/outline.svg) toggles this profile to be enabled via the `ToggleThisProfile` task.
+ 6 Tasks (each one is composed by smaller pieces, called _actions_):
  + `CheckLevelTaro`:
    + `AutoTools HTML Read`: Fetches from the web info about the latest river water level and time/date of the last measurement.
    + `Simple Match/Regex`: Extracts water level from data fetched with regex. Stored in variable `%level_level_mt_match`
    + `Simple Match/Regex`: Extracts water last measurement time from data fetched with regex.
    + `Simple Match/Regex`: Extracts water last measurement date from data fetched with regex.
    + `AutoTools HTML Read`: Fetches from the web info about all rainfall statistics.
    + `Simple Match/Regex`: Extracts rainfall quantities (array) from data fetched with regex.
    + `Widget v2`: Refresh widget on desktop, as previously described.
      + Column 1 - icon buttons:
        + ![](https://material-icons.github.io/material-icons/svg/autorenew/outline.svg): Interaction with `CheckLevelTaro` task.
        + ![](https://material-icons.github.io/material-icons/svg/looks_5/outline.svg): Interaction with `ToggleThisProfile` task.
        + ![](https://material-icons.github.io/material-icons/svg/alarm/outline.svg): Interaction with `ToggleAlarm` task.
      + Column 2 - informative banners:
        + River water level data visualization: No interaction.
        + Rainfall data visualization: No interaction.
        + River water level programmed level visualization: Interaction with `SetThreshold` task.
    + `Perform Task` - `AlarmON`: triggers alarm if ALL these conditions are met:
      + `%level_level_mt_match > %Level_th`.
      + `%ToggleAlarm==True`.
      + `CheckLevel.* in %PENABLED`.
        + `%PENABLED` is a Tasker variable that reports all enabled profiles.
  + `AlarmON`:
    + `Media Volume: 15`: Sets media volume to Android max level 15.
    + `Music Play`: Plays an `*.mp3` file present in your phone.
    + `Show Scene: PopupAlarm`: Shows a dialog with a text message about the river crossing the critical threshold.
      + Clicking on the text, the `AlarmOFF` task is run. 
  + `AlarmOFF`:
    + `Music Stop`: Stops the playback of the `*.mp3`.
    + `Media Volume: 3`: Restores media volume to an arbitrary level 3.
    + `Destroy Scene: PopupAlarm`: Close the popup previously opened.
  + `ToggleThisProfile`, bound to ![](https://material-icons.github.io/material-icons/svg/looks_5/outline.svg) icon click:
    + `Profile Status`: Toggles state of `CheckLevelTaro5min` profile.
    + `Wait 30ms`: Allows previous action to take effect.
    + `if/else/end`: Checks for `CheckLevel.* in %PENABLED` (current profile state).
      + `Variable set`: Change value of `%Enabled_data_var_col` variable to update ![](https://material-icons.github.io/material-icons/svg/looks_5/outline.svg) icon color according to current profile state.
    + `Perform Task` - `CheckLevelTaro`: Run main task and updates widget.
  + `ToggleAlarm`, bound to ![](https://material-icons.github.io/material-icons/svg/alarm/outline.svg) icon click:
    + `if/else/end`: Checks for `%ToggleAlarm` variable boolean state (alarm enabled).
      + `Variable set`: Toggles boolean value of `%ToggleAlarm` variable itself.
      + `Variable set`: Change value of `%Enabled_alarm_var_col` variable to update ![](https://material-icons.github.io/material-icons/svg/alarm/outline.svg) icon color according to current profile state.
      + `Perform Task` - `CheckLevelTaro`: Run main task and updates widget.
  + `SetThreshold`, bound to third banner ![](https://placehold.co/15x15/5F4D89/5F4D89.png) click:
    + `Show Scene: PopupInputTh`: Shows a dialog with an input text field where to set a float value for the new threshold to be stored in `%Level_th` variable. Clicking on the `》》》` text the scene is destroyed and the popup closes. 
+ 2 Scenes:
  + `PopupInputTh`: new screen/popup that allows you to set a new threshold to be checked against river level. It shows up when you click on the third banner ![](https://placehold.co/15x15/5F4D89/5F4D89.png).
    + Clicking on the `》》》` text the scene destroys itself and the popup closes.
  + `PopupAlarm`: new screen/popup that shows up whenever the river level crosses the programmed threshold, together with an audio alarm. It features a text message that close the popup when clicked.
    + Icon ![](https://material-icons.github.io/material-icons/svg/alarm/outline.svg) toggles this popup to be enabled.
      + This scene is triggered only if ![](https://material-icons.github.io/material-icons/svg/looks_5/outline.svg) is also enabled. It doesn't make sense to trigger the alarm on a manual update since the user is already interacting with the phone and aware of the result.
    + Clicking on the text, the scene destroys itself, the popup closes and the music stops.
+ 4 Variables:
  + `%Enabled_data_var_col` and `%Enabled_alarm_var_col` to store icon colors respectively for ![](https://material-icons.github.io/material-icons/svg/looks_5/outline.svg) and ![](https://material-icons.github.io/material-icons/svg/alarm/outline.svg).
  + `%Level_th` to store programmable threshold level.
  + `%ToggleAlarm` to store if alarm is enabled or not (no need to do this for automatic data parsing, since the `%PENABLED`(profiles enabled) Tasker variable can be used).
