#Si este comentario lo estás viendo antes del 31 de mayo a las 11:59pm, todavía no está completo el trabajo, por favor aún no lo revises. Subí el enlace para evitar problemas con Canvas, pero me falta separar en limpieza entrenamiento y pruebas; y explicar el paper que utilicé al igual que los modelos y sus hiperparámetros.


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
