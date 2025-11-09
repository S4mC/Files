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
                                - ... Functions or helpers that do not depend on React
                            :::
                        - 📄 App.css
                        - 📄 App.tsx
                    :::
            - ... Folders containing React content from another HTMLs
            - 📂 assets
                :::connector
                    - ... Global static files such as: Images (.png, .svg, .jpg), Icons, etc
                :::
            - 📂 components
                :::connector
                    - ... Global reusable and small components: buttons, cards, modals, headers, etc
                :::
            - 📂 hooks
                :::connector
                    - ... Global custom hooks, that is, functions with reusable logic based on React Hooks (useState, useEffect, etc.)
                :::
            - 📂 utils
                :::connector
                    - 📄 util.ts
                     - ... Global functions or helpers that do not depend on React
                :::
            - 📂 styles
                :::connector
                    - 📄 style.css
                    - ... Global css styles (from various HTML)
                :::
        :::
    - 📄 index.html
    - ... Other HTMLs
    - 📄 commands.md
    - 📄 vite.config.js
:::


## Configuration
1. Install the tools, in Vite create a React JS proyect
2. Create green`vite.config.js` in the root folder for compile all html and put relative paths
    ```typescript -folded
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
4. Create green`util.ts` in the purple`src/utils` folder for easy access to tools:
    ```typescript -folded
        // querySelector
        const $ = (selector: string) => document.querySelector(selector);

        export { $ };
    ```
5. Create or edit green`style.css` in the purple`src/styles` folder for a global style and themes:
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
                <div id="root" style="height: 100%; width: 100%; position: absolute;"></div>
                <script type="module">
                    import React from "react";
                    import { createRoot } from "react-dom/client";
                    import "./src/styles/style.css";
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


## How to make an application with multiple languages and a language selector

Run:
```bash
    npm install i18next
```

Create green`src/utils/languages.ts` and inside put **(Don't forget to change AVAILABLE_LANGUAGES to put the languages you are going to accept)**:
```typescript -folded
    // Simple i18n system with i18next
    import i18next from "i18next";
    import { useState, useEffect } from "react";

    const LANG_KEY = "i18nextLng";

    // Available languages with native names
    export interface Language {
        code: string;
        name: string;
    }

    export const AVAILABLE_LANGUAGES: Language[] = [
        { code: "en", name: "English" },
        { code: "es", name: "Español" },
    ];

    const SUPPORTED_LANGS = AVAILABLE_LANGUAGES.map((lang) => lang.code);

    // Track all namespaces that are used
    const registeredNamespaces = new Set<string>();

    // Get browser's preferred language
    function getBrowserLanguage(): string {
        const browserLang = navigator.language.split("-")[0]; // Get 'en' from 'en-US'
        return SUPPORTED_LANGS.includes(browserLang) ? browserLang : "en";
    }

    // Get language from localStorage or browser
    function detectLanguage(): string {
        const saved = localStorage.getItem(LANG_KEY);
        if (saved && SUPPORTED_LANGS.includes(saved)) {
            // Verify the language has translation files available
            const testPath = `/languages/${saved}/index.js`;
            if (translationModules[testPath]) {
                return saved;
            }
            // If saved language doesn't have files, clear it
            localStorage.removeItem(LANG_KEY);
        }
        
        // Try browser language, then fallback to English
        return getBrowserLanguage();
    }

    // Pre-register all translation modules with Vite (this lets Vite know about them at build time)
    // but we only load the specific one we need at runtime
    // Only register root-level files (not nested in folders) so Vite bundles imports together
    const translationModules = import.meta.glob<Record<string, Record<string, string>>>('/languages/*/*.js', { eager: false });

    // Flatten nested objects into dot notation keys
    function flattenTranslations(obj: Record<string, unknown>, prefix = ""): Record<string, string> {
        const result: Record<string, string> = {};

        for (const [key, value] of Object.entries(obj)) {
            const newKey = prefix ? `${prefix}.${key}` : key;

            if (typeof value === "object" && value !== null && !Array.isArray(value)) {
                // Recursively flatten nested objects
                Object.assign(result, flattenTranslations(value as Record<string, unknown>, newKey));
            } else if (typeof value === "string") {
                result[newKey] = value;
            }
        }

        return result;
    }

    // Load translation file with error handling
    async function loadTranslations(lang: string, namespace: string): Promise<Record<string, string>> {
        try {
            // Always load from root-level files only (index.js, hola.js, etc.)
            const moduleLoader = translationModules[`/languages/${lang}/${namespace}.js`];
            
            if (!moduleLoader) {
                console.error(`Translation module not found for ${lang}/${namespace}.js`);
                // Try to load English fallback if not already English
                if (lang !== "en") {
                    console.warn(`Attempting to load English fallback for namespace "${namespace}"`);
                    return await loadTranslations("en", namespace);
                }
                // Return empty object if English also fails
                return {};
            }
            
            // This actually loads the file (dynamic import at runtime)
            // Vite will bundle any imports inside this file automatically
            // The browser's HTTP cache will handle caching of these JS modules
            const module = await moduleLoader();
            const data = module[namespace];

            if (!data) {
                console.error(`Translation data not found for namespace "${namespace}" in ${lang}/${namespace}.js`);
                // Try English fallback
                if (lang !== "en") {
                    return await loadTranslations("en", namespace);
                }
                return {};
            }

            // Flatten nested objects to support dot notation
            const flattenedData = flattenTranslations(data as Record<string, unknown>);

            return flattenedData;
        } catch (error) {
            console.error(`Error loading translations for ${lang}/${namespace}:`, error);
            // Try English fallback on any error
            if (lang !== "en") {
                return await loadTranslations("en", namespace);
            }
            return {};
        }
    }

    // // Load a specific namespace dynamically
    // export async function loadNamespace(lang: string, namespace: string): Promise<void> {
    //     const translations = await loadTranslations(lang, namespace);
        
    //     // Add the namespace to i18next if not already present
    //     if (!i18next.hasResourceBundle(lang, namespace)) {
    //         i18next.addResourceBundle(lang, namespace, translations);
    //     }
    // }

    // Initialize i18next - loads all registered namespaces
    export async function initLanguages(): Promise<typeof i18next> {
        const lang = detectLanguage();
        const resources: Record<string, Record<string, Record<string, string>>> = { [lang]: {} };

        // Get registered namespaces
        const namespaces = Array.from(registeredNamespaces);
        const defaultNS = namespaces[0] || "index";

        // Load all registered translations
        try {
            for (const ns of registeredNamespaces) {
                const translations = await loadTranslations(lang, ns);
                resources[lang][ns] = translations;
            }
        } catch (error) {
            console.error("Error loading translations:", error);
            // Continue with whatever translations we have
        }

        await i18next.init({
            lng: lang,
            resources,
            ns: namespaces.length > 0 ? namespaces : ["index"],
            defaultNS: defaultNS,
            fallbackLng: "en",
            interpolation: { escapeValue: false },
        });

        localStorage.setItem(LANG_KEY, lang);
        document.documentElement.setAttribute("lang", lang);

        return i18next;
    }

    // Simple function to get translator for a namespace
    // Automatically registers the namespace for loading
    export interface TranslationFunction {
        t: (key: string, params?: Record<string, string | number>) => string;
        scope: (prefix: string) => TranslationFunction;
    }

    export function translation(namespace?: string): TranslationFunction {
        if (namespace) {
            registeredNamespaces.add(namespace);
        }

        const createTranslator = (prefix: string = ""): TranslationFunction => ({
            t: (key: string, params?: Record<string, string | number>) => {
                const fullKey = prefix ? `${prefix}.${key}` : key;
                const options = { ns: namespace, ...params };
                return namespace ? i18next.t(fullKey, options) : i18next.t(fullKey, params);
            },
            scope: (newPrefix: string) => {
                const fullPrefix = prefix ? `${prefix}.${newPrefix}` : newPrefix;
                return createTranslator(fullPrefix);
            },
        });

        return createTranslator();
    }

    // Get current language
    export function getCurrentLanguage(): string {
        return i18next.language || detectLanguage();
    }

    // Change language dynamically without reload
    export async function changeLanguage(lng: string): Promise<void> {
        if (!SUPPORTED_LANGS.includes(lng)) {
            console.warn(`Language ${lng} is not supported`);
            return;
        }

        // Load all namespaces for the new language
        const namespaces = Array.from(registeredNamespaces);
        const resources: Record<string, Record<string, string>> = {};

        try {
            for (const ns of namespaces) {
                const translations = await loadTranslations(lng, ns);
                resources[ns] = translations;
            }

            // Add resources to i18next for the new language
            for (const ns of namespaces) {
                if (!i18next.hasResourceBundle(lng, ns)) {
                    i18next.addResourceBundle(lng, ns, resources[ns]);
                }
            }

            // Change language in i18next
            await i18next.changeLanguage(lng);

            // Update localStorage and HTML lang attribute
            localStorage.setItem(LANG_KEY, lng);
            document.documentElement.setAttribute("lang", lng);

            console.log(`Language changed to ${lng}`);
        } catch (error) {
            console.error(`Error changing language to ${lng}:`, error);
            // Fallback to reload if dynamic change fails
            localStorage.setItem(LANG_KEY, lng);
            window.location.reload();
        }
    }

    export default i18next;

    // ============================================================================
    // React Hooks for Translation
    // ============================================================================

    export interface ReactTranslationFunction {
        (key: string, params?: Record<string, string | number>): string;
        scope: (prefix: string) => ReactTranslationFunction;
    }

    /**
     * Create a translation hook factory for a specific namespace
     * Call this at MODULE level (outside component) to register namespace before render
     * 
     * Usage:
     *   // At module level (top of file)
     *   const useAppTranslation = createTranslationHook("index");
     * 
     *   // Inside component
     *   function App() {
     *       const t = useAppTranslation();
     *       return <h1>{t("title")}</h1>;
     *   }
     */
    export function createTranslationHook(namespace: string) {
        // Register namespace immediately at module level
        translation(namespace);
        
        // Return a hook that uses this namespace
        return () => useTranslation(namespace);
    }

    /**
     * Hook to get a reactive translation function
     * Only the text changes, not the entire component
     * 
     * For better DX, use createTranslationHook() instead
     * 
     * Usage: 
     *   const t = useTranslation("namespace");
     *   const t = useTranslation("namespace").scope("section");
     */
    export function useTranslation(namespace?: string): ReactTranslationFunction {
        // Auto-register namespace (backup if not using createTranslationHook)
        if (namespace) {
            translation(namespace);
        }

        // Trigger re-render when language changes
        const [, setLanguage] = useState(i18next.language || "");

        useEffect(() => {
            const handleLanguageChange = (lng: string) => {
                setLanguage(lng);
            };

            // Subscribe to language change events
            i18next.on("languageChanged", handleLanguageChange);

            // Cleanup subscription on unmount
            return () => {
                i18next.off("languageChanged", handleLanguageChange);
            };
        }, []);

        // Create translation function with scope support
        const createTranslator = (prefix: string = ""): ReactTranslationFunction => {
            const translator = ((key: string, params?: Record<string, string | number>) => {
                const fullKey = prefix ? `${prefix}.${key}` : key;
                const options = namespace ? { ns: namespace, ...params } : params;
                return i18next.t(fullKey, options);
            }) as ReactTranslationFunction;

            translator.scope = (newPrefix: string) => {
                const fullPrefix = prefix ? `${prefix}.${newPrefix}` : newPrefix;
                return createTranslator(fullPrefix);
            };

            return translator;
        };

        return createTranslator();
    }
```

Create a green`languages` folder in the root folder with this structure:
:::connector
    - 📂 languages
        :::connector
            - 📂 es
                :::connector
                    - 📂 src
                        :::connector
                            - 📂 index
                                :::connector
                                    - ... Here you can organize .js files with the translations that you can then import from the main file (like index.js in this case)
                                    - 📁 components
                                        :::connector
                                            - 📄 component.js
                                        :::
                                :::
                            - ... Other translation folders
                        :::
                    - 📄 index.js
                    - ... Other translation files
                :::
            - 📁 en
            - 📁 de
            - ... Others languages folder
        :::
:::

The translation files must have this structure:
```js
    import {component} from "./src/index/components/component.js";

    export const index = {
        language: "language",
        english: "English",
        spanish: "Spanish",
        component: component, // If you import this way you can then use the .scope("component") function to access the content of component.js
    };
```
:::details -compact Example of the translation content of a component
    ```javascript
        // languages/es/src/index/components/component.js
        export const component = {
            componentName: "Hola soy un componente",
            textTittle: "Titulo",
            // ... other translations
        };
    ```
:::


Create green`components/languageSelector.tsx` inside the contents folder of the HTML to have a language selector and inside put:
```typescript -folded
    import { changeLanguage, AVAILABLE_LANGUAGES, getCurrentLanguage, useTranslation } from "../../utils/languages.ts";

    export default function LanguageSelector() {
        // This ensures the selector updates when language changes
        useTranslation();
        const language = getCurrentLanguage();

        return (
            <div className="language-selector">
                <select
                    value={language}
                    onChange={(e) => changeLanguage((e.target as HTMLSelectElement).value)}
                >
                    {AVAILABLE_LANGUAGES.map((lang) => (
                        <option key={lang.code} value={lang.code}>
                            {lang.name}
                        </option>
                    ))}
                </select>
            </div>
        );
    }
```

To use the languages in the html you must first add the blue`LanguageSelector` component in the green`App.tsx` of an html to be able to change the language and try:
```typescript
    // Import the LanguageSelector.tsx component and createTranslationHook
    import LanguageSelector from "./components/languageSelector.tsx";
    import { createTranslationHook } from "../utils/languages.ts";

    // Create the translation hook at module level (outside the component)
    // This auto-registers the namespace before React renders, preventing flickering
    const useAppTranslation = createTranslationHook("index"); // "index" must match the name of your translation file (index.js)
    
    // Call to the language selector
    function App() {
        // Use the hook inside the component
        const t = useAppTranslation();

        return (
            <>
                <LanguageSelector />
                <span>{t("language") /* This will show the text from the "language" key in languages/<selected_language>/index.js */}</span>
                {/* Separate content */}
            </>
        );
    }
    
    export default App;
```

:::details -compact Example of how translation works in a component with scoping
    ```typescript
        import { createTranslationHook } from "../../utils/languages.ts";

        // Create translation hook at module level for "index" namespace
        const useComponentTranslation = createTranslationHook("index");

        export default function Component() {
            // Use the hook and scope it to access nested translations
            const t = useComponentTranslation().scope("component");
            // You can chain multiple scopes: .scope("component").scope("subsection").scope("item")
            // This allows accessing deeply nested translations in your index.js file

            return (
                <div>
                    {t("componentName") /* Accesses index.component.componentName */}
                </div>
            );
        }
    ```
:::


In the **HTML** that calls green`App.tsx` you must initialize the languages for it to work, for that you must put:
```html
    <!-- Other HTML -->
    <div id="root" style="height: 100%; width: 100%; position: absolute;"></div>
    <script type="module">
        // Others import like App from "./src/index/App.tsx"

        import { initLanguages } from "./src/utils/languages.ts"; // Import initLanguages

        // Initialize languages before rendering the application
        // This prevents flickering (showing translation keys before actual text)
        const root = createRoot(document.getElementById("root"));
        initLanguages().then(() => {
            root.render(
                React.createElement(React.StrictMode, null, React.createElement(App))
            );
        });
    </script>
    <!-- Other HTML -->
```

### Advanced Features

:::connector
    #### Interpolation (Variables in translations)

    You can use variables in your translations:

    ```javascript
        // In your translation file (e.g., languages/en/index.js)
        export const index = {
            greeting: "Hello, {{name}}!",
            itemCount: "You have {{count}} items",
        };
    ```

    ```typescript
        // In your component
        import { createTranslationHook } from "../utils/languages.ts";

        const useAppTranslation = createTranslationHook("index");

        function App() {
            const t = useAppTranslation();
            const userName = "John";
            const items = 5;

            return (
                <>
                    <p>{t("greeting", { name: userName })}</p>
                    <p>{t("itemCount", { count: items })}</p>
                </>
            );
        }
    ```


    #### Dynamic Language Change (No Page Reload)

    The system supports changing languages without reloading the page. When you use `changeLanguage()`, all components using translation hooks will automatically re-render with the new language:

    ```typescript
        import { changeLanguage } from "../utils/languages.ts";

        // Change language dynamically
        await changeLanguage("es"); // Components will re-render automatically
    ```


    #### Browser Language Detection

    The system automatically detects the user's browser language on first visit:
    - Checks localStorage for saved preference
    - Falls back to browser's preferred language (`navigator.language`)
    - Falls back to English if browser language is not supported


    #### Error Handling & Fallbacks

    The translation system includes robust error handling:
    - If a translation file is missing, it automatically falls back to English
    - If a translation key is missing, i18next shows the key itself
    - Console warnings help debug missing translations during development
:::











COMO PONER UN SELECTOR DE TEMA