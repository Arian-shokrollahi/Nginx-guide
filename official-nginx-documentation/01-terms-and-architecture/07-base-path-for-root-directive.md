# base path
## requirements:
- URI path(uniform resource identifer)
- URL(uniform resourse locator)
- this section relating to root directive

---
### یه مثال که بفهمید چی میشه
فرض کن در مرورگر گوگل این آدرس رو وارد می‌کنی:

```
https://example.com/images/logo.png
```

- URL: `https://example.com/images/logo.png`
    
- Domain: `example.com`
    
- URI Path: `/images/logo.png`
    

حالا تنظیمات Nginx:

```
root /var/www/html;
```

- Base Path: `/var/www/html`
    
- چیزی که به Base Path اضافه می‌شود: `/images/logo.png`
    

پس:

```
Base Path        /var/www/html
                       +
URI Path         /images/logo.png
                       =
Final File Path  /var/www/html/images/logo.png
```

نکته طلایی: در `root`، فقط قسمت Path از URI به Base Path اضافه می‌شود، نه دامنه و نه پروتکل `https`.
