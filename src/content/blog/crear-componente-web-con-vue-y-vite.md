---
author: aportela
pubDatetime: 2024-03-04T11:17:27.391Z
title: Cómo crear un componente web con vue y vite
slug: crear-componente-web-con-vue-y-vite
featured: false
draft: false
tags:
  - es
  - desarrollo
  - javascript
  - typescript
  - vue
  - vite
  - npm
description: Aprenderemos a crear un componente web con vue+vite que nos permita incrustarlo en una web existente de un modo fácil (muy útil para pequeños "apaños" en webs/plataformas de código "legacy" en los que no queremos migrar la plataforma completa a un desarrollo completo)
---

## Table of contents

## Requisitos

[Vite](https://vitejs.dev/) - Una herramienta para desarrollar/empaquetar fácilmente nuestro código

[Vue](https://vuejs.org/) - Un framework Javascript en el que programaremos el propio componente

## Instalación de vite (y vue)

```
$ npm create vite@latest
```

Nos saldrá un menú interactivo en el que teclearemos un nombre para el nuevo proyecto, elegiremos como framework vue y opcionalmente (aunque recomendado) typescript como extensión del lenguaje javascript

```
√ Project name: ... vue-module-test
√ Select a framework: » Vue
√ Select a variant: » TypeScript

Scaffolding project in /home/aportela/documents/github/vue-module-test...

Done. Now run:

  cd vue-module-test
  npm install
  npm run dev

```

Entramos en el nuevo directorio del proyecto, instalamos (npm install) las dependencias y lanzamos el servidor web de pruebas con la configuración por defecto

```
cd vue-module-test
```

```
$ npm install

added 45 packages, and audited 46 packages in 17s

5 packages are looking for funding
  run `npm fund` for details

found 0 vulnerabilities
```

## Modificaciones para la creación de nuestro módulo

Partiremos del código de ejemplo que incluye el propio proyecto con el componente existente bajo la ruta **./components/HelloWorld.vue**

Eliminamos los archivos src/App.vue y src/style.css

En el archivo index.html de la raiz del proyecto reemplazamos la línea la línea que inicializaría la aplicación vue genérica de vite:

```
<div id="app"></div>
```

Por esto otro:

```
<wc-hello-world msg="Vite + Vue" />
```

Reemplazamos el contenido del archivo src/main.ts

```
import { createApp } from 'vue'
import './style.css'
import App from './App.vue'

createApp(App).mount('#app')
```

Por esto otro:

```
import { defineCustomElement } from 'vue'

import wcHelloWorld from './components/HelloWorld.vue'

const wcHelloWorldComponent = defineCustomElement(wcHelloWorld)

customElements.define('wc-hello-world', wcHelloWorldComponent)
```

Para ver los cambios en tiempo real según vamos haciendo modificaciones podemos lanzar la lína de comandos que ejecuta un servidor web del siguiente modo:

```
$ npm run dev

VITE v5.1.4  ready in 271 ms

  ➜  Local:   http://localhost:5173/
  ➜  Network: use --host to expose
  ➜  press h + enter to show help
```

Una vez que tenemos una versión estable podemos construir un paquete con la siguiente línea de comandos:

```
$ npm run build

> vue-module-test@0.0.0 build
> vue-tsc && vite build

vite v5.1.4 building for production...
✓ 12 modules transformed.
dist/index.html                  0.48 kB │ gzip:  0.31 kB
dist/assets/index-Bdptthfp.css   0.04 kB │ gzip:  0.06 kB
dist/assets/index-8Nom4_ZL.js   56.49 kB │ gzip: 22.77 kB
✓ built in 486ms
```

Al finalizar tendremos la versión de producción en la ruta **./dist**

## Configuraciones de empaquetado "extra"

Si no queremos que al generar los archivos con cada build se les añada un sufijo "hash" en el nombre (ejemplo: index-aSu_kNFm.js) en el archivo del directorio raiz vite.config.ts añadimos una directiva

```
build: {
  rollupOptions: {
    output: {
      entryFileNames: `assets/[name].js`,
      chunkFileNames: `assets/[name].js`,
      assetFileNames: `assets/[name].[ext]`
    }
  }
}
```

quedando tal que así:

```
import { defineConfig } from 'vite'
import vue from '@vitejs/plugin-vue'

// https://vitejs.dev/config/
export default defineConfig({
  plugins: [vue()],
  build: {
    rollupOptions: {
      output: {
        entryFileNames: `assets/[name].js`,
        chunkFileNames: `assets/[name].js`,
        assetFileNames: `assets/[name].[ext]`
      }
    }
  }
})
```

Si quisieramos que el código común de las librerías (vue) ajenas a nuestro plugin fuera "separado" en otro archivo (vendor) podríamos hacerlo incluyendo el plugin splitVendorChunkPlugin en el archivo de configuracion vite.config.ts quedando tal que así:

```
import { defineConfig, splitVendorChunkPlugin } from 'vite'
import vue from '@vitejs/plugin-vue'

// https://vitejs.dev/config/
export default defineConfig({
  plugins: [vue(), splitVendorChunkPlugin()],
  build: {
    rollupOptions: {
      output: {
        entryFileNames: `assets/[name].js`,
        chunkFileNames: `assets/[name].js`,
        assetFileNames: `assets/[name].[ext]`
      }
    }
  }
})
```
