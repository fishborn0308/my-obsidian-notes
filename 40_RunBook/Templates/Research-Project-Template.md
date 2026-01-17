<%*
// 1. 起動時に入力を求める (変数に格納することで本文での再利用を確実にする)
const targetIp = await tp.system.prompt("Target IP Address", "10.10.10.");

const categories = ['Web', 'Network', 'AD', 'Cloud', 'OS'];

const category = await tp.system.suggester(categories, categories);

const title = tp.file.title;
-%>
---
created:
modified:
environment: [] # 例: [OS/Windows11, Web/Apache2, DB/MySQL]
vulnearability: [] # 例: [SQLi, SSXi, OSCommandi]
type: 
research_category: <% category %>
target_ip: <% targetIp %>
tools: [] # 例: [nmap, gobuster, hydra]
cve: [] # 例: [CVE-2025-3365]
tags:
---

# [Pentest-log] <% category %> - Project: <% tp.file.title %>

## 1. ターゲット情報

- **IP Address:** <% targetIp %>
- **目的:** ---

## 2. 偵察 (Reconnaissance)

### チェックリスト
- [ ] **Nmap全ポートスキャン**
- [ ] **Webサービス列挙 (Port 80/443)**
- [ ] **ディレクトリ・ファイル探索 (Gobuster/Feroxbuster)**
- [ ] **脆弱性DB検索 (Searchsploit)**

### Nmap Scan
```bash
# 基本スキャン
nmap -sV -sC -p- -oN nmap_result.txt <% targetIp %>
````

### Web Enumeration (Port 80/443)

---

## 3. 脆弱性分析・潜入 (Exploitation)

- **発見した脆弱性:** - **使用コード/ペイロード:** 

```bash
  
# ペイロードや実行コマンドをここに記録

```

---

## 4. 権限昇格 (Privilege Escalation)

- **Local Recon (linpeas/winpeas):** - **Path to Root:** 

```bash
# 昇格に使用したコマンド
```

---

## 5. 証跡 (Evidence)

> [!TIP]
> 
> MX Master 4 のアクションで撮影したスクショを 99_Assets に保存し、ファイル名を以下の形式に合わせてリンクさせます。

|**内容**|**スクリーンショット**|
|---|---|
|**Nmap結果**|![[<% tp.file.title %>_nmap.png]]|
|**User Flag**|![[<% tp.file.title %>_user.png]]|
|**Root Flag**|![[<% tp.file.title %>_root.png]]|

---

## 6. 考察・メモ
