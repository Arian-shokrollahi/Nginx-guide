

دایرکتیو مربوط به فعال‌سازی Directory Listing در Nginx این است:

```
autoindex on;
```

برای مثال:

```
server {
    listen 80;
    server_name example.com;

    location /files/ {
        alias /var/www/files/;
        autoindex on;
    }
}
```

**حالت‌های این دایرکتیو:**

- `autoindex on;` نمایش لیست فایل‌ها و پوشه‌ها
    
- `autoindex off;` 
غیرفعال کردن نمایش لیست (حالت پیش‌فرض)
چند دایرکتیو مرتبط هم داریم:

```
autoindex_exact_size off;
autoindex_localtime on;
autoindex_format html;
```

- `autoindex_exact_size`: نمایش اندازه دقیق یا خواناتر فایل‌ها.
    
- `autoindex_localtime`: نمایش زمان فایل بر اساس Local Time سرور.
    
- `autoindex_format`: تعیین فرمت خروجی مثل HTML یا JSON یا XML.
    

**نکته Production:** <MARK>معمولاً `autoindex off` نگه داشته می‌شود تا ساختار پوشه‌ها و فایل‌های سرور افشا نشود.</MARK>
