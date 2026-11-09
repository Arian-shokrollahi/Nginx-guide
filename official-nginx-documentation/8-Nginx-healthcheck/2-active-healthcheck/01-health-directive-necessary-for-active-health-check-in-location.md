# Health check directive
---
## ۱. `health_check` چیست؟

دستور `health_check`، Directive<mark> اصلی برای فعال‌سازی قابلیت Active Health Check </mark>در NGINX Plus است.

با استفاده از این Directive، NGINX به‌صورت دوره‌ای درخواست‌های آزمایشی (Health Check Requests) را به سرورهای Backend ارسال می‌کند و پاسخ آن‌ها را بررسی می‌کند تا مشخص شود آیا سرورها سالم و آماده پردازش درخواست هستند یا خیر.

-- 
به زبان ساده، این Directive به NGINX می‌گوید:

«وضعیت سرورهای Backend را به‌صورت خودکار بررسی کن و سرورهایی را که شرایط سلامت را ندارند، از چرخه توزیع ترافیک خارج کن.»

این قابلیت در NGINX Plus ارائه می‌شود و در NGINX Open Source وجود ندارد.


---
## ۲. آیا `health_check` یک Directive است یا Parameter؟

برخلاف `interval` که یک Parameter است، `health_check` خودش یک Directive محسوب می‌شود.

ساختار اصلی آن:

```
health_check;
```

می‌توانیم پارامترهای مختلفی را نیز به آن اضافه کنیم:

```
health_check interval=5s fails=3 passes=2;
```

در این مثال:

- ا-`health_check`: فعال‌سازی Active Health Check
    
- ا-`interval`: فاصله زمانی بررسی‌ها
    
- ا-`fails`: تعداد شکست‌های متوالی
    
- ا-`passes`: تعداد موفقیت‌های متوالی
    

بنابراین `health_check` دستور اصلی است و پارامترهای دیگر رفتار آن را کنترل می‌کنند.

---
## ۳. ساختار و محل قرارگیری Directive

ساختار کلی:

```
health_check [parameters];
```

مشخصات Directive

|ویژگی|مقدار|
|---|---|
|Type|Directive|
|Module|`ngx_http_upstream_hc_module`|
|Context|`location`|
|Default|غیرفعال|
|Availability|NGINX Plus|
|Purpose|فعال‌سازی Active Health Check|

این Directive باید در Context مربوط به `location` قرار بگیرد؛ یعنی همان بخشی که درخواست‌ها را با `proxy_pass` به گروه Upstream ارسال می‌کند.

همچنین برای استفاده از Active Health Check در HTTP باید در بلوک `upstream` یک Shared Memory Zone با Directive به نام `zone` تعریف شود.

---
ا-<mark>`zone` یک Shared Memory Zone است که NGINX برای ذخیره و به‌اشتراک‌گذاری اطلاعات مربوط به </mark>وضعیت Upstream Serverها بین Worker Processها استفاده می‌کند.

مثال:

```
upstream backend {
    zone backend 64k;
    server 192.168.1.10;
    server 192.168.1.11;
}
```

- ا-`backend`: نام حافظه مشترک
    
- ا-`64k`: حجم حافظه اختصاص‌یافته
    

نکته: <mark>در Active Health Check نسخه NGINX Plus، تعریف `zone` برای Upstream ضروری است.</mark>

---
## ۴. مثال ساده از استفاده

فرض کن دو سرور Backend داری:

```
http {

    upstream backend {
        zone backend 64k;

        server 192.168.1.10:8080;
        server 192.168.1.11:8080;
    }

    server {
        listen 80;

        location / {
            proxy_pass http://backend;

            health_check;
        }
    }
}
```

در این مثال چه اتفاقی می‌افتد؟

ا-1. NGINX درخواست‌های کاربران را به گروه `backend` ارسال می‌کند.
    
2. با فعال شدن `health_check`، بررسی سلامت سرورها آغاز می‌شود.
    
ا-3. NGINX به‌صورت پیش‌فرض هر ۵ ثانیه یک درخواست آزمایشی به هر سرور ارسال می‌کند.
    
4. مسیر پیش‌فرض بررسی `/` است.
    
5. اگر ارتباط با سرور با خطا یا Timeout مواجه شود، یا پاسخ HTTP خارج از محدوده 200 تا 399 باشد، بررسی ناموفق محسوب می‌شود.
    
6. سرور Unhealthy از دریافت درخواست‌های جدید کاربران کنار گذاشته می‌شود تا دوباره بررسی سلامت را با موفقیت پشت سر بگذارد
---
## ۵. تنظیمات در محیط Production

در محیط Production بهتر است `health_check` را بدون بررسی دقیق Endpoint برنامه فعال نکنی.

برای مثال، فرض کن برنامه تو یک Endpoint اختصاصی به نام `/health` دارد که وضعیت آمادگی برنامه را بررسی می‌کند.

می‌توانیم تنظیمات زیر را داشته باشیم:

```
http {

    upstream backend {
        zone backend 64k;

        server 192.168.1.10:8080;
        server 192.168.1.11:8080;
    }

    server {
        listen 80;

        location / {
            proxy_pass http://backend;

            health_check
                interval=5s
                fails=3
                passes=2
                uri=/health;
        }
    }
}
```

در این تنظیمات:

- هر ۵ ثانیه سلامت سرورها بررسی می‌شود.
    
- پس از ۳ شکست متوالی، سرور Unhealthy می‌شود.
    
- برای بازگشت به وضعیت Healthy باید ۲ بررسی موفق متوالی داشته باشد.
    
- بررسی سلامت از طریق Endpoint اختصاصی `/health` انجام می‌شود.
    

توصیه‌های مهم برای Production:

- ا-Endpoint سلامت را متناسب با وضعیت واقعی برنامه طراحی کن.
    
- بررسی سلامت نباید عملیات سنگین یا پرهزینه انجام دهد.
    
- اگر دیتابیس برای عملکرد برنامه ضروری است، وضعیت اتصال به آن را نیز در Readiness Check در نظر بگیر.
    
- تعداد Backendها و تعداد Health Checkها را در محاسبه بار سیستم لحاظ کن.
    
- برای جلوگیری از ارسال ترافیک به سرور تازه‌اضافه‌شده قبل از اولین بررسی موفق، استفاده از `mandatory` را بررسی کن.

----

