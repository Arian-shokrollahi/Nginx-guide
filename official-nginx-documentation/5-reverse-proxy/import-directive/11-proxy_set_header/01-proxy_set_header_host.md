## نقشه راه پیشنهادی ادامه یادگیری Nginx

مرحله ۱: مباحثی که تا الان خوندی

Architecture، Configuration، Directives، Signals، Static Files و مقدمات Reverse Proxy

مطالعه‌شده

مرحله ۲: تکمیل Reverse Proxy

پیشنهاد برای مرحله بعد

- `proxy_set_header`
    
- `Host` و `X-Real-IP`
    
- `X-Forwarded-For` و `X-Forwarded-Proto`
    
- `proxy_redirect`
    
- `proxy_connect_timeout` و `proxy_read_timeout`
    
- `proxy_next_upstream`
    

مرحله ۳: Load Balancing

- `upstream`
    
- Round Robin
    
- Least Connections
    
- IP Hash
    
- `weight`، `backup` و `max_fails`
    
- Passive Health Checks
    

مرحله ۴: HTTPS و SSL/TLS

- `listen 443 ssl`
    
- SSL Certificates
    
- HTTP to HTTPS Redirect
    
- TLS Termination
    

مرحله ۵: امنیت، عملکرد و عیب‌یابی

- Rate Limiting
    
- Access Control
    
- Proxy Caching
    
- Gzip
    
- Custom Log Format
    
- خطاهای 403، 404، 502، 503 و 504
    

این ترتیب با قابلیت‌های مستند Nginx Open Source هم سازگاره. فقط دقت کن Active Health Checks داخلی با دستور `health_check` مربوط به امکانات تجاری NGINX Plus هست؛ در نسخه Open Source می‌تونی ابتدا Passive Health Checks رو یاد بگیری.

NGINX Documentation+2

## الان دقیقاً چی بخونی؟

به نظرم درس بعدیت باید `proxy_set_header` باشه.

چون تا الان یاد گرفتی Nginx درخواست رو به Backend می‌فرسته، اما هنوز باید بفهمی چه اطلاعاتی همراه درخواست منتقل می‌شن.

مثلاً:

```
server {
    listen 8080;
    server_name localhost;

    location /api/ {
        proxy_pass http://127.0.0.1:3000/;

        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

در این کانفیگ:

|Directive|وظیفه|
|---|---|
|`proxy_pass`|ارسال درخواست به Backend|
|`proxy_set_header Host`|انتقال نام Host|
|`X-Real-IP`|ارسال IP کلاینت از دید Nginx|
|`X-Forwarded-For`|انتقال زنجیره IPهای Proxy و کلاینت|
|`X-Forwarded-Proto`|مشخص کردن پروتکل درخواست ورودی|

این هدرها باید با تنظیمات اعتماد به Proxy در Backend هماهنگ باشن تا اطلاعات قابل جعل، به‌اشتباه معتبر فرض نشن.

nginx.org

---
## 3. قبل از proxy_set_header چی یاد بگیری؟

به نظرم این ترتیب عالیه:

1. HTTP Request و HTTP Response
    
2. Request Line و Status Line
    
3. HTTP Headers و HTTP Body
    
4. تفاوت Request Headers و Response Headers
    
5. Headerهای مهم مثل `Host`، `Content-Type`، `Authorization` و `User-Agent`
    
6. بعد وارد `proxy_set_header` در Nginx شو.
    

نکته مهم: لازم نیست کل پروتکل HTTP رو حرفه‌ای یاد بگیری تا بتونی Nginx کار کنی. فقط ساختار درخواست و پاسخ، Headerها و Status Codeها رو خوب بفهم.

بعد از اون، `proxy_set_header` برات خیلی ساده‌تر می‌شه، چون می‌فهمی دقیقاً داری چه اطلاعاتی رو بین Client، Nginx و Backend جابه‌جا می‌کنی.

---
### 4- تست دایرکتیو proxy_set_header Host $host

<p align=center>
	<img src"../../../00-images/test-proxy-set-header-Host.png" alt="images" width=800>
</p>
