---
author: aportela
pubDatetime: 2024-01-22T07:46:22.166Z
title: Como actualizar la bios de un toshiba satellite L750-L755 sin una batería conectada
slug: actualizar-bios-toshiba-satellite-l750-l755-sin-bateria-conectada
featured: false
draft: false
tags:
  - es
  - bios
  - toshiba satellite
description: El proceso oficial de actualización de la bios requiere tener una batería conectada. A continuación se exponen los pasos para saltarse esta limitación.
---

## Table of contents

## Introducción y explicación del problema

Si eres como yo y reaprovechas ciertas piezas o componentes de hardware antiguo quizás te hayas encontrado con este problema.

Estaba intentando recuperar un antiguo portatil [Toshiba Satellite](https://www.notebookcheck.org/Toshiba-Satellite-L750-12N.59241.0.html) L750-L755 (i5 2410M con una tarjeta gráfica NVidia GT525M) y despues de instalar los drivers me dí cuenta de que el audio no funcionaba a través de la salida HDMI. Indagué en internet y acabe encontrando esto:

[Audio can not be heard when using an HDMI cable](https://support.dynabook.com/support/viewContentDetail?contentId=3337838)

```
Issue

    On computers configured with a NVIDIA N12P-GV graphics controller, there intermittently, will be no audio output to a device connected through the HDMI cable.
Resolution

    On the Satellite L650/L655/L750/L755 the issue was corrected in BIOS version 3.10. Update to BIOS version 3.10 or higher.

    On the Satellite (Pro) L730/L735 the issue was corrected in BIOS version 2.20. Update to BIOS version 2.20 or higher.

    One the Satellite (Pro) L740/L745 the issue was corrected in BIOS version 2.30. Update to BIOS version 2.30 or higher.
```

### Descarga / actualización de la bios

Puedes descargar aquí la última versión (3.60) de la bios BIOS:

https://support.dynabook.com/support/viewContentDetail?contentId=3483311

Por si acaso el link desaparece, dejo aquí una [copia local](../public/assets/blog/attachments/sk1wv360.exe).

Notas / lista de cambios

```
        The BIOS update will force the computer to shut down or restart. Please make sure to save all work in progress before starting BIOS updates.
        Power on the computer if it is off.
        While the "Toshiba" LOGO is displayed, press the F2 function key to start BIOS Setup.
        Check the version of BIOS and press the F9 function key then Enter to load setup defaults.
        Press the F10 function key then Enter to save settings and exit. The computer will automatically reboot.

    Change History

        Version 3.60 - 2012-06-26
            Implemented changes for the Windows 8 upgrade.
            Modified: DDR3 memory support.
        Version 3.50 - 2012-06-19
            Updated: Microcode.
            Updated: ME firmware V7.1.52.1176 to improve potential DVD/BD playback issues when using PAVP-enabled media.
        Version 3.40 - 2012-06-08
            Fixed: HWID cannot be read in fast boot mode.
            Fixed: Discrete (nVidia) VGA SKU Windows 8 black screen when resuming from Hibernation (S4).
            ASL code upgrades.
            Updated the EC to V2.90.
        Version 3.30 - 2012-05-24
            Fixed: System cannot auto log-on when waking up from Hi Speed Start mode.
            Fixed: Hang after a DOS BIOS flash update.
            EC V2.80: Added SMBus deadlock protection.
        Version 3.10 - 2012-02-10
            Modified: NVIDIA VGA brightness adjust sequence for an intermittent HDMI audio detection issue.
            Corrects an issue where, under specific conditions the BIOS update will corrupt the system BIOS and cause constant boot failures.
        Version 2.90 - 2011-11-21
            Updated: PPM RC from 1.3.1.1 to 1.4.0.
            Updated: 206A7 microcode from rev. 1B to rev. 23.
            Hangs intermittently when waking from Sleep (S3) mode.
            Fixed: 3-cell models do not enter Hibernation (S4).
        Version 2.70 - 2011-10-14
            Fixed: EC updated to correct a "keystroke loss" issue.
            Modified: SMLink1 Thermal Reporting Select and TRC register bits.
            Fixed: System does not RTC wake-up while in hibernation/soft-off (S4/S5) on battery mode.
            Fixed: System intermittently wakes up on its own in Hi-Speed Start mode when a LAN cable is connected.
            Updated: PPM RC from 1.3.1.1 to 1.4.0.
            Updated: 206A7 microcode from rev. 1B to rev. 23.
            Hangs intermittently when waking from Sleep (S3) mode.
            Fixed: 3-cell models do not enter Hibernation (S4).
        Version 2.50 - 2011-09-23
            Updated the 206A7 microcode from rev. 1A to rev. 1B.
        Version 2.40 - 2011-09-23
            Fixed: Toshiba Flash Cards do not function after resuming from hibernation.
            Fixed: File copy problem on some HDDs in AHCI mode.
        Version 2.30 - 2011-08-02
            Fixed: When "USB Legacy Emulation" is disabled and the wireless LAN (WLAN) is turned off using Fn+F8, the WLAN LED still lights.
            Fixed: "WebCamera" disappears when disabled.
            Updated: CPT RC to 1.2.0.
            Updated: 206A7 microcode from rev. 15 to rev. 1A.
            Updated: Hi-speed Start function.
            Updated: VerbTable - MaxxAudio LE support.
            Updated: Energy Star region rule.
            Added support for the Alarm Power On function.
            Updated: SA RC to 1.2.2.
            Updated: PPM RC to 1.3.1.1 - Fixed: i7-2670 CPU frequency does not always scale lower when the Dynamic CPU Freq. Mode in Setup is set to "Always Low".
            Updated: N12P-GV Video BIOS (VBIOS).
        Version 2.00 - 2011-06-16
            Updated the UMA VBIOS to 2104.
            Updated the N12M-GE & N12P-LP VBIOS.
            Updated the 206A7 microcode from rev. 14 to rev. 15.
            Intel Sandy Bridge Processor Errata #BK79 (Mobile).
            Updated SA RC to v1.2.0.
            Updated PPM RC to v1.2.1.
            Fixed: S3 will hang on 0x53 with Pentium Series SNB CPUs.
            Web camera settings were removed in the SCU on computers without Web cameras.
            Fixed: Hyper-Threading (HT) can't be disabled when core multi-processing is disabled.
            Updated the ME version to 7.1.10.1065.
            Added support for a new wireless/Bluetooth combo card - Intel Advanced-N1030.
            Hid the "Turbo boost" and "Virtualization technology" options when not supported by the CPU.
            Fixed: DMI type 0 error.
            Added support for the Hi-speed Start and PeakShift functions.
        Version 1.40 - 2011-03-17
            Fixed: Lid actions do not operate correctly.
            Note: Supports the Intel B3 stepping Cougar Point PCH.

```

### Ejecución y fallo de la actualización

![Menú de la aplicación para actualizar](@assets/images/blog/8/8fcf64c7-931e-4253-b516-820b64c02e3f.png)

Al descomprimir y ejecutar el instalador que actualiza la bios me encontré con el **PROBLEMA. NO FUNCIONA SI EL PORTATIL NO TIENE CONECTADA UNA BATERÍA** (y yo no disponía de una).

![Modal de aviso para insertar la batería requerida](@assets/images/blog/7/7a299aa8-73fc-43d0-9cac-758bc33d472e.png)

El programa no deja continuar y es imposible realizar la actualización de la bios a menos que conectemos una batería (con al menos el 20% de carga).

## Descripción del proceso

Mi primera aproximación fue intentar abrir el binario con un desensamblador e interceptar la rutina condicional que comprobaba el estado de la batería así que inicialmente (asumiendo que el binario principal podría estar programado en .net) descargué el [ILSpy](https://github.com/icsharpcode/ILSpy) para investigar algo más y me encontré con que internamente contenía un conjunto de directorios y archivos que posibilitaban el acceso a las herramientas de actualización (en consola). El propio ILSpy permite extraer a mano uno a uno estos archivos pero luego se me ocurrio que a lo mejor el 7-zip me permitia extraer/descomprimir todo esto de un modo mas fácil y efectivamente.

### Extraer archivos con 7-zip

![Ventana listado de archivos con 7-zip](@assets/images/blog/b/babe4732-8952-4ba5-9104-eb19a26f6dc7.png)

![Ventana para extraer los archivos con 7-zip](@assets/images/blog/1/1927f6a8-8a1e-4375-a98f-6151efbef961.png)

### Localizar y editar el archivo de configuración

Luego tan sólo tuve que navegar al directorio (en mi caso arquitectura x64) que contenía lo que parecían ser los binarios de consola de actualización.

![Ventana con ruta de archivos de la actualización](@assets/images/blog/a/a8800874-1772-4086-b3e7-dee81869f444.png)

Allí había un archivo **platform.ini** en el que se pueden personalizar ciertas cosas (incluidas las comprobaciones de batería). El proceso es tan simple como abrirlo con un editor de texto y modificar el valor de **BatteryCheck de 1 a 0**.

![Ventana del editor de texto con el archivo de configuración](@assets/images/blog/3/35689a88-3eb6-4d81-b925-1465c26d12fc.png)

### Ejecutar la actualización con el binario de línea de comandos

Finalmente tan solo resta ejecutar (en una consola con privilegios de administrador el archivo batch que inicia el proceso) y de este modo podremos proceder a actualizar la bios sin ningún problema.

![Ventana de línea de comandos a ejecutar para actualizar](@assets/images/blog/a/a99a438d-eb96-44c8-93dc-b2277a2afde5.png)

Se abrira una ventana con el software InsydeFlash que mostrará la versión actual de la bios y a la que vamos a actualizar. Cuando finalice el proceso reiniciará automaticamente el equipo y ya tendremos la bios actualizada.

## Conclusiones

> Si tu portatil es de otra marca/modelo el proceso probablemente será diferente pero a lo mejor puedes intentar una aproximación similar (buscar si los binarios de actualización reales estan contenidos de algun modo en alguno de los archivos de la actualización descargada). Sinó probablemente tendrás que recurrir a ingeniería inversa del binario (lo que iba a intentar yo inicialmente) y localizar la rutina condicional y eliminarla/desactivarla (lo cual ya no está al alcance de usuarios sin conocimientos avanzados de programación).
