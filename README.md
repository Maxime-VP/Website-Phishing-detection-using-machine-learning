# Si este comentario lo estás viendo antes del 31 de mayo a las 11:59pm, todavía no está completo el trabajo, por favor aún no lo revises. Subí el enlace para evitar problemas con Canvas, pero me falta separar en limpieza entrenamiento y pruebas; y explicar el paper que utilicé al igual que los modelos y sus hiperparámetros. Si el comentario está y ya no es la fecha, entonces todo el código se encuentra en el .ipynb dentro de la carpeta "Codigo Monolítico"


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

Para este proyecto me estoy basando en el paper "A high-accuracy phishing website detection method based on machine learning":

[1] M. Bahaghighat, M. Ghasemi y F. Ozen,
“A high-accuracy phishing website detection method based on machine learning,”
Journal of Information Security and Applications,
vol. 77,
art. 103553,
2023.
doi: 10.1016/j.jisa.2023.103553.
Available: https://www.sciencedirect.com/science/article/pii/S2214212623001370

Este paper probó varios modelos para resolver el mismo problema utilizando parámetros distintos: Los modelos que se tratan son  Logistic Regression, K-Nearest Neighbors, Naive Bayes, Random Forest, Support Vector Machine, y Extreme Gradient Boosting (XGBoost). 

De estos modelos he seleccionado únicamente 3 Logistic Regression, Random Forest y XGBoost. El primer modelo tuvo un desempeño moderado y lo quiero más que nada como un benchmark. Los otros dos modelos fueron los que mejor desempeño tuvieron alcanzando un F1 ligeramente arriva de 95 antes de ajustar los hiperparámetros.

El experimento fue realizado con un dataset público de 58,000 ejemplos de sitios legítimos y 30,647 de phishing, además de contar con 112 atributos. El dataset que empleo yo tiene una clase adicional, muchos menos ejemplos y solo 9 atributos que se vuelven 25 tras el encoding. Por estas características se espera que las métricas que se obtengan sean peores que las del paper, pero lo ideal es que se acerquen lo más posible (al menos entre 85 y 95 del F1 score).

El paper menciona al XGBoost como el mejor modelo para resolver el problema ya que alcanza hasta un 99% de accuracy, precision, recall y specificity. Además, su runtime es bastante rápido. 

Para los modelos que voy a hacer quiero usar de métrica solamente a F1 score (y a accuracy como soporte). El motivo es que el F1 score es menos suceptible a datasets desbalanceados y la clasificación intermedia de sitio sospechoso aparece mucho menos que las otras dos clases. Además, como hay 3 clasificaciones posibles en lugar de 2, prefiero utilizar una métrica que combina las métricas de precision y recall en lugar de calcularlas individualmente para cada clase.

Accuracy lo quiero para contrastarlo con el F1, si ambos son muy similares, es una buena señal de que el modelo funciona bien para clasificar las 3 posibles clasificaciones. Si al cotrario el accuracy y el f1 son muy diferentes, significa que probablemente el modelo no esté detectando bien una de las clases (probablemente por desbalance).

En el paper se utilizan 8 escenarios distintos que no son más que diferentes combinaciones de aplicar balanceo, eliminar constantes y PCA (Principal Component Analysis es cuando dos atributos son muy parecidos se combinan en una sola variable). El escenario 1 es donde no aplican ninguna de las 3 herramientas y el escenario 4 (el que obtuvo mejores resultados) es cuando usaron balanceo y eliminación de constantes pero no PCA. En el contexto de mi dataset eliminar constantes y PCA no tienen mucho sentido, pero balancear mejor el dataset sí puede servir para mejorar el desempeño del modelo.

La técnica de balanceo que emplean es oversampling con SMOTEENN, (la misma que empleamos en MATCHAI). Esta lo que hace es generar nuevas instancias artificiales de la clase desbalanceada con menos ejemplos (SMOTE) y (ENN [Edited Nearest Neighbors]) elimina observaciones ruidosas o ambiguas.
