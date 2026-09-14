# sing-box-rules

Личные списки доменов и подсетей для маршрутизации в прокси, поверх общих списков [itdoginfo/allow-domains](https://github.com/itdoginfo/allow-domains).

Формат — source rule-set sing-box (`version: 3`), подключается как remote `rule_set` без компиляции в `.srs`.

## Файлы

| Файл | Назначение |
|---|---|
| `proxy-domains.json` | Домены, которые направлять в прокси |

## Текущий список

- `support.apple.com` — Akamai geo-DNS отдает нашему резолверу эджи в сети UA-RETN (87.245.216.0/24), TCP/443 напрямую не устанавливается; через прокси 200. Обоснование: [репорт в allow-domains](https://github.com/itdoginfo/allow-domains/discussions/75#discussioncomment-18440303). Убрать, когда домен появится в списке itdog.

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
