# Proyecto 2 — Redes de Computadoras 2

**Universidad de San Carlos de Guatemala**  
**Facultad de Ingeniería — Ingeniería en Ciencias y Sistemas**  
**Autor:** Edin Rafael Santizo Barrera  
**Carné:** 202202072  
**Segundo semestre de 2026**

## Descripción del proyecto

Diseñé e implementé en **Cisco Packet Tracer 8.2.2.0400** una infraestructura de telecomunicaciones que interconecta tres proveedores de servicios de Internet (ISP): **FibraNet GT, AndinaCom y MoonLink**. Mi propuesta permite comunicar los departamentos de las tres empresas, compartir servicios de red y mantener reglas de acceso entre los diferentes segmentos.

Cada ISP tiene una topología y un protocolo de enrutamiento interno apropiados para su organización. Utilicé **BGP** para intercambiar rutas entre las empresas, **OSPF** en FibraNet GT y AndinaCom, y **EIGRP** en MoonLink.

## Arquitectura implementada

| ISP | Diseño | Enrutamiento interno | Aporte principal |
|---|---|---|---|
| **FibraNet GT** | Árbol | OSPF (área 0) | DNS y servidor web HTTP/HTTPS |
| **AndinaCom** | Jerárquico | OSPF (área 0) | DHCP centralizado, VLAN y respaldo HSRP |
| **MoonLink** | Hub-and-spoke | EIGRP (AS 100) | Soporte, Seguridad y conexión WiFi |

Configuré **15 routers Cisco 2911**, **13 switches Cisco 2960**, **14 computadoras**, **1 laptop inalámbrica**, **2 servidores** y **1 router inalámbrico**. Implementé seis agregaciones de enlaces **EtherChannel mediante LACP**.

La interconexión de los routers de borde se realiza con **eBGP**, utilizando los sistemas autónomos **65001, 65002 y 65003**.

## Servicios disponibles

| Servicio | Ubicación | Identificación |
|---|---|---|
| DNS y sitio web | FibraNet GT | Servidor `SRV-F-DNS-WEB` — `172.16.12.10` |
| Dominio web | FibraNet GT | `www.proyecto2_202202072.com` |
| DHCP centralizado | AndinaCom | Servidor `SRV-A-DHCP` — `172.16.22.140` |
| Red inalámbrica | MoonLink | SSID `MOONLINK_202202072` |
| Redundancia de puerta de enlace | AndinaCom | HSRP, grupos 10, 20 y 30 |
| Seguridad entre departamentos | Tres ISP | Listas de control de acceso extendidas (ACL) |

Los equipos finales reciben su configuración de red por **DHCP**. Las direcciones de los servidores son estáticas para mantener disponibles sus servicios centrales.

## Cómo utilizar la simulación

1. Abro `Proyecto2_202202072.pkt` con **Cisco Packet Tracer 8.2 o superior**.
2. Selecciono **Realtime** para recorrer la topología y utilizar las computadoras.
3. En una PC entro a **Desktop → IP Configuration → DHCP** para obtener la dirección IP.
4. Para consultar la página web abro **Desktop → Web Browser** y escribo `http://www.proyecto2_202202072.com` o `https://www.proyecto2_202202072.com`.
5. Para comprobar la comunicación utilizo **Desktop → Command Prompt** (`ping`) o **Add Simple PDU** (ícono del sobre).
6. Para inspeccionar configuraciones abro la pestaña **CLI** del router o switch correspondiente.

El documento [`MANUAL_USUARIO.md`](MANUAL_USUARIO.md) desarrolla cada operación con pasos y ejemplos fáciles de seguir.

## Resultados de las pruebas

Durante mis pruebas de funcionamiento obtuve **13 de 13 resultados esperados** en la matriz de comunicación ICMP entre departamentos: los enlaces autorizados respondieron y las conexiones restringidas fueron bloqueadas. También comprobé la conmutación de **HSRP**, la asignación de direcciones mediante DHCP, la navegación HTTP/HTTPS, la resolución DNS y el restablecimiento de los protocolos después de cerrar y reabrir el archivo `.pkt`.

Documenté el comportamiento de los protocolos mediante una captura real de **Wireshark/TShark** desde Ubuntu en WSL 2. El archivo contiene **39 tramas**, incluyendo el establecimiento de TCP, DNS, HTTP, TLS e ICMP.

> Las pruebas de comunicación de Packet Tracer y la captura de Wireshark corresponden a entornos distintos: el primero simula la infraestructura diseñada y el segundo registra tráfico real generado desde Ubuntu.

## Estructura del proyecto

```text
Proyecto_2/
├── README.md
├── MANUAL_USUARIO.md
├── MANUAL_USUARIO.pdf
├── MANUAL_TECNICO.md
├── Proyecto2_202202072.pkt
├── Proyecto2_Presentacion_202202072.pdf
├── Proyecto2_Presentacion_202202072.pptx
├── GUION_EXPOSICION.md
├── Configuraciones/
│   └── Configuraciones de los 15 routers y 13 switches
├── Evidencias/
│   ├── Capturas de topologías, servicios y configuraciones
│   └── INDICE_EVIDENCIAS.md
└── Wireshark/
    ├── captura_wireshark_202202072.pcapng
    └── ANALISIS_CAPTURA.md
```

## Documentación y evidencias

- **[Manual de usuario](MANUAL_USUARIO.md):** instrucciones para abrir y utilizar la red, navegar, hacer pruebas y visualizar capturas.
- **[Manual técnico](MANUAL_TECNICO.md):** topologías, subneteo, direccionamiento, protocolos, configuración, ACL y pruebas.
- **[Índice de evidencias](Evidencias/INDICE_EVIDENCIAS.md):** fotografías del desarrollo y funcionamiento.
- **[Análisis Wireshark](Wireshark/ANALISIS_CAPTURA.md):** explicación por capas y números reales de trama de TCP, DNS, HTTP/HTTPS e ICMP.
- **Presentación ejecutiva:** `Proyecto2_Presentacion_202202072.pdf`, con el guion en `GUION_EXPOSICION.md`.

## Herramientas utilizadas

- Cisco Packet Tracer 8.2.2.0400.
- Ubuntu con WSL 2.
- TShark / Wireshark 4.6.4.
- GitHub como plataforma de publicación y organización de archivos.

---

**Edin Rafael Santizo Barrera — Carné 202202072**  
**Redes de Computadoras 2 · USAC · 2026**
