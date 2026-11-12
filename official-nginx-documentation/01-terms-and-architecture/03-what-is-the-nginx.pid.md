# در مسیر var/run/nginx.pid چه چیزی نگه داری میشود:
---
- مسیر var/run/nginx.pid مسیره نگهداری pid مربوط به پردازه اصلی 
<p align=center>
	<img src="../00-images/runpidpath.png" alt="image" width=800>
</p>
-  همانطور که در عکس میبینید 
- ما اومدیم یه  grep کردیم از خروجی ps -aux و شمار اون pid مربوط به پردازه اصلی رو پیدا کردیم
- حالا همونطور که مشاهده میکنید اون شماره با شماره که درون مسیر run/nginx.pid که گفتیم در فایل کانفیگ etc/nginx.conf/ است تنظیم میشد یکی است
