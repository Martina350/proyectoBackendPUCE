# ResourceLoop — Backend

Propuesta de una API REST multitenant para gestionar préstamos de herramientas y equipos compartidos, desarrollada con **Java, Spring Boot y PostgreSQL**.

## Propuesta

ResourceLoop permitirá que universidades, laboratorios, comunidades y empresas administren su inventario, reservas, entregas y devoluciones desde una misma plataforma. Cada organización tendrá sus propios usuarios, sedes, equipos y políticas, con acceso independiente a sus datos.

La primera versión se enfocará en mantener un registro confiable de qué equipos existen, cuándo están disponibles, quién los tiene y en qué condiciones se devuelven.

## Arquitectura y módulos

Se utilizará un **monolito modular**: una aplicación Spring Boot y una base de datos PostgreSQL, con responsabilidades separadas por módulo.

| Módulo | Responsabilidad |
| --- | --- |
| **Organizaciones** | Administrar organizaciones, sedes y políticas de préstamo. |
| **Usuarios** | Gestionar cuentas, membresías, roles y permisos. |
| **Inventario** | Registrar categorías, modelos y unidades físicas con su condición operativa. |
| **Reservas** | Consultar disponibilidad y reservar equipos para un período. |
| **Préstamos** | Registrar entregas, devoluciones, atrasos e incidencias. |

Los módulos colaborarán mediante interfaces definidas y conservarán sus propias reglas de negocio. Esta estructura permitirá ampliar funcionalidades y optimizar la aplicación cuando aumente la demanda.

## Lógica de negocio

- **Aislamiento por organización:** cada operación validará que el usuario tenga acceso a los datos del tenant correspondiente. Una persona podrá pertenecer a varias organizaciones con diferentes permisos.
- **Roles:** el administrador de plataforma gestionará organizaciones; el administrador de organización administrará su operación; el responsable registrará préstamos y devoluciones; el miembro gestionará sus propias reservas.
- **Inventario por unidad:** cada herramienta o equipo físico tendrá un código único dentro de su organización, una sede y una condición: operativa, en mantenimiento, fuera de servicio o retirada.
- **Disponibilidad por fechas:** se calculará según el período solicitado, las reservas existentes, la condición del equipo y su custodia real.
- **Reservas sin cruces:** una unidad no podrá tener reservas activas superpuestas. Si una solicitud incluye varios equipos, todos deberán estar disponibles para confirmar la operación completa.
- **Políticas de préstamo:** cada organización podrá definir duración máxima, anticipación y límites de reservas activas. La V1 confirmará automáticamente las solicitudes que cumplan estas reglas.
- **Entrega controlada:** una reserva confirmada podrá generar un único préstamo. Antes de entregar, se verificará que todas las unidades estén aptas y no continúen prestadas.
- **Devoluciones parciales:** se registrarán las unidades recibidas y su condición, manteniendo pendientes las restantes. Los daños podrán bloquear nuevos préstamos y señalar reservas futuras afectadas.
- **Atrasos y trazabilidad:** se identificarán equipos no devueltos a tiempo y se conservará el historial de responsables, reservas, entregas, devoluciones e incidencias.

## Tecnologías previstas

| Tecnología | Uso |
| --- | --- |
| **Java y Spring Boot** | Desarrollo del backend y sus reglas de negocio. |
| **Spring Web** | Exposición de la API REST para futuros clientes web o móviles. |
| **Spring Security** | Autenticación y autorización por organización y rol. |
| **Spring Data JPA y PostgreSQL** | Persistencia, consultas, transacciones e integridad de datos. |
| **OpenAPI / Swagger UI** | Documentación y exploración de los endpoints. |

## Alcance y evolución

Cubrirá préstamos internos sin cobros, con reservas de una o varias unidades de una misma sede, entrega completa y devoluciones completas o parciales.

La escalabilidad se apoyará en límites claros entre módulos, consultas paginadas, índices, transacciones y medición del rendimiento. La capacidad se validará con pruebas según aumente la demanda.

Posteriormente se podrán incorporar **kits dinámicos de equipos compatibles**, sugerencias de alternativas y recuperación de reservas afectadas por averías o atrasos. Estas funciones ampliarán la base de préstamos sin formar parte del alcance inicial.
