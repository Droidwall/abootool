# abootool #
By Roee Hay / Aleph Research, HCL Technologies

Simple fuzzer for discovering hidden fastboot gems.

**Modus Operandi**: Based on static knowledge (strings fetched from available bootloader images), dynamically fuzz for hidden fastboot OEM commands.

Appears in the USENIX WOOT '17 paper: [fastboot oem vuln: Android Bootloader Vulnerabilities in Vendor Customizations (USENIX WOOT `17)](https://www.usenix.org/conference/woot17/workshop-program/presentation/hay)


## Usage ##
1. Download your favourite OTAs/Factory images and populate with `abootool.py -a <dir>`.
`abootool.py -l` will then show you the populated images. 
2. Hook your device to the nearest USB port and run `abootool.py`. It will try to automatically discover the product or OEM. If it fails, it will fuzz with all of the available strings. 
One can force a specific OEM using `-e <oem>` parameter. 
At the end, the tool will print the discovered commands, discovered restricted commands, and commands which timed-out.

See `abootool.cfg` and `abootool.py -h` for advanced usage.

Explanation of progress bar:
```
[####......] [012923/030245/+01/R02/T01/E02] [CMD: foobar] [LAST: fdsaf]
  |             |      |    |   |    |   |        |         |
  |             |      |    |   |    |   |        |         `-> Last non-neg CMD
  |             |      |    |   |    |   |        `-----------> Last CMD
  |             |      |    |   |    |   `--------------------> # of USB error CMDs 
  |             |      |    |   |    `------------------------> # of timeout CMDs
  |             |      |    |   `-----------------------------> # of restricted CMDs
  |             |      |    `---------------------------------> # of positive CMDs
  |             |      `--------------------------------------> Total # of CMDs
  |             `---------------------------------------------> # of tested CMDS
  `-----------------------------------------------------------> % completed                    
```




## Dependencies ##
1. [python-adb](https://github.com/google/python-adb) 
2. [android-sdk-tools](https://developer.android.com/studio/releases/sdk-tools.html)
3. [Boot.img tools](https://forum.xda-developers.com/showthread.php?t=2319018) (only required for populating `fugu` images) 


## Tips ##

1. ADB-authorize your device for automatic-recovery from fastboot reboots.
2. If you had populated many images, running with `-g` would improve loading times.
3. If the device hangs, do not reset `abootool`, but rather reboot the device (into `fastboot`). `abootool` will then proceed automatically. 


## Tested on ##

Host environment:
Ubuntu 17.04 `zesty` 

Devices:
TBA

## Example ##

Running on Nexus 6P `angler`:
```terminal
$ ./abootool.py 
INFO: Welcome to abootool by Aleph Research, HCL technologies
INFO: fastboot connected to EN?????????
INFO: Device reported name = angler
INFO: Loading strings...
INFO: Loaded 54687 strings from 31 ABOOTs
[##########] [054687/054687/+15/R05/T00/E00] [CMD:      {@=] [LAST: uart dis]
INFO: Done.
INFO: Found 15 OEM commands
INFO:  1. device-info
INFO:  2. disable-charger-screen
INFO:  3. enable reduced-version
INFO:  4. enable-charger-screen
INFO:  5. frp-erase
INFO:  6. frp-unlock
INFO:  7. get-bsn
INFO:  8. get-imei1
INFO:  9. get-meid
INFO: 10. get-sn
INFO: 11. get_verify_boot_status
INFO: 12. hwdog certify begin
INFO: 13. off-mode-charge disable
INFO: 14. select-display-panel
INFO: 15. uart disable
INFO: Found 5 Restricted OEM commands
INFO:  1. disable-bp-tools
INFO:  2. disable-hw-factory
INFO:  3. enable-bp-tools
INFO:  4. enable-hw-factory
INFO:  5. ramdump disable
INFO: Found 0 USB Error OEM commands
INFO: Found 0 Timed-out OEM commands
````

List populated images:

```terminal
$ ./abootool.py  -l
INFO: BY OEM:
INFO: -------
INFO:              asus: 120               google:   1
INFO:               htc:  81               huawei:  33
INFO:                lg:  71             motorola:  74
INFO:           oneplus:  50              samsung:  45
INFO:              sony:   1               xiaomi:   7
INFO: 
INFO: BY DEVICE:
INFO: ----------
INFO:            angler:  31               athene:   2
INFO:        athene_amz:   1             bullhead:  31
INFO:         capricorn:   1               cedric:   1
INFO:               deb:   8              eva-l19:   1
INFO:             f5121:   1                  flo:   4
INFO:          flounder:  16         flounder_lte:  15
INFO:              fugu:  29               gemini:   2
INFO:          gra-ul00:   1           hammerhead:  27
INFO:        harpia_amz:   1           harpia_vzw:   1
INFO:          hennessy:   1                kenzo:   1
INFO:           lithium:   1             mantaray:  20
INFO:            marlin:  25                mysid:   3
INFO:          mysidspr:   2               nakasi:  11
INFO:           nakasig:   9                nikel:   1
INFO:             occam:  13             oneplus1:   1
INFO:          oneplus2:   9             oneplus3:  22
INFO:         oneplus3t:  16             oneplusx:   2
INFO: osprey_retbr_dstv:   1               potter:   1
INFO:             razor:  27               razorg:  32
INFO:          sailfish:  25                shamu:  66
INFO:              soju:   3                sojua:   3
INFO:             sojuk:   3                sojus:   3
INFO:             takju:   4             tungsten:   1
INFO:             yakju:   4
```
