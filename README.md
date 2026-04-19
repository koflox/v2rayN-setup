# v2rayN Routing: российские сервисы напрямую, остальное через VPN

## Цель

Настроить split-tunneling в v2rayN (Windows) так чтобы:
- Сайты заблокированные РКН → через VPN (proxy)
- Российские сервисы (VK, Яндекс, Mail.ru и др.) → напрямую (direct)
- Всё остальное → напрямую (direct) по умолчанию

## Требования

- v2rayN v7.20.4+ с Xray core
- Подключение к серверу уже настроено и работает

## Шаг 1 — Скачать актуальные geo-файлы

Стандартные `geoip.dat` и `geosite.dat` от Xray не содержат российских категорий. Нужны файлы от [runetfreedom/russia-v2ray-rules-dat](https://github.com/runetfreedom/russia-v2ray-rules-dat) — они обновляются каждые 6 часов.

Скачать напрямую (всегда последняя версия):
- `https://raw.githubusercontent.com/runetfreedom/russia-v2ray-rules-dat/release/geoip.dat`
- `https://raw.githubusercontent.com/runetfreedom/russia-v2ray-rules-dat/release/geosite.dat`

## Шаг 2 — Положить файлы в папку v2rayN

Скачанные файлы положить в подпапку `bin\` внутри папки v2rayN:

```
X:\Programs\v2rayN\bin\geoip.dat
X:\Programs\v2rayN\bin\geosite.dat
```

> ⚠️ Важно: файлы должны быть именно в `bin\`, а не в корневой папке v2rayN. Иначе Xray их не найдёт и выдаст ошибку при старте.

## Шаг 3 — Создать новый routing пресет

В v2rayN: **Settings → Routing Settings → Add**

В открывшемся Rule Settings:

| Поле | Значение |
|---|---|
| Remarks | Russia Bypass |
| Domain strategy | (оставить пустым) |

## Шаг 4 — Добавить правила

Внутри пресета нажать **Add Rule** и добавить два правила в следующем порядке (порядок важен — правила применяются сверху вниз).

### Правило 1 — Заблокированные в России → proxy

| Поле | Значение |
|---|---|
| Remarks | Proxy for blocked |
| outboundTag | proxy |
| Domain | `geosite:ru-blocked` |

### Правило 2 — Всё остальное → direct

| Поле | Значение |
|---|---|
| Remarks | Direct all others |
| outboundTag | direct |
| Domain | `regexp:.*` |

> ⚠️ Важно: правило `regexp:.*` должно стоять **последним** (большее значение Sort или внизу списка). Иначе оно перехватит весь трафик до проверки остальных правил.

## Шаг 5 — Применить пресет

1. Нажать **Confirm** для сохранения пресета
2. В главном окне v2rayN внизу в поле **Routing** выбрать "Russia Bypass"
3. Нажать **Reload** или перезапустить Xray

## Проверка

Открой несколько сайтов и проверь логи в нижней части окна v2rayN:

| Сайт | Ожидаемый результат |
|---|---|
| vk.com | `[socks -> direct]` |
| yandex.ru | `[socks -> direct]` |
| dzen.ru | `[socks -> direct]` |
| mail.ru | `[socks -> direct]` |
| youtube.com | `[socks -> proxy]` |
| instagram.com | `[socks -> proxy]` |

## Доступные категории в geosite.dat

| Категория | Содержимое |
|---|---|
| `geosite:ru-blocked` | Домены заблокированные в России (antifilter + re:filter) |
| `geosite:ru-blocked-all` | Все известные заблокированные домены (700k+, осторожно) |
| `geosite:ru-available-only-inside` | Домены доступные только изнутри России |
| `geoip:ru` | Российские IP-адреса |
| `geoip:yandex` | IP-адреса Яндекса |

## Дополнительные правила (опционально)

Если нужно явно направить конкретные сервисы через proxy — добавить правила **выше** `regexp:.*`:

| Remarks | Domain | outboundTag |
|---|---|---|
| Proxy Google | `geosite:google` | proxy |
| Proxy YouTube | `geosite:youtube` | proxy |
| Proxy Meta | `geosite:meta` | proxy |

## Обновление geo-файлов

Файлы не обновляются автоматически. Рекомендуется периодически скачивать свежие версии по тем же ссылкам и заменять файлы в `bin\`. Репозиторий runetfreedom обновляется каждые 6 часов.
