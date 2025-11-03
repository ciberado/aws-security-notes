# Números binarios y redes

## Direcciones IP individuales

###  ¿Qué es una dirección IP?

- Internet Protocol (IP) es el conjunto de reglas que rigen cómo se envían los datos a través de Internet.
- En este protocolo, cada dispositivo conectado a una red tiene una **dirección IP** única, un número que lo identifica.
- Dos redes independientes pueden tener dispositivos con la misma dirección IP, pero dentro de una misma red, cada dispositivo debe tener una dirección única.
- El número va desde 0 hasta 4.294.967.295 (cuatro mil doscientos noventa y cuatro millones novecientos sesenta y siete mil doscientos noventa y cinco).
- Para entender el por qué de este rango debes aprender cómo funcionan los números binarios y su relación con los 32 bits.

###  ¿Qué son los números binarios?
Los números binarios son una forma de representar valores usando solo **dos dígitos: 0 y 1**.  
- Cada dígito se llama **bit** (de *binary digit*).  
- En lugar de base 10 (decimal), el sistema binario es **base 2**.

Por ejemplo:  
- Decimal **5** → Binario **101**:

Vamos a ver por qué. Imagina que tienes tres casillas, y en cada una solo puedes poner un **1** o un **0**:

```
1    0    1
```

Ahora, cada casilla tiene un "valor" diferente dependiendo de su posición:

- La casilla de más a la **derecha** vale **1**
- La del **medio** vale **2** 
- La de más a la **izquierda** vale **4**

Entonces, para saber qué número es, solo tienes que:

1. **Mirar cada casilla**
2. **Si tiene un 1**, sumas su valor
3. **Si tiene un 0**, no sumas nada

Veamos el **101**:

```
Posición:    1    0    1
Valor:       4    2    1
```

- Primera casilla (izquierda): tiene un **1** → sumamos **4**
- Segunda casilla (medio): tiene un **0** → no sumamos nada
- Tercera casilla (derecha): tiene un **1** → sumamos **1**

**Total: 4 + 1 = 5** ✓

---

###  ¿Por qué 32 bits?
En redes y sistemas, se usan números de **32 bits** porque permiten representar muchos valores:  
- Cada bit puede ser 0 o 1.  
- Con 32 bits hay 2^32 combinaciones (dos multiplado por dos 32 veces) → **4.294.967.296 valores posibles**.

Esto se usa, por ejemplo, en direcciones IPv4.

---

###  ¿Cómo se ve un número de 32 bits?
Un número de 32 bits es una secuencia de 32 ceros y unos.  
Ejemplo:  
```
00000000 00000000 00000000 00000001
```

- Este sería el número **1** en binario, pero escrito con 32 bits (rellenando con ceros a la izquierda).
- Los separamos en grupos de ocho bits (un byte) para facilitar la lectura.

---

###  ¿Cómo se relaciona con redes?

Digamos que queremos asignar la dirección IP `3232235521` a un dispositivo. Este número es casi imposible de recordar, así que lo
representamos en binario: `11000000 10101000 00000000 00000001`. Ahora tenemos el problema de que resulta muy incómodo leer y recordar números tan largos en binario, así que dividimos los 32 bits en cuatro bloques de 8 bits (octetos) y convertimos cada bloque a decimal.

- `11000000` → 192
- `10101000` → 168
- `00000000` → 0
- `00000001` → 1

Así, la dirección IP `3232235521` en formato decimal punteado es `192.168.0.1`.

###  **Cómo convertir un número decimal a binario (32 bits)**

Utilizando una calculadora. Pero si de verdad quieres hacerlo a mano:

1. **Divide entre 2** el número decimal y anota el resto (0 o 1).
2. **Repite** la división con el cociente hasta llegar a 0.
3. **Lee los restos al revés** (de abajo hacia arriba): eso es el binario.
4. **Rellena con ceros** a la izquierda hasta tener 32 bits.

Ejemplo: convertir **10** a binario:
- 10 ÷ 2 = 5 resto **0**
- 5 ÷ 2 = 2 resto **1**
- 2 ÷ 2 = 1 resto **0**
- 1 ÷ 2 = 0 resto **1**

Leyendo al revés: **1010** → con 32 bits:  
```
00000000 00000000 00000000 00001010
```

---

###  **Tabla práctica: Decimal ↔ Binario (32 bits)**

| Decimal | Binario (32 bits)                              |
|---------|-----------------------------------------------|
| 1       | 00000000 00000000 00000000 00000001          |
| 10      | 00000000 00000000 00000000 00001010          |
| 255     | 00000000 00000000 00000000 11111111          |
| 1024    | 00000000 00000000 00000100 00000000          |
| 4294967295 (máx 32 bits) | 11111111 11111111 11111111 11111111 |

---


## Rangos de red

###  ¿Qué es un rango de red?

Un rango de red define **el número inicial y final que puede asignarse a un dispositivo para identificarlo dentro de una red**.  
Como consecuencia, también limita el número total de dispositivos que pueden conectarse a esa red, ya que cada uno de ellos
requerirá una dirección IP única dentro del rango definido. Ahora te cuento más detalles.

---

###  ¿Por qué importa el número de dispositivos?
Cada dispositivo necesita una **dirección IP única**. En IPv4, esas direcciones se representan con **32 bits**.  
Pero no siempre usamos los 32 bits para los dispositivos: parte se reserva para identificar la **red** y el resto para los **hosts** (dispositivos).

Esto facilita el interconectar distintas redes, como sucede con los prefijos telefónicos. Si todos los números de España empiezan por 34 y todos los números de México por 52, es posible conectar las dos redes de telefonía sin que existan dos dispositivos con el mismo número.

En redes IPv4 funciona igual: se elige un prefijo de los 32 bits para identificar la red y el resto para los dispositivos dentro de esa red. Y es importante que si crees que en el futuro ambas redes van a interconectarse entre ellas, los prefijos sean completamente diferentes.

---

### La analogía de los teléfonos

- En telefonía, cada país tiene su propia red.
- Puedes tener un teléfono en España con el número `680 80 80 80` y otro en México con el número `680 80 80 80`. Ambos números son iguales, pero pertenecen a redes diferentes (España y México), pero no pueden existir dos teléfonos con el mismo número dentro de España.
- La red española también un número que la identifica, el prefijo `+34`, y la red mexicana el prefijo `+52`.
- De esta manera, estos dispositivos se identifican globalmente como `+34 680 80 80 80` y `+52 680 80 80 80`, anteponiendo el prefijo de su red.
- Es interesante que los números en la red española van desde el `000 00 00 00` hasta el `999 99 99 99`, lo que permite tener hasta 100 millones de teléfonos diferentes en España (en realidad, no, pero esto es solo una analogía).
- Esos números marcan el rango de la red española.
- De la misma manera, en redes IP, cada red tiene un rango de direcciones IP que puede asignar a sus dispositivos.
- El prefijo de la red ayuda a identificar a qué red pertenece cada dispositivo.
- Si dos redes diferentes se interconectan, no habrá conflictos de direcciones IP siempre que hayamos tenido la previsión de hacer que  sus prefijos sean distintos.

---

### ¿Cómo calcular cuántos bits necesitas para los dispositivos?
- Si quieres **N dispositivos**, necesitas suficientes bits para representarlos.
- Con 8 bits puedes representar 256 valores (de 0 a 255), por ejemplo. Sería suficiente para 100 dispositivos, pero no para 300.
- Otro ejemplo: con 6 bits puedes representar 64 valores (de 0 a 63), suficiente para 50 dispositivos, pero no para 70.


---

### ¿Cómo funcionan los prefijos de red en IP?
- IPv4 tiene 32 bits. Si deseamos por ejemplo reservar 6 bits son para hosts (dispositivos) porque tenemos previsto desplegar 50 dispositivos, los otros **26 son para la red (prefijo)**.
- En cierto sentido, los 26 primeros bits son "fijos" y sirven para dar un número de red. Sería equivalente al prefijo de los números de teléfono: todos los teléfonos de España empiezan por 34, y todos los teléfonos de México por 52.
- El prefijo de red termina siendo el número que representa esa red. España en telefonía es el 34, y una supuesta red que contenga un máximo de 64 dispositivos (6 bits) tendría un prefijo de 26 bits como este: `11000000.10101000.00000000.00xxxxxx` (que si recuerdas, corresponde a `192.168.0.0`). 
- Dado que los primeros 26 bits son fijos, solo los últimos 6 pueden variar para asignar a los dispositivos.
- Esto te indica que el primer número asignable a un dispositivo sería `000000` (0 en decimal) y el último `111111` (63 en decimal).
- La IP completa del primero número sería `11000000.10101000.00000000.00000000` (0 en decimal) y la del último `11000000.10101000.00000000.00111111` (63 en decimal) si ponemos la dirección de red que hemos elegido seguida de la dirección del host (dispositivo).

### CIDR Notation

- Representar un rango de red como `192.168.0.0-192.168.0.63` es pesadísimo y poco práctico.
- Por eso se usa la notación CIDR (Classless Inter-Domain Routing).
- En esta notación, se escribe la dirección de red seguida de una barra inclinada y el número de bits que se usan para la red.
- En nuestro ejemplo, la red sería `192.168.0.0/26`, porque usamos 26 bits para la red y 6 bits para los dispositivos.
- Esto indica que los primeros 26 bits son fijos (para la red) y los últimos 6 bits pueden variar (para los dispositivos).
- No hay magia, solo una forma más cómoda de representar el rango de direcciones IP.

### Calculadora de rangos IP

- Las personas que pasan el día en redes pueden calcular estos rangos a mano (e incluso mentalmente), pero para el resto de mortales existen calculadoras online que hacen el trabajo sucio.
- Mi recomendación es usar [https://cidr.xyz/](https://cidr.xyz/).
- Visita [este enlace concreto](https://cidr.xyz/#192.168.0.0/26) y verás el rango de direcciones IP que hemos estado usando como ejemplo (`192.168.0.0/26`).
- La calculadora te muestra el rango completo, el número de hosts disponibles, la máscara de subred, la dirección de broadcast, etc. Pero no te preocupes demasiado por esos términos ahora mismo.
- Si quisiésemos crear otras tres redes para otros 50 dispositivos cada una, podríamos usar los siguientes prefijos, puedes conseguir sus direcciones pulsando en los últimos bits del prefijo de red (los que están en amaraillo). Verás que la calculadora actualiza el rango automáticamente: `192.168.0.64/26`, `192.168.0.128/26` y `192.168.0.192/26`.
- Como estas redes no se solapan entre ellas (no hay direcciones IP repetidas), podríamos interconectarlas sin problemas en el futuro si lo deseamos.

---

## Ejercicio

## 📝Calcular el rango de red para 100 dispositivos

**Situación:**
Una pequeña empresa necesita conectar **100 ordenadores** a su red local. Tu tarea es determinar qué rango de direcciones IP privadas necesitan.

**Dato inicial:**
La dirección de red que te han asignado es: **192.168.1.0**

---

### Preguntas paso a paso:

**Paso 1:** ¿Cuántas direcciones IP necesitas en total?

**Paso 2:** ¿Cuál es la potencia de 2 más cercana que cubre esas direcciones?
- Pista: 2^6 = 64, 2^7 = 128, 2^8 = 256, 2^9 = 512

**Paso 3:** Con ese número de direcciones, ¿cuántos bits necesitas para los hosts (dispositivos)?

**Paso 4:** Escribe la supuesta IP de uno de esos dispositivos en binario (por ejemplo, `192.168.1.30`)
- Recuerda: una dirección IP tiene 32 bits en total
- Los primeros bits son para la red y los últimos son para los hosts

**Paso 5:** ¿Cuál es el rango completo de IPs disponibles para asignar a los ordenadores de la red?
- Primera IP: 192.168.1.___
- Última IP: 192.168.1.___

**Paso 6:** Escribe el rango de red en formato CIDR
- Formato: 192.168.1.0/___


