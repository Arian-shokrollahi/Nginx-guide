
# لازمه فعال شدن قابلیت Active health check  با نوشتن health_check خالی هم فعال میشه و خودش پیش فرض fails و passes میزاره رو یک و مسیر پیش فرض رو هم میده ولی اگر بخوای همچی خطا ها fails و passes به همون مقداری باشه که خودت میخوای باید دستی اضافشون کنی

- یه مثال نمونه
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

---



## توضیحات تکمیلی و توضیح دو مدل ساده و پر و پیمون

اگر فقط بنویسی:

```
health_check;
```

ا-NGINX Plus از مقادیر پیش‌فرض استفاده می‌کند:

- ا-`interval=5s` → هر ۵ ثانیه بررسی سلامت انجام می‌شود.
    
- ا-`fails=1` → یک شکست برای Unhealthy شدن کافی است.
    
- ا-`passes=1` → یک موفقیت برای Healthy شدن مجدد کافی است.
    
- ا-`uri=/` → مسیر پیش‌فرض بررسی سلامت است.
    

اگر بخواهی رفتار Health Check را کنترل کنی، پارامترهای موردنظرت را دستی مشخص می‌کنی:

```
health_check interval=10s fails=3 passes=2 uri=/health;
```

یعنی:

- هر ۱۰ ثانیه بررسی کن.
    
- بعد از ۳ شکست متوالی سرور را Unhealthy کن.
    
- بعد از ۲ موفقیت متوالی دوباره Healthy کن.
    
- مسیر `/health` را بررسی کن.
    

یک نکته: این مقادیر پیش‌فرض و رفتارها مربوط به Active HTTP Health Check در NGINX Plus هستند. همچنین `health_check` به‌تنهایی نیاز به تعریف `zone` در Upstream دارد.

---

