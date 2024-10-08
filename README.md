# MS45.1-ESS-TS2
Custom tune for an ESS TS2 Supercharged E46 BMW 330i ZHP using 42lb injectors and 79mm pulley (MS45.1 DME)

***DISCLAIMER: This archive is provided as-is and without warranty.  Use it at your own risk. If you need tuning support, I highly recommend reaching out to a qualified tuner.***

Credit and thanks goes out to Frank Smith at TTFS (www.tuningtechfs.com) for his support, committment, and resources. Also huge thanks to bimmerlabs.com for providing a means to flash the files onto the car back when there weren't any. Without them, this wouldn't be possible.

>**Your MS45.1 DME must be flashed to HW version 7561382 (this is included in a number of ZB versions for MS45.1, such as ZB 7561520 for the ZHP models)**

This tune was developed on a 2003 BMW 330i ZHP with the following modifications:  
- MS45.1 flashed to HW version 7561382 (see Renovelo's site for list of MS45.1 ZB package versions that include HW 7561382: https://www.renovelo.com/bytetuner-vehicle-compatibility/ )  
- ESS Twin Screw Stage 2+ supercharger kit with 79mm pulley  
- Schrick 248/256 cams  
- Bosch 42lb "Green Giant" fuel injectors  
- Upgraded fuel pump  
- Catless headers  
- Removed SAP
- Removed downstream O2 sensors


Helpful links:  
http://www.tuningtechnfs.com - Frank Smith tuning services  
https://www.bimmerlabs.com/store - Buy a USB-OBDII cable  
https://www.renovelo.com/ - ByteTuner for tuning and logging, or ByteLogger just for logging, and good info in the forums  
https://www.ms4x.net/index.php?title=Siemens_MS45 - MS45.1 tuning wiki, feel free to contribute!  
https://github.com/terraphantm/MS45-Flasher - MS45 Flasher utility, can be used to flash by bin files onto your MS45.1 DME


Files included in this repo:  
330i-zhp-ts2-42lb-79mm_v***.bin - Partial 128k bin of the configuration space  
330i-zhp-ts2-42lb-79mm_v***.dme2 - Renovelo ByteTuner file  
330i-zhp-ts2-42lb-79mm_v***_full.bin - Full 1024k bin includes the program and configuration space, provided for posterity  
README.md - The file you are reading.  


## Description of tuning changes from stock

Learned variants need to be reset for the DME to change LV_VAR_SAP and LV_VAR_LSH_DOWN to 0 when it cannot detect the SAP or downstream O2 sensors after they've been removed from the car.  Learned variants are only reset by changing LC_AD_CLR_VAR to 1 and starting the car, then changing LC_AD_CLR_VAR back to 0.  

Changes for removed cats and downstream o2 sensors:  
- C_CONF_CAT changed to 0 to set exhaust configuration (2 upstream O2 sensors only, with cats)  
- Disabled cat heating:
   - 	Set IP_T_CH_TQ_ADD = IP_T_CH_TQ_ADD_WOUT_CAT
   - 	Set IP_T_CH_TQ_ADD_IS = IP_T_CH_TQ_ADD_IS_WOUT_CAT
   - 	Set IP_T_CH_N_SP_IS to 0s 
   - 	Set IP_T_CH_IVVT to 0s
   - 	Set ID_T_CH_TI to 0s (this is set to 0 in stock form)
   - 	Set LC_INH_TQ_ADD_CH to 1 Disable torque reserve request for cat heating
- Disable cat purge function, set C_N_MAX_CAT_PURGE to 100
- Force OBD cat readiness with LC_LS_CAT_DIAG_CDN_MAN_ACT
- Raised cat overheat limits to avoid unwanted lambda enrichment using IP_TEG_COP_SP and IP_TEG_COP_SP_LIM

Intake Manifold Model and torque tables:
- Adjust IP_EFF_VOL_SLOP_MAX and IP_EFF_VOL_SLOP_CAM_VO based on increased volume of air flow possible through manifold
- Adjust the mg/stk axis of IP_TQI_REF__N__MAF and IP_TQI_REF_MON
- Adjust the values of IP_MAF_SP based on the changes to the axis of IP_TQI_REF__N__MAF and IP_TQI_REF_MON
- Set alternator torque loss to -12 to compensate for supercharger friction losses which should aid drivability using C_TQ_LOSS_ALTER_LIH
- Added additional torque losses on startup to compensate for supercharger load in IP_TQFR_ST

Fueling:
- Impacts of different injectors (size, dwell, minimum injection time)
   - C_FAC_MFF_TI_STND_1 and C_FAC_MFF_TI_STND_2 is injector throughput measured by Ms/(mg/stk)
   - IP_TI_ADD_DLY injector dead times
   - C_TI_MIN and C_TI_MIN_THD should equal min injector inj time, with threshold being slightly higher than c_ti_min.
- Add a bit of fuel under heavier part-load scenarios to smooth transition to full load fueling using IP_LAMB_BAS_1 and IP_LAMB_BAS_2
- Add fuel correction above 5900 rpm due to maxed out maf IP_MFF_COR_1 and IP_MFF_COR_2
- Set full load fueling targets IP_LAMB_FL__N
- Added fuel to IP_LAMB_BAS_WUP_COR for more fuel on warmup
- Increased C_MAF_MFF_AD_MAX to ensure proper adaptations

Ignition timing:
- Dial back ignition timing in IP_IGA_BAS_RON_98__N__MAF & IP_IGA_BAS_RON_91__N__MAF 
- Dial back IP_IGA_BAS__N__MAF for good measure in case vanos is disabled or broke 
- Adjust temperature based timing retards to be more aggressive using IP_IGA_BAS_TEMP_COR__TCO__TIA (supercharger is going to be more susceptible to heat)

Impacts of removed DISA (variable intake manifold):
- Set IP_EFF_VOL_SLOP_CAM_VO_VIM = ip_eff_vol_slop_cam_vo
- Set IP_EFF_VOL_SLOP_CAM_IN_VIM = ip_eff_vol_slop_cam_in
- C_DET_INC_VIM = 0 to disable SES light from missing valve

Misc changes:
- Remove speed limiter by setting C_VS_MAX to 255
- Set max maf diag threshold C_MAF_MAX_DIAG
- Update IP_MAF_1_DIAG__N_32__TPS_AV with realistic maf values so TPS diags don’t trip
- Change C_OBD_STATE_2 to 101 (109 is stock).  101 will show SAP tests are supported in OBD2
- Adjust IP_FAC_TQ_REQ_DRIV to make throttle response a bit more linear and not so punchy for throttle management under aggressive driving scenarios
