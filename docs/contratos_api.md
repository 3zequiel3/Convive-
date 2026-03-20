# Contratos API (MVP)

## 1. Convenciones generales

- Base URL: /api/v1
- Formato: JSON
- Autenticación: Bearer JWT en rutas protegidas
- Moneda: valores numéricos con 2 decimales
- Fechas: formato ISO 8601

## 2. Endpoints principales

### 2.1 Autenticación

#### POST /api/v1/auth/register

Registra un usuario.

Request:

```json
{
	"nombre": "Ana",
	"email": "ana@mail.com",
	"password": "Secreta123"
}
```

Response 201:

```json
{
	"id": "usr_001",
	"nombre": "Ana",
	"email": "ana@mail.com"
}
```

#### POST /api/v1/auth/login

Inicia sesión y entrega token JWT.

Request:

```json
{
	"email": "ana@mail.com",
	"password": "Secreta123"
}
```

Response 200:

```json
{
	"access_token": "jwt-token",
	"token_type": "bearer"
}
```

### 2.2 Períodos de liquidación

#### POST /api/v1/periodos

Crea período mensual de trabajo.

Request:

```json
{
	"nombre": "2026-03",
	"fecha_inicio": "2026-03-01",
	"fecha_fin": "2026-03-31"
}
```

Response 201:

```json
{
	"id": "per_2026_03",
	"estado": "abierto"
}
```

#### GET /api/v1/periodos/{periodo_id}

Obtiene estado y resumen básico del período.

### 2.3 Ingresos por inquilino (HU-A)

#### POST /api/v1/periodos/{periodo_id}/ingresos

Registra o actualiza ingreso mensual de un inquilino.

Request:

```json
{
	"usuario_id": "usr_001",
	"monto": 350000.00
}
```

Response 201:

```json
{
	"id": "ing_001",
	"periodo_id": "per_2026_03",
	"usuario_id": "usr_001",
	"monto": 350000.00
}
```

### 2.4 Gastos y premium personal (HU-C)

#### POST /api/v1/periodos/{periodo_id}/gastos

Registra un gasto con ítems base y/o premium personal.

Request:

```json
{
	"descripcion": "Supermercado semanal",
	"fecha": "2026-03-10",
	"items": [
		{
			"nombre": "Arroz",
			"monto_base": 10.00,
			"premium_personal": 0.00,
			"usuario_premium_id": null
		},
		{
			"nombre": "Pan sin gluten",
			"monto_base": 4.00,
			"premium_personal": 3.50,
			"usuario_premium_id": "usr_002"
		}
	]
}
```

Response 201:

```json
{
	"id": "gas_001",
	"periodo_id": "per_2026_03",
	"total_base": 14.00,
	"total_premium": 3.50
}
```

### 2.5 Tareas y créditos (HU-B)

#### POST /api/v1/periodos/{periodo_id}/tareas

Registra tarea completada para solicitar crédito.

Request:

```json
{
	"usuario_id": "usr_003",
	"tipo_tarea": "limpieza_baño",
	"valor_credito": 8.00,
	"fecha_ejecucion": "2026-03-12"
}
```

Response 201:

```json
{
	"id": "tar_001",
	"estado": "pendiente_validacion"
}
```

#### PATCH /api/v1/tareas/{tarea_id}/validacion

Aprueba o rechaza tarea para generar crédito.

Request:

```json
{
	"estado": "aprobada",
	"motivo": "Ejecucion correcta"
}
```

Response 200:

```json
{
	"id": "tar_001",
	"estado": "aprobada",
	"credito_aplicado": 8.00
}
```

### 2.6 Cierre y reparto mensual

#### POST /api/v1/periodos/{periodo_id}/calcular-reparto

Ejecuta el cálculo del período según reglas RN-001 a RN-014.

Response 200:

```json
{
	"periodo_id": "per_2026_03",
	"estado": "calculado",
	"resumen": {
		"gasto_comun_total": 1200.00,
		"creditos_totales": 140.00,
		"sobrecostos_premium_totales": 65.50
	}
}
```

#### GET /api/v1/periodos/{periodo_id}/liquidacion

Retorna deuda neta final por inquilino.

Response 200:

```json
{
	"periodo_id": "per_2026_03",
	"deudas": [
		{
			"usuario_id": "usr_001",
			"cuota_base": 420.00,
			"premium_personal": 0.00,
			"creditos": 20.00,
			"deuda_neta": 400.00
		},
		{
			"usuario_id": "usr_002",
			"cuota_base": 380.00,
			"premium_personal": 30.50,
			"creditos": 0.00,
			"deuda_neta": 410.50
		}
	]
}
```

## 3. Errores base

### 400 Bad Request

Datos inválidos o faltantes.

### 401 Unauthorized

Token ausente, vencido o inválido.

### 403 Forbidden

Usuario sin permisos para la operación.

### 404 Not Found

Recurso no encontrado.

### 409 Conflict

Conflicto de negocio (ejemplo: tarea duplicada en el período).

### 422 Unprocessable Entity

Regla de negocio incumplida (ejemplo: suma de ingresos del grupo = 0 al calcular reparto).
