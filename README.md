# Split-tunneling: российские сервисы напрямую, остальное через VPN

Настройка раздельной маршрутизации трафика в клиентах v2rayN (Windows) и v2rayNG (Android).

**Логика работы:**
- Сайты заблокированные РКН → через VPN
- Российские сервисы (VK, Яндекс, Mail.ru и др.) → напрямую
- Всё остальное → напрямую по умолчанию

---

## Шаг 1 — Скачать geo-файлы

Стандартные файлы от Xray не содержат российских категорий. Нужны файлы от [runetfreedom/russia-v2ray-rules-dat](https://github.com/runetfreedom/russia-v2ray-rules-dat) — обновляются каждые 6 часов.

Скачать (всегда последняя версия):
- `https://raw.githubusercontent.com/runetfreedom/russia-v2ray-rules-dat/release/geoip.dat`
- `https://raw.githubusercontent.com/runetfreedom/russia-v2ray-rules-dat/release/geosite.dat`

### Используемые категории

| Категория | Содержимое |
|---|---|
| `geosite:ru-blocked` | Домены заблокированные в России (antifilter + re:filter) |
| `geosite:ru-blocked-all` | Все известные заблокированные домены (700k+, осторожно) |
| `geosite:ru-available-only-inside` | Домены доступные только изнутри России |
| `geoip:ru` | Российские IP-адреса |
| `geoip:yandex` | IP-адреса Яндекса |

> ⚠️ Файлы не обновляются автоматически. Рекомендуется периодически скачивать свежие версии по тем же ссылкам.

---

## v2rayN (Windows)

### Установка geo-файлов

Положить скачанные файлы в подпапку `bin\` внутри папки v2rayN:

```
X:\Programs\v2rayN\bin\geoip.dat
X:\Programs\v2rayN\bin\geosite.dat
```

> ⚠️ Файлы должны быть именно в `bin\`, а не в корневой папке. Иначе Xray выдаст ошибку при старте.

### Создание routing пресета

**Settings → Routing Settings → Add**

В открывшемся Rule Settings указать:

| Поле | Значение |
|---|---|
| Remarks | Russia Bypass |

### Добавление правил

Нажать **Add Rule** и добавить два правила. Порядок важен — правила применяются сверху вниз.

**Правило 1 — заблокированные в России → proxy:**

| Поле | Значение |
|---|---|
| Remarks | Proxy for blocked |
| outboundTag | proxy |
| Domain | `geosite:ru-blocked` |

**Правило 2 — всё остальное → direct:**

| Поле | Значение |
|---|---|
| Remarks | Direct all others |
| outboundTag | direct |
| Domain | `regexp:.*` |

> ⚠️ Правило `regexp:.*` должно стоять последним. Порядок настраивается через опциональное меню на правую кнопку мыши или горячими клавишами.

### Применение

1. Нажать **Confirm**
2. В главном окне внизу в поле **Routing** выбрать "Russia Bypass"
3. Нажать **Reload**

### Проверка (логи в нижней части окна)

| Сайт | Ожидаемый результат |
|---|---|
| vk.com, yandex.ru, mail.ru | `[socks -> direct]` |
| youtube.com, instagram.com | `[socks -> proxy]` |

---

## v2rayNG (Android)

### Установка geo-файлов

Скопировать скачанные файлы в папку приложения:

```
Android/data/com.v2ray.ang/files/assets/geoip.dat
Android/data/com.v2ray.ang/files/assets/geosite.dat
```

> ⚠️ Если доступ к `Android/data/` ограничен (возможно на Android 11+). Для копирования нужен файловый менеджер с расширенным доступом — например MiXplorer или MT Manager.

### Настройка routing

Сэндвич меню → **Settings → Routing settings**

> Опционально: отключить дефолтные китайские правила если они есть.

Добавить два правила через **+** (порядок настраивается перетаскиванием):

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

Включить routing (toggle вверху) и переподключиться.

### Проверка (логи)

Сэндвич меню → **Logcat**

Включить логирование если пусто: Settings → **Log Level** → `warning` или `info`.
