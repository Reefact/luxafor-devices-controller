# Controlador de dispositivos Luxafor

Una librería .Net que proporciona una API simple para controlar los dispositivos Luxafor.

## Luxafor

### Company Overview

[Luxafor](https://luxafor.com) es una empresa que diseña y vende productos para la productividad en la oficina, como indicadores de disponibilidad y herramientas de notificación. 

Su producto estrella es un [indicador LED de disponibilidad](https://luxafor.com/product/flag) que puede programarse para mostrar distintos colores en función del estado de disponibilidad del usuario. 

El objetivo de Luxafor es proporcionar a los usuarios una forma sencilla y eficaz de señalar su disponibilidad a los compañeros de trabajo y mejorar la comunicación y la colaboración en el lugar de trabajo.

### Vista rápida de los dispositivos

He aquí una lista no exhaustiva de [dispositivos Luxafor](https://luxafor.com/products):

- Luxafor Flag: un indicador LED de disponibilidad que muestra la disponibilidad personal
- Luxafor Bluetooth': un indicador LED de disponibilidad inalámbrico y controlado por software que muestra las notificaciones y la disponibilidad personal.
- Luxafor Switch: un indicador de disponibilidad inalámbrico y teledirigido que muestra en tiempo real la disponibilidad de las salas de reuniones y los puestos de trabajo.
- Luxafor Cube`: un indicador LED de disponibilidad autónomo que muestra la disponibilidad de las salas de reuniones
- Luxafor Pomodoro-Timer`: un temporizador LED alimentado por USB que permite dividir el trabajo en espacios más pequeños (véase [Pomodoro](https://reefact.net/craftsmanship/tools/pomodoro))
- Luxafor Orb`: un indicador LED de disponibilidad USB de gran ángulo
- Luxafor CO2 Monitor: un sensor que analiza la calidad del aire de una habitación y le avisa cuando es necesario ventilarla.
- Botón Luxafor Mute: enciende/apaga el micrófono con un solo toque e indica si estás disponible con el rojo/verde
- Luxafor Colorblind Flag: luz monocroma USB LED de disponibilidad elimina las distracciones y aumenta la productividad

### Integración

Estos diferentes dispositivos están diseñados para ser accionados manualmente ("mecánicamente") para algunos, semiautomáticamente (accionamiento manual vía [software](https://luxaformanual.com)) / automáticamente (integración vía [software](https://luxaformanual.com) con herramientas como Teams, Skype, Cisco, Zappier o vía Webhook) para otros. 

## Presentación de la biblioteca

Esta librería tiene como objetivo permitir la integración de dispositivos LED USB a sus aplicaciones internas sin necesidad de pasar por el servidor Luxafor (webhook).

Está desarrollado en .Net Core y se basa en la librería [HidLibrairy] (https://github.com/mikeobrien/HidLibrary) que permite enumerar y comunicarse con dispositivos USB compatibles con HID en .NET.

El siguiente código muestra un ejemplo de uso básico de la biblioteca para controlar un dispositivo [Luxafor Orb](https://luxafor.com/product/orb/).

https://github.com/Reefact/luxafor-devices-controller/blob/eb984aebc8db58c9922f9b480706e946a8ef5d88/LuxaforDevicesController.UnitTests/UsageExamples.cs#L20-L32

La línea 21 muestra cómo conectarse a un único Orb conectado al puerto USB de la máquina.

Voy a repasar rápidamente el conjunto de comandos posibles para enviar a los dispositivos desde el `LuxaforDevice`.

### Turn off

```csharp
void TurnOff(); // Apaga todos los LEDs del dispositivo
void TurnOff(TargetedLeds targetedLeds); // Apagar los LEDs del dispositivo apuntado
```

### Establecer un solo color

```csharp
void SetColor(BasicColor basicColor); // Enciende todos los LEDs del dispositivo en un color básico.
void SetColor(CustomColor customColor); // Enciende los LEDs del dispositivo en un color personalizado.
void SetColor(TargetedLeds targetedLeds, BasicColor basicColor); // Enciende todos los LEDs targeted del dispositivo en un color básico.
void SetColor(TargetedLeds targetedLeds, CustomColor color); // Enciende los LEDs del dispositivo targeted en un color personalizado.
```

### Hacer una transición (fundido)

```csharp
void FadeColor(BasicColor basicColor, FadeDuration duration); // Transición de todos los LEDs del dispositivo a un color básico
void FadeColor(CustomColor color, FadeDuration duration); // Cambia todos los LEDs del dispositivo a un color personalizado
void FadeColor(TargetedLeds targetedLeds, BasicColor basicColor, FadeDuration duration); // Transición de los LEDs del dispositivo a un color básico
void FadeColor(TargetedLeds targetedLeds, CustomColor color, FadeDuration duration); // Transición de los LEDs del dispositivo a un color personalizado
```

### Blink (efecto estroboscópico)

```csharp
void Strobe(BasicColor basicColor, Speed speed, Repeat repeat); // Parpadea todos los LEDs del dispositivo en un color básico
void Strobe(CustomColor customColor, Speed speed, Repeat repeat); // Parpadea todos los LEDs del dispositivo en un color personalizado.
void Strobe(TargetedLeds targetedLeds, BasicColor basicColor, Speed speed, Repeat repeat); // Hace parpadear los LEDs del dispositivo en un color básico
void Strobe(TargetedLeds targetedLeds, CustomColor customColor, Speed speed, Repeat repeat); // Hace parpadear los LEDs del dispositivo objetivo en un color personalizado
```

### Waves

```csharp
void Wave(WaveType waveType, BasicColor basicColor, Speed speed, Repeat repeat); // Inicia un patrón de "onda" que se dirige a todos los LEDs del dispositivo en función de un color básico.
void Wave(WaveType waveType, CustomColor customColor, Speed speed, Repeat repeat); // Inicia un patrón de onda que se dirige a todos los LEDs del dispositivo basándose en un color personalizado.
```

### Patrones incorporados

```csharp
void PlayPattern(BuiltInPattern, Repeat repeat); // Iniciar un patrón incorporado que apunte a todos los LEDs del dispositivo.
```

### Enviar un comando

Es posible crear comandos personalizados llamados `LightningCommand` para que puedan ser reutilizados en el código:

```csharp
var command = LightningCommand.CreateStrobeCommand(TargetedLeds.All, BasicColor.Yellow, Speed.FromByte(20), Repeat.Count(3));
```

El método `Enviar` le permite utilizar estos comandos.

```csharp
void Send(LightningCommand command); // Enviar un comando al dispositivo
```