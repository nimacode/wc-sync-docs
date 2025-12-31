# مستندات API پلاگین WC Server Sync

## معرفی
این پلاگین یک REST API برای همگام‌سازی محصولات WooCommerce بین سرور و کلاینت‌ها فراهم می‌کند.

**نسخه:** 1.0.0  
**Base URL:** `https://your-domain.com/wp-json/wc-sync/v1`

---

## احراز هویت (Authentication)

تمام درخواست‌ها نیاز به احراز هویت با API Key دارند. دو روش برای ارسال API Key وجود دارد:

### روش 1: Header (توصیه می‌شود)
```
X-WC-Sync-Token: your-api-key-here
```

### روش 2: Query Parameter
```
?api_key=your-api-key-here
```

### محدودیت درخواست (Rate Limiting)
- **محدودیت:** 100 درخواست در 60 ثانیه
- **کد خطا در صورت فراتر رفتن:** `429 Too Many Requests`

---

## اندپوینت‌ها (Endpoints)

### 1. دریافت لیست محصولات
دریافت لیست صفحه‌بندی شده محصولات منتشر شده

**URL:** `/products`  
**متد:** `GET`

#### پارامترهای Query
| پارامتر | نوع | الزامی | پیش‌فرض | توضیحات |
|---------|-----|--------|---------|---------|
| `page` | integer | خیر | 1 | شماره صفحه |
| `per_page` | integer | خیر | 20 | تعداد محصولات در هر صفحه (حداکثر: 50) |

#### مثال درخواست

**با cURL:**
```bash
curl -X GET "https://your-domain.com/wp-json/wc-sync/v1/products?page=1&per_page=20" \
  -H "X-WC-Sync-Token: your-api-key-here"
```

**با PHP:**
```php
$api_key = 'your-api-key-here';
$url = 'https://your-domain.com/wp-json/wc-sync/v1/products?page=1&per_page=20';

$response = wp_remote_get($url, array(
    'headers' => array(
        'X-WC-Sync-Token' => $api_key
    )
));

$body = wp_remote_retrieve_body($response);
$data = json_decode($body, true);
```

**با JavaScript/Fetch:**
```javascript
const apiKey = 'your-api-key-here';
const url = 'https://your-domain.com/wp-json/wc-sync/v1/products?page=1&per_page=20';

fetch(url, {
    method: 'GET',
    headers: {
        'X-WC-Sync-Token': apiKey
    }
})
.then(response => response.json())
.then(data => console.log(data));
```

#### پاسخ موفق (200 OK)
```json
{
  "products": [
    {
      "id": 123,
      "name": "نام محصول",
      "slug": "product-slug",
      "type": "simple",
      "status": "publish",
      "featured": false,
      "description": "توضیحات کامل محصول",
      "short_description": "توضیحات کوتاه",
      "sku": "PROD-123",
      "price": "100000",
      "regular_price": "120000",
      "sale_price": "100000",
      "stock_status": "instock",
      "stock_quantity": 50,
      "manage_stock": true,
      "images_urls": [
        "https://your-domain.com/wp-content/uploads/2024/01/image1.jpg",
        "https://your-domain.com/wp-content/uploads/2024/01/image2.jpg"
      ],
      "categories": [
        {
          "term_id": 15,
          "name": "دسته‌بندی",
          "slug": "category-slug"
        }
      ],
      "attributes_full": [
        {
          "name": "pa_color",
          "slug": "pa_color",
          "label": "رنگ",
          "options": ["قرمز", "آبی", "سبز"],
          "variation": true,
          "visible": true,
          "is_taxonomy": true
        }
      ],
      "variations_full": []
    }
  ],
  "total": 150,
  "max_num_pages": 8
}
```

#### خطاها
- `401 Unauthorized` - API Key نامعتبر یا موجود نیست
- `429 Too Many Requests` - محدودیت تعداد درخواست

---

### 2. دریافت یک محصول خاص
دریافت اطلاعات کامل یک محصول با ID

**URL:** `/products/{id}`  
**متد:** `GET`

#### پارامترهای URL
| پارامتر | نوع | الزامی | توضیحات |
|---------|-----|--------|---------|
| `id` | integer | بله | شناسه محصول |

#### مثال درخواست

**با cURL:**
```bash
curl -X GET "https://your-domain.com/wp-json/wc-sync/v1/products/123" \
  -H "X-WC-Sync-Token: your-api-key-here"
```

**با PHP:**
```php
$api_key = 'your-api-key-here';
$product_id = 123;
$url = "https://your-domain.com/wp-json/wc-sync/v1/products/{$product_id}";

$response = wp_remote_get($url, array(
    'headers' => array(
        'X-WC-Sync-Token' => $api_key
    )
));

$body = wp_remote_retrieve_body($response);
$product = json_decode($body, true);
```

**با JavaScript/Fetch:**
```javascript
const apiKey = 'your-api-key-here';
const productId = 123;
const url = `https://your-domain.com/wp-json/wc-sync/v1/products/${productId}`;

fetch(url, {
    method: 'GET',
    headers: {
        'X-WC-Sync-Token': apiKey
    }
})
.then(response => response.json())
.then(product => console.log(product));
```

#### پاسخ موفق (200 OK)
```json
{
  "id": 123,
  "name": "نام محصول",
  "slug": "product-slug",
  "type": "variable",
  "status": "publish",
  "featured": false,
  "description": "توضیحات کامل",
  "short_description": "توضیحات کوتاه",
  "sku": "PROD-123",
  "price": "100000",
  "regular_price": "120000",
  "sale_price": "100000",
  "stock_status": "instock",
  "stock_quantity": null,
  "manage_stock": false,
  "images_urls": [
    "https://your-domain.com/wp-content/uploads/2024/01/image1.jpg"
  ],
  "categories": [
    {
      "term_id": 15,
      "name": "دسته‌بندی",
      "slug": "category-slug"
    }
  ],
  "attributes_full": [
    {
      "name": "pa_size",
      "slug": "pa_size",
      "label": "سایز",
      "options": ["کوچک", "متوسط", "بزرگ"],
      "variation": true,
      "visible": true,
      "is_taxonomy": true
    }
  ],
  "variations_full": [
    {
      "id": 124,
      "name": "نام محصول - کوچک",
      "sku": "PROD-123-SM",
      "price": "95000",
      "stock_quantity": 10,
      "attributes": {
        "attribute_pa_size": "small"
      }
    },
    {
      "id": 125,
      "name": "نام محصول - بزرگ",
      "sku": "PROD-123-LG",
      "price": "105000",
      "stock_quantity": 5,
      "attributes": {
        "attribute_pa_size": "large"
      }
    }
  ]
}
```

#### خطاها
- `401 Unauthorized` - API Key نامعتبر
- `404 Not Found` - محصول یافت نشد
- `429 Too Many Requests` - محدودیت تعداد درخواست

---

### 3. بررسی و کسر موجودی
بررسی موجودی محصول یا کسر موجودی برای سفارش

**URL:** `/order-check`  
**متد:** `POST`  
**Content-Type:** `application/json`

#### پارامترهای Body
| پارامتر | نوع | الزامی | پیش‌فرض | توضیحات |
|---------|-----|--------|---------|---------|
| `id` | integer | خیر | - | شناسه محصول (یکی از id یا sku الزامی است) |
| `sku` | string | خیر | - | SKU محصول |
| `qty` | integer | خیر | 1 | تعداد درخواستی |
| `action` | string | خیر | "check" | نوع عملیات: "check" (بررسی) یا "deduct" (کسر موجودی) |

#### مثال درخواست

**بررسی موجودی با cURL:**
```bash
curl -X POST "https://your-domain.com/wp-json/wc-sync/v1/order-check" \
  -H "X-WC-Sync-Token: your-api-key-here" \
  -H "Content-Type: application/json" \
  -d '{
    "sku": "PROD-123",
    "qty": 2,
    "action": "check"
  }'
```

**کسر موجودی با PHP:**
```php
$api_key = 'your-api-key-here';
$url = 'https://your-domain.com/wp-json/wc-sync/v1/order-check';

$body = array(
    'id' => 123,
    'qty' => 2,
    'action' => 'deduct'
);

$response = wp_remote_post($url, array(
    'headers' => array(
        'X-WC-Sync-Token' => $api_key,
        'Content-Type' => 'application/json'
    ),
    'body' => json_encode($body)
));

$result = json_decode(wp_remote_retrieve_body($response), true);
```

**با JavaScript/Fetch:**
```javascript
const apiKey = 'your-api-key-here';
const url = 'https://your-domain.com/wp-json/wc-sync/v1/order-check';

const data = {
    sku: 'PROD-123',
    qty: 2,
    action: 'deduct'
};

fetch(url, {
    method: 'POST',
    headers: {
        'X-WC-Sync-Token': apiKey,
        'Content-Type': 'application/json'
    },
    body: JSON.stringify(data)
})
.then(response => response.json())
.then(result => console.log(result));
```

#### پاسخ موفق - بررسی موجودی (200 OK)
```json
{
  "available": true,
  "stock_quantity": 50
}
```

#### پاسخ موفق - کسر موجودی (200 OK)
```json
{
  "available": true,
  "stock_quantity": 48,
  "message": "Stock deducted"
}
```

#### پاسخ موجودی نامحدود (200 OK)
اگر محصول manage_stock غیرفعال داشته باشد:
```json
{
  "available": true,
  "stock_quantity": null
}
```

#### خطاها
```json
// محصول یافت نشد
{
  "code": "not_found",
  "message": "Product not found",
  "data": {
    "status": 404
  }
}

// موجودی کافی نیست
{
  "code": "out_of_stock",
  "message": "Insufficient stock",
  "data": {
    "status": 400,
    "current_stock": 1
  }
}

// API Key نامعتبر
{
  "code": "rest_forbidden",
  "message": "Invalid API Key.",
  "data": {
    "status": 401
  }
}
```

---

## ساختار داده‌های محصول

### فیلدهای اصلی محصول
```json
{
  "id": "شناسه محصول",
  "name": "نام محصول",
  "slug": "اسلاگ URL",
  "type": "نوع: simple, variable, variation, grouped",
  "status": "وضعیت: publish, draft, pending",
  "featured": "محصول ویژه: true/false",
  "description": "توضیحات کامل",
  "short_description": "توضیحات کوتاه",
  "sku": "کد محصول",
  "price": "قیمت فعلی (رشته)",
  "regular_price": "قیمت اصلی",
  "sale_price": "قیمت تخفیف خورده",
  "stock_status": "وضعیت موجودی: instock, outofstock, onbackorder",
  "stock_quantity": "تعداد موجودی (عدد یا null)",
  "manage_stock": "مدیریت موجودی فعال: true/false",
  "weight": "وزن",
  "dimensions": {
    "length": "طول",
    "width": "عرض",
    "height": "ارتفاع"
  },
  "images_urls": ["آرایه URL های تصاویر"],
  "categories": ["آرایه دسته‌بندی‌ها"],
  "attributes_full": ["آرایه ویژگی‌ها"],
  "variations_full": ["آرایه تنوع‌ها (فقط برای محصولات متغیر)"]
}
```

### ساختار Categories
```json
{
  "term_id": 15,
  "name": "نام دسته‌بندی",
  "slug": "category-slug",
  "term_group": 0,
  "term_taxonomy_id": 15,
  "taxonomy": "product_cat",
  "description": "توضیحات دسته‌بندی",
  "parent": 0,
  "count": 10
}
```

### ساختار Attributes
```json
{
  "name": "pa_color",
  "slug": "pa_color",
  "label": "رنگ",
  "options": ["قرمز", "آبی", "سبز"],
  "variation": true,
  "visible": true,
  "is_taxonomy": true
}
```

---

## مدیریت کلاینت‌ها

### ساخت API Key جدید
در پنل مدیریت وردپرس:
1. به `WooCommerce → Server Sync Settings` بروید
2. روی "Add New Client" کلیک کنید
3. نام و URL کلاینت را وارد کنید
4. API Key به صورت خودکار تولید می‌شود

### حذف کلاینت
در لیست کلاینت‌ها روی "Delete" کلیک کنید.

---

## نکات امنیتی

1. **API Key را محرمانه نگه دارید** - این کلید دسترسی کامل به API شما را می‌دهد
2. **از HTTPS استفاده کنید** - همیشه از اتصال امن استفاده کنید
3. **IP را محدود کنید** - در صورت امکان، IP کلاینت‌ها را محدود کنید
4. **لاگ‌ها را بررسی کنید** - تلاش‌های ناموفق احراز هویت در error_log ثبت می‌شوند
5. **Rate Limiting** - محدودیت 100 درخواست در 60 ثانیه برای هر API Key

---

## مثال کامل: همگام‌سازی محصولات

```php
<?php
class ProductSyncClient {
    
    private $api_url;
    private $api_key;
    
    public function __construct($server_url, $api_key) {
        $this->api_url = trailingslashit($server_url) . 'wp-json/wc-sync/v1';
        $this->api_key = $api_key;
    }
    
    /**
     * دریافت تمام محصولات با صفحه‌بندی خودکار
     */
    public function sync_all_products() {
        $page = 1;
        $all_products = array();
        
        do {
            $response = $this->get_products($page, 50);
            
            if (is_wp_error($response)) {
                error_log('Sync Error: ' . $response->get_error_message());
                break;
            }
            
            $products = $response['products'];
            $all_products = array_merge($all_products, $products);
            
            $page++;
        } while ($page <= $response['max_num_pages']);
        
        return $all_products;
    }
    
    /**
     * دریافت محصولات یک صفحه
     */
    public function get_products($page = 1, $per_page = 20) {
        $url = add_query_arg(array(
            'page' => $page,
            'per_page' => $per_page
        ), $this->api_url . '/products');
        
        $response = wp_remote_get($url, array(
            'headers' => array(
                'X-WC-Sync-Token' => $this->api_key
            ),
            'timeout' => 30
        ));
        
        if (is_wp_error($response)) {
            return $response;
        }
        
        $body = wp_remote_retrieve_body($response);
        return json_decode($body, true);
    }
    
    /**
     * دریافت یک محصول خاص
     */
    public function get_product($product_id) {
        $url = $this->api_url . '/products/' . $product_id;
        
        $response = wp_remote_get($url, array(
            'headers' => array(
                'X-WC-Sync-Token' => $this->api_key
            )
        ));
        
        if (is_wp_error($response)) {
            return $response;
        }
        
        $body = wp_remote_retrieve_body($response);
        return json_decode($body, true);
    }
    
    /**
     * بررسی موجودی قبل از سفارش
     */
    public function check_stock($product_id, $qty = 1) {
        $url = $this->api_url . '/order-check';
        
        $response = wp_remote_post($url, array(
            'headers' => array(
                'X-WC-Sync-Token' => $this->api_key,
                'Content-Type' => 'application/json'
            ),
            'body' => json_encode(array(
                'id' => $product_id,
                'qty' => $qty,
                'action' => 'check'
            ))
        ));
        
        if (is_wp_error($response)) {
            return $response;
        }
        
        $body = wp_remote_retrieve_body($response);
        return json_decode($body, true);
    }
    
    /**
     * کسر موجودی پس از تکمیل سفارش
     */
    public function deduct_stock($product_id, $qty = 1) {
        $url = $this->api_url . '/order-check';
        
        $response = wp_remote_post($url, array(
            'headers' => array(
                'X-WC-Sync-Token' => $this->api_key,
                'Content-Type' => 'application/json'
            ),
            'body' => json_encode(array(
                'id' => $product_id,
                'qty' => $qty,
                'action' => 'deduct'
            ))
        ));
        
        if (is_wp_error($response)) {
            return $response;
        }
        
        $body = wp_remote_retrieve_body($response);
        return json_decode($body, true);
    }
}

// نحوه استفاده
$sync_client = new ProductSyncClient(
    'https://server-domain.com',
    'your-api-key-here'
);

// دریافت محصولات صفحه اول
$products = $sync_client->get_products(1, 20);

// دریافت یک محصول خاص
$product = $sync_client->get_product(123);

// بررسی موجودی
$stock_check = $sync_client->check_stock(123, 2);

// کسر موجودی
if ($stock_check['available']) {
    $result = $sync_client->deduct_stock(123, 2);
}
```

---

## Troubleshooting (رفع مشکلات)

### خطای 401 Unauthorized
- بررسی کنید API Key صحیح باشد
- مطمئن شوید Header به درستی ارسال شده
- بررسی کنید کلاینت در پنل مدیریت موجود باشد

### خطای 429 Too Many Requests
- تعداد درخواست‌ها را کاهش دهید
- از caching استفاده کنید
- 60 ثانیه صبر کنید و دوباره تلاش کنید

### خطای 404 Not Found
- بررسی کنید URL صحیح باشد
- مطمئن شوید permalink ها فعال باشند
- بررسی کنید محصول وجود داشته باشد

### لاگ خطاها
خطاهای احراز هویت در `wp-content/debug.log` ثبت می‌شوند:
```
WC Server Sync Blocked: Missing API Key from IP 192.168.1.100
WC Server Sync Blocked: Invalid API Key abc12... from IP 192.168.1.100
WC Server Sync Blocked: Rate Limit Exceeded for client MyClient
```

---

## تغییرات و نسخه‌ها

### نسخه 1.0.0
- اندپوینت دریافت لیست محصولات
- اندپوینت دریافت محصول تکی
- اندپوینت بررسی و کسر موجودی
- سیستم احراز هویت با API Key
- Rate limiting
- پشتیبانی از محصولات متغیر
- لاگ امنیتی

---

## پشتیبانی
**نویسنده:** NimaCode  
**ایمیل:** nimaghorbani101@gmail.com  
**وب‌سایت:** https://eevgold.com
