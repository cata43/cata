# Laboratorio 3: Segmentación de esclerosis múltiple y glioblastoma

Curso BME423. Enunciado: <https://aavelozb.github.io/bme423/evals/lab3.html>

## Contenido

| Archivo | Descripción |
|---|---|
| `lab3_segmentacion_EM_GBM.ipynb` | **Entregable.** Notebook ejecutado, con todas las salidas (figuras y tablas). |
| `resultados/` | Tablas comparativas y de ablación en CSV (las genera el notebook). |
| `requirements.txt` | Dependencias de Python. |
| `data/` | Imágenes. **No se versionan**: el notebook las descarga al ejecutarse. |

## Qué hace el notebook

Tres *pipelines* de segmentación 3D, cada uno con las etapas preprocesamiento → segmentación → postproceso → evaluación:

| | A (determinista) | B (espectral + agrupamiento) | C (probabilístico + contexto) |
|---|---|---|---|
| Filtro | Perona-Malik (implementación propia) | Butterworth pasa-bajos con `numpy.fft` (espectro antes y después) | Gaussiano leve |
| Segmentación | Otsu + umbral adaptativo | *k-means* | Mezcla gaussiana + ICM sobre un MRF de Potts (regla de actualización propia) |
| Parámetros del experimento | EM: κ, c · GBM: κ, n_iter | f_c, k | EM: c, β · GBM: K, β |

Se aplican a esclerosis múltiple (patient04, 08 y 14) y a glioblastoma (tumor completo y tumor realzante). El notebook incluye:

* métricas por vóxel (Dice, Jaccard, sensibilidad, VPP, error de volumen) y por lesión (tasa de detección, FP lesionales, Dice de lesiones < 50 vóxeles);
* el experimento de parámetros (elegir en el paciente 1, transferir a los pacientes 2 y 3, y comparar con el óptimo propio);
* tablas comparativas, contornos y mapas de error;
* ablación (sin filtrado, sin postproceso y β = 0);
* discusión y recomendación por problema.

**Resultado principal:** para EM recomiendo el *pipeline* C, y para GBM el *pipeline* B (ver la sección 7 del notebook).

## Datos

El notebook intenta bajar primero los zip del curso (`lab3-ms.zip` y `lab3-gbm.zip`). Si están en `data/`, los usa directamente. Si no los puede bajar:

* **EM:** baja los mismos tres pacientes desde [Open MS data](https://github.com/muschellij2/open_ms_data) (los volúmenes lesionales coinciden con el enunciado).
* **GBM:** usa como **sustituto** dos glioblastomas de BraTS 2015 (ejemplos de [DeepMedic](https://github.com/deepmedic/deepmedic)), con las etiquetas convertidas a la convención del enunciado (1 necrosis, 2 edema, 3 realce). Sólo traen FLAIR y CT1.

> La versión ejecutada que está en el repositorio usó el sustituto de GBM, porque desde el entorno de ejecución no se podía acceder a `aavelozb.github.io`. Para usar LUMIERE, basta con poner `lab3-gbm.zip` en `data/` y volver a ejecutar.

## Cómo ejecutarlo

```bash
cd lab3_segmentacion
pip install -r requirements.txt
jupyter notebook lab3_segmentacion_EM_GBM.ipynb     # o: jupyter nbconvert --to notebook --execute --inplace lab3_segmentacion_EM_GBM.ipynb
```

La ejecución completa toma unos 15 minutos en un CPU de 4 núcleos (la mayor parte se va en los barridos de parámetros) y necesita unos 4 GB de RAM y unos 150 MB de descarga.
