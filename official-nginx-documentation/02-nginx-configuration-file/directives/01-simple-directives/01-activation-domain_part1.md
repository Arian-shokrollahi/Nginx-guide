##  0 تا 100 خرید DNS و فعال کردن اون HTTPs و certbot
---
## مراحل خرید DNS از ایرنیک و دادن NAMESERVER بهش

ا-<mark>اول</mark>----> انتخاب میکنید که  پسوند و اون سایت چی باشه من خودم ir. رو انتخاب کردم و شما هم باید انتخاب کنید بر اساس اون بودجتون و علاقتون که چی بزارید اون Suffix یا پسوند دامنتون رو 

---

ا-<mark>دوم</mark> شما ---> میرید داخله سایت ایرنیک ---> به ادرس nic.ir 
بعد اگر ثبت نام کردید وارد حسابتون شید و اگر ثبت نام نکردید یه خورده طولانیه 

ثبت نام در ایرنیک(nic.ir)---> میرید ثبت نام میکنید و دامنه مورد نظرتون رو انتخاب میکنید و در هنگام پرداخت و انتخاب اون دامنه باید برید درون سایت <mark> هدا (هویت دیجیتال ایرانیان)</mark> احرازهویت (authentication)کنید. سامانه ها اصلن چیست و چرا:
#### سامانه هُدا چیست؟

هُدا مخفف هویت دیجیتال ایرانیان است و برای احراز هویت دیجیتال افراد استفاده می‌شود.

ایرنیک برای اطمینان از اینکه شخصی که دامنه `.ir` ثبت می‌کند واقعاً همان مالک معرفی‌شده است، از این فرایند احراز هویت استفاده می‌کند.

این فرایند برای کاربران حقیقی شامل تأیید اطلاعات هویتی و احراز هویت تصویری است.

---

ا-<mark>سوم</mark> ---> بعد از اینکه شما احرازهویت کردید میرید و اون دامنه رو میخرید
و بعد برایه شما چیزی مثل این نشون میده

<p align=center>
	<img src="../00-images/nic.irdashborad.png" alt="images" width=800>
</p>

---

ا-<mark>چهارم</mark> ---> وقتی بزنید رویه اون  DNS یه همچین صحفه ای میبینید

<p align=center>
	<img src="../00-images/nameserverin-nic.ir.png" alt="images" width=800>
</p>

### فهمیدید چی شد دو مدل دارد پس تنظیمات dns درون ایرنیک:

ا-<mark> ۱. حالت اول</mark>: Nameserver متعلق به شرکت دیگری است

فرض کن DNS دامنه‌ات را به Cloudflare سپرده‌ای:

```
aron.ns.cloudflare.com
garrett.ns.cloudflare.com
```

در NIC فقط نام کارگزار را وارد می‌کنی و IP را خالی می‌گذاری. همین کاری که من کردم

ا-<mark> ۲. حالت دوم</mark>: Nameserver متعلق به خودت است

فرض کن شرکتت دامنه زیر را دارد:

```
company.com
```

و می‌خواهی DNS Server اختصاصی خودت را معرفی کنی:

```
ns1.company.com
```

حالا یک مشکل داریم:

برای پیدا کردن IP دامنه `ns1.company.com` باید DNS را جست‌وجو کنیم، اما DNS همین دامنه قرار است روی `ns1.company.com` باشد!

اینجا به Glue Record نیاز داریم.

مثلاً:

|فیلد|مقدار|
|---|---|
|Nameserver|ns1.company.com|
|IP|203.0.113.10|

ا-<mark> ما از حالت ۱ استفاده کردیم چون:</mark> چون  nameserver IP ما رو DNS resolver میتونه پیدا کند چون ماله cloudflare

 مقایسه در Production

|سناریو|نام کارگزار|IP کارگزار|
|---|---|---|
|Cloudflare|لازم|معمولاً خالی|
|AWS Route 53|لازم|معمولاً خالی|
|DNS Provider خارجی|لازم|معمولاً خالی|
|Nameserver اختصاصی داخل دامنه خودت|لازم|Glue IP لازم|

 نتیجه‌ای که باید یاد بگیری

دو مفهوم را از هم جدا کن:

- ا-NS Record: مشخص می‌کند مسئول مدیریت DNS دامنه کدام Nameserver است.
    
- ا-Glue Record: آدرس IP کمکی Nameserver را در Zone والد قرار می‌دهد تا مشکل وابستگی دوری حل شود.

---

ا-<mark>پنجم</mark> ---> حالا اون name server haro از کجا بیاریم شما میرید درون کلود فلر یا (DNS provider)


<p align=center>
	<img src="../00-images/cloudflareadddomain.png" alt="images" width=800>
</p>

---

ا-<Mark>ششم</mark>---> حال بعد اینکه دامنتون رو اد کردید و قبول شد برایه شما دو تا name srever میاره که این ها همونایی هستند که باید در بخش


<p align=center>
	<img src="../00-images/addnameserverinnic.ir.png" alt="images" width=800>
</p>

---

ا-<mark>هفتم</mark>---> حالا باید فرصت بدید تا فرایند DNS Delegation و برسی شود که اون nameserver هایی که وارد کردید از کجاست بین ۱ تا ۲۴ ساعت زمان میبره و بعد 

ا-Delegation چیست؟

ما این دو Nameserver را در پنل NIC ثبت کردیم.

در واقع به NIC گفتیم:

«مدیریت DNS دامنه من را به این دو Nameserver واگذار کن.»

ا-NIC هم اطلاعات این واگذاری را در Zone والد `.ir` منتشر کرد.

به این فرایند Delegation می‌گوییم.

---
ا-<mark>هشتم</mark> ---->برای تست DNS Delegation با دستور `dig` چند روش داریم. می‌خواهیم مطمئن شویم NIC دامنه را به Nameserverهای Cloudflare واگذار کرده است.

دامنه ما:

```
shokbil.ir
```

Nameserver مثلا های ما:

```
aron.ns.cloud.com
garrett.cloudflare.com
```

### تست Delegation از خود NIC.ir

این مهم‌ترین تست ماست:

```
dig @a.nic.ir shokbil.ir NS +norecurse
```

|بخش|معنی|کاربرد|
|---|---|---|
|`dig`|DNS Lookup|ابزار بررسی DNS|
|`@a.nic.ir`|DNS Server مقصد|ارسال مستقیم درخواست به NIC|
|`shokbil.ir`|Domain|دامنه مورد بررسی|
|`NS`|Query Type|درخواست رکوردهای Nameserver|
|`+norecurse`|غیرفعال‌کردن Recursion|درخواست پاسخ مستقیم از سرور مقصد|

خلاصه: از Nameserver رسمی NIC می‌پرسیم که Nameserverهای مسئول دامنه `shokbil.ir` کدام‌اند؛ بدون درخواست جست‌وجوی بازگشتی.

خروجی مورد انتظار:

```
status: NOERROR

AUTHORITY SECTION:
shokbil.ir. 900 IN NS garrett.ns.cloud.com.
shokbil.ir. 900 IN NS aron.cloudflare.com.
```

این خروجی چه معنایی دارد؟

- ا-`NOERROR`: درخواست با موفقیت پاسخ داده شده است.
    
- ا-`NS`: نوع رکوردی که درخواست کرده‌ایم.
    
- ا-`AUTHORITY SECTION`: در این پاسخ، اطلاعات Delegation در بخش Authority قرار گرفته است.
    
- ا-`+norecurse`: از NIC می‌خواهیم خودش به درخواست پاسخ دهد و جست‌وجوی بازگشتی انجام ندهد.
    

نکته مهم: در این تست ممکن است `ANSWER: 0` ببینی؛ این طبیعی است، چون NIC به‌عنوان Zone والد، اطلاعات Nameserverهای دامنه فرزند را در بخش Authority معرفی می‌کند.

### تست Delegation از DNS عمومی Cloudflare

```
dig @1.1.1.1 NS shokbil.ir +short
```

|بخش|معنی|کاربرد|
|---|---|---|
|`dig`|DNS Lookup|ابزار بررسی DNS|
|`@1.1.1.1`|Cloudflare DNS Resolver|ارسال درخواست به DNS عمومی Cloudflare|
|`NS`|Query Type|درخواست رکوردهای Nameserver|
|`shokbil.ir`|Domain|دامنه مورد بررسی|
|`+short`|Short Output|نمایش خلاصه پاسخ، بدون جزئیات اضافی|

خلاصه: از DNS عمومی Cloudflare می‌پرسیم Nameserverهای دامنه `shokbil.ir` کدام‌اند و فقط پاسخ نهایی را نمایش می‌دهیم.

خروجی مورد انتظار:

```
aron.ns.cloud.com.
garrett.cloudflare.com.
```

این تست نشان می‌دهد DNS Resolver عمومی هم Nameserverهای دامنه را پیدا می‌کند.

---
ا-<mark>نهم</mark>:---> تست مستقیم DNS Serverهای Cloudflare

تا اینجا فهمیدیم NIC دامنه را به Cloudflare واگذار کرده است. حالا باید مطمئن شویم خود Cloudflare هم Zone دامنه ما را نگهداری می‌کند.

ابتدا Nameserver اول را تست کن:

```
dig @aron.cloudflare.com shokbil.ir SOA +norecurse
```

سپس Nameserver دوم:

```
dig @garrett.ns.cloud.com shokbil.ir SOA +norecurse
```
خروجی مورد انتظار:

```
status: NOERROR
flags: qr aa
```

مهم‌ترین قسمت <mark>`aa`</mark> است:

`AA = Authoritative Answer`

یعنی Cloudflare خودش پاسخ معتبر مربوط به Zone دامنه را ارائه می‌دهد.

این تست را برای هر دو Nameserver انجام می‌دهیم تا مطمئن شویم هر دو درست کار می‌کنند.

---
ا-<mark> دهم </mark> ----> برایه این بخش اول فایل  [section 10 requirements](01-section-10-requirements.md)رو بخونید 

دهم: ساخت API Token در Cloudflare

حالا می‌خواهیم به Certbot اجازه بدهیم از طریق API با Cloudflare ارتباط برقرار کند و رکوردهای TXT را به‌صورت خودکار ایجاد و حذف کند.

چرا؟ چون برای گرفتن SSL از Let's Encrypt با روش DNS-01 باید بتوانیم TXT Record بسازیم.

### مراحل ساخت Token

1. وارد داشبورد Cloudflare شو:

[Cloudflare API Tokens](https://dash.cloudflare.com/profile/api-tokens?utm_source=chatgpt.com)


2. روی Create Token کلیک کن.

<p align=center>
	<img src="../00-images/createtoken.png" alt="images" width=800>
</p>


بعد همچین

میرید تویه همچین صحفه ای 
<p align=center>
	<img src="../00-images/edittoken name.png" alt="images" width=800>
</p>



بعد از اینکه رویه create new token زدید رویه گزینه ی Edit zone DNS بزنید 
چون قراره یه کار مهم کنیم: جلوتر میگیم



<p align=center>
	<img src="../00-images/change name for toke and select edit zone.png" alt="images" width=800>
</p>


بعد که شما میاید رویه اون add policy میزنید


<p align=center>
	<img src="../00-images/entireaccount.png" alt="images" width=800>
</p>


و این هم از کار هایی که باید بکنید 


<p align=center>
	<img src="../00-images/editpolicy.png" alt="images" width=800>
</p>
عبارت All IP addresses allowed یعنی محدودیت آدرس IP برای استفاده از این API Token تعریف نکرده‌ایم.

- اگر IP عمومی ثابت (Static IP) داشته باشی: می‌توانی آن IP را در قسمت `Client IP address filtering` وارد کنی تا فقط همان IP اجازه استفاده از Token را داشته باشد.
    
- اگر IP داینامیک (Dynamic IP) داشته باشی: بهتر است این قسمت را خالی بگذاری و وضعیت `All IP addresses allowed` را حفظ کنی.
    

چون با تغییر IP، درخواست‌های Certbot ممکن است مسدود شوند.

یک نکته مهم: محدودیت IP با محدودیت دسترسی دامنه فرق دارد. ما همچنان توکن را فقط به `shokbil.ir` محدود کرده‌ایم.

و حالا نهایی سازی و گرفتن توکن و قضیه All IP addresses allowed

<p align=center>
	<img src="../00-images/review toke.png" alt="images" width=800>
</p>
 

بعد به شما یک کد میده که اون کد رو نباید جایی نشون بدید

---
ا-<mark> یازدهم </mark>   در فایل هایه بعدی 

## جمع بندی مرحله ۱ تا ۱۰


## جمع‌بندی نهایی مراحل ۱ تا ۱۰

## مسیر طی‌شده

1. ثبت دامنه
    
    انجام شد
    
    ثبت `shokbil.ir` در NIC
    
2. معرفی Nameserver
    
    انجام شد
    
    معرفی Nameserverهای Cloudflare در NIC
    
1. ا-DNS Delegation
    
    انجام شد
    
    واگذاری مدیریت DNS به Cloudflare
    
2. بررسی DNS
    
    مسیر بررسی انجام شد
    
    تست Delegation و Authoritative DNS
    
3. ساخت API Token
    
    انجام شد
    
    ایجاد توکن محدود به دامنه `shokbil.ir`
