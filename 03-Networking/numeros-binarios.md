# Números binarios y redes

## 0. ¿Qué es una dirección IP?

- Internet Protocol es el conjunto de reglas que rigen cómo se envían los datos a través de Internet.
- En este protocolo, cada dispositivo conectado a una red tiene una **dirección IP** única.
- Una dirección IP es una serie de números que identifica a un dispositivo en una red.
- El número va desde 0 hasta 4.294.967.295 (cuatro mil doscientos noventa y cuatro millones novecientos sesenta y siete mil doscientos noventa y cinco).
- Para entender el por qué de este rango debes aprender cómo funcionan los números binarios y su relación con los 32 bits.

## 1. ¿Qué son los números binarios?
Los números binarios son una forma de representar valores usando solo **dos dígitos: 0 y 1**.  
- Cada dígito se llama **bit** (de *binary digit*).  
- En lugar de base 10 (decimal), el sistema binario es **base 2**.

Por ejemplo:  
- Decimal **5** → Binario **101** (porque \(5 = 1\times2^2 + 0\times2^1 + 1\times2^0\)).

---

## 2. ¿Por qué 32 bits?
En redes y sistemas, se usan números de **32 bits** porque permiten representar muchos valores:  
- Cada bit puede ser 0 o 1.  
- Con 32 bits hay \(2^{32}\) combinaciones → **4.294.967.296 valores posibles**.

Esto se usa, por ejemplo, en direcciones IPv4.

---

## 3. ¿Cómo se ve un número de 32 bits?
Un número de 32 bits es una secuencia de 32 ceros y unos.  
Ejemplo:  
```
00000000 00000000 00000000 00000001
```

- Este sería el número **1** en binario, pero escrito con 32 bits (rellenando con ceros a la izquierda).
- Los separamos en grupos de ocho bits (un byte) para facilitar la lectura.

---

## 4. ¿Cómo se relaciona con redes?
En IPv4, una dirección como `192.168.0.1` se convierte en binario:  
- 192 → `11000000`  
- 168 → `10101000`  
- 0 → `00000000`  
- 1 → `00000001`  

Juntas forman 32 bits:  
```
11000000 10101000 00000000 00000001
```

---

Perfecto, vamos paso a paso:

---

## 5. **Cómo convertir un número decimal a binario (32 bits)**

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

## 6. **Cómo convertir una dirección IP a binario**
Cada bloque (octeto) de la IP se convierte por separado:
- IP: `192.168.0.1`
- 192 → `11000000`
- 168 → `10101000`
- 0 → `00000000`
- 1 → `00000001`

Resultado:
```
11000000 10101000 00000000 00000001
```

Aunque no te engañes, normalmente usamos calculadoras ;)

---

## 7. **Tabla práctica: Decimal ↔ Binario (32 bits)**

| Decimal | Binario (32 bits)                              |
|---------|-----------------------------------------------|
| 1       | 00000000 00000000 00000000 00000001          |
| 10      | 00000000 00000000 00000000 00001010          |
| 255     | 00000000 00000000 00000000 11111111          |
| 1024    | 00000000 00000000 00000100 00000000          |
| 4294967295 (máx 32 bits) | 11111111 11111111 11111111 11111111 |

---


### 8. ¿Qué es un rango de red?
Un rango de red define **el número inicial y final que puede asignarse a un dispositivo para identificarlo dentro de una red**.  

---

### 9. ¿Por qué importa el número de dispositivos?
Cada dispositivo necesita una **dirección IP única**. En IPv4, esas direcciones se representan con **32 bits**.  
Pero no siempre usamos los 32 bits para los dispositivos: parte se reserva para identificar la **red** y el resto para los **hosts** (dispositivos).

Esto facilita el interconectar distintas redes, como sucede con los prefijos telefónicos. Si todos los números de España empiezan por 34 y todos los números de México por 52, es posible conectar las dos redes de telefonía sin que existan dos dispositivos con el mismo número.

En redes IPv4 funciona igual: se elige un prefijo de los 32 bits para identificar la red y el resto para los dispositivos dentro de esa red. Y es importante que si crees que en el futuro ambas redes van a interconectarse entre ellas, los prefijos sean completamente diferentes.

---

### 10. ¿Cómo calcular cuántos bits necesitas para los dispositivos?
- Si quieres **N dispositivos**, necesitas suficientes bits para representarlos.
- Fórmula:  
  \[
  2^h - 2 \geq N
  \]
  donde **h** = número de bits para hosts (quitamos 2 porque una dirección es para la red y otra para broadcast).

Ejemplo:  
- Si quieres **50 dispositivos**:  
  \[
  2^h - 2 \geq 50
  \]
  Probamos:  
  - \(h = 5\) → \(2^5 - 2 = 30\) (no alcanza)  
  - \(h = 6\) → \(2^6 - 2 = 62\) (¡sí alcanza!)  

Entonces necesitas **6 bits para hosts**.

---

### 11. ¿Cómo se ve en binario y decimal?
IPv4 tiene 32 bits. Si 6 son para hosts, los otros **26 son para la red (prefijo)**.  
Esto se llama **máscara de subred**. Recuerda que en este caso, 32 bits en total - 26 bits para red = 6 bits para hosts.

************* TODO añade el prefijo real en lugar de la máscara de subred. **********************
Intégralo con https://cidr.xyz/
*************************************************************************************************

- Binario: `11111111.11111111.11111111.11000000`  
- Decimal: `255.255.255.192`  

Esto significa que teóricamente el primer host (dispositivo) podría tener el número `000000` (0 en decimal) y el último `111111` (63 en decimal). Para serte sincero, esas direcciones no se usan para asignar a dispositivos, pero estamos simplificando ;)

Si añadimos cuatro dispositivos y le damos una IP a cada uno, podrían quedar así:

- **Host 1** → `192.168.1.1`
- **Host 2** → `192.168.1.2`
- **Host 3** → `192.168.1.3`
- **Host 4** → `192.168.1.4`

En binario (solo los últimos 6 bits cambian, los 26 restantes para llegar a 32 hemos dicho que los fijábamos a `11111111.11111111.11111111.11`):

```
000001 → 192.168.1.1
000010 → 192.168.1.2
000011 → 192.168.1.3
000100 → 192.168.1.4
```

Las direcciones no tienen que ser consecutivas, simplemente tienen que estar dentro del rango que hemos definido: `192.168.1.0` a `192.168.1.63`. Igual que los números de teléfono en España empiezan por 34, pero el resto del número puede variar.


