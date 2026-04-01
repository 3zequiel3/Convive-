# Historias de Usuario



## HU-A: Gestión del Grupo de Hogar y Roles (RBAC)

**Como** usuario registrado en Convive+,  
**quiero** poder crear un grupo de hogar, invitar a otros miembros y asignar roles,  
**para** que cada integrante tenga los permisos correctos según su responsabilidad dentro del grupo.

### Criterios de aceptación

1. Dado un usuario registrado, cuando crea un nuevo grupo de hogar, entonces el sistema lo establece automáticamente como **Admin** de ese grupo y le entrega un código o enlace de invitación único.
2. Dado un código de invitación válido, cuando un usuario registrado lo utiliza, entonces se lo agrega al grupo con el rol de **Miembro** por defecto.
3. Dado un Admin del grupo, cuando quiere promover a un Miembro al rol de Admin, entonces el sistema actualiza su rol y el nuevo Admin adquiere todos los permisos correspondientes.
4. Dado un Admin del grupo, cuando intenta aprobar una tarea que él mismo completó, entonces el sistema rechaza la operación y muestra un mensaje de error indicando que un Admin no puede aprobar sus propias tareas.
5. Dado un Miembro del grupo, cuando intenta aprobar la tarea de otro integrante, entonces el sistema rechaza la operación con un error de permisos insuficientes.
6. Dado un grupo de hogar, cuando se consulta la lista de integrantes, entonces se visualiza el nombre y el rol (Admin / Miembro) de cada uno.
7. Dado un Admin del grupo, cuando decide remover a un integrante, entonces el sistema elimina al usuario del grupo y sus gastos pendientes se redistribuyen en el período actual según las reglas de reparto vigentes.


## HU-B: Algoritmo de Equidad Financiera

**Como** integrante del grupo de hogar con ingresos variables,  
**quiero** que el sistema calcule mi cuota de gastos comunes según la proporción de mis ingresos frente al total del grupo,  
**para** que el pago sea equitativo y proporcional, no igualitario.

> **Contexto de roles (ver HU-A):** cualquier integrante (Admin o Miembro) puede registrar sus ingresos. Solo los Admins pueden iniciar el cierre del período y disparar el cálculo de reparto.

### Criterios de aceptación

1. Dado un período mensual activo, cuando todos los integrantes del grupo registran sus ingresos, entonces el sistema calcula el porcentaje de participación de cada uno sobre el ingreso total del grupo.
2. Dado un monto total de gastos comunes del período (ya descontados los sobrecostos premium de HU-D), cuando el sistema aplica la proporción de ingresos, entonces asigna a cada integrante una cuota equivalente a su porcentaje de participación.
3. Dado que un integrante registra $0 de ingresos en el período, cuando el sistema calcula su cuota, entonces le asigna la cuota mínima garantizada equivalente al reparto igualitario simple (`total_gastos_base / N_integrantes`), para que ningún miembro quede eximido de responsabilidad financiera base.
4. Dado que el total de ingresos del grupo es $0, cuando un Admin intenta iniciar el cálculo de reparto proporcional, entonces el sistema bloquea el cálculo, muestra una advertencia de datos insuficientes y ofrece activar el reparto igualitario simple como alternativa temporal hasta que se registren ingresos.
5. Dado que un Admin inicia el cierre mensual, cuando el sistema ejecuta el cálculo final, entonces aplica el pipeline en orden: (1) separar sobrecostos premium (HU-D), (2) calcular cuotas proporcionales por ingreso sobre el gasto base, (3) descontar créditos Task-to-Cash aprobados (HU-C).

### Reglas de negocio aplicables

Ver [`reglas_negocio.md`](./reglas_negocio.md): **RN-001** (distribución proporcional), **RN-002** (integridad del total), **RN-003** (cuota mínima garantizada), **RN-004** (grupo sin ingresos), **RN-005** (redondeo), **RN-006** (cierre restringido a Admin).

---

## HU-C: Conversión de Esfuerzo en Valor (Task-to-Cash)

**Como** integrante del grupo de hogar (Admin o Miembro) con más tiempo que dinero,  
**quiero** que las tareas del hogar que completé se conviertan en créditos financieros,  
**para** reducir mi deuda mensual mediante trabajo físico en lugar de dinero.

> **Contexto de roles (ver HU-A):** cualquier integrante puede cargar tareas completadas. Solo un **Admin distinto al ejecutor** puede aprobar el crédito. El catálogo de tareas y sus valores monetarios solo pueden ser configurados por un Admin.

### Criterios de aceptación

1. Dado un catálogo de tareas configurado por un Admin, cuando cualquier integrante (Admin o Miembro) marca una tarea como completada, entonces el sistema registra una solicitud de crédito pendiente de aprobación asociada al integrante.
2. Dado una solicitud de crédito pendiente, cuando un **Admin diferente al integrante que ejecutó la tarea** la aprueba, entonces el crédito queda confirmado y se aplica al balance del integrante en el período activo.
3. Dado una solicitud de crédito pendiente, cuando un Admin la rechaza, entonces no se genera crédito, el sistema registra el motivo del rechazo y deja trazabilidad auditable (quién rechazó y cuándo).
4. Dado el cierre mensual iniciado por un Admin, cuando se calculan las deudas finales, entonces los créditos aprobados en el período se descuentan de la deuda de cada integrante sin permitir que el saldo resultante sea negativo por esta regla.
5. Dado que una tarea se completa y aprueba **después del cierre mensual**, cuando el sistema procesa la aprobación, entonces el crédito se imputa al período activo siguiente, no al período ya cerrado.

### Reglas de negocio aplicables

Ver [`reglas_negocio.md`](./reglas_negocio.md): **RN-007** (catálogo valorizado), **RN-008** (aprobación por Admin distinto), **RN-009** (rechazo sin impacto), **RN-010** (unicidad / recurrencia), **RN-011** (crédito post-cierre), **RN-012** (límite inferior de deuda).

---

## HU-D: Ajuste por Dieta Especial (Premium de Consumo Personal)

**Como** integrante del grupo de hogar con necesidades dietéticas o de consumo específicas,  
**quiero** etiquetar productos premium en las facturas de compra compartidas,  
**para** que el sobrecosto personal sea asumido solo por mí y no impacte el presupuesto base del grupo.

> **Contexto de roles (ver HU-A):** cualquier integrante (Admin o Miembro) puede registrar un comprobante de compra y etiquetar ítems como premium personales propios. Este paso ocurre **antes** del reparto proporcional (HU-B); es el primer paso del pipeline de cálculo del período.

### Criterios de aceptación

1. Dado un comprobante de compra registrado por cualquier integrante del grupo, cuando se ingresan los ítems, entonces el integrante puede marcar uno o más productos como **premium personal** y asignarlos a su propio perfil.
2. Dado un ítem marcado como premium personal, cuando el Admin inicia el cierre del período, entonces el sistema separa automáticamente ese costo del gasto común base antes de calcular el reparto proporcional.
3. Dado que múltiples integrantes tienen ítems premium en la misma compra, cuando se consolida el comprobante, entonces cada sobrecosto premium se imputa únicamente al integrante titular que lo etiquetó, de forma independiente.
4. Dado un reporte de cierre mensual, cuando cualquier integrante visualiza el detalle de gastos, entonces el reporte muestra por separado: (a) gasto común base distribuido y (b) sobrecostos personales por integrante.
5. Dado que un integrante intenta etiquetar un ítem premium en nombre de **otro** integrante, entonces el sistema rechaza la operación; cada uno solo puede imputarse ítems a sí mismo.

### Reglas de negocio aplicables

Ver [`reglas_negocio.md`](./reglas_negocio.md): **RN-013** (etiquetado de ítems), **RN-014** (cálculo del diferencial), **RN-015** (separación antes del reparto), **RN-016** (auto-imputación exclusiva), **RN-017** (auditabilidad).
