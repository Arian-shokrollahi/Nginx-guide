# Certificate issuance (صدور گواهی نامه)
---
### **مرحله ۱. آماده‌سازی DNS و Nameserver**

ابتدا باید DNS دامنه توسط Cloudflare مدیریت شود.

در مورد دامنه تو:

- دامنه در NIC.ir ثبت شده است.
    
- ا-Nameserverهای Cloudflare در NIC.ir به‌عنوان Nameserver دامنه تنظیم شده‌اند.
    
- مدیریت رکوردهای DNS از طریق Cloudflare انجام می‌شود.
    

نکته: لازم نیست خود دامنه را به Cloudflare منتقل کنی؛ فقط مدیریت DNS آن را به Cloudflare واگذار می‌کنی.

---

### **مرحله ۲. ساخت API Token در Cloudflare**

وارد داشبورد Cloudflare شو:

[Cloudflare API Tokens](https://dash.cloudflare.com/profile/api-tokens?utm_source=chatgpt.com)

یک API Token با دسترسی محدود بساز.

مجوز موردنیاز:

|Permission|Access|
|---|---|
|Zone|DNS: Edit|
|Zone Resources|Include: Specific zone|
|Zone|shokbil.ir|

این Token به Certbot اجازه می‌دهد رکوردهای DNS موردنیاز برای اعتبارسنجی دامنه را ایجاد و حذف کند.

---

### **مرحله ۳. ذخیره API Token در سرور**

فایل زیر را ایجاد می‌کنیم:

```
sudo nano /etc/letsencrypt/cloudflare.ini
```

محتوای فایل:

```
dns_cloudflare_api_token = YOUR_CLOUDFLARE_API_TOKEN
```

به‌جای مقدار نمونه، Token واقعی را قرار می‌دهیم.

سپس دسترسی فایل را محدود می‌کنیم:

```
sudo chmod 600 /etc/letsencrypt/cloudflare.ini
```

این کار باعث می‌شود فقط مالک فایل (در اینجا root) بتواند آن را بخواند و تغییر دهد.


---


### **مرحله ۴. اجرای Certificate Issuance**

دستور اصلی:

```
sudo certbot certonly \
--dns-cloudflare \
--dns-cloudflare-credentials /etc/letsencrypt/cloudflare.ini \
-d shokbil.ir
```

معنی پارامترها:

|پارامتر|وظیفه|
|---|---|
|`certbot`|اجرای ابزار Certbot|
|`certonly`|صدور گواهی بدون نصب خودکار در Web Server|
|`--dns-cloudflare`|استفاده از Cloudflare DNS Plugin|
|`--dns-cloudflare-credentials`|تعیین مسیر API Token|
|`-d shokbil.ir`|تعیین دامنه موردنظر|

این Plugin به‌صورت خودکار رکورد TXT مربوط به ACME Challenge را ایجاد می‌کند و پس از اعتبارسنجی آن را حذف می‌کند.

---

### **مرحله ۵. فرایند اعتبارسنجی DNS-01**

**Certbot**

درخواست صدور گواهی

**Cloudflare API**

ایجاد رکورد TXT

**_****acme-challenge.shokbil.ir**

رکورد TXT برای اثبات کنترل دامنه

**Let's Encrypt**

بررسی و تأیید DNS Challenge

**Certificate Issuance**

صدور موفق گواهی

---
### **مرحله ۶. دریافت فایل‌های Certificate**

پس از صدور موفق، Certbot فایل‌ها را در این مسیر قرار می‌دهد:

```
/etc/letsencrypt/live/shokbil.ir/
```

چهار فایل اصلی:

|فایل|کاربرد|
|---|---|
|`cert.pem`|گواهی دامنه|
|`chain.pem`|گواهی‌های واسط|
|`fullchain.pem`|گواهی دامنه به همراه زنجیره واسط|
|`privkey.pem`|کلید خصوصی|

نکته: این مسیر معمولاً شامل Symbolic Linkهایی به فایل‌های نسخه‌دار داخل `archive` است.

---
## **۷. فعال‌سازی HTTPS در Nginx**

حالا از دو فایل اصلی استفاده می‌کنیم:

```
server {
    listen 443 ssl;
    server_name shokbil.ir;

    ssl_certificate /etc/letsencrypt/live/shokbil.ir/fullchain.pem;

    ssl_certificate_key /etc/letsencrypt/live/shokbil.ir/privkey.pem;
}
```

سپس:

```
sudo nginx -t
sudo systemctl reload nginx
```

---

## **یک نکته مهم برای حرفه‌ای شدن**

در این روش، Certbot خودش Private Key را تولید می‌کند و لازم نیست حتماً CSR را دستی بسازی. در واقع این فرایند با ACME انجام می‌شود و مدیریت کلید و درخواست صدور بر عهده Certbot است.

همچنین چون از DNS-01 استفاده می‌کنی، برای اعتبارسنجی دامنه نیازی به باز بودن پورت 80 نداری و حتی می‌توانی Wildcard Certificate هم صادر کنی:

```
-d shokbil.ir -d '*.shokbil.ir'
```
