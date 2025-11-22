# Conceptos de Gobernanza e Identidad en AWS

## El Problema: Del Caos al Control

Imagina que empiezas con una aplicación sencilla en la nube. Un par de servidores, una base de datos, quizás un balanceador. Todo controlado, todo claro. Seis meses después tienes cincuenta aplicaciones corriendo, veinte desarrolladores experimentando, tres entornos por aplicación (desarrollo, preproducción, producción), y de repente nadie sabe muy bien quién puede tocar qué, cuánto está costando cada cosa, ni dónde diablos están los recursos de aquel proyecto que empezó Marketing el trimestre pasado.

Bienvenidos al caos.

Y el caos no es solo molesto. El caos cuesta dinero. Literalmente. Hay historias reales de empresas que han pasado de gastar $5 al día a $310,000 en un fin de semana porque alguien subió credenciales a un repositorio público en GitHub y un bot las encontró. En cuestión de horas, miles de máquinas estaban minando criptomonedas a tu costa. El microinfarto no te lo quita nadie, aunque AWS perdone la deuda (que normalmente lo hace si es la primera vez y abres un ticket explicando la situación).

Pero el problema no es solo el dinero. Es que cuando llegas al lunes y tienes 500 cuentas de AWS distribuidas por diferentes departamentos, regiones y proyectos, y alguien te pregunta "¿quién tiene acceso a la base de datos de producción de la aplicación de e-commerce?", no puedes tardar tres días en averiguarlo. Necesitas saberlo ya. Y necesitas poder cambiar ese acceso de forma segura, auditable y sin romper nada.

## Por Qué la Gobernanza es Crítica

La gobernanza no es burocracia. Es supervivencia.

Cuando hablamos de gobernanza en cloud, hablamos de tres pilares que mantienen tu infraestructura funcional, segura y económicamente viable:

**Organización**: Cómo agrupas las cosas para que tengan sentido. Para que cuando alguien nuevo llegue al equipo no tarde dos semanas en entender dónde está cada pieza. Para que puedas decir "esto es del departamento de ventas, esto de fabricación" y que esa estructura se refleje claramente en cómo está montado tu cloud.

**Costes**: Porque si no sabes cuánto gastas, estás volando a ciegas. Y no hablamos solo de saber el total a final de mes. Hablamos de poder decir "esta aplicación cuesta €5,000 mensuales" y poder hacer chargeback al departamento que la usa. Hablamos de detectar cuándo algo va raro antes de que la factura se dispare. Las empresas serias revisan métricas de coste con la misma frecuencia que revisan el uso de CPU.

**Seguridad**: Y aquí entra la identidad, que es donde vamos a centrar la mayor parte de esta explicación. Porque puedes tener la infraestructura más robusta del mundo, pero si cualquiera puede entrar y borrar la base de datos de producción, no tienes nada. La gestión de identidades (quién es quién), autenticación (cómo demuestran que son quienes dicen ser) y autorización (qué pueden hacer una vez dentro) es posiblemente el aspecto más crítico de la seguridad en cloud.

## La Entropía Solo Crece

Hay una ley física que dice que el desorden de un sistema solo puede aumentar. En cloud esto es especialmente cierto. Cada semana alguien necesita un entorno nuevo, cada mes se despliega otra aplicación, cada trimestre se reorganiza un departamento. Si no tienes mecanismos claros desde el principio, al cabo de un año no hay quien maneje aquello.

La buena noticia es que estos mecanismos existen. AWS (y el resto de proveedores cloud) proporcionan herramientas específicamente diseñadas para gestionar este crecimiento orgánico. No son perfectas—ya veréis que AWS tiene algunas decisiones de diseño bastante cuestionables—pero funcionan.

La mala noticia es que son complejas. Especialmente en AWS, donde el sistema ha crecido de forma orgánica durante casi dos décadas, añadiendo capas sobre capas de forma retrocompatible. Esto significa que a veces vas a encontrar tres formas de hacer lo mismo, terminología confusa (preparaos para que "cuenta" no signifique lo que pensáis que significa), y decisiones que solo tienen sentido si conoces la historia de por qué se hicieron así.

## Lo Que Vamos a Aprender

En las siguientes secciones vamos a construir, paso a paso, el conocimiento necesario para gestionar identidades y organización en AWS de forma profesional. Empezaremos por los conceptos más básicos (qué es una cuenta, qué es un usuario) y construiremos los conceptos más sofisticados encima de ellos.

Al final, entenderéis cómo una empresa real con miles de empleados y cientos de aplicaciones mantiene todo bajo control. Cómo asegurarse de que los desarrolladores pueden experimentar sin miedo a romper producción. Cómo los auditores pueden revisar costes sin poder tocar la infraestructura. Cómo, cuando alguien deja la empresa, podéis revocar todos sus accesos en cinco minutos.

Y lo más importante: entenderéis por qué cada pieza está ahí. Porque en seguridad y gobernanza, implementar algo sin entender el porqué es la forma más rápida de crear agujeros de seguridad que ni siquiera sabías que existían.

Vamos allá.

---

## Gobernanza

La gobernanza en cloud consiste en establecer reglas y mecanismos para controlar el caos natural que tiende a generarse. Se compone de tres pilares fundamentales:

- **Organización**: Cómo distribuimos y agrupamos elementos
- **Costes**: Monitorización y control del gasto
- **Seguridad**: Incluyendo identidad, autenticación y autorización

**Nota crítica sobre costes**: La monitorización de costes es detección de intrusión. Un gasto anómalo (como pasar de $5 a $310,000 en un fin de semana) suele indicar credenciales comprometidas usadas para minado de criptomonedas. AWS puede perdonar estos cargos si se reportan inmediatamente, pero es responsabilidad del cliente configurar alertas.

## Account (Cuenta de AWS)

Una **account** es un grupo de recursos de infraestructura relacionados entre sí. A diferencia de otros contextos, en AWS "cuenta" NO se refiere a una identidad de usuario, sino a un contenedor organizativo similar a un Resource Group en Azure.

Ejemplos de uso:
- Infraestructura de una aplicación en desarrollo
- Infraestructura de la misma aplicación en producción
- Entorno experimental de un desarrollador

**Concepto clave**: En organizaciones grandes, pueden existir cientos o miles de cuentas. No es raro que Netflix tuviera ~14,000 máquinas con CPU al 0% olvidadas.

## Root User

El **root user** es la identidad que crea una cuenta específica y tiene poderes omnipotentes sobre ella, sin posibilidad de restricción de permisos.

Características:
- Cada cuenta tiene su propio root user distinto
- Está asociado a una dirección de correo electrónica
- **Nunca debe usarse** para operaciones cotidianas
- Debe protegerse con MFA (autenticación multifactor)
- Utiliza direcciones genéricas y no personales (ej: `ops-lead@example.com`), dado que las personas cambiamos de puesto y empresa.

**Truco del email**: Puedes usar el formato `usuario+alias@dominio.com` para crear múltiples root users con una sola dirección de correo (ej: `alice.wonderland+aldebaran@aprendercloud.com`).

## AWS Organizations

**AWS Organizations** es el servicio que permite crear y gestionar jerarquías de cuentas de forma centralizada, sin necesidad de repetir el proceso manual de creación para cada una.

###1. Management Account

La **management account** es la primera cuenta que se crea y establece la relación inicial entre una empresa y AWS. Desde ella se activa Organizations y se puede:
- Crear nuevas cuentas sin introducir tarjeta de crédito
- Gestionar la jerarquía organizativa
- Tener visibilidad global de costes

**Importante**: El término "root account" en Organizations no tiene nada que ver con "root user". Ahora se llama "management account" para evitar confusión.

###2. Organizational Unit (OU)

Una **Organizational Unit** es una carpeta o contenedor que puede albergar:
- Cuentas (accounts)
- Otras Organizational Units (estructura jerárquica)

Se usan para reflejar la estructura organizativa de la empresa:
```
Root OU
├── Ventas
├── Marketing  
│   ├── App 1 (dev)
│   ├── App 1 (prod)
│   └── App 2
└── Fabricación
    ├── Europa
    │   └── Garrapinillos
    └── Asia
```

**Concepto equivalente en Azure**: Management Groups

###3. Tags (Etiquetas)

Los **tags** son metadatos clave-valor que se asignan a recursos y cuentas. Son críticos para:
- Identificar centro de coste (quién paga)
- Realizar chargeback (facturación interna)
- Organizar y filtrar recursos

Ejemplo: `CentroCoste: Fabrica`

## Limitaciones de Gasto

**No es posible establecer límites rígidos de gasto en producción** porque:
- Los recursos se escalan elásticamente según demanda
- Un límite podría apagar la infraestructura en un momento crítico de éxito (aplicación viral)
- Los caps de Azure están pensados solo para desarrollo

**Solución**: Configurar alertas sobre métricas de coste y proyecciones de gasto mensual. Estas alertas permiten tomar decisiones informadas sin automatismos peligrosos.

**Herramientas de gestión de costes**:
- AWS Cost Explorer (básico, incluido)
- Aptio (IBM), CloudHealth (Broadcom): 2-4% del gasto total

## Identidad y Acceso

###1. Identidad (Identity)

Un elemento (persona, aplicación, servicio) que pretende realizar acciones en el sistema. Requiere ser distinguible de otros elementos.

###2. Autenticación (Authentication)

Proceso de demostrar mediante credenciales que eres quien dices ser. Verifica la identidad.

###3. Autorización (Authorization)

Lista de acciones que una identidad autenticada tiene permiso para realizar.

## AWS Identity Center

**AWS Identity Center** (anteriormente AWS SSO) es el servicio que gestiona la autenticación de humanos para acceder a las cuentas de AWS.

Características:
- Pertenece a una región específica (elegir cualquiera excepto us-east-1/Virginia)
- Se integra con proveedores de identidad externos mediante SAML
- Permite Single Sign-On (SSO)

###1. Integración con Entra ID (Azure Active Directory)

El patrón recomendado es:
1. Los usuarios están dados de alta en Entra ID (Microsoft)
2. Identity Center se conecta a Entra ID
3. Los usuarios usan sus credenciales corporativas para acceder a AWS
4. El MFA se gestiona en Entra ID, no en AWS

**Alternativas**: También soporta Active Directory on-premise, Okta, y otros proveedores SAML.

###2. Directorio Interno

Identity Center incluye un directorio simple para pruebas, pero **no debe usarse en producción**. Requiere:
- Crear usuarios manualmente uno a uno
- Configurar MFA por usuario
- No tiene las capacidades empresariales de un directorio real

## Permission Set

Un **permission set** es un documento que define qué acciones están autorizadas. Es equivalente a un rol en Azure, pero AWS usa terminología distinta.

Componentes:
- **Policies**: Lista específica de permisos (ej: `Billing` para ver costes)
- **Scope**: Cuentas sobre las que aplican estos permisos

Ejemplo: Permission set "Auditoría" que permite ver costes pero no crear recursos.

**Permission sets predefinidos** comunes:
- Billing (ver costes)
- AdministratorAccess
- PowerUserAccess
- ReadOnlyAccess

## Grupos

Un **grupo** es un conjunto de usuarios que comparten:
- Un permission set específico
- Acceso a un conjunto de cuentas

Flujo de asignación:
```
Usuario (Alice) 
  → Grupo (Auditoría Cuentas)
    → Permission Set (Billing)
      → Cuentas (Garrapinillos)
```

**Resultado**: Alice puede acceder a la cuenta de Garrapinillos con permisos de billing.

###1. Mapeo desde Entra ID

Cuando se integra con Entra ID:
- Los grupos de Entra ID se mapean a permission sets
- Los usuarios del grupo heredan automáticamente el acceso
- No es necesario crear usuarios individuales en AWS

## Proceso de Acceso con Identity Center

1. El usuario accede a la URL del Identity Center (ej: `https://d-xxxx.awsapps.com/start`)
2. Introduce su usuario nominal (ej: `alice.wonderland`)
3. Es redirigido a la página de login de su organización (Entra ID)
4. Completa la autenticación (incluyendo MFA si está configurado)
5. Ve una lista de cuentas disponibles
6. Selecciona una cuenta y un permission set
7. Accede a la consola de AWS con esos permisos

**Cambio de "gorra"**: Un mismo usuario puede tener múltiples permission sets en la misma cuenta, permitiendo cambiar de nivel de permisos según necesidad.

## Organization Account Access Role

Cuando se crea una cuenta desde Organizations, automáticamente se genera un **Organization Account Access Role** que permite a la management account administrar la nueva cuenta sin necesidad del root user.

**Nota**: Este concepto se mencionó pero no se explicó en detalle durante la sesión.

## Cuotas (Quotas)

Las **cuotas** son límites técnicos (ej: máximo 20 CPUs) que:
- **NO sirven** para control de costes en producción
- Solo son útiles para entornos no productivos
- Se suben automáticamente en entornos productivos
- Son diferentes del concepto de límites presupuestarios

## Breaking Glass

Una cuenta de emergencia (breaking glass) es:
- Una identidad local en AWS (no integrada con Entra ID)
- Protegida con MFA guardado en lugar ultraseguro
- Solo para usar si falla completamente el sistema de autenticación principal

**Debate**: No todos los clientes implementan esto; algunos prefieren depender del soporte de AWS.

## Buenas Prácticas Resumidas

1. **Nunca usar root user** excepto para configuración inicial
2. **Siempre configurar MFA** en identidades críticas
3. **Tags obligatorios** en todas las cuentas (especialmente centro de coste)
4. **Alertas de costes** desde el día uno
5. **Integrar con Entra ID** en lugar de gestionar usuarios en AWS
6. **Estructura de OUs** que refleje la organización real
7. **Permission sets específicos** en lugar de permisos administrativos globales
8. **Documentar todo** porque la complejidad crece exponencialmente

## Comparación con Azure

| Concepto AWS | Equivalente Azure |
|---|---|
| Account | Resource Group (o Subscription) |
| Management Account | - |
| Organizational Unit | Management Group |
| Root User | Global Admin (pero con diferencias) |
| Permission Set | Role (RBAC) |
| Identity Center | Azure AD / Entra ID integration |
| Organizations | Tenant + Management Groups |

**Diferencia clave**: Azure diseñó su sistema de identidades desde el principio con 30 años de experiencia en gestión de identidades. AWS lo fue construyendo orgánicamente, añadiendo capas de forma retrocompatible, resultando en mayor complejidad pero funcionando sorprendentemente bien.

---

## El Problema: Múltiples Capas de Identidad

Cuando hablas de "identidad" en AWS, la gente normalmente piensa en crear un usuario y darle una contraseña. Incorrecto. En AWS coexisten varias capas de identidad simultáneamente, y cada una opera en un nivel diferente:

- La identidad que uses para **acceder a la consola web** (tu usuario nominal, como "alice.wonderland")
- La identidad **dentro de cada cuenta** que realiza acciones (un rol IAM)
- Potencialmente, una identidad **local de respaldo** para emergencias

Entender esta distinción es crítico porque cuando alguien te pregunta "¿quién accedió al bucket de S3?", la respuesta incluye información de todas estas capas. Y si no comprendes cómo se relacionan, nunca entenderás completamente dónde está el problema cuando hay un incidente de seguridad.

## El Viaje de una Acción

Para entender por qué esto es así, hagamos un viaje mental. Imagina que necesitas crear un servidor dentro de una cuenta específica de AWS:

1. **Punto de partida**: Eres Alice Wonderland, una usuaria corporativa con el username `alice.wonderland`
2. **Acceso inicial**: Entras a Identity Center (el portal SSO) con tu username
3. **Cambio de identidad**: Identity Center consulta con tu proveedor de identidad (Entra ID, SAML, etc.) y confirma quién eres (autenticación)
4. **Selección de contexto**: El sistema te muestra las cuentas y permission sets disponibles
5. **Asumir un rol**: Cuando entras en una cuenta específica, **dejas de ser Alice** y asumes una identidad distinta: un **rol IAM** válido solo dentro de esa cuenta
6. **Acción protegida**: Cuando intentas crear un servidor, el sistema de IAM de esa cuenta verifica si el rol que asumiste tiene permiso para crear servidores
7. **Auditoría**: En los registros de auditoría (CloudTrail), queda registrado que Alice Wonderland asumió el rol "Administrator" y luego ejecutó la acción "CreateInstance"

Este viaje de transformación de identidades es la característica central de la seguridad en AWS. Sin él, no habría forma de auditar quién hizo qué. Sin él, no podrías tener sandbox separados del código de producción manteniendo el control sobre quién accede a cada uno.

## Identity Center vs. IAM

Este es el punto donde la mayoría se pierden. Hay dos servicios distintos tratando cuestiones de identidad:

**Identity Center**: Gestiona **quién eres externamente**. Es el portal SSO. Es lo que ves cuando abres la URL `https://d-xxxx.awsapps.com/start` y escribes tu username (es común hacerlo coincidir con tu dirección de correo corporativo).

**IAM (Identity and Access Management)**: Gestiona **quién eres dentro de una cuenta específica**. Es un servicio local a cada cuenta. No ves la interfaz de IAM cuando loguearte; la ves cuando ya estás dentro.

### ¿Por qué dos servicios?

Por motivos históricos y de diseño. AWS creció orgánicamente, añadiendo capas de seguridad con el tiempo. Identity Center es relativamente nuevo (2019) y se creó para facilitar la gestión de acceso a múltiples cuentas. IAM existe desde los primeros días de AWS y es el sistema fundamental de control de acceso dentro de cada cuenta.

A mí me gusta imaginarme el IAM como la guarda que espera en la puerta del edificio (una cuenta particular sobre la que quieres actuar). Antes de poder hacer nada dentro de dicha cuenta (como crear un servidor), tienes que elegir una de las chapas de identificación (roles IAM) que el Identity Center le indicó a la guarda que tienes permiso para usar (*asumir*, en la terminología de AWS).

## El Rol es lo Importante

**Lo que realmente importa es el rol IAM, no tu identidad corporativa.**

Cuando ejecutas una acción en AWS, lo que el sistema comprueba es:
- ¿Tienes un rol IAM válido en esta cuenta?
- ¿Ese rol tiene permiso para hacer lo que intentas?
- ¿El rol **no** tiene un *Deny* explícito para esa acción?

Tu nombre (Alice Wonderland) es irrelevante en ese momento. Es como mostrar la chapa del FBI antes de entrar a la oficina: la credencial es lo importante, no la cara detrás de ella.

## El IAM Explicado

**IAM** es el servicio dentro de cada cuenta que:
- Define quiénes son las identidades de seguridad válidas (roles)
- Asigna permisos a esas identidades
- Autentica y autoriza acciones

### Conceptos Clave

**Rol**: Una identidad de seguridad dentro de una cuenta específica. Es como un documento de identificación que otorga ciertos permisos.

Características de un rol:
- Es local a una cuenta (existe solo en esa cuenta)
- Puede ser asumido por una identidad (un usuario, otra cuenta, o un servicio)
- Contiene políticas que definen qué acciones permite

**Policy**: Un documento JSON que lista exactamente qué acciones pueden o no ejecutarse.

Estructura simple:
```json
{
  "Effect": "Allow",
  "Action": "s3:GetObject",
  "Resource": "*"
}
```

Traducción: "Permite la acción GetObject (descargar un objeto) en cualquier recurso de S3"

**Trust Relationship**: Define quién puede asumir este rol.

Ejemplo:
```json
{
  "Principal": "arn:aws:iam::123456789:root",
  "Effect": "Allow",
  "Action": "sts:AssumeRole"
}
```

Traducción: "El root de esta cuenta puede asumir este rol". El uso de `root` aquí es genérico y no se refiere al usuario root, es una forma de decir "cualquier identidad dentro de la cuenta `123456789` referida" y en particular lo utiliza el Identity Center para asignar roles. Calma, esto no es muy importante ahora mismo.

### La Convención de Nombres

Cuando creas un rol desde un Permission Set en Identity Center, AWS genera nombres como:

```
AWSReservedSSO_StaticWeb_abcdef123456g789hij
```

Estructura:
- `AWSReservedSSO_`: Prefijo que indica que fue creado por Identity Center
- `StaticWeb`: El nombre del Permission Set
- `abcdef123456g789hij`: ID único (evita colisiones si hay múltiples Permission Sets con el mismo nombre)

No necesitas memorizar estos nombres, pero es útil saberlos cuando inspecciones los logs de auditoría.

## Más sobre los Permisssion Sets

Un **Permission Set** es el documento que dice qué puede hacer un grupo en qué cuentas.

### Características

- **Agnóstico de cuenta**: Defines un Permission Set una sola vez
- **Reutilizable**: Se aplica a múltiples grupos
- **Temporal**: Puede requerir renovación cada X horas
- **Auditable**: Cuando se asigna, Identity Center automáticamente crea los roles en cada cuenta

### El Proceso Automático

Cuando asignas un Permission Set "Admin" al grupo "Desarrolladores" en las cuentas "Sandbox" y "Garrapinillos":

1. Identity Center recibe el comando
2. Identity Center contacta con Organizations
3. Para cada cuenta, Identity Center:
   - Entra en la cuenta
   - Crea un rol IAM con las políticas del Permission Set
   - Configura el Trust Relationship para permitir que Identity Center lo asigne
4. Tres minutos después, todos los desarrolladores pueden elegir entre ambas cuentas

No necesitas tocar nada dentro de cada cuenta. Se hace automáticamente.

### Permission Sets Predefinidos

AWS viene con Permission Sets listos:
- `AdministratorAccess`: Acceso total (equivalente a \*)
- `PowerUserAccess`: Todo excepto cambiar usuarios/roles
- `ReadOnlyAccess`: Solo lectura
- `Billing`: Solo ver costes

## El Flujo Completo: De la Puerta a la Acción

Visualicémoslo paso a paso:

**Paso 1: Authentication** (Autenticación)
```
Tú → Identity Center → "¿Quién eres?"
Tú → "alice.wonderland"
Tú → Identity Center → Entra ID → "¿Contraseña?"
Tú → Contraseña + MFA
Entra ID → Identity Center → "Válido"
```

**Paso 2: Selection** (Selección)
```
Identity Center → "¿A qué cuenta quieres acceder?"
Te muestra:
  - Cuenta: Sandbox, Permission Set: StaticWeb
  - Cuenta: Sandbox, Permission Set: Administrator
  - Cuenta: Garrapinillos, Permission Set: Administrator
```

**Paso 3: Role Assumption** (Asumir Rol)
```
Tú → "Quiero acceder a Sandbox con StaticWeb"
Identity Center → Sandbox IAM → "Crea un rol temporal para Alice"
Sandbox IAM → "Generando credenciales temporales..."
Tú recibes:
  - AccessKeyId
  - SecretAccessKey
  - SessionToken (caduca en X horas)
```

**Paso 4: Action** (Acción)
```
Tú → "Quiero crear un servidor"
Sandbox IAM → Verifica las credenciales
Sandbox IAM → Lee el rol que asumiste
Sandbox IAM → Lee las políticas del rol
Sandbox IAM → "¿Permite crear servidores? Sí"
EC2 Service → "Creando servidor..."
CloudTrail → "Usuario Alice Wonderland (rol Administrator) ejecutó CreateInstance"
```

## Cuando Todo Falla: Breaking Glass

¿Y si el IdP (proveedor de identidades, como Entra Id) se cae? ¿Y si el servicio de Identity Center explota? ¿Cómo accedes a tu infraestructura crítica? ¿eh?

Para eso existe el **Breaking Glass**: Una identidad en el IAM local de una determinada cuenta que no depende de nada externo.

Características:
- Es un usuario/rol creado directamente en IAM
- Tiene MFA activado (más te vale)
- Las credenciales (físicas o digitales) se guardan en un lugar ultra-seguro (caja de seguridad, bóveda de emergencia)
- Solo se usa cuando absolutamente nada funciona

La decisión de implementar Breaking Glass depende de tu risk tolerance. Netflix utiliza este patrón. Una startup típica de 10 personas probablemente no lo necesita. Pero si tu SLA incluye "99.99% uptime", tendrás que añadirlo.

## Buenas Prácticas

1. **Nunca usar root user** directamente en cuentas de producción
2. **Siempre asumir roles**, nunca trabajar directamente como usuario IAM local
3. **Documentar quién necesita qué**: Permission Set, cuentas, renovación (si aplica)
4. **Revisar acceso regularmente**: Trimestralmente mínimo
5. **Auditar con CloudTrail**: Si no sabes quién hizo qué, no tienes seguridad
6. **MFA en todo**: Identity Center, Breaking Glass, todo

## Comparación: AWS vs Azure

| Aspecto | AWS | Azure |
|---|---|---|
| Portal SSO | Identity Center | Azure AD / Entra ID |
| Roles por cuenta | IAM (local) | No existe nivel equivalente |
| Permission Sets | Concepto explícito | Implícito en RBAC |
| Complexity | Alta (crecimiento orgánico) | Más limpia (diseño original) |
| Auditoría | CloudTrail (excelente) | Activity Log |

**Conclusión**: AWS es más complejo porque creció durante 20 años. Pero una vez lo entiendes, ofrece un control granular imposible de conseguir en otros sitios. Azure es más directo, pero menos flexible.

---

# Políticas de Recursos: Autenticación y Autorización a Nivel de Recurso

## El Problema: ¿Quién Puede Acceder a Qué?

Hasta ahora hemos hablado de autenticación y autorización como algo que sucede a nivel de usuario/rol:
- Alice Wonderland asume el rol "Administrator"
- El rol tiene una política que dice "puede crear servidores"
- Por lo tanto, Alice puede crear servidores

Esto funciona perfectamente cuando controlamos quién accede al sistema. Pero ¿qué pasa con escenarios como este:

**Escenario 1: Tu página web pública**
Necesitas que 8 mil millones de personas en Internet descarguen una foto de tu producto. Pero no quieres crearles credenciales de AWS a todas. De hecho, ni siquiera quieres que sepan que usan AWS.

**Escenario 2: Un servicio compartido**
Tu equipo de auditoría necesita leer logs de costes sin poder crear ni modificar nada. Pero esos logs están mezclados con datos sensibles que otros equipos guardan en el mismo bucket.

**Escenario 3: Integración entre servicios**
Tu aplicación web (corriendo en un servidor EC2) necesita leer imágenes de un bucket S3. La aplicación tiene credenciales, pero no quieres darle permiso para borrar datos ni acceder a otros buckets.

En todos estos casos, necesitas un mecanismo distinto. No puedes asignar permisos en el usuario/rol porque:
- El usuario no existe (internet)
- Quieres permisos ultra-específicos
- Necesitas un punto de control en el recurso mismo

**Solución: Políticas de Recursos (Resource Policies)**

## Dos Tipos de Autenticación

Hasta ahora vimos que la autenticación ocurría en **dos capas**:

1. **Capa de Identidad**: ¿Eres realmente quién dices ser? (Identity Center → Entra ID)
2. **Capa de Autorización**: ¿Qué puedes hacer? (IAM roles con políticas)

Ambas están "en el usuario". El usuario dice "soy Alice", demuestra que es Alice, y luego comprobamos qué puede hacer Alice.

Pero los recursos en AWS pueden tener **su propia capa de autorización**:

3. **Capa de Recurso**: ¿Permite este recurso específico lo que intentas hacer?

Esto es crucial porque permite algo imposible de otra forma: **otorgar permisos sin conocer la identidad del solicitante**.

## La Demo: Punto de Partida

Recapitulemos lo que hicimos:

1. **Creamos buckets** en la cuenta Sandbox
2. **Subimos archivos** (HTML, imágenes) usando nuestro rol
3. **Necesitábamos hacerlo público** para que cualquiera lo viera

En el paso 3, pasó algo especial. Veamos qué.

## Autenticación vs. Autorización en S3

### ¿Quién accede a S3 en nuestro caso?

La gente en Internet. No tienen credenciales de AWS. No están en Entra ID. No tienen un rol asignado.

Para ellos, el flujo tradicional de autenticación es irrelevante:

```
Internet User → S3 → "¿Quién eres?" 
Internet User → "No sé, soy anónimo"
S3 → "Vale, voy a comprobar mi política de bucket"
S3 Lee su política → "Permite a cualquiera (principal: *) descargar objetos (GetObject)"
S3 → "Adelante, toma el archivo"
```

Nota algo importante: **S3 ni siquiera pidió credenciales**.

### El Principal

En las políticas de AWS, `Principal` es quien va a realizar la acción:

```json
{
  "Principal": "*",
  "Action": "s3:GetObject",
  "Effect": "Allow",
  "Resource": "arn:aws:s3:::mi-bucket/*"
}
```

`Principal: "*"` significa "cualquiera, autenticado o no". `Principal: "*"` es el que permite acceso anónimo.

## De la Teoría a la Práctica: La Demo

Aquí es donde entendemos realmente qué pasó en clase.

### Paso 1: Todos los Estudiantes Asumen Roles

Cada uno de vosotros entrasteis con vuestra identidad corporativa. Entrásteis en la **misma cuenta** (Sandbox) con el **mismo Permission Set** (StaticWeb).

Resultado: Todos asumisteis el rol `AWSReservedSSO_StaticWeb_xxxxx` en la misma cuenta.

### Paso 2: Vosotros Creáis Buckets

Con ese rol, teníais permiso para crear buckets S3 (incluido en el Permission Set).

```
Vosotros (con rol StaticWeb) → CreateBucket en S3
IAM → Comprueba si el rol StaticWeb permite CreateBucket
IAM → Sí, la política incluye s3:* (todo en S3)
S3 → Crea el bucket
```

Cada uno creasteis un bucket: `elena-web`, `marco-web`, etc.

**Aquí viene lo interesante**: Si intentabas listar los buckets, ¿qué veías?

### Paso 3: El Descubrimiento

Esperabas ver solo tu bucket. En cambio, veías **todos los buckets de toda la clase**.

¿Por qué? Porque todos los buckets están en **la misma cuenta** (imagínatela de nuevo como un edicificio con una sola puerta) y todos habéis creado buckets dentro de esa cuenta. Por ello, cuando revisas qué buckets existen, el sistema responde con todos los que hay en la cuenta (todos los que se han creado en el mismo edificio).

```
Tú → ListBuckets
IAM → Tu rol permite s3:*
IAM → Sí, puede listar
S3 → Devuelve todos los buckets de la cuenta
```

S3 no distingue entre "mi" bucket y "tu" bucket a nivel de IAM. Solo ve "buckets en la cuenta".

### Paso 4: Aquí Entra la Magia - Política de Bucket

Queríamos usar S3 para publicar nuestra web, así que necesitabais hacer vuestros buckets públicos. Si lo recuerdas, modificaste **el bucket mismo** con una **Bucket Policy**.

Fuisteis a: Bucket → Permissions → Bucket Policy → Edit

Y pegasteis una política JSON que esencialmente decía algo similar a esto:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "PublicReadGetObject",
      "Effect": "Allow",
      "Principal": "*",
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::mi-bucket/*"
    }
  ]
}
```

Traducción literal: "Permite a CUALQUIERA descargar cualquier objeto de este bucket".

### Paso 5: ¿Qué Sucede Cuando Alguien Accede?

Ahora, cuando una persona en Internet intenta descargar tu página:

```
Internet User → GET /index.html desde mi-bucket.s3.amazonaws.com
S3 recibe la solicitud → "¿Quién eres?"
Usuario → "No sé, soy anónimo"
S3 → "OK, voy a comprobar si permito a anónimos descargar"
S3 Lee la política del bucket → "Principal: *, Action: GetObject - SÍ"
S3 → Devuelve index.html
```

**Punto clave**: S3 **ni siquiera pregunta por credenciales** porque la política permite a al *principal* `*` (cualquiera, incluyendo *anónimos*) acceder a S3.

## El Modelo de Control de Acceso

AWS usa lo que se llama el **evaluation logic**:

```
¿Hay una política explícita en el usuario/rol que PERMITA? ✓
└─ Sí → ¿Hay una política explícita que NIEGUE? ✗
   └─ Sí → DENEGADO
   └─ No → PERMITIDO
   
¿No hay política de permiso en el usuario?
└─ Comprueba la política del recurso
   └─ ¿Permite el recurso? ✓
   └─ Sí, y no hay DENEGACIÓN explícita → PERMITIDO
   └─ No → DENEGADO
   
Ni usuario ni recurso permiten → DENEGADO (por defecto)
```

**En nuestro caso**:

```
Internet User → Intenta descargar de mi-bucket
IAM → "¿Quién eres?"
Usuario → "Anónimo"
IAM → "No tienes rol, no tienes políticas de usuario"
IAM → DENEGADO por defecto
S3 → Espera, ahora reviso MI política de bucket
S3 → "Principal: * permite GetObject"
S3 → PERMITIDO
```

Sin la política de bucket, el resultado sería DENEGADO en la segunda línea.

Con la política de bucket, se revisa esa y se permite.

## Más Allá de lo Público: Políticas Sofisticadas

Las políticas no tienen que ser solo "público o privado". Pueden ser muy específicas:

### Ejemplo 1: Solo lectura desde una IP específica

```json
{
  "Principal": "*",
  "Action": "s3:GetObject",
  "Resource": "arn:aws:s3:::logs-bucket/*",
  "Condition": {
    "IpAddress": {
      "aws:SourceIp": "203.0.113.0/24"
    }
  }
}
```

Traducción: "Cualquiera puede descargar, pero solo si viene desde la red 203.0.113.0/24".

### Ejemplo 2: Solo si accedes mediante HTTPS

```json
{
  "Principal": "*",
  "Action": "s3:GetObject",
  "Resource": "arn:aws:s3:::secure-bucket/*",
  "Condition": {
    "Bool": {
      "aws:SecureTransport": "true"
    }
  }
}
```

Traducción: "Descarga permitida, pero solo si usas HTTPS".

### Ejemplo 3: Denegar a propósito (el peor enemigo es uno mismo)

```json
{
  "Effect": "Deny",
  "Principal": "*",
  "Action": "s3:DeleteObject",
  "Resource": "arn:aws:s3:::critical-backups/*"
}
```

Traducción: "Nadie, ni siquiera con credenciales administrativas, puede borrar nada en este bucket". Útil para backups críticos.

## El Contexto de la Demo: Capas de Identidad

Recapitulemos lo que aprendemos con la demo:

### Capa 1: Identity Center (tu identidad corporativa)
- Eres Alicia, Bruno, Carla, etc.
- Te autentificas con Entra ID
- Ves el portal de AWS

### Capa 2: IAM Role (tu identidad en esta cuenta)
- Asumes el rol `AWSReservedSSO_StaticWeb_xxxxx`
- Tienes permisos: crear buckets, subir objetos, crear políticas
- Puedes hacer estas cosas porque tu rol lo permite

### Capa 3: Bucket Policy (controles del recurso)
- El bucket dice: "Principal: * puede descargar"
- Internet User (sin credenciales) → Comprueba la política → Permitido

**La importancia**: Sin la política de bucket, Internet User sería denegado en la capa 2 (no tiene rol IAM).

Con la política de bucket, se salta la capa 2 y va directo: "¿Dice la política del bucket que puedo? Sí → Adelante".

## Comparación: Usuario IAM vs. Política de Recurso

| Aspecto | Política de Usuario/Rol | Política de Recurso |
|---|---|---|
| Se define en | IAM → Roles | El recurso (Bucket, Topic, etc.) |
| Se aplica a | Un rol o usuario específico | Cualquier Principal |
| Cuándo se revisa | Siempre (es la primera línea) | Si la política de usuario lo permitió, O si no hay usuario |
| Ejemplo de uso | "Los desarrolladores pueden crear servidores" | "Este bucket es público" |
| Seguridad | Fácil de monitorear centralmente | Riesgo si lo olvidas |

## Resumen: Autenticación Multinivel

En AWS, la autenticación y autorización no son un solo check:

1. **¿Quién eres?** (Identity Center / Entra ID)
2. **¿Tienes un rol en esta cuenta?** (IAM)
3. **¿Tu rol lo permite?** (Políticas de rol)
4. **¿El recurso lo permite?** (Políticas de recurso)

Todas estas capas pueden decir "no" en cualquier momento. Solo si **todas dicen sí** (o no aplican) se permite la acción.

## Implicaciones para la Seguridad

### Responsabilidad

Cada capa es responsabilidad de distintas personas:

- **Capas 1-2**: Administrador de identidades (se integra Identity Center con Entra ID)
- **Capas 3**: Arquitecto de seguridad (definen roles y permisos)
- **Capa 4**: Dueño del recurso (configura política del bucket)

Si cualquiera de ellas falla, tienes un problema.

### Auditoría

CloudTrail es el servicio de auditoría de AWS que (si lo activas, ya que tiene coste) registra todo lo que sucede en tu organización. Con CloudTrail, ves:

```
User: Elena
AssumedRole: AWSReservedSSO_StaticWeb_xxxxx
Action: PutBucketPolicy
Resource: arn:aws:s3:::elena-web
Result: Success
```

Esta entrada explica que Elena, usando su rol, cambió la política del bucket `elena-web`.

### La Sorpresa

Muchas intrusiones suceden cuando alguien:
1. Roba credenciales
2. Asume un rol con acceso amplio
3. Luego se aprovecha de políticas de recurso permisivas

Ejemplo: Un atacante roba credenciales de un dev, asume su rol (que tiene acceso a S3), y ve que un bucket tiene una política que permite descargar a cualquiera. Aunque el atacante no estuviera "autorizado" por el bucket policy, el rol del dev SÍ lo está, entonces puede descargar.


## Buenas Prácticas para Políticas de Recurso

1. **Siempre especifica el Principal**: Nunca uses `*` a menos que sea realmente público
2. **Sé específico con Actions**: No uses `s3:*`, usa solo lo que necesitas (`s3:GetObject`)
3. **Sé específico con Resources**: No uses `arn:aws:s3:::*`, usa el bucket exacto
4. **Revisa regularmente**: CloudTrail te dirá quién cambió la política
5. **Usa Conditions**: Restringe por IP, HTTPS, hora del día, etc. si es posible
6. **Denegar siempre que sea crítico**: Para backups o datos muy sensibles, usa `Deny` explícitamente
7. **Documenta el por qué**: Cada política debería tener un `Sid` claro

## La Moraleja

La demo de crear un sitio web estático en S3 NO era realmente sobre S3. Era sobre entender que **la autorización en AWS ocurre en múltiples capas**, y que el concepto de "política de recurso" es fundamentalmente distinto de "política de rol".

Una vez entiendas esto, entiendes por qué:
- Puedes hacer cosas públicas sin dar acceso a usuarios reales
- Puedes compartir un recurso entre cuentas
- Puedes tener "emergencias" donde ciertas acciones se permitensiempre
- Puedes auditar exactamente qué sucedió y cuándo

Es la base de cómo AWS construye confianza en sistemas complejos con cientos de personas, máquinas, y cuentas.