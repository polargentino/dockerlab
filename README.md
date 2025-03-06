# dockerlab: # 🐧 **DockerLab - Hacking Ético en Máquinas de Bajos Recursos**  

## 📌 **¿Qué es DockerLab?**
DockerLab es un entorno diseñado para realizar **pruebas de hacking ético** y aprender ciberseguridad sin necesidad de virtualización.  
Aquí se despliegan **máquinas vulnerables** utilizando **Docker**, permitiendo practicar ataques reales en un sistema con **bajos recursos de hardware**.  

✅ **Objetivo:** Simular escenarios de pentesting y seguridad ofensiva, documentando cada paso desde el escaneo hasta la escalación de privilegios.  

---

## 🛠 **Metodología de Ataque**  
Cada máquina desplegada en **DockerLab** sigue un proceso estructurado de explotación:  

1️⃣ **Escaneo y reconocimiento:**  
   - Se identifican los servicios y puertos abiertos con `nmap`.  
   - Se enumeran directorios ocultos con `gobuster`.  

2️⃣ **Explotación inicial:**  
   - Se buscan vulnerabilidades en aplicaciones web (`sqlmap`, `dirb`, `nikto`).  
   - Se intentan ataques de fuerza bruta si es necesario (`hydra`).  

3️⃣ **Escalada de privilegios:**  
   - Se buscan binarios con SUID (`find / -perm -4000 -user root`).  
   - Se explotan permisos incorrectos (`sudo -l`).  

4️⃣ **Post-explotación y persistencia:**  
   - Se extraen credenciales almacenadas.  
   - Se establecen backdoors para acceso futuro.  

🔍 **Cada ataque está documentado en archivos:**  
- `README_1.md` → Ataque y explotación de la máquina "Injection".  
- `README_2.md` → Próximos estudios de otras máquinas.  

---

## 💻 **Entorno de Trabajo**  
Este laboratorio se ejecuta en una **laptop antigua sin virtualización**, demostrando que **no hay excusas para no aprender hacking ético**.  

🔹 **Hardware:**  
- **Equipo:** Hewlett-Packard Compaq Presario CQ40 (2010).  
- **CPU:** Intel Pentium T4200 (2 núcleos, 2.0GHz).  
- **RAM:** 4GB DDR2.  
- **Almacenamiento:** SSD de 240GB.  
- **Gráficos:** Intel Mobile 4 Series.  

🔹 **Sistema Operativo:**  
- **Kali Linux Rolling 2025** (basado en Debian Testing).  
- **Entorno de escritorio:** Xfce (ligero y optimizado para hardware antiguo).  

🔹 **Docker:**  
- Se usa para desplegar máquinas vulnerables sin necesidad de máquinas virtuales.  
- Ejemplo de uso:  
  ```bash
  docker run --rm -it -p 80:80 vulnerables/web-dvwa

