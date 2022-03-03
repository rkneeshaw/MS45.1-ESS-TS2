# MS45.1-ESS-TS2
Custom tune for an ESS TS2 kit with 42lb injectors and 79mm pulley on a MS45.1 DME

**DISCLAIMER: This archive is provided as-is and without warranty.  Use it at your own risk. If you need tuning support, I highly recommend reaching out to a qualified tuner.

Credit and thanks goes out to Frank Smith at TTFS (www.tuningtechfs.com) for his support, committment, and resources
Also huge thanks to bimmerlabs.com for providing a means to flash the files onto the car back when there weren't any.  
Without them, this wouldn't be possible.

Technical Requirements:
A laptop with a functioning copy of BMW Tools 2.12 or later (Including WinKFP and INPA)
Compatible OBDII USB cable, available online from many sources including bimmergeeks and bimmerlabs.com
And most importantly:
**** Your MS45.1 DME must be flashed to HW version 7561382 (this is included in a number of ZB versions for MS45.1, such as ZB 7561520 for the ZHP models) **

Strongly recommended:
A copy of Renovelo ByteLogger for data logging after the tune is applied to ensure AFR and timing are looking good.
Or if you want to make your own tweaks to the tune afterwards, try a copy of Renovelo ByteTuner, it can both log and you can change any of these tuning parameters yourself. 


This tune was developed on a vehicle with the following modifications that directly influenced the values used:
MS45.1 flashed to HW version 7561382 (see Renovelo's site for list of MS45.1 ZB package versions that include HW 7561382: https://www.renovelo.com/bytetuner-vehicle-compatibility/ )
ESS Twin Screw Stage 2 supercharger kit with 79mm pulley
Bosch 42lb "Green Giant" fuel injectors
Upgraded fuel pump to keep up with larger injectors
Catless headers


Helpful links:
http://www.tuningtechnfs.com - Frank Smith tuning services
https://www.bimmerlabs.com/store - Buy a USB-OBDII cable
https://www.bimmerlabs.com/help - Configure WinKFP for efficient flashing
https://www.renovelo.com/ - ByteTuner for tuning and logging, or ByteLogger just for logging, and good info in the forums
https://www.ms4x.net/index.php?title=Siemens_MS45 - MS45.1 tuning wiki, feel free to contribute!


Files included in this repo:
330i-zhp-ts2-42lb-79mm_v250.bin - Partial 128k bin of the configuration space, provided for posterity
330i-zhp-ts2-42lb-79mm_v250_full.bin - Full 1024k bin includes the program and configuration space, provided for posterity
README.md - The file you are reading.



APPENDIX

Actual tuning changes from stock:
Adjusted injector sizing (c_ti_as, c_ti_add_dly)
Slight timing retard at higher loads (ip_iga_bas_ron_91__n__maf, ip_iga_bas_ron_98__n__maf)
Adjusted intake manifold model (ip_eff_vol_slop_cam_vo, ip_eff_vol_slop_cam_vo_vim, ip_eff_vol_slop_max)
Increased mg/stk load scaling in torque reference maps (ip_tqi_ref__n__maf, ip_tqi_ref_mon, ip_maf_sp)
Increased base fuel tables (ip_mff_cor_1 and ip_mff_cor_2) above 6k RPM to add fuel due to MAF maxing out at 5900 RPM and to improve WOT long term trims 
Adjusted full load fueling (ip_lamb_fl__n)
Adjusted cold start fueling (ip_fac_ti_tco_wup)
Increased idle speed to 850 (8 maps, too lazy to list them)
Cat and rear O2 sensor delete:
Disabled post-cat O2 sensor heating function (lc_lsh_down_1, lc_lsh_down_2)
   Disabled post-cat O2 sensor diagnostics (lc_inh_ls_down_man_deac)
   Disabled cat efficiency diagnostics (lc_inh_cat_diag_man_deac)
   Removed cat warmup cycle on startup (ip_t_ch_tq_add, ip_t_ch_tq_add_is)
Disabled SAP monitoring (ip_t_sa_off_0,  lc_inh_sa_man_deac, lc_inh_safm_man_deac, lc_sap_det_di)
Removed speed limiter (c_vs_max)
Anti debounce counter for VIM changed to 0 to disable DISA DTC (c_det_inc_vim)
Increased min MAF diagnostic threshold (c_maf_max_diag)
Increased values in ip_maf_1_diag__n__tps_av to prevent DTC for expected maf flow during long highway cruises
Increased fan speed in final stages (ip_n_ecf)
