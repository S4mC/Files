# Create a Preact Web App with Vite

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
2. Create green`util.js` for easy access to tools:
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
3. Create or edit green`style.css` for a global style and themes:
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
4. AÑADIR COMO PONER UN SELECTOR DE IDIOMA Y DE TEMA

