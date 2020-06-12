なんか書く。


Documentsフォルダの下にpowershell_workというフォルダを作る。

```
> cd Documents
Documents> dir
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
linux_intro_ml> dir
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
linux_intro_ml> dir
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
linux_intro_ml> dir
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

