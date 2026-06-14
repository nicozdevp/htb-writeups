# Meow - Hack The Box (Starting Point)

**Sistema Operativo:** Linux 🐧  
**Dificultad:** Very Easy   
**Plataforma:** Hack The Box (Tier 0)  

## Contexto
Meow es la máquina introductoria del *Starting Point* de Hack The Box. El objetivo principal de este laboratorio es familiarizarse con el proceso de conexión a la VPN de la plataforma, realizar un escaneo básico de puertos y explotar un servicio mal configurado mediante la conexión a un protocolo de administración remota heredado (Telnet) utilizando credenciales por defecto nulas.

---

## Reconocimiento

El primer paso de nuestra metodología consiste en verificar el enrutamiento hacia la máquina objetivo mediante una traza ICMP (Ping). 

```bash
ping 10.129.86.56
```

Al realizar el ping el TTL de respuesta cercano a 63 o 64 suele indicar que nos estamos enfrentando a un sistema operativo Linux, lo que nos ayuda a orientar nuestros próximos pasos.

Escaneo de Puertos
Con la conexión verificada de forma exitosa, procedemos a realizar un escaneo activo utilizando Nmap para identificar los puertos abiertos y los servicios expuestos en el servidor.

```bash
nmap -p- --open -sV -sC --min-rate 5000 10.129.86.56 -oN escaneo_inicial.txt
```

Desglose de los parámetros utilizados:

* -p-: Escanea la totalidad de los 65.535 puertos.

* --open: Muestra únicamente los puertos con un estado abierto.

* -sV: Enumera las versiones exactas de los servicios que están corriendo.

* -sC: Ejecuta un conjunto de scripts básicos de Nmap para enumeración por defecto.

* -oN: Guarda los resultados en un archivo de texto llamado escaneo_inicial.txt para consultarlo después.

Resultados del EscaneoTras finalizar el escaneo, identificamos el siguiente panorama de exposición:

PuertoProtocoloEstadoServicioNotas / Versión23TCPAbierto Telnet

Conclusión del Reconocimiento: El único servicio disponible externamente es Telnet en el puerto por defecto (23). Al ser un protocolo heredado que transmite información en texto plano, se convierte inmediatamente en nuestro principal y único vector de ataque.

Procedemos a interactuar con el servicio utilizando el cliente de Telnet desde nuestra terminal:

```bash
telnet 10.129.86.56
```

Al establecer la conexión, el servidor nos presenta un banner de bienvenida y nos solicita un usuario para iniciar sesión (login:).

En sistemas mal configurados o de prueba, es común encontrar cuentas de administración sin contraseña. Intentamos iniciar sesión con el usuario de máximos privilegios en sistemas Linux:

Usuario: root

Contraseña: (Ninguna / En blanco)

```bash
login: root
```


##  Escalada de Privilegios y Flag
El acceso es concedido inmediatamente sin solicitar contraseña. Dado que iniciamos sesión directamente como root, no es necesario realizar ninguna técnica de escalada de privilegios, ya que poseemos control total sobre la máquina.

Verificamos nuestra identidad y listamos el contenido del directorio actual para encontrar la flag:

```bash
whoami
# Resultado: root
ls
# Resultado: flag.txt snap
cat flag.txt
```

##  Conclusiones y Aprendizaje
1. Importancia del escaneo inicial: Identificar puertos abiertos es el primer paso crítico en cualquier auditoría.
2. Riesgos de servicios heredados: Telnet no debe ser utilizado en entornos de producción. Debe ser reemplazado por SSH.
3. Mala configuración de credenciales: Dejar cuentas administrativas sin contraseña (como el usuario root en este caso) representa un compromiso crítico inmediato del sistema.

