# Conectando Grafana con Elasticsearch
El siguiente paso es acceder a la interfaz web de Grafana y añadir Elasticsearch como fuente de datos.

He accedido desde la máquina host con la URL
http://192.168.56.102:3000/connections/datasources/new (indicando la IP que corresponde al adaptar host-only de la máquina con Elasticsearch)

En la interfaz, desde **Home > Connections > Data sources > Add data source** he seleccionado Elasticsearch

> A continuación, he creado un Data Source para ver los registros de Filebeat. Como este Data Source apunta al índice .ds-filebeat-*, solo mostrará lo que recopile
Filebeat.

### Data Source de Filebeat para recopilar logs
![Nuevo data source en Grafana](/img/grafana-add-data-source.png)

En la configuración he modificado estos valores:
- **Nombre:** Seguridad (Filebeat)
- **URL del servidor que contiene Elasticsearch:** https://10.0.3.10:9200
- **Autenticación básica:** elastic/yvsSfQ4Xf*SfTz_XwjlF (Proporcionados durante la instalación de Elasticsearch)

  ![Método de autenticación para acceder al data source](/img/grafana-data-source-authentication.png)

- **Autocertificado firmado:** http_ca.crt

  ![Autocertificado](/img/filebeat-ca-certificate.png)

- **Nombre del índice:** .ds-filebeat-*

  ![Nombre del índice](/img/filebeat-index-name.png)

Después he guardado los cambios y aparece este mensaje:

![Cambios guardados](/img/data-source-healthy.png)

De esta manera se ha creado una fuente de datos para Filebeat:

![Data source de seguridad creado](/img/seguridad-data-source.png)

