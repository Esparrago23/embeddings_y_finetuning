# Declaración de uso de IA

**Herramienta utilizada:** Claude (Anthropic)

A continuación se declara dónde se usó IA, para qué celda, qué proporcionó y qué se revisó o modificó.


## Lab 5 — Embeddings y búsqueda semántica

### 1. Analogías por aritmética vectorial (celda A.4)
- **Qué dio la IA:** el código con `ft.get_analogies` para probar `rey - hombre + mujer` y una segunda analogía propia (`mascota - perro + alas`), con el formato de impresión de resultados.
- **Qué hice yo:** elegí la segunda analogía, ejecuté ambas, analicé los resultados y redacté la respuesta explicando cuándo acierta (relación simple y frecuente como rey/reina) y cuándo falla (relación ambigua como mascota/alas, donde el modelo da `plumas` y `alitas` en lugar de `ave`).

### 2. Buscador semántico (celda B.2)
- **Qué dio la IA:** la función `buscar_semantico` completa: preprocesar la consulta, promediar los vectores de sus tokens con `vector_documento` y rankear por coseno contra `EMB_DOCS`.
- **Qué hice yo:** ejecuté la prueba con "problemas de agua", verifiqué que d02 (Crisis hídrica) apareciera entre los primeros resultados y comparé contra lo que daban TF-IDF y BM25.

### 3. Comparación de los tres sistemas (celda B.3)
- **Qué dio la IA:** la función `comparar_3` que imprime TF-IDF, BM25 y semántico lado a lado para tres consultas y marca los documentos que solo el semántico encuentra.
- **Qué hice yo:** elegí las tres consultas de prueba, analicé los resultados e identifiqué los casos donde el semántico supera a los otros (vocabulario diferente, mismo significado).

### 4. Re-evaluación con métricas del Lab 3 (celda B.4)
- **Qué dio la IA:** el código que llama a `evaluar` para los tres sistemas y presenta la tabla comparativa de nDCG@5 y MAP.
- **Qué hice yo:** interpreté los números (TF-IDF y BM25: 0.931, semántico: 0.996) y redacté la respuesta explicando que la mejora se da en consultas con vocabulario distinto al del documento, porque los vectores de palabras sinónimas apuntan en la misma dirección en el espacio de 300 dimensiones.


