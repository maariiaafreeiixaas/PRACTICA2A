# Pràctica 2

## Pràctica A: Interrupció per GPIO

### Objectiu
Comprendre el funcionament de les interrupcions de maquinari en l'ESP32. Controlarem dos LEDs de forma periòdica i utilitzarem una entrada que, en ser activada, canviarà la freqüència de les oscil·lacions només en un LED.

### Codi
El codi següent configura una interrupció per a un botó connectat al pin GPIO 18. Cada vegada que el botó és premut, la interrupció s'activa i es comptabilitzen les pulsacions.

```cpp
struct Button { 
  const uint8_t PIN; 
  uint32_t numberKeyPresses; 
  bool pressed; 
}; 

Button button1 = {18, 0, false}; 

//funció de servei d'interrupció, incrementa el comptador de pulsacions i marca el botó com a premut.
void IRAM_ATTR isr() { 
  button1.numberKeyPresses += 1; 
  button1.pressed = true; 
} 

//setup() inicialitza el pin del botó com a entrada amb resistència de pull-up i configura la interrupció per detectar la caiguda de senyal.
void setup() { 
  Serial.begin(115200); 
  pinMode(button1.PIN, INPUT_PULLUP); 
  attachInterrupt(button1.PIN, isr, FALLING); 
} 

//bucle principal, comprova si el botó ha estat premut, imprimeix el nombre de pulsacions i restableix l'estat del botó.
void loop() { 
  if (button1.pressed) { 
    Serial.printf("Button 1 has been pressed %u times\n", button1.numberKeyPresses); 
    button1.pressed = false; 
  } 

  // Desactivar la interrupció després d'1 minut
  static uint32_t lastMillis = 0; 
  if (millis() - lastMillis > 60000) { 
    lastMillis = millis(); 
    detachInterrupt(button1.PIN); 
    Serial.println("Interrupt Detached!"); 
  } 
}

Sortides a través de la impressió sèrie

Button 1 has been pressed 1 times
Button 1 has been pressed 2 times
...
Interrupt Detached!