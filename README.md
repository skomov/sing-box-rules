# sing-box-rules

Персональные домены и подсети для маршрутизации в прокси поверх подключенного набора [runetfreedom/russia-v2ray-rules-dat](https://github.com/runetfreedom/russia-v2ray-rules-dat).

Формат — source rule-set sing-box (`version: 3`), подключается как remote `rule_set` без компиляции в `.srs`.

## Файлы

| Файл | Назначение |
|---|---|
| `proxy-domains.json` | Домены и подсети, которые направлять в прокси |

## Текущий список

- `139.45.192.0/19` и `87.245.216.0/24`: RETN-подсети cloud endpoints приложения камеры. При добавлении endpoints из нескольких `/24` не открывались напрямую и работали через прокси. Широкий `/19` сохраняется до повторного сбора точных активных `/24` и проверки соседних адресов.
- `support.apple.com` удален из персонального списка: домен покрыт подключенным `geosite-ru-blocked`. Отдельный CIDR для него не требуется, но `87.245.216.0/24` пока остается из-за camera app.

## Подключение на роутере

```json
{
  "tag": "personal-proxy",
  "type": "remote",
  "format": "source",
  "url": "https://raw.githubusercontent.com/skomov/sing-box-rules/main/proxy-domains.json",
  "http_client": { "detour": "proxy" }
}
```

И правило маршрутизации `{ "rule_set": ["personal-proxy"], "outbound": "proxy" }`.

Обновление списка: правка `proxy-domains.json` + push; роутер подтянет в пределах `update_interval` (по умолчанию 1d) или после рестарта сервиса.
