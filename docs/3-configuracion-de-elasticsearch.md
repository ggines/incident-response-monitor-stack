# Configuración de Elasticsearch
El archivo de configuración es ```/etc/elasticsearch/elasticsearch.yml```

He configurado las siguientes directivas:
- **network.host:** Define en qué interfaces de red y direcciones IP va a escuchar Elasticsearch. En este caso he indicado la IP del propio servidor **(10.0.3.10)**, a la
que la víctima tiene acceso.

  ![network.host](/img/network-host.png)

- **cluster.name:** Define un nombre único para identificar el clúster.

  ![cluster.name](/img/cluster-name.png)

- **xpack.security.enabled:** Permite activar las funcionalidades de seguridad para proteger el clúster. En este caso están activadas con el valor ```true```

  ![xpack.security.enabled](/img/xpack-security-enabled.png)

- **http.port:** Define el puerto en el que escuchará Elasticsearch para el tráfico HTTP. Por defecto es el puerto ```9200```

  ![http.port](/img/http-port.png)

Después de guardar los cambios en el archivo de configuración, he activado el servicio de Elasticsearch para que se ejecute ahora y al arrancar el sistema:

![Reinicio del servicio de Elasticsearch](/img/service-restart-elasticsearch.png)

Para comprobar su funcionamiento, he ejecutado el comando ```curl -X GET -u elastic:<password> https://{ip address of server}:9200 --cacert /etc/elasticsearch/certs/http_ca.crt```

En *password* y *ip address of server* he indicado:
- La contraseña generada para el superusuario de elastic proporcionada al instalar Elasticsearch
- La dirección IP indicada en el parámetro ```network.host``` del archivo ```elasticsearch.yml```. Esta IP es la del servidor, y es accesible desde la MV víctima, que están en la misma Red NAT

Si el comando devuelve lo siguiente, significa que está funcionando correctamente:

![curl -X GET](/img/get-elasticsearch.png)

> En este caso el comando solo se completa con éxito con el usuario root, ya que es quién tiene permisos de lectura del certificado.

He probado a realizar la conexión anterior desde la MV víctima:

![curl -X GET](/img/get-elasticsearch-from-victim.png)

> Para realizar la conexión desde la MV víctima, copié el certificado ```http_ca.crt``` desde el servidor y lo indiqué en el comando.

[Siguiente: Instalación de Grafana en la MV Monitor](4-instalacion-de-grafana-en-la-mv-monitor.md)
