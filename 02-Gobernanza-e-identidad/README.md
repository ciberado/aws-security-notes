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

## 1. Gobernanza

La gobernanza en cloud consiste en establecer reglas y mecanismos para controlar el caos natural que tiende a generarse. Se compone de tres pilares fundamentales:

- **Organización**: Cómo distribuimos y agrupamos elementos
- **Costes**: Monitorización y control del gasto
- **Seguridad**: Incluyendo identidad, autenticación y autorización

**Nota crítica sobre costes**: La monitorización de costes es detección de intrusión. Un gasto anómalo (como pasar de $5 a $310,000 en un fin de semana) suele indicar credenciales comprometidas usadas para minado de criptomonedas. AWS puede perdonar estos cargos si se reportan inmediatamente, pero es responsabilidad del cliente configurar alertas.

## 2. Account (Cuenta de AWS)

Una **account** es un grupo de recursos de infraestructura relacionados entre sí. A diferencia de otros contextos, en AWS "cuenta" NO se refiere a una identidad de usuario, sino a un contenedor organizativo similar a un Resource Group en Azure.

Ejemplos de uso:
- Infraestructura de una aplicación en desarrollo
- Infraestructura de la misma aplicación en producción
- Entorno experimental de un desarrollador

**Concepto clave**: En organizaciones grandes, pueden existir cientos o miles de cuentas. No es raro que Netflix tuviera ~14,000 máquinas con CPU al 0% olvidadas.

## 3. Root User

El **root user** es la identidad que crea una cuenta específica y tiene poderes omnipotentes sobre ella, sin posibilidad de restricción de permisos.

Características:
- Cada cuenta tiene su propio root user distinto
- Está asociado a una dirección de correo electrónica
- **Nunca debe usarse** para operaciones cotidianas
- Debe protegerse con MFA (autenticación multifactor)
- Utiliza direcciones genéricas y no personales (ej: `ops-lead@example.com`), dado que las personas cambiamos de puesto y empresa.

**Truco del email**: Puedes usar el formato `usuario+alias@dominio.com` para crear múltiples root users con una sola dirección de correo (ej: `javi.moreno+aldebaran@aprendercloud.com`).

## 4. AWS Organizations

**AWS Organizations** es el servicio que permite crear y gestionar jerarquías de cuentas de forma centralizada, sin necesidad de repetir el proceso manual de creación para cada una.

### 4.1. Management Account

La **management account** es la primera cuenta que se crea y establece la relación inicial entre una empresa y AWS. Desde ella se activa Organizations y se puede:
- Crear nuevas cuentas sin introducir tarjeta de crédito
- Gestionar la jerarquía organizativa
- Tener visibilidad global de costes

**Importante**: El término "root account" en Organizations no tiene nada que ver con "root user". Ahora se llama "management account" para evitar confusión.

### 4.2. Organizational Unit (OU)

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

### 4.3. Tags (Etiquetas)

Los **tags** son metadatos clave-valor que se asignan a recursos y cuentas. Son críticos para:
- Identificar centro de coste (quién paga)
- Realizar chargeback (facturación interna)
- Organizar y filtrar recursos

Ejemplo: `CentroCoste: Fabrica`

## 5. Limitaciones de Gasto

**No es posible establecer límites rígidos de gasto en producción** porque:
- Los recursos se escalan elásticamente según demanda
- Un límite podría apagar la infraestructura en un momento crítico de éxito (aplicación viral)
- Los caps de Azure están pensados solo para desarrollo

**Solución**: Configurar alertas sobre métricas de coste y proyecciones de gasto mensual. Estas alertas permiten tomar decisiones informadas sin automatismos peligrosos.

**Herramientas de gestión de costes**:
- AWS Cost Explorer (básico, incluido)
- Aptio (IBM), CloudHealth (Broadcom): 2-4% del gasto total

## 6. Identidad y Acceso

### 6.1. Identidad (Identity)

Un elemento (persona, aplicación, servicio) que pretende realizar acciones en el sistema. Requiere ser distinguible de otros elementos.

### 6.2. Autenticación (Authentication)

Proceso de demostrar mediante credenciales que eres quien dices ser. Verifica la identidad.

### 6.3. Autorización (Authorization)

Lista de acciones que una identidad autenticada tiene permiso para realizar.

## 7. AWS Identity Center

**AWS Identity Center** (anteriormente AWS SSO) es el servicio que gestiona la autenticación de humanos para acceder a las cuentas de AWS.

Características:
- Pertenece a una región específica (elegir cualquiera excepto us-east-1/Virginia)
- Se integra con proveedores de identidad externos mediante SAML
- Permite Single Sign-On (SSO)

### 7.1. Integración con Entra ID (Azure Active Directory)

El patrón recomendado es:
1. Los usuarios están dados de alta en Entra ID (Microsoft)
2. Identity Center se conecta a Entra ID
3. Los usuarios usan sus credenciales corporativas para acceder a AWS
4. El MFA se gestiona en Entra ID, no en AWS

**Alternativas**: También soporta Active Directory on-premise, Okta, y otros proveedores SAML.

### 7.2. Directorio Interno

Identity Center incluye un directorio simple para pruebas, pero **no debe usarse en producción**. Requiere:
- Crear usuarios manualmente uno a uno
- Configurar MFA por usuario
- No tiene las capacidades empresariales de un directorio real

## 8. Permission Set

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

## 9. Grupos

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

### 9.1. Mapeo desde Entra ID

Cuando se integra con Entra ID:
- Los grupos de Entra ID se mapean a permission sets
- Los usuarios del grupo heredan automáticamente el acceso
- No es necesario crear usuarios individuales en AWS

## 10. Proceso de Acceso con Identity Center

1. El usuario accede a la URL del Identity Center (ej: `https://d-xxxx.awsapps.com/start`)
2. Introduce su email corporativo
3. Es redirigido a la página de login de su organización (Entra ID)
4. Completa la autenticación (incluyendo MFA si está configurado)
5. Ve una lista de cuentas disponibles
6. Selecciona una cuenta y un permission set
7. Accede a la consola de AWS con esos permisos

**Cambio de "gorra"**: Un mismo usuario puede tener múltiples permission sets en la misma cuenta, permitiendo cambiar de nivel de permisos según necesidad.

## 11. Organization Account Access Role

Cuando se crea una cuenta desde Organizations, automáticamente se genera un **Organization Account Access Role** que permite a la management account administrar la nueva cuenta sin necesidad del root user.

**Nota**: Este concepto se mencionó pero no se explicó en detalle durante la sesión.

## 12. Cuotas (Quotas)

Las **cuotas** son límites técnicos (ej: máximo 20 CPUs) que:
- **NO sirven** para control de costes en producción
- Solo son útiles para entornos no productivos
- Se suben automáticamente en entornos productivos
- Son diferentes del concepto de límites presupuestarios

## 13. Breaking Glass

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