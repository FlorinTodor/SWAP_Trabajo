# Implementación de un WAF en entornos Docker – SWAP (UGR)

## 📌 Descripción
Este proyecto, desarrollado como parte de la asignatura **Servidores Web de Altas Prestaciones (SWAP)** en la Universidad de Granada, consiste en la implementación de un **Web Application Firewall (WAF)** para proteger una granja web frente a ataques comunes.  
Se ha utilizado **ModSecurity** junto al conjunto de reglas **OWASP Core Rule Set (CRS)** desplegados sobre un contenedor **Nginx** en un entorno Docker, intercalando este WAF entre el cliente y los servidores backend.

El sistema permite mitigar ataques como:
- Inyección SQL (SQLi)
- Cross-Site Scripting (XSS)
- Inclusión de ficheros locales y remotos (LFI/RFI)
- Peticiones maliciosas en general

---

## 🎯 Objetivos
1. Integrar un **WAF** en un entorno de balanceo y múltiples servidores web.
2. Configurar **ModSecurity** con **OWASP CRS** para detección y bloqueo de ataques.
3. Realizar **pruebas funcionales** contra vulnerabilidades conocidas.
4. Evaluar el impacto en **rendimiento** mediante pruebas de carga.
5. Documentar el procedimiento completo y analizar resultados.

---

## 🛠 Tecnologías utilizadas
- **Docker** y **Docker Compose**
- **Nginx** como balanceador inverso y servidor WAF
- **ModSecurity v3**
- **OWASP Core Rule Set (CRS)**
- **Apache HTTP Server** como servidores backend
- **Apache Benchmark (ab)** para pruebas de carga
- **Bash scripting** para automatización

---

## 📐 Arquitectura
La infraestructura implementada está formada por:
- **1 contenedor WAF**: Nginx con ModSecurity y reglas OWASP CRS.
- **2 contenedores backend** con Apache.
- **Red Docker interna** `red_web` para la comunicación segura entre WAF y backends.

**Esquema general:**
```text
Cliente  →  WAF (Nginx + ModSecurity + CRS)  →  Apache Backends
