# Cloudflare: исправленный порядок публикации

## 1. Исправление ошибки `npm ci / Missing ... from lock file`

В старой версии архива файл `package-lock.json` остался от MongoDB и не соответствует `package.json` с Wrangler. В ЭТОМ архиве старый lock-файл удалён. Cloudflare сможет установить зависимости через `npm install` и создать корректный lock-файл.

**ОЧЕНЬ ВАЖНО для существующего репозитория GitHub `sat7`:** при загрузке файлов поверх старых GitHub не удаляет старый `package-lock.json`. Его надо удалить вручную: открой файл `package-lock.json` в корне репозитория GitHub -> меню `...` -> Delete file -> Commit changes. В новом архиве этого файла нет. Альтернатива на своём компьютере: `npm install`, затем загрузить новый `package-lock.json` вместе с `package.json`. После этого `npm ci` снова станет работать.

## 2. Создание базы D1 — обязательно для Worker API

В Cloudflare открой Storage & databases > D1 > Create database. Название базы: `sat-platform-db`. Скопируй настоящий Database ID в `wrangler.toml` вместо `REPLACE_WITH_YOUR_D1_DATABASE_ID`. Не публикуй проект, пока не изменишь ID.

Для первоначальной настройки таблиц (в папке проекта в терминале):

```bash
npm install
npx wrangler login
npx wrangler d1 execute sat-platform-db --remote --file=./schema.sql
```

## 3. Подключение GitHub к Cloudflare Workers

При настройке Worker используй: 
- Root directory: `/` (корень репозитория)
- Build command: оставить пустым, если не нужен отдельный build (сайт уже лежит в `public/`)
- Deploy command: `npx wrangler deploy`

Важно: наличие `wrangler.toml` с заменённым database ID и рабочей базы D1 — обязательные условия.

## 4. Проверка

После успешного деплоя открой `https://<worker-host>/api/health`: должен быть JSON с `ok: true` и `database: "d1"`. Затем проверь регистрацию, вход, тесты, сохранение прогресса и Premium. Если ответы `/api/*` не работают, сайт НЕ считается полноценно запущенным.

Данные из MongoDB автоматически не перенесены. Внешний источник SAT-вопросов требует реального тестирования на Cloudflare. Платежи в режиме manual не подтверждаются автоматически.
