<%*
// 1. 起動時に入力を求める (変数に格納することで本文での再利用を確実にする)
const categories = ['Exploit', 'Tool', 'Command', 'Network'];

const category = await tp.system.suggester(categories, categories);

const title = tp.file.title;
-%>
---
created:
modified:
environment: [] # 例: [OS/Windows11, Web/Apache2, DB/MySQL]
vulnearability: [] # 例: [SQLi, SSXi, OSCommandi]
type: research
research_category: <% category %>
tools: [] # 例: [nmap, gobuster, hydra]
cve: [] # 例: [CVE-2025-3365]
tags:
---

# [Research] <% category %> - Project: <% tp.file.title %>

## 1. 検証の目的

## 2. 検証プロセス、実行コマンド

### コマンド / 設定例

```bash

```

## 3. 検証結果・結論

## 4. 考察・メモ