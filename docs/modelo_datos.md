# Modelo de Datos (MVP)

## 1. Objetivo

Definir las entidades principales 

## 2. Entidades principales

1. Usuario
2. GrupoConvivencia
3. GrupoUsuario (Intermedia entre Usuario y GrupoConvivencia)
4. PeriodoLiquidacion
5. IngresoMensual
6. Gasto
7. GastoItem
8. TareaCatalogo
9. TareaEjecutada
10. Liquidacion
11. LiquidacionDetalle

## 3. Relación rápida entre entidades

1. GrupoConvivencia 1:N PeriodoLiquidacion
2. Usuario N:M GrupoConvivencia (mediante GrupoUsuario)
3. PeriodoLiquidacion 1:N IngresoMensual
4. Usuario 1:N IngresoMensual
5. PeriodoLiquidacion 1:N Gasto
6. Gasto 1:N GastoItem
7. Usuario 1:N GastoItem (solo para usuario_premium_id cuando aplica premium)
8. GrupoConvivencia 1:N TareaCatalogo
9. PeriodoLiquidacion 1:N TareaEjecutada
10. Usuario 1:N TareaEjecutada (ejecutor)
11. Usuario 1:N TareaEjecutada (validador, opcional)
12. PeriodoLiquidacion 1:1 Liquidacion
13. Liquidacion 1:N LiquidacionDetalle
14. Usuario 1:N LiquidacionDetalle

## 4. Campos clave por entidad

### Usuario

- id
- nombre
- email (unico)
- password_hash
- created_at

### GrupoConvivencia

- id
- nombre
- created_by
- created_at

### GrupoUsuario

- id
- grupo_id
- usuario_id
- rol (admin o miembro)
- fecha_alta

### PeriodoLiquidacion

- id
- grupo_id
- nombre (ej: 2026-03)
- fecha_inicio
- fecha_fin
- estado (abierto, en_calculo, cerrado)

### IngresoMensual

- id
- periodo_id
- usuario_id
- monto
- created_at

### Gasto

- id
- periodo_id
- descripcion
- fecha
- total_base
- total_premium
- created_by

### GastoItem

- id
- gasto_id
- nombre
- monto_base
- premium_personal
- usuario_premium_id (nullable)

### TareaCatalogo

- id
- grupo_id
- codigo
- descripcion
- valor_credito
- activo

### TareaEjecutada

- id
- periodo_id
- tarea_catalogo_id
- usuario_ejecutor_id
- usuario_validador_id (nullable)
- estado (pendiente_validacion, aprobada, rechazada)
- valor_credito
- motivo_validacion (nullable)
- fecha_ejecucion
- fecha_validacion (nullable)

### Liquidacion

- id
- periodo_id (unico)
- gasto_comun_total
- creditos_totales
- sobrecostos_premium_totales
- calculado_at

### LiquidacionDetalle

- id
- liquidacion_id
- usuario_id
- cuota_base
- premium_personal
- creditos
- deuda_neta

## 5. Restricciones mínimas recomendadas

1. IngresoMensual: unique(periodo_id, usuario_id).
2. GrupoUsuario: unique(grupo_id, usuario_id).
3. TareaEjecutada: evitar duplicidad de acreditación por ejecución en el período.
4. Liquidacion: unique(periodo_id).
5. Validación de no negativos en montos financieros.

## 6. Entidades fuera de alcance MVP

Para no complejizar el primer release, no se incluyen por ahora:

1. Presupuestos planificados.
2. Reglas dinámicas avanzadas de reparto por IA.
3. Múltiples monedas o tipo de cambio.
