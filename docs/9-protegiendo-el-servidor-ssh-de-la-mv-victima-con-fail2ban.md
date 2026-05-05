# Protegiendo el servidor SSH de la MV víctima con Fail2ban
En este punto, se trata de usar la herramienta **Fail2ban** para proteger la MV víctima en caso de que detecte cierta cantidad de intentos de inicio de sesión fallidos
al servidor SSH.

Fail2ban es una herramienta de seguridad capaz de proteger los servidores frente intrusos y accesos no autorizados. Para ello, monitoriza los archivos de registro (logs)
de servicios y bloquea las IPs sospechosas en base a las reglas que le indiquemos (por ejemplo, cantidad de intentos de acceso fallidos permitidos o duración del bloqueo).

Para instalar Fail2ban en la máquina víctima, he ejecutado el comando ```sudo apt update && sudo apt install fail2ban```

![apt install fail2ban](/img/apt-install-fail2ban.png)

![systemctl status fail2ban](/img/fail2ban-status.png)

El archivo ```/etc/fail2ban/jail.conf``` es clave. Este archivo define las reglas para proteger el sistema de intentos de intrusiones o ataques.

En este archivo podemos activar “jaulas” para cada servicio. Dentro de estas se definen medidas de bloqueo cuando se detecta un comportamiento sospechoso.

Primero, he hecho una copia de este archivo por seguridad (```jail.old```):

![jail.old](/img/jail-old.png)

En este caso se trata de proteger el servicio de SSH. Dentro del archivo ```/etc/fail2ban/jail.conf```, he ido a la sección del servicio *sshd* y he añadido la directiva
```enabled``` con el valor ```true``` para activar el monitoreo del servicio.

También he añadido estas otras directivas:
- ```maxretry = 5``` --> Banea a los 5 intentos fallidos
- ```bantime = 1h``` --> Tiempo de baneo, 1 hora
- ```findtime = 10m``` --> Ventana de tiempo para contar los reintentos, 10 minutos

![sshd jail](/img/sshd-jail.png)

Después de guardar el archivo, he reiniciado el servicio de Fail2ban con ```sudo systemctl restart fail2ban```

![systemctl restart fail2ban](/img/restart-fail2ban.png)

Si ejecuto el comando ```sudo fail2ban-client status```, aparece la jaula que monitorea el servicio de SSH activada:

![fail2ban client status](/img/fail2ban-client-status.png)

Si se especifica el servicio en el comando, aparece más información. Incluyendo el total y la lista de IPs baneadas:
![fail2ban client status sshd](/img/fail2ban-client-status-sshd.png)

En este punto, desde la Kali, he probado a iniciar sesión mediante SSH con una contraseña incorrecta.

**Para verificar que funciona, en el sexto intento la IP del Kali debería ser bloqueada:**

![IP baneada tras múltiples intentos de inicio de sesión fallidos](/img/sshd-banned-ip.png)

En el sexto intento de inicio de sesión fallido, **la conexión ya no responde**, y en el comando de Fail2ban aparece que la IP fue bloqueada,
además del total de intentos fallidos.

Como ahora la IP está baneada, aparece **Connection refused:**

![Connection refused](/img/connection-refused.png)

En los logs de Fail2ban ```/var/log/fail2ban.log```, podemos ver que detectó la IP y la bloqueó:

![IP baneada en el log de fail2ban](/img/fail2ban-log.png)

![IP baneada en el log de fail2ban](/img/fail2ban-log-2.png)

En el caso de que quisiéramos desbloquear la IP al instante, podríamos usar el comando ```fail2ban-client set sshd unbanip 10.0.3.3```

O para bloquearla directamente, usar el comando ```fail2ban-client set sshd banip 10.0.3.3```

De esta manera, podemos proteger el servidor de intrusiones a servicios como ssh, vsftpd, apache, etc...

> Es importante saber que para que Fail2ban funcione, el servicio que queremos proteger debe estar configurado para escribir logs.
>
> Si el servicio no registra los fallos, Fail2ban no tendrá nada que leer.

### Dashboard de Fail2ban en Grafana
Una vez instalado y configurado Fail2ban, he creado un pipeline ingest en Elasticsearch para extraer las IPs bloqueadas.

En la MV Monitor he ejecutado el siguiente comando:

```
sudo curl -u elastic:yvsSfQ4Xf*SfTz_XwjlF \
-X PUT "https://10.0.3.10:9200/_ingest/pipeline/fail2ban-pipeline" \
-H 'Content-Type: application/json' \
-d '{
    "description": "Extraer IP de los logs de fail2ban",
    "processors": [
      {
        "grok": {
          "field": "message",
          "patterns": [
            ".*Ban %{IP:fail2ban.ip}"
          ]
        }
      }
    ]
}' --cacert /etc/elasticsearch/certs/http_ca.crt
```

![Creación de un pipeline ingest para fail2ban](/img/fail2ban-pipeline-ingest.png)

He comprobado que se ha creado correctamente con el comando
```sudo curl -u elastic:yvsSfQ4Xf*SfTz_XwjlF https://10.0.3.10:9200/_ingest/pipeline/fail2ban-pipeline?pretty --cacert /etc/elasticsearch/certs/http_ca.crt```

![Pipeline creado](/img/fail2ban-pipeline-ingest-created.png)

Este pipeline permitirá que Elasticsearch pueda “extraer” la IP de los logs de Fail2ban.

He modificado el archivo de configuración de Filebeat ```/etc/filebeat/filebeat.yml``` para añadir un nuevo input que monitorice los logs de Fail2ban:

![Nuevo input para monitorear los logs de fail2ban](/img/filebeat-yml-input.png)

En este nuevo input, he indicado el pipeline creado anteriormente para procesar y extraer la dirección IP de los eventos de tipo *Ban*,
así como un índice específico para almacenar estos registros de forma independiente.

De esta forma, Filebeat recoge los eventos generados por Fail2ban, los procesa mediante el pipeline definido y
los envía a Elasticsearch dentro del índice ```fail2ban-*.```

Después de aplicar los cambios, he reiniciado el servicio de Filebeat:

![systemctl restart filebeat](/img/restart-filebeat.png)

Una vez reiniciado el servicio, he comprobado que el nuevo índice para Fail2ban exista, usando el comando
```sudo curl -u elastic:yvsSfQ4Xf*SfTz_XwjlF https://10.0.3.10:9200/_cat/indices?v --cacert /etc/elasticsearch/certs/http_ca.crt``` desde la MV Monitor:

![Índice de fail2ban](/img/fail2ban-index.png)

![Índice de fail2ban](/img/fail2ban-index-2.png)

Ahora aparece el nuevo índice de Fail2ban.

Después de estos pasos, se trata de crear un panel en Grafana con los logs de Fail2ban.

Por ejemplo, crear un panel con la cantidad de IPs bloqueadas.

Antes de eso, he añadido una nueva fuente de datos en Grafana indicando el índice de Fail2ban:

![Nueva fuente de datos en Grafana para fail2ban](/img/fail2ban-datasource.png)

![Fuente de datos para fail2ban creada](/img/fail2ban-datasource-created.png)

Me aseguré de que Filebeat está leyendo los logs de Fail2ban, ejecutando el comando
```sudo curl -u elastic:yvsSfQ4Xf*SfTz_XwjlF --cacert /etc/elasticsearch/certs/http_ca.crt "https://10.0.3.10:9200/fail2ban-*/_search?pretty&size=5&q=fail2ban.ip:*"``` en la MV Monitor:

![Comprobación de que Filebeat lea los logs de fail2ban](/img/filebeat-reads-fail2ban-logs.png)

Filebeat está detectando los logs correctamente. Tras bloquear la IP del Kali (10.0.3.3), esta aparece en el campo ```fail2ban.ip```:

![Comprobación de que Filebeat lea los logs de fail2ban](/img/filebeat-reads-fail2ban-logs-2.png)

Una vez creada la nueva fuente de datos, se trata de crear un dashboard en Grafana usando la fuente de datos de Fail2ban creada anteriormente con un panel que muestre
algo en relación con los logs de Fail2ban.

Por ejemplo, he creado un panel que indica la cantidad de IPs que están bloqueadas por parte de Fail2ban.
Para crear este panel, he seleccionado la fuente de datos Fail2ban. La query es ```fail2ban.ip: *```

![Query](/img/fail2ban-query.png)

Esta query filtra solo los documentos que tienen una IP extraída.

En Metric, he seleccionado **"Unique Count"** para que solo muestre la cantidad de IPs únicas, no repetidas.
Y he seleccionado el campo ```fail2ban.ip``` que es el que contiene la IP.

En Transformations, he añadido la transformación **"Series to rows"** con el cálculo **"Max"** para que Grafana tome todos los buckets y muestre el valor real de Unique Count.

Dependiendo del intervalo seleccionado, se mostrará la cantidad de IPs que han sido bloqueadas en ese intervalo de tiempo.

Por ejemplo, he seleccionado el intervalo de los últimos 30 minutos:

![Panel de IPs únicas bloqueadas con intervalo de 30 minutos](/img/30-minutes-interval.png)
> Aparece 1, ya que en los últimos 30 minutos Fail2ban bloqueó la IP 10.0.3.3 del Kali.

Si ahora selecciono el intervalo de los últimos 5 minutos, no aparece ninguna, ya que no se bloqueó ninguna IP en este intervalo:

![Panel de IPs únicas bloqueadas con intervalo de 5 minutos](/img/5-minutes-interval.png)

Por último, he creado otro panel para mostrar qué IPs fueron bloqueadas y la cantidad de veces:

![Query](/img/fail2ban-query-2.png)

![Query](/img/fail2ban-query-3.png)

Al igual que el anterior panel, la fuente de datos es la de Fail2ban y la query es ```fail2ban.ip: *``` para filtrar solo los documentos que tienen una IP extraída.

En Group By, he seleccionado **"Terms"** y el campo ```fail2ban.ip```, ordenado por Count.

He seleccionado un intervalo en el que se bloquearon IPs (por ejemplo, en la última hora) y aparecen las IPs con la cantidad de veces que fueron bloqueadas por Fail2ban:

![Panel con las IPs baneadas por fail2ban y la cantidad de veces](/img/fail2ban-banned-ips.png)

![Dashboard de fail2ban](/img/fail2ban-dashboard.png)

En este caso, en la última hora se bloqueó 2 veces la IP de la MV atacante Kali ```10.0.3.3```.

> La IP 5.6.7.8 no es real, únicamente la agregué anteriormente al log de fail2ban para hacer pruebas

Historial de logs de Fail2ban:

![Historial de logs de fail2ban](/img/fail2ban-log-history.png)

> Es importante saber que Filebeat no lee los logs antiguos que ya estaban antes de configurar el input de Fail2ban,
> por lo que solo puede leer los logs que se generaron después de configurar el input.
>
> Es decir, si el log de Fail2ban contiene registros de antes de que añadiese el nuevo input a Filebeat, Filebeat no los leerá.
> Solo tendrá en cuenta los nuevos que se generen.
>
> Aunque esto se puede cambiar configurando el parámetro ```ignore_older``` en el archivo ```/etc/filebeat/filebeat.yml```
>
> Este parámetro fuerza a que Filebeat lea todos los logs, incluyendo los más antiguos.

Finalmente, tenemos 3 dashboards:
- **Dashboard de Fail2ban:** Para mostrar datos en relación a los logs de Fail2ban, como IPs bloqueadas.
- **Rendimiento:** Para mostrar métricas del sistema como el tráfico de red entrante y saliente o el consumo de memoria por procesos (útil para detectar ataques).
- **Seguridad:** Para mostrar IPs potencialmente atacantes, intentos de acceso y usuarios que están intentando vulnerar.

![Todos los dashboards](/img/all-dashboards.png)

[Siguiente: Conclusiones y webgrafía](10-conclusiones-y-webgrafía.md)

