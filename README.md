## 🌍 Laravel Ülke, Şehir ve İlçe Yapısı Entegrasyonu

Bu rehber, Laravel projenize hazır **Ülke (Country)**, **Şehir (City)** ve **İlçe (District)** model, migration ve seeder yapılarını adım adım entegre etmenizi sağlar.

---

## 🛠️ Gereksinimler

- PHP >= 8.0
- Laravel >= 9.x
- Composer
- MySQL (veya Laravel’in desteklediği bir veritabanı)

---

## 🚀 Kurulum Adımları

### 1. Laravel Projesi Oluştur (Eğer yoksa)

```bash
laravel new ulke-sehir-projem
cd ulke-sehir-projem
```

### 2. GitHub Reposunu Projeye Dahil Et

#### 2.1. Repo’yu Klonla (Geçici olarak)

```bash
git clone https://github.com/kaankaltakkiran/Country_City_district_Laravel.git
```

#### 2.2. Dosyaları Kopyala

```bash
# Modeller
cp /app/Models/Country.php app/Models/
cp /app/Models/City.php app/Models/
cp /app/Models/District.php app/Models/

# Migration dosyaları
cp /database/migrations/* database/migrations/

# Seeder dosyaları
cp database/seeders/CountrySeeder.php database/seeders/
cp database/seeders/CitySeeder.php database/seeders/
cp database/seeders/DistrictSeeder.php database/seeders/
```

### 3. Veritabanı Yapılandırması

`.env` dosyanı düzenleyerek veritabanı bağlantı bilgilerini gir:

```dotenv
DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=ulke_sehir_db
DB_USERNAME=root
DB_PASSWORD=
```

> Bu adımdan önce `ulke_sehir_db` adında bir veritabanı oluşturmayı unutma ve `DB_USERNAME` ve `DB_PASSWORD` değerlerini kendi veritabanı kullanıcı bilgilerinle değiştir.

---

## 🔄 Composer Autoload’ı Yeniden Oluştur

Bu adım, yeni modeller ve seeder'ların otomatik olarak yüklenmesini sağlar:

```bash
composer dump-autoload
```

> **_NOTE:_** Bu adımdan sonra kod geliştirme aracınızı(vscode,windsruf,cursor vs gibi) yeniden başlatmayı unutmayın.

---

## 🧩 Seeder Dosyalarının Tanımlanması

Seeder'ları çalıştırmadan önce `database/seeders/DatabaseSeeder.php` dosyasına şu satırları ekle:

```php
public function run(): void
{
    $this->call([
        CountrySeeder::class,
        CitySeeder::class,
        DistrictSeeder::class,
    ]);
}
```

---

## 🔁 Migration ve Seed İşlemleri

Aşağıdaki komutlarla veritabanı tablolarını oluştur ve verileri yükle:

```bash
php artisan migrate
php artisan db:seed
```

---

## 🔍 Kullanım Örneği

```php

```

---

## 📁 Dosya Yapısı

```
app/
└── Models/
    ├── Country.php
    ├── City.php
    └── District.php

database/
├── migrations/
│   ├── 202x_xx_xx_create_countries_table.php
│   ├── 202x_xx_xx_create_cities_table.php
│   └── 202x_xx_xx_create_districts_table.php
├── seeders/
│   ├── CountrySeeder.php
│   ├── CitySeeder.php
│   ├── DistrictSeeder.php
│   └── DatabaseSeeder.php
```

---
