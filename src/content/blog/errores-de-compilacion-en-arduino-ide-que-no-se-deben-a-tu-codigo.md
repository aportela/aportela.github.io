---
author: aportela
pubDatetime: 2024-06-07T20:11:14.121Z
title: Errores de compilación en Arduino IDE que no (siempre) se deben a tu código
slug: errores-de-compilacion-en-arduino-ide-que-no-se-deben-a-tu-codigo
featured: false
draft: false
tags:
  - arduinoide
  - windows10
  - c
  - cplusplus
  - microcontroladores
description: Razones por las que tu código puede estar correcto pero no compilar correctamente en Arduino IDE
---

## Table of contents

## Introducción

Durante mi reciente periplo por el mundillo de los microcontroladores y en gran parte de las ocasiones programando con Arduino IDE me he revanado los sesos preguntandome que rayos estaba pasando porque mi código estaba bien (repasándolo decenas de veces e incluso ya a la desesperada consultando con chatgpt a falta de otra persona real con la que comentar la jugada) pero la compilación fallaba.

Revisando el error que sale por consola a primera vista parece deberse a una múltiple definición de alguna entidad

> multiple definition of `TU_CLASE'; ...: first defined here

# Causas

Lo primero que piensas **si no te fijas bien** es: "rayos, en algún lado debo tener alguna entidad mal declarada o duplicada" pero por más que buscas, los archivos .h y .cpp (o .c) están correctamente. La clave reside en leer el mensaje anterior completo (yo he recortado y puesto "..." en el ejemplo de arriba, pero ahí hay unas rutas que indican que el linker (ld) está evaluando cosas raras). Al final tirando del hilo ves que el problema viene de unos archivos en la ruta de los temporales que usa Arduino IDE para tu proyecto.

En mi caso (Windows 10) residen bajo (cada proyecto tiene su propia ruta, esa cadena hexadecimal del final)

> c:\Users\xxxx\AppData\Local\Temp\arduino\sketches\0EB5C63599850CA23359877640E4269A\

¿ Cómo se genera el problema ? (en mi caso al menos): en ocasiones refactorizando/renombrando clases / archivos por alguna razón estos temporales no se deben estar actualizando "bien" y debe quedar "basurilla antigua" que hace que en la compilación actual se encuentren símbolos duplicados (los que tienes en la última versión de tu código pero tambien otros que en mi caso corresponden a versiones anteriores y no deberían existir).

## Solución

Tan sólo hay que eliminar todo lo que existe en esa ruta temporal y en la próxima compilación se volverá a generar a partir del código actual sin ningún problema (puede que tarde algo más).
