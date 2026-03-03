# Scanning and Initial Access

## Version Scanning with Nmap and Netcat

### Version Scanning

• Nmapが開放ポートを検出すると、そのポートに一般的に関連付けられるデフォルトサービスを表示します
− nmap-servicesファイルに基づく
− では他のサービスはどうでしょうか？
• 非標準ポート上のサービスはどうでしょうか？
− 例：TCP 90上のWebサーバーやTCP 3322上のsshd
• ターゲットがリッスンしているサービスは、どのサービスとプロトコルバージョンを使用しているのでしょうか？
Nmapのバージョンスキャンが答えを出します。

