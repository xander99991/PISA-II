# Desigualdad de oportunidades educativas con PISA 2025

**¿Cuánto de lo que aprende un estudiante depende de dónde nació?**

Este proyecto mide la desigualdad de oportunidades educativas en los países de PISA 2025, con foco en Ecuador. Replica la metodología de Ferreira y Gignoux (2014, *World Bank Economic Review*) y la extiende con machine learning, análisis de clusters y un clasificador de riesgo de bajo desempeño.

> [Ver la lámina resumen del proyecto](https://xander99991.github.io/PISA-II/lamina_resumen_iop_ecuador_pisa2025.html)

---

## Contexto

Ferreira y Gignoux (Banco Mundial) propusieron dos medidas simples para comparar desigualdad educativa entre países con PISA 2006:

- **Desigualdad de logro:** la desviación estándar de los puntajes. A diferencia del Gini o el Theil, no cambia el ranking de países al estandarizar las notas.
- **Desigualdad de oportunidades (IOp):** la parte de la varianza de los puntajes que explican las **circunstancias** que el estudiante no eligió (educación y ocupación de los padres, libros y bienes en el hogar, migración, zona de la escuela, sexo). Se calcula como el **R²** de una regresión de la nota sobre esas circunstancias y es un límite inferior del efecto total del origen.

En 57 países encontraron que el origen explica entre 10 % y 35 % de la desigualdad de notas. Ecuador no participó en PISA 2006; con PISA 2025 es posible estimarlo con este método.

## Qué hace el proyecto

1. **Replica** la IOp y la desviación estándar para 89 países de PISA 2025, en matemáticas, usando los 10 valores plausibles y los 80 pesos replicados (BRR, método de Fay) para obtener errores estándar correctos.
2. **Extiende con machine learning:** compara MCO con Lasso, árboles, bosque aleatorio y gradient boosting, midiendo la IOp **fuera de muestra** con validación cruzada (con hiperparámetros elegidos dentro del entrenamiento) para evitar el sesgo por sobreajuste.
3. **Agrupa países** según nivel (puntaje medio), desigualdad total (DE) e injusticia (IOp) con K-means y Mean Shift.
4. **Clasifica riesgo:** predice si un estudiante quedará bajo el nivel 2 de matemáticas usando solo su origen (regresión logística y bosque aleatorio), con AUC, exactitud balanceada, matriz de confusión y odds ratios.

## Principales resultados para Ecuador

| Indicador | Valor | Lectura |
|---|---|---|
| Desigualdad de oportunidades (IOp) | **26,5 %** (EE 1,9) | Más de un cuarto de la desigualdad de notas se explica por el origen |
| Desviación estándar de puntajes | **64,8** (EE 1,1) | Dispersión baja frente a ~100 en la OCDE: las notas están juntas, pero abajo |
| Estudiantes bajo el nivel 2 (matemáticas) | **80,2 %** | 8 de cada 10 no alcanzan el mínimo de la OCDE |
| Poder predictivo del origen (AUC) | **0,78** | El origen identifica bien quién está en riesgo |

- Ecuador se ubica en la **mitad de América Latina**: por debajo de Brasil y Guatemala (~39 %) y por encima de Chile y México.
- El machine learning **confirma el método clásico**: la relación entre origen y logro es casi lineal y los modelos flexibles agregan poca IOp adicional.
- **Factores de riesgo:** padre o tutor sin trabajo (odds ratio 2,76). **Factores protectores:** internet y computadora en casa, madre universitaria, vivir en ciudad.

**En Ecuador el fracaso es generalizado, y el origen lo vuelve casi predecible.**

## Estructura del repositorio

```
├── scripts/
│   ├── 01_conversion_brechas.ipynb          # Conversión de microdatos PISA 2025 a parquet y brechas descriptivas
│   ├── 02_desigualdad_oportunidades.ipynb   # IOp y DE por país (Ferreira y Gignoux), con PV y pesos BRR
│   └── 03_ml_clusters.ipynb                 # IOp con machine learning, clusters de países y clasificación
├── lamina_resumen_iop_ecuador_pisa2025.html # Resumen visual del proyecto
└── README.md
```

## Datos

Los microdatos **no se incluyen** en el repositorio por su tamaño. Son públicos y se descargan del sitio de PISA de la OCDE (base de estudiantes `CY09_MS_STU_PUF` de PISA 2025).

Para reproducir:

1. Descarga los archivos de PISA 2025 y colócalos en la raíz del proyecto.
2. Ejecuta `01_conversion_brechas.ipynb`: convierte los datos a parquet particionados por país en `datos/`.
3. Ejecuta `02` y `03` en orden. Los resultados intermedios se guardan en `datos/resultados/`.

## Requisitos

Python 3.10 o superior con:

```bash
pip install pandas numpy pyarrow scikit-learn matplotlib jupyter
```

## Limitaciones

- **Selección muestral:** PISA solo cubre a jóvenes de 15 años escolarizados en 7.º grado o más. En países con baja cobertura, la desigualdad puede estar subestimada.
- **Circunstancias incompletas:** algunos países no aplicaron ciertas preguntas (por ejemplo, El Salvador y Costa Rica); su IOp está subestimada y se excluyen de las comparaciones principales.
- **Asociaciones, no causas:** los coeficientes y odds ratios describen asociaciones condicionadas; solo la IOp conjunta tiene interpretación de límite inferior.
- Algunos resultados del clasificador provienen de un solo valor plausible y una partición aleatoria por estudiante (no por escuela), por lo que pueden ser algo optimistas.

## Referencia principal

Ferreira, F. H. G., y Gignoux, J. (2014). The Measurement of Educational Inequality: Achievement and Opportunity. *The World Bank Economic Review*, 28(2), 210–246. https://doi.org/10.1093/wber/lht004

## Autor

Alexander Fernández Pérez · Maestría en Ciencia de Datos, Yachay Tech
