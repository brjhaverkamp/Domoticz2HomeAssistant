This file is intended for migrating historical data from Domoticz to Home assistant with the use of the “Import statistics” integration of Klaus Jandl (https://github.com/klausj1/homeassistant-statistics)
Currently this file only supports the Meter_Calendar, Multimeter_calendar and Temperature_Calendar from the domoticz database. But I think it’s straightforward to include others as well

Instructions
1) Download a copy of the Domoticz database , “domoticz.db” that you want to use to your pc or laptop. Use a sqlite tool like sqlite-browser to open it.
2) Find the DeviceID’s of the device you want to migrate the data from. You can get them  in Domoticz from the  devices list, or in the DeviceStatus table in the database. Note it down.
3) Domoticz stores its long term history in tables ending on “_Calendar", like Temperature_calendar, Multimeter_calendar, etc. Find the relevant tables for your device and export those to CSV
4) Copy the data from that table in the corresponding tab in this sheet
5) In the “Settings” tab, fill in the Home Assistant entity name and the corresponding Domoticz DeviceID, name of the table or sheet name where the data is stored(Meter_Calendar, Temperature_calendar, etc) in and the unit of the measurements (W, kWh, C, etc)
6) For metering entities like the gas meter or electricity meter, Home Assistant has an extra column called sum. This is basically the increase of the meter value since the first recording. It also compensates for intermittent resets of the meter. Find the oldest value of your entity recording in Home Assistant and fill this in for the “sum zero point”. 
6a) If you want to be a bit more precise on the sum zero point, go into the home Assistant database (e.g. with the sqlitebrowser add-on) and find the oldest recording for your entity in the “statistics” table. Note down the state and sum value. Then calculate  sum zero point = state-sum.
7) For some meters like the P1 smart utility meter in the Netherlands, there are multiple counter values for one domoticz device. Select the column/counter you need.
8) Set the appropriate scaling. Domoticz records the electricity usage in Wh, Home assistant in kWh. So the scaling is 1000. Domoticz records power in deciW(!!?!), Home Assistant in W, so the scaling is 10.
9) Check the Output table you need. Both “Output statesum” and “Output minmaxmean" are generated, but only one contains sensible data. Adjust your settings appropriately until your output table is OK.
10) Please double check it fhe table extends far enough down to show all your data. I made it for a maximum of 3000 data points, but maybe your history is longer than mine
11) Save the output table as a csv file.
12) Follow the instructions of the “Import Statistics” integration to read the data into home assistant

Notes:
I assume the _calendar files to be no longer than 9999 rows. If they are longer, adjust the formula in Settings.B12
Domoticz records its history once a day. Home assistant once per hour. I didn’t make any attempt at interpolating the data. There might be gaps in graphs.
I tested this only on the data I used, so there might be cases my sheet didn’t account for. Caveat Emptor/Buyer beware!
Press F9 to recalculate the sheet, if something looks wrong. The FilterRange macro doesn’t seem to update automatically, always
![image](https://github.com/brjhaverkamp/Domoticz2HomeAssistant/assets/3051118/edba10b0-889d-47e5-8c54-08095abe67fe)
