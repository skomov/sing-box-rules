# sing-box-rules

Персональные правила маршрутизации поверх подключенного набора [runetfreedom/russia-v2ray-rules-dat](https://github.com/runetfreedom/russia-v2ray-rules-dat): цели для прокси и точечные исключения для прямого выхода.

Формат — source rule-set sing-box (`version: 3`), подключается как remote `rule_set` без компиляции в `.srs`.

## Файлы

| Файл | Назначение |
|---|---|
| `proxy-domains.json` | Домены и подсети, которые направлять в прокси |
| `direct-domains.json` | Домены, которые направлять напрямую до проверки общих списков для прокси |

## Текущий список

- `139.45.192.0/19` и `87.245.216.0/24`: RETN-подсети cloud endpoints приложения камеры. При добавлении endpoints из нескольких `/24` не открывались напрямую и работали через прокси. Широкий `/19` сохраняется до повторного сбора точных активных `/24` и проверки соседних адресов.
- `support.apple.com` удален из персонального списка: домен покрыт подключенным `geosite-ru-blocked`. Отдельный CIDR для него не требуется, но `87.245.216.0/24` пока остается из-за camera app.
- `t.tb.ru` в `direct-domains.json`: точечное исключение перед общим `geoip-ru-blocked`, не распространяется на другие поддомены `tb.ru` или сайты на тех же IP.

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

Для прямого исключения подключить `direct-domains.json` как отдельный remote source rule-set с тегом `personal-direct`. После `sniff` и перехвата DNS, но **до** общих rule-set'ов для прокси, добавить `{ "rule_set": ["personal-direct"], "outbound": "direct" }`. Добавление домена в `proxy-domains.json` не обеспечивает прямой выход: весь этот список привязан к `proxy`.

Обновление списков: правка нужного JSON + push; роутер подтянет подключённый список в пределах `update_interval` (по умолчанию 1d) или после штатного canary-применения конфигурации. Сам по себе новый файл не меняет маршрутизацию, пока его не подключили на роутере.
