# proxy_set_header Host $host
---
### ۱-دایرکتیو proxy_set_header Host $host چیست؟
`proxy_set_header Host $host;`

یعنی نام دامنه یا IP درخواست Client را در هدر Host به Backend ارسال کن، بدون پروتکل و پورت.


---

### ۲-دقیقا چیست proxy_set_header Host $host
- برایه فهمیدن این اول باید بدونید که ساختار http request به چه صورته
ساختار آدرس در HTTP Request به این صورته:

```
http://domain-or-IP:port/clientURI
```

- `http://` → پروتکل
    
- `domain-or-IP` → دامنه یا IP مقصد
    
- `port` → پورت مقصد
    
- `clientURI` → مسیر درخواستی کلاینت

این دایرکتیو بما <mark> domain or ip </mark> که اون کلاینت میزنه رو نشون میده

---

### ۳- دلیل استفاده از این دایرکتیو چیست:

چون ممکن است چندین وب‌سایت روی یک سرور Backend میزبانی شوند. سرور Backend با بررسی هدر `Host` تشخیص می‌دهد درخواست مربوط به کدام وب‌سایت است.

پس به زبان ساده:

با این دستور به Backend می‌گوییم درخواست اولیه کاربر برای کدام دامنه یا IP بوده است.


----  


### 4- تست دایرکتیو proxy_set_header Host $host

<p align=center>
	<img src="../../00-images/test-proxy-set-header-Host.png" alt="images" width=800>
</p>
