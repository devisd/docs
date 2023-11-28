# EmailRequirementsPopup Component Documentation

## Introduction

The `EmailRequirementsPopup` component is designed to handle email-related notifications and interactions with users. This documentation provides an overview of how to import and use the `EmailRequirementsPopup` component within your Next.js application.

### Importing the Component

To use the `EmailRequirementsPopup` component, import it into your file as follows:

```javascript
import EmailRequirementsPopup from 'widgets/NotificationForm/NotificationForm'
```

## Prerequisites

Before triggering the component, user session data is required. You can obtain this data asynchronously using the following code:

```javascript
const session = await getSession()
```

From the session data, extract the user's email using `session.user.email`. If the email is `null`, the modal popup (popup) is triggered, prompting the user to provide their email. The user must then confirm their email by following a link sent via email.

### Example Usage

To use the `EmailRequirementsPopup` component, place it directly in your pages or components where needed. The component is entirely independent, as all necessary data is requested from the server within the component itself.

Example usage:

```jsx
<EmailRequirementsPopup />
```

## Component Structure

The `EmailRequirementsPopup` component incorporates several sub-components and logical checks to handle different scenarios. Below is an overview of the key components and their functions:

- `session` (automatically provided by the 'next-auth/react' library)
- `profile` (user profile data, automatically retrieved from the server)
- `<Attention />` (notification component for informing about the absence of an email in the session and user profile)
- `<InputEmail />` (component with an email input field for the user to provide their email)
- `<Confirm />` (component to confirm the user's email after submission)
- `<ErrorPopup />` (component to handle errors related to email confirmation)
- `<Modal />` (component for displaying modals, serving as a wrapper for other components)
- `showInput`, `showText`, `showTextWithLink` (local state variables for logic execution)

## Internationalization (i18n)

The project utilizes internationalization, with the variable `t` representing translation functions. The text content in components is localized using this variable.

## Component Details

### EmailRequirementsPopup Component (`<EmailRequirementsPopup />`)

The `EmailRequirementsPopup` component is designed for flexibility and independence. It handles scenarios such as the absence of an email in the session and user profile or unconfirmed email. The component triggers modal popups and includes sub-components for various actions.

Example Code:

```jsx
<>
  {(session?.user.email === null && profile?.email === null) || profile?.email?.confirmed === false ? (
    <Modal active={active} setActive={handleSetActive} isAlert className={styles.notificationForm}>
      {/* ... */}
      {/* Render other components based on conditions */}
      {/* ... */}
    </Modal>
  ) : null}
</>
```

### Attention Component (`<Attention />`)

The `Attention` component displays a notification about the absence of an email in the session and user profile. It includes buttons for navigation and user interaction.

Example Code:

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

### InputEmail Component (`<InputEmail />`)

The `InputEmail` component provides an input field for the user to enter their email. It handles the form submission and updates the user's email on the server.

Example Code:

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
      <Checkbox name="accept" required />
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

### Confirm Component (`<Confirm />`)

The `Confirm` component prompts the user to verify their email. It includes a button to trigger the verification process.

Example Code:

```jsx
<>
  <div>
    <h2>{t.notification_form.confirm.title}</h2>
    <p>{t.notification_form.confirm.text.replace('{email}', profile?.email?.email)}</p>
  </div>
  <div>
    <Button variant="blue" loading={loading} onClick={handleClick}>
      {loading ? t.notification_form.btn_loading : t.notification_form.btn_confirm}
    </Button>
    <LinkBack title={t.notification_form.btn_back} onClick={() => router.push('/')} />
  </div>
</>
```

### ErrorPopup Component (`<ErrorPopup />`)

The `ErrorPopup` component handles errors related to email confirmation. It includes elements like a title, text, link, and a button with a timeout for retrying the confirmation process.

Example Code:

```jsx
<>
  <div>
    <h2>{t.notification_form.error.title}</h2>
    <div>
      {textBeforeLink}
      <Link href="/profile/update/">{t.notification_form.error.link}</Link>
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

In summary, the `EmailRequirementsPopup` component is highly versatile and can be used throughout the application independently. It handles various email-related scenarios and provides a seamless user experience.
