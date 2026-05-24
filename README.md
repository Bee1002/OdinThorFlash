# OdinThorFlash

**Réplica de [Samsung-Loki/Thor](https://github.com/Samsung-Loki/Thor) para Windows con interfaz WPF.**

Thor original es una consola (.NET) con protocolo Odin completo en `TheAirBlow.Thor.Library`, pero **solo implementa USB en Linux**. Este proyecto usa **la misma librería de protocolo** (en `OdinThorFlash.Core`) y añade:

| Componente | Thor upstream | OdinThorFlash |
|------------|---------------|---------------|
| Protocolo Odin (LOKE, flash, PIT, reinicio) | `TheAirBlow.Thor.Library` | Igual en `OdinThorFlash.Core` |
| USB | Linux DevFS | **Windows** (`Platform/Windows.cs`, LibUsbDotNet + WinUSB/Zadig) |
| Interfaz | Consola `TheAirBlow.Thor.Shell` | **WPF** `OdinThorFlash` |

No es un “inspirado en” Odin genérico: es **el código Thor** adaptado a escritorio Windows.

## Requisitos

- Windows 10/11, .NET 8
- Teléfono Samsung en **modo Download** (VID `04E8`)
- Driver **WinUSB** en la interfaz CDC **0x0A** ([guía](docs/Instalar-WinUSB-Samsung.md))

## Compilar y ejecutar

```bash
dotnet build OdinThorFlash.sln -c Release
```

Ejecutable: `OdinThorFlash\bin\Release\net8.0-windows\OdinThorFlash.exe`

## Flujo (equivalente a la consola Thor)

1. `connect` → **Conectar USB**
2. `begin` → **Iniciar Odin**
3. Comandos Odin (tablas abajo)
4. `end` → **Fin sesión**
5. `disconnect` → **Desconectar** — tras flash o fin de sesión, **reinicia el teléfono en Download** ([aviso oficial Thor])

## Comandos Thor → pantallas WPF

| Comando Thor | En OdinThorFlash |
|--------------|------------------|
| `connect` | Dispositivo → Conectar USB |
| `begin` | Iniciar Odin |
| `end` | Fin sesión |
| `disconnect` | Desconectar |
| `flashTar` | Flash firmware → Escanear + Flashear selección |
| `flashFile` | Archivo suelto |
| `dumpPit` | PIT → Volcar PIT del dispositivo |
| `printPit` | Ver PIT (dispositivo / archivo) |
| `flashPit` | PIT → Flashear PIT desde archivo |
| `factoryReset` | PIT → Borrar userdata |
| `erasePartition` | Avanzado → Borrar partición |
| `setRegion` | Avanzado → Código de región |
| `options efsclear` | Flash firmware → casilla **EFS Clear** |
| `options blupdate/resetfc` | Automático en el motor al flashear `.tar` (BL en lote → bootloader update; reset flash count al final) |
| `reboot` / reinicio Odin | Reinicio (+ **Reinicio automático** tras flash) |
| `write` / `read` raw USB | Solo en shell Thor (no en WPF) |

## Estructura del repositorio

```
OdinThorFlash.sln
├── OdinThorFlash.Core/          # Motor: protocolo Odin (TheAirBlow.Thor.Library) + USB Windows
│   ├── Communication/           # IHandler, USB, dispositivos
│   ├── Protocols/               # LOKE/Odin (handshake, flash, PIT, reinicio)
│   ├── Platform/                # WinUSB (LibUsbDotNet, ZLP, interfaces CDC 0x0A)
│   ├── PIT/                     # Tabla de particiones
│   ├── OdinOperations.cs        # Flash .tar, progreso, escaneo firmware
│   ├── OdinSession.cs           # Sesión USB + Odin (API de alto nivel)
│   └── SerialFlashOperations.cs # Reservado: flash por COM (no usado en Windows USB)
├── OdinThorFlash/               # App WPF (solo UI; referencia al Core)
├── docs/                        # WinUSB, flashear firmware
└── drivers/                     # INF ejemplo Samsung 04E8:685D
```

## Licencia

El núcleo deriva de [Samsung-Loki/Thor](https://github.com/Samsung-Loki/Thor) (**MPL-2.0**).
