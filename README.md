composer create-project laravel/laravel app
cd app
cp .env.example .env
php artisan key:generate
touch database/database.sqlite
php artisan migrate
npm install
npm run build
php artisan serve --host=0.0.0.0 --port=8000
composer create-project laravel/laravel spinplay
cd spinplay

composer require laravel/breeze --dev
php artisan breeze:install

php artisan migrate
npm install
npm run build

php artisan serve
Route::middleware('auth')->group(function () {
    Route::get('/wallet', [WalletController::class, 'index']);
    Route::post('/wallet/top-up', [TopUpController::class, 'create']);
    Route::get('/shop', [ShopController::class, 'index']);
    Route::post('/shop/{item}/buy', [ShopController::class, 'buy']);
    Route::get('/inventory', [InventoryController::class, 'index']);
});

Route::post('/payment/webhook', [PaymentWebhookController::class, 'handle']);
import csv
import requests
from bs4 import BeautifulSoup
from urllib.parse import urljoin

url = "https://badak178banyak.com/"

headers = {
    "User-Agent": "Mozilla/5.0"
}

response = requests.get(url, headers=headers, timeout=30)
response.raise_for_status()

soup = BeautifulSoup(response.text, "html.parser")

rows = []

# Mengambil semua teks yang terlihat
for element in soup.find_all(["h1", "h2", "h3", "h4", "h5", "h6", "p", "li"]):
    text = " ".join(element.get_text(" ", strip=True).split())

    if text:
        rows.append({
            "jenis": element.name,
            "data": text,
            "url": ""
        })

# Mengambil semua tautan
for link in soup.find_all("a", href=True):
    text = " ".join(link.get_text(" ", strip=True).split())
    target = urljoin(url, link["href"])

    rows.append({
        "jenis": "link",
        "data": text,
        "url": target
    })

with open("data_situs_publik.csv", "w", newline="", encoding="utf-8-sig") as file:
    writer = csv.DictWriter(file, fieldnames=["jenis", "data", "url"])
    writer.writeheader()
    writer.writerows(rows)

print(f"{len(rows)} baris berhasil disimpan ke data_situs_publik.csv")
