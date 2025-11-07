# Web App React
Create a React Web App with Vite

## Tools
- Vite:
    It helps to compile the code, minify it, put together the node modules.
    run: bash`npm create vite@latest`

## Structure

:::connector
    - 📂 public
        :::connector
            - 📄 icon.svg
            - ... The folder where static files that are not processed or transformed by Vite are normally stored
        :::
    - 📂 src
        :::connector
            - 📂 index
                    :::connector
                        - 📂 assets
                            :::connector
                                - ... Static files such as: Images (.png, .svg, .jpg), Icons, etc
                            :::
                        - 📂 components
                            :::connector
                                - ... Reusable and small components: buttons, cards, modals, headers, etc
                            :::
                        - 📂 hooks
                            :::connector
                                - ... Custom hooks, that is, functions with reusable logic based on React Hooks (useState, useEffect, etc.)
                            :::
                        - 📂 utils
                            :::connector
                                - ... Pure functions or helpers that do not depend on React
                            :::
                        - 📄 App.css
                        - 📄 App.tsx
                    :::
            - ... Folders containing React content from another HTMLs
            - 📄 style.css
            - 📄 util.ts
        :::
    - 📄 index.html
    - ... Other HTMLs
    - 📄 commands.md
    - 📄 vite.config.js
:::


## Configuration
1. Install the tools, in Vite create a React JS proyect
2. Create green`vite.config.js` in the root folder for compile all html and put relative paths
    ```javascript -folded
        import { defineConfig } from 'vite'
        import react from '@vitejs/plugin-react'
        import { resolve } from "path";
        import { readdirSync } from "fs";

        // Automatically find all HTML files in the root directory
        const htmlFiles = readdirSync(".").filter((file) => file.endsWith(".html"));
        const input = Object.fromEntries(
            htmlFiles.map((file) => [
                file.replace(".html", ""), // Use filename without extension as the key
                resolve(__dirname, file),
            ])
        );

        // https://vite.dev/config/
        export default defineConfig({
        base: "./", // Use relative paths in build output
            build: {
                rollupOptions: {
                    input,
                },
            },
        plugins: [react()],
        })
    ```
3. Create green`commands.md` in the root folder for save and run the commands:
    ```markdown
        Commands in the proyect:
         run `npx vite`(Run preview)
         run build`npx vite build`(Build)
    ```
4. Create green`util.ts` in the purple`src` folder for easy access to tools:
    ```javascript -folded
        // utils.ts - Common utilities for React
        import React, { useState, useEffect, useRef } from 'react';

        // querySelector
        const $ = (selector: string) => document.querySelector(selector);

        export { $, React, useState, useEffect, useRef };
    ```
5. Create or edit green`style.css` in the purple`src` folder for a global style and themes:
    ```css -folded
        :root {
            /* Common variables for all themes */
            --border-radius: 8px;
            --spacing-xs: 4px;
            --spacing-sm: 8px;
            --spacing-md: 16px;
            --spacing-lg: 24px;
            --spacing-xl: 32px;
            --spacing-2xl: 48px;

            /* Font */
            --font-family: "Ubuntu", -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Oxygen, Cantarell,
                "Helvetica Neue", sans-serif;
        }

        /* Themes */
        :root,
        [data-theme="dark"] {
            /* Dark theme style */
            --chroma: 0.01; /* 0.0 to 0.2 */
            --hue: 126; /* 20 to 340 */

            --primary-color: #a5c14f;
            --accent-color: #238636;
            --error-color: #ef4444;

            --bg: oklch(0.15 var(--chroma) var(--hue));
            --bg-dark: oklch(0.1 var(--chroma) var(--hue));
            --bg-light: oklch(0.2 var(--chroma) var(--hue));
            --bg-highlight: oklch(0.25 var(--chroma) var(--hue));
            --bg-hover: oklch(0.5 0.03 246);

            --color: oklch(0.96 0.03 246);
            --color-secondary: oklch(0.76 0.03 246);
            --color-muted: #a1a1aa;

            --border: oklch(0.4 0.03 246);
            --border-muted: oklch(0.3 0.03 246);

            --scroll-bar: var(--color-muted) var(--bg-highlight);
        }

        [data-theme="dark-green"] {
            /* Dark Green theme style */
            --chroma: 0.015; /* 0.0 to 0.2 */
            --hue: 150; /* 20 to 340 */
        }

        [data-theme="dark-blue"] {
            /* Dark Blue theme style */
            --chroma: 0.015; /* 0.0 to 0.2 */
            --hue: 246; /* 20 to 340 */
        }

        [data-theme="light"] {
            /* Light theme by default */
            --chroma: 0; /* 0.0 to 0.2 */
            --hue: 110; /* 20 to 340 */

            --primary-color: oklch(0.4 0.1 126);
            --accent-color: #f59e0b;
            --error-color: #ef4444;

            --bg: oklch(0.98 var(--chroma) var(--hue));
            --bg-dark: oklch(1 var(--chroma) var(--hue));
            --bg-light: oklch(0.96 var(--chroma) var(--hue));
            --bg-highlight: oklch(0.9 var(--chroma) var(--hue));
            --bg-hover: oklch(0.86 0.01 var(--hue));

            --color: oklch(0.1 0.02 var(--hue));
            --color-secondary: oklch(0.2 0.02 var(--hue));
            --color-muted: #94a3b8;

            --border: oklch(0.7 0.01 var(--hue));
            --border-muted: oklch(0.8 0.01 var(--hue));

            --scroll-bar: var(--color-muted) var(--bg-highlight);
        }

        /* Global styles */
        * {
            margin: 0;
            padding: 0;
            outline: none;
            box-sizing: border-box;
        }

        html {
            scrollbar-width: thin;
            scrollbar-color: var(--scroll-bar);
        }

        body {
            font-family: var(--font-family);
            line-height: 1.6;
            color: var(--color-secondary);
            background-color: var(--bg-dark);
        }
    ```
6. Modify the green`index.html` in the root folder and put:
    ```html -folded
        <!DOCTYPE html>
        <html lang="en">
            <head>
                <meta charset="UTF-8" />
                <link rel="icon" type="image/svg+xml" href="/icon.svg" />
                <meta name="viewport" content="width=device-width, initial-scale=1.0" />
                <title>Tittle</title>
            </head>
            <body>
                <div id="root"></div>
                <script type="module">
                    import React from "react";
                    import { createRoot } from "react-dom/client";
                    import "./src/style.css";
                    import App from "./src/index/App.tsx";

                    const root = createRoot(document.getElementById("root"));
                    root.render(
                        React.createElement(React.StrictMode, null, React.createElement(App))
                    );
                </script>
            </body>
        </html>
    ```
    The green`App.tsx` used is:
    ```js -folded
        import { useState } from 'react'
        import './App.css'

        function App() {
        const [count, setCount] = useState(0)

        return (
            <>
            <div>Page content goes here</div>
            <button onClick={() => setCount((count) => count + 1)}>count is {count}</button>
            </>
        )
        }

        export default App
    ```
    The green`App.css` is used to add a CSS to just the green`index.html` unlike green`style.css` which is or should be global
7. If you are going to use github create green`.gitignore` in the root folder and put:
    ```markdown -min -folded
        # Logs
        logs
        *.log
        npm-debug.log*
        yarn-debug.log*
        yarn-error.log*
        pnpm-debug.log*
        lerna-debug.log*

        node_modules
        dist
        docs
        dist-ssr
        *.local

        # Editor directories and files
        .vscode/*
        .github/*
        !.vscode/settings.json
        !.vscode/tasks.json
        !.vscode/launch.json
        !.vscode/extensions.json
        .idea
        .DS_Store
        *.suo
        *.ntvs*
        *.njsproj
        *.sln
        *.sw?
    ```
8. If you are going to use copilot create green`copilot-instructions.md` in the purple`.github` folder, you can put things like:
    ```markdown -min
        Always write comments in English.
        Ignore the /dist folder.
    ```




Arreglar Language SELECTOR ARREGLAR ARREGLAR

## How to set a language selector

Run:
```bash
    npm install i18next
```

In green`vite.config.js` in javascript`defineConfig({` add:
```bash
    define: {
        __BUILD_NUMBER__: JSON.stringify(Date.now()),
    },
```

Create green`components/languages.js` and inside put **(Don't forget to change AVAILABLE_LANGUAGES to put the languages you are going to accept)**:
```javascript -folded
    // Simple i18n system with i18next
    import i18next from "i18next";

    const LANG_KEY = "i18nextLng";
    const CACHE_KEY_PREFIX = "i18n_cache_";
    const BUILD_KEY = "i18n_build";

    // Available languages with native names
    export const AVAILABLE_LANGUAGES = [
        { code: "en", name: "English" },
        { code: "es", name: "Español" },
        { code: "fr", name: "Français" },
        { code: "de", name: "Deutsch" },
        { code: "it", name: "Italiano" },
        { code: "pt", name: "Português" },
        { code: "zh", name: "中文" },
        { code: "ja", name: "日本語" },
    ];

    const SUPPORTED_LANGS = AVAILABLE_LANGUAGES.map(lang => lang.code);

    // Track all namespaces that are used
    const registeredNamespaces = new Set();

    // Get language from localStorage or browser
    function detectLanguage() {
        const saved = localStorage.getItem(LANG_KEY);
        if (saved && SUPPORTED_LANGS.includes(saved)) return saved;
        
        const browser = navigator.language.split("-")[0];
        return SUPPORTED_LANGS.includes(browser) ? browser : "en";
    }

    // Load translation file with cache busting
    async function loadTranslations(lang, namespace) {
        const isDev = import.meta.env.DEV;
        const cacheKey = `${CACHE_KEY_PREFIX}_${lang}_${namespace}`;
        const storedBuild = localStorage.getItem(BUILD_KEY);
        const currentBuild = String(__BUILD_NUMBER__);
        
        // In development, skip cache - always fetch fresh
        if (!isDev && storedBuild === currentBuild) {
            const cached = localStorage.getItem(cacheKey);
            if (cached) {
                try {
                    return JSON.parse(cached);
                } catch (e) {
                    console.warn(`Failed to parse cached translations for ${namespace}`, e);
                }
            }
        }
        
        // Fetch from server
        const url = `./languages/${lang}/${namespace}.json?v=${currentBuild}`;
        const response = await fetch(url);
        if (!response.ok) throw new Error(`Failed to load ${lang} translations`);
        
        const data = await response.json();
        
        // Cache the data only in production
        if (!isDev) {
            try {
                localStorage.setItem(cacheKey, JSON.stringify(data));
                localStorage.setItem(BUILD_KEY, currentBuild);
            } catch (e) {
                console.warn('Failed to cache translations (localStorage might be full)', e);
            }
        }
        
        return data;
    }

    // Initialize i18next - automatically loads all registered namespaces
    export async function initLanguages() {
        const lang = detectLanguage();
        const resources = { [lang]: {} };
        
        // Get first namespace as default (usually the page namespace)
        const namespaces = Array.from(registeredNamespaces);
        const defaultNS = namespaces[0] || 'index';
        
        // Load all registered translations
        for (const ns of registeredNamespaces) {
            const translations = await loadTranslations(lang, ns);
            resources[lang][ns] = translations;
        }
        
        await i18next.init({
            lng: lang,
            resources,
            ns: namespaces,
            defaultNS: defaultNS,
            fallbackLng: false,
            interpolation: { escapeValue: false },
        });
        
        localStorage.setItem(LANG_KEY, lang);
        document.documentElement.setAttribute("lang", lang);
        
        return i18next;
    }

    // Simple function to get translator for a namespace
    // Automatically registers the namespace for loading
    export function useTranslation(namespace) {
        if (namespace) {
            registeredNamespaces.add(namespace);
        }
        
        return {
            t: (key) => namespace ? i18next.t(key, { ns: namespace }) : i18next.t(key)
        };
    }

    // Get current language
    export function getCurrentLanguage() {
        return i18next.language || detectLanguage();
    }

    // Change language and reload page
    export function changeLanguage(lng) {
        if (!SUPPORTED_LANGS.includes(lng)) {
            console.warn(`Language ${lng} is not supported`);
            return;
        }
        
        localStorage.setItem(LANG_KEY, lng);
        window.location.reload();
    }

    export default i18next;
```

Create green`components/languageSelector.js` and inside put:
```javascript -folded
    // Language selector dropdown
    import { html } from "./utils.ts";
    import { getCurrentLanguage, changeLanguage, AVAILABLE_LANGUAGES } from "./languages.js";

    export default function LanguageSelector() {
        const language = getCurrentLanguage();

        return html`
            <div class="language-selector">
                <select value=${language} onChange=${(e) => changeLanguage(e.target.value)}>
                    ${AVAILABLE_LANGUAGES.map(
                        (lang) => html`<option value=${lang.code}>${lang.name}</option>`
                    )}
                </select>
            </div>
        `;
    }
```

Create a green`languages` folder with this structure:
:::connector
    - 📂 languages
        :::connector
            - 📂 es
                :::connector
                    - 📄 index.json
                    - 📁 components
                        :::connector
                            - 📄 component.json
                        :::
                    - ... Other translation files
                :::
            - 📁 en
            - 📁 de
            - ... Others languages folder
        :::
:::

The translation files must have this structure:
```json
    {
      "welcome": "Text that will be replaced where t('welcome') is in the html",
      "description": "Text that will be replaced where t('description') is in the html"
    }
```

To use the languages in the html you must first add the blue`LanguageSelector` component in some html to be able to change the language and try:
```javascript
    // Import the LanguageSelector.js component
    import LanguageSelector from './components/languageSelector.js';
    
    // Code...

    // Call to the language selector and render it in the <div id="app"></div> element
    function App() {
        return html`
            <h1>Language Selector</h1>
            <${LanguageSelector} />
        `;
    }
    render(html`<${App} />`, $("#app"));
```

Then in the html where you want to have different texts as languages you can put something like this structure:
```javascript
    // Import the languages.js component
    import { useTranslation, initLanguages } from "./components/languages.js";

    // Initialize the translation from the index.json file, you can also use something like "components/component"
    const { t } = useTranslation("index");

    // Where you want to put a text you must put the function t("NameOfText") so inside an html`` you must put ${t("NameOfText")}
    function App() {
        return html`
            <div>
                <h1>${t("welcome")}</h1>
                <p>${t("description")}</p>
            </div>
        `;
    }

    // Initialize languages first, then render
    initLanguages().then(() => {
        render(html`<${App} />`, $("#app"));
    });
```














COMO PONER UN SELECTOR DE TEMA