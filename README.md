# SellCount System — GitHub Pages

Готовая статическая версия POS-приложения с входом через **Google**.

## Авторизация (Google)

1. Firebase Console → **Authentication** → Sign-in method → включите **Google**.
2. Authentication → Settings → **Authorized domains** — добавьте:
   - `localhost`
   - ваш домен GitHub Pages (`username.github.io`)
3. Firestore → коллекция **`users`**

### Как назначаются роли

| Событие | Что происходит |
|---------|----------------|
| Первый вход через Google | Создаётся документ `users/{uid}` с `role: "seller"` |
| Сделать админом | В Firestore откройте `users/{uid}` и поставьте `role: "admin"` |

Пример документа пользователя:

```
users/{uid}
  email: "user@gmail.com"
  name: "Иван"
  role: "admin"     // или "seller"
  store: "Мой магазин"
```

Продавец **не видит** чистую прибыль и вкладку «Каталог».

## Firebase config

В `index.html` блок `firebaseConfig` — подставьте ключи своего проекта при необходимости.

Коллекции Firestore:

- `products` — товары
- `sales` — продажи
- `debtors` — должники
- `users` — роли пользователей (создаётся при первом входе)

## Публикация на GitHub Pages

1. Загрузите содержимое папки в репозиторий.
2. Settings → Pages → branch `main` / root.
3. Добавьте домен Pages в Authorized domains Firebase Auth.

## Правила Firestore (для старта)

```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /{document=**} {
      allow read, write: if request.auth != null;
    }
  }
}
```

Для продакшена ограничьте доступ по `request.auth.uid` и полям `role`.
