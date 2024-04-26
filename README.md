# Тестове завдання на позицію Frontend-developer в компанії BillTech

Перед вами спрощена сторінка видачі авіаквитків з фільтром і сортуванням. Також є невеликий сервер для тестового завдання, який реалізує техніку long polling для передачі пачки квитків. Вам необхідно реалізувати клієнт, який буде отримувати випадково згенеровані квитки від сервера та відкривати інтерфейс згідно з макетом у Figma. Достатньо буде відрендерити 5 перших квитків відповідно до обраних фільтрів і сортувань.

## Макет (Figma)
https://github.com/vadimmarkov/billtech-frontend-test-tasks/blob/master/BillTech.fig

Figma

![](search_preview.png?raw=true)

## Умови

- Використовуйте Vue3 або Nuxt3
- Не використовуйте Axios та Moment.js
- Працездатність в актуальній версії Google Chrome
- Все інше на ваш розгляд

# Опис взаємодії із сервером

Схема роботи проста: спочатку необхідно ініціювати пошук на сервері та отримати ідентифікатор пошуку (`searchId`). Далі, з отриманим `searchId`, ви робите запити для отримання невідсортованих списків квитків. Зверніть увагу, що квитки прилітають пачками, які необхідно агрегувати, фільтрувати і сортувати згідно з обраними в інтерфейсі параметрами. Для ускладнення завдання, сервер може на один із запитів відповісти помилкою.

## Отримання `searchId`
Просто надішліть GET-запит на `https://avs-backend.vercel.app/search` і отримайте його.

Приклад:

Request: `https://avs-backend.vercel.app/search`

Response: `{"searchId":"4niyd"}`

## Отримання пачки квитків

Надсилайте GET-запити на `https://avs-backend.vercel.app/tickets` і передайте searchId отриманий із запиту вище GET-параметром.

Приклад:

Request: `https://avs-backend.vercel.app/tickets?searchId=4niyd`

Response: `{tickets: [], stop: false}`

## Обробка завершення пошуку

Пошук вважається завершеним, коли в черговій відповіді від сервера прийде значення `{stop: true}`.

Приклад:

Request: `https://avs-backend.vercel.app/tickets?searchId=4niyd`

Response: `{tickets: [], stop: true}`

## Структура квитка

У списку `tickets` лежатимуть квитки такої структури:

```typescript
interface Ticket {
  // Ціна в гривнях
  price: number
  // Код авіакомпанії (iata)
  carrier: string
  // Масив перельотів
  // У тестовому завданні це завжди пошук "туди-назад" значить складається з двох елементів
  segments: [
    {
      // Код міста (iata)
      origin: string
      // Код міста (iata)
      destination: string
      // Дата і час вильоту туди
      date: string
      // Масив кодів (iata) міст з пересадками
      stops: string[]
      // Загальний час перельоту в хвилинах
      duration: number
    },
    {
      // Код міста (iata)
      origin: string
      // Код міста (iata)
      destination: string
      // Дата і час вильоту назад
      date: string
      // Масив кодів (iata) міст з пересадками
      stops: string[]
      // Загальний час перельоту в хвилинах
      duration: number
    }
  ]
}
```

P.S.: Картинки авіакомпаній можете брати з цього CDN: `//pics.avs.io/99/36/{IATA_CODE_HERE}.png`