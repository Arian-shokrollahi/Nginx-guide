## 0 تا 100 خرید DNS و فعال کردن اون HTTPs و certbot پارت دوم
---
حالا وارد بخش دوم پروژه می‌شویم: نصب Certbot، دریافت گواهی SSL از Let's Encrypt و فعال‌سازی HTTPS روی Nginx.

با توجه به اینکه:

- سیستم‌عامل ما Ubuntu 24.04 است.
    
- وب‌سرور ما Nginx است.
    
- دامنه ما `shokbil.ir` است.
    
- ا-DNS را به Cloudflare سپرده‌ایم.
    
- ا-API Token را ساخته‌ایم.
    
- ا-Certbot هنوز نصب نشده است.
    
این مسیر را از مرحله ۱۱ ادامه می‌دهیم.

---

ا-<mark> یازدهمین مرحله</mark> نصب certbot

- ۱-مخازنتون رو بروز کنید 

```
sudo apt update
```


- ۲-نصب certbot و پلاگین cloudflare

```
sudo apt install certbot python3-certbot-dns-cloudflare
```

چرا دو پکیج؟

|پکیج|کاربرد|
|---|---|
|`certbot`|دریافت و تمدید Certificate|
|`python3-certbot-dns-cloudflare`|اتصال Certbot به Cloudflare API|
- ۳-برسی نصب و نسخه اون 

```
certbot --version
```

- ۴-برسی پلاگین ها 

```
sudo certbot plugins
```

باید این پلاگین را ببینی:

```
dns-cloudflare
```

---
ا-<mark> دوازدهم </mark>  ما یه توکنی درست کردن بودیم به certbot-dns-cloudflare دیگر نیازی به ساخت توکن جدید نداریم و ما از همان توکن نیز استفاده میکنیم


##### نصب افزونه Cloudflare

چون Certbot را با Snap نصب کرده‌ای، ابتدا این دستور را اجرا کن:

```
sudo snap set certbot trust-plugin-with-root=ok
```

این دستور چه کاری می‌کند؟

به Snap اعلام می‌کند که به افزونه Certbot اجازه می‌دهیم با سطح دسترسی لازم اجرا شود؛ چون افزونه DNS باید بتواند با دسترسی مدیریتی کار کند.

حالا دستور دوم:

```
sudo snap install certbot-dns-cloudflare
```

این دستور افزونه Cloudflare را نصب می‌کن

----
ا-<mark>سیزدهم</mark> ساخت فایل credentilas؟
## فایل Credentials چیست؟

کلمه `Credentials` یعنی اطلاعات احراز هویت.

در پروژه ما، فایل Credentials محلی است که API Token کلادفلر را داخل آن نگهداری می‌کنیم تا Certbot بتواند هویت خودش را نزد Cloudflare ثابت کند.

### چرا به این فایل نیاز داریم؟

کلمه `Credentials` یعنی اطلاعات احراز هویت.

فرض کن Certbot می‌خواهد برای دامنه `shokbil.ir` گواهی SSL بگیرد.

برای این کار باید به Cloudflare درخواست بدهد:

«لطفاً یک رکورد TXT برای تأیید مالکیت دامنه ایجاد کن.»

اما Cloudflare از کجا بفهمد این درخواست از طرف یک شخص مجاز است؟

اینجا API Token وارد عمل می‌شود.

دو لایه امنیت داریم:

ا-1. API Token: اثبات مجوز دسترسی به Cloudflare.
    
ا-2. Credentials File: محل نگهداری امن آن مجوز در سرور.
    

پس خود فایل مجوز نیست؛ Token داخل فایل مجوز است.

### این فایل احراز هویت اطلاعات یا credentials رو کجا باید بزاریم ؟

معمولا در پوشه کاربر ویژه میگذارند در پوشه ای مخفی که ما به این صورت به صورت دلخواه انتخاب کردیم
```
/root/
    └── .secrets/       (700)
          └── certbot/  (700)
                └── cloudflare.ini (600)
```

```
sudo mkdir -p /root/.secrets/certbot
sudo chmod 700 /root/.secrets   /root/.secrets/certbot
```
حالا باید فایل Credentials را داخل پوشه‌ای که ساختیم ایجاد کنیم و API Token کلادفلر را داخل آن قرار بدهیم.

#### اسمه فایل credentials رو ایا محدودیتی در انتخابش وجود دارد؟
اسم فایل Credentials را خودمان انتخاب می‌کنیم؛ Cloudflare یا Certbot اسم اجباری برای آن تعیین نکرده‌اند.

اما بهتر است نامی انتخاب کنیم که مشخص کند فایل مربوط به چیست.

پیشنهاد من:

```
cloudflare.ini
```

### چرا این اسم؟

- ا-`cloudflare`: مشخص می‌کند اطلاعات مربوط به Cloudflare است.
    
- ا-`.ini`: پسوندی برای فایل تنظیمات (Configuration File) است.
    

### نکته مهم

ا-<mark>Certbot از کجا می‌فهمد این فایل را بخواند؟</mark>

ما هنگام اجرای Certbot مسیر فایل را به آن معرفی می‌کنیم:

---
ا-<mark>چهاردهمین مرحله </mark>  گذاشتن اون api token  که cloud flare بهتون داده بود درون اون credentials

ا-Certbot از کجا می‌فهمد این فایل را بخواند؟

ما هنگام اجرای Certbot مسیر فایل را به آن معرفی می‌کنیم:

```
sudo certbot certonly \
--dns-cloudflare \
--dns-cloudflare-credentials /root/.secrets/certbot/cloudflare.ini \
-d shokbil.ir
```
### هر قسمت چه کاری می‌کند؟

- ا-`sudo`: اجرای Certbot با دسترسی مدیریتی.
    
- ا-`certbot`: اجرای برنامه Certbot.
    
- ا-`certonly`: فقط دریافت گواهی.
    
- ا-`--dns-cloudflare`: استفاده از افزونه Cloudflare.
    
- ا-`--dns-cloudflare-credentials`: معرفی فایل حاوی API Token.
    
- ا-`-d shokbil.ir`: تعیین دامنه.


برایه فهمیدن این که این خطوط چی هستند به فایل
[--dns-cloudflare-credentials](01-part2about---dns-cloudflare-credentials.md)
یعنی اسم فایل و مسیرش را خودمان تعیین کرده‌ایم و با این آرگومان به Certbot می‌گوییم کجا دنبالش بگردد.

### پس ساختار ما:

```
/root/
  └── .secret/
       └── certbot/
            └── cloudflare.ini
```

و داخل فایل:

```
dns_cloudflare_api_token = YOUR_API_TOKEN
```

اون توکنی که بهتون درون cloud flare  داده بود
نتیجه: اسم فایل اختیاری است، اما محتوای آن باید مطابق فرمت مورد انتظار افزونه Cloudflare باشد.

الان فایل credentials شما میشه cloudflare.ini و اینکه اون api tokenکه در اخر ساخت api token در cloud flare بهتون داده شد رو اینجا جایگذاری میکنید  و سپس این دستور رو میزنید

```
sudo certbot certonly \
--dns-cloudflare \
--dns-cloudflare-credentials /root/.secrets/certbot/cloudflare.ini \
-d shokbil.ir
```


---
ا-<mark> پانزدهمین مرحله </mark> اون گواهی نامه certificate صادر شده  و همچین خروجی به شما نشون میده


<p align=center>
	<img src="../00-images/accepted certificate.png" alt="images" width=800>
</p>


## گواهی‌ها کجا ذخیره شدند؟

ا-Certbot دو فایل مهم در اختیارمان قرار داده است:

Certificate

```
/etc/letsencrypt/live/shokbil.ir/fullchain.pem
```

گواهی عمومی دامنه به همراه زنجیره گواهی‌های میانی

Private Key

```
/etc/letsencrypt/live/shokbil.ir/privkey.pem
```

---
ا- <mark> مرحله شانزدهم</mark> حالا که هم certificate داریم  و هم private key بریم سراغه چی؟

حالا وارد یکی از مهم‌ترین مراحل می‌شویم: اتصال Certificate و Private Key به Nginx و فعال‌سازی HTTPS روی پورت 443.

تا اینجا گواهی را از Let's Encrypt دریافت کردیم. حالا باید به Nginx بگوییم از این دو فایل برای برقراری ارتباط امن با کاربران استفاده کند.

---
 - ا-<mark> مرحله هفدهم</mark> حالا باید درون virtual hostمون بیایم و اون دو دایرکتیو 

```


    ssl_certificate /etc/letsencrypt/live/shokbil.ir/fullchain.pem;

    ssl_certificate_key /etc/letsencrypt/live/shokbil.ir/privkey.pem;

```
تعریف کنیم و لذتشو ببریم


