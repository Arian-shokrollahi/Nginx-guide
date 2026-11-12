# intro serving static and dynamic
---
## 1-ما چند مدل سرو کردن فایل درون وب داریم:
- ۲ مدل static server && dynamic serve

---
## 2-توضیح از نمای کلی که چه اتفاقی میوفته:

### ۱. Static files (فایل‌های ثابت)

فایل‌هایی هستند که محتوایشان از قبل آماده است و برای هر کاربر تغییر نمی‌کند.

مثال:

```
index.html
style.css
script.js
image.jpg
logo.png
font.ttf
```

وقتی کاربر درخواست می‌دهد:

```
GET /logo.png
```

ا-Nginx فایل `logo.png` را از روی دیسک پیدا می‌کند و مستقیم برای کاربر ارسال می‌کند.

به این کار می‌گوییم:

```
Nginx is serving a static file
```

---

### ۲. Dynamic content (محتوای پویا)

محتوا در لحظه تولید می‌شود.

مثلاً:

- صفحه پروفایل کاربر
- نتیجه جستجو
- اطلاعات سبد خرید

اینجا معمولاً Nginx درخواست را به یک برنامه دیگر می‌فرستد:

```
Client
  |
  ↓
Nginx
  |
  ↓
Application (Python, Node.js, PHP, etc.)
  |
  ↓
Database
```

---

### مثال در Nginx:

```
server {
    listen 80;

    location / {
        root /var/www/html;
    }
}
```

اگر داخل مسیر:

```
/var/www/html
```

این فایل وجود داشته باشد:

```
index.html
```

و کاربر درخواست کند:

```
http://example.com/index.html
```

Nginx آن فایل را مستقیم می‌فرستد.

---

## ۳-توضیح به صورت دیاگرامی

## ۱. Static file serving (ارائه فایل‌های ثابت)

در این حالت فایل **از قبل ساخته شده** و Nginx آن را مستقیم به کاربر می‌دهد.

ساختار:

```
Client
  |
  |  Request: /image.png
  ↓
Nginx
  |
  |  پیدا کردن فایل روی دیسک
  ↓
Static File
  |
  ↓
Client
```

مثال فایل‌ها:

```
index.html
style.css
app.js
logo.png
video.mp4
```

اینجا Nginx فقط نقش **تحویل‌دهنده فایل** را دارد.

---

## ۲. Dynamic content serving (ارائه محتوای پویا)

در این حالت محتوا در لحظه تولید می‌شود و معمولاً Nginx خودش آن را تولید نمی‌کند؛ بلکه درخواست را به یک **Application Server** می‌فرستد.

ساختار:

```
Client
  |
  ↓
Nginx
  |
  ↓
Application Server
(Python / Node.js / PHP / Java ...)
  |
  ↓
Database
  |
  ↓
Generated Response
  |
  ↓
Client
```

مثال:

کاربر وارد حساب کاربری می‌شود:

```
GET /profile
```

برنامه باید:

- اطلاعات کاربر را از Database بگیرد.
- صفحه را بسازد.
- پاسخ را برگرداند.

---

پس تفاوت اصلی:

|ویژگی|Static|Dynamic|
|---|---|---|
|محتوا|از قبل آماده|در لحظه ساخته می‌شود|
|سازنده محتوا|فایل روی دیسک|برنامه + دیتابیس|
|نقش Nginx|تحویل مستقیم|واسطه (Reverse Proxy)|
|سرعت|معمولاً سریع‌تر|وابسته به برنامه و دیتابیس|

---
### ۳-nginx در کدوم یکی از مدل هایه بالا کارامد تر است و کجا ها حرفی برایه گفتن دارد

ا-Nginx بیشتر به خاطر **Static File Serving** و **Reverse Proxy** معروف شده است؛ یعنی دو نقش اصلی دارد:

### ۱. Static File Server (ارائه‌دهنده فایل‌های ثابت) ⭐

این یکی از اولین و معروف‌ترین کاربردهای Nginx است.

Nginx در این حالت فایل‌ها را مستقیم از دیسک به کاربر می‌دهد:

```
Client
  |
  ↓
Nginx
  |
  ↓
HTML / CSS / JS / Image
```

چرا معروف است؟

- سرعت خیلی بالا دارد.
- مصرف RAM و CPU کمی دارد.
- برای تعداد زیادی درخواست هم‌زمان مناسب است.

مثلاً:

```
location /images/ {
    root /var/www;
}
```

درخواست:

```
example.com/images/logo.png
```

باعث می‌شود Nginx فایل را مستقیم ارسال کند.

---

### ۲. Reverse Proxy ⭐⭐⭐

امروزه Nginx بیشتر در معماری‌های مدرن به‌عنوان **Reverse Proxy** استفاده می‌شود.

اینجا Nginx جلوی Application Server قرار می‌گیرد:

```
Client
  |
  ↓
Nginx
  |
  ↓
Backend Application
(Node.js / Python / Java / PHP)
```

کارهای Nginx:

- دریافت درخواست کاربر
- ارسال درخواست به Backend مناسب
- ا-Load Balancing بین چند سرور
- مدیریت SSL/TLS
- ا-Cache کردن پاسخ‌ها

مثلاً:

```
/api/users
        ↓
     Backend

/images/logo.png
        ↓
     Nginx خودش ارسال می‌کند
```

---
