# Web App
Create a Preact Web App with Vite

## Tools
- Vite:
    It helps to compile the code, minify it, put together the node modules.
    run:
     bash `npm create vite@latest my-preact-htm-site -- --template vanilla`
- Preact:
    Helps keep code in components.
    run:
    ```bash
        npm init -y
        npm install preact
    ```
- Htm:
    Helps write the HTML of the components without using JSX, using template strings
    run:
     bash `npm install preact htm`


## Configuration
1. Install the tools, in Vite create a vanilla JS proyect
2. Create green`vite.config.js` in the root folder for compile all html and put relative paths
    ```javascript
        import { defineConfig } from "vite";
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

        export default defineConfig({
            base: "./", // Use relative paths in build output
            build: {
                rollupOptions: {
                    input,
                },
            },
        });
    ```
3. Create green`commands.md` in the root folder for save and run the commands:
    ```markdown
        Commands in the proyect:
        run `npx vite`(Run preview)
        run `npx vite build`(Build)
    ```
4. Create green`util.js` in the purple`components` folder for easy access to tools:
    ```javascript
        // utils.js - Common utilities for Preact with HTM
        import { h } from 'preact';
        import htm from 'htm';
        import { useState, useEffect, useRef } from 'preact/hooks';
        import { render } from 'preact';

        // html - To use JSX syntax with htm
        const html = htm.bind(h);

        // querySelector
        const $ = (selector) => document.querySelector(selector);

        export default html;
        export { html, $, useState, useEffect, useRef, render };
    ```
5. Create or edit green`style.css` in the purple`styles` folder for a global style and themes:
    ```javascript
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
6. Modify the green`index.html` and put:
    ```html 
        <!DOCTYPE html>
        <html lang="en">
            <head>
                <meta charset="UTF-8" />
                <link rel="icon" type="image/svg+xml" href="/icon.svg" />
                <meta name="viewport" content="width=device-width, initial-scale=1.0" />
                <title>Title</title>
            </head>
            <body>
                <div id="app"></div>
                <!-- You can also import the js code from another file -->
                <!-- <script type="module" src="/src/main.js"></script> -->
                <script type="module">
                    import "./styles/style.css";
                    import { html, $, render } from "./components/utils.js";
                    import Counter from './components/counter.js';
                    function App() {
                        return html`
                            <h1>Hello world</h1>
                            <${Counter} />
                        `;
                    }
                    render(html`<${App} />`, $("#app"));
                </script>
            </body>
        </html>
    ```
    The green`counter.js` component used is:
    ```js
        import {html, useState } from './utils.js';

        export default function Counter() {
        const [count, setCount] = useState(0);
        return html`
            <div class="counter">
            <p>Contador: ${count}</p>
            <button onClick=${() => setCount(count + 1)}>+1</button>
            </div>
        `;
        }
    ```
7. If you are going to use github create green`.gitignore` in the root folder and put:
    ```markdown -min
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
9. AÑADIR COMO PONER UN SELECTOR DE IDIOMA Y DE TEMA

