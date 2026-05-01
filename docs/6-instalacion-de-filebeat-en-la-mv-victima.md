# Instalación de Filebeat en la MV víctima
Para instalar Filebeat en la MV víctima he ejecutado el comando ```sudo apt-get install filebeat```

![apt-get-install-filebeat](/img/apt-get-install-filebeat.png)

En el archivo de configuración de Filebeat ```/etc/filebeat/filebeat.yml``` he realizado modificaciones en el bloque ```output.elasticsearch```:
- **output.elasticsearch:**
  - **hosts:** [https://10.0.3.10:9200] --> Indica la dirección del host donde está Elasticsearch
  - **protocol:** "https" --> Para utilizar el protocolo HTTPS
  - **username:** "elastic" --> Indica el usuario de elasticsearch elastic
  - **password:** "yvsSfQ4Xf*SfTz_XwjlF" --> Indica la contraseña del usuario elastic de Elasticsearch, proporcionada durante la instalación de Elasticsearch
  - **ssl:**
    - **certificate_authorities:** ["/etc/filebeat/certs/http_ca.crt"] --> Indica la ruta del certificado de Elasticsearch

  ![filebeat.yml](/img/filebeat-yml.png)

También he copiado el certificado al directorio ```/etc/filebeat/certs/``` (creando antes la carpeta certs):

![Certificado http_ca.crt en el directorio certs de Filebeat](/img/filebeat-cert.png)

He habilitado el módulo **system** de Filebeat con el comando ```sudo filebeat modules enable system```. Este módulo se utiliza para recolectar, procesar y visualizar
automáticamente los registros (logs) estándar de sistemas operativos basados en Unix/Linux.

Este módulo simplifica la gestión de logs al incluir configuraciones predefinidas para los archivos más comunes del sistema, eliminando la necesidad de configurarlos
manualmente.

![filebeat modules enable system](/img/filebeat-modules-enable-system.png)

He editado el archivo de configuración del módulo **system** (```/etc/filebeat/modules.d/system.yml```) para habilitar los filesets **syslog** y **auth**:

![system.yml](/img/system-yml.png)

Indicando la ruta ```/var/log/auth.log```, nos aseguramos de que Filebeat se centre en el archivo donde se registran los intentos de login.

> Es importante habilitar estos filesets, ya que si están en false y el módulo no tiene indicado ningún archivo de log para leer, Filebeat se detendrá por seguridad.

He ejecutado el comando ```sudo filebeat setup``` para preparar la infraestructura externa (Elasticsearch).
Su función es cargar las plantillas de datos (mappings), las políticas de rotación de índices y los tableros visuales, asegurando que los logs se almacenen de
forma estructurada y sean legibles inmediatamente.

![filebeat setup](/img/filebeat-setup.png)

Al igual que antes, da un error de Kibana, ya que en esta práctica usamos Grafana en lugar de Kibana.

He ejecutado el siguiente comando para cargar los **ingest pipelines**:
```
sudo filebeat setup --pipelines --modules system \
-M "system.auth.enabled=true" \
-E output.elasticsearch.hosts=["https://10.0.3.10:9200"] \
-E output.elasticsearch.username="elastic" \
-E output.elasticsearch.password="yvsSfQ4Xf*SfTz_XwjlF" \
-E output.elasticsearch.ssl.verification_mode="none"
```

![Cargar los pipelines](/img/filebeat-setup-pipelines.png)

> Los pipelines son reglas que transforman el texto plano del log en campos estructurados. Cargando los pipelines nos aseguramos de que Elasticsearch pueda
separar los campos de los logs y que no aparezcan todos como un único campo.

He comprobado la conexión con el comando ```sudo filebeat test output```

![filebeat test output](/img/filebeat-test-output.png)

Y he comprobado que la configuración sea correcta con el comando ```sudo filebeat test config```

![filebeat test config](/img/filebeat-test-config.png)

Por último, he ejecutado los siguientes comandos para iniciar el servicio de Filebeat y activarlo automáticamente al arrancar el sistema:
- ```sudo systemctl enable filebeat```
- ```sudo systemctl start filebeat```

![Iniciar y activar el servicio de Filebeat](/img/enable-start-filebeat.png)

Desde la MV Monitor, he realizado una consulta a la API de Elasticsearch **para listar de forma legible todos los índices almacenados en el servidor.**
En este caso aparecen los 2 índices (Metricbeat y Filebeat):

```sudo curl -u elastic:yvsSfQ4Xf*SfTz_XwjlF https://10.0.3.10:9200/_cat/indices?v --cacert /etc/elasticsearch/certs/http_ca.crt```

![Consulta a la API de Elasticsearch para comprobar los índices](/img/elasticsearch-api-query.png)

![Consulta a la API de Elasticsearch para comprobar los índices](/img/elasticsearch-api-query-2.png)

Esto confirma que Metricbeat y Filebeat están funcionando y que los datos llegan a Elasticsearch.

![Siguiente: Conectando Grafana con Elasticsearch](6-conectando-grafana-con-elasticsearch.md)
