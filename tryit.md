解説をする前に、そもそもPowerShellでどんな事が出来るのか？というのを体験してみましょう。

シャープで始まる行はコメント、つまり単なる解説です。
`PS>`のあとにあるコマンドをタイプしてEnterしてみてください。

立ち上げ方。
Windowsキー+Q したあと 「powershell」と打ってenter。

```
# 1から15まで足す
PS> 1..15 | Measure-Object -Sum

Count    : 15
Average  :
Sum      : 120
Maximum  :
Minimum  :
Property :

# 00から15までの連番のファイル名を出力する（今回はただプリントするだけ）
PS> 0..15 | %{ "mostodon_cloud_{0:00d}.txt" -f $_ }
mostodon_cloud_00d.txt
mostodon_cloud_01d.txt
mostodon_cloud_02d.txt
mostodon_cloud_03d.txt
mostodon_cloud_04d.txt
mostodon_cloud_05d.txt
mostodon_cloud_06d.txt
mostodon_cloud_07d.txt
mostodon_cloud_08d.txt
mostodon_cloud_09d.txt
mostodon_cloud_10d.txt
mostodon_cloud_11d.txt
mostodon_cloud_12d.txt
mostodon_cloud_13d.txt
mostodon_cloud_14d.txt
mostodon_cloud_15d.txt

# 1234は16進数ではいくつ？（プログラマしか知りたくならないか）
PS> "0x{0:X}" -f 1234
0x4D2

```


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

苔鯖のLTLを6個見る。
```
PS> Invoke-WebRequest "https://mostodon.cloud/api/v1/timelines/public?local=1&limit=6" | %{ $_.Content } | ConvertFrom-Json | %{ $names = $_.account.username; $conts = $_.content; 0..($names.Length-1) | %{ "$($names[$_]): $($conts[$_])"} }
raito: <p>微妙ですね</p>
raito: <p>ファナチカルの高級ガチャでSaints Row4出てきた</p>
hiyo_rock: <p>作業途中で昼寝すっかってなって起きたら作業内容忘れてるのクソ仕様だな</p>
rakuda: <p>140、オレンジレンジくらい</p>
hiyo_rock: <p>おはよう</p>
yum: <p>音ゲーマーの感覚でBPM140前後が中速って前提で会話してたら噛み合わなかった</p>

# GUIで表示する。
PS> Invoke-WebRequest "https://mostodon.cloud/api/v1/timelines/public?local=1&limit=6" | %{ $_.Content } | ConvertFrom-Json |  %{ $names = $_.account.username; $conts = $_.content; 0..($names.Length-1) | %{ [pscustomobject]@{name=$names[$_]; contents=$conts[$_]}  } | Out-GridView }
```

csvを取ってきてちょろっと処理。
```
# 個人的に私がメンテしている日本の実質GDPのCSV
PS> invoke-webrequest -OutFile gdp.csv -Uri "https://docs.google.com/spreadsheet/pub?key=0AnKwf3jHs-oIdGVESWc4OGs2cVJxYVFLaTFZNHhOLVE&single=true&gid=0&output=csv"

# 2010年以上の平均を見る。
PS> > Get-Content gdp.csv | select -Skip 1 | %{ Write-Output(,$_.Split(",")) } | ?{ [int]$_[0] -ge 2010 } | %{ $_[1] } | measure-object -average


Count    : 9
Average  : 513083.244444444
Sum      :
Maximum  :
Minimum  :
Property :

# 513兆円。結構増えたね。

# 後始末
PS> rm gdp.csv
```

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
PS> Get-CimInstance -Class Win32_QuickFixEngineering | sort -Descending -Property InstalledOn | select -f 5

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

ググる
```
PS> $ie = new-object -ComObject "InternetExplorer.Application"
PS> $ie.Visible = $true
PS> $ie.navigate("https://www.google.co.jp")
PS> $inp = $ie.Document.getElementsByName("q")
PS> $btn = $ie.Document.getElementsByName("btnK")
PS> $inp[0].value = "酒はオワコン"
PS> $btn[0].click()
# これでIEで検索してあるはず。

# 終了する時はバツボタンでもいいけど、以下でもいい
PS> $ie.quit()
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
# なお、プロセスの名前はcalcじゃなくてCalculatorだったりする。
PS> get-process -Name calc* | Stop-Process
```



ファイルのサイズの合計(適当なディレクトリで)
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

