# Meow - Hack The Box (Starting Point)

**Sistema Operativo:** Linux 🐧  
**Dificultad:** Very Easy  
**Plataforma:** Hack The Box (Tier 0)  

## Contexto de la maquina.
Meow es la máquina introductoria del *Starting Point* de Hack The Box. El objetivo principal de este laboratorio es familiarizarse con el proceso de conexión a la VPN de la plataforma, realizar un escaneo básico de puertos y explotar un servicio mal configurado mediante la conexión a un protocolo Telnet utilizando credenciales por defecto nulas.

---

## Reconocimiento (Recon)

Una vez conectado a la red de HTB, comenzamos verificando la conectividad con la máquina objetivo usando `ping`:

```bash
ping 10.129.86.56

A continuación, ejecutamos un escaneo de puertos con Nmap para descubrir qué servicios están corriendo y qué puertos están abiertos.

nmap -sV -sC 10.129.86.56

Resultados clave del escaneo:

El puerto 23/tcp se encuentra abierto.

El servicio corriendo en ese puerto es Telnet.

Telnet es un protocolo de red utilizado en Internet y redes de área local para proporcionar una instalación de comunicación bidireccional interactiva orientada a texto. Es conocido por ser inseguro, ya que transmite los datos (incluidas las contraseñas) en texto plano.

Procedemos a interactuar con el servicio utilizando el cliente de Telnet desde nuestra terminal:

telnet 10.129.86.56

Al establecer la conexión, el servidor nos presenta un banner de bienvenida y nos solicita un usuario para iniciar sesión (login:).

En sistemas mal configurados o de prueba, es común encontrar cuentas de administración sin contraseña. Intentamos iniciar sesión con el usuario de máximos privilegios en sistemas Linux:

Usuario: root

Contraseña: (Ninguna / En blanco)

Bash
login: root

Escalada de Privilegios y Flag
El acceso es concedido inmediatamente sin solicitar contraseña. Dado que iniciamos sesión directamente como root, no es necesario realizar ninguna técnica de escalada de privilegios, ya que poseemos control total sobre la máquina.

Verificamos nuestra identidad y listamos el contenido del directorio actual para encontrar la flag:

Bash
whoami
# Resultado: root

ls
# Resultado: flag.txt snap

cat flag.txt
Flag obtenida: HTB{REDACTED}

Conclusiones y Aprendizaje
Importancia del escaneo inicial: Identificar puertos abiertos es el primer paso crítico en cualquier auditoría.

Riesgos de servicios heredados: Telnet no debe ser utilizado en entornos de producción. Debe ser reemplazado por SSH.

Mala configuración de credenciales: Dejar cuentas administrativas sin contraseña (como el usuario root en este caso) representa un compromiso crítico inmediato del sistema.
