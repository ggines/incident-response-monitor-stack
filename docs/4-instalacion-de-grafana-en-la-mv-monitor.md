# Instalación de Grafana en la MV Monitor
Para instalar Grafana en la MV Monitor he seguido estos pasos:

He instalado los paquetes de requisitos previos con el comando ```sudo apt-get install -y apt-transport-https software-properties-common wget```

![Instalación de paquetes previos](/img/apt-get-install-previous-grafana.png)

He importado la clave GPG con los comandos ```sudo mkdir -p /etc/apt/keyrings/ y wget -q -O - https://apt.grafana.com/gpg.key | gpg --dearmor | sudo tee /etc/apt/keyrings/grafana.gpg > /dev/null```

![Importación de la clave GPG](/img/key-grafana.png)

He agregado el repositorio para versiones estables con el comando ```echo "deb [signed-by=/etc/apt/keyrings/grafana.gpg] https://apt.grafana.com stable main" | sudo tee -a /etc/apt/sources.list.d/grafana.list```

![Repositorio para versiones estables](/img/stable-versions-repository.png)

He actualizado la lista de paquetes disponibles con el comando ```sudo apt-get update```

![apt-get-update](/img/apt-get-update.png)

He instalado la última versión de Grafana OSS con el comando ```sudo apt-get install grafana```

![Instalación de Grafana OSS](/img/apt-get-install-grafana.png)

Por último, he iniciado el servicio y lo he activado para que se inicie al arrancar el sistema:
- ```sudo systemctl daemon-reload```
- ```sudo systemctl start grafana-server```
- ```sudo systemctl enable grafana-server```

![Iniciando el servicio de Grafana](/img/starting-grafana-service.png)

He comprobado que el servicio esté en ejecución con el comando ```sudo systemctl status grafana-server```

![Estado del servicio de Grafana](/img/status-grafana-server.png)

Después de instalar Grafana, podemos acceder a su interfaz web **indicando la IP del servidor y el puerto 3000**.

En este caso puedo acceder desde la máquina host anfitriona, indicando la IP ```192.168.56.102``` que corresponde al adaptador host-only:

![Interfaz web de Grafana](/img/grafana-login.png)

Las credenciales por defecto son ***admin / admin***

> Al iniciar sesión por primera vez, tenemos la opción de cambiar la contraseña. En este caso, al ser un entorno de pruebas, no es estrictamente necesario:

![Saltar cambio de contraseña](/img/skip-password-change.png)

Después de iniciar sesión, ya tenemos acceso al panel:

![Inicio exitoso a la interfaz web de Grafana](/img/grafana-login-successfully.png)

[Siguiente: Instalación de Metricbeat en la MV víctima](5-instalacion-de-metricbeat-en-la-mv-victima.md)
