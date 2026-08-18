# Clasificación de Enfermedades en Hojas de Tomate

**Autor:** Jacobo Diaz Alvarado

## Resumen

Proyecto para analizar cómo las capas convolucionales afectan el aprendizaje y desempeño de una red neuronal para clasificación de imágenes.

Elegí trabajar con hojas de tomate porque actualmente estoy cultivando tomates en mi balcón, por lo que me pareció interesante aplicar el modelo a un problema relacionado con algo que estoy experimentando personalmente.

Se trabajó con un subconjunto de **PlantVillage** de aproximadamente 5.558 imágenes y cuatro clases:

- `Tomato___healthy`
- `Tomato___Late_blight`
- `Tomato___Septoria_leaf_spot`
- `Tomato___Spider_mites Two-spotted_spider_mite`

## Trabajo realizado

- Preparación y balanceo del dataset.
- EDA y revisión de imágenes, dimensiones y clases.
- Construcción de un modelo **baseline sin convoluciones**.
- Diseño de una **CNN desde cero**.
- Comparación entre una CNN de 2 capas convolucionales y otra de 3 capas.
- Evaluación de accuracy y loss en entrenamiento y validación.
- Pruebas de predicción sobre imágenes individuales.
- Preparación del modelo para su almacenamiento y despliegue en AWS SageMaker.

## Descubrimientos principales

- La normalización de las imágenes mediante `Rescaling(1./255)` fue importante para conseguir un entrenamiento estable.
- Un `learning_rate` demasiado alto provocó problemas de aprendizaje, mientras que `0.0001` produjo resultados más estables.
- Agregar más capas convolucionales no garantizó un mejor resultado. Bajo las condiciones de cómputo utilizadas, la CNN de 2 capas obtuvo mejores resultados de validación que la de 3 capas.
- `GlobalAveragePooling2D` permitió mantener una arquitectura pequeña y reducir considerablemente la cantidad de parámetros.
- Las convoluciones son especialmente útiles para este problema porque permiten aprender patrones espaciales y características locales presentes en las hojas.

## Resultado destacado

La CNN utilizada en el experimento alcanzó aproximadamente:

- **Accuracy de entrenamiento:** 85.3%
- **Accuracy de validación:** 79.8%

Estos resultados muestran que una arquitectura convolucional relativamente pequeña puede aprender características relevantes de las imágenes sin necesidad de utilizar una red con millones de parámetros.

## SageMaker

Se avanzó hasta la etapa de despliegue del modelo en **AWS SageMaker**.

Se logró:

- Guardar correctamente el modelo.
- Empaquetarlo como `model.tar.gz`.
- Subirlo correctamente a un bucket de Amazon S3.
- Verificar que el archivo existiera en S3.
- Crear correctamente el objeto `TensorFlowModel` en SageMaker.
- Intentar crear un endpoint de inferencia.

El proceso presentó inicialmente problemas de conectividad con SageMaker Studio. El kernel permaneció en estado **Connecting** y la página dejó de cargar correctamente. También apareció un error de guardado del notebook `NN_plant_detection.ipynb`.

Una vez recuperado el acceso a SageMaker, se continuó con el despliegue. Sin embargo, la creación del endpoint fue rechazada por AWS con un `AccessDeniedException`. El rol `LabRole` utilizado por el entorno de laboratorio no tenía autorización para ejecutar:

```text
sagemaker:CreateEndpointConfig
