# ssl_ciphers directive
---
# 1-دایرکتیو ssl_ciphers چیست ؟

این دایرکتیو یکی از مهم‌ترین تنظیمات امنیتی TLS است و مشخص می‌کند کلاینت و سرور از چه مجموعه الگوریتم‌های رمزنگاری (Cipher Suites) برای برقراری ارتباط امن استفاده کنند.

وقتی مرورگر کاربر می‌خواهد با سرور Nginx ارتباط HTTPS برقرار کند، در فرایند TLS Handshake مجموعه‌ای از الگوریتم‌های رمزنگاری را پیشنهاد می‌دهد.

سرور نیز بر اساس تنظیمات خود، از میان گزینه‌های قابل پشتیبانی، یک Cipher Suite را انتخاب می‌کند.

هر Cipher Suite مجموعه‌ای از الگوریتم‌هاست که وظایف مختلفی در ارتباط امن دارند؛ مانند:

- ا-Key Exchange: تبادل یا توافق روی کلید.
    
- ا-Authentication: احراز هویت.
    
- ا-Encryption: رمزنگاری اطلاعات.
    
- ا-Integrity: اطمینان از صحت و عدم تغییر اطلاعات.

---
# 2- **این دایرکتیو در کجا قرار می‌گیرد؟**

دایرکتیو `ssl_ciphers` در دو Context قابل استفاده است:

- ا-`http`
    
- ا-`server`
    

در Context مربوط به `location` قابل استفاده نیست.

## ساختار دایرکتیو به چه صورت است؟

ساختار کلی:

```
ssl_ciphers cipher1:cipher2:cipher3;
```

در این ساختار، Cipher Suiteهای موردنظر را به‌صورت یک رشته مشخص می‌کنیم و آن‌ها را با `:` از یکدیگر جدا می‌کنیم.

مثال:

```
ssl_ciphers HIGH:!aNULL:!MD5;
```

بیایید این مثال را بررسی کنیم:

|عبارت|مفهوم|
|---|---|
|`HIGH`|انتخاب Cipher Suiteهای دارای سطح رمزنگاری قوی|
|`!aNULL`|حذف الگوریتم‌های احراز هویت بدون اعتبارسنجی|
|`!MD5`|حذف Cipher Suiteهای مبتنی بر MD5|

علامت `!` به معنی حذف دائمی آن دسته از الگوریتم‌ها از مجموعه انتخاب‌شده است.

## **مثال در محیط Production**

```
server {
    listen 443 ssl;
    server_name example.com;

    ssl_certificate /etc/nginx/ssl/fullchain.pem;
    ssl_certificate_key /etc/nginx/ssl/privkey.pem;

    ssl_protocols TLSv1.2 TLSv1.3;

    ssl_ciphers HIGH:!aNULL:!MD5;
}
```

---
## 3-**تفاوت ssl_protocols و ssl_ciphers**

این دو دایرکتیو را به‌خوبی از هم تفکیک کن:

|Directive|وظیفه|
|---|---|
|`ssl_protocols`|تعیین نسخه‌های مجاز TLS|
|`ssl_ciphers`|تعیین مجموعه الگوریتم‌های رمزنگاری مجاز|

برای مثال:

```
ssl_protocols TLSv1.2 TLSv1.3;
ssl_ciphers HIGH:!aNULL:!MD5;
```

یعنی ابتدا مشخص می‌کنیم چه نسخه‌هایی از TLS مجاز هستند و سپس مجموعه Cipher Suiteهای قابل استفاده را تعیین می‌کنیم.
