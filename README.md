# Кухня та бар — Система замовлень

Хакатон-демо · 18 травня

## Структура

```
/
├── index.html        # Лендінг з посиланнями на термінали
├── bar.html          # Термінал бару
├── restaurant.html   # Термінал ресторану
└── vercel.json       # Конфіг Vercel (cleanUrls)
```

## Перед деплоєм

В обох файлах `bar.html` і `restaurant.html` замінити плейсхолдер webhook URL на справжній від ліда n8n.

Шукати блок:
```js
const CONFIG = {
  venue: "bar",  // або "restaurant"
  webhookUrl: "https://[ДОМЕН-ВІД-ЛІДА]/webhook/smartorder-demo"
};
```

Замінити `webhookUrl` на реальний (типу `https://stakanov92.app.n8n.cloud/webhook/smartorder-demo`).

## Деплой на Vercel — 3 способи

### Спосіб 1: GitHub → Vercel (рекомендовано)

1. Створити репо на github.com → завантажити 4 файли
2. vercel.com → Add New → Project → Import репозиторій
3. Framework Preset: **Other**, Root Directory: `./`
4. Build/Output/Install — все вимкнути (тогли)
5. Deploy

Vercel автоматично передеплоюватиме при кожному push у `main`.

### Спосіб 2: Vercel CLI (швидко)

```bash
npm i -g vercel
cd kuhnya-bar
vercel
# Відповідати: Set up and deploy? Y → scope → link existing? N → name → directory ./
vercel --prod
```

### Спосіб 3: Drag & drop

Поки що не працює напряму, тільки через CLI або GitHub.

## URL після деплою

Після успішного деплою отримаєте URL виду `https://kuhnya-bar.vercel.app`:
- `/` — лендінг (index.html)
- `/bar` — термінал бару (cleanUrls вмикає це автоматично)
- `/restaurant` — термінал ресторану

## Тестування перед справжнім webhook

Поки лід не дав n8n URL — використати webhook.site:

1. Відкрити `webhook.site` → скопіювати свій URL
2. Тимчасово вставити в `CONFIG.webhookUrl` обох файлів
3. Закомітити, дочекатись передеплою
4. Відкрити `/bar`, ввести "2 пляшки джину Hendrick's"
5. На webhook.site побачити payload `{ venue: "bar", action: "new_order", text: "...", ... }`
6. Те саме для `/restaurant` — має бути `venue: "restaurant"`

Коли все ОК — повернути справжній webhook URL від ліда.

## Контракт payload (узгоджено з n8n)

```json
{
  "action": "new_order",
  "venue": "bar",
  "text": "2 пляшки джину",
  "timestamp": "2026-05-12T18:00:00.000Z"
}
```

Також є `action: "confirm"` (підтвердження замовлення) і `action: "add_more"` (додавання до існуючого).

## Acceptance criteria

- [ ] 3 сторінки задеплоєні на публічний Vercel URL
- [ ] `/` (index) має 2 робочі посилання
- [ ] `/bar`: "2 пляшки джину" → запит з `venue: "bar"`
- [ ] `/restaurant`: те саме з `venue: "restaurant"`
- [ ] Скрін успішного тесту на webhook.site
