# **Análisis de Orientación Política en Transcripciones con BERT**

Este proyecto implementa un pipeline completo de Procesamiento de Lenguaje Natural (PLN) para detectar orientación política (“izquierda” vs “derecha”) en fragmentos de texto provenientes de transcripciones. Se parte del preprocesamiento del corpus, extracción de entidades, filtrado semántico, construcción del dataset, y se finaliza con el fine-tuning de un modelo **BERT en español** para clasificación binaria.

El trabajo está dividido en cuatro etapas principales, totalmente reproducibles.

---

## **📌 1. Preprocesamiento del corpus**

* Se cargan las transcripciones clasificadas en carpetas:
  **`transcripciones/izquierda`**, **`transcripciones/derecha`**, **`transcripciones/neutral`**.
* Cada archivo se segmenta en frases usando reglas simples de puntuación.
* Se extraen entidades nombradas (PER, ORG, LOC) con **spaCy (es_core_news_sm)**.
* Se genera un archivo JSON por transcripción con todas las frases procesadas y las entidades detectadas.

Salida principal de esta etapa:

```
/resultados/*.json
```

*(Referencia del código fuente: )*

---

## **📌 2. Análisis de entidades y filtrado**

### **2.1 Frecuencias de entidades**

Se recorren todos los JSON para construir un ranking global de entidades mencionadas.
Esto genera:

```
entidades_frecuentes.csv
```

*(Referencia: )*

### **2.2 Limpieza del ranking**

Se filtran palabras irrelevantes, expresiones coloquiales y términos no alfabéticos.
Se conserva solo un top 100 de entidades políticamente útiles:

```
entidades_top100_limpias.csv
```

*(Referencia: )*

### **2.3 Filtrado de frases relevantes**

Se recorren los JSON originales y se seleccionan solo las frases que contienen entidades relevantes del top 100. Se genera:

```
/resultados_filtrados/izquierda/*.json
/resultados_filtrados/derecha/*.json
/resultados_filtrados/neutral/*.json
```

Finalmente, se construye el dataset balanceado y listo para entrenamiento:

```
train_significativo.csv
```

*(Referencia: )*

---

## **📌 3. Fine-tuning de BERT**

Se utiliza el modelo base:

```
dccuchile/bert-base-spanish-wwm-cased
```

El pipeline incluye:

* Tokenización
* Chunking automático para textos largos
* División train/test (80/20)
* Entrenamiento con métricas: accuracy, precision, recall, F1
* Guardado del mejor modelo

Salida del modelo:

```
/modelo_finetuneado_significativo/
```

*(Referencia del código: )*

---

## **📌 4. Clasificación de textos nuevos**

Con el modelo fine-tuneado se clasifica cada frase:

* Se chunkearon las frases largas igual que en el entrenamiento.
* Se calcula el score promedio por fragmento.
* Si score > 0.5 → **“derecha”**, si no → **“izquierda”**.
* Se genera un JSON con:

  * etiqueta final
  * score promedio
  * detalle por frase

Salida:

```
/resultados_significativos/izquierda/*.json
/resultados_significativos/derecha/*.json
```

*(Referencia de implementación: )*

---

## **📂 Estructura del proyecto**

```
.
├── transcripciones/
│   ├── izquierda/
│   ├── derecha/
│   └── neutral/
├── resultados/
├── resultados_filtrados/
├── resultados_significativos/
├── entidades_frecuentes.csv
├── entidades_top100_limpias.csv
├── train_significativo.csv
├── modelo_finetuneado_significativo/
├── PLN.ipynb
├── PLN.pdf
└── README.md
```

---

## **📦 Instalación**

```bash
pip install --upgrade pip
pip install torch torchvision torchaudio --index-url https://download.pytorch.org/whl/cu118
pip install transformers datasets scikit-learn pandas ipywidgets spacy
python -m spacy download es_core_news_sm
```

---

## **▶️ Ejecución del pipeline**

1. Ejecutar el preprocesamiento completo (Parte 1)
2. Ejecutar análisis de entidades y filtrado (Parte 2)
3. Entrenar BERT (Parte 3)
4. Clasificar textos nuevos (Parte 4)

Cada sección del notebook reproduce exactamente estos pasos.

---

## **📊 Resultados esperados**

* Más de **29.000 frases significativas** detectadas para entrenamiento.
* Clasificador BERT entrenado para detectar orientación política con métricas sólidas.
* Exportación de resultados por archivo, con detalle de scores por frase.

---

## **📘 Documentación técnica**

El archivo PDF incluido contiene toda la explicación teórica y práctica del pipeline y sus fundamentos:

>

---

## **✔️ Estado del proyecto**

✔️ Pipeline completo
✔️ Dataset generado
✔️ Modelo BERT fine-tuneado
✔️ Clasificación de transcripciones neutral
✔️ Código documentado
