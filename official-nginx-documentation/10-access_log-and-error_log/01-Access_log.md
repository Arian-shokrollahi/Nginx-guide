# Access log
---
## ا-**Access Log در Nginx چیست؟**

دایرکتیو `access_log` برای ثبت اطلاعات مربوط به درخواست‌هایی (HTTP Requests) استفاده می‌شود که کاربران به وب‌سرور Nginx ارسال می‌کنند.

به عبارت ساده، هر زمان کاربری درخواستی به Nginx ارسال کند، اطلاعات مربوط به آن درخواست و پاسخ Nginx می‌تواند در فایلی به نام Access Log ذخیره شود.

این اطلاعات برای بررسی درخواست‌های کاربران، مشاهده Status Codeها و عیب‌یابی مشکلات وب‌سرور استفاده می‌شوند.

---

## **۱. محل تعریف در ساختار Nginx**

دایرکتیو `access_log` در سه Context قابل استفاده است:

- ا-`http`
    
- ا-`server`
    
- ا-`location`
    

مثال:

```
http {

    access_log /var/log/nginx/access.log;

    server {
        listen 80;
        server_name shokbil.ir;

        access_log /var/log/nginx/shokbil_access.log;

        location / {
            root /var/www/html;
        }
    }
}
```

**تفاوت محل تعریف:**

- اگر در `http` تعریف شود، به‌صورت پیش‌فرض برای Server Blockهای زیرمجموعه اعمال می‌شود.
    
- اگر در `server` تعریف شود، برای درخواست‌های همان Virtual Host استفاده می‌شود.
    
- اگر در `location` تعریف شود، می‌توان برای یک مسیر مشخص لاگ جداگانه تعریف کرد.
    

نکته: تنظیمات سطح پایین‌تر می‌توانند تنظیمات به‌ارث‌رسیده را جایگزین کنند.

---
## **۲. ساختار Directive**

```
access_log /var/log/nginx/access.log;
```

- ا-`access_log`: دایرکتیو ثبت درخواست‌ها
    
- ا-`/var/log/nginx/access.log`: مسیر ذخیره فایل لاگ
    

برای غیرفعال کردن ثبت درخواست‌ها:

```
access_log off;
```

---
## **۳. محل ذخیره پیش‌فرض**

در نصب‌های معمول Ubuntu و Debian:

```
/var/log/nginx/access.log
```

برای مشاهده زنده درخواست‌ها:

```
sudo tail -f /var/log/nginx/access.log
```

---

## **۴. چه اطلاعاتی ثبت می‌شود؟**

در فرمت پیش‌فرض `combined`، اطلاعات زیر ثبت می‌شوند:

- ا-IP Address کاربر
    
- زمان ارسال درخواست
    
- ا-HTTP Method
    
- ا-Request URI
    
- ا-HTTP Protocol
    
- ا-HTTP Status Code
    
- حجم پاسخ ارسال‌شده
    
- ا-Referer
    
- ا-User-Agent

---
## 5. نمونه ای از خطوط 

<p align=center>
	<img src="../../00-images/accesslog.png" alt="images" width=800>
</p>


**جمع‌بندی:** `access_log` به ما کمک می‌کند بفهمیم چه درخواست‌هایی به Nginx ارسال شده‌اند، چه پاسخی دریافت کرده‌اند و چه کاربرانی به وب‌سرور متصل شده‌اند.
