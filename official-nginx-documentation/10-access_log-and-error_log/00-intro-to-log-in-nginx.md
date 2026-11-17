# logging in nginx
---
## برایه loging درون nginx ما دو دایرکتیو داریم:
- ا- `access_log`: ثبت درخواست‌ها و پاسخ‌های HTTP.
    
2. ا-`error_log`: ثبت خطاها و پیام‌های عملیاتی Nginx.
نمونه تنظیم Production:

```
http {
    access_log /var/log/nginx/access.log;
    error_log /var/log/nginx/error.log warn;
}
```

همین دو دایرکتیو، پایه اصلی Logging در Nginx هستند. ✅

مثلاً:

```
server {
    listen 443 ssl;
    server_name shokbil.ir;

    access_log /var/log/nginx/shokbil_access.log;
    error_log /var/log/nginx/shokbil_error.log warn;
}
```

**تفاوت محل تعریف:**

- در `http`: تنظیمات به‌صورت پیش‌فرض برای تمام Server Blockها اعمال می‌شوند.
    
- در `server`: تنظیمات مربوط به همان Virtual Host هستند.
    
- در `location`: می‌توانی برای یک مسیر خاص تنظیم جداگانه داشته باشی.
    

**پیشنهاد من برای Production:** اگر می‌خواهی لاگ هر دامنه را جداگانه مدیریت کنی، تعریف این دو Directive داخل `server` انتخاب مناسبی است.
