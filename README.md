# Detección de plagas
Fortalecimiento de capacidades, conocimientos y herramientas en ctei para el aprovechamiento de los residuos orgánicos del cultivo de marañón en el Departamento de Vichada

## Tabla de contenidos
- [Resumen](#resumen)
- [Conjuntos de datos utilizados](#conjuntos-de-datos-utilizados)
- [Flujo de trabajo](#flujo-de-trabajo)
- [Introducción](#introducción)
- [Requisitos previos](#requisitos-previos)
- [Flujo de trabajo completo](#flujo-de-trabajo-completo)
- [Pasos a seguir](#pasos-a-seguir)
- [Referencias](#referencias)
- [Agradecimientos](#agradecimientos)

## Resumen/Motivación
La agricultura es una ocupación muy importante y antigua en India. Dado que la economía de India se basa en la producción agrícola, es necesario prestar la máxima atención a la producción de alimentos. Las plagas como virus, hongos y bacterias causan infecciones en las plantas, lo que resulta en pérdida de calidad y cantidad en la producción. Los agricultores sufren grandes pérdidas en la producción debido a esto. Por lo tanto, es necesario cuidar adecuadamente las plantas. El procesamiento de imágenes proporciona formas más eficientes de detectar enfermedades causadas por hongos, bacterias o virus en las plantas. Las observaciones a simple vista para detectar enfermedades no son precisas y el uso excesivo de productos químicos también daña la calidad de los nutrientes de las plantas, lo que resulta en una gran pérdida de producción para los agricultores. Por lo tanto, el uso de técnicas de procesamiento de imágenes para detectar y clasificar enfermedades en aplicaciones agrícolas es útil.

## Conjuntos de datos utilizados
Para este proyecto, los conjuntos de datos son imágenes que se pueden descargar de 2 maneras:

1. Descarga por lotes de Fatkun (Extensión de Chrome)
2. FFmpeg - Herramienta de línea de comandos (extrae imágenes de videos)

## Flujo de trabajo
El flujo de trabajo se divide en 4 partes principales.

## Introducción
ImageNet es un conjunto de datos académicos común en el aprendizaje automático para entrenar un sistema de reconocimiento de imágenes. El código en este directorio demuestra cómo usar TensorFlow para entrenar y evaluar un tipo de red neuronal convolucional (CNN) en este conjunto de datos.

- [Artículo original](http://arxiv.org/abs/1512.00567)

## Requisitos previos
- Python 3.5+
- TensorFlow
- Docker Toolbox
- Android Studio

## Flujo de trabajo completo
- ¿Por qué usar un modelo preentrenado cuando podemos construir uno nuevo?
- ¿Cómo funciona la red preentrenada?
- Pasos a seguir:
  1. Instale Python 3.5 o superior, TensorFlow y Docker Toolbox.
  2. Coloque todos sus conjuntos de datos en una carpeta y el script de clasificación utilizará los nombres de las carpetas como nombres de etiquetas, y las imágenes dentro de cada carpeta deben ser imágenes que correspondan a esa etiqueta.
  3. Clone el repositorio y navegue hasta el directorio:
     ```
     git clone https://github.com/r-karthik/Detection-of-pests.git
     cd Detection-of-pests
     ```
  4. Para entrenar, puede descargar los conjuntos de datos de muestra de flores y extraerlos en la carpeta tf_files/training_dataset.
  5. Ejecute retrain.py en Docker con el siguiente comando. Mientras se entrena, puede iniciar TensorBoard en segundo plano y verá una serie de salidas de pasos, cada una mostrando precisión de entrenamiento, precisión de validación y la entropía cruzada:
     ```
     # Configurar la arquitectura
     TAMAÑO_IMAGEN=224
     ARQUITECTURA="mobilenet_0.50_${TAMAÑO_IMAGEN}"
     # El entrenamiento predeterminado es de 4000 iteraciones.
     python -m scripts.retrain --bottleneck_dir=tf_files/bottlenecks --model_dir=tf_files/models/"${ARQUITECTURA}" --summaries_dir=tf_files/training_summaries/"${ARQUITECTURA}" --output_graph=tf_files/retrained_graph.pb --output_labels=tf_files/retrained_labels.txt --architecture="${ARQUITECTURA}" --image_dir=tf_files/training_dataset
     # TensorBoard
     tensorboard --logdir tf_files/training_summaries &
     ```
  6. El entrenamiento se completó con un 92% de precisión.
  7. Cuando el entrenamiento esté completo, la salida será retrained_graph.pb y retrained_labels.txt. Puede probar el modelo utilizando el gráfico.
  8. Para realizar pruebas, ejecute el script label_image.py con el siguiente comando:
     ```
     python -m scripts.label_image --graph=tf_files/retrained_graph.pb --image=tf_files/testing_dataset/image_name.jpg
     ```
  9. Si desea ejecutar el modelo para predecir en un dispositivo Android, debe optimizar el modelo utilizando algunos métodos de compresión.
  10. Puede realizar la optimización del archivo retrained_graph.pb para que el modelo sea compresible ejecutando los siguientes comandos:
     ```
     # Gráfico optimizado
     python -m tensorflow.python.tools.optimize_for_inference --input=tf_files/retrained_graph.pb --output=tf_files/optimized_graph.pb --input_names="input" --output_names="final_result"
     # Gráfico redondeado
     python -m scripts.quantize_graph --input=tf_files/optimized_graph.pb --output=tf_files/graph.pb --output_node_names=final_result --mode=weights_rounded
     ```
  11. Pegue el archivo graph.pb y labels.txt en la carpeta android/tfmobile/assets y abra Android Studio, elija el proyecto existente tfmobile. Deje que la compilación de Gradle termine y ejecute su aplicación. Ahora puede utilizar su teléfono Android para clasificar objetos utilizando la aplicación.

## Referencias
1. [Enlace 1](https://ieeexplore.ieee.org/document/7155951/)
2. [Enlace 2](https://ieeexplore.ieee.org/document/7916653/)
3. [Enlace 3](https://www.sciencedirect.com/science/article/pii/S0168169917311742?via%3Dihub)
4. [Enlace 4](https://www.researchgate.net/publication/322941653_Deep_learning_models_for_plant_disease_detection_and_diagnosis)
