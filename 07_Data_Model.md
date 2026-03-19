## 7. Побудова моделі даних

Основою системи є структурована реляційна модель даних. Головними сутностями предметної області виступають: Користувач (User), Транспортний засіб (Vehicle), Локація (Facility), Вантаж (Load) та Призначення (Assignment).

Сутність «Транспортний засіб» містить атрибути ідентифікації (унікальний номер), фізичних характеристик (тип, максимальна вага, довжина, ширина, висота) та операційного стану (поточний поштовий індекс, статус зайнятості). Сутність «Локація» зберігає інформацію про географічні точки (назва об'єкта, адреса, місто, штат, поштовий індекс).

Центральною сутністю транзакційного процесу є «Вантаж». Вона пов'язана із сутністю «Локація» двома зв'язками типу «один до багатьох», оскільки кожен вантаж обов'язково має одну точку завантаження та одну точку розвантаження, при цьому на одній локації може оброблятися безліч вантажів. Вантаж також містить специфічні атрибути: кількість одиниць (piece count), загальна вага та поточний статус виконання.

Зв'язок між вантажем та транспортним засобом реалізується через асоціативну сутність «Призначення». Вона вирішує проблему зв'язку «багато до багатьох» у часі (машина може перевозити багато вантажів протягом місяця, а вантаж у разі перевантаження може змінити машину). Атрибутами призначення є ідентифікатори вантажу та авто, час створення запису та узгоджена вартість рейсу.

### Мал. 3. ER-діаграма логістичної інформаційної системи

```mermaid
erDiagram
    DISPATCHER ||--o{ LOAD : manages
    VEHICLE ||--o{ ASSIGNMENT : has
    LOAD ||--o| ASSIGNMENT : has
    FACILITY ||--o{ LOAD : "pickup at"
    FACILITY ||--o{ LOAD : "delivery to"

    DISPATCHER {
        uuid id PK
        string full_name
        string email
        string password_hash
        datetime created_at
    }

    VEHICLE {
        int id PK
        string vin_number "Унікальний номер"
        string vehicle_type "Sprinter / Box / Straight"
        string current_zipcode
        float max_weight_lbs
        float length_inches
        float width_inches
        float height_inches
        string status "Available / In Transit / Out of Service"
    }

    FACILITY {
        int id PK
        string company_name
        string street_address
        string city
        string state
        string zipcode
    }

    LOAD {
        int id PK
        uuid dispatcher_id FK
        int pickup_facility_id FK
        int delivery_facility_id FK
        int piece_count
        float total_weight_lbs
        string status "Unassigned / Dispatched / Delivered"
        datetime created_at
    }

    ASSIGNMENT {
        int id PK
        int load_id FK
        int vehicle_id FK
        datetime assigned_at
        float agreed_rate "Узгоджена вартість перевезення"
    }
```