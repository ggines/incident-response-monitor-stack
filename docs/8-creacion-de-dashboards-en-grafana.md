# Creación de dashboards en Grafana
Para comprobar que los campos de los logs están correctamente estructurados en Grafana, he realizado un intento de conexión al servidor SSH con un usuario erróneo
desde la máquina Kali:

![Intento de conexión SSH](/img/ssh-kali.png)

A continuación, desde la interfaz de **Grafana > Explore** , he seleccionado la fuente de datos de Filebeat y he ido al apartado de **Logs:**

![Panel de Filebeat](/img/filebeat-panel-logs.png)

Aquí aparecen los campos estructurados, como por ejemplo los campos **source.ip** y **user.name**, en este caso con la IP del Kali y el usuario erróneo que indiqué para
intentar conectarme mediante SSH:

![Campos source.ip y user.name](/img/filebeat-sourceip-username-fields.png)

![Campos estructurados de Filebeat](/img/filebeat-fields.png)

En este punto, después de confirmar que los campos están separados correctamente, he creado un dashboard para cada fuente de datos (Filebeat y Metricbeat) con varios
paneles para mostrar diferentes registros en cada uno.

### Dashboard de seguridad (Filebeat)
Dashboard para mostrar registros relacionados con la seguridad, como intrusiones, cuentas vulneradas, IPs atacantes, etc...

- **Panel de usuarios atacados:** Muestra qué cuentas están intentando vulnerar.
  - **Query:** ```event.outcome: "failure"``` --> Si el log contiene la palabra failure, significa que intentaron acceder a esa cuenta, pero no se realizó con éxito.
  - **Visualización:** Tabla
  - **Técnica de MITRE ATT&CK:** T1110 - Brute Force --> En un escenario real, el atacante podría usar técnicas de fuerza bruta para acceder a cuentas sin saber la contraseña.

    ![Query](/img/filebeat-query.png)

    ![Usuarios atacados](/img/usuarios-atacados.png)

    En este caso, se han intentado realizar conexiones usando los usuarios **victim, root** y **admin.**

- **Panel de IPs atacantes:** Muestra el origen de la amenaza a partir de las IPs que originaron el ataque.
  - **Visualización:** Gráfico circular (Pie chart)
  - **Valor:** Identifica desde qué máquina de la red proviene el escaneo o el ataque.
 
    ![Query](/img/filebeat-query-2.png)

    ![IP atacante 10.0.3.3](/img/ip-atacante-2.png)
 
    Aparece la IP de la máquina Kali desde donde intenté conectarme por SSH con un usuario incorrecto (10.0.3.3).

    ![IP atacante 10.0.3.10](/img/ip-atacante-1.png)

    En este caso, también aparece la IP de la MV Monitor porque anteriormente me conecté por SSH para interactuar más cómodamente con la terminal (10.0.3.10).

- **Panel de accesos exitosos VS fallidos:** Muestra la cantidad de accesos exitosos y fallidos.
  - **Visualización:** Gráfico circular (Pie chart)
  - **Valor:** Permite ver la cantidad de intentos de intrusión que tuvieron éxito y los que no.

    ![Query](/img/filebeat-query-3.png)

    ![Acceso no exitoso](/img/failure-access.png)

    ![Acceso exitoso](/img/success-access.png)

    En este caso 4 fueron fallidas (failure) y 1 exitosa (success).

Así queda este dashboard a vista general:

![Dashboard de Filebeat](/img/filebeat-dashboard.png)

### Dashboard para métricas de rendimiento (Metricbeat)
Dashboard para mostrar métricas del rendimiento del sistema, como uso de la CPU, disco, memoria, red, etc...

- **Panel de consumo de memoria por proceso:** Muestra el porcentaje de memoria que están usando los procesos.
  - **Visualización:** Gráfica circular (Pie chart)
  - **Query:** ```NOT process.name: "metricbeat" AND NOT process.name: "filebeat"``` --> Para excluir el proceso de Metricbeat y Filebeat, que normalmente consumen bastante memoria.
  - **Valor:** Permite identificar si el proceso **sshd** está consumiendo más recursos de lo normal debido a las conexiones concurrentes del atacante.

    ![Query](/img/metricbeat-query.png)

    ![Proceso sshd](/img/proceso-sshd.png)

    En este caso el proceso sshd estaba consumiendo un 11% de la memoria durante un ataque de fuerza bruta desde la Kali:

    ![Ataque de fuerza bruta desde Kali](/img/kali-bruteforce-attack.png)

- **Panel de tráfico entrante y saliente:** Muestra la cantidad de tráfico de red entrante y saliente, en KB/s o MB/s.
  - **Visualización:** Time series
  - **Valor:** Útil para detectar escaneos de red o exfiltraciones de datos. Picos de tráfico entrante puede ser señal de que un atacante está escaneando puertos
    o alojando malware. Y picos de tráfico saliente puede ser señal de que un atacante nos está robando información.
  - **Técnicas de MITRE ATT&CK:** Las técnicas de MITRE ATT&CK que podrían usar los atacantes y generarían tráfico de red, serían:
    - **T1105 – Ingress Tool Transfer:** Cuando los atacantes transfieren herramientas o malware de un sistema externo al sistema víctima.
    - **T1595.002 - Active Scanning: Vulnerability Scanning:** Cuando los atacantes envían paquetes a los puertos de la víctima para determinar qué puertos
      están abiertos, cerrados o filtrados por un firewall.
    - **T1046 – Network Service Discovery:** Cuando los atacantes realizan un escaneo de puertos para descubrir otros sistemas y servicios abiertos dentro de la red.
    - **T1020 – Automated Exfiltration:** Cuando los atacantes exfiltran datos mediante el uso de procesamiento automatizado.
   
    ![Query](/img/metricbeat-query-2.png)

    ![Query](/img/metricbeat-query-3.png)

    ![Standard options](/img/standard-options.png)

    En este caso he realizado un escaneo de puertos con nmap desde la máquina Kali para ver si subía el tráfico entrante:

    ![Escaneo de puertos desde Kali](/img/kali-nmap.png)

    ![Panel de tráfico de red](/img/network-traffic.png)

    Y en efecto, se puede ver que el tráfico entrante ha subido mientras se realizaba el escaneo de puertos.

Así queda este dashboard a vista general:

![Dashboard de Metricbeat](/img/metricbeat-dashboard.png)

[Siguiente: Protegiendo el servidor SSH de la MV víctima con Fail2ban](9-protegiendo-el-servidor-ssh-de-la-mv-victima-con-fail2ban.md)
