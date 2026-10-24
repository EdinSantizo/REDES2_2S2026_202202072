# Evidencias reales — Proyecto 2 de Redes 2

Estas capturas son copias **sin alterar** de imágenes proporcionadas por el estudiante durante la elaboración del proyecto. No se generaron pantallas nuevas ni se alteraron resultados. Las capturas provienen de distintos momentos del proceso y no necesariamente muestran simultáneamente el estado definitivo.

## Inventario de capturas

| Archivo | Demuestra o ilustra | Categoría |
|---|---|---|
| [01_topologia_fibranet.png](01_topologia_fibranet.png) | Topología de FibraNet y enlaces entre ISP (vista de Packet Tracer). | Diseño |
| [02_topologia_andinacom.png](02_topologia_andinacom.png) | Topología jerárquica de AndinaCom con routers HSRP y servidor DHCP. | Diseño |
| [03_topologia_moonlink.png](03_topologia_moonlink.png) | Topología hub-and-spoke de MoonLink y segmento inalámbrico. | Diseño |
| [04_tablas_rutas_ospf_eigrp.png](04_tablas_rutas_ospf_eigrp.png) | Tablas de enrutamiento OSPF/EIGRP tomadas durante configuración. | Enrutamiento |
| [05_bgp_vecinos.png](05_bgp_vecinos.png) | Dos vecinos BGP establecidos en R-FIBRA-BORDE, junto a vecinos OSPF. | Enrutamiento |
| [06_ospf_full.png](06_ospf_full.png) | Dos vecinos OSPF FULL en R-ANDINA-BORDE y rutas aprendidas. | Enrutamiento |
| [07_hsrp_active.png](07_hsrp_active.png) | show standby brief en R-A-HSRP-1: grupos 10, 20 y 30 Active. | Redundancia |
| [08_dhcp_atencion.png](08_dhcp_atencion.png) | Configuración IP recibida por DHCP en PC-F-ATC-1. | Servicios |
| [09_dhcp_ventas.png](09_dhcp_ventas.png) | Configuración IP recibida por DHCP en PC-A-VENT-2. | Servicios |
| [10_dhcp_seguridad.png](10_dhcp_seguridad.png) | Configuración IP recibida por DHCP en PC-M-SEG-1. | Servicios |
| [11_web_http.png](11_web_http.png) | Página web personalizada cargada vía HTTP desde PC-F-ADM-1. | Servicios |
| [12_acl_atencion.png](12_acl_atencion.png) | ACL_ATC_PROTO aplicada inbound en Gi0/0 de R-F-ATC-2. | Seguridad |
| [13_acl_hsrp1.png](13_acl_hsrp1.png) | ACLs de Ventas y Facturación en R-A-HSRP-1. | Seguridad |
| [14_acl_hsrp2.png](14_acl_hsrp2.png) | ACLs de Ventas y Facturación en R-A-HSRP-2. | Seguridad |
| [15_acl_seguridad.png](15_acl_seguridad.png) | ACL_SEG_PROTEGIDA aplicada a R-M-SEG. | Seguridad |
| [16_wireshark_icmp_terminal.png](16_wireshark_icmp_terminal.png) | Tráfico ICMP observado mediante TShark en Ubuntu WSL 2. | Wireshark |

## Alcance de las evidencias

Las capturas de configuraciones y rutas muestran información real del dispositivo en el momento fotografiado. Las imágenes de direccionamiento prueban la asignación de IP/gateway/DNS/DHCP en los equipos mostrados, pero no certifican todas las estaciones.

La captura HSRP muestra el estado **Active** del primario; la conmutación al secundario fue **probada y reportada por el estudiante**, pero no hay fotografía de ese instante entre las capturas disponibles.

El estudiante reportó 13 de 13 resultados correctos de Simple PDU y siete comprobaciones correctas tras reabrir la simulación. **No se incluye una captura de la lista Simple PDU que muestre los trece resultados conjuntamente**; esos resultados se documentan como reportados en el manual.

**Pendiente para la demostración de calificación:** recorrido de paquetes en modo **Simulation** de Packet Tracer (DHCP DORA, DNS, HTTP/HTTPS, OSPF/EIGRP, BGP). Estas imágenes no sustituyen esa demostración. La captura **real** de Wireshark se encuentra en `../Wireshark/captura_wireshark_202202072.pcapng`.
