---
author: aportela
pubDatetime: 2024-05-24T21:58:22.102Z
title: Optimizando código para microcontroladores (parte 1 - cadenas de caracteres)
slug: optimizando-codigo-para-microcontroladores-parte-1-cadenas-caracteres
featured: false
draft: false
tags:
  - es
  - c
  - c++
  - microcontroladores
  - esp32
description: Algunas anotaciones a tener en cuenta sobre optimizaciones de código cuando trabajamos con cadenas de caracteres en microcontroladores
---

## Introducción

Recientemente me he auto-introducido en el mundo de los microcontroladores (arduino / esp8266 / esp32 / raspberry pi pico...) y debido a un pequeño proyecto que tengo en marcha me veo obligado a intentar que el código sea lo más eficiente posible ya que consta de una parte que gestiona y evalua la lógica y otra que se dedica a dibujar/pintar en una pequeña pantalla ciertas cosas relacionadas con esta lógica (todo dentro del típico bucle).

Evidentemente si una de las partes tarda en procesarse más de lo normal, la otra se verá afectada. Hablando claro: si los cálculos / procesamiento de la lógica inicial tardan 2 segundos en ejecutarse, el refresco de pantalla que viene despues no se hará hasta pasados esos 2 segundos. Lo deseable a priori sería poder tener un refresco de pantalla de 30 fps osea que el loop de "procesamiento/logica" + "repintado de pantalla" debería ejecutarse un mínimo de 30 veces por segundo.

En esta primera parte me voy a centrar en la optimización del procesamiento / lógica (en posteriores partes quizás haga otros apuntes sobre como optimizar el redibujado).

En mi proyecto, la mayor parte de la lógica principal se establece en base a unas lecturas y escrituras de datos (mayormente cadenas) a través del puerto serie, para entendernos y poniendo un ejemplo que todos puedan entender: envío una cadena "TEMPERATURA;" (solicito leer la temperatura) y recibo una cadena "TEMPERATURA120;" (me responden que el valor de la temperatura es 120 grados), hay varios tipos de comandos (TEMPERATURA/VELOCIDAD...) y cada uno con una estructura propia de valores de respuesta. Como me interesa trabajar en un entorno "en tiempo real" (lo más aproximado posible, claro) se envian/reciben decenas o cientos de estos comandos (en cadenas de caracteres) que deben ser tratados.

Debido a que en Arduino IDE por defecto se usa C++ para programar y todo el tema de cadenas en C++ se encapsula en la clase String (para facilitar todo el uso en comparación con C) se me ocurrió que quizás por aquí podría "arañar algun ciclo de CPU", al fin y al cabo, cada capa de abstracción siempre añade un coste extra.

Voy a explicar con dos ejemplos (en C++ y C) a lo que me refiero:

**C++**

```
void setup() {
  Serial.begin(9600);
  // variables que almacenan diferentes valores que podemos recibido en la respuesta (para diferenciarlos)
  String RespuestaOK = "OK;";
  String RespuestaError = "ERROR;";

  // variable que almacena la respuesta real
  String RespuestaRecibida;

  // ...
  // aqui en realidad nunca asignaríamos así sinó que el valor lo obtendríamos de otro lado
  // ...
  RespuestaRecibida = "OK;";

  if (RespuestaRecibida == RespuestaOK) {
    Serial.println("OK");
  } else if (RespuestaRecibida == RespuestaError) {
    Serial.println("ERROR");
  } else {
    Serial.println("OTRO TIPO");
  }
}

void loop() {
  delay(10);
}
```

**C**

```
void setup() {
  Serial.begin(9600);
  // variables que almacenan diferentes valores de respuesta
  const char *RespuestaOK = "OK;";
  const char *RespuestaError = "ERROR;";

  // variable que almacena la respuesta real
  char RespuestaRecibida[20];

  // ...
  // aqui en realidad nunca asignaríamos así sinó que el valor lo obtendríamos de otro lado
  // ...
  strcpy(RespuestaRecibida, RespuestaOK);

  if (strcmp(RespuestaRecibida, RespuestaOK) == 0) {
    Serial.println("OK");
  } else if (strcmp(RespuestaRecibida, RespuestaError) == 0) {
    Serial.println("ERROR");
  } else {
    Serial.println("OTRO TIPO");
  }
}

void loop() {
  delay(10);
}
```

Como veis en C++ el código es mas simple y facil de entender ya que los operadores de asignación (=) y comparación (==) son "directos", a diferencia de C, que tenemos que usar "funciones intermedias" para todo este tratamiento, por no hablar claro de que en C++ las funciones pueden devolverte una cadena (tipo String) pero para hacer esto en C tienes que pasar como parámetro un puntero a la cadena lo cual puede hacerlo mas "feo/complejo" para gente que no esté acostumbrada al lenguage C.

Aquí la cuestión principal que me traía de cabeza es si trabajar con la clase String de C++ sería menos eficiente que usar arrays de caracteres en C con funciones intermedias además de posibles punteros para procesar todo esto ya que en pocas operaciones por segundo la diferencía podría ser nimia pero en decenas o miles la cosa podría tener más relevancia. En mi proyecto se envian comandos (en cadenas) cada pocos ms vía puerto serie y se reciben respuestas que se también se asignan a cadenas (que luego deben ser evaluadas y/o comparadas).

Para evaluar todo esto he creado unos proyectos en C++ y C que ejecutan una serie de benchmarks con el propósito de ver cual era más eficiente.

## Benchmark 1: Asignación y comparación exacta de cadenas

### Lenguaje C++

```
void setup() {
  Serial.begin(9600);
  while (!Serial) {
    yield();
    delay(10);
  }

  String RespuestaCorrecta = "OK";
  unsigned long startTime = millis();
  unsigned long count = 0;
  unsigned long duration = 1000;

  while (millis() - startTime < duration) {
    String RespuestaRecibida = "OK";
    if (RespuestaRecibida == RespuestaCorrecta) {
      count++;
    }
  }

  Serial.printf("Nº de asignaciones/comparaciones evaluadas: %u (tipo String y operador ==)\n", count);
}

void loop() {
}
```

#### Resultado compilación:

```
Sketch uses 262545 bytes (20%) of program storage space. Maximum is 1310720 bytes.
Global variables use 21400 bytes (6%) of dynamic memory, leaving 306280 bytes for local variables. Maximum is 327680 bytes.
```

#### Resultado ejecución:

```
Nº de asignaciones/comparaciones evaluadas: 302840 (tipo String y operador ==)
```

### Lenguaje C

```
#include <string.h>

void setup() {
  Serial.begin(9600);
  while (!Serial) {
    yield();
    delay(10);
  }

  const char *RespuestaCorrecta = "OK";
  unsigned long startTime = millis();
  unsigned long count = 0;
  unsigned long duration = 1000;

  while (millis() - startTime < duration) {
    char RespuestaRecibida[20];
    strcpy(RespuestaRecibida, RespuestaCorrecta);
    if (strcmp(RespuestaRecibida, RespuestaCorrecta) == 0) {
      count++;
    }
  }

  Serial.printf("Nº de asignaciones/comparaciones evaluadas evaluadas: %u (tipo char[] y función strcmp)\n", count);
}

void loop() {
}
```

#### Resultado compilación:

```
Sketch uses 262285 bytes (20%) of program storage space. Maximum is 1310720 bytes.
Global variables use 21400 bytes (6%) of dynamic memory, leaving 306280 bytes for local variables. Maximum is 327680 bytes.
```

#### Resultado ejecución:

```
Nº de asignaciones/comparaciones evaluadas evaluadas: 751663 (tipo char[] y función strcmp)
```

## Benchmark 2: Asignación y comparación parcial de cadenas

### Lenguaje C++

```
void setup() {
  Serial.begin(9600);
  while (!Serial) {
    yield();
    delay(10);
  }

  String str1 = "cadena_de_prueba";
  String str2 = "cadena_de";
  unsigned long startTime = millis();
  unsigned long count = 0;
  unsigned long duration = 1000;

  while (millis() - startTime < duration) {
    String RespuestaRecibida = str2;
    if (str1.startsWith(RespuestaRecibida)) {
      count++;
    }
  }

  Serial.printf("Nº de asignaciones/comparaciones evaluadas: %u (tipo String y método startsWith)\n", count);
}

void loop() {
}
```

#### Resultado compilación:

```
Sketch uses 262649 bytes (20%) of program storage space. Maximum is 1310720 bytes.
Global variables use 21400 bytes (6%) of dynamic memory, leaving 306280 bytes for local variables. Maximum is 327680 bytes.
```

#### Resultado ejecución:

```
Nº de asignaciones/comparaciones evaluadas: 220637 (tipo String y método startsWith)
```

### Lenguaje C

```
#include <string.h>

void setup() {
  Serial.begin(9600);
  while (!Serial) {
    yield();
    delay(10);
  }

  const char *str1 = "cadena_de_prueba";
  const char *str2 = "cadena_de";
  unsigned long startTime = millis();
  unsigned long count = 0;
  unsigned long duration = 1000;

  while (millis() - startTime < duration) {
    char RespuestaRecibida[20];
    strcpy(RespuestaRecibida, str2);
    if (strncmp(RespuestaRecibida, str1, 9) == 0) {
      count++;
    }
  }

  Serial.printf("Nº de asignaciones/comparaciones evaluadas: %u (tipo char[] y función strncmp)\n", count);
}

void loop() {
}
```

#### Resultado compilación:

```
Sketch uses 262305 bytes (20%) of program storage space. Maximum is 1310720 bytes.
Global variables use 21400 bytes (6%) of dynamic memory, leaving 306280 bytes for local variables. Maximum is 327680 bytes.
```

#### Resultado ejecución:

```
Nº de asignaciones/comparaciones evaluadas: 497464 (tipo char[] y función strncmp)
```

### Conclusiones

#### Benchmark 1: Asignación y comparación exacta de cadenas

C se ejecuta aproximadamente el doble de rapido (751663) que C++ (302840) en estos ejemplos de asignación / comparación exacta. El uso en bytes de lo generado por C (program storage space) es algo menor (262285) también (si estais limitados) que lo generado por C++ (262545) aunque no hay tanta diferencia como en la velocidad de ejecución.

#### Benchmark 2: Asignación y comparación parcial de cadenas

C vuelve a ejecutarse aproximadamente el doble de rapido (497464) que C++ (220637) en estos ejemplos de asignación / comparación parcial. El uso en bytes de lo generado por C (program storage space) de nuevo es algo menor (262305) también con respecto a lo generado por C++ (262649).
