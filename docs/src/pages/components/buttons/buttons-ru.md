---
title: Компонент React Button
components: Button, IconButton, ButtonBase
materialDesign: https://material.io/components/buttons
githubLabel: 'component: Button'
waiAria: 'https://www.w3.org/TR/wai-aria-practices/#button'
---

# Button

<p class="description">Кнопки позволяют пользователям выполнять действия и делать выбор одним нажатием.</p>

[Кнопки](https://material.io/design/components/buttons.html) обозначают действия, которые могут выполнять пользователи. Они используются в таких местах пользовательского интерфейса, как:

- Диалоги
- Всплывающие окно
- Формы
- Карточки
- Панели инструментов

{{"component": "modules/components/ComponentLinkHeader.js"}}

## Contained buttons

[Блочные кнопки](https://material.io/design/components/buttons.html#contained-button) имеют высокий акцент, отличаются использованием возвышения и заполнения. Они содержат действия, которые являются основными для вашего приложения.

{{"demo": "pages/components/buttons/ContainedButtons.js"}}

Вы можете убрать эффект "всплытия" с помощью пропа `disableElevation`.

{{"demo": "pages/components/buttons/DisableElevation.js"}}

## Text buttons

В Карточках (Cards) текстовые кнопки помогают сохранить акцент на содержании карточки.

- В диалогах
- В карточках - Cards

В Карточках (Cards) текстовые кнопки помогают сохранить акцент на содержании карточки.

{{"demo": "pages/components/buttons/TextButtons.js"}}

## Outlined buttons

[Контурные кнопки](https://material.io/design/components/buttons.html#outlined-button) - это кнопки со средним акцентом. They contain actions that are important, but aren't the primary action in an app.

Выделенные кнопки также являются альтернативой выделенным кнопкам или могут использоваться как альтернатива текстовым кнопкам.

{{"demo": "pages/components/buttons/OutlinedButtons.js"}}

## Handling clicks

All components accept an `onClick` handler that is applied to the root DOM element.

```jsx
<Button onClick={() => { alert('clicked') }}>Click me</Button>
```

Note that the documentation [avoids](/guides/api/#native-properties) mentioning native props (there are a lot) in the API section of the components.

## Кнопка загрузки файла

{{"demo": "pages/components/buttons/UploadButtons.js"}}

## Размеры

Fancy larger or smaller buttons? Use the `size` property.

{{"demo": "pages/components/buttons/ButtonSizes.js"}}

## Кнопки с иконками и текстом

Sometimes you might want to have icons for certain buttons to enhance the UX of the application as we recognize logos more easily than plain text. Например, если у вас есть кнопка удаления, вы можете пометить ее значком мусорной корзины.

{{"demo": "pages/components/buttons/IconLabelButtons.js"}}

## Icon button

Кнопки с иконками обычно находятся на панелях навигации и на панелях инструментов.

Значки также подходят для кнопок переключения, которые позволяют выбрать элемент или отменить выбор, например, добавление или удаление звезды для элемента.

{{"demo": "pages/components/buttons/IconButtons.js"}}

## Настраиваемые кнопки

Ниже находятся примеры кастомизации компонента. You can learn more about this in the [overrides documentation page](/customization/how-to-customize/).

{{"demo": "pages/components/buttons/CustomizedButtons.js", "defaultCodeOpen": false}}

🎨 If you are looking for inspiration, you can check [MUI Treasury's customization examples](https://mui-treasury.com/styles/button).

## Сложные кнопки

The loading buttons can show loading state and disable interactions.

{{"demo": "pages/components/buttons/LoadingButtons.js"}}

Здесь можно ознакомится [с примером использования с react-router](/guides/composition/#button).

Компонент ButtonBase устанавливает `pointer-events: none;` на отключенных (disabled) кнопках, что отменяет появление disabled-курсора.

## Complex buttons

Текстовые кнопки, плавающие кнопки действий, блочные кнопки построены на основе одного и того же компонента: `ButtonBase`. You can take advantage of this lower-level component to build custom interactions.

{{"demo": "pages/components/buttons/ButtonBase.js"}}

## Сторонняя библиотека маршрутизации

Одно из обыденных случаев использования кнопки - это навигация на другую страницу. Одно из обыденных случаев использования кнопки - это навигация на другую страницу. However for certain focus polyfills `ButtonBase` requires the DOM node of the provided component. Этого можно достичь, указав ref для данного компонента, ожидая что компонент пересылает этот ref в базовый узел DOM. Учитывая то, что многие наши компоненты используют `ButtonBase`, вы сможете пользоваться ими повсюду в вашем приложении.

Здесь можно ознакомится [с примером использования с react-router](/guides/composition/#button).

## Ограничения

### Cursor not-allowed

Компонент ButtonBase устанавливает `pointer-events: none;` на отключенных (disabled) кнопках, что отменяет появление disabled-курсора.

Есть два способа использовать `not-allowed`

1. **CSS only**. Вы можете удалить все стили событий курсора в выключенном(disabled) состоянии в элементе `<button>`:

```css
.MuiButtonBase-root:disabled {
    cursor: not-allowed;
    pointer-events: auto;
  }
```

Однако:

- Необходимо вернуть `pointer-events: none;` назад, в момент когда вам нужно будет отобразить [подсказку на отключенном элементе](/components/tooltips/#disabled-elements).
- Курсор не изменится, в случае если вы отрендерите какой-либо другой элемент, например `<a>`.

2. **DOM change**. Вы можете обернуть кнопку в дополнительный контейнер:

```jsx
<span style={{ cursor: 'not-allowed' }}>
    <Button component={Link} disabled>
      disabled
    </Button>
  </span>
```

Этот способ работает для всех элементов, в том числе и для `<a>`.
