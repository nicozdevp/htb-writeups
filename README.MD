# Hack The Box - Writeups & Metodologías

Este repositorio contiene la documentación detallada, metodologías y el proceso paso a paso utilizado para resolver diversas máquinas y desafíos dentro de la plataforma **Hack The Box**. El objetivo de este espacio es registrar mi progreso en ciberseguridad, estructurar mi aprendizaje y servir como un portafolio técnico.

---

## Mi Perfil de Hack The Box
Puedes ver mi progreso en tiempo real y mis estadísticas oficiales aquí:
👉 [Perfil de Hack The Box](https://app.hackthebox.com/users/3301542)

---

## Índice de Máquinas Resueltas

### Starting Point (Introducción)
Máquinas iniciales enfocadas en la familiarización con protocolos básicos y metodologías de enumeración esenciales.

| Máquina | Sistema Operativo | Dificultad | Vectores / Vulnerabilidades Clave | Reporte |
| :--- | :---: | :---: | :--- | :---: |
| **Meow** | 🐧 Linux | Very Easy | Telnet, Enumeración de servicios básicos, Credenciales por defecto | [Ver Writeup](./starting-point/Meow/README.md) |
| **Fawn** | 🐧 Linux | Very Easy | FTP, Conexión anónima (Anonymous login) | [Ver Writeup](./starting-point/Fawn/README.md) |

### Máquinas Estándar
Resolución de entornos completos divididos por Sistema Operativo.

| Máquina | Sistema Operativo | Dificultad | Vectores / Vulnerabilidades Clave | Reporte |
| :--- | :---: | :---: | :--- | :---: |
| **Cap** | 🐧 Linux | Easy | IDOR (Insecure Direct Object Reference), Análisis de tráfico (PCAP), Capabilities | [Ver Writeup](./linux/Cap-Easy/README.md) |

---

## Herramientas Utilizadas Frecuentemente
* **Reconocimiento y Enumeración:** `nmap`, `gobuster`, `feroxbuster`, `enum4linux`.
* **Análisis y Explotación:** `wireshark`, `netcat`, `python3`.
* **Escalada de Privilegios:** `linpeas.sh`, `winpeas.exe`.

## Aviso Legal
De acuerdo con los Términos de Servicio de Hack The Box, este repositorio **solo contiene soluciones de máquinas retiradas (Retired) o de la sección Starting Point**. No se publicará ningún tipo de información, pistas o flags de máquinas activas (Active) para preservar la integridad de los desafíos de la plataforma.
