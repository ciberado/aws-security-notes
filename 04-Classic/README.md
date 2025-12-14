# Arquitectura Clásica en AWS - Conceptos Clave

Vamos a repasar lo que montamos en esta sesión, que básicamente es la arquitectura típica que vas a encontrar en el 95% de las aplicaciones del mundo. Y lo mejor es que lo hacemos paso a paso, entendiendo primero el problema y luego la solución.

## El Objetivo: Una Aplicación Web Completa

Necesitas montar una aplicación web (en este caso, nuestra app de Pokémon) que sea accesible desde internet, que tenga datos persistentes, y que no se caiga si algo falla. Proponemos una arquitectura clásica de tres capas:

1. **Balanceador de carga** (para distribuir el tráfico)
2. **Flota de servidores** (para ejecutar la aplicación)
3. **Base de datos** (para guardar los datos)

## Primera Pieza: La Base de Datos (RDS)

Para empezar necesitarás  guardar información y poder consultarla después. Y no quieres que administrar backups, parches y alta disponibilidad te quite la vida. Relational Database Service (RDS) es la solución gestionada de bases de datos de AWS que resuelve todo esto por ti.

### ¿Por qué RDS es espectacular?

Imagínate el trabajo de antes: configurar la base de datos, vigilar las copias de seguridad (había equipos enteros dedicados solo a esto), aplicar parches de seguridad... Un auténtico dolor de cabeza.

Con RDS:

**1. Alta Disponibilidad automática**
- Despliegas dos instancias en dos subredes distintas (Multi-AZ)
- Una es la principal, la otra es standby
- Si la principal falla, en menos de 10 minutos la standby se activa
- Los datos se replican automáticamente entre ambas

**2. Backups automáticos**
- Se hacen copias de seguridad automáticamente
- Guardas snapshots completos
- Y también el log de cambios continuo
- Puedes viajar en el tiempo: recuperar tu base de datos a cualquier punto de los últimos 7-31 días
- Todo se guarda en S3 (11 nueves de durabilidad)

**3. Parcheo automático**
- Los parches de seguridad se aplican solos si quieres
- No son cambios de versión mayor, solo actualizaciones de seguridad

### La diferencia entre Alta Disponibilidad y Disaster Recovery

- **Alta disponibilidad (Multi-AZ)**: Si un nodo falla, el otro toma el relevo en 10 minutos
- **Disaster Recovery (Backups)**: Si te cargas los datos (como el colega que hizo un UPDATE sin WHERE y cerró un banco día y medio), recuperas de un backup

### Configuración clave

- **Red**: La pones en subredes PRIVADAS, no tocan internet
- **Security Group**: Solo acepta conexiones desde el Security Group de la aplicación
- **Tamaño**: Empieza pequeño (puedes cambiar después, pero requiere reinicio)
- **Disco**: 20GB suele bastar para empezar, puede crecer automáticamente

## Segunda Pieza: El Balanceador de Carga

Los usuarios necesitan un único punto de entrada, pero tú quieres repartir la carga entre varias máquinas. La solución consistirá en desplegar un balanceador de carga situado entre tus usuarios y tu flota de servidores.

### ¿Qué hace un balanceador?

Recibe peticiones y las reenvía a una de las máquinas de su lista. Así:
- Los usuarios hablan a un único sitio (el balanceador)
- El balanceador reparte entre N máquinas
- Si una máquina falla, deja de enviarle tráfico

### Tipos de balanceadores

**Application Load Balancer (ALB)**
- Para aplicaciones web (HTTP/HTTPS)
- El más común y flexible
- Es el que usamos para el 95% de casos

**Network Load Balancer (NLB)**
- Para protocolos que NO son HTTP
- Ejemplo: una base de datos distribuida con protocolo propietario
- También si necesitas IPs estáticas (las del ALB cambian)

**Gateway Load Balancer**
- Para meter firewalls de inspección de tráfico (Palo Alto, Fortinet, etc.)
- Reenvía todo el tráfico al appliance para que lo analice

### Componentes del balanceador

**1. Listener**: ¿En qué puerto escucha?
- Ejemplo: Puerto 80 (HTTP estándar)

**2. Target Group**: ¿A quién reenvía?
- Es la lista de máquinas
- Le dices el puerto de las máquinas (ejemplo: 8080)
- Hace Health Checks: pregunta periódicamente "¿estás bien?"
- Si una máquina no responde, deja de enviarle tráfico

**3. Security Group**: ¿Quién puede hablarle?
- El balanceador en zona pública
- Acepta tráfico de internet

### Historia importante: Las IPs del ALB cambian

**SIEMPRE usa el nombre DNS del balanceador, NUNCA las IPs directamente.**

Acuérdate de lo que te conté del colega consultor hizo una auditoría de seguridad apuntando las IPs del balanceador. Las IPs cambiaron y terminó auditando la empresa equivocada. Tres semanas después presentó un informe lleno de vulnerabilidades... de otra compañía.

## Tercera Pieza: La Flota de Máquinas

Por útlimo también necesitas múltiples servidores idénticos que se puedan crear y destruir automáticamente según la demanda. Puedes usar un Launch Template junto con un Auto Scaling Group para lograr esto.

### Launch Template: La Receta

No es una máquina real, es la **descripción** de cómo crear máquinas:
- ¿Qué sistema operativo? (AMI de Ubuntu, por ejemplo)
- ¿Qué tamaño? (t3.medium)
- ¿En qué subredes? (Las privadas)
- ¿Qué Security Group? (El de la aplicación)
- ¿Qué hacer al arrancar? (User Data: instala y arranca la app)

Recuerda que el **User Data** es el script que se ejecuta al arrancar

### Instance Profile: La Chapita del FBI

Tu aplicación necesita permisos para acceder a otros servicios AWS (como S3). Le pones un **Instance Profile** a la máquina, que es el mecanismo que asigna un rol IAM a la instancia. Todas las aplicaciones en esa máquina pueden usar esa identidad.

**Regla de oro:** Una aplicación por servidor. Si tienes varios microservicios, un servidor pequeño por cada uno.

### Auto Scaling Group: La Flota Inteligente

Aquí está la magia. Le dices:
- Usa este Launch Template
- Quiero tener 6 máquinas (desired)
- Pero nunca menos de 2 (minimum) para tolerancia a fallos (y no quedarte sin servicio por haber apagado todas las máquinas)
- Y nunca más de 10 (maximum) para no pasarte de presupuesto
- Conéctalas al Target Group del balanceador

**¿Qué hace el Auto Scaling Group?**

1. **Mantiene el número deseado**: Si pides 6 y una se cae, crea otra automáticamente
2. **Se adapta a la carga**: Es fácil de configurar para que el parámetro *desire* cambie automáticamente en base a alguna condición. Por ejemplo, si la CPU sube del 50%, añade máquinas. Si baja, las quita.
3. **Health Checks**: Puede preguntarle al balanceador si las máquinas están bien

**Ejemplo real: Black Friday**
- Días normales: 50 máquinas
- Black Friday: 400 máquinas
- Cambias un número en la configuración y listo

### Cuidado con los Health Checks del balanceador

Si activas que el Auto Scaling Group pregunte al balanceador por la salud de las máquinas, ten cuidado:
- Bajo carga extrema, las máquinas pueden estar agobiadas
- El Health Check no se responde a tiempo
- El Auto Scaling Group piensa que está rota y la mata
- Agobias más al resto de máquinas
- Efecto dominó = desastre

## Los Firewalls: NACL vs Security Groups

Recuerda: necesitas controlar qué tráfico entra y sale.

### Network ACLs (NACL)
- **Stateless**: No sirve para nada (es broma, pero casi)
- Están en cada subred
- Tienes que configurar entrada Y salida
- Nadie los usa mucho

### Security Groups
- **Stateful**: El que importa de verdad
- Si envías un paquete, la respuesta entra automáticamente (aunque no haya regla explícita)
- Son como etiquetas que asignas a recursos
- Lo mejor: Puedes decir "acepto tráfico del Security Group X" sin necesidad de IPs

**Ejemplo de nuestra arquitectura:**
- Security Group del balanceador: Acepta de internet
- Security Group de la app: Acepta del SG del balanceador
- Security Group de la BD: Acepta del SG de la app

Es como una cadena de confianza.

## La Arquitectura Completa

```
Internet
   ↓
[Internet Gateway]
   ↓
[Application Load Balancer] (zona pública)
   ↓
[Target Group]
   ↓
[Auto Scaling Group] → [Máquina 1, 2, 3...N] (zona privada)
   ↓
[Base de datos RDS] → [Standby] (zona privada)
```

Todo en la zona privada excepto el balanceador. Las máquinas nunca tocan internet directamente.

## Costes y Gestión

**Recuerda borrar todo cuando termines de practicar:**
- Base de datos: Cara si la dejas corriendo
- Auto Scaling Group: 6 máquinas suman
- Balanceador: Lo más barato (~30€/mes)
- NAT Gateway: 30€ cada uno

Con 50€ de crédito, no va a durar mucho si dejas todo encendido.

## El Cambio de Mentalidad

Antes: "Tengo 3 servidores preciosos, les pongo nombre, los cuido"

Ahora: "Tengo una capa de computación. Me da igual si hay 2 o 200 máquinas, aparecen y desaparecen solas. No tienen nombre, son ganado, no mascotas"

**Consecuencias para seguridad:**
- Los logs no pueden estar en las máquinas (desaparecen)
- No puedes investigar conectándote a una máquina (ya no existe)
- La seguridad está en la configuración (Launch Template, Security Groups)
- Auditas el código, no las máquinas individuales

## La Realidad

Con esta arquitectura, si te vas de vacaciones 6 meses y vuelves, esto debería seguir funcionando. Claro, en realidad no te vas porque pasan cosas, pero la idea es que la mayoría de problemas se resuelven solos:
- Una máquina falla → Se reemplaza automáticamente
- La BD falla → En 10 minutos tienes la standby activa
- Llega más tráfico → Se crean más máquinas
- Baja el tráfico → Se eliminan máquinas

**Ejemplo real:** Un equipo de 40 personas gestionaba más de 10,000 servicios y decenas de miles de máquinas. ¿Cómo? Porque casi todo se arreglaba solo. Configurar bien es la clave.

Y eso es todo. Esta es la arquitectura que vas a encontrar una y otra vez. El sota, caballo y rey de las aplicaciones cloud. Lo demás son variaciones sobre este tema.