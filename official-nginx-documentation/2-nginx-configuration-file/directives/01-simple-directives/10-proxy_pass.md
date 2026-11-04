## توضیح دایرکتیو proxy_pass
---
## 1-دایرکتیو proxy_pass چیست؟
دستور `proxy_pass` یکی از مهم‌ترین دستورهای Nginx است، مخصوصاً وقتی می‌خواهیم از Nginx به‌عنوان Reverse Proxy استفاده کنیم.

دایرکتیو `proxy_pass` در Nginx برای ارسال (Forward) درخواست‌های HTTP کلاینت به یک سرور دیگر (Backend یا Upstream Server) استفاده می‌شود.

این دایرکتیو یکی از اجزای اصلی پیاده‌سازی Reverse Proxy در Nginx است.

###### خیلی کوتاه upstream server چیه:
ا-Upstream Server سروری است که Nginx درخواست‌های کلاینت را به آن ارسال می‌کند تا پردازش شوند و پاسخ برگردانده شود.

---
## 2-حالا اصلا چرا ما از این دایرکتیو استفاده میکنیم:

- ا-Reverse Prox : قرار دادن Nginx بین کلاینت و Backend.
    
- ا-Load Balancing: توزیع درخواست‌ها بین چند Backend.
    
- ا-Security: جلوگیری از دسترسی مستقیم کاربران به Backend، در صورت تنظیم صحیح شبکه.
    
- ا-Centralized Management: مدیریت درخواست‌ها از طریق یک نقطه مرکزی.

---
## 3-ساختار دستور proxy_pass به چه صورت است:

دایرکتیو `proxy_pass` معمولاً داخل بلاک `location` تعریف می‌شود و وظیفه آن مشخص کردن مقصد درخواست‌های کلاینت است.


```
proxy_pass http://backend_address:port;
```

- ا-`proxy_pass`: نام دایرکتیو
    
- ا-`http://`: پروتکل ارتباط با Backend (می‌تواند `https://` هم باشد)
    
- ا-`backend_address`: آدرس IP، دامنه یا نام گروه Upstream
    
- ا-`port`: پورت سرور Backend
    
- `;`: پایان دایرکتیو
    

### محل تعریف (Context)

`proxy_pass` در این Contextها قابل تعریف است:

- ا-`location` (رایج‌ترین)
    
- ا-`if in location`
    
- ا-`limit_except`

---

### 4-قضیه و قانون (جایزگزینی مسیر)URI Replacement چیست:

این قانون مشخص می‌کند وقتی Nginx درخواست را با `proxy_pass` به Backend می‌فرستد، کدام قسمت از مسیر درخواست حفظ شود و کدام قسمت تغییر کند.

## قانون اصلی چیست؟

اگر در `proxy_pass` یک URI بنویسیم، Nginx قسمت منطبق با `location` را با آن URI جایگزین می‌کند.

#### این URI  درون دایرکتیو proxy_pass چیست؟

ا-URI در `proxy_pass` یعنی مسیر (Path) مشخص‌شده بعد از آدرس سرور Backend و پورت آن.

مثلاً:

```
proxy_pass http://127.0.0.1:8081/images/;
```

این دستور سه بخش دارد:

- `http://` → پروتکل
    
- `127.0.0.1:8081` → آدرس و پورت Backend
    
- `/images/` → URI

### دو مثال

بدون URI:

```
proxy_pass http://127.0.0.1:8081;
```

با URI:

```
proxy_pass http://127.0.0.1:8081/photos/;
```

حالا که فهمیدید URI چیست بریم سراغه اون قانون خیلی مهم

قانون طلایی برای مثال‌های فعلی ما:

- ۱-`proxy_pass` بدون URI: مسیر اصلی حفظ می‌شود.
    
- ۲-`proxy_pass` با URI مثل `/` یا `/v1/`: قسمت منطبق با `location` جایگزین می‌شود.

چه کمکی میکند URI Replacement:

به کمک `proxy_pass` می‌توانیم این تفاوت را مدیریت کنیم، بدون اینکه کاربر مجبور باشد مسیر داخلی Backend را بداند.

#### حالا چند مثال از URI replacement:

```
# ================= EX1 =================

location /company/api/v3/products/ {
    proxy_pass http://127.0.0.1:8081/internal/v2/catalog/;
}

# Client URI:
/company/api/v3/products/electronics/mobile/samsung/s25/details

# Answer (Backend URL):
http://127.0.0.1:8081/internal/v2/catalog/electronics/mobile/samsung/s25/details


# ================= EX2 =================

location /services/payment/v2/ {
    proxy_pass http://127.0.0.1:9000;
}

# Client URI:
/services/payment/v2/transactions/2026/11/status?user=125&type=online

# Answer (Backend URL):
http://127.0.0.1:9000/services/payment/v2/transactions/2026/11/status?user=125&type=online


# ================= EX3 =================

location /api/admin/users/ {
    proxy_pass http://127.0.0.1:4000/secure/management/accounts/;
}

# Client URI:
/api/admin/users/active/2026/department/IT/list?page=3&status=enabled

# Answer (Backend URL):
http://127.0.0.1:4000/secure/management/accounts/active/2026/department/IT/list?page=3&status=enabled


# ================= EX4 =================

location /storage/private/documents/ {
    proxy_pass http://127.0.0.1:8085/archive/2026/files/;
}

# Client URI:
/storage/private/documents/company/finance/annual/reports/final.pdf?download=1&version=4

# Answer (Backend URL):
http://127.0.0.1:8085/archive/2026/files/company/finance/annual/reports/final.pdf?download=1&version=4
```

---
### 5-جمع بندی:
#### پس ما چه چیزایی داریم درمورد مبحث proxy_pass

- ا-Client URI → مسیری که کلاینت درخواست می‌کنه.
    
- ا-Backend URL → آدرسی که Nginx درخواست رو به اون می‌فرسته.

#### و این مثال هم برایه جمع بندی client URI va Backend URI

حالت اول: با URI

```
location /api/ {
    proxy_pass http://127.0.0.1:3000/backend/;
}
```

نتیجه:

```
Client URI:  /api/users/123
Backend URI: /backend/users/123
```

حالت دوم: بدون URI

```
location /api/ {
    proxy_pass http://127.0.0.1:3000;
}
```

نتیجه در این مثال ساده:

```
Client URI:  /api/users/123
Backend URI: /api/users/123
```


#### و این هم دلیل استفاده ما از proxy pass

ما در Nginx از `proxy_pass` استفاده می‌کنیم تا:

1. ا-Forward Request: درخواست کلاینت رو به سرور Backend ارسال کنیم.
    
2. ا-Hide Backend Address: آدرس و مسیر داخلی Backend رو از دید کلاینت پنهان کنیم.
    
3. ا-URL Mapping: مسیر درخواست کلاینت رو در صورت نیاز به مسیر متفاوتی در Backend تبدیل کنیم.
