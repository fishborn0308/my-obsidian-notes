---
created: 2026-01-08 18:10
modified: 2026-01-17 18:18
environment:
  - Server/Web
vulnearability:
  - LFI
type: pentest-walkthrough
pentest_category: Web
platform:
  - PurpleFlair
tools: []
cve: []
tags:
  - Log
  - Incident_Response
---
# [Pentest-Walkthrough] Web - Project: LFIログ調査

セキュリティベンダーが、あなたの会社の運営しているWebアプリケーションに、ローカルファイルインクルージョン(LFI)の脆弱性が存在すると指摘しました。これを受けて、あなたはこのWebアプリケーションにローカルファイルインクルージョンの脆弱性をついた攻撃が発生しているかについて調査することになりました。

このWebアプリケーションには、WAFとしてModSecurityが導入されています。

WAFのログファイルから、ローカルファイルインクルージョンの脆弱性をついた攻撃を試みた攻撃者のIPアドレスを解答してください。

開始手順

    問題ファイルをダウンロード

以下のURLにアクセスし、問題ファイルをダウンロードし、問題に解答してください。

```
http://10.234.132.38/modsec_audit-60e131671259a2c2008303d68e7e5cde.log
```

### 解説

この問題は、LFIの脆弱性をついた攻撃が発生し、WAFのログから攻撃を試行した攻撃者を特定する状況を想定したものです。

WAF

このウェブアプリケーションにはOSSのWAFであるModSecurityが導入されています。ModSecurityはウェブサーバへのリクエストとサーバからの応答を攻撃検知ルールに基づき検査します。検査の結果、悪意のあるリクエストだと判断された場合はルールに記述された動作が実行されます。

また、攻撃検知のルールとしてOWASP Core Rule Set(CRS)を使用しています。このルールセットはOWASP Top10に代表される攻撃などからWebアプリケーションを保護する目的で作成されています。

ModSecurityのログにはパートが存在します。大文字のアルファベットで情報が区切られていて、対応する情報が記録されます。詳細については公式ドキュメントを参照ください。

ログ調査

LFIの脆弱性をついた攻撃手法の中でも、典型的なものとして任意のファイルを読み出すものがあります。

```
http://<target_address>/<target_page>?<key>=../../../../<file_path>
```

`<key>`には以下のような値が想定されます。

    file
    page
    site
    content

また、`<file_path>`としては`/etc/passwd`などが指定されやすいです。

このリクエストの特徴として、../を用いて階層をさかのぼり、任意のファイルを参照していることが挙げられます。

この攻撃を用いた場合、リクエストに../やURLエンコードされた..%2F等が含まれるはずです。 以上から、ログファイルのリクエストの記録に注目し、攻撃者を特定します。

```
$grep "\-B\-\-" modsec_audit-60e131671259a2c2008303d68e7e5cde.log -C 1
```

などで、Aパート(Audit Log Header)に記録されている送信元IPアドレスや宛先IPアドレスと、Bパート(Request Headers)に記録されているリクエストラインを抽出します。

これに対して、悪性なリクエストに含まれる可能性が高い../で検索します。

```
$ grep "\-B\-\-" modsec_audit-60e131671259a2c2008303d68e7e5cde.log -C 1 | grep "\.\./" -B 3
--
[29/Feb/2024:04:45:58.399923 +0000] ZeAMBumw_3AnOt1pI4dtawAAAMA 172.19.32.104 54480 172.16.33.70 80
--1dad8e27-B--
GET /index.php?site=../../../../ HTTP/1.1
--
--
[29/Feb/2024:04:52:01.462434 +0000] ZeANcemw_3AnOt1pI4dtbQAAAMY 172.19.32.104 46592 172.16.33.70 80
--f98ed156-B--
GET /index.php?site=../../../../../../../../var/log/apache2/access.log HTTP/1.1
```

上記内容がヒットし、access.logを参照できないか試行していたことがわかります。

Aパートでは、`<送信元IPアドレス> <port>`、`<宛先IPアドレス>` `<port>`の順で記録されています。 よって、攻撃者のIPアドレスは172.19.32.104とわかります。

補足

OWASP CRSのドキュメントにて、LFIを検索します。 すると、ルールの説明ページにREQUEST-930-APPLICATION-ATTACK-LFI.confというルールの説明が記載されています。

ログファイルのHパート(Audit Log Trailer)には、メッセージとしてマッチした検知ルールについての情報が記録されます。

実際に、access.logを参照しようとした通信では次の内容が記録されています。

```
--f98ed156-H--
Message: Warning. Pattern match "(?:^([\\d.]+|\\[[\\da-f:]+\\]|[\\da-f:]+)(:[\\d]+)?$)" at REQUEST_HEADERS:Host. [file "/etc/modsecurity.d/owasp-crs/rules/REQUEST-920-PROTOCOL-ENFORCEMENT.conf"] [line "789"] [id "920350"] [msg "Host header is a numeric IP address"] [data "172.16.33.70"] [severity "WARNING"] [ver "OWASP_CRS/4.0.0"] [tag "modsecurity"] [tag "application-multi"] [tag "language-multi"] [tag "platform-multi"] [tag "attack-protocol"] [tag "paranoia-level/1"] [tag "OWASP_CRS"] [tag "capec/1000/210/272"] [tag "PCI/6.5.10"]
Message: Warning. Pattern match "(?i)(?:[/\\x5c]|%(?:2(?:f|5(?:2f|5c|c(?:1%259c|0%25af))|%46)|5c|c(?:0%(?:[2aq]f|5c|9v)|1%(?:[19p]c|8s|af))|(?:bg%q|(?:e|f(?:8%8)?0%8)0%80%a)f|u(?:221[5-6]|EFC8|F025|002f)|%3(?:2(?:%(?:%6|4)6|F)|5%%63)|1u)|0x(?:2f|5c))(?:\\.(?:%0[0-1]|\\?)?|\\?\\.?|%(?: ..." at REQUEST_URI_RAW. [file "/etc/modsecurity.d/owasp-crs/rules/REQUEST-930-APPLICATION-ATTACK-LFI.conf"] [line "53"] [id "930100"] [msg "Path Traversal Attack (/../) or (/.../)"] [data "Matched Data: /../ found within REQUEST_URI_RAW: /index.php?site=../../../../../../../../var/log/apache2/access.log"] [severity "CRITICAL"] [ver "OWASP_CRS/4.0.0"] [tag "modsecurity"] [tag "application-multi"] [tag "language-multi"] [tag "platform-multi"] [tag "attack-lfi"] [tag "paranoia-level/1"] [tag "OWASP_CRS"] [tag "capec/1000/255/153/126"]

```

先程検索したルールで攻撃が検知されていることがわかります。

Hパート下部のAction: Intercepted (phase 2)という記述から、WAFによって該当の攻撃通信は遮断されたことがわかります。このときのEパート(Intended Response Body)とFパート(Response Headers)を見ることで、詳細なレスポンスも確認できます。

このように、WAFはWebアプリケーションの保護に有効です。運用しているWebアプリケーションの通常利用に影響がないことを確認した上で活用しましょう。
