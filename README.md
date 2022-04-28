# MS45.1-ESS-TS2
Custom tune for an ESS TS2+ kit with 42lb injectors and 79mm pulley on a MS45.1 DME 

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


Files included in this repo:  
330i-zhp-ts2-42lb-79mm_v250.bin - Partial 128k bin of the configuration space, provided for posterity  
330i-zhp-ts2-42lb-79mm_v250_full.bin - Full 1024k bin includes the program and configuration space, provided for posterity  
README.md - The file you are reading.  


## Description of tuning changes from stock

Learned variants need to be reset for the DME to change LV_SAP and LV_LSH_DOWN to 0 when it cannot detect the SAP or downstream O2 sensors afte they've been removed from the car.  Learned variants are reset by changing LC_AD_CLR_VAR to 1 and starting the car, then changing LC_AD_CLR_VAR back to 0.  

Changes for removed cats:  
- C_CONF_CAT changed to 0 to set exhaust confiuration (2 upstream O2 sensors only, with cats)  
- Disabled cat heating:
   - 	Set IP_T_CH_TQ_ADD = IP_T_CH_TQ_ADD_WOUT_CAT
   - 	Set IP_T_CH_TQ_ADD_IS = IP_T_CH_TQ_ADD_IS_WOUT_CAT
   - 	Set IP_T_CH_N_SP_IS to 0s 
   - 	Set IP_T_CH_IVVT to 0s
   - 	Set ID_T_CH_TI to 0s (this is set to 0 in stock form)
   - 	Set LC_INH_TQ_ADD_CH to 1 Disable torque reserve request for cat heating
- Disable cat purge function, set C_N_MAX_CAT_PURGE to 100
- Force OBD cat readiness with LC_LS_CAT_DIAG_CDN_MAN_ACT

Intake Manifold Model and torque tables:
- Adjust IP_EFF_VOL_SLOP_MAX and IP_EFF_VOL_SLOP_CAM_VO based on increased volume of air flow possible through manifold
- Adjust the mg/stk axis of IP_TQI_REF__N__MAF and IP_TQI_REF_MON
- Adjust the values of IP_MAF_SP based on the changes to the axis of ip_tqi_ref__n__maf and ip_tqi_ref_mon

Fueling:
- Impacts of different injectors (size, dwell, minimum injection time)
   - C_FAC_MFF_TI_STND_1 and C_FAC_MFF_TI_STND_2 is injector throughput measured by Ms/(mg/stk)
   - IP_TI_ADD_DLY injector dead times
   - C_TI_MIN and C_TI_MIN_THD should equal min injector inj time, with threshold being slightly higher than c_ti_min.
- Add a bit of fuel under heavier part-load scenarios to smooth transition to full load fueling using IP_LAMB_BAS_1 and IP_LAMB_BAS_2
- Add fuel correction above 5900 rpm due to maxed out maf IP_MFF_COR_1 and IP_MFF_COR_2
- Set full load fueling targets IP_LAMB_FL__N
- Add fuel during cold starts to compensate for extra load of the supercharger
   - Set IP_FAC_TI_TCO_WUP set to 1 to eliminate post-lambda corrections
   - Added fuel to IP_LAMB_BAS_WUP_COR for more fuel on warmup
   - Reduce idle warmup reductions IP_FAC_LAMB_WUP_IS

Ignition timing:
- Dial back ignition timing in IP_IGA_BAS_RON_98__N__MAF & IP_IGA_BAS_RON_91__N__MAF 
- Dial back IP_IGA_BAS__N__MAF for good measure in case vanos is disabled or broke 
- Adjust temperature effects on timing using IP_IGA_BAS_TEMP_COR__TCO__TIA (supercharger is going to generate more heat than a N/A motor)

Impacts of removed DISA (variable intake manifold):
- Set ip_eff_vol_slop_cam_vo_vim = ip_eff_vol_slop_cam_vo
- Set ip_eff_vol_slop_cam_in_vim = ip_eff_vol_slop_cam_in
- c_det_inc_vim = 0 to disable SES light from missing valve

Misc changes:
- Remove speed limiter by setting C_VS_MAX to 255
- Set max maf diag threshold C_MAF_MAX_DIAG
- Update IP_MAF_1_DIAG__N_32__TPS_AV with realistic maf values so TPS diags don’t trip
- Change C_OBD_STATE_2 to 101 (109 is stock).  101 will show SAP tests are supported in OBD2
