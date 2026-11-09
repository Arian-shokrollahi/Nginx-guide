# Fails and passes parameter in health check
---

### ۱. `fails`

یک Parameter از Directive `health_check` است که تعداد شکست‌های متوالی Health Check را مشخص می‌کند تا NGINX سرور را Unhealthy تشخیص دهد.

مثال:

```
health_check fails=3;
```

یعنی اگر ۳ بررسی متوالی ناموفق باشد، سرور Unhealthy می‌شود.

مقدار پیش‌فرض: 1

---

### ۲. `passes`

مشخص می‌کند چند بررسی موفق متوالی لازم است تا سروری که قبلاً Unhealthy شده، دوباره Healthy شناخته شود.

مثال:

```
health_check passes=2;
```

یعنی سرور باید ۲ بررسی متوالی موفق داشته باشد تا دوباره وارد چرخه توزیع ترافیک شود.

مقدار پیش‌فرض: 1

---

### تفاوت در یک نگاه

|Parameter|وظیفه|
|---|---|
|`fails`|تعیین زمان خارج شدن سرور از چرخه ترافیک|
|`passes`|تعیین زمان بازگشت سرور به چرخه ترافیک|

مثال ترکیبی:

```
health_check interval=5s fails=3 passes=2;
```

یعنی هر ۵ ثانیه بررسی انجام شود، پس از ۳ شکست متوالی سرور Unhealthy شود و برای بازگشت به وضعیت Healthy به ۲ موفقیت متوالی نیاز داشته باشد.

---
پارامترهای `fails` و `passes` هر دو متعلق به Directive اصلی `health_check` هستند و داخل همان Directive قرار می‌گیرند.

### ۱. ساختار

```
health_check fails=3 passes=2;
```

- ا-`fails=3`: سه شکست متوالی برای Unhealthy شدن سرور.
    
- ا-`passes=2`: دو موفقیت متوالی برای Healthy شدن مجدد سرور.
    

### ۲. محل قرارگیری

هر دو شکل درسته. اما بهتره برای خوانایی، هر سه پارامتر رو داخل یک Directive بنویسی:

```
location / {
    proxy_pass http://backend;

    health_check interval=5s fails=3 passes=2;
}
```

یا چندخطی:

```
location / {
    proxy_pass http://backend;

    health_check
        interval=5s
        fails=3
        passes=2;
}
```

پیشنهاد من: شکل چندخطی برای Production خواناتر و نگهداریش راحت‌تره.
