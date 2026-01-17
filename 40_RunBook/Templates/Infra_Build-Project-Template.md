<%*
// 1. 起動時に入力を求める (変数に格納することで本文での再利用を確実にする)
const categories = ['Server', 'Network', 'Security-Hardening'];

const category = await tp.system.suggester(categories, categories);

const title = tp.file.title;
-%>
---
created:
modified:
environment: [] # 例: [OS/Windows11, Web/Apache2, DB/MySQL]
vulnearability: [] # 例: [SQLi, SSXi, OSCommandi]
type: research
build_category: <% category %>
tools: [] # 例: [nmap, gobuster, hydra]
cve: [] # 例: [CVE-2025-3365]
tags:
---

# [Build] <% category %> - Project: <% tp.file.title %>

## 1. 設計の概要

### 目的

### ネットワーク構成

#### LAN

#### DMZ

## 2. 構築ステップ

### コマンド / 設定例

- [ ] OSインストール

```bash

```

- [ ] セキュリティ設定

```bash

```

- [ ] サービスインストール

```bash

```

## 3. 設計の詳細

### 設定ファイルパス

```bash

```

### 変更点

```bash

```

## 4. メンテナンス・運用メモ

### アップデート手順

### バックアップ対象

## 5. 接続情報

### User

- Admin
- SSH
### URL
