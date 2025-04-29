#crash #ios 
1. Use atos (Address to Symbol)
```bash
atos -arch <BinaryArchitecture> -o <PathToDSYMFile>/Contents/Resources/DWARF/<BinaryName> -l <LoadAddress> <AddressesToSymbolicate>
```

We can find arch, LoadAddress, AddressesToSymbolicate in .ips file.
![img?tid=%22c_qsh1GPaLWg9D7x2_UjMPrh5ruZc2x5cid9L2eBS79zo-VXSFUAj5AMuLTD_s6Ue_EwpGPhPBC7_GpW%22&linkId=%2212gCgjEBAcKiUrD2aIB0lVNtZWPYyNQn%22](https://office.synology.com/oo/file/1003299_3L196NFAJD7VT164VQ94IE9GV0.slide/ACFG0BE7Q90LP76S8D9V9PQ6D0/img?tid=%22c_qsh1GPaLWg9D7x2_UjMPrh5ruZc2x5cid9L2eBS79zo-VXSFUAj5AMuLTD_s6Ue_EwpGPhPBC7_GpW%22&linkId=%2212gCgjEBAcKiUrD2aIB0lVNtZWPYyNQn%22)
![](https://office.synology.com/oo/file/1003299_3L196NFAJD7VT164VQ94IE9GV0.slide/LDM2CRB3KP7PJ5FT4DE830FPSG/img?tid=%22c_qsh1GPaLWg9D7x2_UjMPrh5ruZc2x5cid9L2eBS79zo-VXSFUAj5AMuLTD_s6Ue_EwpGPhPBC7_GpW%22&linkId=%2212gCgjEBAcKiUrD2aIB0lVNtZWPYyNQn%22)![](https://office.synology.com/oo/file/1003299_3L196NFAJD7VT164VQ94IE9GV0.slide/MGIU8I5IO95PRFFBT11EB0ERV4/img?tid=%22c_qsh1GPaLWg9D7x2_UjMPrh5ruZc2x5cid9L2eBS79zo-VXSFUAj5AMuLTD_s6Ue_EwpGPhPBC7_GpW%22&linkId=%2212gCgjEBAcKiUrD2aIB0lVNtZWPYyNQn%22)
Find the arch, LoadAddress, and AddressesToSymbolicate in crash report which is 0x100bcc000 and 0x100c2c058 in the above picture.
```bash
atos -arch arm64 -o iOS-BeeFiles_Distribution.app.dSYM/BeeFiles.app.dSYM/Contents/Resources/DWARF/BeeFiles -l 0x100bcc000 0x100c2c058  
```
the last 6th command shows as following:
```bash
> -[FileTableViewCell reloadTable] (in BeeFiles) (FileTableViewCell.m:85)
```


2. CrashSymbolicator.py
This python program can be found in Xcode, using the following command:
```
find /Applications/Xcode.app -name CrashSymbolicator.py -type f

Output:
/Applications/Xcode.app/Contents/SharedFrameworks/CoreSymbolicationDT.framework/Versions/A/Resources/CrashSymbolicator.py
```
Usage:
```
python3 $(CrashSymbolicator.py path) -d xxx.dSYM -p xxx.ips > result.txt
```

| Before                            | After                             |
| --------------------------------- | --------------------------------- |
| ![[截圖 2025-04-29 上午11.55.18.png]] | ![[截圖 2025-04-29 上午11.56.07.png]] |
3. symbolicatecrash
> Note: Xcode 13.3 has deprecated this method.

Command:
```
find /Applications/Xcode.app -name symbolicatecrash -type f
```

```
./symbolicatecrash $(ips path) <PathToDSYMFile>/Contents/Resources/DWARF/<BinaryName> > result.txt
```

![](https://office.synology.com/oo/file/1003299_3L196NFAJD7VT164VQ94IE9GV0.slide/VO1VDTE7BH48P0R3ISG29O3E8O/img?tid=%22c_qsh1GPaLWg9D7x2_UjMPrh5ruZc2x5cid9L2eBS79zo-VXSFUAj5AMuLTD_s6Ue_EwpGPhPBC7_GpW%22&linkId=%2212gCgjEBAcKiUrD2aIB0lVNtZWPYyNQn%22)