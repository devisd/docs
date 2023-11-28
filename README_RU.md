# Документация по компоненту EmailRequirementsPopup

## Введение

Компонент `EmailRequirementsPopup` предназначен для обработки уведомлений и взаимодействия с пользователями, связанных с электронной почтой. Данная документация предоставляет обзор того, как импортировать и использовать компонент `EmailRequirementsPopup` в вашем приложении на Next.js.

### Импорт компонента

Чтобы использовать компонент `EmailRequirementsPopup`, импортируйте его в ваш файл следующим образом:

```javascript
import EmailRequirementsPopup from 'widgets/NotificationForm/NotificationForm';
```

## Предварительные требования

Для активации компонента необходимы данные сессии пользователя. Вы можете получить эти данные асинхронным запросом, используя следующий код:

```javascript
const session = await getSession();
```

Из данных сессии извлеките электронную почту пользователя с помощью `session.user.email`. Если электронная почта равна `null`, триггерится модальное окно (popup), призывая пользователя предоставить свою электронную почту. Пользователь должен затем подтвердить свою электронную почту, перейдя по ссылке из электронного письма.

### Пример использования

Чтобы использовать компонент `EmailRequirementsPopup`, разместите его непосредственно на ваших страницах или компонентах по мере необходимости. Компонент полностью независим, поскольку все необходимые данные запрашиваются из сервера внутри самого компонента.

Пример использования:

```jsx
<EmailRequirementsPopup />
```

## Структура компонента

Компонент `EmailRequirementsPopup` включает в себя несколько подкомпонентов и логических проверок для обработки различных сценариев. Ниже представлен обзор основных компонентов и их функций:

- `session` (автоматически предоставляется библиотекой 'next-auth/react')
- `profile` (данные профиля пользователя, автоматически запрашиваются с сервера)
- `<Attention />` (компонент уведомления об отсутствии электронной почты в сессии и профиле пользователя)
- `<InputEmail />` (компонент с полем ввода для электронной почты пользователя)
- `<Confirm />` (компонент для подтверждения электронной почты после отправки)
- `<ErrorPopup />` (компонент для обработки ошибок, связанных с подтверждением электронной почты)
- `<Modal />` (компонент модального окна, используется как оболочка для других компонентов)
- `showInput`, `showText`, `showTextWithLink` (локальные переменные состояния для логики выполнения)

## Интернационализация (i18n)

Проект использует интернационализацию, переменная `t` представляет функции перевода. Текстовое содержимое в компонентах локализуется с использованием этой переменной.

## Подробности по компонентам

### Компонент EmailRequirementsPopup (`<EmailRequirementsPopup />`)

Компонент `EmailRequirementsPopup` разработан с учетом гибкости и независимости. Он обрабатывает сценарии, такие как отсутствие электронной почты в сессии и профиле пользователя или неподтвержденная электронная почта. Компонент вызывает модальные всплывающие окна и включает в себя подкомпоненты для различных действий.

Пример кода:

```jsx
<>
  {(session?.user.email === null && profile?.email === null) || profile?.email?.confirmed === false ? (
    <Modal active={active} setActive={handleSetActive} isAlert className={styles.notificationForm}>
      {/* ... */}
      {/* Рендер других компонентов в зависимости от условий */}
      {/* ... */}
    </Modal>
  ) : null}
</>
```

### Компонент Attention (`<Attention />`)

Компонент `Attention` отображает уведомление об отсутствии электронной почты в сессии и профиле пользователя. Включает в себя кнопки для навигации и взаимодействия с пользователем.

Пример кода:

```jsx
<>
  <div>
    <h2>{t.notification_form.attention.title}</h2>
    <p>{t.notification_form.attention.subtitle}</p>
    <p>{t.notification_form.attention.text}</p>
  </div>
  <div>
    <LinkBack title={t.notification_form.btn_back} onClick={() => router.push('/')} />
    <Button variant="blue" onClick={onClick}>
      {t.notification_form.attention.btn_blue}
    </Button>
  </div>
</>
```

### Компонент InputEmail (`<InputEmail />`)

Компонент `InputEmail` предоставляет поле ввода для пользователя для ввода своей электронной почты. Обрабатывает отправку формы и обновление электронной почты пользователя на сервере.

Пример кода:

```jsx
<>
  <Form onSubmit={handleSubmit(onSubmit)}>
    <div>
      <h2>{t.notification_form.input.title}</h2>
      <Input
        placeholder="nick@gmail.com"
        name="email"
        inputMode="email"
        value={email}
        onChange={handleChange}
        required={t.index.required_field}
        className={styles.inputFormInput}
      />
    </div>
    <div>
      <Checkbox name="accept"

 required />
      <p>{t.notification_form.input.checkbox}</p>
    </div>
    <div>
      <LinkBack title={t.notification_form.btn_back} onClick={() => router.push('/')} />
      <Submit variant="blue" loading={loading} validating>
        {loading ? t.notification_form.btn_loading : t.notification_form.input.btn_blue}
      </Submit>
    </div>
  </Form>
</>
```

### Компонент Confirm (`<Confirm />`)

Компонент `Confirm` призывает пользователя подтвердить свою электронную почту. Включает в себя кнопку для запуска процесса подтверждения.

Пример кода:

```jsx
<>
  <div>
    <h2>{t.notification_form.confirm.title}</h2>
    <p>
      {t.notification_form.confirm.text.replace('{email}', profile?.email?.email)}
    </p>
  </div>
  <div>
    <Button variant="blue" loading={loading} onClick={handleClick}>
      {loading ? t.notification_form.btn_loading : t.notification_form.btn_confirm}
    </Button>
    <LinkBack title={t.notification_form.btn_back} onClick={() => router.push('/')} />
  </div>
</>
```

### Компонент ErrorPopup (`<ErrorPopup />`)

Компонент `ErrorPopup` обрабатывает ошибки, связанные с подтверждением электронной почты. Включает элементы, такие как заголовок, текст, ссылка и кнопка с таймаутом для повторной попытки процесса подтверждения.

Пример кода:

```jsx
<>
  <div>
    <h2>{t.notification_form.error.title}</h2>
    <div>
      {textBeforeLink}
      <Link href="/profile/update/">
        {t.notification_form.error.link}
      </Link>
      {textAfterLink}
    </div>
  </div>
  <div>
    <Button variant="blue" loading={loading} onClick={handleClick}>
      {loading ? t.notification_form.btn_loading : t.notification_form.btn_confirm}
    </Button>
    <LinkBack title={t.notification_form.btn_back} onClick={handleBackClick} />
  </div>
</>
```

В заключение, компонент `EmailRequirementsPopup` очень гибок и может использоваться по всему приложению независимо. Он обрабатывает различные сценарии, связанные с электронной почтой, и предоставляет бесперебойный пользовательский опыт.
