<%*

// 1. カテゴリを選択させる（変数に格納して実行を安定させる）

const knowledge_categories = ['Command', 'OS', 'Server', 'Network', 'Web-arch', 'Vuln-theory', 'Exploit-technique', 'Language', 'Security'];

const knowledge_category = await tp.system.suggester(knowledge_categories, knowledge_categories);

const title = tp.file.title;

-%>
---
created:
modified:
environment: [] # 例: [OS/Windows11, Web/Apache2, DB/MySQL]
vulnearability: [] # 例: [SQLi, SSXi, OSCommandi]
knowledge_category:  <% knowledge_category %>
tags:
---

# <% knowledge_category %>  - Linux - <% tp.file.title %>

