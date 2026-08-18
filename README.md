# Resumen del Proyecto — Clasificación de Enfermedades en Hojas de Tomate

**Autor:** Jacobo Diaz Alvarado

## Resumen

Proyecto para analizar el efecto de las capas convolucionales en la clasificación de imágenes (hojas de tomate). Se siguieron los pasos del enunciado hasta el punto 5 (EDA, modelo baseline sin convoluciones, diseño de una CNN desde cero, experimento controlado y reflexión arquitectónica). El punto 6 (despliegue en SageMaker) queda como espacio para evidencias y scripts posteriores.

## Qué se hizo

- Selección y preparación del dataset: se creó un subconjunto equilibrado de `plantvillage` con 4 clases (`Tomato___healthy`, `Tomato___Late_blight`, `Tomato___Septoria_leaf_spot`, `Tomato___Spider_mites Two-spotted_spider_mite`) y ~5,558 imágenes en total.
- Exploración (EDA): revisión de conteos por clase, visualización de ejemplos y comprobación de dimensiones y canales (todas las imágenes en RGB y 256×256).
- Baseline (sin convoluciones): modelo `Flatten` + `Dense` (128→64→4), ~25M parámetros, entrenamiento con `Rescaling(1./255)`. Resultado: train acc ≈ 0.86, val acc ≈ 0.82 (inestabilidad observada). Limitaciones: muchos parámetros, alto costo computacional y sensibilidad al learning rate.
- CNN diseñada desde cero: arquitectura simple con 2 capas conv (16@5x5 → 32@3x3), `padding='same'`, `ReLU`, `MaxPooling2D`, `GlobalAveragePooling2D` y dense final. Menos parámetros (~8k) y mejor desempeño.
- Experimento controlado: comparativa entre CNN de 2 capas y 3 capas (todo lo demás fijo). Resultado: la CNN de 2 capas superó a la de 3 capas en validación y fue más rápida por epoch bajo las condiciones de CPU y 10 épocas.
- Interpretación: la convolución preserva vecindad espacial y agrega un sesgo inductivo útil para detectar texturas y patrones locales; no es adecuada para datos tabulares sin estructura espacial.

## Descubrimientos principales

- Normalizar entradas (rescaling) fue crítico: sin ello el entrenamiento colapsó.
- Un learning rate demasiado alto (p.ej. 0.01) llevó a colapso; 0.0001 permitió aprendizaje estable.
- Más capas no siempre mejora: la CNN de 2 capas fue mejor que la de 3 bajo el límite de cómputo usado.
- `GlobalAveragePooling2D` reduce drásticamente el número de parámetros comparado con `Flatten()` sin perder la representatividad global de los mapas de características.

## Cómo reproducir

1. Abrir y ejecutar el notebook `NN_plant_detection.ipynb` (celdas en orden). Las dependencias principales son: `tensorflow`, `matplotlib`, `Pillow`.
2. Recomiendo ejecutar en un entorno con GPU si se dispone para reducir tiempo de entrenamiento.


## Evidencia de SageMaker 



---

