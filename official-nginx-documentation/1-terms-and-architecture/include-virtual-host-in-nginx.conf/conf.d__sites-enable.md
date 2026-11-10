# اگر به فایل اصلی کانفیگ درون nginx (/etc/nginx/nginx.conf) دقت کنید درون اون دو include  خورده که درمورد این دو میخوایم توضیح بدیم 

که چیزی که قراره درون این فایل بخونیم اینه که به چه صورت ما بیایم
فعال کنیم (active virtual host) و چطوری غیرفعال کنیم(disable virtual host) درون دو مدل

---
## 1- ما درمورد چی داریم صبحت میکنیم میگیم درون فایل اصلی


<p align=center>
	<img src="../../00-images/virtual host config in nginx.conf .png" alt="images" width=800>
</p>

در فایل اصلی Nginx یعنی:

```
/etc/nginx/nginx.conf
```

معمولاً دو Directive داریم:

```
include /etc/nginx/conf.d/*.conf;

include /etc/nginx/sites-enabled/*;
```

هر دو به Nginx می‌گویند فایل‌های داخل این مسیرها را بخوان و تنظیماتشان را بارگذاری کن.

بنابراین از دید Nginx، هر دو روش می‌توانند یک Virtual Host را تعریف کنند.

تفاوت اصلی در مدیریت فایل‌هاست:

- ا-`conf.d`: فایل Configuration را مستقیماً آنجا قرار می‌دهی.
    
- ا-`sites-enabled`: فایل فعال را آنجا قرار می‌دهی.
    
- ا-`sites-available`: فایل اصلی را نگه می‌داری و با Symlink به `sites-enabled` متصل می‌کنی.
    

مثلاً:

```
nginx.conf
   |
   |---- include conf.d/*.conf
   |          |
   |          └── example.conf
   |
   |---- include sites-enabled/*
              |
              └── example -> sites-available/example
```


---
## 2- پس برایه فعال کردن

- مدل ۱-<mark> etc/nginx/conf.d/*.conf/</mark> 

در این مدل به این صورت است که شما هر  فایلی که با پسوند conf. درون مسیر  conf.d بگذارید nginx براتون اون رو بالا میارد  پس یعنی هر چی که با پسوند conf. باشد هر فایلی

هر فایلی که با الگوی Include مطابقت داشته باشد، توسط Nginx بارگذاری می‌شود:

```
include /etc/nginx/conf.d/*.conf;
```

مثلاً:

```
conf.d/
├── site1.conf    ← بارگذاری می‌شود
├── site2.conf    ← بارگذاری می‌شود
└── site3.conf    ← بارگذاری می‌شود
```

- مدل ۲-<mark> etc/nginx/sites-available/</mark> به چه صورت است  

 چند مرحله دارد شما میاید درون sites-available اون فایل هایه کانفیگ virtual host رو مینویسید بعد کانفیگ هایی رو که میخواید برایه شما بالا بیاره رو میاید رون etc/nginx/sites-enabled/ لینک نمادین میزنید و به دلیلی 
 اون include که درون etc/nginx.conf/ خورده اون هارو برایه شما بالا میاره

در روش `sites-available` و `sites-enabled`:

فایل‌ها را در `sites-available` نگه می‌داری و فقط با Symlink کردن آن‌ها به `sites-enabled` فعالشان می‌کنی.

```
sites-available/
├── site1
├── site2
└── site3

sites-enabled/
├── site1 -> ../sites-available/site1
└── site3 -> ../sites-available/site3
```

اینجا سایت دوم غیرفعال است، ولی فایل تنظیماتش همچنان وجود دارد.

مقایسه نهایی:

|ویژگی|conf.d|sites-available/enabled|
|---|---|---|
|تعریف Virtual Host|بله|بله|
|فعال‌سازی|قرارگیری در مسیر Include|ایجاد Symlink|
|غیرفعال‌سازی|خروج از الگوی Include|حذف Symlink|
|نگهداری تنظیمات غیرفعال|با روش جداگانه|به‌صورت پیش‌فرض|
|مدیریت چند سایت|امکان‌پذیر|ساده‌تر و منظم‌تر|

---

## 3-برایه غیر فعال کردن 

- مدل ۱ <mark> etc/nginx/conf.d/</mark>  در این مدل برایه اینکه بخواید اون فایل کانفیگتون در فایل کانفیگ اصلی بارگذاری نشه باید بیاید و اون فایلی که درون پوشه conf.d در مسیر etc/nginx/ است رو از اون pattern که تهش فقط conf. است در بیارید مثلا
برای غیرفعال‌کردن یک سایت باید فایل را از الگوی Include خارج کنی؛ مثلاً:

```
sudo mv /etc/nginx/conf.d/site2.conf \
/etc/nginx/conf.d/site2.conf.disabled
```


- مدل ۲-<mark> etc/nginx/sites-available/</mark> هم شما باید بیاید و اون کانفیگی که میخواید بارگذاری نشه رو لینک نمادین نزنید به به etc/nginx/sites-enabled/ تا بارگذاری نشه در کانفیگ اصلی 


