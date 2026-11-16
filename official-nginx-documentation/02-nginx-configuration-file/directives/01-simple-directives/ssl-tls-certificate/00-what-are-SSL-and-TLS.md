# Intro to SSL and TLS
---
## 1-پروتوکل هایه SSL  و TLS مخفف چی هستند و چیکار میکنند؟

پروتکل SSL مخفف (secure socket layer) و پروتکل TLS مخفف (Transport layer security) است : 
ا-TLSنسل جدیدتر و جایگزین پروتکل قدیمی SSL است که امروزه برای برقراری ارتباط امن بین Client و Server در وب استفاده می‌شود.

**وظایف اصلی TLS:**

1. رمزنگاری اطلاعات (Encryption)
    
2. احراز هویت سرور (Authentication)
    
3. حفظ یکپارچگی اطلاعات (Integrity)
    

**نکته:** امروزه از TLS 1.2 و TLS 1.3 استفاده می‌شود.

---
## 2- **برای چه استفاده می‌شوند؟**

هر دو برای محافظت از اطلاعات هنگام انتقال بین دو سیستم طراحی شده‌اند:

- رمزنگاری اطلاعات (Encryption)
    
- احراز هویت سرور (Authentication)
    
- جلوگیری از دست‌کاری اطلاعات (Integrity)

---
**نکته مهم برای یادگیری Nginx:**

ا-SSL منسوخ شده و امروزه باید از TLS استفاده کنیم. با این حال، هنوز در اصطلاحات رایج مثل SSL Certificate از نام SSL استفاده می‌شود، درحالی‌که منظور<mark> معمولاً گواهی مورد استفاده در TLS </mark>است.

---
## 3- ما در انجین ایکس هعی میگیم گواهی نامه SSL منظورمون چیه؟

در Nginx وقتی می‌گوییم **SSL Certificate**، معمولاً منظورمان گواهی دیجیتالی‌ای است که برای احراز هویت سرور در ارتباط TLS استفاده می‌شود.

یعنی:

- ا-**TLS:** پروتکلی است که ارتباط امن بین Client و Server را برقرار می‌کند.
    
- ا-**SSL Certificate:** گواهی دیجیتالی برای معرفی و احراز هویت سرور است.
    
- ا-**Private Key:** کلید خصوصی متناظر با Certificate است.
    

مثلاً در Nginx:

```
listen 443 ssl;

ssl_certificate /etc/letsencrypt/live/example.com/fullchain.pem;

ssl_certificate_key /etc/letsencrypt/live/example.com/privkey.pem;
```

اینجا با وجود کلمه `ssl` در تنظیمات Nginx، ارتباط امن می‌تواند با پروتکل TLS برقرار شود.

**پس این نکته را به خاطر بسپار:**

ا-SSL در اصطلاحات رایج و تنظیمات Nginx هنوز استفاده می‌شود، اما پروتکل امنیتی مورد استفاده امروزی TLS است.

---
## 4-الان ما در تنظیمات مثلا مینویسیم SSL چیه منظورمون ؟

ما در Nginx می‌نویسیم:

```
listen 443 ssl;
```

اما این به معنی استفاده از پروتکل قدیمی SSL نیست!

**چرا؟**

چون کلمه `ssl` در تنظیمات Nginx یک نام تاریخی برای فعال‌کردن قابلیت ارتباط امن است.

در واقع Nginx با این تنظیم، ارتباط امن مبتنی بر TLS را برقرار می‌کند.

برای مشخص‌کردن نسخه‌های مجاز TLS هم می‌توانیم بنویسیم:

```
ssl_protocols TLSv1.2 TLSv1.3;
```

پس این سه نکته را به خاطر بسپار:

1. پورت 443 پورت استاندارد HTTPS است.
    
2. کلمه `ssl` در Nginx به معنی فعال‌کردن قابلیت SSL/TLS است.
    
3. پروتکلی که امروزه استفاده می‌کنیم TLS 1.2 یا TLS 1.3 است.
    

**نتیجه:** `listen 443 ssl` **می‌نویسیم، اما از TLS استفاده می‌کنیم.**


----
## 5-و لیستی از دایرکتیو هایی که در بخش ssl certificate استفاده میکنیم

## **راهنمای درجه اهمیت**

- ⭐⭐⭐ ضروری
    
    برای راه‌اندازی HTTPS و امنیت پایه
    
- ⭐⭐ مهم
    
    برای بهینه‌سازی و امنیت بیشتر
    
- ⭐ پیشرفته
    
    برای سناریوهای خاص و حرفه‌ای
    

#### **۱. Directiveهای اصلی SSL/TLS**

|Directive|اهمیت|کاربرد|
|---|---|---|
|`listen 443 ssl`|⭐⭐⭐|فعال‌کردن HTTPS روی پورت 443|
|`ssl_certificate`|⭐⭐⭐|تعیین مسیر Certificate|
|`ssl_certificate_key`|⭐⭐⭐|تعیین مسیر Private Key|
|`ssl_protocols`|⭐⭐⭐|تعیین نسخه‌های مجاز TLS|
|`ssl_ciphers`|⭐⭐|تعیین الگوریتم‌های رمزنگاری مجاز|
|`ssl_session_cache`|⭐⭐|ذخیره اطلاعات نشست‌ها برای کاهش هزینه برقراری مجدد ارتباط|
|`ssl_session_timeout`|⭐|تعیین مدت اعتبار نشست TLS|
|`ssl_prefer_server_ciphers`|⭐|تعیین اولویت Cipherهای سرور در TLS 1.2 و قدیمی‌تر|

#### **۲. Directiveهای مربوط به Certificate و اعتبارسنجی**

|Directive|اهمیت|کاربرد|
|---|---|---|
|`ssl_trusted_certificate`|⭐⭐|معرفی CAهای مورد اعتماد برای اعتبارسنجی گواهی‌ها|
|`ssl_client_certificate`|⭐|معرفی CA صادرکننده گواهی کلاینت|
|`ssl_verify_client`|⭐|فعال‌کردن احراز هویت کلاینت (mTLS)|
|`ssl_verify_depth`|⭐|تعیین عمق بررسی زنجیره گواهی کلاینت|

#### **۳. Directiveهای مربوط به بهینه‌سازی و امنیت**

|Directive|اهمیت|کاربرد|
|---|---|---|
|`ssl_stapling`|⭐⭐|فعال‌کردن OCSP Stapling برای ارائه وضعیت اعتبار گواهی|
|`ssl_stapling_verify`|⭐⭐|بررسی اعتبار پاسخ OCSP|
|`ssl_session_tickets`|⭐|فعال یا غیرفعال‌کردن Session Tickets|
|`ssl_dhparam`|⭐|تعیین پارامترهای Diffie-Hellman برای برخی Cipher Suiteها|

#### **۴. نمونه تنظیمات Nginx در محیط واقعی**

این نمونه یک پیکربندی پایه و مناسب برای HTTPS با TLS 1.2 و TLS 1.3 است:

```
server {
    listen 443 ssl;
    server_name example.com;

    # Certificate
    ssl_certificate /etc/letsencrypt/live/example.com/fullchain.pem;

    # Private Key
    ssl_certificate_key /etc/letsencrypt/live/example.com/privkey.pem;

    # TLS Versions
    ssl_protocols TLSv1.2 TLSv1.3;

    # Session Cache
    ssl_session_cache shared:SSL:10m;
    ssl_session_timeout 10m;

    # OCSP Stapling
    ssl_stapling on;
    ssl_stapling_verify on;
}
```

#### **۵. ترتیب پیشنهادی یادگیری**

من پیشنهاد می‌کنم به این ترتیب جلو بروی:

1. **مرحله اول: راه‌اندازی HTTPS**
    
    `listen 443 ssl`
    
    `ssl_certificate`
    
    `ssl_certificate_key`
    
2. **مرحله دوم: تنظیم امنیت TLS**
    
    `ssl_protocols`
    
    `ssl_ciphers`
    
3. **مرحله سوم: بهینه‌سازی ارتباط**
    
    `ssl_session_cache`
    
    `ssl_session_timeout`
    
4. **مرحله چهارم: امنیت پیشرفته**
    
    `ssl_stapling`
    
    `ssl_stapling_verify`
    
    `ssl_verify_client`
    

**نکته فنی:** درجه اهمیت به سناریوی استفاده بستگی دارد؛ مثلاً `ssl_verify_client` برای یک وب‌سایت عمومی معمولاً ضروری نیست، اما در ارتباطات سازمانی مبتنی بر Mutual TLS اهمیت بالایی دارد. همچنین برای TLS 1.3، تنظیم `ssl_ciphers` به‌تنهایی Cipher Suiteهای TLS 1.3 را کنترل نمی‌کند.
