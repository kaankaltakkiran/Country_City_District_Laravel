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
# Modelleri kopyala
cp Country_City_district_Laravel/app/Models/Country.php app/Models/
cp Country_City_district_Laravel/app/Models/City.php app/Models/
cp Country_City_district_Laravel/app/Models/District.php app/Models/

# Migration dosyalarını kopyala
cp Country_City_district_Laravel/database/migrations/* database/migrations/

# Seeder dosyalarını kopyala
cp Country_City_district_Laravel/database/seeders/CountrySeeder.php database/seeders/
cp Country_City_district_Laravel/database/seeders/CitySeeder.php database/seeders/
cp Country_City_district_Laravel/database/seeders/DistrictSeeder.php database/seeders/

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

Bu örnek kullanım Türkiye'deki seçilen il ve ilçeleri gösterir.

Bu komut ile `HomeController.php` dosyasını oluşturun:

```bash
php artisan make:controller HomeController
```

`app/Http/Controllers/HomeController.php` dosyasını aşağıdaki gibi düzenleyin:

```php
<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;
use App\Models\Country;
use App\Models\City;
use App\Models\District;

class HomeController extends Controller
{
    //
    public function index()
{
   /*  $countries = Country::all();
    return view('welcome', compact('countries')); */
    $countries = Country::where('id', 214)->get();
    return view('welcome', compact('countries'));
}
public function getCities($countryId)
{
    return City::where('country_id', $countryId)->get();
}

public function getDistricts($cityId)
{
    return District::where('city_id', $cityId)->get();
}
}
```

Blade View `resources/views/welcome.blade.php` dosyasını aşağıdaki gibi düzenleyin:

```php
<!DOCTYPE html>
<html>
<head>
    <title>Ülke Şehir İlçe Seçimi</title>
    <meta name="csrf-token" content="{{ csrf_token() }}">
</head>
<body>
    <h2>Adres Seçimi</h2>

    <form method="POST" action="#">
        @csrf

        <label for="country">Ülke:</label>
        <select id="country" name="country_id">
            <option value="">Seçiniz</option>
            @foreach ($countries as $country)
                <option value="{{ $country->id }}">{{ $country->title }}</option>
            @endforeach
        </select>

        <br><br>

        <label for="city">Şehir:</label>
        <select id="city" name="city_id">
            <option value="">Önce ülke seçiniz</option>
        </select>

        <br><br>

        <label for="district">İlçe:</label>
        <select id="district" name="district_id">
            <option value="">Önce şehir seçiniz</option>
        </select>
    </form>

    <br>
    <p><strong>Seçilen Adres:</strong> <span id="selected-address">Henüz seçim yapılmadı.</span></p>

    <script src="https://code.jquery.com/jquery-3.6.0.min.js"></script>
    <script>
        let selectedCountry = '';
        let selectedCity = '';
        let selectedDistrict = '';

        function updateAddressText() {
            const fullAddress = [selectedCountry, selectedCity, selectedDistrict]
                .filter(Boolean)
                .join(' / ');
            $('#selected-address').text(fullAddress || 'Henüz seçim yapılmadı.');
        }

        $('#country').on('change', function () {
            let countryId = $(this).val();
            selectedCountry = $(this).find("option:selected").text();
            selectedCity = '';
            selectedDistrict = '';
            updateAddressText();

            $('#city').html('<option>Yükleniyor...</option>');
            $('#district').html('<option>Önce şehir seçiniz</option>');

            if (countryId) {
                $.get(`/get-cities/${countryId}`, function (data) {
                    $('#city').html('<option value="">Seçiniz</option>');
                    $.each(data, function (key, city) {
                        $('#city').append(`<option value="${city.id}">${city.title}</option>`);
                    });
                });
            } else {
                $('#city').html('<option>Önce ülke seçiniz</option>');
            }
        });

        $('#city').on('change', function () {
            let cityId = $(this).val();
            selectedCity = $(this).find("option:selected").text();
            selectedDistrict = '';
            updateAddressText();

            $('#district').html('<option>Yükleniyor...</option>');

            if (cityId) {
                $.get(`/get-districts/${cityId}`, function (data) {
                    $('#district').html('<option value="">Seçiniz</option>');
                    $.each(data, function (key, district) {
                        $('#district').append(`<option value="${district.id}">${district.title}</option>`);
                    });
                });
            } else {
                $('#district').html('<option>Önce şehir seçiniz</option>');
            }
        });

        $('#district').on('change', function () {
            selectedDistrict = $(this).find("option:selected").text();
            updateAddressText();
        });
    </script>
</body>
</html>
```

Route tanımlamalarını `routes/web.php` dosyasına ekleyin:

```php
Route::get('/', [HomeController::class, 'index']);
Route::get('/get-cities/{country_id}', [HomeController::class, 'getCities']);
Route::get('/get-districts/{city_id}', [HomeController::class, 'getDistricts']);
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

## 📖 Ek Kaynaklar
