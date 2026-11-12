## مسیر فایل هایه استاتیک
---
مسیر **رایج** برای قرار دادن فایل‌های ثابت سایت در لینوکس، زیر پوشهٔ **`/var/www/`** است؛ مثلاً:

```
/var/www/mysite/
```

برای یک سایت ساده می‌تونی این ساختار رو داشته باشی:

```
/var/www/mysite/index.html
/var/www/mysite/css/style.css
/var/www/mysite/js/app.js
/var/www/mysite/images/logo.png
```

و مقدار `root` رو این‌طور تنظیم کنی:

```
root /var/www/mysite;
```

**مسیر اجباری و یکسانی برای همهٔ نصب‌ها وجود نداره.** `/var/www/html` هم مسیر رایجیه؛ مسیر `/data/www` در آموزشی که می‌خونی، صرفاً یک انتخاب برای تمرینه.

---
### پس چیشد ما مسیر یکسانی نداریم ولی معمولا میایم در زیر مجموعه var/www/ میزاریم اون فایل هارو 


```
/var/www/mysite/index.html
/var/www/mysite/images/logo.png
/var/www/mysite/css/style.css
```

بعد داخل بلوک `server` در فایل تنظیمات می‌نویسی:

```
server {
    listen 80;
    root /var/www/mysite;

    location / {
    }
}
```

اینجا `location /` مقدار `root` رو از `server` می‌گیره:

|مسیر درخواست|فایل روی دیسک|
|---|---|
|`/index.html`|`/var/www/mysite/index.html`|
|`/images/logo.png`|`/var/www/mysite/images/logo.png`|
|`/css/style.css`|`/var/www/mysite/css/style.css`|

**لازم نیست برای هر پوشه یک `root` جدا بنویسی**؛ وقتی همه داخل پوشهٔ سایت هستن، همین یک `root` کافیه. همچنین لازم نیست داخل `/var/www` دوباره پوشه‌ای به اسم `www` بسازی.

---
## اینارو حالا جلوتر توضیح میدم ولی بد نیست بدونید


اگر همهٔ فایل‌های سایت داخل `/var/www/site1` هستن:

```
location / {
    root /var/www/site1;
}
```

یعنی:

- ا-**`location /`** → درخواست‌هایی که مسیرشون با `/` شروع می‌شه.
- ا-**`root /var/www/site1;`** → پوشهٔ پایهٔ فایل‌های سایت روی سرور.

مثلاً درخواست `/images/logo.png` به این فایل می‌رسه:

```
/var/www/site1/images/logo.png
```

پس **مسیر پوشهٔ سایت رو جلوی `root` می‌نویسی، نه جلوی `location`**. لازم نیست مسیر دقیق تک‌تک فایل‌ها رو بنویسی؛ Nginx مسیر درخواست رو به انتهای `root` اضافه می‌کنه.

---
### این برایه فهم بهترتون از location & root


- ا-**`location`** → الگوی مسیر درخواستی کاربر در آدرس سایت.
- ا-**`root`** → مسیر پوشهٔ پایهٔ فایل‌ها روی دیسک سرور.

مثلاً کاربر این آدرس رو باز می‌کنه:

```
http://example.com/images/logo.png
```

با این تنظیم:

```
location /images/ {
    root /var/www/site1;
}
```

ا-Nginx فایل زیر رو می‌فرسته:

```
/var/www/site1/images/logo.png
```

پس **کاربر مسیر `/images/logo.png` رو درخواست می‌کنه؛ Nginx فایل رو با ترکیب `root` و مسیر درخواست پیدا می‌کنه.**
