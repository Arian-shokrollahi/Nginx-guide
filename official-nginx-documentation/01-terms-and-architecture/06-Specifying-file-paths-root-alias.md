# Specifying file paths for serving them
- root(Base Path + URI)
- alias(Path Replacement)
# Requirements
- URL
- URI path
- root architect philosophy
- alias architect philosophy
---
## خب اول مسیر چه استفاده ای دارد درون nginx
- مسیر درون nignx  برای مشخص کردن اون فایل هایی که قراره سرو شوند استفاده میشود
- ما دومدل برایه سرو کردن فایل های داریم که یکی به کمک دایرکتیو root است و یکی دیگر به کمک alias
- ۱-root (Base Path + URI) :‌این هم مدلی است که URI رو به base path میچسبونه
- ۲-alias(Path Replacement) : این همون روشی است که مسیر کامل رو جایگزین URI میکرد

---
## ۱- مدل <mark>root</mark>:
- دایرکتیو root  چیکار میکند ؟


دایرکتیو `root` یک مسیر پایه (Base Path) تعیین می‌کند و Nginx، URI درخواست‌شده را به آن اضافه می‌کند.

```
location /images/ {
    root /var/www;
}
```

فرض کن کاربر این آدرس را درخواست می‌کند:

```
http://localhost/images/logo.png
```

Nginx مسیر فایل را این‌طور می‌سازد:

```
root + URI

/var/www + /images/logo.png

= /var/www/images/logo.png
```

---

## ۲-مدل <mark> alias</mark> :
- دایرکتیو alias چیکار میکند؟



دایرکتیو `alias` بخش URI که با `location` مطابقت دارد را با مسیر مشخص‌شده جایگزین می‌کند.

```
location /images/ {
    alias /var/www/assets/;
}
```

اگر کاربر بزنه :

```
http://localhost:8080/images/logo.png
```


مسیر نهایی می‌شه:


```
/var/www/assets/ + logo.png

= /var/www/assets/logo.png
```

---
## اینم هم برایه یاد اوری درمورد  ادرسی که کاربر وارد میکند

وقتی کاربر این آدرس رو وارد می‌کنه:

```
http://localhost:8080/images/logo.png
```

آدرس به این بخش‌ها تقسیم می‌شه:

|بخش|مقدار|توضیح|
|---|---|---|
|Protocol|`http://`|پروتکل ارتباطی|
|Host|`localhost`|نام میزبان|
|Port|`8080`|پورت سرور|
|URI Path|`/images/logo.png`|مسیر درخواست‌شده|

1. ا-URL (آدرس کامل):

```
http://localhost:8080/images/logo.png
```

2. ا-URI Path (مسیر منبع):

```
/images/logo.png
```

نکته مهم اینه که URL کامل هم خودش نوعی URI محسوب می‌شه.

اما در Nginx وقتی از `URI` صحبت می‌کنیم، معمولاً منظور همین مسیر درخواست‌شده است:

```
/images/logo.png
```

### پس برای یادگیری Nginx:

- ا-`URL` = آدرس کامل شامل پروتکل، هاست، پورت و مسیر
    
- ا-`URI Path` = فقط مسیر منبع بعد از هاست و پورت
    
- ا-`root` = اضافه کردن URI Path به مسیر پایه
    
- ا-`alias` = جایگزین کردن بخش مطابق `location` از URI Path
    

