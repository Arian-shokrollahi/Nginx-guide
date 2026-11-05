# proxy_redirect  
---
### 1- این  دایرکتیو چیکار میکند اگر بخوام به صورت تصویری نشان بدم

<p align=center>
	<img src="../../../../00-images/Nginx Proxy Redirect Explained.png" alt="images" width=800>
</p>

دستور `proxy_redirect` در Nginx آدرس‌های داخلی Backend را در هدرهای Redirect مثل `Location` اصلاح می‌کند تا IP و پورت داخلی Backend برای کاربر افشا نشود.

خلاصه: آدرس Redirect در پاسخ Backend را به آدرس مناسب سمت کاربر تبدیل می‌کند همانطور که مشاهده میکنید

---

### ۲-جایگاه دایرکتیو redirect

دستور `proxy_redirect` داخل بلوک `location` قرار می‌گیرد؛ درست کنار `proxy_pass` و سایر دستورهای مربوط به Proxy.

ساختار کلی:

```
server {
    listen 80;

    location / {
        proxy_pass http://127.0.0.1:8082;

        proxy_redirect http://127.0.0.1:8082/ /;
    }
}
```

جایگاه دستور:

- `server`: تنظیمات سرور
    
- `location`: تعیین مسیر درخواست
    
- `proxy_pass`: ارسال درخواست به Backend
    
- `proxy_redirect`: اصلاح آدرس Redirect در پاسخ Backend
    

نکته: `proxy_redirect` را می‌توان در سطح `server` هم قرار داد؛ اما معمولاً کنار `proxy_pass` در `location` قرار می‌دهیم تا تنظیمات خواناتر باشند.


---

### ۳- اهمیت این دایرکتیو در محیط پروداکشن

اهمیت `proxy_redirect` در محیط Production متوسط و وابسته به معماری برنامه است.

دلایل اهمیت:

1. جلوگیری از افشای IP و پورت داخلی Backend در هدرهای `Location`.
    
2. جلوگیری از Redirect شدن کاربر به آدرس داخلی یا غیرقابل‌دسترسی.
    
3. حفظ درست مسیر هدایت کاربر هنگام استفاده از Reverse Proxy.
    

نکته: اگر Backend از ابتدا Redirectهای درست و سازگار با آدرس عمومی تولید کند، ممکن است اصلاً نیازی به `proxy_redirect` نداشته باشیم.
