# Countries5YearsWidget

Компонент `Countries5YearsWidget` представляет собой React-компонент, который предназначен для выбора периода времени и списка стран. Компонент использует библиотеки `react-hook-form` для управления формой и `react-select` для реализации выпадающих списков.

## Пропсы

### Обязательные пропсы

- **name**: строка - уникальное имя для компонента в контексте формы.
- **widget**: объект - содержит информацию о виджете, такую как конечная точка для запроса данных.
- **choices**: строка - строка, содержащая информацию о возможных выборах (периодах и странах).

### Дополнительные пропсы

- **IDynamicComponent**: интерфейс, предоставляющий дополнительные свойства для динамических компонентов.
- **IWidget**: интерфейс, предоставляющий свойства для виджетов.

## Структура компонента

1. **Импорт зависимостей и стилей**
   - `useEffect`, `useMemo`, `useState` - хуки React.
   - `Controller`, `useFormContext` - хуки из библиотеки `react-hook-form`.
   - `IWidget`, `IDynamicComponent` - интерфейсы для типизации пропсов.
   - `useLocale` - хук для получения текущей локали.
   - `request` - функция для отправки запросов на сервер.
   - `Button`, `Select`, `IconNo` - компоненты для отображения кнопок, выпадающих списков и иконок.
   - `FIELD_NAME` - константа с именем поля формы.

2. **Тип данных для ответа от сервера**
   - `CountriesResponse`: объект, представляющий ответ от сервера с информацией о странах.

3. **Функциональный компонент `Countries5YearsWidget`**
   - **Получение текущей локали и функции перевода** (`useLocale`, `translation`).
   - **Доступ к контексту формы** (`useFormContext`).
   - **Парсинг строки выборов** (`parseChoices`): извлечение периодов и стран из строки выборов.
   - **Установка начального состояния на основе выборов** (`initialSelectedFields`).
   - **Стейты для выбранных полей, доступных стран и лет** (`selectedFields`, `availableCountries`).
   - **Генерация массива лет для компонента `Select`** (`years`, `opt`).
   - **Запрос данных о странах с сервера** (`useEffect`).

4. **Обработчики событий**
   - **Добавление нового поля** (`handleAddField`).
   - **Удаление поля** (`handleRemoveField`).
   - **Изменение года** (`handleYearChange`).
   - **Изменение списка стран** (`handleCountriesChange`).

5. **Вспомогательные функции**
   - **Генерация строки вывода на основе выбранных полей** (`generateOutputString`).
   - **Получение доступных стран на основе выбранных полей** (`getAvailableCountries`).
   - **Генерация опций для выбора лет в компоненте `Select`** (`generateYearOptions`).

6. **Отрисовка компонента**
   - **Использование `Controller` из `react-hook-form`**.
   - **Отображение выпадающих списков для выбора лет и стран**.
   - **Возможность добавления и удаления полей**.
   - **Обработка ошибок и отображение кнопки для добавления нового поля**.

Компонент предоставляет пользователю интерфейс для выбора периода и списка стран с использованием динамической формы.

```
'use client'

// Importing necessary dependencies from React and other modules
import { useEffect, useMemo, useState } from 'react'
import { Controller, useFormContext } from 'react-hook-form'
import { IWidget } from 'entities/question'
import { useLocale } from 'shared/lib/i18n'
import request from 'shared/lib/request'
import { Button } from 'shared/ui/Button'
import { Select } from 'shared/ui/Form'
import { IconNo } from 'shared/ui/Icon'
import { FIELD_NAME } from '../../lib/consts'
import { IDynamicComponent } from '../../lib/types'
import * as translation from './translation'
import styles from './widget.module.scss'

// Defining a type for the response from the server
type CountriesResponse = {
  id: number
  priority: number
  value: string
  label: string
}

// Functional component definition
export default function Countries5YearsWidget({ name, widget, choices }: IDynamicComponent & IWidget) {
  // Getting the current locale and translation function
  const locale = useLocale()
  const t = translation[locale]

  // // Getting form context to access form values
  const { getValues } = useFormContext()

  // Parse the input string and extract years and countries
  const parseChoices = (choicesString) => {
    const regex = /(\d{4}) - (.*?)(?=(\d{4} -|$))/g
    const matches = []
    let match
    while ((match = regex.exec(choicesString)) !== null) {
      const yearString = match[1]
      const countriesString = match[2]

      // Assuming year has label and value properties
      const year = {
        label: yearString as string,
        value: Number(yearString),
      }

      const countries = countriesString.split(', ').map((country) => ({ label: country, value: country }))
      matches.push({ year, countries })
    }
    return matches
  }

  // Set initial state based on choices
  const initialSelectedFields = useMemo(() => {
    const initialValue: string = getValues()[FIELD_NAME]
    const parsedChoices = parseChoices(initialValue)
    return parsedChoices.length > 0 ? parsedChoices : [{ year: null, countries: [] }]
  }, [choices])

  // State for selectedFields, availableCountries, and years
  const [selectedFields, setSelectedFields] = useState(initialSelectedFields)
  const [availableCountries, setAvailableCountries] = useState(null)

  // Generating an array of years for the Select component
  const years = Array.from({ length: 6 }, (_, index) => new Date().getFullYear() - index)
  const opt = years.map((year) => ({
    label: year.toString(),
    value: year,
  }))

  // Fetching available countries from the server
  useEffect(() => {
    request<CountriesResponse[]>({
      path: widget.endpoint.slice(7),
    }).then(({ error, data }) => {
      if (error) throw data
      setAvailableCountries(data)
    })
  }, [])

  // Event handler to add a new field to the selectedFields array
  const handleAddField = () => {
    if (selectedFields.length < 6) {
      setSelectedFields([...selectedFields, { year: null, countries: [] }])
    }
  }

  // Event handler to remove a field from the selectedFields array
  const handleRemoveField = (index) => {
    const updatedFields = [...selectedFields]
    updatedFields.splice(index, 1)
    setSelectedFields(updatedFields)
  }

  // Event handler for year change in the Select component
  const handleYearChange = (index, value) => {
    const updatedFields = [...selectedFields]
    updatedFields[index].year = value

    // Reset years and countries for subsequent selects
    for (let i = index + 1; i < updatedFields.length; i++) {
      updatedFields[i].year = null
      updatedFields[i].countries = []
    }

    setSelectedFields(updatedFields)
  }

  // Event handler for countries change in the Select component
  const handleCountriesChange = (index, selectedOptions) => {
    const updatedFields = [...selectedFields]
    updatedFields[index].countries = selectedOptions
    setSelectedFields(updatedFields)
  }

  // Function to generate the output string based on selectedFields
  const generateOutputString = () => {
    if (selectedFields.length === 0) {
      return ''
    }

    return selectedFields
      .filter((field) => field.year && field.countries.length > 0)
      .map((field) => `${field.year} - ${field.countries.map((country) => country.label).join(', ')}`)
      .join('. ')
  }

  // Function to get available countries based on the selectedFields
  const getAvailableCountries = (index) => {
    const selectedYears = selectedFields.map((field, i) => (i < index ? field.year : null)).filter(Boolean)
    const selectedCountries = selectedFields.map((field, i) => (i < index ? field.countries : [])).flat()
    const availableCountriesForYear = availableCountries
      ?.filter(
        (country) =>
          !selectedYears.includes(country.value) && !selectedCountries.find((c) => c.value === country.value)
      )
      .map((country) => ({
        label: country.label,
        value: country.value,
      }))

    return index < selectedFields.length ? availableCountriesForYear : availableCountries
  }

  // Function to generate year options for the Select component
  const generateYearOptions = () => {
    const selectedYears = selectedFields.map((field) => field.year).filter(Boolean)
    return opt.filter((option) => !selectedYears.includes(option.value))
  }

  // Rendering the component
  return (
    <Controller
      name={name}
      render={({ field: { onChange } }) => {
        // Checking if any field is empty
        const isAnyFieldEmpty = selectedFields.some((field) => !field.year || field.countries.length === 0)

        return (
          <>
            {selectedFields.map((field, index) => (
              <div key={index} className={styles.year}>
                <Select
                  className={styles.select}
                  classNamePrefix="react-select"
                  options={generateYearOptions()}
                  variant="white"
                  placeholder={t?.year}
                  styles={{
                    control: (styles) => ({
                      ...styles,
                      backgroundColor: 'white',
                      height: 50,
                      width: 98,
                    }),
                  }}
                  defaultValue={field.year}
                  onChange={(selectedOption) => handleYearChange(index, selectedOption.value)}
                />
                <Select
                  options={getAvailableCountries(index)}
                  isMulti
                  placeholder={t?.placeholder}
                  className={styles.select}
                  styles={{
                    control: (styles) => ({
                      ...styles,
                      backgroundColor: 'white',
                      minHeight: 50,
                      width: 470,
                      display: 'flex',
                      marginBottom: 1,
                    }),
                    multiValueLabel: (styles) => ({
                      ...styles,
                      color: 'white',
                    }),
                    multiValue: (styles) => ({
                      ...styles,
                      backgroundColor: '#5A8FD4',
                      padding: '0 5px',
                      color: 'white',
                      borderRadius: 6,
                    }),
                    multiValueRemove: (styles) => ({
                      ...styles,
                      ':hover': {
                        backgroundColor: '#5AAFF9',
                        color: 'white',
                      },
                    }),
                  }}
                  defaultValue={field.countries}
                  onChange={(selectedOptions) => {
                    handleCountriesChange(index, selectedOptions)
                    const outputString = generateOutputString()
                    onChange(outputString)
                  }}
                />
                {selectedFields.length > 1 && (
                  <button className={styles.noBtn} type="button" onClick={() => handleRemoveField(index)}>
                    <IconNo />
                  </button>
                )}
              </div>
            ))}
            {selectedFields.length > 1 && <span className={styles.error}>{isAnyFieldEmpty && t.error}</span>}
            <Button variant="outline-blue" type="button" className={styles.addBtn} onClick={handleAddField}>
              {t?.button}
            </Button>
          </>
        )
      }}
    />
  )
}
```
