# Reporte de Valores Faltantes — Garments Worker Productivity

**Dataset:** Tema_11.csv
**Total de filas:** 1207
**Total de columnas:** 15
**Columnas con valores faltantes:** 15 de 15 (todas)

---

## Resumen por columna

| Columna | Tipo | Nulos | Porcentaje | Valores unicos |
|---------|------|------:|----------:|---------------:|
| `wip` | float64 | 539 | 44.66% | 528 |
| `date` | string | 62 | 5.14% | 59 |
| `day` | string | 61 | 5.05% | 6 |
| `targeted_productivity` | float64 | 61 | 5.05% | 9 |
| `idle_time` | float64 | 61 | 5.05% | 12 |
| `department` | string | 60 | 4.97% | 3 |
| `quarter` | string | 60 | 4.97% | 5 |
| `no_of_workers` | float64 | 60 | 4.97% | 59 |
| `no_of_style_change` | float64 | 60 | 4.97% | 3 |
| `incentive` | float64 | 60 | 4.97% | 48 |
| `idle_men` | float64 | 60 | 4.97% | 9 |
| `actual_productivity` | float64 | 60 | 4.97% | 836 |
| `team` | string | 59 | 4.89% | 13 |
| `smv` | float64 | 59 | 4.89% | 70 |
| `over_time` | float64 | 58 | 4.81% | 140 |

---

## Analisis detallado por columna

### `wip` — Work in Progress
- **Nulos:** 539 (44.66%)
- **Tipo de faltante:** Estructural
- **Causa:** El departamento `finishing` (acabado final) no registra trabajo en proceso por definicion del flujo productivo. Cuando una prenda llega a finishing, el proceso de manufactura esta casi terminado y no hay unidades intermedias que contar.
- **Tratamiento:** Imputar con `0` en filas de `finishing`. En `sewing`, imputar con la mediana del equipo correspondiente.

---

### `date`, `day`, `quarter` — Fecha y periodo
- **Nulos:** ~60-62 (~5%)
- **Tipo de faltante:** Aleatorio (MAR — Missing At Random)
- **Causa:** Registros que no completaron los campos temporales, probablemente ingresados de forma incompleta.
- **Tratamiento:** `day` puede recuperarse a partir de `date` (conversion a datetime y extraccion del dia de la semana). `quarter` puede derivarse del mes. Los nulos restantes no afectan el modelo predictivo.

---

### `department` — Departamento
- **Nulos:** 60 (4.97%)
- **Tipo de faltante:** Aleatorio
- **Causa:** Registros sin asignacion de departamento. Sin esta informacion no es posible clasificar ni interpretar la fila.
- **Tratamiento:** Eliminar las filas sin departamento. Son criticas para el analisis.

---

### `team` — Numero de equipo
- **Nulos:** 59 (4.89%)
- **Tipo de faltante:** Aleatorio + Inconsistencias (el valor `"invalid_value"` se convierte a NaN)
- **Causa:** Ademas de registros sin equipo, existe al menos un valor no numerico (`"invalid_value"`) que se detecta como inconsistencia y se convierte en NaN durante la limpieza.
- **Tratamiento:** Eliminar las filas sin equipo. Son criticas para las imputaciones por grupo.

---

### `smv` — Standard Minute Value
- **Nulos:** 59 (4.89%)
- **Tipo de faltante:** Aleatorio
- **Causa:** Tiempo estandar de la tarea no registrado. Adicionalmente existe un outlier extremo (545.6 min) que se corrige antes de imputar.
- **Tratamiento:** Corregir el outlier (> 100 min) con la mediana del departamento `sewing`. Luego imputar nulos con la mediana por departamento.

---

### `over_time` — Tiempo extra (minutos)
- **Nulos:** 58 (4.81%)
- **Tipo de faltante:** Aleatorio
- **Causa:** Overtime no registrado. Existe un outlier imposible (129.600 min = 90 dias de overtime en un dia) que se corrige antes de imputar.
- **Tratamiento:** Corregir el outlier (> 25.000 min) con la mediana del departamento `finishing`. Luego imputar nulos con la mediana por departamento.

---

### `targeted_productivity` — Productividad objetivo
- **Nulos:** 61 (5.05%)
- **Tipo de faltante:** Aleatorio
- **Causa:** Meta de productividad no asignada para ese equipo y dia.
- **Tratamiento:** Imputar con la mediana del grupo `(department, day)`. Si el grupo no tiene mediana disponible, usar la mediana global.

---

### `no_of_workers` — Cantidad de trabajadores
- **Nulos:** 60 (4.97%)
- **Tipo de faltante:** Aleatorio
- **Causa:** Tamano del equipo no registrado.
- **Tratamiento:** Imputar con la mediana del equipo (`team`). Si el equipo no tiene mediana, usar la mediana global.

---

### `incentive` — Incentivo economico (BDT)
- **Nulos:** 60 (4.97%)
- **Tipo de faltante:** Ausencia del fenomeno (MNAR — Missing Not At Random)
- **Causa:** Si no hubo incentivo ese dia, no se registra el campo. El faltante significa $0.
- **Tratamiento:** Imputar con `0`.

---

### `idle_time` — Tiempo de inactividad (minutos)
- **Nulos:** 61 (5.05%)
- **Tipo de faltante:** Ausencia del fenomeno
- **Causa:** Si no hubo interrupcion de produccion, el campo queda vacio. El faltante significa que no hubo tiempo inactivo.
- **Tratamiento:** Imputar con `0`.

---

### `idle_men` — Trabajadores inactivos
- **Nulos:** 60 (4.97%)
- **Tipo de faltante:** Ausencia del fenomeno
- **Causa:** Si no hubo interrupcion, ningun trabajador estuvo inactivo. El faltante significa `0` trabajadores.
- **Tratamiento:** Imputar con `0`.

---

### `no_of_style_change` — Cambios de estilo
- **Nulos:** 60 (4.97%)
- **Tipo de faltante:** Ausencia del fenomeno
- **Causa:** Si no hubo cambio de estilo en el producto ese dia, el campo no se registra.
- **Tratamiento:** Imputar con `0`.

---

### `actual_productivity` — Productividad real (variable objetivo)
- **Nulos:** 60 (4.97%)
- **Tipo de faltante:** Aleatorio
- **Causa:** Productividad no medida o no reportada para ese equipo y dia.
- **Tratamiento:** Eliminar las filas sin productividad real. Es la variable objetivo del modelo — sin ella la fila es inutilizable para entrenamiento y evaluacion.

---

## Patron de faltantes

La coincidencia de ~60 nulos en casi todas las columnas sugiere la existencia de aproximadamente **60 filas completamente vacias** en el dataset. Estas filas fueron creadas en el sistema pero nunca completadas con datos reales. Al eliminar las filas sin `team`, `department` y `actual_productivity`, la mayoria de estos registros vacios desaparecen automaticamente.

```
Filas originales:           1207
Filas tras limpieza:        1024
Filas eliminadas:            183  (15.2%)
```

---

## Estrategias de imputacion aplicadas

| Estrategia | Columnas | Criterio |
|------------|----------|----------|
| Imputar con `0` | `wip` (finishing), `incentive`, `idle_time`, `idle_men`, `no_of_style_change` | Faltante = ausencia del fenomeno |
| Mediana por grupo | `wip` (sewing por equipo), `targeted_productivity` (por depto+dia), `no_of_workers` (por equipo), `smv` (por depto), `over_time` (por depto) | Preserva la variabilidad real del grupo |
| Eliminacion de fila | `actual_productivity`, `team`, `department` | Columnas criticas sin las cuales la fila no aporta informacion util |
| No imputar | `date`, `quarter`, `day` (residuales) | No se usan como predictores en el modelo |
