# weewx-sunrainduration
## Evaluates solar radiation and rain and thus generates the corresponding duration

##### sunduration based on https://github.com/Jterrettaz/sunduration/blob/master/sunduration.py
```
   
    sunshine_time = Value when sunshine duration is recorded in W/m²
    sunshine_time = Value when sunshine duration is recorded in W/m²
    sunshineDur   = Sunshine duration value in the archive interval in seconds
    rainDur       = rain duration value in the archive interval in seconds
    hailDur       = rain duration value (Ecowitt-Piezo) in the archive interval in seconds
    sunshineDur_2 = Sunshine duration value in the archive interval in seconds for 2. DAVIS station (e.g. live or console)
    rainDur_2     = Rain duration value in the archive interval in seconds for 2. DAVIS station (e.g. live or console)

```


```
   
    weewx.conf:


[StdReport]
 [[Defaults]]
  [[[Units]]]
   [[[[Groups]]]]
       group_deltatime = hour
  
[StdWXCalculate]
     [[WXXTypes]]
         [[[maxSolarRad]]]
             algorithm = rs
             atc = 0.9

[RadiationDays]
    min_sunshine = 120    # Entry of extension radiationhours.py, if is installed (= limit value)
    sunshine_coeff = 0.91 # Factor from which value sunshine is counted - the higher the later
    sunshine_min = 18     # below this value (W/m²), sunshine is not taken into account.
    sunshine_loop = 1     # use for sunshine loop packet (or archive: sunshine_loop = 0)
    rainDur_loop = 0      # use for rain duration loop packet - default is not  
    hailDur_loop = 0      # use for piezo-rain duration loop packet - default is not
    sunshine_log = 0      # should not be logged when sunshine is recorded
    rainDur_log = 0       # no logging for rain duration
    hailDur_log = 0       # no logging for piezo-rain duration
    rain2 = 0             # no rain2 available (Davis Live, Davis Console)
    sunshine2 = 0         # no shunhine2 available (Davis Live, Davis Console) 
    sunshine2_loop = 1
    rainDur2_loop = 0
    sunshine2_log = 0
    rainDur2_log = 0

The "atc = 0.9" and "sunshine_coeff = 0.91" have to be adjusted for your own location.
With this adjustment, the sunshine duration values are very accurate and also work for other stations (e.g. Ecowitt), 
not just VantagePro.

```
   
    add_sunrain.sh: Weewx (v4.5.0 to V4.10.2)
  
     #!/bin/bash
     sudo echo "y" | wee_database --config=/etc/weewx/weewx.conf --add-column=sunshine_time --type=REAL
     sudo echo "y" | wee_database --config=/etc/weewx/weewx.conf --add-column=sunshineDur --type=REAL
     sudo echo "y" | wee_database --config=/etc/weewx/weewx.conf --add-column=rainDur --type=REAL
     sudo echo "y" | wee_database --config=/etc/weewx/weewx.conf --add-column=hailDur --type=REAL
     # only for second station (like VantagePro and VantageVUE)
     sudo echo "y" | wee_database --config=/etc/weewx/weewx.conf --add-column=sunshineDur_2 --type=REAL
     sudo echo "y" | wee_database --config=/etc/weewx/weewx.conf --add-column=rainDur_2 --type=REAL

     Weewx V. 5.0 or newer :
     #sudo echo "y" | weectl database add-column sunshine_time
     weectl database add-column sunshine_time --type=REAL --config=/etc/weewx/weewx.conf -y
 
```
   
    extension.py:
  
     import weewx.units
     weewx.units.obs_group_dict['sunshine_time'] = 'group_radiation'
     weewx.units.obs_group_dict['hailDur'] = 'group_deltatime'
     weewx.units.obs_group_dict['sunshineDur_2'] = 'group_deltatime'
     weewx.units.obs_group_dict['rainDur_2'] = 'group_deltatime'

```

![image](https://github.com/WernerKr/weewx-sunrainduration/assets/93549501/fc3d2e26-f57e-4a0f-95a7-8d49c52d8d11)

![image](https://github.com/WernerKr/weewx-sunrainduration/assets/93549501/b562ed48-a41b-427c-b545-54259dd87285)

![image](https://github.com/WernerKr/weewx-sunrainduration/assets/93549501/bfe4adc4-9b8a-454e-827c-5f96c7b69bf9)

![image](https://github.com/WernerKr/weewx-sunrainduration/assets/93549501/0abe03aa-a85f-437b-8eb7-9181a737760a)
