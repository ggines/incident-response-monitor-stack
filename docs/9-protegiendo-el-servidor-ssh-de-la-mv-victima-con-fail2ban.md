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

Primero, he hecho una copia de este archivo por seguridad (jail.old):

![jail.old](/img/jail-old.png)

En este caso se trata de proteger el servicio de SSH. Dentro del archivo ```/etc/fail2ban/jail.conf```, he ido a la sección del servicio *sshd* y he añadido la directiva
```enabled``` con el valor ```true``` para activar el monitoreo del servicio.

También he añadido estas otras directivas:
- ```maxretry = 5``` --> Banea a los 5 intentos fallidos
- ```bantime = 1h``` --> Tiempo de baneo, 1 hora
- ```findtime = 10m``` --> Ventana de tiempo para contar los reintentos, 10 minutos

![sshd jail](/img/sshd-jail.png)
