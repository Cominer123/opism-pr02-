
# Практична робота № 2

**Дисципліна:** Основи побудови інформаційних систем та мереж (ОК-13)

**Тема:** Структура повідомлень прикладного протоколу HTTP. Формування запиту вручну

| Поле | Значення |
|---|---|
| Студент (ПІБ) | Магомедов Ельдар | 
| Група | ІПЗ 2.01 | 
| Номер варіанта | 20 |
| Індивідуальний домен | w3.org |
| Чужий домен (варіант 28) | knu.ua |
| Середовище виконання | Windows PowerShell |
| Дата виконання |27.11.2026 |

---

## Частина A. Збір експериментальних даних

### Завдання A.1. Формування запиту вручну

**Команда:**

```powershell
$d = "w3.org"
$c = New-Object System.Net.Sockets.TcpClient($d, 80)
$s = $c.GetStream()
$w = New-Object System.IO.StreamWriter($s)
$w.Write("GET / HTTP/1.1`r`nHost: $d`r`nConnection: close`r`n`r`n")
$w.Flush()
(New-Object System.IO.StreamReader($s)).ReadToEnd()
$c.Close()
```

**Набраний запит:**

```http
GET / HTTP/1.1
Host: w3.org
Connection: close

```

**Відповідь:**

```text
HTTP/1.1 301 Moved Permanently
Date: Fri, 27 Nov 2026 15:56:48 GMT
Content-Type: text/html; charset=UTF-8
Transfer-Encoding: chunked
Connection: close
Location: https://www.w3.org/
set-cookie: __cf_bm=yA97ffed3dwKH8WI3jimrL6si5ZrR4ESE8kq77FHGRw-1795795008.9521928-1.0.1.1-oJ9Vfq78Le8129guxLHDtQ6z.UqP8JOIgmWYP17e433nSgO4gb_a8J3n3DMGRRR6XZeIY_iefST.NK.bKP2XzF3icru47zpnn4BYp6BIVV1SrSJtlH587jOvxPjhxOF5; HttpOnly; Path=/; Domain=w3.org; Expires=Fri, 27 Nov 2026 16:26:48 GMT
X-Content-Type-Options: nosniff
Server: cloudflare
CF-RAY: a6125335ff30f531-AMS
alt-svc: h3=":443"; ma=86400

a7
<html>
<head><title>301 Moved Permanently</title></head>
<body>
<center><h1>301 Moved Permanently</h1></center>
<hr><center>cloudflare</center>
</body>
</html>

0
```



### Завдання A.2. Запит без поля `Host` у версії 1.1

**Команда:**

```powershell
$c = New-Object System.Net.Sockets.TcpClient($d, 80)
$s = $c.GetStream()
$w = New-Object System.IO.StreamWriter($s)
$w.Write("GET / HTTP/1.1`r`nConnection: close`r`n`r`n")
$w.Flush()
(New-Object System.IO.StreamReader($s)).ReadToEnd()
$c.Close()
```

**Вивід:**

```text
HTTP/1.1 400 Bad Request
Server: cloudflare
Date: Fri, 27 Nov 2026 15:59:46 GMT
Content-Type: text/html
Content-Length: 155
Connection: close
CF-RAY: -

<html>
<head><title>400 Bad Request</title></head>
<body>
<center><h1>400 Bad Request</h1></center>
<hr><center>cloudflare</center>
</body>
</html>
```




### Завдання A.3. Вплив поля `Host` на відповідь сервера

#### A.3.1. Чуже доменне ім'я в полі `Host`

**Команда:**

```powershell
$d = "w3.org"
$c = New-Object System.Net.Sockets.TcpClient($d, 80)
$s = $c.GetStream()
$w = New-Object System.IO.StreamWriter($s)
$w.Write("GET / HTTP/1.1`r`nHost: knu.ua`r`nConnection: close`r`n`r`n")
$w.Flush()
(New-Object System.IO.StreamReader($s)).ReadToEnd()
$c.Close()
```

**Вивід:**

```text
HTTP/1.1 409 Conflict
Date: Fri, 27 Nov 2026 16:08:13 GMT
Content-Type: text/plain; charset=UTF-8
Content-Length: 17
Connection: close
Cache-Control: private, max-age=0, no-store, no-cache, must-revalidate, post-check=0, pre-check=0
Expires: Thu, 01 Jan 1970 00:00:01 GMT
Referrer-Policy: same-origin
X-Frame-Options: SAMEORIGIN
Server: cloudflare
CF-RAY: a61263ec9d8699d3-AMS

error code: 1001
```


#### A.3.2. Неіснуюче ім'я в полі `Host`

**Команда:**

```powershell
$d = "w3.org"
$c = New-Object System.Net.Sockets.TcpClient($d, 80)
$s = $c.GetStream()
$w = New-Object System.IO.StreamWriter($s)
$w.Write("GET / HTTP/1.1`r`nHost: opism-pr02.invalid`r`nConnection: close`r`n`r`n")
$w.Flush()
(New-Object System.IO.StreamReader($s)).ReadToEnd()
$c.Close()
```

**Вивід:**

```text
HTTP/1.1 409 Conflict
Date: Fri, 27 Nov 2026 16:08:25 GMT
Content-Type: text/plain; charset=UTF-8
Content-Length: 17
Connection: close
Cache-Control: private, max-age=0, no-store, no-cache, must-revalidate, post-check=0, pre-check=0
Expires: Thu, 01 Jan 1970 00:00:01 GMT
Referrer-Policy: same-origin
X-Frame-Options: SAMEORIGIN
Server: cloudflare
CF-RAY: a612643668ae96e5-AMS

error code: 1001
```


#### A.3.3. Запит без поля `Host` у версії 1.0

**Команда:**

```powershell
$d = "w3.org"
$c = New-Object System.Net.Sockets.TcpClient($d, 80)
$s = $c.GetStream()
$w = New-Object System.IO.StreamWriter($s)
$w.Write("GET / HTTP/1.0`r`n`r`n")
$w.Flush()
(New-Object System.IO.StreamReader($s)).ReadToEnd()
$c.Close()
```

**Вивід:**

```text
HTTP/1.1 403 Forbidden
Date: Fri, 27 Nov 2026 16:08:36 GMT
Content-Length: 57
Connection: close
Cache-Control: private, max-age=0, no-store, no-cache, must-revalidate, post-check=0, pre-check=0
Referrer-Policy: same-origin
Expires: Thu, 01 Jan 1970 00:00:01 GMT
CF-RAY: a61264796f7b9719-AMS

Cloudflare encountered an error processing this request:
```


Зведення результатів наведено в **Додатку Д**.

### Завдання A.4. Два запити в одному з'єднанні

**Команда:**

```powershell
$d = "w3.org"
$c = New-Object System.Net.Sockets.TcpClient($d, 80)
$s = $c.GetStream()
$w = New-Object System.IO.StreamWriter($s)
$w.Write("GET /opism-pr02-12345 HTTP/1.1`r`nHost: $d`r`n`r`nGET / HTTP/1.1`r`nHost: $d`r`nConnection: close`r`n`r`n")
$w.Flush()
(New-Object System.IO.StreamReader($s)).ReadToEnd()
$c.Close()
```

**Вивід:**

```text
HTTP/1.1 301 Moved Permanently
Date: Fri, 27 Nov 2026 16:09:03 GMT
Content-Type: text/html; charset=UTF-8
Transfer-Encoding: chunked
Connection: keep-alive
Location: https://www.w3.org/opism-pr02-12345
set-cookie: __cf_bm=Eo86Fv7wYLmVCqpqbKLmHQnJGfYDdCqr3SWz5qXjnIE-1795795743.889485-1.0.1.1-GMYRcZX_jACqX8DPJwATyvRL4hNzju9xW7rrKWmGF8VQ8D9QY_qc65ehNoUJxrgez5kwm59SYOMbFzNl7Zbgv5h1ETNzsGsQdrG3NiMbU4nr_2Y4vbXuQKcrlckURzvE; HttpOnly; Path=/; Domain=w3.org; Expires=Fri, 27 Nov 2026 16:39:03 GMT
X-Content-Type-Options: nosniff
Server: cloudflare
CF-RAY: a61265274f2f6db0-AMS
alt-svc: h3=":443"; ma=86400

a7
<html>
<head><title>301 Moved Permanently</title></head>
<body>
<center><h1>301 Moved Permanently</h1></center>
<hr><center>cloudflare</center>
</body>
</html>

0

HTTP/1.1 301 Moved Permanently
Date: Fri, 27 Nov 2026 16:09:03 GMT
Content-Type: text/html; charset=UTF-8
Transfer-Encoding: chunked
Connection: close
Location: https://www.w3.org/
set-cookie: __cf_bm=1eNQNhbG4tMnZvB2GtCBDUwARG.K9t5E7eBvXfPQUVk-1795795743.9001684-1.0.1.1-CVmYfh118E9IEwsxKv4Yc0_Nkzba0JJ4jjw43AJxh6Ugt9q5q7zP311jgIQ3xAxRz1byBadf18ZlRnmTa0Ik0AVZb0JTMTK4T5YAv4X8BFy1FtNCL.EM00S6EXAdp5sc; HttpOnly; Path=/; Domain=w3.org; Expires=Fri, 27 Nov 2026 16:39:03 GMT
X-Content-Type-Options: nosniff
Server: cloudflare
CF-RAY: a61265275f776db0-AMS
alt-svc: h3=":443"; ma=86400

a7
<html>
<head><title>301 Moved Permanently</title></head>
<body>
<center><h1>301 Moved Permanently</h1></center>
<hr><center>cloudflare</center>
</body>
</html>

0
```

**Кількість отриманих відповідей:** 2

**Коди стану отриманих відповідей:** 301, 301


### Завдання A.5. Запит за допомогою клієнтської програми

**Команда:**

```powershell
curl.exe -v --http1.1 http://w3.org/ -o NUL
```

**Вивід:**

```text
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
  0     0    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0* Host w3.org:80 was resolved.
* IPv6: (none)
* IPv4: 104.18.23.19, 104.18.22.19
*   Trying 104.18.23.19:80...
* Established connection to w3.org (104.18.23.19 port 80) from 10.1.8.239 port 50603
* using HTTP/1.x
> GET / HTTP/1.1
> Host: w3.org
> User-Agent: curl/8.16.0
> Accept: */*
>
* Request completely sent off
< HTTP/1.1 301 Moved Permanently
< Date: Fri, 27 Nov 2026 16:09:26 GMT
< Content-Type: text/html; charset=UTF-8
< Transfer-Encoding: chunked
< Connection: keep-alive
< Location: https://www.w3.org/
< set-cookie: __cf_bm=JJ_jmkCaK80z3Lm5EvDJ7O_SdgRCRC0TqzPClAZiOsQ-1795795766.3409266-1.0.1.1-n2MVjwqJIDwxpsuB7hJ1MuNgO.ksQ_r_1ulvQtvD3.3fzZ87HSv6Xd5r0iMl0rs_hgmUFkmyqAf3.T5dyw8YFC167shJ6V3TUQggWH3Fv8Kac8eo0dXWaPRAbdhOdERE; HttpOnly; Path=/; Domain=w3.org; Expires=Fri, 27 Nov 2026 16:39:26 GMT
< X-Content-Type-Options: nosniff
< Server: cloudflare
< CF-RAY: a61265b39ecbf531-AMS
< alt-svc: h3=":443"; ma=86400
<
{ [178 bytes data]
100   167    0   167    0     0    551      0 --:--:-- --:--:-- --:--:--   556
* Connection #0 to host w3.org:80 left intact
```


### Завдання A.6. Запит через захищене з'єднання

**Ресурс, на якому виконано завдання:** `w3.org`

**Підстава для використання резервного ресурсу:** не застосовується, оскільки в A.1 отримано 301, а TLS-з'єднання з власним доменом встановлено.

**Команда:**

```powershell
& "C:\Program Files\Git\usr\bin\openssl.exe" s_client -connect w3.org:443 -servername w3.org -crlf -quiet
```

**Набраний запит:**

```http
GET / HTTP/1.1
Host: w3.org
Connection: close

```

**Вивід:**

```text
Connecting to 104.18.22.19
depth=2 C=US, O=Google Trust Services LLC, CN=GTS Root R4
verify return:1
depth=1 C=US, O=Google Trust Services, CN=WE1
verify return:1
depth=0 CN=w3.org
verify return:1

HTTP/1.1 301 Moved Permanently
Date: Fri, 27 Nov 2026 16:10:58 GMT
Content-Type: text/html; charset=UTF-8
Transfer-Encoding: chunked
Connection: close
Location: https://www.w3.org/
set-cookie: __cf_bm=5.WdfltPtTj5lWN76znqQvyor1RnCgBXHNVi1peTMuM-1795795858.7979906-1.0.1.1-hfN0MzCA_Y.GxbZkyXRGLMNtF6GVINWB.8Sltz3TemA5k2LKCn3G1WErpN4vSTojif9lo6HgCe9golMpe0wCm9mY1ALTZhqul0Izh86mjDTYgI0SSg_xnIx0OhhjWBJo; HttpOnly; Secure; Path=/; Domain=w3.org; Expires=Fri, 27 Nov 2026 16:40:58 GMT
X-Content-Type-Options: nosniff
Server: cloudflare
CF-RAY: a61267dc98396e85-AMS
alt-svc: h3=":443"; ma=86400

a7
<html>
<head><title>301 Moved Permanently</title></head>
<body>
<center><h1>301 Moved Permanently</h1></center>
<hr><center>cloudflare</center>
</body>
</html>

0
```

---

## Частина B. Розбір полів заголовка

Розбирається відповідь, отримана в завданні A.1.

**Загальна кількість полів заголовка у відповіді:** 10

| № | Поле заголовка | Значення | Призначення (власне формулювання) | Походження: сервер / проміжний вузол / не визначено | Обґрунтування |
|---|---|---|---|---|---|
| 1 | `Date` | `Fri, 27 Nov 2026 15:56:48 GMT` | Дата й час формування HTTP-повідомлення у GMT. | не визначено | Час міг установити як кінцевий сервер, так і Cloudflare. |
| 2 | `Content-Type` | `text/html; charset=UTF-8` | Тип даних тіла та кодування символів UTF-8. | не визначено | Описує тіло відповіді; поле не ідентифікує вузол. |
| 3 | `Transfer-Encoding` | `chunked` | Передавання тіла окремими блоками з розмірами. | проміжний вузол | Відповідь від Cloudflare має блок a7 та завершальний блок 0; формат стосується поточного з'єднання. |
| 4 | `Connection` | `close` | Закриття поточного з'єднання після відповіді. | проміжний вузол | Відповідає параметру з'єднання між клієнтом і вузлом Cloudflare. |
| 5 | `Location` | `https://www.w3.org/` | Адреса, на яку потрібно перенаправити клієнта. | не визначено | Переадресацію міг налаштувати власник сайту або Cloudflare. |
| 6 | `set-cookie` | `__cf_bm=yA97ffed3dwKH8WI3jimrL6si5ZrR4ESE8kq77FHGRw-1795795008.9521928-1.0.1.1-oJ9Vfq78Le8129guxLHDtQ6z.UqP8JOIgmWYP17e433nSgO4gb_a8J3n3DMGRRR6XZeIY_iefST.NK.bKP2XzF3icru47zpnn4BYp6BIVV1SrSJtlH587jOvxPjhxOF5; HttpOnly; Path=/; Domain=w3.org; Expires=Fri, 27 Nov 2026 16:26:48 GMT` | Установлення cookie для домену w3.org із часом дії та атрибутами. | проміжний вузол | Назва __cf_bm характерна для механізму керування ботами Cloudflare. |
| 7 | `X-Content-Type-Options` | `nosniff` | Заборона браузеру самостійно визначати MIME-тип замість зазначеного. | не визначено | Поле безпеки може додавати сервер або проміжний вузол. |
| 8 | `Server` | `cloudflare` | Ідентифікація ПЗ або сервісу, який обробив відповідь. | проміжний вузол | Значення cloudflare вказує на сервіс Cloudflare. |
| 9 | `CF-RAY` | `a6125335ff30f531-AMS` | Ідентифікатор обробки запиту в мережі Cloudflare. | проміжний вузол | Назва CF-RAY та суфікс AMS характерні для Cloudflare. |
| 10 | `alt-svc` | `h3=":443"; ma=86400` | Оголошення альтернативного сервісу HTTP/3 на порту 443 на 86400 секунд. | не визначено | Параметр може бути встановлений на межі мережі, але одного заголовка недостатньо для точного висновку. |

---

## Частина D. Висновки

**D.1.** Що з поведінки сервера виявилося неочевидним або несподіваним.
Мене здивувало, що при введенні чужого та неіснуючого домену сервер видав однакову помилку 409. Також було цікаво, що сервер зміг відповісти на два запити в одному з'єднанні.

**D.2.** Яке з полів заголовка викликало найбільше утруднення при визначенні походження та з якої причини.
Найскладнішим для мене було поле Location, тому що я не зміг точно визначити, хто його додав  основний сервер чи Cloudflare.

**D.3.** Яке питання залишилося без відповіді після виконання роботи.
Мені залишилося незрозуміло, як дізнатися, чи відповідає сам сервер сайту, чи Cloudflare робить це замість нього.
---

## Контрольні питання

### 1. У завданні A.1 сервер не надсилав відповіді, доки не було введено порожній рядок. Чим це зумовлено?

**Відповідь:** HTTP-заголовки завершуються послідовністю CRLF CRLF (`\r\n\r\n`): перший CRLF завершує останній рядок, другий утворює порожній рядок. Це дозволяє серверу визначити межу заголовків. У моєму виконанні PowerShell передавав весь запит одразу через `$w.Flush()`, тому окремого очікування під час ручного набору не спостерігалося.

### 2. Порівняйте результати завдань A.1, A.2 та A.3.1–A.3.3. За яких значень поля Host і за якої версії протоколу сервер обслуговує запит, а за яких — ні? Яку задачу розв'язує поле Host?

**Відповідь:** У A.1 з `Host: w3.org` отримано `HTTP/1.1 301 Moved Permanently`, тобто запит прийнято й перенаправлено. У A.2 без Host у HTTP/1.1 отримано `400 Bad Request`. Для `knu.ua` та `opism-pr02.invalid` у A.3.1 і A.3.2 отримано `409 Conflict` та `error code: 1001`. У A.3.3 без Host у HTTP/1.0 отримано `403 Forbidden`. Host визначає цільове доменне ім'я та дає вузлу змогу вибрати відповідний віртуальний сайт, навіть коли кілька доменів використовують спільну IP-адресу.

### 3. Скільки відповідей надійшло у завданні A.4 і з якими кодами стану? Чи залежить відповідь сервера на порту 80 від запитаного шляху — і що це говорить про роль цього сервера?

Якщо надійшла одна відповідь, знайдіть у ній поле заголовка, яке це пояснює, або зазначте, що такого поля немає. Якщо надійшло дві — що це означає для клієнтської програми, яка завантажує сторінку з великою кількістю вкладених ресурсів?

**Відповідь:** Надійшло дві відповіді, обидві `HTTP/1.1 301 Moved Permanently`. Перша містить `Connection: keep-alive` та `Location: https://www.w3.org/opism-pr02-12345`, друга — `Connection: close` та `Location: https://www.w3.org/`. Код стану не залежав від двох перевірених шляхів, але адреса перенаправлення зберегла шлях. Це узгоджується з роллю вузла на порту 80 як засобу переадресації на HTTPS. Клієнт може використовувати одне TCP-з'єднання для кількох запитів, зменшуючи витрати на встановлення нових з'єднань; у цьому досліді використано два послідовно надіслані запити без очікування першої відповіді.

### 4. Які поля заголовка програма curl додала самостійно (завдання A.5)? З якою метою їх додано?

**Відповідь:** У A.5 curl додав `User-Agent: curl/8.16.0` та `Accept: */*`. User-Agent повідомляє серверу назву й версію клієнтської програми. Accept указує, що клієнт приймає будь-який тип вмісту. У A.1 ці поля були відсутні, але сервер також повернув 301.

### 5. За якими ознаками у вашому виводі виявляється присутність проміжного вузла? Якщо таких ознак не виявлено, поясніть, що з цього випливає.

**Відповідь:** У A.1 є `Server: cloudflare`, `CF-RAY: a6125335ff30f531-AMS` та cookie `__cf_bm`. У A.3.3 тіло містить `Cloudflare encountered an error processing this request:`. Ці ознаки вказують на обробку запитів сервісом Cloudflare, але не дозволяють визначити, чи кожен запит був переданий кінцевому серверу. Відсутність таких ознак сама по собі не доводила б відсутність посередників.

### 6. Три рядки, про які не йшлося на лекції

Наведено в Додатку В.

---

## Додаток В. Відповіді на питання 6

| № | Рядок виводу | Джерело (номер завдання) |
|---|---|---|
| 1 | `CF-RAY: a6125335ff30f531-AMS` | A.1 |
| 2 | `alt-svc: h3=":443"; ma=86400` | A.1 |
| 3 | `error code: 1001` | A.3.1 |



---

## Додаток Д. Зведення результатів завдання A.3

**Вузол, з яким установлювалося з'єднання:** w3.org

| Проба | Значення поля Host | Версія | Код стану | Обсяг тіла відповіді | Збігається з A.1 |
|---|---|---|---|---|---|
| A.1 (вихідна) | w3.org | 1.1 | 301 | 167 | — |
| A.2 | поле відсутнє | 1.1 | 400 | 155 | ні |
| A.3.1 | knu.ua | 1.1 | 409 | 17 | ні |
| A.3.2 | opism-pr02.invalid | 1.1 | 409 | 17 | ні |
| A.3.3 | поле відсутнє | 1.0 | 403 | 57 | ні |

**Висновок за таблицею (2–4 речення):** У пробах A.1–A.3 змінювалися значення Host, його наявність та версія протоколу; з'єднання встановлювали з доменом w3.org. Правильне доменне ім'я призвело до переадресації 301, відсутнє поле в HTTP/1.1 — до 400, а чуже та неіснуюче імена — до 409. У HTTP/1.0 без Host отримано 403. Обсяг тіла A.1 визначено за розміром блоку `a7` (167 байтів), в інших пробах — за `Content-Length`.



---

## Декларування використання технологій штучного інтелекту

**Рівень Р3 — ШІ як співвиконавець.**

Виводи команд частини A не можуть бути згенеровані та мають бути отримані внаслідок фактичного виконання команд.

**Чи використовувалися технології ШІ під час виконання роботи:** так.

| № | Інструмент (назва, версія) | Етап роботи | Дослівний текст запиту (промпту) | Як використано результат |
|---|---|---|---|---|
| 1 | ChatGPT (GPT-6.2) | Оформлення звіту та консультації | хорошо,теперь прошу давать мне команді | Допомога з підготовкою команд PowerShell і поясненням порядку виконання; команди виконав  самостійно. |



---

*ОПІСМ (ОК-13) · Практична робота № 2*
