# Infraestructura como Código

## Tu Data Center es un Programa

La idea revolucionaria detrás de la infraestructura como código es simple pero poderosa: **podemos convertir nuestro data center en un programa**.

Los proveedores cloud exponen un **API (Application Program Interface)** al que hacemos llamadas HTTPS. ¿Recuerdas cómo funciona esto en mi cabeza? Yo siempre me lo imagino como un robot gigante al que le pedimos que construya piezas: redes, bases de datos, flotas de computación, balanceadores.

El hecho de poder usar HTTPS para dar órdenes al robot y crear data centers significa que **podemos codificar todas esas órdenes en un programa**. Y esto es la clave de todo: **gestionar infraestructura se convierte en un proyecto de software**.

Eso implica que podemos aplicar todo lo que sabemos sobre desarrollo:

- Control de versiones
- Revisiones de código
- Pruebas automatizadas
- Documentación viviente
- Colaboración en equipo

## Imperativo vs Declarativo

### Programación Imperativa

Este es el acercamiento que tomas cuando das instrucciones paso a paso de **qué hacer** y **en qué orden**. Es fácil de entender, piensa que le vas gritando al robot cada uno de los pasos que tiene que llevar a cabo. En programación, esto se traduce en scripts que ejecutan comandos uno tras otro.

**Características:**

- Especificas cada operación individual
- Controlas el orden de ejecución
- Tú decides cómo llegar al resultado final
- El código describe **el proceso**

Pero el problema que te encuentras es que el script no tiene memoria del estado anterior. Si lo ejecutas dos veces, intentará crear todo dos veces. No modifica, solo crea.

### Programación Declarativa

En este caso, describes el **estado final** que deseas. Es como mostrarle una foto al robot: "quiero que el resultado se vea así, tú averigua cómo conseguirlo". En programación, esto se traduce en lenguajes y herramientas que permiten definir la configuración deseada como una estructura de datos. La lista de lo que quieres obtener, vamos. Pero no necesitas decirle cómo llegar ahí.

**Características:**

- Describes qué quieres, no cómo conseguirlo
- La herramienta determina los pasos necesarios
- El código representa **el estado deseado**
- Puedes ejecutarlo mil veces, siempre converge al mismo resultado

Esto tiene una ventaja muy importante: la herramienta mantiene memoria del estado actual y calcula automáticamente qué hay que cambiar, añadir o eliminar. No sirve solo para crear, también permite modificar y borrar recursos según sea necesario generando solo las instrucciones imprescindibles para llevar a cabo esta labor.

Ojo, escribir código imperativo resulta muchas veces útil: por ejemplo, es ideal para automatizar procesos como la generación de backups, la ejecución de tareas repetitivas o críticas (es muy habitual tener un script para aislar máquinas en caso de que se detecte una intrusión). Pero para gestionar infraestructura, el enfoque declarativo es mucho más potente y eficiente.

## El Problema del Día Dos

Vamos a concretar un poco. Imagina que has desplegado tu aplicación. Todo funciona. Pero al día siguiente necesitas hacer un cambio: abrir el puerto 8080 en lugar del 80.

Si editas tu script bash cambiando el puerto y lo ejecutas de nuevo... **crearás recursos duplicados**, no modificarás los existentes.

¿Por qué? Porque estás dando instrucciones de creación. El script no sabe que ya existe un security group. No tiene contexto. Se limita a volver a ejecutar los pasos de nuevo, con las modificaciones que hayas puesto.

La solución tradicional consistía en escribir un SEGUNDO script que localice el recurso existente, elimine la regla antigua y añada la nueva. Y luego un tercero para el siguiente cambio. Y así infinitamente. Es como las migrations de SQL que se acumulan durante años. Un horror de mantenimiento y muy poco eficiente.

Cuando usas un acercamiento declarativo, te limitas a explicar qué es lo que quieres. La herramienta se encarga de calcular las diferencias y aplicar solo los cambios necesarios. Por ejemplo, si describes una regla de security group así:

```hcl
ingress {
  from_port = 8080
  to_port   = 8080
}
```

La herramienta (Terraform) revisará cómo se encuentra configurado ahora mismo ese security group. Digamos que verá que el puerto 80 está abierto y se dará cuenta de que en realidad el que quieres es el 8080. Así  qeu calculará que debe eliminar esa regla y añadir la nueva:

1. Ve el estado actual (puerto 80 abierto)
2. Lee el código (quieres puerto 8080)
3. Calcula: "necesito eliminar regla de 80 y añadir regla de 8080"
4. Lo ejecuta automáticamente

De esta manera **tu código siempre refleja la realidad**. No necesitas scripts de modificación, solo volver a ejecutar el mismo código con los cambios que desees. Terraform (y este tipo de herramientas) se encarga de todo el trabajo sucio de escribir el equivalente al .bash de modificaciones.

## Idempotencia: El Superpoder

Un comando es **idempotente** si ejecutarlo múltiples veces produce el mismo resultado. Terraform es idempotente: ejecuta `apply` mil veces con el mismo código, siempre converge al mismo estado. Si no hay cambios pendientes, no hace nada.

**Por qué importa:** 

- Puedes reejecutar tras fallos sin miedo
- Evitas duplicación accidental de recursos
- El sistema es predecible

## Dependency Graph: Orden Automático

Terraform construye automáticamente un **grafo de dependencias** analizando las referencias entre recursos.

Si una instancia referencia un security group, y ese security group referencia una VPC, Terraform entiende:

1. Primero crear VPC
2. Luego security group
3. Finalmente instancia

**Tú no especificas el orden**. Terraform lo calcula. Y además puede paralelizar recursos independientes. Por supuesto, no puede adivinar dependencias implícitas (como esperar a que un servicio esté listo), pero para la mayoría de casos funciona perfectamente. En el resto, puedes usar `depends_on` para forzar dependencias explícitas cuando sea necesario.

## Servicios adicionales

### IAM Instance Profile: Permisos sin Credenciales

Cuando ejecutas Terraform desde una instancia EC2, necesitas permisos para crear recursos. El **Instance Profile** es el mecanismo para asociar un rol IAM a una instancia. Cualquier aplicación dentro de esa instancia hereda los permisos del rol.

De esta manera, no gestionas credenciales manualmente. No hay claves que rotar, no hay secretos que filtrar. La aplicaciones que se ejecuten en la instancia simplemente tiene los permisos.

### Session Manager: Acceso sin SSH

El Session Manager permite conectar a instancias sin abrir el puerto 22, sin gestionar claves SSH, directamente desde la consola web. Es el modelo moderno de acceso a instancias en AWS.

### User Data: Inicialización Automática

Recuerda que el User Data es un script que se ejecuta automáticamente cuando una instancia arranca por primera vez. Te servirá para tareas de inicialización, en este caso, descargar la aplicación ejecutarla.

### Systems Manager Parameter Store

Parameter Store es un servicio para guardar configuración de aplicaciones: strings, secretos, passwords. En nuestro script, Terraform crea el password, lo guarda en Parameter Store cifrado, la aplicación lo lee desde allí. El password nunca aparece en logs ni en código.

## Limpieza de recursos

El concepto de `destroy` habilita **infraestructura efímera**: data centers que existen solo cuando los necesitas.

- Desarrollo: monta el entorno por la mañana, destrúyelo por la noche
- Testing: crea, prueba, destruye automáticamente
- Demos: levanta para la presentación, elimina después

Quizá tengas que hacer un ligero **cambio de mentalidad:** La infraestructura deja de ser permanente y cara. Pasa a ser temporal y bajo demanda.

## Conclusión

La infraestructura como código transforma la gestión de sistemas de un proceso manual, propenso a errores y difícil de auditar, en un proceso automatizado, versionado y colaborativo.

El salto de imperativo a declarativo es el equivalente a pasar de ensamblador a lenguajes de alto nivel. Sigues teniendo control, pero trabajas en un nivel de abstracción superior.

Recuerda: tu código describe el "qué", las herramientas se encargan del "cómo".