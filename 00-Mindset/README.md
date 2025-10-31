# Apuntes: Introducción al Cloud Computing

## 1. De los Objetivos de Negocio a la Infraestructura Cloud

### El Flujo Completo del Negocio Digital

**Ganar Dinero (Objetivo)** → **Servicios/Productos (Mecanismo)** → **Procesos (Requisito)** → **Aplicaciones (Automatización)** → **Infraestructura (Ejecución)** → **Cloud (Escalabilidad)**

#### Ganar Dinero (Objetivo)
El objetivo fundamental de cualquier empresa es generar valor y obtener beneficios económicos. Este es el punto de partida que define toda la estrategia empresarial.

#### Servicios/Productos (Mecanismo)
Para alcanzar el objetivo de generar ingresos, las empresas crean **servicios** o **productos** que satisfacen necesidades del mercado:
- **Productos**: Bienes tangibles o digitales que se venden (software, apps, hardware, tomates)
- **Servicios**: Prestaciones intangibles que se ofrecen (consultoría, streaming, jardinería)

#### Procesos (Requisito)
Los servicios y productos requieren **procesos empresariales** para su creación, distribución y mantenimiento:
- Procesos de producción
- Procesos de ventas y marketing
- Procesos de atención al cliente
- Procesos de logística
- Procesos de facturación

Sin procesos bien definidos y eficientes, no es posible entregar valor de manera consistente.

#### Aplicaciones (Automatización)
Para optimizar y escalar los procesos, se desarrollan **aplicaciones informáticas** que los automatizan:
- ERP (Enterprise Resource Planning)
- CRM (Customer Relationship Management)
- Plataformas de e-commerce
- Aplicaciones web y móviles
- Sistemas de gestión de contenidos

Las aplicaciones eliminan tareas manuales, reducen errores y permiten operar a mayor escala.

#### Infraestructura (Para ejecutarlas)
Las aplicaciones necesitan **infraestructura tecnológica** para ejecutarse:
- **Servidores**: Máquinas físicas o virtuales que ejecutan el código
- **Almacenamiento**: Sistemas para guardar datos (bases de datos, archivos)
- **Redes**: Conectividad entre componentes y usuarios
- **Sistemas operativos**: Software base que gestiona recursos
- **Middleware**: Software intermediario (balanceadores, colas de mensajes)

#### Cloud (Para no sufrir con nuevos procesos)
El **cloud computing** proporciona infraestructura bajo demanda que elimina las fricciones tradicionales:
- **Sin inversión inicial**: No hay que comprar hardware costoso
- **Escalabilidad inmediata**: Se añaden recursos en minutos
- **Flexibilidad alta**: Se crean y destruyen entornos según necesidad
- **Enfoque en el negocio**: Los equipos se centran en crear valor, no en mantener servidores

Cuando el negocio crece o surgen nuevos procesos, el cloud permite responder rápidamente sin las limitaciones de infraestructura tradicional.

---

## 2. Cloud como Infraestructura 100% Automatizable

### Definición de Public Cloud Computing
El **public** cloud computing es un modelo de provisión de recursos informáticos (servidores, almacenamiento, bases de datos, redes, software) a través de Internet, con las siguientes características:

- **On-demand self-service**: Los usuarios pueden aprovisionar recursos automáticamente sin intervención humana
- **Broad network access**: Accesible desde cualquier dispositivo con conexión a Internet
- **Resource pooling**: Los recursos se comparten entre múltiples clientes de forma transparente
- **Rapid elasticity**: Los recursos se pueden escalar hacia arriba o abajo rápidamente
- **Measured service**: El uso se mide y factura según consumo real

### Automatización al 100%
La característica diferencial del cloud es que **toda la infraestructura es programable**:

- **Infrastructure as Code (IaC)**: La infraestructura se define mediante código
- **APIs de gestión**: Todas las operaciones (crear servidores, configurar redes, desplegar aplicaciones) se realizan mediante llamadas API utilizando el protocolo de la web (HTTPs)
- **Orquestación**: Se pueden automatizar flujos complejos de aprovisionamiento y configuración

**Ejemplo práctico**: Con un script, puedes crear 100 servidores, configurar balanceadores de carga, bases de datos y redes en minutos. Destruirlos todos es igual de sencillo.

### Cloud Público vs Cloud Privado

#### Cloud Público
Infraestructura compartida operada por un proveedor externo (AWS, Azure, GCP):
- **Ventajas**: Sin inversión inicial, máxima escalabilidad, sin mantenimiento físico
- **Casos de uso**: Startups, aplicaciones web, desarrollo y testing
- **Ejemplos**: Amazon Web Services (AWS), Microsoft Azure, Google Cloud Platform (GCP)

#### Cloud Privado
Infraestructura dedicada a una única organización (puede ser on-premises o gestionada):
- **Ventajas**: Mayor control, cumplimiento normativo estricto, personalización total
- **Casos de uso**: Bancos, entidades gubernamentales, empresas con requisitos de seguridad extremos
- **Ejemplos**: VMware vSphere, OpenStack, Azure Stack

#### Cloud Híbrido
Combinación de cloud público y privado:
- Datos sensibles en cloud privado
- Aplicaciones de cara al público en cloud público
- Posibilidad de "cloud bursting" (expandirse al cloud público temporalmente)

---

## 3. El Cloud como Datacenters Dinámicos Configurables por HTTP API

### Cambio de Paradigma
Tradicionalmente, aprovisionar infraestructura requería:
1. Solicitud formal al departamento de IT
2. Aprobación de presupuesto
3. Compra de hardware (semanas/meses)
4. Instalación física en datacenter
5. Configuración manual del sistema

Con cloud computing, este proceso se reduce a:
1. Llamada HTTP API
2. Respuesta con recursos aprovisionados (segundos/minutos)

### APIs HTTP como Interfaz Universal
Todos los proveedores cloud exponen sus servicios mediante **APIs RESTful sobre HTTP/HTTPS**:

```
POST https://ec2.amazonaws.com/
  Action=RunInstances
  &ImageId=ami-12345678
  &InstanceType=t2.micro
  &MinCount=1
  &MaxCount=1
```

Esta llamada crea un servidor virtual en AWS.

### Ventajas del Modelo API

#### 1. Programabilidad
Cualquier lenguaje de programación puede interactuar con el cloud:
- Python con boto3 (AWS SDK)
- JavaScript con AWS SDK
- Terraform (lenguaje declarativo)
- CLI tools (aws cli, az cli, gcloud)

#### 2. Repetibilidad
El mismo código produce siempre el mismo resultado, eliminando errores de configuración manual.

#### 3. Versionado
La infraestructura definida en código se guarda en repositorios Git, con historial completo de cambios.

#### 4. Testing
Se pueden probar configuraciones de infraestructura antes de aplicarlas en producción.

#### 5. Velocidad
Crear entornos completos (desarrollo, staging, producción) en minutos en lugar de semanas.

### Datacenters Dinámicos
El cloud funciona como un **datacenter virtual** que se reconfigura constantemente:
- **Elasticidad**: Los recursos aparecen y desaparecen según demanda
- **Multi-tenancy**: Miles de clientes comparten la misma infraestructura física de forma aislada
- **Abstracción**: Los usuarios no saben (ni necesitan saber) en qué servidor físico corren sus aplicaciones
- **Orquestación automática**: El proveedor mueve cargas de trabajo entre servidores físicos para optimizar recursos

---

## 4. Cloud Players Principales y Otros Proveedores

### Los Tres Grandes (Hiperescaladores)

#### Amazon Web Services (AWS)
- **Lanzamiento**: 2006 (pionero del cloud público)
- **Cuota de mercado**: ~32% (líder mundial)
- **Fortalezas**: 
  - Mayor catálogo de servicios (+200)
  - Ecosistema más maduro
  - Mejor documentación y comunidad
  - Innovación constante
- **Casos de uso típicos**: Startups tecnológicas, empresas nativas digitales, big data
- **Servicios destacados**: EC2 (compute), S3 (storage), Lambda (serverless), RDS (databases)

#### Microsoft Azure
- **Lanzamiento**: 2010
- **Cuota de mercado**: ~23%
- **Fortalezas**:
  - Integración con ecosistema Microsoft (Windows, Active Directory, Office 365)
  - Excelente para empresas tradicionales con infraestructura Microsoft
- **Casos de uso típicos**: Grandes corporaciones, migraciones de entornos Windows
- **Servicios destacados**: Azure VMs, Azure AD/Entra ID, Azure SQL Database

#### Google Cloud Platform (GCP)
- **Lanzamiento**: 2011
- **Cuota de mercado**: ~10%
- **Fortalezas**:
  - Mejor red global (infraestructura de Google)
  - Excelente para data analytics (BigQuery)
- **Casos de uso típicos**: Data science, machine learning, aplicaciones containerizadas
- **Servicios destacados**: Compute Engine, BigQuery, Cloud Run, Vertex AI

### Comparativa de Cuota de Mercado (2024)
1. AWS: ~32%
2. Azure: ~23%
3. GCP: ~10%
4. Otros: ~35%

### Otros Proveedores Relevantes

#### Oracle Cloud Infrastructure (OCI)
- **Enfoque**: Bases de datos Oracle, aplicaciones empresariales
- **Fortaleza**: Migraciones de Oracle Database, compatibilidad con licencias existentes
- **Target**: Clientes enterprise con inversiones grandes en Oracle

#### VMware
- **Enfoque**: Cloud privado
- **Fortaleza**: Compatibilidad total con infraestructura VMware existente
- **Producto clave**: VMware vSphere, VMware Cloud on AWS
- **Target**: Empresas que quieren mantener gestión VMware en cloud público

#### Otros Proveedores de Nicho
- **Alibaba Cloud**: Líder en China y Asia
- **IBM Cloud**: Fuerte en mainframes y entornos legacy
- **DigitalOcean**: Enfocado en desarrolladores y pequeñas empresas (simplicidad)
- **OVH**: Proveedor europeo, enfoque en privacidad y GDPR

---

## 5. Alquilar en lugar de Comprar: Ventajas e Inconvenientes del Cloud

### Modelo Tradicional: CAPEX (Capital Expenditure)
Comprar y mantener infraestructura propia:
- Inversión inicial alta en hardware
- Depreciación a largo plazo
- Propiedad de los activos
- Control total físico

### Modelo Cloud: OPEX (Operational Expenditure)
Alquilar infraestructura bajo demanda:
- Sin inversión inicial
- Pago por uso mensual
- Sin propiedad de activos
- Gestión delegada al proveedor

---

### Ventajas del Cloud

#### 1. Sin Inversión Inicial (Reducción CAPEX)
- No hay que comprar servidores, almacenamiento, redes
- Barrera de entrada baja para startups
- Presupuesto predecible (OPEX en lugar de CAPEX)

#### 2. Escalabilidad Elástica
- **Vertical**: Aumentar CPU/RAM de un servidor en minutos
- **Horizontal**: Añadir más servidores automáticamente
- **Respuesta a picos**: Black Friday, campañas de marketing, eventos virales
- **Ahorro en valles**: Reducir recursos cuando no se necesitan

#### 3. Agilidad y Velocidad
- Entornos de desarrollo listos en minutos
- Experimentación rápida (fail fast)
- Time-to-market reducido
- Innovación acelerada

#### 4. Alcance Global
- Presencia en múltiples regiones mundiales sin construir datacenters
- Baja latencia para usuarios globales
- Cumplimiento de regulaciones de residencia de datos

#### 5. Sin Mantenimiento de Hardware
- El proveedor se encarga de:
  - Reemplazo de hardware defectuoso
  - Actualizaciones de firmware
  - Refrigeración y electricidad
  - Seguridad física del datacenter

#### 6. Alta Disponibilidad y Disaster Recovery
- SLAs de 99.95% - 99.99% garantizados
- Backups automáticos
- Replicación geográfica sencilla
- Recuperación ante desastres simplificada

#### 7. Acceso a Tecnología Avanzada
- Machine Learning, IA, IoT, Big Data sin inversión en infraestructura especializada
- Servicios gestionados (bases de datos, analytics, containers)

#### 8. Enfoque en Core Business
- Equipos IT centrados en crear valor, no en mantener servidores
- Reducción de personal de operaciones

#### 9. Compliance y Regulación
- Residencia de datos (GDPR requiere datos en UE)
- Certificaciones específicas por sector (HIPAA, PCI-DSS)

---

### Inconvenientes del Cloud

#### 1. Costes a Largo Plazo
- Para cargas de trabajo estables y predecibles, el OPEX acumulado puede superar el CAPEX de comprar hardware
- "Cloud repatriation": Empresas que vuelven a infraestructura propia (Dropbox, Basecamp). En mi opinión esto es factible
solo para empresas de producto que tienen casos de uso muy concretos y bien definidos y además cuentan con equipos técnicos muy especializados.
- Necesidad de optimización constante de costes

#### 2. Dependencia del Proveedor (Vendor Lock-in)
- Servicios propietarios dificultan la migración
- Costes de salida elevados (transferencia de datos)
- Riesgo de cambios de precios unilaterales
- Necesidad de formación específica por proveedor

#### 3. Falta de Control Total
- No se puede acceder físicamente al hardware
- Dependencia de la disponibilidad del proveedor
- Limitaciones en configuraciones muy específicas
- Posibles caídas del servicio fuera de tu control

#### 4. Seguridad y Privacidad
- Datos en infraestructura de terceros
- Riesgos de compliance en sectores regulados
- Posible acceso gubernamental a datos (CLOUD Act en EEUU)
- Shared responsibility model: la seguridad se comparte con el proveedor

#### 5. Conectividad de Red Crítica
- Dependencia total de conexión a Internet
- Latencia variable
- Costes de transferencia de datos (especialmente egress)

#### 6. Complejidad de Gestión
- Facturación compleja (miles de líneas de items)
- Necesidad de gobernanza estricta tanto técnica como económica (FinOps)
- Curva de aprendizaje pronunciada
- Riesgo de gastos descontrolados (bill shock)

---

### Cuándo Usar Cloud vs On-Premises

**Casos Ideales para Cloud:**
- Startups y empresas en crecimiento
- Cargas de trabajo variables o impredecibles
- Desarrollo y testing
- Aplicaciones web y móviles
- Necesidad de alcance global rápido

**Casos donde On-Premises puede tener sentido:**
- Cargas de trabajo estables durante años
- Requisitos regulatorios muy estrictos
- Aplicaciones con latencia ultra-baja (trading de alta frecuencia)
- Empresas con grandes inversiones existentes en datacenters
- Organizaciones con equipos IT especializados en infraestructura

**Solución Híbrida:**
Muchas empresas optan por un modelo híbrido que combina lo mejor de ambos mundos.

