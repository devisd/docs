# NotificationForm Component Documentation

## Introduction

The `NotificationForm` component is a versatile component designed for displaying notifications and interacting with the user based on their session data. This documentation provides an overview of how to import and use the `NotificationForm` component within your application.

### Importing the Component

To use the `NotificationForm` component, import it into your file as follows:

```javascript
import NotificationForm from 'widgets/NotificationForm/NotificationForm'
```

## Prerequisites

Before triggering the component, user session data is required. You can obtain this data asynchronously using the following code:

```javascript
const session = await getSession()
```

From the session data, extract the user's email using `session.user.email`. If the email is `null`, trigger the modal popup, prompting the user to provide their email. The user must then confirm their email by following a link sent via email.

Example usage:

```javascript
{
  session?.user.email === null && <NotificationForm session={session} />
}
```

## Component Structure

The `NotificationForm` component incorporates several sub-components and logical checks to handle different scenarios. Below is an overview of the key components and their functions:

- `session` (user session data passed as a prop)
- `profile` (user profile data, automatically retrieved from the server)
- `<Attention />` (notification component for informing about the absence of an email in the session and user profile)
- `<InputEmail />` (component with an email input field for the user to provide their email)
- `<Confirm />` (component to confirm the user's email after submission)
- `<ErrorPopup />` (component to handle errors related to email confirmation)
- `<Modal />` (component for displaying modals, serving as a wrapper for other components)
- `showInput`, `showText`, `showTextWithLink` (local state variables for logic execution)

## Internationalization (i18n)

The project utilizes internationalization, with the variable `t` representing translation functions. The text content in components is localized using this variable.

## Example Usage

Below is an example code snippet illustrating the conditional rendering of different parts of the `NotificationForm` component:

```javascript
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

## Component Details

### Attention Component (`<Attention />`)

The `Attention` component is responsible for notifying the user about the absence of an email in the session and user profile. It contains the following elements:

- **Title and Subtitle:** Displays a title and subtitle to convey the attention-grabbing message.

- **Text Content:** Provides additional information to guide the user on what action to take.

- **Link and Button:** Includes a link for navigation and a button for user interaction.

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

The `InputEmail` component provides an input field for the user to enter their email. It handles the form submission and updates the user's email on the server. Key elements include:

- **Form Structure:** Includes a form with an email input field and a checkbox for user acceptance.

- **Input Field:** Allows users to enter their email.

- **Checkbox:** Requires users to accept certain conditions.

- **Buttons:** Provides buttons for navigation and form submission.

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

The `Confirm` component prompts the user to verify their email. It includes elements such as:

- **Title and Text:** Displays a title and text to inform the user about the email confirmation process.

- **Button:** Triggers the verification process.

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

The `ErrorPopup` component handles errors related to email confirmation. It includes elements like:

- **Title and Text:** Communicates the error and provides information on what the user can do.

- **Link:** Offers a link for additional actions.

- **Button with Timeout:** Triggers a retry of the confirmation process, with a timeout for loading indication.

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

These detailed descriptions should provide a clearer understanding of each component's role and the elements they contain within the `NotificationForm` component.

## Conclusion

The `NotificationForm` component is designed to be flexible and independent, allowing it to be used throughout the application seamlessly. By following the guidelines in this documentation, you can integrate this component into your project to handle email-related notifications and interactions with users.
