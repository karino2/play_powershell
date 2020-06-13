
- [このシリーズのコンセプト](concept.md)

シャープで始まる行はコメント、つまり単なる解説です。
`PS>`のあとにあるコマンドをタイプしてEnterしてみてください。



今日は何日？
```
PS> Get-Date

2020年6月13日 15:03:22

PS> $dt = Get-Date
PS> $dt.Year
2020
PS> $dt.Month
6

# ファイル名作りたいとかは以下
PS> > Get-Date -UFormat "%Y_%m_%d_%H%M_%S"
2020_06_13_1510_53
```

ひよりみろっくがもう一年の半分が終わったというが6月はまだ終わってないじゃないか！
という事でどれくらい終わったか確認。
上の続き（dtを使う)

```
PS> $dt - (Get-Date -Year 2020 -Month 1 -Day 1)


Days              : 163
Hours             : 23
Minutes           : 57
Seconds           : 46
Milliseconds      : 52
Ticks             : 141694660521630
TotalDays         : 163.998449677812
TotalHours        : 3935.9627922675
TotalMinutes      : 236157.76753605
TotalSeconds      : 14169466.052163
TotalMilliseconds : 14169466052.163

PS> 163/365
0.446575342465753 
```

まだ44.7%しか終わってないじゃないか。騙されるところだったぜ。


どんなマシン使ってるの？
```
PS> Get-CimInstance -Class Win32_Processor | fl


Caption           : Intel64 Family 6 Model 61 Stepping 4
DeviceID          : CPU0
Manufacturer      : GenuineIntel
MaxClockSpeed     : 1297
Name              : Intel(R) Processor 5Y70 CPU @ 1.10GHz
SocketDesignation : IC1



PS> Get-CimInstance -Class Win32_Processor | Select-Object -Property Name, Number*

Name                                  NumberOfCores NumberOfEnabledCore NumberOfLogicalProcessors
----                                  ------------- ------------------- -------------------------
Intel(R) Processor 5Y70 CPU @ 1.10GHz             2                   2                         4
# 2コア。

#メモリは？
PS> Get-CimInstance -Class Win32_PhysicalMemory | measure-object capacity -Sum


Count    : 3
Average  :
Sum      : 4311744512
Maximum  :
Minimum  :
Property : capacity



PS> 4311744512/1GB
4.015625

# 4GBだって。

# ハードディスクは？
PS> Get-PSDrive | Where Used
Get-PSDrive | ?{ $_.Used }

Name           Used (GB)     Free (GB) Provider      Root
----           ---------     --------- --------      ----
C                 303.05        161.52 FileSystem    C:\ 

# 合計何ギガ？
PS> Get-PSDrive | ?{ $_.Used } | %{ "$($_.Name): $(($_.Used+$_.Free)/1GB)GB" }
C: 464.578170776367GB

# 464GBだって。

# OSは？
PS> Get-CimInstance -Class Win32_OperatingSystem | fl


SystemDirectory : C:\WINDOWS\system32
Organization    :
BuildNumber     : 18362
RegisteredUser  : CF-RZ4
SerialNumber    : 00330-80000-00000-XXXXX
Version         : 10.0.18362

# Wn10、まぁそれはそう。

# 最近あてたWindows Update
PS> Get-CimInstance -Class Win32_QuickFixEngineering | sort -Descending -Property InstalledOn | head

Source        Description      HotFixID      InstalledBy          InstalledOn
------        -----------      --------      -----------          -----------
LETS-NOTE-RZ4 Security Update  KB4560959     NT AUTHORITY\SYSTEM  2020/06/12 0:00:00
LETS-NOTE-RZ4 Security Update  KB4561600     NT AUTHORITY\SYSTEM  2020/06/12 0:00:00
LETS-NOTE-RZ4 Update           KB4560960     NT AUTHORITY\SYSTEM  2020/06/12 0:00:00
LETS-NOTE-RZ4 Update           KB4552931     NT AUTHORITY\SYSTEM  2020/05/14 0:00:00
LETS-NOTE-RZ4 Security Update  KB4552152     NT AUTHORITY\SYSTEM  2020/04/27 0:00:00
LETS-NOTE-RZ4 Security Update  KB4541338     NT AUTHORITY\SYSTEM  2020/03/26 0:00:00
LETS-NOTE-RZ4 Security Update  KB4524244     NT AUTHORITY\SYSTEM  2020/02/14 0:00:00


# BIOSは？（ってプログラマしか興味持たないか）
PS> Get-CimInstance -Class Win32_BIOS


SMBIOSBIOSVersion : V1.00L14
Manufacturer      : American Megatrends Inc.
Name              : V1.00L14
SerialNumber      : 5CKSA3XXXX
Version           : MATBIO - 1072009


# どこ製？
PS> Get-CimInstance -Class Win32_ComputerSystem | fl


Domain              : WORKGROUP
Manufacturer        : Panasonic Corporation
Model               : CFRZ4-1
Name                : LETS-NOTE-RZ4
PrimaryOwnerName    : CF-RZ4
TotalPhysicalMemory : 4180226048

# いつからつけてる？
PS> Get-CimInstance -Class Win32_LogonSession | fl


AuthenticationPackage : CloudAP
LogonId               : 539420
LogonType             : 2
Name                  :
StartTime             : 20200612132107.002739+540
Status                :

AuthenticationPackage : CloudAP
LogonId               : 539165
LogonType             : 2
Name                  :
StartTime             : 20200612132106.987116+540

# 2020年6月12日から。
```

デスクトップに「苔鯖notepad」という名前のショートカットを作る。中身は単なるnotepad。

```
PS> $desktop = [Environment]::GetFolderPath("Desktop")
PS> $shell = new-object -ComObject WScript.Shell
PS> $shortcut = $shell.CreateShortcut("$desktop\苔鯖notepad.lnk")
PS> $shortcut.TargetPath = "C:\Windows\notepad.exe"
PS> $shortcut.Save()

# これでデスクトップに「苔鯖notepad」というショートカットが出来ていて、ダブルクリックするとnotepad.exeが立ち上がるはずです。
# 試したらあとは普通に手動で削除してください。
```

chromeがどれくらいメモリを使っているか。
```
PS> Get-Process | ?{ $_ -match "chrome" }

Handles  NPM(K)    PM(K)      WS(K)     CPU(s)     Id  SI ProcessName
-------  ------    -----      -----     ------     --  -- -----------
    283      19    46444      43444      29.64    316   1 chrome
    384      28    59048      72088      11.91    808   1 chrome
    233      16    19632      26776       3.11   1832   1 chrome
    329      20    43644      66788      12.23   3452   1 chrome
    322      21    44920      54024      59.36   3460   1 chrome
...

PS> Get-Process | ?{ $_ -match "chrome" } | measure-object WS -Sum

Count    : 34
Average  :
Sum      : 1676832768
Maximum  :
Minimum  :
Property : WS

# 1676832768って何ギガ？
PS> 1676832768/1GB
1.56167221069336

# 1.56GBだそうで。

# 以下みたいな書き方も出来る。
PS> 1676832768/(1024*1024*1024)
1.56167221069336
```

電卓を立ち上げて、電卓を殺す
```
# 電卓を立ち上げて
PS> calc

# 電卓を閉じる。calcで始まる他のプロセスがあったら閉じちゃうので注意。
PS> get-process -Name calc* | Stop-Process
```


ファイルのサイズの合計
```
PS> ls
Mode                LastWriteTime         Length Name
----                -------------         ------ ----
-a----       2020/02/10     17:42             33 .gitignore
-a----       2020/02/10     17:42           7987 index.md
-a----       2020/02/10     17:42          27676 linux_cmd.md
-a----       2020/02/10     17:42           1092 machine_admin.md
-a----       2020/02/10     17:42            254 other_cmd.md
-a----       2020/02/10     17:42          24968 shell_intro.md
-a----       2020/02/10     17:42            192 sync.sh
-a----       2020/02/10     17:42          41712 text_op.md
-a----       2020/02/10     17:42           1793 _config.yml

PS> ls | measure-object Length -Sum

Count    : 9
Average  :
Sum      : 105707
Maximum  :
Minimum  :
Property : Length
```



----

メモ（そのうち消す）

Documentsフォルダの下にpowershell_workというフォルダを作る。

```
> cd Documents
Documents> ls
...
Documents> mkdir powershell_work
Documents> cd powershell_work
Documents\powershell_work> explorer .
```

なお、Documentsを移動している奇特な人は以下だが、そういう人はこのドキュメントの対象じゃないでしょう。
```
> cd ([Environment]::GetFolderPath("Personal"))
```



エクスプローラでちゃんとDocuments下のpowershell_workというディレクトリが出来ているか確認。
`>`の左側はもっと長いはずだが面倒なので書かない。以下適当にさぼる。


```
> invoke-webrequest -Uri https://github.com/karino2/linux_intro_ml/archive/master.zip -OutFile linux_intro_ml.zip
> ls
...
Mode                LastWriteTime         Length Name
----                -------------         ------ ----
-a----       2020/06/12     23:01          38959 linux_intro_ml.zip
```

zipを展開。

タブ補完
```
> Expand-Arc[ここでタブ]
> Expand-Archive 
```

```
> Expand-Archive linux[ここでタブ]
> Expand-Archive .\linux_intro_ml.zip
> dir
...
Mode                LastWriteTime         Length Name
----                -------------         ------ ----
d-----       2020/06/12     23:04                linux_intro_ml
-a----       2020/06/12     23:01          38959 linux_intro_ml.zip
```

なんかあったらdir。Explorerでも確認。

linux_intro_mlの中にcdする。

```
> cd linu[ここでタブ]
> cd .\linux_intro_ml\
linux_intro_ml> ls
...
Mode                LastWriteTime         Length Name
----                -------------         ------ ----
d-----       2020/06/12     23:04                linux_intro_ml-master
```

あら、ようするに以下のようになっているな。

- powershell_work
   - linux_intro_ml
      - linux_intro_ml-master
         - ファイルいろいろ

explorerでも確認してみよう。


linux_intro_ml/linux_intro_ml-masterの中のものをlinux_intro_ml/に移動する。


```
linux_intro_ml> mv linu[ここでタブ]
linux_intro_ml> mv .\linux_intro_ml-master\
linux_intro_ml> mv .\linux_intro_ml-master\* ./
linux_intro_ml> ls
...
d-----       2020/06/12     23:11                linux_intro_ml-master
-a----       2020/02/10     17:42             33 .gitignore
-a----       2020/02/10     17:42           7987 index.md
-a----       2020/02/10     17:42          27676 linux_cmd.md
-a----       2020/02/10     17:42           1092 machine_admin.md
-a----       2020/02/10     17:42            254 other_cmd.md
-a----       2020/02/10     17:42          24968 shell_intro.md
-a----       2020/02/10     17:42            192 sync.sh
-a----       2020/02/10     17:42          41712 text_op.md
-a----       2020/02/10     17:42           1793 _config.yml
```

linux_intro_ml-masterディレクトリを削除しておこう。Explorerで消してもいいが、rmdirで消す

```
linux_intro_ml> rmdir linux[ここでタブ]
linux_intro_ml> rmdir .\linux_cmd.md
```

おや、違う物に補完されてしまった。そういう時はもう一回タブを押す。

```
linux_intro_ml> rmdir .\linux_cmd.md[ここでさらにタブ]
linux_intro_ml> rmdir .\linux_intro_ml-master\
linux_intro_ml> ls
...
Mode                LastWriteTime         Length Name
----                -------------         ------ ----
-a----       2020/02/10     17:42             33 .gitignore
-a----       2020/02/10     17:42           7987 index.md
-a----       2020/02/10     17:42          27676 linux_cmd.md
-a----       2020/02/10     17:42           1092 machine_admin.md
-a----       2020/02/10     17:42            254 other_cmd.md
-a----       2020/02/10     17:42          24968 shell_intro.md
-a----       2020/02/10     17:42            192 sync.sh
-a----       2020/02/10     17:42          41712 text_op.md
-a----       2020/02/10     17:42           1793 _config.yml
```

消えている事を目で確認。

