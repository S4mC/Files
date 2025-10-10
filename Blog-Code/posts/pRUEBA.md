# Create a Preact Web App with Parcel (Deprecated xd)

## Herramientas
- Parcel:
    Ayuda a compilar el código, minificarlo, juntar los módulos de node.
    Instalación:
     bash`npm install --save-dev parcel`
- Preact:
    Ayuda a mantener el código en componentes.
    Instalación:
    ```bash
        npm init -y
        npm install preact
    ```
- Htm:
    Ayuda a escribir el HTML de los componentes sin usar JSX, usando template strings
    Instalación:
     bash`npm install preact htm`


## Configuración
1. Instala las herramientas
2. Modifica el package.json
    Quita la linea:
    ```json
        "main": "main.js",
    ```
    Añade los script para iniciar el servidor y hacer el build:
    ```json
        "scripts": {
            "dev": "parcel serve index.html",
            "build": "parcel build index.html"
        },
    ```

