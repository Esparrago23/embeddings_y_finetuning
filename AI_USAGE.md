# Declaración de uso de IA

**Herramienta utilizada:** Claude (Anthropic)

A continuación se declara dónde se usó IA, para qué celda, qué proporcionó y qué se revisó o modificó.


## Lab 5 — Embeddings y búsqueda semántica

### 1. Analogías por aritmética vectorial (celda A.4)
- **Qué dio la IA:** el código con `ft.get_analogies` para probar `rey - hombre + mujer`, con el formato de impresión de resultados.
- **Qué hice yo:** elegí la segunda analogía, ejecuté ambas, analicé los resultados y redacté la respuesta explicando cuándo acierta (relación simple como rey/reina) y cuándo falla (relación ambigua como mascota/alas, donde el modelo da `plumas` y `alitas` en lugar de `ave`).

### 2. Buscador semántico (celda B.2)
- **Qué dio la IA:** la función `buscar_semantico` completa: preprocesar la consulta, promediar los vectores de sus tokens con `vector_documento` y rankear por coseno contra `EMB_DOCS`.
- **Qué hice yo:** ejecuté la prueba con "problemas de agua", verifiqué que d02 (Crisis hídrica) apareciera entre los primeros resultados y comparé contra lo que daban TF-IDF y BM25.

### 3. Comparación de los tres sistemas (celda B.3)
- **Qué dio la IA:** la función `comparar_3` que imprime TF-IDF, BM25 y semántico lado a lado para tres consultas y marca los documentos que solo el semántico encuentra.
- **Qué hice yo:** elegí las tres consultas de prueba, analicé los resultados e identifiqué los casos donde el semántico supera a los otros (vocabulario diferente, mismo significado).

### 4. Re-evaluación con métricas del Lab 3 (celda B.4)
- **Qué dio la IA:** el código que llama a `evaluar` para los tres sistemas y presenta la tabla comparativa de nDCG@5 y MAP.
- **Qué hice yo:** interpreté los números (TF-IDF y BM25: 0.931, semántico: 0.996) y cheque porque los vectores de palabras sinónimas apuntan en la misma dirección en el espacio de 300 dimensiones.

---

## Lab 6 — Fine-tuning de BERT

### 5. Entorno — fix de compatibilidad de datasets (celda 0 · Setup)
- **Qué dio la IA:** diagnóstico del error `RuntimeError: Dataset scripts are no longer supported` (datasets 3.x eliminó soporte para scripts .py) y la solución `!pip install -q datasets==2.21.0` para forzar la versión compatible. También diagnosticó el error secundario con `eriktks/conll2002` como espejo válido para CoNLL-2002 cuando el dataset original falla por namespace.
- **Qué hice yo:** ejecuté el fix, reinicié el entorno y verifiqué que los datasets cargaran correctamente.

### 6. Línea base SBERT y métricas (celda A.1)
- **Qué dio la IA:** las funciones `buscar_sbert` (coseno entre vector de consulta y embeddings del corpus) y `ndcg_medio` (promedio de nDCG@5 sobre todas las consultas de los qrels), reutilizando los qrels del Lab 3.
- **Qué hice yo:** ejecuté la línea base, obtuve nDCG@5 = 0.800 y lo usé como punto de comparación para el afinado.

### 7. Fine-tuning SBERT con qrels (celda A.2)
- **Qué dio la IA:** la construcción de pares `InputExample(texts=[consulta, documento])` para positivos con grado ≥ 2, el `DataLoader`, `MultipleNegativesRankingLoss` y la llamada a `modelo.fit(epochs=2)`.
- **Qué hice yo:** ejecuté el entrenamiento, obtuve nDCG@5 = 0.883 (+0.083 sobre la base) y redacté el comentario: el afinado mejora pero no supera a FastText (0.996) porque  hay pocos datos para que el modelo mejore.

### 8. Tokenización con BETO (celda B.2)
- **Qué dio la IA:** la función `tokenizar` con `truncation=True, padding='max_length', max_length=128` y el `set_format` para convertir a tensores PyTorch.
- **Qué hice yo:** ejecuté la tokenización sobre train y test y verifiqué que los formatos quedaran correctos para el Trainer.

### 9. Fine-tuning de sentimiento (celda B.3)
- **Qué dio la IA:** `compute_metrics` (accuracy + f1_macro), `AutoModelForSequenceClassification`, `TrainingArguments` (lr=2e-5, fp16=True, 3 épocas) y el `Trainer` completo.
- **Qué hice yo:** ejecuté el entrenamiento, obtuve accuracy=0.68 y f1_macro=0.68, y redacté el análisis: neutral es la clase más difícil y F1-macro es mejor criterio que accuracy.

### 10. Alineación de subpalabras para NER (celda C.2)
- **Qué dio la IA:** la función `tokeniza_y_alinea` completa: `tok(..., is_split_into_words=True)`, uso de `enc.word_ids(batch_index=i)` para asignar la etiqueta real solo a la primera subpalabra de cada palabra y `-100` al resto (incluyendo `[CLS]` y `[SEP]`).
- **Qué hice yo:** ejecuté el mapeo sobre todo el dataset, verifiqué el ejemplo de "Tuxtla Gutiérrez" (se fragmenta en Tu/##x/##t/##la) .

### 11. Evaluación NER con seqeval (celda C.4)
- **Qué dio la IA:** la reconstrucción de secuencias ignorando `-100` y la llamada a `seq_report` de seqeval.
- **Qué hice yo:** ejecuté la evaluación, obtuve F1 macro=0.85, e interpreté los resultados: seqeval es más exigente que accuracy porque evalúa entidades completas (o aciertas todo o falla la entidad entera); MISC es la más difícil (F1=0.67) porque es una categoría residual, como meter todo lo que no encaja en un solo montón.


