# Alias directive 
- اموزش این قسمت رو به دو دسته تقسیم میکنیم 
- ۱-یک در بخش اول بهتون توضیح میدم که این دایرکتیو چیکار میکند  و به صورت خوندنی
- ۲-دوم در بخش دو بهتون نشون میدم که این دایرکتیو چیکار میکند 
- 3- این رو هم بخونید درمورد معماری این است که این دایرکتیو چجوری کار میکند و چه فرقی با دایرکتور root دارد
- ----> [alias and root architect](../../../1-terms-and-architecture/06-Specifying-file-paths-root-alias.md )
---
# بخش اول
## ۱-۱ محل دایرکتیو alias  کجاست ؟
- در Nginx، دایرکتیو `alias` داخل بلاک `location` تعریف می‌شه و وظیفه‌اش اینه که مسیر URL درخواست‌شده رو به یک مسیر مشخص در فایل‌سیستم سرور نگاشت کنه.


## ۲-۲ ایا این دایرکتیو یک  دایرکتیو بلاکی است یا ساده؟
- این دایرکتیو یک دایرکتیو ساده (simple directive ) است که در بلا ک location تعریف میشه

## ۳-۳ اگه بخوام تویه یک بلاک نشون بدم که چه اتفاقی میوفته
```
# 1. Nginx Configuration

server {
    listen 80;

    location /images/ {
        alias /var/www/pictures/;
    }
}


# 2. User Request (URI)
#
#    /images/cat.jpg
#
# 3. Match Location
#
#    /images/cat.jpg
#    |-------| |-----|
#     location   file
#
# 4. Remove Location Prefix
#
#    /images/cat.jpg
#    ------- حذف می‌شود
#
#    Remaining URI = cat.jpg
#
# 5. Get Alias Path
#
#    alias = /var/www/pictures/
#
# 6. Append Remaining URI to Alias
#
#    /var/www/pictures/ + cat.jpg
#
#                  |
#                  v
#
# 7. Final File Path
#
#    /var/www/pictures/cat.jpg
#
# 8. Nginx reads the file and sends it to the user.
#
# نکته:
# آدرس کاربر تغییر نمی‌کند.
# فقط مسیر واقعی فایل در سرور مشخص می‌شود.
```

---
# بخش دوم اینکه این دایرکتیو چیکار میکند
### قراره چیکار بکنیم درون این قسمت:
- ۱- یک عکس داریم به اسمه donky.png  که میخوایم 
- ۲- و قراره توسط دایرکتیو alias   در مسیری که عکس درون اون قرار دارد  var/www/images/donky.png  سرو شود
--
#### چه چیزایی نیاز داریم 
- ۱-فایل کانفیگ 
- ۲- اون عکسه باید درون  اون مسیر خودش باشد ---> var/www/name
-  و nginx -t  برایه برسی فایل کانفیگ و nginx -s reload  برایه ایجاد worker processes هایه جدید

----
<p align=center>
	<img src="../00-images/alias-directive.png" alt="image" width=800>
</p>
