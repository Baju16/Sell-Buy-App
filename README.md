# Sell-Buy App – portal z ogłoszeniami kupna-sprzedaży

Projekt zaliczeniowy z przedmiotu **AI1**.

- [Repozytorium projektu](https://github.com/lab2-team1/ai1)
- [Tablica projektowa](https://github.com/orgs/lab2-team1/projects/1)

## Spis treści

1. [Opis projektu](#opis-projektu)
2. [Zespół](#zespół)
3. [Funkcjonalności](#funkcjonalności)
4. [Technologie](#technologie)
5. [Wymagania](#wymagania)
6. [Instalacja i uruchomienie](#instalacja-i-uruchomienie)
7. [Konta testowe](#konta-testowe)
8. [Konfiguracja (.env)](#konfiguracja-env)
9. [Struktura projektu](#struktura-projektu)
10. [Najważniejsze trasy](#najważniejsze-trasy)
11. [Baza danych](#baza-danych)
12. [Widoki aplikacji](#widoki-aplikacji)
13. [Testy i jakość kodu](#testy-i-jakość-kodu)
14. [Rozwiązywanie problemów](#rozwiązywanie-problemów)

---

## Opis projektu

Aplikacja webowa oparta na frameworku Laravel 12, będąca portalem z ogłoszeniami kupna-sprzedaży. Użytkownicy mogą wystawiać własne ogłoszenia ze zdjęciami, przeglądać i wyszukiwać oferty, kupować przedmioty, wybierać metodę płatności oraz wystawiać sobie nawzajem oceny. Administrator zarządza użytkownikami, kategoriami, adresami i ogłoszeniami.

## Zespół

| Profil | Rola |
| ------ | ------ |
| [Dawid Bajek](https://github.com/Baju16) | lider zespołu |
| [Krzysztof Dąbrowski](https://github.com/SooNlK) | członek zespołu |
| [Oscar Borowiec](https://github.com/oscarek03) | członek zespołu |
| [Łukasz Babiś](https://github.com/Chirockat) | członek zespołu |

## Funkcjonalności

**Dla każdego (gość)**
- przeglądanie strony głównej i listy ogłoszeń, szczegóły ogłoszenia (licznik odwiedzin),
- filtrowanie (kategoria, zakres dat, zakres cen) i sortowanie (cena, data, tytuł),
- wyszukiwarka z podpowiedziami (autouzupełnianie),
- publiczne profile użytkowników wraz z ocenami,
- rejestracja i logowanie.

**Dla zalogowanego użytkownika**
- panel użytkownika (dashboard) z danymi konta, edycja profilu,
- zarządzanie własnymi ogłoszeniami (dodawanie, edycja, usuwanie),
- obsługa zdjęć ogłoszeń: wiele zdjęć, usuwanie, wybór zdjęcia głównego, zmiana kolejności,
- zarządzanie własnymi adresami,
- kupowanie ogłoszeń, wybór płatności (BLIK, przelew, płatność przy odbiorze) i potwierdzenie transakcji,
- historia transakcji i statystyki transakcji,
- wystawianie ocen innym użytkownikom po transakcji,
- uwierzytelnianie dwuskładnikowe (2FA, TOTP) – włączanie i wyłączanie w panelu.

**Dla administratora**
- CRUD użytkowników, kategorii, adresów i ogłoszeń,
- panel administracyjny z osobnym menu bocznym (dostęp chroniony middleware `is_admin`).

**Dodatkowo**
- promowane ogłoszenia – przy dodaniu ogłoszenie dostaje losową promocję (1–48 h) i do wygaśnięcia jest wyświetlane na górze listy,
- własne strony błędów (403, 404, 419, 500).

## Technologie

| Obszar | Technologia |
| ------ | ------ |
| Backend | PHP ^8.2, Laravel ^12.0 |
| Baza danych | PostgreSQL (zalecana; wymagana dla sortowania promocji – używa `NOW()`) |
| Frontend | Blade, Vite ^6, PostCSS + Autoprefixer, Axios |
| 2FA | `spomky-labs/otphp`, `bacon/bacon-qr-code` |
| Narzędzia deweloperskie | Laravel Pint, Pail, Tinker, Sail, PHPUnit ^11, Mockery, Faker |
| Środowisko | Composer, Node.js (LTS), npm |

## Wymagania

- PHP 8.2+ z rozszerzeniami `pdo_pgsql` i `pgsql` (odkomentowane w `php.ini`),
- Composer,
- Node.js LTS wraz z npm,
- PostgreSQL (domyślnie port `5432`, użytkownik `postgres`) oraz `psql` dostępne w zmiennej `Path`, np. `C:\Program Files\PostgreSQL\16\bin`,
- Windows (skrypt `start.bat`) – na innych systemach wykonaj kroki z sekcji [Instalacja ręczna](#instalacja-ręczna).

## Instalacja i uruchomienie

### Szybki start (Windows)

1. Przeczytaj plik `_setup/AI1 Instalacja potrzebnych komponentów.pdf` i wykonaj wszystkie opisane polecenia, włącznie z instalacją rozszerzeń VS Code (`_setup/Instalacja rozszerzeń VSCode.bat`).
2. Zainstaluj [PostgreSQL](https://www.enterprisedb.com/downloads/postgres-postgresql-downloads) z ustawieniami domyślnymi, jako hasło użytkownika `postgres` podaj `123`.
3. Zainstaluj [Node.js LTS](https://nodejs.org/en) z ustawieniami domyślnymi.
4. Dodaj `C:\Program Files\PostgreSQL\16\bin` do zmiennej środowiskowej `Path` (zmienne użytkownika → Path → Nowy).
5. Uruchom `start.bat` z katalogu głównego projektu. Skrypt:
   - ustawia `ExecutionPolicy` dla PowerShell,
   - tworzy bazę `ShopDB` (jeśli nie istnieje),
   - kopiuje `.env.example` do `.env` i wpisuje dane połączenia z PostgreSQL,
   - wykonuje `composer install` i `npm install`,
   - wykonuje `php artisan migrate:fresh --seed` (**usuwa wszystkie dane w bazie**),
   - tworzy link `storage` (`php artisan storage:link`).
6. Uruchom serwery (w dwóch osobnych terminalach):

   ```bash
   npm run dev
   ```

   ```bash
   php artisan serve
   ```

7. Otwórz w przeglądarce adres wyświetlony przez `php artisan serve` (domyślnie http://127.0.0.1:8000).

### Instalacja ręczna

```bash
composer install
npm install
cp .env.example .env
php artisan key:generate
```

Skonfiguruj połączenie z bazą w `.env` (patrz [Konfiguracja](#konfiguracja-env)), utwórz bazę `ShopDB`, a następnie:

```bash
php artisan migrate --seed
php artisan storage:link
npm run dev
php artisan serve
```

> `start.bat` nie wywołuje `php artisan key:generate` – jeśli aplikacja zgłasza brak `APP_KEY`, wykonaj tę komendę ręcznie.

Alternatywnie można uruchomić wszystko jednym poleceniem (serwer, kolejka, logi, Vite):

```bash
composer run dev
```

### Build produkcyjny frontendu

```bash
npm run build
```

## Konta testowe

Dane tworzone przez `UserSeeder` (hasło dla wszystkich kont: `1234`):

| Rola | E-mail |
| ------ | ------ |
| administrator | swit@email.com |
| użytkownik | kowalski@email.com |
| użytkownik | nowak@email.com |
| użytkownik | wisniewski@email.com |

Seedery dodają także 10 kategorii (m.in. Elektronika, Moda, Motoryzacja) oraz przykładowe ogłoszenia (`ListingSeeder`). Konta testowe służą wyłącznie do celów deweloperskich – nie używaj ich w środowisku produkcyjnym.

## Konfiguracja (.env)

Skrypt `start.bat` ustawia następujące wartości połączenia z bazą:

```env
DB_CONNECTION=pgsql
DB_HOST=127.0.0.1
DB_PORT=5432
DB_DATABASE=ShopDB
DB_USERNAME=postgres
DB_PASSWORD=123
```

Pozostałe istotne ustawienia z `.env.example`: `SESSION_DRIVER=database`, `QUEUE_CONNECTION=database`, `CACHE_STORE=database`, `MAIL_MAILER=log`, `FILESYSTEM_DISK=local`. Zmień `APP_ENV` i `APP_DEBUG=false` oraz hasła przed wdrożeniem produkcyjnym.

## Struktura projektu

```
app/
  Http/Controllers/    # Auth, Home, Listing, Search, Transaction, User, UserAddress,
                       # UserRating, Category, Image oraz Admin/{User,Address}
  Http/Middleware/     # IsAdmin
  Models/              # User, Listing, ListingImage, Category, Address, Transaction, UserRating
bootstrap/             # konfiguracja aplikacji i aliasy middleware
config/                # konfiguracja Laravel
database/
  migrations/          # schemat bazy
  seeders/             # User, Category, Listing
  factories/
docs-img/              # zrzuty ekranu i diagram ERD użyte w dokumentacji
resources/
  css/, js/            # zasoby frontendu (Vite)
  views/               # widoki Blade (auth, dashboards, listings, payment, users, errors, shared)
routes/web.php         # definicje tras
tests/                 # testy PHPUnit (Feature, Unit)
_setup/                # instrukcje instalacji środowiska
start.bat              # skrypt inicjalizujący środowisko (Windows)
```

## Najważniejsze trasy

| Metoda | Ścieżka | Opis |
| ------ | ------ | ------ |
| GET | `/` | strona główna z filtrami i sortowaniem |
| GET | `/search`, `/search-suggestions` | wyszukiwarka i podpowiedzi |
| GET | `/listings`, `/listings/{id}` | lista i szczegóły ogłoszeń |
| GET/POST | `/auth/login`, `/auth/register` | logowanie, rejestracja |
| GET/POST | `/2fa/verify` | weryfikacja kodu 2FA |
| GET | `/user/dashboard`, `/user/transactions`, `/user/transaction-stats` | panel użytkownika *(auth)* |
| resource | `/user/listings`, `/user/addresses` | własne ogłoszenia i adresy *(auth)* |
| GET/POST | `/user/2fa`, `/user/2fa/enable`, `/user/2fa/disable` | konfiguracja 2FA *(auth)* |
| POST | `/listings/{id}/buy` | zakup ogłoszenia *(auth)* |
| GET/POST | `/payment/choose/{id}`, `/payment/confirmation/{id}` | płatność i potwierdzenie *(auth)* |
| POST | `/transactions/{id}/confirm` | potwierdzenie transakcji przez kupującego *(auth)* |
| GET/POST | `/userRatings/create/{transaction_id}`, `/userRatings` | oceny użytkowników |
| resource | `/admin/users`, `/admin/categories`, `/admin/addresses`, `/admin/listings` | panel administratora *(auth + is_admin)* |

Pełną listę tras wyświetli polecenie `php artisan route:list`.

## Baza danych

Tabele: `users`, `categories`, `addresses`, `listings`, `listing_images`, `transactions`, `user_ratings` (oraz tabele techniczne Laravel, m.in. `sessions`).

- `users` – dane osobowe, flaga `admin`, `otp_secret` i `two_factor_enabled` dla 2FA,
- `listings` – tytuł, opis, cena, status, licznik odwiedzin `visits`, `promotion_expires_at`,
- `transactions` – kupujący, sprzedający, ogłoszenie, kwota, metoda i status płatności,
- `user_ratings` – ocena (liczba) i komentarz powiązane z transakcją.

![Diagram ERD](./docs-img/erd.png)

## Widoki aplikacji

*Strona główna*
![Strona główna](./docs-img/glowna.jpeg)

*Logowanie*
![Logowanie](./docs-img/logowanie.png)

*Rejestracja*
![Rejestracja](./docs-img/rejstracja.png)

*Oferta*
![Oferta](./docs-img/oferta.jpeg)

*CRUD ogłoszeń*
![Oferty](./docs-img/oferty.png)
![Nowa oferta](./docs-img/nowa-oferta.png)
![Edycja oferty](./docs-img/edytowanie-oferty.png)
![Usuwanie oferty](./docs-img/usuwanie-oferty.png)

*CRUD kategorii*
![Kategorie](./docs-img/kategorie.png)
![Nowa kategoria](./docs-img/nowa-kategoria.png)
![Edycja kategorii](./docs-img/edytowanie-kategorii.png)
![Usuwanie kategorii](./docs-img/usuwanie-kategorii.png)

*CRUD adresów*
![Adresy](./docs-img/adresy.png)
![Nowy adres](./docs-img/nowy-adres.png)
![Edycja adresu](./docs-img/edytowanie-adresu.png)
![Usuwanie adresu](./docs-img/usuwanie-adresu.png)

*Zarządzanie użytkownikami*
![Użytkownicy](./docs-img/użytkownik.png)
![Nowy użytkownik](./docs-img/nowy-użytkownik.png)
![Edycja użytkownika](./docs-img/edytowanie-użytkownika.png)
![Usuwanie użytkownika](./docs-img/usuwanie-użytkownika.png)

*Profil użytkownika*
![Panel](./docs-img/panel.png)
![Panel – oferty](./docs-img/panel-oferty.png)
![Panel – historia](./docs-img/panel-historia.png)
![Panel – statystyki](./docs-img/panel-statystyki.png)
![Panel – 2FA](./docs-img/panel-2fa.png)

*Zakup i płatność*
![Zakup](./docs-img/kupowanie.png)
![Płatność](./docs-img/płatność.png)
![Potwierdzenie](./docs-img/potwierdzenie.png)

## Testy i jakość kodu

```bash
php artisan test        # testy PHPUnit (obecnie tylko przykładowe testy szkieletu)
./vendor/bin/pint       # formatowanie kodu (Laravel Pint)
```

## Rozwiązywanie problemów

- **`could not find driver`** – odkomentuj `extension=pdo_pgsql` i `extension=pgsql` w `php.ini`.
- **`psql` nie jest rozpoznawane** – dodaj katalog `bin` PostgreSQL do zmiennej `Path` i otwórz nowy terminal.
- **Brak `APP_KEY`** – uruchom `php artisan key:generate`.
- **Brak zdjęć ogłoszeń** – uruchom `php artisan storage:link`.
- **Brak stylów / błędy Vite** – upewnij się, że działa `npm run dev` (lub wykonaj `npm run build`).
- **Błąd wykonywania skryptów w PowerShell** – `start.bat` ustawia `RemoteSigned`; można to zrobić ręcznie: `Set-ExecutionPolicy -Scope CurrentUser -ExecutionPolicy RemoteSigned`.
