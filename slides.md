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

---

<img class="h-90% mx-auto" src="/1.png" />

<!--
Чтобы обеспечить __стабильный__ рост проекта. В простом проекте без сложной логики можно обойтись без тестов. Чем сложнее логика, тем сложнее расширять и поддерживать проект без юнит тестов, растёт уровень беспорядка в системе. Происходит это по очень простой причине. Чем сложнее система, тем больше вероятность того, что изменение в ней приведёт к регрессии - когда старая функциональность выходит из строя, подвид бага.

Эту ситуацию в полной мере можно прочувствовать, например, на Order'е — исправление одной ошибки ведёт к появлению нескольких других. Вернуть проект в стабильное состояние становится сложнее с каждым изменением.

Процесс, при котором проект становится более "хрупким" можно назвать энтропией. С расширением системы возрастает уровень беспорядка в ней. Чем сильнее беспорядок, тем более вероятны регрессии.

Чистка кода и рефакторинг уменьшают беспорядок. Добавление нового функционала его увеличивает.

Тесты защищают от регрессий. Написали интеграционный тест на Ордере, чтобы быть уверенными, что всегда можно заказать такси - тест провалится, когда разработчик сломает эту функциональность, это сигнал о возможной регрессии.

Однако тесты требуют значительных начальных вложений времени, и окупаются они только в долгосрочной перспективе для сложных проектов.
-->

---

<img class="h-90% mx-auto" src="/2.png" />

<!--
Более того, *качество тестов* определяет то, насколько быстро проект будет замедляться в разработке. Идеальные тесты делают рост проекта линейным.

Но мы все понимаем.

Неэффективные тесты возымеют эффект, проект сможет прожить и развиться больше, чем без них, но в итоге всё равно может прийти полная стагнация из-за постоянных регрессий или ложных срабатываний этих тестов.
-->

---
layout: center
---

<h1 class="text-green-400 pb-4">Что нужно тестировать?</h1>

<ul v-click="1">
  <li><span v-mark.highlight.red="6">Бизнес логика</span></li>
  <li v-click="2">Инфраструктурный код</li>
  <li v-click="3">Связующий код</li>
  <li v-click="4">Внешние зависимости</li>
  <li v-click="5">Отображение на фронте</li>
</ul

<!-- step 1 -->
<!--
Не всё нужно тестировать. Тестовый код, как и любой код, требует поддержки. Провели чистку - надо поправить тесты. Поменяли бизнес-логику - опять тесты, добавили новую фичу - сломались тесты. Поэтому не весь код заслуживает, чтобы его тестировали. Покрытие в 100% не гарантирует 100% защиту от регрессий, это титанический труд. тестировать нужно наиболее важные части проекта.

Сломается форма заказа, и пользователи не смогут приносить бизнесу деньги. Важно? Важно.

В одном диалоговом окне за десятью кликами, пятью страницами в закутке, куда никто не заходит, в инпуте текст перестанет быть выровнен по центру. Важно?

Это два крайних случая, на самом деле, конечно, *"важность"* кода это, скорее, градиент. Что-то важнее, что-то можно удалить, и никто не заметит.

Тогда какой код *"важный"*? Давайте разделим код на условные "типы":

- [click] Бизнес логика. Часто связана с деньгами, и поэтому, ну, наверное, это важно. Даже если не приносит деньги напрямую, код решает проблему пользователя, а значит этому функционалу не стоит ломаться.

- [click] Инфраструктурный код. Настройки, конфиги, логирование. Стоит ли это тестировать? Некоторые логи важны, например, аналитика. Если она перестанет приходить, может нарушиться работа множества людей в компании.

- [click] Связующий код. Контроллеры, фасады, адаптеры. Важно? Может и важно. Почему бы и не написать тест на метод, который используется в 20 местах. Нарушение его работы может привести к багам по всему проекту.

- [click] Внешние зависимости. Язык, фреймворк, библиотеки, файловая система, база данных, API. Вы можете поспорить, что они и так должны работать, и проверять их работу не надо. Может быть. А может быть обновление с одной версии ноды на другую сломает к херам ваш код по работе с файловой системой или запросами. Кто знает.

- [click] Отображение. Применимо только к фронту. Обычно код для отображения примитивен. Наверное, не стоит проверять, если отображается параграф текста, статически заданный в вёрстке компонента. Наверное. Но ведь наши интеграционные тесты в том числе проверяют вёрстку. Кликают, считывают текст со страницы. Даже для юнит тестов может найтись место. Если отображение предполагает сложные алгоритмы и работу с DOM элементами, форматирование и преобразование текста.

Напоминаю, что 100% кода - не вариант. Это слишком времязатратно и не принесёт достаточной пользы. Код во всех этих категориях может быть важен. Но не равноценно. [click] Как ни крути, бизнес логика всегда должна стоять на первом месте. Мы в первую очередь должны убедиться, что задача пользователя решена, что он не заплатит лишний раз за второе вызванное такси из-за какого-нибудь бага и не купит в магазине банку шпрот по цене ста банок, потому что мы добавили копейки к рублям как строку, без точки.
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
Ну окей, давайте тестировать бизнес логику. Помним, что паршиво написанные тесты *помогают*... но мы стремимся к идеалу.

Давайте разбираться. Начнём с начала.

Что такое юнит тест? (ЧИТАЙ СОС ЛАЙДА)

Конкретнее.

Юнит — единица поведения, что-то, что можно описать простым языком не-программисту. Единица поведения не зависит от кода, имплементации. Она может покрывать метод-однострочник или класс целиком, а может даже несколько классов.

Изолирован — означает, что "реальным" во время теста будет только тестируемый функционал. Внешние зависимости: базы данных, API, используемые или инъектируемые классы - заменяются заглушками. Константы типа строк, enum'ов заменять не нужно, но можно, если это полезно для теста, например, конфиги или переменные окружения.

Быстро — если вас устраивает скорость, то тест работает быстро.
-->

---
layout: center
class: p-60
---

Если падает тест, то можно точно понять, в каком классе и какая функциональность сломалась

<br>

Тестами легко покрыть сложный граф зависимостей, так как все зависимости мокируются

<!--
Из этого вытекает, что:
-->

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
Кроме юнит тестов есть интеграционные. Интеграционным можно считать тест, который нарушает какой-либо из признаков юнит-теста.

- Проверяем несколько единиц поведения - интеграционный тест
- Не используем моки - интеграционный тест
- Работает медленно - тоже интеграционный тест

Последний фактор, скорее, субъективен. Однако если мы для теста поднимаем весь проект, полностью, и для кадого теста полностью до нуля чистим состояние, вычищаем приложение из памяти и загружаем заново, то это будет *долго*.

Не все интеграционные тесты проверяют несколько единиц поведения. Однако все интеграционные тесты проверяют, что разные компоненты системы слаженно работают между собой. В следствие этого, интеграционные тесты одним своим прогоном часто покрывают огромное количество кода, внешние зависимости типа фреймворка и библиотек.
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
Сквозной или end-to-end тест - это ещё более медленный интеграционный тест. Сквозные тесты могут работать с внешними зависимостями, типа API и баз данных. Они проверяют проект с точки зрения конечного пользователя.

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

<h1 class="text-green-400">Секция действий</h1>

````md magic-move {lines:true}
```ts {*|8-9|*}
it('Stores data in localStorage', () => {
  // Arrange
  const key = StorageKey.TOKEN
  const data = 'data'
  const storageMock = mockLocalStorage()
  service = TestBed.inject(StorageService)

  // Act
  service.set(key, data)

  // Assert
  expect(storageMock.setItem).toHaveBeenCalledTimes(1)
  expect(storageMock.setItem).toHaveBeenCalledWith(key, data)
})
```

```ts
test(TestDescription.MOBILE_TRIPS_EDIT, async () => {
  // ...

  // act
  await mainPage.navigateToTrips();

  await tripPageObject.clickEdit();

  await orderForm.setAddressInFullMapDialog(
    AddressItemType.FROM_ITEM,
    addresses[0].title!
  );

  await orderForm.openAddressToList();
  await orderForm.setAddressInFullMapDialog(
    AddressItemType.TO_ITEM,
    addresses[1].title!,
    0
  );

  // ...
});
```

```ts {*|8-10|*}
it('Purchase succeeds if enough balance', () => {
  // Arrange
  balanceStoreMock.getBalance.mockReturnValue(999999)
  const item = makeMockItem({ id: 123, price: 10 })
  shopStoreMock.getItem.mockReturnValue(item)
  service = TestBed.inject(ShopService)

  // Act
  service.purchaseItem(123)
  service.removeFromBalance(10)

  // Assert
  expect(balanceStoreMock.remove).toHaveBeenCalledWith(10)
  expect(shopStoreMock.purchaseItem).toHaveBeenCalledWith(item)
})
```

```ts {*|8-9|*}
it('Purchase succeeds if enough balance', () => {
  // Arrange
  balanceStoreMock.getBalance.mockReturnValue(999999)
  const item = makeMockItem({ id: 123, price: 10 })
  shopStoreMock.getItem.mockReturnValue(item)
  service = TestBed.inject(ShopService)

  // Act
  service.purchaseItem(123)

  // Assert
  expect(shopStoreMock.getItem).toHaveBeenCalledWith(123)
  expect(balanceStoreMock.remove).toHaveBeenCalledWith(10)
  expect(shopStoreMock.purchaseItem).toHaveBeenCalledWith(item)
})
```
````
<!--
Хорошо, с секцией подготовки разобрались. О действиях. [click:1] Пишите одно действие. Не ошибётесь.

[click:3] В интеграционных тестах так, чаще всего, не получится, по крайней мере на фронте. Нам нужно прокликать кучу разных кнопок, вьюшек и всё такое, и это нормально. На бэке, наверное, скорее нет. У вас будет что-то вроде точки входа, типа запроса на ваш сервис. Вы бы его иммитировали и проверяли, что возвращает система.

[click:4] Если же вам нужно сделать два действия в юнит тесте, то вы что-то сделали не так. [click:5] Когда класс требует вызова двух методов для выполнения одного действия, то публичный API этого класса паршиво спроектирован. Класс не должен заставлять разработчика это помнить. [click:5] Решить проблему очень просто. [click:6] Уберите второй метод в первый. Это тот самый случай, когда по юнит тесту видно, что код написан плохо.
-->


---
layout: center
---

<h1 class="text-green-400">Секция проверок</h1>

````md magic-move {lines:true}
```ts {*|2-3,11-12|4-5,13|*}
it('Purchase succeeds if enough balance', () => {
  // Arrange
  balanceStoreMock.getBalance.mockReturnValue(999999)
  const item = makeMockItem({ id: 123, price: 10 })
  shopStoreMock.getItem.mockReturnValue(item)
  service = TestBed.inject(ShopService)

  // Act
  service.purchaseItem(123)

  // Assert
  expect(balanceStoreMock.getBalance).toHaveBeenCalled()
  expect(shopStoreMock.getItem).toHaveBeenCalledWith(123)
  expect(balanceStoreMock.remove).toHaveBeenCalledWith(10)
  expect(shopStoreMock.purchaseItem).toHaveBeenCalledWith(item)
})
```

```ts
it('Purchase succeeds if enough balance', () => {
  // Arrange
  balanceStoreMock.getBalance.mockReturnValue(999999)
  const item = makeMockItem({ id: 123, price: 10 })
  shopStoreMock.getItem.mockReturnValue(item)
  service = TestBed.inject(ShopService)

  // Act
  service.purchaseItem(123)

  // Assert
  expect(shopStoreMock.getItem).toHaveBeenCalledWith(123)
  expect(balanceStoreMock.remove).toHaveBeenCalledWith(10)
  expect(shopStoreMock.purchaseItem).toHaveBeenCalledWith(item)
})
```

```ts
it('method is correct', () => {
  expect(fetch.toString()).toBe(
    'function fetch(input, init = undefined) {\n' +
    '    if (!fetchImpl) {\n' +
    "      const undiciModule = require('internal/deps/undici/undici');\n" +
    '      fetchImpl = undiciModule.fetch;\n' +
    '    }\n' +
    '    return fetchImpl(input, init);\n' +
    '  }'
  )
})
```
````

<!--
В секции проверок просто нужно протестировать результат работы метода и его сайд эффекты. [click:1] Не проверяйте всё подряд. Вам должно быть безразлично, откуда метод берёт данные для своих рассчётов и какие зависимости он использует для своих задач. Вот так делать плохо. Заглушки, которые предоставляют исходные данные для тестируемой системы, называются стабами. `getBalance` это стаб. [click:2] С `getItem` не всё так просто, это, как бы, стаб, но требует аргумент для верной работы. [click:4] Поэтому, првоерку `getBalance` убираем, а в `getItem` оставляем только проверку на аргумент id.

[click:5] Проверять порядок вызова зависимостей, моков, геттеров, что используется и как - это плохая идея, потому что тестируемая система должна быть чёрным ящиков для вас. Забудьте о её имплементации. Тестируйте поведение. Хороший тест проверяет результат, а не то, какие шаги выполняет система. С таким же успехом можно проверять, что код метода посимвольно равен правильной имплементации. Поймает ли такой тест ошибку? Абсолютно точно да. Однако вместе с этим он будет выдавать ложную тревогу на каждый чих рядом с этим методом. Чуть позже я ещё расскажу о том, что делает тест эффективным, но могу лишь сказать, что эффективность такого теста равна нулю, и правильным решением будет его удалить.
-->

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

<!--
Хороший тест обладает четырьмя атрибутами:

- Защищает от багов
- Устойчив к рефакторингу
- Быстро даёт обратную связь
- Прост в поддержке

-->

---
layout: center
---

<h1 class="text-green-400">Публичный API ≠ Поведение системы</h1>

<!--
Я слышал часто от Андрея, что мы тестируем публичный API системы. Можно сказать, что публичный API это и есть наблюдаемое поведение, но это не так.
-->

---
layout: center
---

````md magic-move {lines:true}
```ts {*}
class UserService {
  public userStore = inject(UserStore)
  public name: string;

  public normalizeName(name: string) {
    return name.trim().slice(0, 64)
  }

  public renameUser(id: number, newName: string) {
    const user = this.userStore.getUser(id)
    this.userStore.updateUser({ ...user, name: newName })
  }
}
```

```ts {*}
class UserService {
  userStore = inject(UserStore)
  name: string;

  normalizeName(name: string) {
    return name.trim().slice(0, 64)
  }

  renameUser(id: number, newName: string) {
    const user = this.userStore.getUser(id)
    this.userStore.updateUser({ ...user, name: newName })
  }
}
```

```ts {*}
class UserService {
  userStore = inject(UserStore)
  name: string;

  normalizeName(name: string) {
    return name.trim().slice(0, 64)
  }

  renameUser(id: number, newName: string) {
    const user = this.userStore.getUser(id)
    this.userStore.updateUser({ ...user, name: newName })
  }
}

class MyComponent {
  userService = inject(UserService)

  onRenameClick(id: number, newName: string) {
    const normalizedName = this.userService.normalizeName(newName)
    this.userService.renameUser(id, normalizedName)
  }
}
```

```ts {*}
class UserService {
  userStore = inject(UserStore)
  name: string;

  normalizeName(name: string) {
    return name.trim().slice(0, 64)
  }

  renameUser(id: number, newName: string) {
    const user = this.userStore.getUser(id)
    this.userStore.updateUser({ ...user, name: newName })
  }
}

class MyComponent {
  userService = inject(UserService)

  onRenameClick(id: number, newName: string) {
    this.userService.name = newName
  }
}
```

```ts {*}
class UserService {
  private userStore = inject(UserStore)
  private name: string;

  normalizeName(name: string) {
    return name.trim().slice(0, 64)
  }

  renameUser(id: number, newName: string) {
    const user = this.userStore.getUser(id)
    this.userStore.updateUser({ ...user, name: newName })
  }
}

class MyComponent {
  userService = inject(UserService)

  onRenameClick(id: number, newName: string) {
    const normalizedName = this.userService.normalizeName(newName)
    this.userService.renameUser(id, normalizedName)
  }
}
```

```ts {*}
class UserService {
  private userStore = inject(UserStore)
  private name: string;

  normalizeName(name: string) {
    return name.trim().slice(0, 64)
  }

  renameUser(id: number, newName: string) {
    const user = this.userStore.getUser(id)
    this.userStore.updateUser({ ...user, name: newName })
  }
}

class MyComponent {
  userService = inject(UserService)

  onRenameClick(id: number, newName: string) {
    this.userService.renameUser(id, newName)
  }
}
```

```ts {*}
class UserService {
  private userStore = inject(UserStore)
  private name: string;

  private normalizeName(name: string) {
    return name.trim().slice(0, 64)
  }

  renameUser(id: number, newName: string) {
    const user = this.userStore.getUser(id)
    const normalizedName = this.normalizeName(newName)
    this.userStore.updateUser({ ...user, name: normalizedName })
  }
}

class MyComponent {
  userService = inject(UserService)

  onRenameClick(id: number, newName: string) {
    this.userService.renameUser(id, newName)
  }
}
```
````

<!--
it('renames user', () => {
  service = TestBed.inject(UserService)
  const userMock = makeUser({ id: 1 })
  userStoreMock.getUser.mockReturnValue(userMock)

  service.renameUser(1, normalizedName)

  expect(userStoreMock.upadteUser).toHaveBeenCalledWith(
    expect.objectContaining({ name: 'new name' })
  )
})
-->

<!--
Это два разных способа классифицировать код. Публичный или приватный интерфейс относится к коду. Мы как программисты ручками пишем `public` и `private`. Наблюдаемое поведение и детали имплементации это другое, их мы проектируем.

[click:1] В TypeScript'е все поля по умолчанию public, поэтому я их уберу для простоты.

Скажем, у нас есть этот класс, и мы знаем, что имя пользователя должно быть не больше 64 символов. Имеем два метода, переименовать пользователя и нормализовать имя.

[click:2] Используем класс следующим образом. Нормализуем имя, пихаем в `renameUser`. С этой имплементацией есть две проблемы.

[click:3] Первая в том, что мы можем сделать вот так. Это несовпадение публичного API с поведением.

[click:4] Впрочем, опытный разработчик такого не допустит, и пометит приватные поля.

[click:5] Это всё равно не защищает нас от ошибки, когда мы вызываем метод `renameUser` без нормализации строки. Это тоже несовпадение публичного API с поведением.

Поведение системы может отличаться от публичного интерфейса.
-->

---
layout: center
---

<img class="mx-auto" src="/4.1.png" />

---
layout: center
---

<img class="mx-auto" src="/5.1.png" />
