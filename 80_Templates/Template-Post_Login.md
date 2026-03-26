# Post-Login（Linux）

## 🔎 基本情報

```bash
whoami
id
hostname
uname -a
```

---

## 🔑 権限確認

```bash
sudo -l
```

---

## 📁 SUID / SGID

```bash
find / -perm -4000 2>/dev/null
```

---

## 🧾 クレデンシャル探索

```bash
cat ~/.bash_history
grep -r password /home 2>/dev/null
grep -r "pass" /etc 2>/dev/null
```

---

## 📂 書き込み可能ディレクトリ

```bash
find / -writable -type d 2>/dev/null
```

---

## ⏰ Cron

```bash
crontab -l
cat /etc/crontab
ls -la /etc/cron*
```

---

## 🧠 プロセス

```bash
ps aux
```

---

## 🌐 ネットワーク

```bash
ip a
netstat -tulnp
```