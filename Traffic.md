## 🧩 طراحی دیتابیس نهایی (Final DB Schema)

### 1. **User**

| Field         | Type         | Description               |
| ------------- | ------------ | ------------------------- |
| id            | int          | Primary key               |
| full_name     | text         | نام کامل کاربر            |
| phone         | text(unique) | شماره تماس                |
| password_hash | text         | هش رمزعبور                |
| role          | varchar      | admin / operator / viewer |
| is_active     | bool         | وضعیت فعال بودن           |
| created_at    | timestamp    | زمان ایجاد حساب           |

---

### 2. **Segment**

| Field               | Type                     | Description                                         |
| ------------------- | ------------------------ | --------------------------------------------------- |
| id                  | int                      | Primary key                                         |
| name                | text                     | نام سگمنت                                           |
| geom_start          | geometry(Point)          | نقطه شروع مسیر                                      |
| geom_end            | geometry(Point)          | نقطه پایان مسیر                                     |
| geometry            | geometry(LineString)     | مسیر کامل (OSM یا routing API)                      |
| distance_km         | float                    | مسافت مسیر (km)                                     |
| time_period         | varchar                  | دوره زمانی: morning_rush / midday / evening / night |
| traffic_color       | varchar                  | رنگ ترافیک (green/yellow/red/darkred)               |
| congestion_index    | float                    | شاخص تراکم (۰–۱)                                    |
| avg_speed_kmh       | float                    | سرعت میانگین فعلی                                   |
| eta_base_min        | float                    | ETA پایه                                            |
| eta_adjusted_min    | float                    | ETA نهایی با ضرایب                                  |
| impact_seconds      | int                      | اختلاف زمانی با حالت آزاد                           |
| ml_detection        | bool                     | آیا تحلیل ML انجام شده؟                             |
| traffic_label       | varchar                  | وضعیت متنی ترافیک                                   |
| multiplier_traffic  | float                    | ضریب ترافیک                                         |
| multiplier_temporal | float                    | ضریب زمانی                                          |
| multiplier_combined | float                    | ضریب نهایی (traffic×temporal×event)                 |
| dominant_event_id   | int (FK→Event, nullable) | رویداد مؤثر فعلی                                    |
| created_by          | int (FK→User)            | ایجادکننده سگمنت                                    |
| last_updated        | timestamp                | آخرین بروزرسانی                                     |

---

### 3. **CameraMonitoring**

|Field|Type|Description|
|---|---|---|
|id|int|Primary key|
|name|text|نام دوربین|
|iframe_url|text|لینک پخش زنده|
|geometry|geometry(Point)|موقعیت مکانی دوربین|
|direction|float|زاویه دید دوربین|
|segment_id|int (FK→Segment)|سگمنت مرتبط|
|vehicle_per_min|int|نرخ عبور خودرو|
|status|varchar|online / offline / maintenance|
|last_update|timestamp|آخرین زمان داده|
|created_by|int (FK→User)|ایجادکننده|

---

### 4. **Event**

|Field|Type|Description|
|---|---|---|
|id|int|Primary key|
|name|text|عنوان رویداد|
|category|varchar|نوع رویداد (concert, sport, repair, accident_related...)|
|geometry|geometry(Point)|موقعیت رویداد|
|start_time|timestamp|شروع|
|end_time|timestamp|پایان|
|human_count|int|تعداد شرکت‌کنندگان|
|nearest_parking_id|int (FK→Parking, nullable)|پارکینگ نزدیک|
|impact_score|float|شدت اثر (۰–۱)|
|traffic_multiplier|float|ضریب تأثیر بر ETA|
|active|bool|فعال یا منقضی‌شده|
|description|text|توضیحات|
|created_by|int (FK→User)|ایجادکننده|

---

### 5. **Accident**

|Field|Type|Description|
|---|---|---|
|id|int|Primary key|
|geometry|geometry(Point/LineString)|محل تصادف|
|segment_id|int (FK→Segment)|سگمنت مرتبط|
|date|timestamp|زمان وقوع|
|severity|varchar|minor / moderate / severe|
|resolved|bool|آیا رفع شده؟|
|police_handler|text|نام یا شماره مأمور رسیدگی|
|created_by|int (FK→User)|ثبت‌کننده|

---

### 6. **Parking**

|Field|Type|Description|
|---|---|---|
|id|int|Primary key|
|name|text|نام پارکینگ|
|geometry|geometry(Point)|موقعیت مکانی|
|type|varchar|public / private / street / underground|
|capacity|int|کل ظرفیت|
|free|int|ظرفیت آزاد فعلی|
|price_per_hour|float|هزینه هر ساعت|
|is_sensor_enabled|bool|آیا داده بلادرنگ دارد؟|
|last_update|timestamp|آخرین بروزرسانی|
|created_by|int (FK→User)|ثبت‌کننده|

---

### 7. **History**

|Field|Type|Description|
|---|---|---|
|id|int|Primary key|
|segment_id|int (FK→Segment)|سگمنت مربوطه|
|datetime|timestamp|زمان ثبت|
|avg_speed_kmh|float|میانگین سرعت|
|vehicle_count|int(nullable)|تعداد خودروها|
|vehicle_per_min|int(nullable)|نرخ عبور خودرو|
|congestion_index|float|شاخص تراکم|
|weather|varchar(nullable)|وضعیت آب‌وهوا|
|camera_id|int (FK→CameraMonitoring, nullable)|منبع دوربین|
|event_id|int (FK→Event, nullable)|رویداد لحظه‌ای|
|is_accident|bool|آیا تصادف در جریان بوده؟|
|distance_km|float|مسافت مسیر|
|time_period|varchar|بازه زمانی (morning/midday/evening/night)|
|traffic_color|varchar|رنگ ترافیک|
|eta_base_min|float|ETA پایه|
|eta_adjusted_min|float|ETA نهایی|
|impact_seconds|int|اختلاف زمانی با حالت آزاد|
|pixels_analyzed|int|تعداد پیکسل‌های تحلیلی|
|tiles_downloaded|int|کاشی‌های سرویس ترافیکی|
|route_segments_count|int|تعداد زیربخش‌ها|
|ml_detection|bool|تحلیل ML انجام شده؟|
|traffic_label|varchar|وضعیت متنی|
|multiplier_traffic|float|ضریب ترافیک|
|multiplier_temporal|float|ضریب زمانی|
|multiplier_combined|float|ضریب نهایی|

---

### 8. **Compare**

|Field|Type|Description|
|---|---|---|
|id|int|Primary key|
|segment_id|int (FK→Segment)|سگمنت|
|compare_period|varchar|week / month / year|
|diff_percent|float|درصد تغییر سرعت یا تراکم|
|trend_label|varchar|better / worse / same|
|generated_at|timestamp|زمان تولید|
|heatmap_url|text|لینک نقشه حرارتی تولیدی|

---

## 🧠 منطق سیستم (Logic)

- **Auth Logic:**  
    JWT Authentication با role-based access.  
    admin → دسترسی کامل، operator → CRUD داده‌ها، viewer → فقط مشاهده.
    
- **Front Map Layer Loader:**  
    API `/api/map/all` تمام داده‌های لایه‌ها را در یک GeoJSON ترکیب‌شده برمی‌گرداند.  
    در فرانت با  Leaflet روی نقشه رندر می‌شود.
    
- **Traffic Processor:**  
    هر ۵ یا ۱۰ دقیقه، worker با سرویس ترافیکی (Google Tile / ML) داده ETA، رنگ ترافیک، و شاخص‌ها را می‌گیرد و:
    
    - در Segment بروزرسانی می‌کند
        
    - Snapshot را در History ذخیره می‌کند
        
- **Analytics Engine:**  
    با داده‌های History روند، میانگین، و heatmap تولید می‌کند. (برای فرانت روی نقشه قابل نمایش باشه)
    
- **Admin Panel:**  
    ایجاد/ویرایش Segments، Events، Cameras، Parking، Accidents  
    با APIهای CRUD کامل و داشبورد آماری.
    

---

## 🧭 APIها (برای فرانت و ادمین)

### 🔹 Auth & Users

|Endpoint|Method|Description|
|---|---|---|
|`/api/auth/register`|POST|ثبت کاربر جدید|
|`/api/auth/login`|POST|ورود و دریافت JWT|
|`/api/auth/me`|GET|اطلاعات کاربر جاری|
|`/api/users`|GET|لیست کاربران|
|`/api/users/{id}`|GET|جزئیات کاربر|
|`/api/users/{id}`|PUT|ویرایش کاربر|
|`/api/users/{id}`|DELETE|حذف کاربر|

---

### 🔹 Base Map APIs

|Endpoint|Method|Description|
|---|---|---|
|`/api/map/all`|GET|همه Segments، Cameras، Events، Accidents، Parking در یک GeoJSON|

---

### 🔹 Segment APIs

|Endpoint|Method|Description|
|---|---|---|
|`/api/segments`|GET|لیست سگمنت‌ها|
|`/api/segments/{id}`|GET|جزئیات سگمنت|
|`/api/segments`|POST|ایجاد سگمنت جدید|
|`/api/segments/{id}`|PUT|ویرایش سگمنت|
|`/api/segments/{id}`|DELETE|حذف سگمنت|
|`/api/segments/update-traffic`|POST|بروزرسانی شاخص ترافیک از سرویس ML|

---

### 🔹 Camera APIs

|Endpoint|Method|Description|
|---|---|---|
|`/api/cameras`|GET|لیست دوربین‌ها|
|`/api/cameras/{id}`|GET|جزئیات دوربین|
|`/api/cameras`|POST|ثبت دوربین|
|`/api/cameras/{id}`|PUT|ویرایش|
|`/api/cameras/{id}`|DELETE|حذف|
|`/api/cameras/update`|POST|بروزرسانی نرخ عبور|

---

### 🔹 Event & Accident APIs

|Endpoint|Method|Description|
|---|---|---|
|`/api/events`|GET|همه رویدادها|
|`/api/events/{id}`|GET|جزئیات|
|`/api/events`|POST|ایجاد رویداد|
|`/api/events/{id}`|PUT|ویرایش|
|`/api/events/{id}`|DELETE|حذف|
|`/api/events/active`|GET|رویدادهای فعال|
|`/api/accidents`|GET|لیست تصادفات|
|`/api/accidents/{id}`|GET|جزئیات|
|`/api/accidents`|POST|ثبت تصادف|
|`/api/accidents/{id}`|PUT|بروزرسانی وضعیت|
|`/api/accidents/{id}`|DELETE|حذف تصادف|
|`/api/accidents/active`|GET|تصادفات باز|

---

### 🔹 Parking APIs

|Endpoint|Method|Description|
|---|---|---|
|`/api/parking`|GET|لیست پارکینگ‌ها|
|`/api/parking/{id}`|GET|جزئیات|
|`/api/parking`|POST|ایجاد|
|`/api/parking/{id}`|PUT|ویرایش|
|`/api/parking/{id}`|DELETE|حذف|

---

### 🔹 Analytics & Dashboard APIs

|Endpoint|Method|Description|
|---|---|---|
|`/api/analytics/heatmap`|GET|نقشه حرارتی ترافیک|
|`/api/analytics/trend`|GET|روند تغییرات ترافیک|
|`/api/analytics/top-segments`|GET|پرترافیک‌ترین مسیرها|
|`/api/analytics/compare`|GET|مقایسه با بازه قبلی (week/month/year)|

Traffic transparent Layer:
https://mt1.google.com/vt?lyrs=h@159000000,traffic|seconds_into_week:-1&style=3&x={tile_x}&y={tile_y}&z={zoom}

Basemaps:
https://mt1.google.com/vt/lyrs=s&x={x}&y={y}&z={z}


osm:
http://tile.openstreetmap.org/{z}/{x}/{y}.png