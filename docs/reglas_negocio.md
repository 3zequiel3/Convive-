# Reglas de Negocio

## 1. Objetivo

Definir las reglas que gobiernan la gestión del grupo de hogar, el reparto de gastos, la conversión de tareas a crédito y la imputación de sobrecostos personales en Convive+.

## 2. Alcance

Estas reglas aplican a la gestión del grupo de hogar y al cierre mensual de convivencia para:

1. Gestión de grupos y roles (RBAC).
2. Gastos comunes.
3. Créditos por tareas del hogar.
4. Sobrecostos por consumo premium personal.

## 3. Definiciones

1. **Integrante:** cualquier usuario perteneciente a un grupo de hogar, independientemente de su rol.
2. **Admin, minimo 2 por grupo:** integrante con permisos de gestión del grupo, aprobación de tareas y cierre de período.
3. **Miembro:** integrante con permisos de carga de datos y consulta de balances.
4. **Gasto común base:** monto compartido entre todos los integrantes del grupo, excluidos los sobrecostos premium.
5. **Sobrecosto premium personal:** diferencia de precio imputable a una necesidad individual de un integrante.
6. **Crédito por tarea:** monto aprobado por un Admin que reduce la deuda mensual de un integrante.
7. **Tarea recurrente:** tarea configurada por un Admin que puede completarse y acreditarse más de una vez dentro del mismo período.
8. **Período:** ventana de liquidación (mensual para MVP).
9. **Deuda neta:** resultado final por integrante tras aplicar todas las reglas del pipeline de cálculo.

## 4. Reglas de reparto financiero (HU-B)

### RN-001: Distribución proporcional por ingresos

La cuota de gasto común base de cada integrante se calcula según su proporción de ingresos dentro del grupo en el período. El cálculo se ejecuta sobre el gasto común base ya descontados los sobrecostos premium (ver HU-D).

Fórmula base:

```
cuota_i = gasto_comun_base * (ingreso_i / suma_ingresos_grupo)
```

### RN-002: Integridad del total distribuido

La suma de cuotas individuales debe ser exactamente igual al gasto común base total del período.

### RN-003: Cuota mínima garantizada

Ningún integrante puede tener una cuota inferior al reparto igualitario simple. Si el ingreso declarado arroja una cuota menor a ese piso, se le asigna la cuota mínima garantizada:

```
cuota_minima = gasto_comun_base / N_integrantes
```

Esto aplica también a integrantes con ingreso $0 declarado.

### RN-004: Grupo sin ingresos válidos

Si la suma de ingresos del grupo es $0, solo un Admin puede intentar iniciar el cálculo. El sistema bloquea el cálculo proporcional, muestra advertencia de datos insuficientes y ofrece activar el reparto igualitario simple (`gasto_comun_base / N_integrantes`) como alternativa temporal hasta que se registren ingresos.

### RN-005: Regla de redondeo monetario

Todos los cálculos monetarios se redondean a 2 decimales hacia arriba (`ROUND_HALF_UP`) de forma consistente en todo el sistema para evitar diferencias acumuladas entre períodos.

### RN-006: Cierre de período restringido a Admin

Solo un integrante con rol Admin puede confirmar y cerrar un período. Los Miembros pueden consultar el cálculo preliminar (modo lectura) pero no confirmarlo ni ejecutar el pipeline final.

## 5. Reglas de Task-to-Cash (HU-C)

### RN-007: Catálogo de tareas valorizadas

Toda tarea que pueda generar crédito debe existir en un catálogo con valor monetario vigente. El catálogo es configurable únicamente por un Admin del grupo.

### RN-008: Crédito sujeto a validación por Admin distinto

Una tarea completada por un integrante genera solicitud de crédito pendiente. El crédito solo impacta la deuda cuando un **Admin diferente al integrante ejecutor** la aprueba. Ningún integrante puede aprobar sus propias tareas, incluyendo Admins.

### RN-009: Rechazo sin impacto financiero

Si la tarea es rechazada, no se aplica crédito. El rechazo debe registrar el motivo, la fecha y el Admin validador.

### RN-010: Unicidad de crédito por tarea (con excepción recurrente)

La misma ejecución de tarea no puede acreditarse más de una vez dentro del período, salvo que el Admin la haya configurado como **tarea recurrente**, en cuyo caso puede completarse y acreditarse múltiples veces dentro del período según la frecuencia configurada.

### RN-011: Crédito post-cierre

Si una tarea es aprobada después del cierre mensual, el crédito se imputa al período activo siguiente. No se reabre ni modifica el período ya cerrado.

### RN-012: Límite inferior de deuda

Los créditos por tareas no pueden producir deuda neta negativa dentro del período. El mínimo resultado aplicable es 0. El excedente no se arrastra al período siguiente.

## 6. Reglas de consumo premium personal (HU-D)

### RN-013: Etiquetado de ítems premium

Cualquier integrante (Admin o Miembro) puede clasificar ítems de un comprobante de compra como base compartida o premium personal propio. Este etiquetado ocurre durante el registro del comprobante, antes del cierre del período.

### RN-014: Cálculo del diferencial premium

Solo la diferencia de precio del ítem premium respecto a un producto estándar equivalente se imputa individualmente. Si no existe producto estándar equivalente, el 100% del ítem premium es costo personal del titular.

```
sobrecosto_premium_i = precio_item_premium - precio_estandar_equivalente
# Si precio_estandar_equivalente no existe: sobrecosto_premium_i = precio_item_premium
```

### RN-015: Separación antes del reparto proporcional

Los sobrecostos premium se separan del gasto común base **antes** de calcular el reparto proporcional (RN-001). El gasto común base sobre el cual se aplica HU-B ya no incluye los sobrecostos premium.

### RN-016: Imputación individual exclusiva y auto-imputación

Los sobrecostos premium no se prorratean entre integrantes. Cada integrante solo puede etiquetar ítems premium a su propio perfil; está prohibido imputar sobrecostos a nombre de otro integrante.

### RN-017: Auditabilidad por comprobante

Toda imputación premium debe quedar trazada por comprobante, ítem, integrante titular y fecha de registro.

## 7. Pipeline de liquidación del período

Para garantizar consistencia de resultados, el cierre mensual iniciado por un Admin debe seguir este orden estrictamente:

| Paso | Operación | Reglas aplicadas | HU |
|:----:|-----------|-----------------|----|
| 1 | Separar sobrecostos premium de ítems etiquetados | RN-013 a RN-017 | HU-D |
| 2 | Calcular gasto común base = total_gastos − sobrecostos_premium | RN-001, RN-002 | HU-B |
| 3 | Calcular cuotas proporcionales por ingreso (con cuota mínima garantizada) | RN-001 a RN-006 | HU-B |
| 4 | Descontar créditos Task-to-Cash aprobados en el período | RN-007 a RN-012 | HU-C |
| 5 | Imputar sobrecostos premium individuales a cada titular | RN-014 a RN-017 | HU-D |
| 6 | Emitir deuda neta final por integrante | RN-002, RN-012 | — |

> **Invariante del pipeline:** si cualquier paso falla, la transacción completa se revierte (ver RN-021 Atomicidad de cierre).

## 8. Reglas de trazabilidad y seguridad

### RN-018: Trazabilidad obligatoria

Toda operación con impacto financiero debe registrar actor, fecha/hora, valor anterior, valor nuevo y motivo. Esto incluye: aprobación/rechazo de tareas, imputación premium, registro de ingresos y cierre de período.

### RN-019: Control de acceso

Solo integrantes autenticados y con rol autorizado pueden crear, validar o modificar operaciones con impacto en deudas y saldos. La validación de rol se realiza en el backend, no solo en el cliente.

### RN-020: No repudio

Ninguna operación financiera aprobada puede eliminarse; solo puede revertirse mediante una contrapartida registrada con trazabilidad completa.

### RN-021: Atomicidad de cierre

La liquidación mensual debe ejecutarse en transacción atómica: o se confirma completa o se revierte totalmente. Esto garantiza que el pipeline del período nunca quede en estado parcial.

## 9. Estados funcionales mínimos

1. Período abierto: admite carga y edición de datos.
2. Período en cálculo: bloquea modificaciones sensibles.
3. Período cerrado: resultados finales inmutables salvo reapertura controlada.

## 10. Criterios de calidad de datos

1. No se permiten montos negativos en gastos comunes, valores de tareas ni sobrecostos premium.
2. Todo registro financiero debe pertenecer a un período y a un usuario válido.
3. Los datos incompletos deben impedir cierre definitivo hasta su corrección.

## 11. Reglas de Gestión de Grupos y Roles (RBAC) — HU-A

### RN-022: Matriz de permisos por rol

| Acción | Admin | Miembro |
|--------|:-----:|:-------:|
| Crear grupo de hogar | ✅ | ❌ |
| Invitar miembros al grupo | ✅ | ❌ |
| Promover Miembro a Admin | ✅ | ❌ |
| Remover integrante del grupo | ✅ | ❌ |
| Configurar catálogo de tareas Task-to-Cash | ✅ | ❌ |
| Aprobar tareas de otros integrantes | ✅ | ❌ |
| Iniciar y confirmar cierre de período | ✅ | ❌ |
| Cargar tareas completadas propias | ✅ | ✅ |
| Registrar ingresos propios del período | ✅ | ✅ |
| Registrar gastos comunes e ítems | ✅ | ✅ |
| Etiquetar ítems premium propios | ✅ | ✅ |
| Ver balance y cálculo preliminar del grupo | ✅ | ✅ |

### RN-023: Admin único

Un grupo debe tener al menos un Admin activo en todo momento. Si el único Admin abandona el grupo, el sistema promueve automáticamente al Miembro con mayor antigüedad de membresía.

### RN-024: Múltiples Admins

Puede haber múltiples Admins en un mismo grupo. El RBAC gestiona permisos por rol, no por individuo.

### RN-025: Prohibición de auto-aprobación

Ningún integrante puede aprobar sus propias tareas Task-to-Cash, incluyendo Admins. La aprobación siempre requiere un Admin **diferente** al ejecutor de la tarea.

### RN-026: RBAC en backend

La validación de permisos se implementa en cada endpoint del backend antes de ejecutar la acción. La validación en el cliente es complementaria pero no suficiente.

### RN-027: Mínimo de integrantes para reparto

Un grupo requiere un mínimo de 2 integrantes activos para poder activar el motor de reparto y ejecutar el pipeline de liquidación.

---
