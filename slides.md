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
Чтобы обеспечить __стабильный__ рост проекта. В простом проекте без сложной логики можно обойтись без тестов. Чем сложнее логика, тем сложнее расширять и поддерживать проект, растёт уровень беспорядка в системе. Поэтому чем сложнее система, тем больше вероятность того, что изменение в ней приведёт к регрессии - когда старая функциональность выходит из строя, это подтип бага.

Эту ситуацию в полной мере можно прочувствовать на Order'е — исправление одной ошибки ведёт к появлению нескольких других. Вернуть проект в стабильное состояние становится сложнее с каждым изменением.

Процесс, при котором проект становится более "хрупким" можно назвать энтропией. С расширением системы возрастает уровень беспорядка в ней. Чем сильнее беспорядок, тем более вероятны регрессии.

Чистка кода и рефакторинг уменьшают беспорядок. Добавление нового функционала его увеличивает.

Тесты защищают от регрессий. Написали интеграционный тест на Ордере, чтобы быть уверенными, что всегда можно заказать такси - тест провалится, когда разработчик сломает эту функциональность, непрохождение теста это сигнал о возможной регрессии.

Однако тесты требуют значительных начальных вложений времени, и окупаются они только в долгосрочной перспективе для сложных проектов.
-->

---

<img class="h-90% mx-auto" src="/2.png" />

<!--
Более того, *качество тестов* определяет то, насколько быстро проект будет замедляться в разработке. Идеальные тесты делают рост проекта линейным.

Но мы все понимаем.

Неэффективные тесты возымеют эффект, проект сможет прожить и развиться больше, чем без них, но в итоге всё равно может прийти полная стагнация из-за постоянных регрессий или ложных срабатываний этих тестов.

На Ордере ведь у нас есть юнит тесты. Покрыто 40 или 50% всей кодовой базы. Но от регрессий это нас не спасает, потому что на ордере плохие тесты. Я, кстати, виноват в появлении многих хреновых тестов в проекте. Вот эта кривая "С плохими тестами" может стать прямее. Скорее всего, она никогда не станет прямой, но чем лучше, тем быстрее мы будем разрабатывать, потому что сейчас наш проект находится примерно вот здесь. (указать курсором)
-->

---
layout: center
---

<h1 class="text-green-400">Что такое юнит тест?</h1>

<div v-mark.red.box="3" v-click="1">
  <div class="text-white">Это автоматизированный тест, который:</div>

  <ul>
    <li>Проверяет правильность работы одной единицы поведения — юнита</li>
    <li>Изолирует тестируемый класс от его зависимостей</li>
    <li>Работает быстро</li>
  </ul>
</div>

<div class="pt-3" v-click="2">
  По классической школе:

  <ul>
    <li> Проверяет правильность работы одной единицы поведения — юнита</li>
    <li> Изолирует каждый тест друг от друга</li>
    <li> Работает быстро</li>
  </ul>
</div>

<!-- v-mark doesn't create a click for some reason wtf -->
<div v-click="3"></div>

<!--
Надо выпрямить этот график, в том числе для новых проектов. Начинаем с начала. Что вообще такое юнит тест?

Нет, это не вызов функции `it` в тестовом фреймворке.

[click] Это автоматизированный тест, который:

- Проверяет правильность работы одного юнита. Юнит — это единица поведения, что-то, что можно описать простым языком не-программисту. Единица поведения не зависит от кода, имплементации. Она может покрывать метод-однострочник или класс целиком, а может даже несколько классов.

- Изолирует тестируемый класс — означает, что "реальным" во время теста будет только тестируемая система. Внешние зависимости: базы данных, API, используемые или инъектируемые сервисы - заменяются заглушками. Константы типа строк, enum'ов заменять не нужно, но можно, если это полезно для теста, например, конфиги или переменные окружения.

- Быстро — если вас всё устраивает, то работает быстро.

[click] Также есть классическая школа тестирования. Разница в подходах в том, как определяется "изоляция". Для лондонской, как я и сказал, изолируется класс, остальное мокируем. В классической изолируются тесты между собой.

На фронте или, по крайней мере, для Angular, пишут по лондонской, которая выше. Я думаю, это связано с ограничениями тестовых фреймворков, ведь большинство раннеров работают в ноде, поэтому не получается заменять все зависимости, в ноде нельзя просто взять и запустить проект на ангуляре.

По классической, мокируется минимум зависимостей, только внепроцессные типа API, баз данных, файловой системы и сторонних сервисов пишут заглушки. Такой подход приводит к тестам, которые покрывают значительно больше кода, лучше защищают от багов, но и у них есть свои недостатки. Я слышал, что такой подход хорошо применим к бэку, особенно в Максиме, так как у нас постоянно делят функциональность на отдельные сервисы.

[click] Я буду говорить и показывать примеры, по большей части, по лондонской школе, которую используют во фронте, но я постарался сделать так, чтобы для бэка это тоже было полезно. Вы же пишете тесты, да? Так ведь?
-->

---
layout: center
class: p-60
---

Если падает тест, то можно точно понять, в каком классе и какая функциональность сломалась

<br>

Тестами легко покрыть сложный граф зависимостей, так как все зависимости мокируются

<!--
Итак, из пунктов выше о лондонской школе, вытекает, что:

- Если тест падает, то можно точно понять, в каком месте, каком классе и какой конкретный функционал сломался. Это потому, что мы тестируем только один класс за раз. Для классической школы это не обязательно, так как зависимости не мокируются, и ошибки в соседних классах приведут к провалам везде и всюду.

- Тестами легко покрыть сложный граф зависимостей, так как "реальным" остаётся только один класс. Всё остальное всегда будет работать так, как вы это определили моками. Но вообще у вас не должно быть сложного графа зависимостей. Если это так, то, как будто, вы накосячили с архитектурой. Классическая школа, где соседние классы не мокируются, укажет на эту проблему. Но, мы не можем использовать её на фронте, так что ндааа.
-->

---
layout: center
---

<h1 class="text-green-400">Что такое интеграционный тест?</h1>

<div class="pb-3">
  Это тест, который не удовлетворяет хотя бы одному из критериев юнит теста.<br>Обычно, интеграционный тест:
</div>

- Проверяет правильность работы одной или нескольких единиц поведения
- Не мокирует всё подряд
- Работает медленно

<!--
Кроме юнит тестов есть интеграционные. Интеграционным можно считать тест, который нарушает какой-либо из признаков юнит-теста.

- Проверяем несколько единиц поведения
- Изолирован от других тестов, но не мокирует всё вокруг одного класса. Он ИНТЕГРАЦИОННЫЙ, проверяет интеграцию разных частей программы.
- Работает медленно

Интеграционные тесты часто будут нарушать 2 или 3 пункта. Наирмер, тесты на playwright проверяют внешние зависимости типа Angular и библиотек, проверяют множество категорий кода от бизнес логики до отображения, даже конфиги, так как запускается проект.

Не все интеграционные тесты проверяют несколько единиц поведения. Однако все интеграционные тесты проверяют, что разные компоненты системы слаженно работают между собой, и всегда покрывают огромное количество кода по сравнению с юнит тестами.
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
Вы также могли слышать про сквозные или end-to-end тесты. Интеграционные и сквозные тесты - не одно и то же, хотя у нас в команде этого разделения, обычно, не делают. С другой стороны, граница между ними не такая большая.

Сквозной или end-to-end тест - это ещё более медленный интеграционный тест. Сквозные тесты могут работать с внешними зависимостями, типа API и баз данных. Они проверяют проект с точки зрения конечного пользователя.
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
Не всё нужно тестировать. Тестовый код, как и любой код, требует поддержки. Провели чистку - надо поправить тесты. Поменяли бизнес-логику - поменяли тесты, поменяли вёрстку - сломались тесты. Поэтому не весь код заслуживает, чтобы его тестировали. Покрытие в 100% не гарантирует 100% защиту от регрессий, более того, это титанический труд, особенно в больших кодовых базах. Тестировать нужно наиболее важные части проекта.

А что можно считать... ***важным***?

Сломается форма заказа, и пользователи не смогут приносить бизнесу деньги. Важно? Важно.

В диалоговом окне за десятью кликами, куда никто не заходит, в инпуте текст перестанет быть выровнен по центру. Важно?

Да, это крайности. На самом деле, конечно, *"важность"* кода это, скорее, градиент. Что-то важнее, что-то не очень.

Давайте разделим код на условные "типы", чтобы было нагляднее:

- [click] Бизнес логика. Она либо приносит деньги, либо решает проблему пользователя, а значит этому функционалу нельзя ломаться.

- [click] Инфраструктурный код. Настройки, конфиги, логирование. Стоит ли это тестировать? Некоторые логи важны, например, аналитика. Если она перестанет приходить, может нарушиться работа множества людей в компании. Однако аналитики могут подождать, а клиент ждать, пока мы пофиксим критический баг, не будет.

- [click] Связующий код. Контроллеры, фасады, адаптеры. Важно? Может и важно. Почему бы и не написать тест на метод, который используется в 20 местах. Нарушение его работы может привести к ошибкам по всему проекту. Геттеры, сеттеры, методы-однострочники, которые просто пробрасывают данные дальше, наверное, тестировать не нужно.

- [click] Внешние зависимости. Язык, фреймворк, библиотеки, файловая система, база данных, API. Вы можете поспорить, что они и так должны работать, и проверять их работу не надо. Может быть. А может быть обновление версии Ангуляра приведёт к тому, что перестанет работать смена языка, как это произошло на Бурлаке.

- [click] Отображение. Применимо только к фронту. Обычно код для отображения примитивен. Наверное, не стоит проверять, если отображается параграф текста, статически заданный в вёрстке компонента. Наверное. С другой стороны, именно вёрстка это то, с чем взаимодействует пользователь в первую очередь.

Напоминаю, что покрывать 100% кода - не вариант. Это слишком времязатратно и не принесёт достаточной пользы. Код во всех этих категориях может быть важен. Но не равноценно. [click] Как ни крути, бизнес логика всегда должна стоять на первом месте. Мы в первую очередь должны убедиться, что задача пользователя решена, что он не заплатит лишний раз за второе вызванное такси из-за какого-нибудь бага или не купит в каком-нибудь онлайн магазине банку шпрот по цене ста банок, потому что мы добавили копейки к рублям как строку, без точки.
-->

---
layout: center
---

<h1 class="text-green-400">AAAAAAAAAA</h1>

````md magic-move {lines:true}
// step 1
```ts {*|2-7|9-10|12-13|*} twoslash
it('Purchase succeeds when enough inventory', () => {
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
it('Purchase succeeds when enough inventory', () => {
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
```ts {*|11-|*} twoslash
it('Purchase succeeds when enough inventory', () => {
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
it('Purchase succeeds when enough inventory', () => {
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
it('Purchase succeeds when enough inventory', () => {
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
```ts {*|2-7} twoslash
it('Purchase succeeds when enough inventory', () => {
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

// step 7
```ts {3} twoslash
it('Purchase succeeds when enough inventory', () => {
  // Arrange
  const apiServiceMock = makeStoreWithShampoo(15)
  service = TestBed.inject(StoreService)

  // Act
  service.purchase(Product.SHAMPOO, 5)

  // Assert
  expect(apiServiceMock.makePurchase).toHaveBeenCalledTimes(1)
})
```

// step 8
```ts {*|1,9-14|17-|*} twoslash
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

it('Purchase succeeds when enough inventory', () => test(15, 5, true))

it('Purchase fails when not enough inventory', () => test(5, 15, false))
```

// step 8
```ts {*} twoslash
const test = (inStore, toPurchase, shouldCall) => { /* ... */ }

it('Purchase succeeds when enough inventory', () => test(15, 5, true))

it('Purchase fails when not enough inventory', () => test(5, 15, false))
```

// step 8
```ts {*|3-} twoslash
const test = ({ inStore, toPurchase, shouldCall }) => { /* ... */ }

it('Purchase succeeds when enough inventory', () => test({
  shampooInStore: 15,
  shampooToPurchase: 5,
  shouldSucceed: true,
)})

it('Purchase fails when not enough inventory', () => test({
  shampooInStore: 5,
  shampooToPurchase: 15,
  shouldSucceed: false,
}))
```
````

<!--
Поговорим об анатомии тестов. Принято делить внутренности на три секции. У нас в команде довольно давно распространён паттерн ААА - Arrange Act Assert. Бэкендеры в курсе, что это такое?

(ААА - это паттерн, при котором тесты разибваются на три секции - [click] подготовка или arrange, [click] действие или act и [click] проверка или assert.)

[click] Паттерн довольно полезный, так как сильно помогает с читаемостью теста. Ну и простой, это буквально три комментария. У меня даже сниппет есть для их вставки.

[click] Убирайте комментарии для небольших тестов. Это не мешает читаемости, так как секции легко выделяются с помощью пустых строк

[click] Вот так в юнит тестах делать нельзя, каждая секция встречается только один раз. Помним, юнит тесты проверяют одну единицу поведения. [click] Когда у вас дублируются секции, это красный флаг, означающий, что вы проверяете больше, чем одну единицу поведения.

Интеграционные тесты могут себе такое позволить. Иметь множество секций нежелательно, но можно. Стоит делать это тогда, когда после выполнения одного теста система готова к выполнению другого теста, **и когда тест происходит медленно**. Две несвязанные единицы поведения объединять не стоит. Два быстрых интеграционных теста объединять в один тоже не стоит, просто напишите два.

[click:2] Ветвить в тестах тоже нельзя. [click] `if`'ы в тестах тоже означают, что вы проверяете слишком много всего, и вам нужно разбить тесты на разные.

[click:2] Вместо if'ов и дубликатов секций лучше напишите два разных теста. Юнит тесты происходят быстро, время прогона при этом увеличится ну на пару милисекунд. Если это не интеграционные тесты. И даже так, читаемость важна. Поэтому семь раз подумайте, один раз склейте два теста в один.

[click] Подробнее про каждую из секций. [click] Arrange или подготовка обычно самая большая секция, особенно в интеграционных тестах.

[click] Используйте фабрики для тестовых данных и моков, упрощайте код в секции подготовки. Я знаю, что во фронте мы всё подряд выносим в фабрики, и это классно.

[click] Полностью тесты выносить в фабрики плохо. Я так делал, и это привело к нечитаемым тестам. Не надо так. [click] Сама фабрика теста часто будет включать в себя кучу параметров, условия в секции проверок. Это делает ТОЛЬКО хуже. [click] Вызовы фабрики тоже непонятны.

[click:4] Частично ситуацию можно исправить, если назвать аргументы, но даже так читать это тяжело и не понятно, что вообще из себя представляет тест. Слишком много всего убрано, и приходится бегать глазами по всему файлу.
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

```ts {*|-10|12-|1-2,12-13|1-2|12-13|*}
[1, 7, 28].forEach((daysFromNow) =>
  it('Delivery within month is valid', () => {
    const delivery = makeDelivery({ date: makeRelativeDate(daysFromNow) })
    service = TestBed.inject(DeliveryService)

    const actual = service.isDeliveryValid({ date })

    expect(actual).toBeTruthy()
  })
)

[-100, -1, 0, 29, 9999].forEach((daysFromNow) =>
  it('Delivery outside 1 month range is invalid', () => {
    const delivery = makeDelivery({ date: makeRelativeDate(daysFromNow) })
    service = TestBed.inject(DeliveryService)

    const actual = service.isDeliveryValid({ date })

    expect(actual).toBeFalsy()
  })
)
```
````

<br>
<br>

<!--
Ну хорошо, а как тогда выкручиваться из ситуации, когда в тестах куча повторений? Помним, что тестовый код требует поддержки. При написании тестов наша задача написать как можно более баго-защищающие тесты с минимальной затратой на содержание. Скажем, у нас есть вот такой тест. Проверяем, что дата доставки в прошлое некорректна.

[click] Далее нам нужно написать ещё несколько тестов, которые проверяют ту же самую функциональность, и требуют одних и тех же действий. Единственное отличие в них будет в константных значениях: -1 поменяем на 0, 1 или 999. [click] Даже по названиям видно: доставка на вчера, сегодня, завтра, через месяц и три месяца. [click] В таких случаях применяются параметризованные тесты. В любом фреймворке и языке можно сделать что-то похожее, и такие тесты отлично справляются с задачей "максимально защитить от багов с минимальной поддержкой", потому что такие тесты проверяют набор кейсов, единиц поведения, при этом написать их нужно всего один раз. Правда, читать их посложнее. Например, когда много параметров.

[click] Для читаемости можно добавить константам имена. [click] Конкретно в этом случае идеальным вариантом будет разбить тесты на два, для двух исходов метода: [click] положительного и [click] отрицательного. [click] Тогда и имена становятся более точными и понятными. Вместо абстрактного "Проверяет дату" видно, какие даты [click] правильные, а какие [click] неправильные
-->

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

```ts {*|4-5|7|9-12|14-19|*}
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
C секцией подготовки разобрались, теперь о действиях. [click] Пишите одно действие. Не ошибётесь.

[click:2] В интеграционных тестах так, чаще всего, не получится, по крайней мере на фронте. [click] Нам нужно прокликать кучу разных кнопок, вьюшек и всё такое, и это нормально. На бэке, наверное, скорее нет. У вас будет что-то вроде точки входа, типа запроса на ваш сервис. Вы бы его иммитировали и проверяли, что возвращает система.

[click:5] Если же вам нужно сделать два действия в юнит тесте, то вы что-то сделали не так. [click] Когда класс требует вызова двух методов для выполнения одного действия, то публичный API этого класса паршиво спроектирован. Такой код заставляет разработчика держать в голове детали имплементации. [click] Решить проблему очень просто. [click] Уберите второй метод в первый. [click] Это тот самый случай, когда по юнит тесту видно, что код написан плохо.
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
````

<!--
В последней секции проверок нужно... что? Правильно, ***проверить*** результат работы метода и его сайд эффекты.

(Бегло прочитай тест)

[click] Не проверяйте всё подряд. Вам должно быть безразлично, откуда метод берёт данные для своих рассчётов и какие зависимости он использует для своих задач. Вот так делать плохо. `getBalance` это "стаб", заглушка, которая просто предоставляет данные для тестируемой системы. Не проверяйте, если он был вызван, не проверяйте порядок его вызова. Этот вызов не меняет состояние приложения, это не сайд эффект, не.. *результат* работы метода. Проверяйте только РЕЗУЛЬТАТ. [click] С `getItem` не всё так просто, это, как бы, "стаб", то есть предоставляет данные для системы, но требует аргумент для верной работы. [click:2] Поэтому, првоерку `getBalance` убираем, а в `getItem` оставляем только проверку на аргумент id.
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

Существуют шаблоны для названий типа `[метод] - [сценарий] - [результат]`. Они были бы прекрасны... если бы мы тестировали единицы кода, а не поведения. Добавлять в названия тестов имена методов, переменных, значения или типы - не надо. Тестируемая система для вас - чёрный ящик. Достаньте устройство для стирания памяти из людей в чёрном и забудьте, что написано в коде.

[click] Вот реальное название теста из ордера. Прочитайте его и ответьте мне на вопрос - что проверяет этот тест? Автор этого теста пусть молчит и не подсказывает.

[click] Вот так проще? Разница между этими двумя названиями в двух вещах - грамматика и то, на чём они фокусируются.

[click] Первое жётско привязан к коду. Перевод примерно такой: "Должна вернуть false когда тариф не имеет routeMeta и addressTo имеет два или более адреса" - половина этих слов была взята из самого метода, то есть в название проникли детали имплементации. Завтра мы переименуем `routeMeta` в `routeInfo`, а `addressTo` в `destinationAddress`, и тест... не сломается, но перестанет делать хоть какой-то смысл, ведь этих имён больше просто не существует.

[click] Второе название описывает какое-то правило бизнес логики. "Адрес куда показывается для заказа на множество адресов". Я не знаю, если это название бы верно отражало суть теста. Идея тут в том, что его легко понять, потому что оно задаёт правило из предметной области и оно не перестанет делать смысл, если мы проведём рефакторинг.

[click:2] Насчёт включения в тесты имён классов и методов. [click] В наших проектах мы везде указываем названия тестируемых классов. Да, мы тестируем не класс, а поведение, но класс выступает в данном случае как входная точка. В большинстве раннеров это не имеет смысла, так как там указывается название файла. У нас на фронте есть правило "один файл - одна сущность", но если бы его не было, то указывать название класса имело бы больший смысл - с помощью describe'ов можно группировать моки, например. Вреда в этом мало - имена классов меняются редко, используются они один раз на файл *в наших проектах*, и даже если мы поменяем название класса, то можно будет понять по названию самих тестов и файла, что сломалось.

[click] Название метода - это уже слишком далеко. Это усложняет рефакторинг, так как приходится держать в голове дополнительное правило "переименовал метод - переименуй тесты". Имя метода только помешает разработке.

[click:2] В итоге, если следуем этим рекомендациям, у нас получаются легко читаемые и понятные тесты, которые не страшно отрефакторить или изменить.
-->


---
layout: center
---

<h1 class="text-green-400">Как называть тесты?</h1>

- Не используйте шаблоны, сохраняйте свободу самовыражения
- Описывайте логику, а не код
- Пишите так, будто объясняете не-программисту

<!--
Резюмируем.

- Не вводите строгие шаблоны для нейминга тестов
- Забудьте о деталях имплементации, описывайте правила, предметную область
- Если тестируете утилиты, где нет бизнес логики, то пишите просто, как если бы объясняли логику не-программисту

-->

---
layout: center
---

<h1 class="text-green-400">Публичный API ≠ Поведение системы</h1>

<!--
Я слышал часто от Андрея, что мы тестируем публичный API системы. Напрашивается сделать вывод, что публичный API это и есть наблюдаемое поведение системы, но это не так.
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

```ts {*|9-12|5-7}
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

```ts {*|19|20}
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
````

<!-- Публичный или приватный интерфейс относится к коду. Мы как программисты ручками пишем `public` и `private`. Наблюдаемое поведение и детали имплементации это другое, их мы проектируем. Сейчас я вам наглядно покажу, почему разница важна.

Скажем, у нас есть такой класс. В TypeScript'е все поля по умолчанию public, [click] поэтому я их уберу для простоты.

Пускай у нас есть правило, длина имени пользователя 64 символа. Имеем два метода, [click] переименовать пользователя и [click] нормализовать имя.

[click] Используем:

- [click] Нормализуем имя
- [click] Переименовываем пользователя
-->

---
layout: center
---

<h1 class="text-green-400">...</h1>

---
layout: center
---

<h1 class="text-green-400">Profit?</h1>

---
layout: center
---

````md magic-move {lines:true}

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

```ts {2-3|*}
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

```ts
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

it('Assigns new user name', () => {
  service = TestBed.inject(UserService)
  const newName = 'aboba'

  service.name = newName;

  expect(userStoreMock.name).toBe(newName)
})
```
````

<!--
Не так быстро.

[click] Во-первых, мы можем просто присвоить что угодно в поле name.

[click] Конечно, опытный разработчик такого не допустит, и пометит приватные поля. Идея тут в том, что это несовпадение публичного API с поведением. [click] Если бы мы тестировали публичный API, то написали бы тест [click] "Присваивает новое имя пользователю". Технически, правило "Не тестируй приватные поля" тут полностью соблюдается. Имя-то не приватное.
-->

---
layout: center
---

<img class="mx-auto" src="/4.1.png" />

<!--
В идеальном мире, публичный API и наблюдаемое поведение полностью совпадают, ровно как приватный API совпадает с деталями имплементации. Это можно назвать хорошо спроектированной системой.
-->

---
layout: center
---

<img class="mx-auto" src="/5.1.png" />

<!--
Ситуация, когда класс позволяет своему потребителю больше, чем разумно стоило бы, называется утечкой деталей имплементации.

...

...
-->

---
layout: center
---

````md magic-move {lines:true}
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

```ts {*|20-21|*}
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

it('renames user', () => {
  service = TestBed.inject(UserService)
  const userMock = makeUser({ id: 1 })
  userStoreMock.getUser.mockReturnValue(userMock)

  const name = service.normalizeName('   new name   ')
  service.renameUser(1, normalizedName)

  expect(userStoreMock.upadteUser).toHaveBeenCalledWith(
    'new name'
  )
})
```

```ts {*|21|*}
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

it('renames user', () => {
  service = TestBed.inject(UserService)
  const userMock = makeUser({ id: 1 })
  userStoreMock.getUser.mockReturnValue(userMock)

  service.renameUser(1, normalizedName)

  expect(userStoreMock.upadteUser).toHaveBeenCalledWith(
    expect.objectContaining({ name: 'new name' })
  )
})
```
````

<!--
В этом примере, когда мы делаем name публичным, происходит именно это.

[click] В реальном проекте, конечно, вы такого не встретите. Но может ли кто-нибудь назвать, где здесь ещё одна утечка имплементации?

[click] (Верно,) это публичный `normalizeName`. Точно так же, как и с именем, мы можем просто напрямую вызвать `renameUser`. Сделать `normalizeName` приватным недостаточно.

[click] Нужно использовать этот метод внутри `renameUser`, и вот теперь публичный API этого.. *сервиса на 14 строк* становится правильно спроектированным.

[click] Если следовать принципу "тестируй поведение", то этот косяк можно выявить. Для правильной работы сервиса вам потребуется протестировать сценарий "Переименовывает пользователя". [click] В секции действий должен быть один вызов, но для переименования пользователя нам нужно два.

[click:4] Таким образом, тесты делают ваш код красивым
-->

---
layout: center
---

<h1 class="text-green-400">Что мы поняли?</h1>

- С ростом проекта растёт вероятность регрессий
- Тесты защищают от регрессий
- С тестами, проекты живут дольше, чем без них
- Хорошие тесты защищают от регрессий лучше, чем плохие

---
layout: center
---

<h1 class="text-green-400">Что мы поняли?</h1>

<div class="pb-3">Хороший тест...</div>

- Использует паттерн ААА
- Использует фабрики моков
- Производит 1 действие
- Проверяет обозреваемое поведение
- Никогда не включает детали имплементации
- В своём названии содержит правило бизнес логики

---

<img class="h-90% mx-auto" src="/6.png" />

<!--
У тестов есть четыре атрибута, которые вам нужно максимизировать.

- Устойчивость к рефакторингу или то, насколько тест хрупкий. Самый хрупкий тест проверяет, если исходный код метода равен правильному. заменили пробелы на табы - сломался. Вам нужно минимизировать вашу привязку к тестируемому коду.

- Простота поддержки. То, как написаны сами тесты. Пишите простые понятные названия, используйте фабрики моковых данных, параметризованные тесты, но не заходите слишком далеко, держите ваши тесты изолированными друг от друга.

- Защита от багов и быстрая обратная связь взаимоисключающие. Интеграционные тесты медленные, но максимально защищают от багов. Юнит тесты быстрые, но слабо защищают от багов. На обоих стульях вы не усидите.
-->

---
layout: two-cols
---

<img src="/book.png" class="h-65vh ml-auto" />

::right::

![](/slidev.png)

---