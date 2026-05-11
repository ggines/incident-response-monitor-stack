# Instalación de Metricbeat en la MV víctima
Los siguientes pasos consisten en instalar Metricbeat y Filebeat en la MV víctima monitorizada para recopilar y enviar logs y métricas al servidor.

Filebeat se especializa en **logs** (archivos de texto, eventos) y Metricbeat en **métricas** (CPU, memoria, red, uso de servicios).

Para instalar Metricbeat en la MV víctima he seguido estos pasos:

He descargado e instalado la clave de firma pública con el comando ```wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo apt-key add -```

![Instalación de la clave de firma pública](/img/victim-gpg-key-elasticsearch.png)

He instalado el paquete apt-transport-http con el comando ```sudo apt-get install apt-transport-https```

![apt-transport-http](/img/victim-apt-transport-http.png)

He guardado la definición del repositorio en ```/etc/apt/sources.list.d/elastic-9.x.list``` con el comando ```echo "deb https://artifacts.elastic.co/packages/9.x/apt stable main" |
sudo tee -a /etc/apt/sources.list.d/elastic-9.x.list```

![Guardado de la definición del repositorio](/img/victim-elasticsearch-repository.png)

He ejecutado un ```apt-get update``` y he instalado Metricbeat con el comando ```sudo apt-get install metricbeat```

![Instalación del paquete de Metricbeat](/img/install-metricbeat.png)

He indicado que Metricbeat se inicie automáticamente al arrancar el sistema con el comando ```sudo systemctl enable metricbeat```

![systemctl enable metricbeat](/img/enable-metricbeat.png)

He editado el archivo de configuración de Metricbeat ```/etc/metricbeat/metricbeat.yml``` realizando las siguientes modificaciones:
- **output.elasticsearch:**
  - **hosts:** [https://10.0.3.10:9200] --> Indica la dirección del host donde está Elasticsearch
  - **protocol:** "https" --> Para utilizar el protocolo HTTPS
  - **username:** "elastic" --> Indica el usuario *elastic* de Elasticsearch
  - **password:** "yvsSfQ4Xf*SfTz_XwjlF" --> Indica la contraseña del usuario *elastic* de Elasticsearch, proporcionada durante la instalación de Elasticsearch
  - **ssl:**
    - **certificate_authorities:** ["/etc/metricbeat/certs/http_ca.crt"] --> Indica la ruta del certificado de Elasticsearch

  ![metricbeat.yml](/img/metricbeat-yml.png)

> Importante: Para que Metricbeat pueda enviar los datos hacia el servidor de Elasticsearch de manera segura (utilizando el protocolo HTTPS), se puede indicar la
ruta del certificado de Elasticsearch. En este caso, he copiado el certificado de Elasticsearch del servidor ```/etc/elasticsearch/certs/http_ca.crt``` al directorio
```/etc/metricbeat/certs/``` de la MV víctima.
>
> Para hacer esto, he seguido estos pasos:
>
> 1- A partir de una copia del certificado, le he cambiado los propietarios del archivo para que sea ‘monitor’ y le he asignado permisos de lectura y escritura para el
propietario con los comandos ```chown monitor:monitor http_ca.crt``` y ```chmod 644 http_ca.crt```
> 
> ![Cambio de permisos de una copia del certificado](/img/chown-chmod-http-ca-crt.png)
> 
> 2- He iniciado un servidor HTTP con Python para servir el archivo desde la MV Monitor, con el comando ```python3 -m http.server 8000``` desde el directorio
```/home/monitor/``` del servidor. Y he descargado el archivo con el comando ```sudo wget http://10.0.3.10:8000/http_ca.crt``` desde el directorio ```/etc/metricbeat/certs``` de la MV víctima:
> 
> ![Obtener el certificado desde la MV Monitor](/img/python-server-wget.png)

Una vez guardado el certificado en el directorio ```/etc/metricbeat/certs```, he comprobado que la sintaxis del archivo de configuración de Metricbeat esté libre de
errores. Con el comando ```sudo /usr/share/metricbeat/bin/metricbeat test config -c /etc/metricbeat/metricbeat.yml --path.data /var/lib/metricbeat --path.home /usr/share/metricbeat```

![Metricbeat test config](/img/metricbeat-test-config.png)

Y he comprobado que la conexión se realiza correctamente con el comando ```sudo /usr/share/metricbeat/bin/metricbeat test output -c /etc/metricbeat/metricbeat.yml --path.data /var/lib/metricbeat --path.home
/usr/share/metricbeat```

![Comprobación de la conexión con Elasticsearch](/img/metricbeat-connection.png)

Después he ejecutado el comando ```sudo /usr/share/metricbeat/bin/metricbeat setup -c /etc/metricbeat/metricbeat.yml --path.data /var/lib/metricbeat --path.home /usr/share/metricbeat``` 
para configurar los flujos de datos y las vistas de Metricbeat en Elasticsearch.

![Configuración de los flujos de datos y vistas de Metricbeat](/img/metricbeat-setup.png)

> En este caso aparece un mensaje de error en relación con Kibana, pero es normal porque en esta práctica no estamos usando Kibana, sino Grafana.

He ejecutado el siguiente comando para cargar los "assets" de Metricbeat:
```
sudo metricbeat setup --index-management \
-E output.elasticsearch.hosts=["https://10.0.3.10:9200"] \
-E output.elasticsearch.username="elastic" \
-E output.elasticsearch.password="yvsSfQ4Xf*SfTz_XwjlF" \
-E output.elasticsearch.ssl.verification_mode="none"
```

![Cargar los assets de Metricbeat](/img/metricbeat-setup-assets.png)

Por último, he iniciado el servicio de Metricbeat con el comando ```sudo systemctl start metricbeat```

![systemctl start metricbeat](/img/start-metricbeat.png)

[Siguiente: Instalación de Filebeat en la MV víctima](6-instalacion-de-filebeat-en-la-mv-victima.md)
