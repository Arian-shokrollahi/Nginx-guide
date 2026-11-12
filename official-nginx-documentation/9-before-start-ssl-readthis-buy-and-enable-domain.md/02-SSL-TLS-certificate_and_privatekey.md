# SSL TLS certificate
---
# ۱. Certificate چیست و چه شکلی دارد؟

ا-SSL/TLS Certificate

ا-Certificate یک فایل دیجیتالی است که توسط یک مرجع صدور گواهی (CA) مثل Let's Encrypt صادر و امضا می‌شود.

این فایل به‌صورت عمومی قابل مشاهده است و اطلاعاتی مثل این موارد دارد:

- نام دامنه‌ای که گواهی برای آن صادر شده است.
    
- ا-Public Key یا کلید عمومی.
    
- نام صادرکننده Certificate.
    
- تاریخ شروع و پایان اعتبار.
    
- امضای دیجیتال مرجع صادرکننده.
    

مثلاً Certificate دامنه ما می‌تواند اطلاعات زیر را داشته باشد:

```
Domain: shokbil.ir

Issuer: Let's Encrypt

Public Key: موجود

Valid From: 2026-11-11

Valid Until: 2027-02-09

Signature: موجود
```

این فقط یک مثال نمایشی است، نه محتوای واقعی گواهی.

### شکل فایل Certificate

معمولاً با فرمت PEM ذخیره می‌شود و محتوای آن چیزی شبیه این است:

```
-----BEGIN CERTIFICATE-----

MIIFazCCA1OgAwIBAgISA...

...certificate data...

-----END CERTIFICATE-----
```

این متن در واقع داده‌های رمزگذاری‌شده با Base64 از یک ساختار استاندارد X.509 است.

---
# ۲. Private Key چیست؟

ا-Private Key

ا-
Private Key یک کلید خصوصی و محرمانه است که هنگام ایجاد درخواست Certificate، روی سیستم خودت تولید می‌شود.

این کلید:

- محرمانه است.
    
- باید فقط در اختیار سرور و مدیران مجاز باشد.
    
- برای اثبات مالکیت کلید عمومی متناظر در ارتباط TLS استفاده می‌شود.
    
- نباید در اختیار دیگران قرار بگیرد.
    

### شکل فایل Private Key

معمولاً در قالب PEM ذخیره می‌شود.

مثلاً:

```
-----BEGIN PRIVATE KEY-----

MIIEvQIBADANBgkqhkiG9w0...

...private key data...

-----END PRIVATE KEY-----
```

این یک نمونه ساختاری است؛ کلید واقعی بسیار طولانی‌تر است.

نکته: بسته به الگوریتم، ممکن است ابتدای فایل به‌جای `BEGIN PRIVATE KEY` عبارت `BEGIN RSA PRIVATE KEY` یا `BEGIN EC PRIVATE KEY` باشد.

---
# ۳. تفاوت Certificate و Private Key

این دو را با هم اشتباه نگیر:

|ویژگی|Certificate|Private Key|
|---|---|---|
|نوع کلید|شامل Public Key|کلید خصوصی|
|محرمانگی|عمومی|کاملاً محرمانه|
|صادرکننده|CA آن را امضا می‌کند|روی سیستم تولید می‌شود|
|کاربرد|معرفی هویت دامنه|اثبات مالکیت کلید خصوصی|
|فرمت معمول|PEM|PEM|
|پسوند معمول|`.pem`|`.pem`|

---
# ۴. این دو چطور با هم کار می‌کنند؟

فرض کن کاربری می‌خواهد به وب‌سایت تو متصل شود:

```
https://shokbil.ir
```

ا-Nginx باید Certificate و Private Key متناظر را داشته باشد


<p align=center>
	<img src="../00-images/privatekey and certificate.png" alt="images" width=800>
</p>

یک نکته فنی مهم: در TLS 1.2 و TLS 1.3، در حالت‌های مدرن رایج، از Private Key برای امضای دیجیتال و اثبات مالکیت کلید استفاده می‌شود؛ نه اینکه تمام اطلاعات وب‌سایت مستقیماً با Private Key رمزنگاری شوند.

برای رمزنگاری داده‌های ارتباطی از کلیدهای متقارن نشست (Session Keys) استفاده می‌شود.

---
# ۵. Certbot این دو فایل را کجا ذخیره می‌کند؟

وقتی با Certbot برای دامنه `shokbil.ir` گواهی بگیری، معمولاً این مسیر ساخته می‌شود:

```
/etc/letsencrypt/live/shokbil.ir/
```

محتویات مهم آن:

|فایل|کاربرد|
|---|---|
|`cert.pem`|Certificate دامنه|
|`chain.pem`|گواهی‌های میانی CA|
|`fullchain.pem`|Certificate به همراه Chain|
|`privkey.pem`|Private Key|


---
# ۶. چطور این دو را به Nginx معرفی می‌کنیم؟

در Virtual Host مربوط به HTTPS:

```
server {
    listen 443 ssl;
    server_name shokbil.ir;

    ssl_certificate
        /etc/letsencrypt/live/shokbil.ir/fullchain.pem;

    ssl_certificate_key
        /etc/letsencrypt/live/shokbil.ir/privkey.pem;
}
```

دو Directive مهم:

|Directive|کاربرد|
|---|---|
|`ssl_certificate`|معرفی Certificate و زنجیره گواهی|
|`ssl_certificate_key`|معرفی Private Key متناظر|

دقت کن که Private Key نباید در اختیار مرورگر یا کاربران قرار بگیرد

---
## خلاصه نهایی برای جزوه Linux و Nginx

ا-Certificate

فایل عمومی حاوی اطلاعات هویت دامنه، Public Key و امضای CA.

ا-Private Key

فایل محرمانه‌ای که با Public Key موجود در Certificate جفت است و برای اثبات مالکیت کلید استفاده می‌شود.

ا-Fullchain.pem

شامل Certificate دامنه و گواهی‌های میانی موردنیاز برای اعتبارسنجی زنجیره.

ا-Privkey.pem

کلید خصوصی سرور که باید محرمانه نگهداری شود.

---

