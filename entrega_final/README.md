# Proyecto Final — Análisis de Datos

**Predicción de la productividad de operarios en una fábrica textil**

Integrantes: Azael Pignanessi · Facundo Sansalone
Curso: Análisis de Datos — ING0039 (Universidad de Montevideo)

## Contenido de la entrega

| Archivo | Descripción |
|---|---|
| `Informe_Proyecto_Final.pdf` | Informe final con todas las secciones pedidas (entregable principal). |
| `Proyecto_Final.ipynb` | Notebook con el código Python ejecutado y sus salidas. |
| `Tema_11.csv` | Dataset original (*Garments Worker Productivity*). |
| `Tema_11_clean.csv` | Dataset limpio generado por el notebook (etapa de pretratamiento). |
| `requirements.txt` | Dependencias de Python. |

El trabajo integra los prácticos 1 (EDA y pretratamiento), 4 (PCA) y 5 (modelos predictivos),
todos sobre el mismo dataset.

## Estructura del informe

1. Descripción del problema
2. Descripción del conjunto de datos
3. Análisis exploratorio de datos (EDA)
4. Pretratamiento de datos
5. Análisis de datos mediante visualizaciones (PCA)
6. Construcción del modelo predictivo
7. Resultados obtenidos
8. Conclusiones

## Cómo ejecutar

```bash
pip install -r requirements.txt
jupyter notebook Proyecto_Final.ipynb   # o abrir en VS Code y "Run All"
```

El notebook lee `Tema_11.csv`, regenera `Tema_11_clean.csv` en la etapa de pretratamiento
y produce todas las figuras y resultados de forma reproducible (semilla fija `random_state=42`).
