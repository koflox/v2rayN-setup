# Split-tunneling: российские сервисы напрямую, остальное через VPN

Настройка раздельной маршрутизации трафика в клиентах v2rayN (Windows) и v2rayNG (Android).

**Логика работы:**
- Сайты заблокированные РКН → через VPN
- Российские сервисы (VK, Яндекс, Mail.ru и др.) → напрямую
- Всё остальное → напрямую по умолчанию

## Оглавление

- [1. Geo-файлы](#1-geo-файлы)
  - [1.1 Ссылки на файлы](#11-ссылки-на-файлы)
- [2. Routing правила](#2-routing-правила)
- [3. v2rayN (Windows)](#3-v2rayn-windows)
  - [3.1 Загрузка geo-файлов](#31-загрузка-geo-файлов)
  - [3.2 Создание routing пресета](#32-создание-routing-пресета)
  - [3.3 Применение](#33-применение)
- [4. v2rayNG (Android)](#4-v2rayng-android)
  - [4.1 Загрузка geo-файлов](#41-загрузка-geo-файлов)
  - [4.2 Настройка routing](#42-настройка-routing)
  - [4.3 Настройка per-app](#43-настройка-per-app)

---

## 1. Geo-файлы

Стандартные файлы от Xray не содержат российских категорий. Нужны файлы от [runetfreedom/russia-v2ray-rules-dat](https://github.com/runetfreedom/russia-v2ray-rules-dat) — обновляются каждые 6 часов.

Доступные категории:

| Категория | Содержимое |
|---|---|
| `geosite:ru-blocked` | Домены заблокированные в России (antifilter + re:filter) |
| `geosite:ru-blocked-all` | Все известные заблокированные домены (700k+, осторожно) |
| `geosite:ru-available-only-inside` | Домены доступные только изнутри России |
| `geoip:ru` | Российские IP-адреса |
| `geoip:yandex` | IP-адреса Яндекса |

### 1.1 Ссылки на файлы

Всегда актуальная версия:
- `https://raw.githubusercontent.com/runetfreedom/russia-v2ray-rules-dat/release/geoip.dat`
- `https://raw.githubusercontent.com/runetfreedom/russia-v2ray-rules-dat/release/geosite.dat`

---

## 2. Routing правила

Правила применяются сверху вниз — порядок важен. Для корректной работы правило с `regexp:.*` должно стоять последним.

**Правило 1 — заблокированные в России → proxy:**

| Поле | Значение |
|---|---|
| Remarks | Proxy for blocked |
| Domain | `geosite:ru-blocked` |
| Outbound | proxy |

**Правило 2 — всё остальное → direct:**

| Поле | Значение |
|---|---|
| Remarks | Direct all others |
| Domain | `regexp:.*` |
| Outbound | direct |

Ожидаемый результат после настройки:

| Сайт | Маршрут |
|---|---|
| vk.com, yandex.ru, mail.ru | `direct` |
| youtube.com, instagram.com | `proxy` |

---

## 3. v2rayN (Windows)

Скачать клиент: [github.com/2dust/v2rayN/releases](https://github.com/2dust/v2rayN/releases)

### 3.1 Загрузка geo-файлов

Скачать файлы по ссылкам из [раздела 1.1](#11-ссылки-на-файлы) и положить в подпапку `bin\` внутри папки v2rayN:

```
X:\Programs\v2rayN\bin\geoip.dat
X:\Programs\v2rayN\bin\geosite.dat
```

> ⚠️ Файлы должны быть именно в `bin\`, а не в корневой папке. Иначе Xray выдаст ошибку при старте.

> ⚠️ Файлы не обновляются автоматически. Рекомендуется периодически скачивать свежие версии по тем же ссылкам.

### 3.2 Создание routing пресета

**Settings → Routing Settings → Add**

В открывшемся Rule Settings указать Remarks: `Russia Bypass`.

Добавить правила через **Add Rule** согласно [разделу 2](#2-routing-правила). Порядок настраивается через поле Sort или перетаскиванием.

### 3.3 Применение

1. Нажать **Confirm**
2. В главном окне внизу в поле **Routing** выбрать "Russia Bypass"
3. Нажать **Reload**

Логи подключений отображаются в нижней части главного окна.

---

## 4. v2rayNG (Android)

Скачать клиент: [github.com/2dust/v2rayNG/releases](https://github.com/2dust/v2rayNG/releases) (версия 2.0.18+)

### 4.1 Загрузка geo-файлов

Сэндвич меню → **Asset files** → **+** → **Add URL**

Добавить два ассета:

**geosite.dat:**

| Поле | Значение |
|---|---|
| Remarks | `geosite.dat` |
| URL | `https://raw.githubusercontent.com/runetfreedom/russia-v2ray-rules-dat/release/geosite.dat` |

**geoip.dat:**

| Поле | Значение |
|---|---|
| Remarks | `geoip.dat` |
| URL | `https://raw.githubusercontent.com/runetfreedom/russia-v2ray-rules-dat/release/geoip.dat` |

После добавления нажать иконку облака (↓) для загрузки файлов.

> ⚠️ Название в поле Remarks должно точно совпадать с именем существующего файла — иначе файлы не перезапишутся.

**Альтернатива — ручное копирование через ПК:**
Подключить телефон по USB и скопировать файлы из [раздела 1.1](#11-ссылки-на-файлы) напрямую в:

```
Android/data/com.v2ray.ang/files/assets/geoip.dat
Android/data/com.v2ray.ang/files/assets/geosite.dat
```

### 4.2 Настройка routing

Сэндвич меню → **Settings → Routing settings**

Отключить дефолтные китайские правила если они есть.

Добавить правила через **+** согласно [разделу 2](#2-routing-правила). Порядок настраивается перетаскиванием.

Включить routing (toggle вверху) и переподключиться.

Логи: сэндвич меню → **Log**. Включить если пусто: Settings → **Log Level** → `warning` или `info`.

### 4.3 Настройка per-app

Сэндвич меню → **Per-app settings** позволяет выбрать отдельные приложения которые будут идти через VPN или напрямую, независимо от routing правил.
