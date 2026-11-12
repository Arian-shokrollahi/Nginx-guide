# در این فایل درمورد این توضیح میدیم که چرا ما اصلا از چند تا کانفیگ استفاده میکنیم به چه دلیل 

---
## ۱. چرا یک سرور باید چند Virtual Host داشته باشد؟

فرض کن یک سرور لینوکسی داری که Nginx روی آن نصب است و می‌خواهی سه وب‌سایت را روی همان سرور میزبانی کنی:

- `google.example.com`
    
- `shop.example.com`
    
- `blog.example.com`
    

هر سه وب‌سایت می‌توانند روی یک IP و یک Nginx Server اجرا شوند.

اما طبیعتاً هرکدام:

- فایل‌های متفاوتی دارند.
    
- محتوای متفاوتی نمایش می‌دهند.
    
- ممکن است Backend متفاوتی داشته باشند.
    
- تنظیمات امنیتی و SSL متفاوتی داشته باشند.
    

پس برای هرکدام یک Virtual Host تعریف می‌کنیم.

---

## ۲. ساختار sites-available چگونه می‌شود؟

```
/etc/nginx/
│
├── nginx.conf
│
├── sites-available/
│   ├── google.conf
│   ├── shop.conf
│   └── blog.conf
│
└── sites-enabled/
    ├── google.conf -> ../sites-available/google.conf
    ├── shop.conf   -> ../sites-available/shop.conf
    └── blog.conf   -> ../sites-available/blog.conf
```

دقت کن که هر سه سایت فعال هستند!

یعنی Nginx هم‌زمان هر سه Virtual Host را مدیریت می‌کند.

---

## ۳. Nginx از کجا می‌فهمد کدام سایت را نمایش دهد؟

این قسمت خیلی مهم است!

فرض کن هر سه سایت روی پورت 80 و یک IP قرار دارند.

وقتی کاربر وارد این آدرس می‌شود:

```
http://shop.example.com
```

مرورگر در درخواست HTTP، نام دامنه را در Header به سرور ارسال می‌کند:

```
GET / HTTP/1.1
Host: shop.example.com
```

Nginx مقدار `Host` را بررسی می‌کند و آن را با `server_name` در Virtual Hostها مقایسه می‌کند.

مثلاً:

فایل google.conf

```
server {
    listen 80;
    server_name google.example.com;

    root /var/www/google;
}
```

فایل shop.conf

```
server {
    listen 80;
    server_name shop.example.com;

    root /var/www/shop;
}
```

فایل blog.conf

```
server {
    listen 80;
    server_name blog.example.com;

    root /var/www/blog;
}
```

بنابراین Nginx بر اساس نام دامنه تشخیص می‌دهد که درخواست متعلق به کدام Virtual Host است.


---
## ۴. پس sites-enabled چه کاربردی دارد؟

حالا فرض کن فعلاً نمی‌خواهی وب‌سایت Blog در دسترس باشد.

فقط Symlink آن را حذف می‌کنی:

```
sudo rm /etc/nginx/sites-enabled/blog.conf
```

سپس:

```
sudo nginx -t
sudo systemctl reload nginx
```

حالا:

- Google فعال است.
    
- Shop فعال است.
    
- Blog غیرفعال است.
    

اما فایل اصلی Blog همچنان در `sites-available` وجود دارد.

هر زمان خواستی دوباره فعالش کنی:

```
sudo ln -s /etc/nginx/sites-available/blog.conf \
/etc/nginx/sites-enabled/blog.conf
```

این همان مزیت اصلی این ساختار است.

---

## ۵. آیا ممکن است چند Virtual Host برای یک دامنه داشته باشیم؟

بله، اما باید با دقت انجام شود.

مثلاً ممکن است بخواهی برای یک دامنه، نسخه Production و Staging داشته باشی:

```
example.com
staging.example.com
```

این‌ها دو Virtual Host جدا هستند.

اما اگر دو Server Block یکسان با `listen` و `server_name` مشابه تعریف کنی، ممکن است تداخل ایجاد شود و Nginx یکی از آن‌ها را نادیده بگیرد یا رفتار ناخواسته‌ای داشته باشد.

---
### جمع بندی

ا-<mark> پس دلیل اصلی به این دلیل شد که امکان دارد شما چندتا سایت بخوای رویه یک دونه سرور بیاری بالا </mark> 
