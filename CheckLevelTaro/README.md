# CheckLevelTaro

This project has been done to parse data from the web about the level of the local river Tarò in Meda-MB-Italy, and trigger an alarm when it get to a critical threshold, possibly resulting in floods.
Main application is the activation during the night to wake up in case of emergencies (phone needs to be connected to the internet).

Many thanks to our fellow citizen that [publicly shares data from its meteo station](https://www.stefanocolombo.com/public/meteo/webtaro.php).

## Prerequisites
+ Download [Tasker](https://play.google.com/store/apps/details?id=net.dinglisch.android.taskerm&hl=it) and [AutoTools](https://play.google.com/store/search?q=autotools&c=apps&hl=it).
> [!WARNING]
> These tools need access and privileges to your phone to change system settings and run tasks.

## Usage
+ Create a Tasker Widget-v2 and place it on one of your Android desktops, and name it `CheckLevelTaroWidget`. Best if size is 5x1.
+ Download `CheckLevelTaro.prj.xml` project file from this repository and import it in Tasker.
+ Manually run `CheckLevelTaro` task in the tasks page to render the widget for the first time.

Your widget will look like this:

![CheckLevelTaroWidgetScreenshot]()

> [!IMPORTANT]
> Edit AlarmON task, and make sure that `Music Play` action is pointing to an available `.mp3` file in your phone.
