# proxy_next_upstream directive
---
## 1- مقدمه: proxy_next_upstream چیست؟

در معماری Load Balancing، ما چند Backend Server داریم و Nginx درخواست‌های کاربران را بین آن‌ها توزیع می‌کند.

حالا فرض کن سه Backend داریم:

- Backend 1
    
- Backend 2
    
- Backend 3
    

کاربر درخواستی به Nginx ارسال می‌کند و Nginx آن را به Backend 1 می‌فرستد.

اما Backend 1 دچار مشکل می‌شود و نمی‌تواند پاسخ مناسبی ارائه دهد.

اینجا یک سؤال مهم مطرح می‌شود:

آیا Nginx باید درخواست را ناموفق اعلام کند یا می‌تواند همان درخواست را به Backend دیگری ارسال کند؟

اینجاست که `proxy_next_upstream` وارد عمل می‌شود.

این Directive مشخص می‌کند در صورت بروز چه خطاهایی، Nginx اجازه دارد درخواست را به Backend بعدی منتقل کند.

به این رفتار در Load Balancing اصطلاحاً `Retry` می‌گوییم.

---
# 2. این Directive کجا استفاده می‌شود؟

این Directive داخل بلوک `location` یا `server` تعریف می‌شود و معمولاً در `location` قرار می‌گیرد.

ساختار کلی:

```
location / {

    proxy_pass http://backend_servers;

    proxy_next_upstream error timeout http_500 http_502 http_503 http_504;

}
```

در این ساختار:

- ا-`proxy_pass`: مقصد درخواست را مشخص می‌کند.
    
- ا-`proxy_next_upstream`: شرایط انتقال درخواست به Backend بعدی را مشخص می‌کند.
---
## 3- مقادیر Directive: proxy_next_upstream

|مقدار (Value)|عملکرد (Function)|توضیح (Description)|
|---|---|---|
|`error`|Retry on error|اگر هنگام برقراری ارتباط، ارسال درخواست یا دریافت پاسخ از Backend خطای ارتباطی رخ دهد، Nginx می‌تواند درخواست را به Backend بعدی منتقل کند.|
|`timeout`|Retry on timeout|اگر عملیات ارتباط با Backend با Timeout مواجه شود، Nginx می‌تواند درخواست را مجدداً ارسال کند.|
|`invalid_header`|Retry on invalid header|اگر Backend پاسخ با Header نامعتبر ارسال کند، Nginx می‌تواند Backend دیگری را امتحان کند.|
|`http_500`|Retry on HTTP 500|در صورت دریافت پاسخ Internal Server Error، امکان Retry وجود دارد.|
|`http_502`|Retry on HTTP 502|در صورت دریافت پاسخ Bad Gateway، امکان Retry وجود دارد.|
|`http_503`|Retry on HTTP 503|در صورت دریافت پاسخ Service Unavailable، امکان Retry وجود دارد.|
|`http_504`|Retry on HTTP 504|در صورت دریافت پاسخ Gateway Timeout، امکان Retry وجود دارد.|
|`http_403`|Retry on HTTP 403|در صورت دریافت پاسخ Forbidden، امکان Retry وجود دارد.|
|`http_404`|Retry on HTTP 404|در صورت دریافت پاسخ Not Found، امکان Retry وجود دارد.|
|`http_429`|Retry on HTTP 429|در صورت دریافت پاسخ Too Many Requests، امکان Retry وجود دارد.|
|`non_idempotent`|Allow retry for non-idempotent requests|اجازه می‌دهد درخواست‌های غیر Idempotent مانند POST نیز در شرایط مشخص مجدداً ارسال شوند.|

---
### 4- یه مثال نمونه Configuration

```
http {

    upstream backend_servers {

        server 10.0.0.11:8080 max_fails=3 fail_timeout=30s;

        server 10.0.0.12:8080 max_fails=3 fail_timeout=30s;

        server 10.0.0.13:8080 max_fails=3 fail_timeout=30s;

    }

    server {

        listen 80;

        location / {

            proxy_pass http://backend_servers;

            proxy_next_upstream error timeout http_500 http_502 http_503 http_504;

        }
    }
}
```

اگر در یک بازه ۳۰ ثانیه‌ای، ۳ شکست قابل شمارش داشته باشیم، Backend به مدت ۳۰ ثانیه Unavailable در نظر گرفته می‌شود.

فقط دقت کن که لازم نیست این ۳ شکست متوالی باشند.

مثلاً:

- ا-Fail اول: ثانیه ۵
    
- درخواست موفق: ثانیه ۱۰
    
- ا-Fail دوم: ثانیه ۱۵
    
- ا-Fail سوم: ثانیه ۲۰
ا-<mark>  fail_timeout هم میگه چند ثانیه داون شه هم میگه در چه بازه ای اون تعداد max_fails رخ بده</mark>

---
## 5- پس جمع بندی تا اینجا
- ا-max_fails تعداد خطاها 
- ا-fail_timeout بازه زمانی رخ دادن اون تعداد خطا و باز زمانی داون شدن در صورت رخ دادن در اون بازه زمانی
- ا-proxy_next_upstream هم میشه نوع خطاها
