## **۱. Certificate Revocation چیست؟**

فرض کن برای دامنه `shokbil.ir` یک Certificate از Let's Encrypt دریافت کرده‌ای که تا ۹۰ روز اعتبار دارد.

اما بعد از مدتی متوجه می‌شوی Private Key مربوط به این Certificate افشا شده است.

در این شرایط، صرفاً حذف فایل Certificate از سرور کافی نیست؛ چون ممکن است نسخه‌ای از آن همچنان در اختیار افراد دیگر باشد.

بنابراین باید از طریق Certificate Authority یا همان CA درخواست ابطال Certificate را ارسال کنیم.

به این فرایند می‌گوییم:

**Certificate Revocation**

## **۲. چه زمانی Certificate را باطل می‌کنیم؟**

مهم‌ترین دلایل عبارت‌اند از:

|Reason|توضیح|
|---|---|
|`keycompromise`|افشای Private Key|
|`superseded`|جایگزین شدن Certificate|
|`cessationofoperation`|توقف فعالیت سرویس|
|`unspecified`|دلیل نامشخص|

مثلاً اگر Private Key سرور به سرقت برود، باید Certificate مربوط به آن را باطل کنیم و یک Certificate جدید با کلید جدید صادر کنیم.

## **۳. چگونه با Certbot گواهی را باطل کنیم؟**

ابتدا گواهی‌های موجود را مشاهده می‌کنیم:

```
sudo certbot certificates
```

سپس دستور ابطال:

```
sudo certbot revoke \
--cert-path /etc/letsencrypt/live/shokbil.ir/cert.pem
```

در این دستور:

- `certbot`: اجرای ابزار Certbot
    
- `revoke`: درخواست ابطال Certificate
    
- `--cert-path`: مشخص کردن مسیر Certificate موردنظر
    

برای تعیین دلیل ابطال نیز می‌توانیم از `--reason` استفاده کنیم:

```
sudo certbot revoke \
--cert-path /etc/letsencrypt/live/shokbil.ir/cert.pem \
--reason keycompromise
```

در این مثال به Let's Encrypt اعلام می‌کنیم که دلیل ابطال، افشای Private Key است.

## **۴. بعد از Revocation چه اتفاقی می‌افتد؟**

**Valid Certificate**

معتبر

**Certbot Revoke**

ارسال درخواست ابطال به CA

**Revoked Certificate**

باطل‌شده

پس از ابطال، CA وضعیت گواهی را Revoked اعلام می‌کند. کلاینت‌هایی که وضعیت ابطال را بررسی می‌کنند، باید این گواهی را رد کنند؛ البته نحوه و زمان اطلاع از ابطال به سازوکار بررسی وضعیت Certificate بستگی دارد.

**نکته مهم:** ابطال Certificate به معنی حذف خودکار آن از Nginx نیست. ممکن است Nginx همچنان آن را ارائه کند، ولی دیگر یک گواهی معتبر محسوب نمی‌شود.

## **۵. تفاوت Renewal و Revocation**

|Renewal|Revocation|
|---|---|
|تمدید گواهی|ابطال گواهی|
|برای ادامه استفاده|برای خارج کردن از اعتبار|
|قبل از انقضا|معمولاً در شرایط امنیتی یا عملیاتی|
|صدور گواهی جایگزین|لغو اعتبار گواهی فعلی|

## **۶. نکته مهم در محیط Production**

هشدار

فعلاً دستور Revoke را روی Certificate واقعی `shokbil.ir` اجرا نکن!

چون این عملیات واقعی است و باطل کردن گواهی Production می‌تواند باعث بروز خطای اعتبارسنجی HTTPS برای کاربران شود.

اگر Private Key واقعاً افشا شده باشد، باید گواهی را باطل کنی، کلید جدید بسازی و Certificate جدید صادر کنی.

## **۷. تمرین پیشنهادی**

برای اینکه این مبحث را مثل دو مرحله قبلی به‌صورت عملی یاد بگیری، پیشنهاد می‌کنم یک Certificate آزمایشی از Let's Encrypt Staging صادر کنیم و سپس همان را باطل کنیم.

در این تمرین یاد می‌گیری:

1. صدور Certificate آزمایشی
    
2. مشاهده مشخصات Certificate
    
3. اجرای Certificate Revocation
    
4. بررسی نتیجه ابطال
    
5. تفاوت Certificate معتبر و Revoked
    

**به این ترتیب سه بخش اصلی چرخه عمر Certificate را یاد گرفته‌ای:**

**1. Certificate Issuance**

مطالعه شد

**2. Certificate Renewal**

مطالعه شد

**3. Certificate Revocation**

مفهوم آموزش داده شد

بعد از این سه مبحث، موضوع مهم بعدی برای تسلط روی SSL در Production، شناخت **Certificate Chain of Trust، OCSP و CRL** است.
