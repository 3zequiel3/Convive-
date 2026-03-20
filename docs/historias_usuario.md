# Historias de Usuario

## HU-A: Algoritmo de Equidad Financiera

**Como** inquilino con ingresos variables,  
**quiero** que el sistema calcule mi cuota de gastos comunes según la proporción de mis ingresos frente al total del grupo,  
**para** que el pago sea equitativo y no solo igualitario.

### Criterios de aceptación

1. Dado un período mensual, cuando se registran los ingresos de todos los inquilinos, entonces el sistema calcula el porcentaje de participación de cada uno sobre el ingreso total del grupo.
2. Dado un monto total de gastos comunes, cuando el sistema aplica la proporción de ingresos, entonces asigna a cada inquilino una cuota equivalente a su porcentaje de participación.
3. Dado que un inquilino no registra ingresos en el período, cuando se calcula la cuota, entonces su participación financiera es 0 para esa regla de reparto.
4. Dado que el total de ingresos del grupo es 0, cuando se intenta calcular la distribución proporcional, entonces el sistema debe bloquear el cálculo y mostrar una advertencia de datos insuficientes.

### Reglas de negocio asociadas

- El total distribuido entre inquilinos debe ser exactamente igual al total de gastos comunes.
- El redondeo monetario debe estar definido y ser consistente para evitar diferencias acumuladas.

---

## HU-B: Conversión de Esfuerzo en Valor (Task-to-Cash)

**Como** inquilino con más tiempo que dinero,  
**quiero** que mis tareas del hogar completadas se conviertan en créditos financieros,  
**para** reducir mi deuda mensual mediante trabajo físico.

### Criterios de aceptación

1. Dado un catálogo de tareas con valor monetario definido, cuando un inquilino marca una tarea como completada, entonces el sistema registra una solicitud de crédito asociada.
2. Dado que una tarea requiere validación, cuando un responsable aprueba la ejecución, entonces el crédito se aplica al balance del inquilino.
3. Dado que una tarea es rechazada, cuando se cierra la validación, entonces no se genera crédito y el sistema deja trazabilidad del motivo.
4. Dado un cierre mensual, cuando se calculan deudas finales, entonces los créditos aprobados se descuentan de la deuda del inquilino sin permitir saldo negativo por esta regla.

### Reglas de negocio asociadas

- Cada tarea puede generar crédito una sola vez por período.
- Debe existir trazabilidad de quién completó, quién validó y cuándo se acreditó el valor.
- El valor de las tareas debe ser configurable por administración del hogar o grupo.

---

## HU-C: Ajuste por Dieta Especial (Premium de Consumo Personal)

**Como** usuario con necesidades dietéticas específicas,  
**quiero** etiquetar productos premium en las facturas de compra,  
**para** que el sobrecosto sea asumido solo por mí y no impacte el presupuesto base común.

### Criterios de aceptación

1. Dado un comprobante de compra, cuando se registran ítems, entonces el usuario puede marcar ciertos productos como premium personales.
2. Dado un ítem marcado como premium personal, cuando se liquida el período, entonces el sistema separa su costo del gasto común base.
3. Dado que existen varios usuarios con consumos especiales, cuando se consolida la compra, entonces cada sobrecosto premium se asigna únicamente al titular correspondiente.
4. Dado un reporte de cierre mensual, cuando se visualiza el detalle de gastos, entonces deben verse por separado: gasto común base y sobrecostos personales.

### Reglas de negocio asociadas

- El componente base de productos compartidos sigue integrando el gasto común.
- Solo la diferencia premium personal se imputa de forma individual.
- La imputación premium debe ser auditable por ítem y por comprobante.
