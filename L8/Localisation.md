# Set up a Collaborative Translation Tool
To set up a collaborative translation tool we used `Traduora`

## What is Traduora
Traduora is an open-source translation management platform designed to streamline the process of managing translations and localizations for software applications and projects. It provides a web-based interface that allows users to collaboratively work on translating content into multiple languages. Traduora aims to simplify the translation workflow, making it more efficient for development teams and translators to handle multilingual content.

## Set up
1. Clone `https://github.com/ever-co/ever-traduora.git`
2. Go to `cd ever-traduora`
3. Run command `docker-compose -f docker-compose.demo.yaml up`
4. Finally go to `http://localhost:8080/` to visit the page

![image](https://github.com/AshrafMd-1/pupilfirst-wd401/assets/98876115/dc77b181-f501-4acd-a45a-f4f59a86b294)

## Process To Set Up
1. Create a project ![image](https://github.com/AshrafMd-1/pupilfirst-wd401/assets/98876115/0b6f1f60-6f92-4325-9653-bc091cbde223)
2. Create translations ![image](https://github.com/AshrafMd-1/pupilfirst-wd401/assets/98876115/eeb8d375-4d30-4f18-b448-2b654e515d8f)
3. Add terms ![image](https://github.com/AshrafMd-1/pupilfirst-wd401/assets/98876115/e310f313-88d6-43d1-ab92-86dbe08717dd)
4. Add translations for each locale ![image](https://github.com/AshrafMd-1/pupilfirst-wd401/assets/98876115/e8328a8f-b5cc-4357-8ef7-fe57023a0a0b)
5. Finally export it in JSON format ![image](https://github.com/AshrafMd-1/pupilfirst-wd401/assets/98876115/2f5b6357-c9a8-4425-b6d6-130bb2c7dff9)
## Add Language Functionality
1. Save the translation files in `src/locale` folder in your react project ![image](https://github.com/AshrafMd-1/pupilfirst-wd401/assets/98876115/967e3c4b-7ef5-4d89-b2dc-4d5807a1315d)
2. run command `npm install react-i18next i18next`
3. Now create a file `i18n.ts` in `src`

```ts
//i18.ts

import i18n from "i18next";  
import { initReactI18next } from "react-i18next";  
import LanguageDetector from "i18next-browser-languagedetector";  
import de from "./locale/de.json";  
import en from "./locale/en.json";  
  
i18n  
  .use(LanguageDetector)  
  .use(initReactI18next)  
  .init({  
    resources: {  
      en: { ...en },  
      de: { ...de },  
    },  
    fallbackLng: "en",  
    lng: "en",  
    detection: {  
      order: ["path", "localStorage", "htmlTag", "cookie"],  
      caches: ["localStorage", "cookie"], // cache user language on  
    },  
    debug: true,  
  });

```

   - `i18n`: The main library for internationalization.
   - `initReactI18next`: A module to integrate i18next with React.
   - `LanguageDetector`: A module for automatically detecting the user's language based on different sources.
   - `de` and `en`: Importing JSON files containing translations for German and English, respectively. These translations are stored in the `./locale/` directory.
   - `.use(LanguageDetector)`: Configuring i18next to use the `LanguageDetector` for automatic language detection.
   - `.use(initReactI18next)`: Integrating i18next with React using the `initReactI18next` module.
   - `.init({ … })`: Initializing i18next with the provided configuration options.
     - `resources`: Object containing language-specific translations. In this case, English (`en`) and German (`de`) are included.
     - `fallbackLng`: The default language to use if the user's preferred language is not available. Here, it's set to English (`en`).
     - `lng`: The initial language to use; here, it's set to English (`en`).
     - `detection`: Configuration for language detection. The order defines the priority of sources for language detection, and caches specify where to store the detected language.
     - `debug`: Enables debugging mode.
1. Import it into `app.tsx` ![image](https://github.com/AshrafMd-1/pupilfirst-wd401/assets/98876115/acc5f9d5-0687-4826-b587-bccb4de2a46d)
2. Now at places where u want translation import `import { useTranslation } from "react-i18next";` and create a constant `const { t } = useTranslation("common");` and for the words type like this `{t("Get Started")}`

```ts
import backgroundImg from "../../../assets/images/background.jpg";  
import { Link } from "react-router-dom";  
import { useState } from "react";  
import { useTranslation } from "react-i18next";  
  
const HomePage = () => {  
  const { t } = useTranslation("common");  
  const [currentUser] = useState(() => {  
    const user =  
      localStorage.getItem("auth_token") ||  
      sessionStorage.getItem("auth_token");  
    if (user) {  
      return user;  
    }  
    return null;  
  });  
  
  return (  
    <div      className="hero min-h-screen "  
      style={{  
        backgroundImage: `url(${backgroundImg})`,  
      }}  
    >      <div className="hero-overlay bg-black  bg-opacity-60"></div>  
      <div className="hero-content text-center text-neutral-content">  
        <div className="max-w-lg">  
          <h1 className="mb-5 text-5xl text-white font-bold">SportUp</h1>  
          <p className="mb-5 text-white text-2xl">  
            SportUp revolutionizes the way you keep score in sports events with  
            its intuitive score board feature. Whether you're at a little league  
            game, a weekend pickup match, or a competitive tournament, SportUp  
            helps you track scores effortlessly. Stay in control, enjoy the  
            game, and elevate your sporting experience with SportUp. Get started  
            today!  
          </p>  
          <div className="flex space-x-4 justify-center items-center border-t-2 border-white pt-4 ">  
            {currentUser ? (  
              <Link  
                className="btn font-bold tracking-wider border-2 border-white text-white bg-transparent py-4 px-6 text-center no-underline  mt-3 transition-all duration-500 ease-in-out rounded-3xl hover:bg-white hover:text-black"  
                to={"/dashboard"}  
              >  
                Dashboard  
              </Link>  
            ) : (  
              <>  
                <Link  
                  className="btn font-bold tracking-wider border-2 border-white text-white bg-transparent py-4 px-6 text-center no-underline  mt-3 transition-all duration-500 ease-in-out rounded-3xl hover:bg-white hover:text-black"  
                  to={"/signup"}  
                >  
                  {t("Get Started")}  
                </Link>  
                <Link                  className="btn font-bold tracking-wider border-2 border-white text-white bg-transparent py-4 px-6 text-center no-underline mt-3 transition-all duration-500 ease-in-out rounded-3xl hover:bg-white hover:text-black "  
                  to={"/login"}  
                >  
                  Login  
                </Link>  
                <Link                  className="btn font-bold tracking-wider border-2 border-white text-white bg-transparent py-4 px-6 text-center no-underline mt-3 transition-all duration-500 ease-in-out rounded-3xl hover:bg-white hover:text-black "  
                  to={"/dashboard"}  
                >  
                  {t("Guest")}  
                </Link>  
              </>  
            )}  
          </div>  
        </div>  
      </div>  
    </div>  
  );  
};  
  
export default HomePage;
```

1. Now create a button for language change where when the user clicks on the button it changes

```ts
//langaugeEdit.ts
const languages = [  
  { code: "en", name: "English" },  
  { code: "de", name: "Deutsch" },  
];  
  
export default function LanguageEdit() {  
  const { i18n } = useTranslation();  
  
  const [selected, setSelected] = useState(() => {  
    const currentLang = languages.find((l) => l.code === i18n.language);  
    return currentLang ? currentLang : languages[0];  
  });
useEffect(() => {  
  i18n.changeLanguage(selected.code);  
}, [selected, i18n]);

...

<Listbox.Option  
  key={langIdx}  
  className={({ active }) =>  
    `relative cursor-default select-none py-2 pl-10 pr-4 ${  
      active ? "bg-amber-100 text-amber-900" : "text-gray-900"  
    }`  
  }  
  value={lang}  
>  
  {({ selected }) => (  
    <>  
      <span  
        className={`block truncate ${  
          selected ? "font-medium" : "font-normal"  
        }`}  
      >        {lang.name}  
      </span>      {selected ? (  
        <span className="absolute inset-y-0 left-0 flex items-center pl-3 text-amber-600">  
          <CheckIcon className="h-5 w-5" aria-hidden="true" />  
        </span>  
      ) : null}  
    </>  
  )}  
</Listbox.Option>
```

Now when you change the language the website language changes
![image](https://github.com/AshrafMd-1/pupilfirst-wd401/assets/98876115/e7f42d1e-303c-4fd3-904b-f99786e99804)

# Date and Time Localization
To Localize date we can use the `Intl.DateTimeFormat`

```js
const date = new Date();

// Create a date formatter for a specific locale
const dateFormatter = new Intl.DateTimeFormat("fr-FR", {
  year: "numeric",
  month: "long",
  day: "numeric",
});

// Format the date
const formattedDate = dateFormatter.format(date);
```

## Explanation

```javascript
// Create a new Date object representing the current date and time
const date = new Date();
```

- This line initializes a new `Date` object, which represents the current date and time at the moment of execution.

```javascript
// Create a date formatter for a specific locale
const dateFormatter = new Intl.DateTimeFormat("fr-FR", {
  year: "numeric",
  month: "long",
  day: "numeric",
});
```

- Here, an `Intl.DateTimeFormat` object is created. This object is designed for formatting date and time values based on the provided locale and formatting options.

- The first argument to `Intl.DateTimeFormat` is the locale string, in this case, "fr-FR," which represents French as spoken in France.

- The second argument is an options object that specifies how the date should be formatted. In this example:
  - `"numeric"` is used for the year, which formats it as a number.
  - `"long"` is used for the month, which displays the full month name.
  - `"numeric"` is used for the day, formatting it as a number.

```javascript
// Format the date
const formattedDate = dateFormatter.format(date);
```

- The `format` method of the `dateFormatter` object is called with the `date` object as an argument. This method formats the date according to the locale and options specified earlier.

- The formatted date is then stored in the variable `formattedDate`.

## Usage
We can use this code

```ts
export default function UserWelcome() {  
  const date = new Date();  
  const {i18n} = useTranslation();  
  
  // Create a date formatter for a specific locale  
  const dateFormatter = new Intl.DateTimeFormat(i18n.language, {  
    year: "numeric",  
    month: "long",  
    day: "numeric",  
  });  
  const timeFormatter = new Intl.DateTimeFormat(i18n.language, {  
    hour: "numeric",  
    minute: "numeric",  
    second: "numeric",  
  });
```

where `i18n.language` will give the language and then the webpage display in that localization
### English
![image](https://github.com/AshrafMd-1/pupilfirst-wd401/assets/98876115/ed595d45-9eb4-4d5a-8b7b-6420245412d4)

### German
![image](https://github.com/AshrafMd-1/pupilfirst-wd401/assets/98876115/df7597e7-2da6-4c49-a829-5c48d48d2282)

# Locale Switching

Here we used a select input to select the desired language

```ts
//langaugeEdit.ts
const languages = [  
  { code: "en", name: "English" },  
  { code: "de", name: "Deutsch" },  
];  
  
export default function LanguageEdit() {  
  const { i18n } = useTranslation();  
  
  const [selected, setSelected] = useState(() => {  
    const currentLang = languages.find((l) => l.code === i18n.language);  
    return currentLang ? currentLang : languages[0];  
  });
useEffect(() => {  
  i18n.changeLanguage(selected.code);  
}, [selected, i18n]);

...

<Listbox.Option  
  key={langIdx}  
  className={({ active }) =>  
    `relative cursor-default select-none py-2 pl-10 pr-4 ${  
      active ? "bg-amber-100 text-amber-900" : "text-gray-900"  
    }`  
  }  
  value={lang}  
>  
  {({ selected }) => (  
    <>  
      <span  
        className={`block truncate ${  
          selected ? "font-medium" : "font-normal"  
        }`}  
      >        {lang.name}  
      </span>      {selected ? (  
        <span className="absolute inset-y-0 left-0 flex items-center pl-3 text-amber-600">  
          <CheckIcon className="h-5 w-5" aria-hidden="true" />  
        </span>  
      ) : null}  
    </>  
  )}  
</Listbox.Option>
```

And we have used the tag here in Navbar

![image](https://github.com/AshrafMd-1/pupilfirst-wd401/assets/98876115/8ddcbd59-81eb-4971-a89c-26ab2071fade)

![image](https://github.com/AshrafMd-1/pupilfirst-wd401/assets/98876115/69da6907-4650-4041-8b93-d6ccd227d664)

This is how we do localization in react
# Video Link
https://drive.google.com/file/d/1_WOokfQrj6eaOHQrgXkXTfGJOPJE96RA/view?usp=sharing
