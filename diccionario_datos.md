# Diccionario de datos — Proyecto 1 · Finanzas

Cartera sintética de préstamos personales (ene 2022 – oct 2026). Datos 100 % ficticios.
Semilla fija: `2026`. Fecha de corte de la cartera: **31/10/2026**.
Formato: CSV, UTF-8, separador coma, punto decimal, nulos = campo vacío.
Ubicación: `data/raw/` (no modificar; la limpieza va a `data/processed/`).

## Relaciones

    clientes (1) ──< prestamos (N) ──< pagos (N)
    clientes.id_cliente = prestamos.id_cliente
    prestamos.id_prestamo = pagos.id_prestamo

## clientes.csv (≈5.000 filas)

| Columna | Tipo | Descripción |
|---|---|---|
| id_cliente | texto | Clave primaria. Formato `C00001`. |
| fecha_alta | fecha | Fecha de alta como cliente. |
| edad | entero | Edad en años a la fecha de alta. |
| provincia | texto | Provincia de residencia. |
| estado_civil | texto | soltero, casado, divorciado, pareja de hecho, viudo. |
| ingresos_anuales | decimal | Ingresos brutos anuales declarados (€). |
| situacion_laboral | texto | indefinido, temporal, autonomo, funcionario, jubilado, desempleado. |
| score_crediticio | entero | Puntuación de riesgo; rango válido 300–850 (mayor = mejor). |

## prestamos.csv (≈8.000 préstamos)

| Columna | Tipo | Descripción |
|---|---|---|
| id_prestamo | texto | Clave primaria. Formato `PR000001`. |
| id_cliente | texto | Clave foránea a `clientes`. |
| fecha_concesion | fecha | Fecha de concesión (siempre posterior o igual al alta del cliente). |
| importe | decimal | Capital concedido (€). |
| plazo_meses | entero | Duración en meses (6, 12, 18, 24, 36, 48, 60, 72, 84). |
| tipo_interes | decimal | TIN anual en **porcentaje** (p. ej. `8.25` = 8,25 %). |
| finalidad | texto | consumo, coche, reforma, consolidacion_deudas, estudios, vacaciones, tecnologia. |
| canal_captacion | texto | web, sucursal, app_movil, broker, telefono. |
| estado | texto | `vigente` (en curso al corte), `cancelado` (liquidado, al vencimiento o anticipadamente), `impagado`. |

## pagos.csv (≈80.000 cuotas)

Una fila por cuota **vencida hasta el corte** (no hay cuotas futuras). Cuota fija (sistema francés).

| Columna | Tipo | Descripción |
|---|---|---|
| id_pago | texto | Clave primaria. Formato `PG0000001`. |
| id_prestamo | texto | Clave foránea a `prestamos`. |
| fecha_vencimiento | fecha | Fecha en que vencía la cuota. |
| fecha_pago | fecha | Fecha en que se pagó. **Vacía = cuota sin pagar.** |
| importe_cuota | decimal | Cuota teórica (€). |
| importe_pagado | decimal | Importe realmente abonado (€); `0` si no se pagó, puede ser pago parcial. |
| dias_retraso | entero | Días entre vencimiento y pago. En cuotas sin pagar, días transcurridos hasta el corte (31/10/2026). |

## Problemas de calidad incluidos a propósito

Para que practiques la limpieza (pregunta 1 del proyecto), los datos contienen, entre otros:

- Valores nulos en varias columnas.
- Registros duplicados en más de una tabla.
- Fechas en formatos mixtos dentro de una misma columna.
- Textos escritos de formas distintas (mayúsculas, acentos, espacios, nombres alternativos).
- Valores extremos o imposibles en variables numéricas.
- Campos que no cuadran con otros de la misma fila.

No te digo dónde ni cuántos: detectarlos y cuantificarlos es parte del ejercicio.

## Carga rápida (pandas)

```python
import pandas as pd
clientes  = pd.read_csv("data/raw/clientes.csv")
prestamos = pd.read_csv("data/raw/prestamos.csv")
pagos     = pd.read_csv("data/raw/pagos.csv")
```

En Power BI: Obtener datos > Texto/CSV > origen del archivo **65001: Unicode (UTF-8)**.
