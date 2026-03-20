# Reglas de Negocio

## 1. Objetivo

Definir las reglas que gobiernan el reparto de gastos, la conversión de tareas a crédito y la imputación de sobrecostos personales en Convive+.

## 2. Alcance

Estas reglas aplican al cierre mensual de convivencia para:

1. Gastos comunes.
2. Créditos por tareas del hogar.
3. Sobrecostos por consumo premium personal.

## 3. Definiciones

1. Gasto común base: monto compartido entre todos los inquilinos.
2. Sobrecosto premium personal: diferencia de precio imputable a una necesidad individual.
3. Crédito por tarea: monto aprobado que reduce deuda mensual.
4. Período: ventana de liquidación (mensual para MVP).
5. Deuda neta: resultado final por inquilino tras aplicar todas las reglas.

## 4. Reglas de reparto financiero (HU-A)

### RN-001: Distribución proporcional por ingresos

La cuota de gasto común de cada inquilino se calcula según su proporción de ingresos dentro del grupo en el período.

Formula base:

cuota_i = gasto_comun_total * (ingreso_i / suma_ingresos_grupo)

### RN-002: Integridad del total distribuido

La suma de cuotas individuales debe ser exactamente igual al gasto común total del período.

### RN-003: Ingreso individual no informado

Si un inquilino no registra ingresos en el período, su ingreso para el cálculo es 0.

### RN-004: Grupo sin ingresos válidos

Si la suma de ingresos del grupo es 0, el cálculo proporcional no se ejecuta y el período queda en estado pendiente por datos insuficientes.

### RN-005: Regla de redondeo monetario

Todos los cálculos monetarios se redondean a 2 decimales con criterio consistente para todo el sistema.

## 5. Reglas de Task-to-Cash (HU-B)

### RN-006: Catálogo de tareas valorizadas

Toda tarea que pueda generar crédito debe existir en un catálogo con valor monetario vigente.

### RN-007: Crédito sujeto a validación

Una tarea completada por un inquilino genera solicitud de crédito y solo impacta la deuda cuando un responsable la aprueba.

### RN-008: Rechazo sin impacto financiero

Si la tarea es rechazada, no se aplica crédito. El rechazo debe registrar motivo, fecha y usuario validador.

### RN-009: Unicidad de crédito por tarea

La misma ejecución de tarea no puede acreditarse más de una vez dentro del período.

### RN-010: Límite inferior de deuda

Los créditos por tareas no pueden producir deuda neta negativa dentro del período. El mínimo resultado aplicable es 0.

## 6. Reglas de consumo premium personal (HU-C)

### RN-011: Etiquetado de ítems premium

Cada ítem de compra puede clasificarse como base compartida o premium personal.

### RN-012: Separación de componentes

En ítems premium, el componente base integra gasto común y el diferencial premium se imputa solo al titular.

### RN-013: Imputación individual exclusiva

Los sobrecostos premium no se prorratean entre compañeros y se asignan únicamente al usuario responsable.

### RN-014: Auditabilidad por comprobante

Toda imputación premium debe quedar trazada por comprobante, ítem, usuario y fecha.

## 7. Orden de liquidación del período

Para garantizar consistencia de resultados, el cierre mensual debe seguir este orden:

1. Consolidar gasto común base y sobrecostos premium.
2. Calcular cuotas proporcionales del gasto común (RN-001 a RN-005).
3. Imputar sobrecostos premium individuales (RN-011 a RN-014).
4. Aplicar créditos aprobados por tareas (RN-006 a RN-010).
5. Emitir deuda neta final por inquilino.

## 8. Reglas de trazabilidad y seguridad

### RN-015: Trazabilidad obligatoria

Toda operación con impacto financiero debe registrar actor, fecha/hora, valor anterior, valor nuevo y motivo.

### RN-016: Control de acceso

Solo usuarios autenticados y autorizados pueden crear, validar o modificar operaciones con impacto en deudas y saldos.

### RN-017: Atomicidad de cierre

La liquidación mensual debe ejecutarse en transacción atómica: o se confirma completa o se revierte totalmente.

## 9. Estados funcionales mínimos

1. Período abierto: admite carga y edición de datos.
2. Período en cálculo: bloquea modificaciones sensibles.
3. Período cerrado: resultados finales inmutables salvo reapertura controlada.

## 10. Criterios de calidad de datos

1. No se permiten montos negativos en gastos comunes, valores de tareas ni sobrecostos premium.
2. Todo registro financiero debe pertenecer a un período y a un usuario válido.
3. Los datos incompletos deben impedir cierre definitivo hasta su corrección.
