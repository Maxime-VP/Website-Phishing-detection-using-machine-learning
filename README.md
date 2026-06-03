El código está separado por secciones. En la sección de entrenamiento se prueba el desempeño de los modelos. En la carpeta de pruebas, se reserva para más adelante probar los modelos con instancias generadas por un usuario con una interfaz.

Para la revisión más cómoda del modelo también dejo una carpeta de código monolítico en la que el programa corre en un solo .ipynb, este código no incluye las mejoras aplicadas. Si se quiere ver la limpieza y el entrenamiento por separado se puede hacer también, para el entrenamiento es necesario cargar el excel que se encuentra en la carpeta correspondiente; para las pruebas, se deben cargar todos los documentos adjuntos en la carpeta de pruebas.

# Website-Phishing-detection-using-machine-learning

Este proyecto tiene como objetivo desarrollar un modelo de Machine Learning que permita clasificar sitios web como legítimos, phishing o sospechosos a partir de características extraídas de sus URLs y comportamiento web.

Para este proyecto estaré utilizando el dataset de Abdelhamid, N. (2014). Website Phishing [Dataset]. UCI Machine Learning Repository. https://doi.org/10.24432/C5B301.

Este dataset contiene 1353 instancias compuestas de 9 columnas correspondientes a features y una columna target ("Result").

La columna target puede tener 3 valores -1 (phishing), 0 (sospechoso) y 1 (sitio seguro).

Tiene una distribución de:

- 702 sitios phishing

- 548 sitios legítimos

- 103 sitios sospechosos


Las columnas de features son las siguientes: (Variable-Descripción)

- SFH - Server Form Handler (describe el comportamientod de formularios en la página web)

- popUpWindow - Presencia de ventanas emergentes

- SSLfinal_State - Estado del certificado SSL (Existencia y validez de certificados https)

- Request_URL - URLs externas solicitadas

- URL_of_Anchor - Comportamiento de enlaces anchor

- web_traffic - Popularidad/tráfico del sitio

- URL_Length - Longitud de la URL

- age_of_domain - Antigüedad del dominio

- having_IP_Address - Uso de dirección IP en la URL

## Split
Para este proyecto estoy haciendo dos splits de 80% y 20% cuidando que cada clase esté representada de forma proporciona en cada división de los datos. Esto hace que queden el siguiente número de ejemplos:

### En train: 
450 de phishing, 66 sospechosos, 350 legítimos
### En test:
140 de phishing, 21 sospechosos  y 110 legitimos. 
### En validation:
112 de phishing, 16 sospechosos, y 88 legítimos.

En el caso de sitios sospechosos la cantidad de ejemplos no es tanta por lo que se espera que el modelo tenga más trabajo para distinguirla de las demás clases.

# Paper:

Para este proyecto me estoy basando en el paper "A high-accuracy phishing website detection method based on machine learning": [1]

Este paper probó varios modelos para resolver el mismo problema utilizando parámetros distintos: Los modelos que se tratan son  Logistic Regression, K-Nearest Neighbors, Naive Bayes, Random Forest, Support Vector Machine, y Extreme Gradient Boosting (XGBoost). 

De estos modelos he seleccionado únicamente 3 Logistic Regression, Random Forest y XGBoost. El primer modelo tuvo un desempeño moderado y lo quiero más que nada como un benchmark. Los otros dos modelos fueron los que mejor desempeño tuvieron alcanzando un F1 ligeramente arriva de 95 antes de ajustar los hiperparámetros.

El experimento fue realizado con un dataset público de 58,000 ejemplos de sitios legítimos y 30,647 de phishing, además de contar con 112 atributos. El dataset que empleo yo tiene una clase adicional, muchos menos ejemplos y solo 9 atributos que se vuelven 25 tras el encoding. Por estas características se espera que las métricas que se obtengan sean peores que las del paper, pero lo ideal es que se acerquen lo más posible (al menos entre 85 y 95 del F1 score).

El paper menciona al XGBoost como el mejor modelo para resolver el problema ya que alcanza hasta un 99% de accuracy, precision, recall y specificity. Además, su runtime es bastante rápido. 

Para los modelos que voy a hacer quiero usar de métrica principalmente a F1 score (y a accuracy y el resto de métricas como soporte). El motivo es que el F1 score es menos suceptible a datasets desbalanceados y la clasificación intermedia de sitio sospechoso aparece mucho menos que las otras dos clases. Además, como hay 3 clasificaciones posibles en lugar de 2, prefiero utilizar una métrica que combina las métricas de precision y recall en lugar de analizarlas individualmente para cada clase (aunque sí serán calculadas).

Accuracy lo quiero para contrastarlo con el F1, si ambos son muy similares, es una buena señal de que el modelo funciona bien para clasificar las 3 posibles clasificaciones. Si al cotrario el accuracy y el f1 son muy diferentes, significa que probablemente el modelo no esté detectando bien una de las clases (probablemente por desbalance).

En el paper se utilizan 8 escenarios distintos que no son más que diferentes combinaciones de aplicar balanceo, eliminar constantes y PCA (Principal Component Analysis es cuando dos atributos son muy parecidos se combinan en una sola variable). El escenario 1 es donde no aplican ninguna de las 3 herramientas y el escenario 4 (el que obtuvo mejores resultados) es cuando usaron balanceo y eliminación de constantes pero no PCA. En el contexto de mi dataset eliminar constantes y PCA no tienen mucho sentido, pero balancear mejor el dataset sí puede servir para mejorar el desempeño del modelo.

La técnica de balanceo que emplean es oversampling con SMOTEENN, (la misma que empleamos en MATCHAI). Esta lo que hace es generar nuevas instancias artificiales de la clase desbalanceada con menos ejemplos (SMOTE) y (ENN [Edited Nearest Neighbors]) elimina observaciones ruidosas o ambiguas. 

Por el momento solo tengo los 3 modelos base (escenario 0). Una de las posibles mejoras que no sean el ajuste de hiperparámetros es aplicar esta técnica de balanceo.

## Modelos:

Para este problema estoy trabajando con 3 modelos distintos, regresión logística, random forest y xgboost. Como mencioné anteriormente se espera que el primer modelo tenga un desempeño moderadamente bueno y lo quiero más que nada como un benchmark. Los otros dos modelos fueron los que mejor desempeño tuvieron alcanzando en el problema resuelto en el paper con un F1 ligeramente arriva de 95 antes de ajustar los hiperparámetros.

Después de obtener un baselina apliqué dos mejoras, la primera fué una mejora en los hiperparámetros enfocadas en reducir el overfitting. En esta se consiguió mejorar ligeramente los resultados, pero es algo tan mínimo que podría ser algo espurio. En la segunda mejora apliqué SMOTEEEN para generar más ejemplos de la clase sospechosa la cuál hace que el dataset esté desbalanceado. Esta segunda mejora permite que la clase sospechosa sea más fácil de detectar correctamente. Sin embargo, esto causa que al modelo le cueste un poco más de trabajo detectar las otras dos clases. En el contexto de phishing es probable que sea mejor quedarse con el modelo sin smoteen, o mejor aún evaluar con ambos modelos antes de tomar una decisión, de esta forma puedes evaluar si un sitio sospechoso para el modelo con smote es peligroso, seguro o también sospechoso para el modelo sin smote.

### Regresión logística
El modelo analiza las características de entrada y calcula una combinación lineal de ellas. Posteriormente, aplica una función sigmoide para transformar el resultado en una probabilidad. 

Hiperparámetros (default) del primer modelo:
{'alpha': 0.0001,
 'average': False,
 'class_weight': None,
 'early_stopping': False,
 'epsilon': 0.1,
 'eta0': 0.0,
 'fit_intercept': True,
 'l1_ratio': 0.15,
 'learning_rate': 'optimal',
 'loss': 'hinge',
 'max_iter': 1000,
 'n_iter_no_change': 5,
 'n_jobs': None,
 'penalty': 'l2',
 'power_t': 0.5,
 'random_state': None,
 'shuffle': True,
 'tol': 0.001,
 'validation_fraction': 0.1,
 'verbose': 0,
 'warm_start': False}

Hiperparámetros del primer modelo mejorado y del modelo con SMOTEEN: Son los mismos que el default con 
penalty='l2' - Reduce pesos muy grandes
alpha=0.0005 - Que tan severo es el penalty
eta0=0.0005 - learning rate

Resultados:

Entrenamiento:

<img height="300" alt="image" src="https://github.com/user-attachments/assets/bae84253-ed43-45be-8d6e-e62c990a7ca6" />
<img height="300" alt="image" src="https://github.com/user-attachments/assets/3dff1bd7-363f-4c17-a6de-f219650071aa" />
<img height="300" alt="image" src="https://github.com/user-attachments/assets/3ff10113-e1eb-4587-b54d-32c85e14572a" />

Matriz de confusión:

<img height="300" alt="image" src="https://github.com/user-attachments/assets/e8672bde-ac6b-4a61-94e8-7964dc92f0ec" />
<img height="300" alt="image" src="https://github.com/user-attachments/assets/d92831a5-e042-40b9-927b-1d1824dbbb2b" />
<img height="300" alt="image" src="https://github.com/user-attachments/assets/aeab04c9-9e89-4f68-b4ec-525a738a5477" />

Métricas

<img height="300" alt="image" src="https://github.com/user-attachments/assets/3c3fd9c9-a755-46d1-8f2a-e709af83293e" />
<img height="300" alt="image" src="https://github.com/user-attachments/assets/bdd5f123-4503-47f3-8b11-fdd31114b8a4" />
<img height="300" alt="image" src="https://github.com/user-attachments/assets/6a5fde21-808c-4cdb-aa56-254f89627a39" />

### Random forest
Random Forest es un algoritmo de clasificación y regresión basado en un conjunto de árboles de decisión. En lugar de construir un único árbol, genera múltiples árboles independientes y combina sus resultados para obtener una predicción más robusta.

Cada árbol se entrena con una muestra aleatoria de datos y cuando se predice lo que pasa es una votación. Cada árbol vota por una clase y la clase por la que la mayoría vota es la clase elegida.

Para este modelo los hiperparámetros que estoy empleando son:
n_estimators=n,
max_depth=None,
min_samples_leaf=1

Para las dos mejoras:
n_estimators=n,
max_depth=10,
min_samples_leaf=2

n_estimators es el número de árboles creados, en las tres versiones de este modelo lo que estoy haciendo es entrenar muchos modelos cada uno con diferentes números de árboles. Los resultados mostrados son los de el último modelo generado (el que tiene más árboles), sin embargo las graficas muestran que a partir de 100 el modelo se vuelve estable y tiene un buen desempeño entonces no tiene gran impacto (lo ideal sería guardar la versión con mejor desempeño en validation).




## Referencia 
[1] M. Bahaghighat, M. Ghasemi y F. Ozen,
“A high-accuracy phishing website detection method based on machine learning,”
Journal of Information Security and Applications,
vol. 77,
art. 103553,
2023.
doi: 10.1016/j.jisa.2023.103553.
Available: https://www.sciencedirect.com/science/article/pii/S2214212623001370
