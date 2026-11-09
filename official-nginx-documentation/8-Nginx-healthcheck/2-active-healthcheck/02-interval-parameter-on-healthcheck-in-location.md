# Interval parameter for nginx plus
---
## ۱. `interval` چیست؟

پارامتر `interval` مشخص می‌کند NGINX Plus هر چند ثانیه یک‌بار درخواست آزمایشی (Health Check Request) به سرورهای Backend ارسال کند تا وضعیت سلامت آن‌ها را بررسی کند.

به زبان ساده، `interval` فاصله زمانی بین بررسی‌های متوالی سلامت سرور را تعیین می‌کند.

مثلاً اگر مقدار آن ۵ ثانیه باشد، NGINX Plus بررسی سلامت سرورها را با فاصله زمانی ۵ ثانیه انجام می‌دهد.

طبق مستندات رسمی NGINX، مقدار پیش‌فرض این پارامتر ۵ ثانیه است.

---

## ۲. آیا `interval` یک Directive است یا Parameter؟

این نکته مهم است:

ا-`interval` خودش یک Directive مستقل نیست؛ بلکه یک Parameter متعلق به Directive اصلی `health_check` است.

ا-Directive اصلی:

```
health_check;
```

استفاده از پارامتر interval:

```
health_check interval=10s;
```

بنابراین:

- `health_check` = Directive
    
- `interval` = Parameter

---

## ۳. ساختار و محل قرارگیری

ساختار استفاده از آن به شکل زیر است:

```
health_check interval=time;
```

در NGINX HTTP، این Directive در Context مربوط به `location` قرار می‌گیرد؛ یعنی در همان بخشی که درخواست‌ها را به Upstream ارسال می‌کنی.

مثال:

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

            health_check interval=10s;
        }
    }
}
```

در این مثال، NGINX Plus هر ۱۰ ثانیه سرورهای Backend این Upstream را بررسی می‌کند.

نکته: برای فعال شدن Active Health Check در HTTP، تعریف `zone` در بلوک `upstream` نیز ضروری است.

---
## ۴. بررسی عملکرد `interval` با مثال

فرض کن مقدار زیر را تنظیم کرده‌ایم:

```
health_check interval=5s;
```

در این حالت، بررسی‌ها تقریباً به این ترتیب انجام می‌شوند:

<p align=center>
	<img src="../00-images/intervalhealthcheck1.png" alt="images" width=800>
</p>
### اگر مقدار interval را تغییر دهیم چه اتفاقی می‌افتد؟

|مقدار|رفتار|
|---|---|
|`1s`|بررسی بسیار پرتکرار|
|`5s`|بررسی نسبتاً سریع و مقدار پیش‌فرض|
|`10s`|بررسی با فاصله بیشتر|
|`30s`|بررسی کم‌تکرارتر|

هرچه مقدار `interval` کمتر باشد، NGINX زودتر فرصت بررسی مجدد وضعیت سرور را پیدا می‌کند؛ اما تعداد درخواست‌های Health Check نیز افزایش پیدا می‌کند.

---
## ۵. تنظیم `interval` در محیط Production

در محیط Production نمی‌توانیم برای همه پروژه‌ها یک مقدار ثابت پیشنهاد کنیم. انتخاب آن به حساسیت سرویس، تعداد Backendها، ظرفیت سرورها و زمان تشخیص خرابی موردنیاز بستگی دارد.

برای شروع می‌توانیم از این مقادیر به‌عنوان نقطه شروع استفاده کنیم:

|نوع سرویس|مقدار پیشنهادی اولیه|
|---|---|
|سرویس‌های حساس و پرترافیک|2–5s|
|سرویس‌های معمولی|5–10s|
|سرویس‌های کم‌اهمیت‌تر|10–30s|

این‌ها پیشنهادهای اولیه برای طراحی و تست هستند، نه استانداردهای رسمی NGINX.

مثال پیشنهادی برای شروع در Production:

```
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
```

در این تنظیمات:

- `interval=5s`: هر ۵ ثانیه یک بررسی.
    
- `fails=3`: سه شکست متوالی برای Unhealthy شدن.
    
- `passes=2`: دو موفقیت متوالی برای Healthy شدن مجدد.
    
- `uri=/health`: مسیر بررسی سلامت برنامه.
    

نکته مهم Production: زمان تشخیص خرابی فقط به `interval` بستگی ندارد؛ تعداد شکست‌های متوالی (`fails`) و مدت انتظار برای پاسخ Health Check نیز تأثیرگذارند. بنابراین باید مجموع زمان تشخیص خرابی را با SLA سرویس هماهنگ کنی.

---
## ۶. خلاصه مناسب برای یادداشت آموزشی

interval Parameter

- Type: Parameter
    
- Parent Directive: `health_check`
    
- Context: `location` (HTTP)
    
- Default: `5 seconds`
    
- Purpose: Defines the time interval between consecutive health checks.
    
- Availability: NGINX Plus
    
- Production Consideration: Balance failure detection speed against health-check traffic and resource overhead.
