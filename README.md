# splitkz-legal → сайт `split.kz`

Изначально репозиторий был только правовыми документами (`privacy-policy.html`,
`terms.html`, `data-consent.html`, `support.html`), опубликованными на
`ulan414.github.io/splitkz-legal/`. С файлом `CNAME` он же обслуживает домен
`split.kz` целиком — тот, что уже зашит в приложение (`lib/models/models.dart`,
`AndroidManifest.xml`, `Runner.entitlements`).

## Что добавилось и зачем

| Файл | Зачем |
| --- | --- |
| `CNAME` | Говорит GitHub Pages отдавать этот репозиторий по адресу `split.kz`, а не только по `github.io`. |
| `.nojekyll` | Без него Jekyll игнорирует `.well-known/` — папки с точкой в начале имени не публикуются по умолчанию. |
| `.well-known/apple-app-site-association` | Universal Links iOS: подтверждает `split.kz`, что ссылки `/g/*` открывает приложение `936TUD5S34.kz.splitkz.splitkz`, а не Safari. |
| `.well-known/assetlinks.json` | App Links Android: то же самое, но для `kz.splitkz.splitkz`. Отпечатки — `upload-keystore.p12` (сборки отсюда) и debug-ключ (`flutter run`). **Отпечаток Play App Signing нужно дописать**, когда приложение появится в Play Console → Test and release → Setup → App integrity — иначе ссылка не подтвердится на телефонах, где APK стоит из Play. |
| `404.html` | GitHub Pages отдаёт этот файл на любой несуществующий путь, сохраняя URL в адресной строке — на этом трюке держится `/g/<code>`: страницы `/g/*` физически не существуют (код в ссылке — любой), поэтому единственный способ поймать их статикой — общий 404 с JS, который читает `location.pathname`. Пытается открыть `splitkz://g/<код>`, а если приложения нет — показывает код и подсказку установить. |

## Известное ограничение

`/g/<code>` технически отвечает статусом `404` (это и есть трюк выше), а не
`200`. Часть ботов превью ссылок в мессенджерах (не все) на нестандартный
статус не смотрит и карточку всё равно строит по `og:title`/`og:description`
из тела ответа — но гарантии нет. Если превью в чатах будут выглядеть бедно
(просто голая ссылка без карточки), первый кандидат на замену — Cloudflare
Pages: тот же бесплатный статический хостинг, но с настоящими редиректами и
статусом `200` на любой путь.

## Проверить после DNS

```bash
# Android — Google подтверждает файл сама, но проверить можно и руками:
curl -s https://split.kz/.well-known/assetlinks.json

# iOS:
curl -s https://split.kz/.well-known/apple-app-site-association

# Приглашение:
curl -sI https://split.kz/g/test
```
