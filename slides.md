---
theme: default
title: Unit тестирование
# https://sli.dev/features/drawing
drawings:
  persist: false
# slide transition: https://sli.dev/guide/animations.html#slide-transitions
transition: slide-up
# enable MDC Syntax: https://sli.dev/features/mdc
mdc: true
# open graph
# seoMeta:
#  ogImage: https://cover.sli.dev
layout: center
---

<h1 class="text-green-400">Зачем писать юнит тесты?</h1>

<!--
You can have `style` tag in markdown to override the style for the current page.
Learn more: https://sli.dev/features/slide-scope-style
-->

<!--
Чтобы обеспечить __стабильный__ рост проекта. В простом проекте без сложной бизнес логики можно обойтись без тестов. Чем сложнее логика, тем сложнее расширять и поддерживать проект без юнит тестов, растёт уровень беспорядка в системе.
-->

---

<img class="h-90% mx-auto" src="/1.png" />

<!--
Чистка кода и рефакторинг уменьшают беспорядок. Добавление нового функционала увеличивает. Высокий беспорядок можно прочувствовать на Order'е — исправление одной ошибки ведёт к появлению нескольких других, и вернуть проект в стабильное состояние становится всё труднее.

Хорошие тесты помогают не допустить этого, они защищают от регрессий и багов. Это что-то вроде контракта о том, как код должен работать.

Однако тесты требуют значительных начальных вложений времени, и окупаются они только в долгосрочной перспективе для сложных проектов.
-->

---

<img class="h-90% mx-auto" src="/2.png" />

---
layout: center
---

<h1 class="text-green-400 pb-4">Что нужно тестировать?</h1>

<ul v-click="1">
  <li><span v-mark.highlight.red="6">Бизнес логика</span></li>
  <li v-click="2">Инфраструктурный код</li>
  <li v-click="3">Внешние зависимости</li>
  <li v-click="4">Связующий код</li>
  <li v-click="5">Отображение на фронте</li>
</ul

<!-- step 1 -->
<!--
Не всё нужно тестировать. Тестовый код, как и любой код, требует поддержки. Отрефакторили класс, поменяли бизнес-логику, добавили фичу, появился flaky тест — придётся идти и исправлять. Поэтому не весь код заслуживает, чтобы его тестировали. Можно грубо разделить на такие части кода:

Тестировать нужно только важные части проекта. Что из этого важно? Может быть что угодно. Скорее всего, любая бизнес логика — это важно. Даже сервис, слагающий 2 и 2 может быть критической частью кода, если у него 20 потребителей.

Инфраструктурный код может быть важен, типа утилит, которые делают сложные вычисления, любые важные алгоритмы. В коде отображения может быть сложный форматтер, преобразующий тест из одного синтаксиса в другой, как в технограм боте из Markdown ютрека в Markdown технеги.

Как бы то ни было, сосредоточаться всегда и в первую очередь нужно на бизнес логике. Эти тесты обеспечат наибольшую защиту от багов, а значит наиболее эффективны.
-->

---
layout: center
---

<h1 class="text-green-400">Что такое юнит тест?</h1>

<div class="text-white pb-3">Это автоматизированный тест, который:</div>

- Проверяет правильность работы одной единицы поведения — юнита
- Изолирует тестируемый класс от его зависимостей
- Работает быстро

<!--
Юнит — единица поведения, что-то, что можно описать простым языком не-программисту. Такая единица поведения может покрывать метод-однострочник или класс целиком, а может даже несколько классов.

Изолирован — то есть "реален" только тестируемый функционал. Внешние зависимости заменяются заглушками: базы данных, API, используемые или инъектируемые классы. Константы типа строк, enum'ов и файлов заменять не нужно, но можно, если это полезно для теста.

Быстро — вас устраивает скорость прохода теста

Именно по этому все тестовые фреймворки дают возможность замокать всё, что есть в проекте.
-->

---
layout: center
---

<h1 class="text-green-400">Что такое юнит тест?</h1>

<!--
Из этого вытекает, что:

@todo
-->

- Тест проверяет только одну единицу бизнес-логики за раз
- Тестами легко покрыть сложный граф зависимостей, так как все зависимости мокируются
- Если падает тест, то можно точно понять, в каком классе и какая функциональность сломалась.

---
layout: center
---

<h1 class="text-green-400">Что такое интеграционный тест?</h1>

<div class="pb-3">
  Это тест, который не удовлетворяет хотя бы одному из критериев юнит теста.<br>Обычно, интеграционный тест:
</div>

- Проверяет правильность работы одной или нескольких единиц поведения
- Изолирован от других тестов
- Работает медленно

<!--
@todo
Интеграционные тесты проверяют, как взаимодействуют разные модули приложения на более высоком уровне.
-->

---
layout: center
---

<h1 class="text-green-400">Что такое сквозной end-to-end тест?</h1>

<div class="pb-3">это подмножество интеграционных тестов. Отличия от интеграционных:</div>

- Сквозные тесты не мокают практически никакие зависимости
- Проверяют работоспособность проекта с точки зрения конечного пользователя
- Проверяют пользовательские сценарии целиком

<!--
Сквозной тест - это ещё более медленный интеграционный тест. Сквозные тесты могут работать с внешними зависимостями, делать запросы в базы данных или на API. Они проверяют тестируемую систему с точки зрения конечного пользователя.

Граница между интеграционными и сквозными тестами не такая большая, как между интеграционными и юнит тестами.
-->

---
layout: center
---

<h1 class="text-green-400">AAAAAAAAAA</h1>

````md magic-move {lines:true}
// step 1
```ts {*|2-7|9-10|12-13|*} twoslash
it("Purchase succeeds when enough inventory", () => {
  // Arrange
  const apiServiceMock = {
    makePurchase: jest.fn(),
    getItemCount: jest.fn().mockReturnValue(15),
  }
  service = TestBed.inject(StoreService)

  // Act
  service.purchase(Product.SHAMPOO, 5)

  // Assert
  expect(apiServiceMock.makePurchase).toHaveBeenCalledTimes(1)
})
```

// step 2
```ts {*} twoslash
it("Purchase succeeds when enough inventory", () => {
  const apiServiceMock = {
    makePurchase: jest.fn(),
    getItemCount: jest.fn().mockReturnValue(15),
  }
  service = TestBed.inject(StoreService)

  service.purchase(Product.SHAMPOO, 5)

  expect(apiServiceMock.makePurchase).toHaveBeenCalledTimes(1)
})
```

// step 3
```ts {*|11-12,17-18|14-15,20-22|*} twoslash
it("Purchase succeeds when enough inventory", () => {
  // Arrange
  const apiServiceMock = {
    makePurchase: jest.fn(),
    getItemCount: jest.fn()
      .mockReturnValueOnce(15)
      .mockReturnValue(5),
  }
  service = TestBed.inject(StoreService)

  // Act
  service.purchase(Product.SHAMPOO, 5)

  // Assert
  expect(apiServiceMock.makePurchase).toHaveBeenCalledTimes(1)

  // Act
  service.purchase(Product.MACARONI, 15)

  // Assert
  expect(apiServiceMock.makePurchase).not.toHaveBeenCalled()
})
```

// step 4
```ts {*|14-19|*} twoslash
it("Purchase succeeds when enough inventory", () => {
  // Arrange
  const shampooInInventory = 15
  const shampooToPurchase = 5
  const apiServiceMock = {
    makePurchase: jest.fn(),
    getItemCount: jest.fn().mockReturnValue(shampooInInventory),
  }
  service = TestBed.inject(StoreService)

  // Act
  service.purchase(Product.SHAMPOO, shampooToPurchase)

  // Assert
  if (shampooInInventory < shampooToPurchase) {
    expect(apiServiceMock.makePurchase).toHaveBeenCalledTimes(1)
  } else {
    expect(apiServiceMock.makePurchase).not.toHaveBeenCalled()
  }
})
```

// step 5
```ts {*} twoslash
it("Purchase succeeds when enough inventory", () => {
  const apiServiceMock = {
    makePurchase: jest.fn(),
    getItemCount: jest.fn().mockReturnValue(15),
  }
  service = TestBed.inject(StoreService)

  service.purchase(Product.SHAMPOO, 5)

  expect(apiServiceMock.makePurchase).toHaveBeenCalledTimes(1)
})

it("Purchase fails when not enough inventory", () => {
  const apiServiceMock = {
    makePurchase: jest.fn(),
    getItemCount: jest.fn().mockReturnValue(5),
  }
  service = TestBed.inject(StoreService)

  service.purchase(Product.SHAMPOO, 15)

  expect(apiServiceMock.makePurchase).not.toHaveBeenCalled()
})
```

// step 6
```ts {*|2-6} twoslash
it("Purchase succeeds when enough inventory", () => {
  const apiServiceMock = {
    makePurchase: jest.fn(),
    getItemCount: jest.fn().mockReturnValue(15),
  }
  service = TestBed.inject(StoreService)

  service.purchase(Product.SHAMPOO, 5)

  expect(apiServiceMock.makePurchase).toHaveBeenCalledTimes(1)
})
```

// step 7
```ts {2} twoslash
it("Purchase succeeds when enough inventory", () => {
  const apiServiceMock = makeStoreWithShampoo(15)
  service = TestBed.inject(StoreService)

  service.purchase(Product.SHAMPOO, 5)

  expect(apiServiceMock.makePurchase).toHaveBeenCalledTimes(1)
})
```

// step 8
```ts {*|-15|17-|*} twoslash
const test = (inStore, toPurchase, shouldCall) => {
  // Arrange
  const apiServiceMock = makeStoreWithShampoo(inStore)
  service = TestBed.inject(StoreService)

  // Act
  service.purchase(Product.SHAMPOO, toPurchase)

  // Assert
  if (shouldCall) {
    expect(apiServiceMock.makePurchase).toHaveBeenCalledTimes(1)
  } else {
    expect(apiServiceMock.makePurchase)not.toHaveBeenCalled()
  }
}

it("Purchase succeeds when enough inventory", () => test(15, 5, true))

it("Purchase fails when not enough inventory", () => test(5, 15, false))
```

// step 8
```ts {*} twoslash
const test = (inStore, toPurchase, shouldCall) => { /* ... */ }

it("Purchase succeeds when enough inventory", () => test(15, 5, true))

it("Purchase fails when not enough inventory", () => test(5, 15, false))
```

// step 8
```ts {3-} twoslash
const test = (inStore, toPurchase, shouldCall) => { /* ... */ }

it("Purchase succeeds when enough inventory", () => test({
  shampooInStore: 15,
  shampooToPurchase: 5,
  shouldSucceed: true,
)})

it("Purchase fails when not enough inventory", () => test({
  shampooInStore: 5,
  shampooToPurchase: 15,
  shouldSucceed: false,
}))
```
````

<!--
Тесты принято делить на три секции. У нас в команде довольно давно распространён паттерн ААА - Arrange Act Assert. Бэкендеры знают о нём?

(ААА - это паттерн, при котором тесты разибваются на три секции - подготовка или arrange, действие или act и проверка - assert.)

Паттерн довольно полезный, так как сильно помогает с читаемостью теста. Ну и простой, это буквально три комментария. У меня даже сниппет есть для их вставки.

[click:5] Убирайте комментарии для небольших тестов. Это не мешает читаемости, так как секции легко выделяются с помощью пустых строк

[click:6] Вот так в юнит тестах делать нельзя. Помним, юнит тесты проверяют одну единицу поведения. [click:7] Когда у вас дублируются секции, это красный флаг, означающий, что вы проверяете больше, чем одну единицу поведения.

[click:9] Интеграционные тесты могут себе такое позволить. Иметь множество секций нежелательно, но можно. Стоит делать это тогда, когда после выполнения одного теста система готова к выполнению другого теста, **и когда тест происходит медленно**. Две связанные единицы поведения проверять не стоит. Два быстрых интеграционных теста объединять в один тоже не стоит.

[click:10] Ветвить в тестах тоже нельзя. [click:11]`if`'ы в тестах тоже означают, что вы проверяете слишком много всего, и вам нужно разбить тесты на разные.

[click:13] Вместо if'ов и дубликатов секций лучше напишите два разных теста. Юнит тесты происходят быстро, и вы не увеличите время прогона тестов значительно. Ну, если это не интеграционные тесты. И даже так, читаемость важна. Поэтому семь раз подумайте прежде чем склеить два теста в один.

[click:14] Подробнее про каждую из секций. [click:15] Arrange или подготовка обычно самая большая секция, особенно в интеграционных тестах.

[click:16] Используйте фабрики для тестовых данных и моков, упрощайте код в секции подготовки. Я знаю, что во фронте мы всё подряд выносим в фабрики, и это классно.

[click:17] Полностью тесты выносить в фабрики плохо. Я так делал, и это привело к нечитаемым тестам. Не надо так. [click:18] Сама фабрика теста часто будет включать в себя непонятные параметры, условия в секции проверки. Это делает ТОЛЬКО хуже. [click:19] Вызовы фабрики тоже непонятны.

[click:22] Частично ситуацию можно исправить, если назвать аргументы, но даже так читать это тяжело и не понятно, что вообще из себя представляет тест. Слишком много всего убрано.
-->

---
layout: center
---

<h1 v-click="3" class="text-green-400">Параметризованные тесты</h1>

````md magic-move {lines: true}
// step
```ts {*}
it('Delivery with a past date is invalid', () => {
  const delivery = makeDelivery({ date: makeRelativeDate(-1) })
  service = TestBed.inject(DeliveryService)

  const actual = service.isDeliveryValid({ date })

  expect(actual).toBeFalsy()
})
```

```ts {*}
it('Delivery with a past date is invalid', () => {
  const delivery = makeDelivery({ date: makeRelativeDate(-1) })
  service = TestBed.inject(DeliveryService)

  const actual = service.isDeliveryValid({ date })

  expect(actual).toBeFalsy()
})

it('Delivery for today is invalid', () => { /* ... */ })
it('Delivery for tomorrow is valid', () => { /* ... */ })
it('Delivery in 1 month is valid', () => { /* ... */ })
it('Delivery in 3 months is invalid', () => { /* ... */ })
```

```ts {*}
it('Delivery with a past date is invalid')
it('Delivery for today is invalid')
it('Delivery for tomorrow is valid')
it('Delivery in 1 month is valid')
it('Delivery in 3 months is invalid')
```

```ts {*}
[
  [-1, false],
  [0, false],
  [1, true],
  [28, true],
  [90, false],
].forEach(([daysFromNow, expected]) => {
  it('Validates delivery date', () => {
    const delivery = makeDelivery({ date: makeRelativeDate(daysFromNow) })
    service = TestBed.inject(DeliveryService)

    const actual = service.isDeliveryValid({ date })

    expect(actual).toBe(expected)
  })
})
```

```ts {*}
[
  { daysFromNow: -1, expected: false },
  { daysFromNow: 0, expected: false },
  { daysFromNow: 1, expected: true },
  { daysFromNow: 28, expected: true },
  { daysFromNow: 90, expected: false },
].forEach(({ daysFromNow, expected }) => {
  it('Validates delivery date', () => {
    const delivery = makeDelivery({ date: makeRelativeDate(daysFromNow) })
    service = TestBed.inject(DeliveryService)

    const actual = service.isDeliveryValid({ date })

    expect(actual).toBe(expected)
  })
})
```

```ts {*|2,13|*}
[1, 7, 28].forEach((daysFromNow) =>
  it('Delivery within month is valid', () => {
    const delivery = makeDelivery({ date: makeRelativeDate(daysFromNow) })
    service = TestBed.inject(DeliveryService)

    const actual = service.isDeliveryValid({ date })

    expect(actual).toBeTruthy()
  })
)

[-100, -1, 0, 31, 9999].forEach((daysFromNow) =>
  it('Delivery outside 1 month range is invalid', () => {
    const delivery = makeDelivery({ date: makeRelativeDate(daysFromNow) })
    service = TestBed.inject(DeliveryService)

    const actual = service.isDeliveryValid({ date })

    expect(actual).toBeFalsy()
  })
)
```
````

<!--
[click:1] Ну хорошо, а как тогда выкручиваться из ситуации, когда в тестах куча повторений? Помним, что тестовый код требует поддержки. При написании тестов наша задача написать как можно более баго-защищающие тесты с минимальной затратой на поддержку. Скажем, у нас есть вот такой тест.

[click:2] Далее нам нужно написать ещё несколько тестов, которые проверяют до боли похожий функционал, и требуют одних и тех же действий. Единственное отличие в них будет в константных значениях: -1 поменяем на 0, 1 или 999. [click:2] Разница минимальная, что видно по их названиям. [click:3] В таких случаях применяются параметризованные тесты. В любом фреймворке и языке можно сделать что-то похожее, и такие тесты отлично справляются с задачей "максимально защитить от багов с минимальной поддержкой".

Когда параметров много, читать становится сложно. [click:4] Для упрощения иногда можно добавить константам имена. [click:5] Конкретно в этом случае идеальным вариантом будет разбить тесты на два, для двух исходов метода. [click:6] Тогда и имена становятся более точными, понятными, чем просто "Понимает, что дата неправильная".
-->

<br>
<br>

---
layout: center
---

<h1 class="text-green-400">Как называть тесты?</h1>

---
layout: center
---

````md magic-move {lines:true}
```ts
it('[тестируемый метод] [сценарий] [ожидаемый результат]')

it('sum - sums two numbers - returns sum')

it('purchase - not enough inventory - should fail')

```

```ts
it('Should return false when tariff not have routeMeta and addressTo have 2 or more address')
```


```ts {*|1|3|*}
it('Should return false when tariff not have routeMeta and addressTo have 2 or more address')

it('Destination address is visible for a multi-destination order')
```

```ts {*|7|8|*}
class DeliveryService {
  apiService = inject(ApiService)

  purchase(item, quantity) { /* ... */ }
}

describe('DeliveryService', () => {
  it ('[purchase] Purchase succeeds when enough inventory')
})
```

```ts
it('Sum of two numbers')

it('Purchase succeeds when enough inventory')

it('Purchase fails when not enough inventory')

it('Delivery with a past date is invalid')
```
````

<!--
Тесты проверяют поведение. А значит называть их нужно, основываясь на поведении.

Паттерны типа `[метод] - [сценарий] - [результат]` были бы прекрасны, если бы мы тестировали единицы кода, а не поведения. Названия методов, классов. внутренних переменных или типов не важны. Мы проверяем поведение.

[click:1] Вот реальное название теста из ордера. Прочитайте его и ответьте мне на вопрос - что проверяет этот тест.

[click:2] Вот так проще? Разница между этими двумя названиями в двух вещах - грамматика и то, на чём они фокусируются.

[click:3] Первое жётско привязан к коду. "Должна вернуть false когда тарифф не имеет routeMeta и addressTo имеет два или более адреса" - половина этих слов была взята из самого метода.

[click:4] Второе описывает какое-то правило бизнес логики. "Адрес куда показывается для заказа на множество адресов."

[click:6] Насчёт включения названий классов и методов в названия. [click:7] В наших проектах мы везде указываем названия тестируемых классов. Да, мы тестируем не класс, а поведение, но класс выступает в данном случае как входная точка. Плюс, в некоторых раннерах это помогает понять, где сломались тесты. Но название класса должно быть в обёртке тестов, в случае с jest/jasmine/vitest - в блоке describe. [click:8] Название метода - это уже слишком далеко. Это усложняет рефакторинг, так как иногда приходится переписывать весь класс, двигать логику местами и всё такое. В такие моменты название метода вам только помешает. Основывайте ваши названия етстов на поведении, а не на коде.

[click:10] В итоге у вас получатся легко читаемые и понятные тесты, которые не страшно отрефакторить или изменить.
-->


---
layout: center
---

<h1 class="text-green-400">Как называть тесты?</h1>

- Не используйте шаблоны, сохраняйте свободу самовыражения
- Пишите так, будто объясняете не-программисту
- Описывайте логику, а не код
- Не указывайте название метода в тесте
