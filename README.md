# Check Rain Tasks
Customizable rain notifications for tasker. Get rain notifications in the morning if it is going to rain during the day. Check continuously for rain and get notified. Know if tomorrow is going to rain.

Requirements
  * These tasks are for the android app [Tasker](http://tasker.dinglisch.net/)
  * You need an API key from [openweathermap.org](http://openweathermap.org/appid)
  * Lastly, these tasks are configured to launch [Forecastie](https://github.com/martykan/forecastie) by default, although it can be changed.

# Installation
The folder `check-rain` in this repo contains 2 folders, `profiles` and `tasks`. Copy those 2 folders to your android device, preferably to `/sdcard/Tasker`.
  1. Open tasker and then touch and hold on `TASKS` tab until a menu pops up.
  2. Select `import`.
  3. Browse the directory in which you put your `tasks` directory and open it.
  4. Import each profile one by one
  5. Repeat steps 1-5 for the `profiles` directory to the tab `PROFILES`.

Finally open tasker, go to `VARS` tab and create a variable named `%W_ow_key` with you openweathermap.org API key. There are 3 types of notifications: Morning, Continuous and Tomorrow. If you want to enable any of them, you have to enable each profile pair.
```
Check Rain <NOTIFICATION TYPE>
Check Rain <NOTIFICATION TYPE> Touch
```

# Configuration

## Location source

These tasks automatically use your last know location using either gps or network location. If you want to specify a fixed location, you have to specify a city and enable fixed city location for the notification type you want.

The specified city is common to all notification types. It's stored in the global variable `%W_loc_city` and follows the format `<city name>,<country ISO 3166 code>`, for example, `Boston,us`. For each type of notification you can set the location source to `city` to use the city specified in `%W_loc_city` or to `auto`(default).
  * For Morning notifications it is `%W_mor_lsrc`
  * For Continuous notifications it is `%W_con_lsrc`
  * For Tomorrow notifications it is `%W_tmw_lsrc`
  
## Notification titles and text

You can customize the notification title using global variables. The title of the notifications must be stored in global variables so that these notifications can be automatically removed and the profiles `Check Rain <NOTIFICATION TYPE> Touch` can open an app when those notifications are touched.
  * For Morning notifications it is `%W_mor_title`
  * For Continuous notifications it is `%W_con_title`
  * For Tomorrow notifications it is `%W_tmw_title`
  
The text of those notifications can be set modifying the the action `Notifiy Sound` inside each notification type task.

## Change launched app on notification touch

By default when a notification is touched Forecastie is launched. If you want to change the app go to `TASKS`, open the task `Clear Notify And Open Weather` and edit `Launch App`. If you already had Forecastie installed but it was not being launch by touching the notification, you can fix it by setting it again using the previous steps.

## Change the time notifications are delivered

Go to `PROFILES`, expand `Check Rain <NOTIFICATION TYPE>` and edit the trigger. For Morning and Tomorrow notifications set both the `from` and `to` fields to the hour you want the rain check to be triggered, make sure that the field `every` is disabled. For continuous notification types set `from` to the hour you want the continuous rain check to start and `to` to the hour you want the check to stop. The `every` field should be set to the desired frecuency of those checks. Note that the openweathermap.org forecasts are given for intervals of 3 hours, so the `every` field should be a multiple of 3 hours.

## Set how many hours ahead the tasks will check for rain

Go to `VARS`. For Morning notifications the tasks will check until midnight of the day they are run by default. Set the variable `%W_mor_hahead` to the number of hours into the future that the Morning rain check will care about. For example if the check is run at 7:00 in the morning and you set `%W_mor_hahead` to 16, it will check for rain from 7:00 to 23:00.

For Continuous notifications, the variable to set is `%W_con_hahead`, which is 6 by default.

## Beginning of the check interval for Tomorrow notifications

Tomorrow notifications will check rain for the following day starting at hour `%W_tmw_hstart`. That is, if set to 6, it will check for rain after 6:00 for the next day.

## Condition Continous notifications on current weather

Unconditional continuous notifications are great but they are repetitive on rainy days. That is why you can condition them on current weather using the global variable `%W_con_current`.
  * `on` checks current weather and only notifies if it's currently not raining but it will rain the future.
  * `off` disables conditional Continuous notifications on current weather. It will always notify if it rains in the future.
  * `smart`(default) not only calculates when rain will start, but also when it will end within the time window allowed by `%W_con_hahead`, which is updated each time the task is run. It won't notify you again if it has already notified you about the current rain. It's as if it tracked rain segments separated by non-rain segments.
  
Conditioning on current weather (`on`) has several drawbacks. If the first time you the task runs it is already raining, you won't get a notification. If it was originally forecasted that rain will start at 5:00 but on a later forecast rain is expected to start at 11:00 you will be notified twice. `smart` solves all of this problems.

There is however one requirement for `smart` to work. `%W_con_hahead` interval should be large enough to cover at least 2 checks. That is, if the Continuous profile is set to check every 3 hours, `%W_con_hahead` should be equal or larger than 6.

## Attributions

Data provided and subject to the terms provided by [OpenWeatherMap](openweathermap.org/terms) and [WUnderground](www.wunderground.com/weather/api/d/terms.html) weather services.
