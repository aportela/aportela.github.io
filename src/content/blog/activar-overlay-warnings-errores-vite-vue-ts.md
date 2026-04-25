---
author: aportela
pubDatetime: 2026-04-25T19:41:32.000Z
title: Cómo activar el overlay web de advertencias/errores en un entorno vite/vue/typescript
slug: activar-overlay-advertencias-errores-vite-vue-ts
featured: false
draft: false
tags:
  - vite
  - vuejs
  - typescript
  - quasarjs
description: Pasos a seguir para tener un overlay web de advertencias/errores similar al del framework QuasarJS
---

## Table of contents

## Creación del proyecto de prueba

Vamos a crear un proyecto de prueba en el que haremos unas modificaciones para generar advertencias y se muestren estos en el nuevo overlay

```shell
npm create vite@latest
```

Saldra un pequeño asistente en el que introduciremos un nombre de proyecto, seleccionaremos el framework vue con typescript y le diremos que no queremos instalar e iniciar aún.

```
> npx
> create-vite

│
◇  Project name:
│  test-overlay
│
◇  Select a framework:
│  Vue
│
◇  Select a variant:
│  TypeScript
│
◇  Install with npm and start now?
│  No
│
◇  Scaffolding project in C:\temp\test-overlay...
│
└  Done. Now run:

  cd test-overlay
  npm install
  npm run dev
```

Ejecutamos los 2 primeros comandos para instalar las dependencias principales:

```shell
  cd test-overlay
  npm install
```

## Instalación de las dependencias extra requeridas

A continuación necesitaremos instalar el plugin checker de vite como dependencia para desarrollo

```shell
npm i vite-plugin-checker --save-dev
```

## Configuraciones requeridas

Ahora necesitaremos modificar el archivo **vite.config.ts**

Así estaba originalmente:

```js
import { defineConfig } from "vite";
import vue from "@vitejs/plugin-vue";

// https://vite.dev/config/
export default defineConfig({
  plugins: [vue()],
});
```

Añadiremos la configuracion requerida por este nuevo plugin, quedando algo asi una vez modificado:

```js
import { defineConfig } from "vite";
import vue from "@vitejs/plugin-vue";
import checker from "vite-plugin-checker";

// https://vite.dev/config/
export default defineConfig({
  plugins: [
    vue(),
    checker({
      typescript: {
        tsconfigPath: "./tsconfig.app.json",
      },
      vueTsc: {
        tsconfigPath: "./tsconfig.app.json",
      },
    }),
  ],
});
```

Y ahora viene la razón del porqué escribo esto, yo inicialmente en los **tsconfigPath** estaba poniendo el valor **"./tsconfig.json"** que en mi caso contenía esto:

```js
{
  "files": [],
  "references": [
    { "path": "./tsconfig.app.json" },
    { "path": "./tsconfig.node.json" }
  ]
}
```

Es como un wrapper que envuelve/incluye la configuracion de app y node, pero haciéndolo así no se resolvían las referencias de las rutas correctamente, por lo que finalmente tiene el valor de app (**"./tsconfig.app.json"**)

El archivo tsconfig.app.json no he tenido que tocarlo (aunque podríamos modificar aqui si queremos más o menos cosas a tener en cuenta en el linting que muestren/oculten detalles), contiene esto:

```js
{
  "extends": "@vue/tsconfig/tsconfig.dom.json",
  "compilerOptions": {
    "tsBuildInfoFile": "./node_modules/.tmp/tsconfig.app.tsbuildinfo",
    "types": ["vite/client"],

    /* Linting */
    "noUnusedLocals": true,
    "noUnusedParameters": true,
    "erasableSyntaxOnly": true,
    "noFallthroughCasesInSwitch": true
  },
  "include": ["src/**/*.ts", "src/**/*.tsx", "src/**/*.vue"]
}
```

A mayores, necesitaremos añadir en la ruta src/ un archivo **shims-vue.d.ts** ya que vue no proporciona tipos por defecto para los archivos con extensión .vue en proyectos TypeScript con este contenido:

```js
declare module "*.vue" {
  import { DefineComponent } from 'vue'
  const component: DefineComponent<{}, {}, any>
  export default component
}
```

## Inicio del servidor de pruebas

Una vez realizados todos los pasos iniciaremos el servidor web de desarrollo:

```shell
npm run dev
```

Saldrá un conjunto de información que nos indicará la URL del servidor de desarrollo:

```
> test-overlay@0.0.0 dev
> vite


  VITE v8.0.10  ready in 245 ms

  ➜  Local:   http://localhost:5173/
  ➜  Network: use --host to expose
  ➜  press h + enter to show help

[TypeScript] Found 0 errors. Watching for file changes.

[vue-tsc] Found 0 errors. Watching for file changes.

```

Abriremos un navegador en la URL indicada y veremos la página de ejemplo por defecto, sin errores ni advertencias de ningún tipo.

## Modificación del código fuente actual para generar problemas y verlos en el overlay web

Ahora procederemos a modificar el código fuente generando algún tipo de problema para ver si el overlay lo muestra. Para ello por ejemplo bajo la ruta src/ modificaremos el archivo App.vue

Contenido original:

```js
<script setup lang="ts">
import HelloWorld from './components/HelloWorld.vue'
</script>

<template>
  <HelloWorld />
</template>
```

Contenido modificado

```js
<script setup lang="ts">
  import HelloWorld from './components/HelloWorld.vue'
  import { ref } from 'vue';

  visible = true;

</script>

<template>
  <HelloWorld />
</template>
```

Hemos añadido un import extra del **ref** de vue y una sentencia de asignación del valor **true** en visible, ambos van a generar advertencias que deberíamos ver en un overlay de la página web del navegador por encima de la página de ejemplo con un texto similar a este:

```
 [vue-tsc] 'ref' is declared but its value is never read.

/TEMP/test-overlay/src/App.vue:3:3

    1 | <script setup lang="ts">
    2 |   import HelloWorld from './components/HelloWorld.vue'
  > 3 |   import { ref } from 'vue';
      |   ^^^^^^^^^^^^^^^^^^^^^^^^^^
    4 |
    5 |   visible = true;
    6 |

[vue-tsc] Cannot find name 'visible'.

/TEMP/test-overlay/src/App.vue:5:3

    3 |   import { ref } from 'vue';
    4 |
  > 5 |   visible = true;
      |   ^^^^^^^
    6 |
    7 | </script>
    8 |
```

De este modo resulta mucho más sencillo durante el desarrollo ir arreglando advertencias y errores que se nos vayan colando en el código.

Podemos ver que al modificar de nuevo el archivo con el siguiente contenido de ejemplo corregido, el overlay de advertencias desaparece al no encontrar ningún problema:

```js
<script setup lang="ts">
  import HelloWorld from './components/HelloWorld.vue'
  import { ref } from 'vue';

  const visible = ref(true);

</script>

<template>
  <HelloWorld v-if="visible" />
</template>
```
