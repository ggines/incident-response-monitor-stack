# Instalación de Elasticsearch en la MV Monitor
Para instalar Elasticsearch en la MV Monitor he seguido estos pasos:

He descargado e instalado la clave de firma pública con el comando ```wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo gpg --dearmor -o /usr/share/keyrings/elasticsearch-keyring.gpg```

![GPG-KEY Elasticsearch](/img/key-elasticsearch.png)

He instalado el paquete apt-transport-https con el comando ```sudo apt-get install apt-transport-https```

![apt-transport-https](/img/apt-transport-https.png)

He guardado la definición del repositorio en etc/apt/sources.list.d/elastic-9.x.list con el comando ```echo "deb [signed-by=/usr/share/keyrings/elasticsearch-keyring.gpg] https://artifacts.elastic.co/packages/9.x/apt stable main" | sudo tee /etc/apt/sources.list.d/elastic-9.x.list```

![Definición del repositorio](/img/elasticsearch-keyring.png)

He instalado el paquete de Elasticsearch con el comando ```sudo apt-get update && sudo apt-get install elasticsearch```

![Instalación del paquete de Elasticsearch](/img/apt-get-install-elasticsearch.png)

Al instalar Elasticsearch, este no se inicia por defecto al arrancar el sistema. Para iniciarlo automáticamente, hay que ejecutar estos 2 comandos:
- ```sudo systemctl daemon-reload```
- ```sudo systemctl enable elasticsearch.service```

Para iniciarlo ahora, hay que ejecutar el comando ```sudo systemctl start elasticsearch```

[Siguiente: Configuración de Elasticsearch](3-configuracion-de-elasticsearch.md)
