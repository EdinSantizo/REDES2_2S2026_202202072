# Análisis real de tráfico con Wireshark — Proyecto 2

**Estudiante:** Edin Rafael Santizo Barrera  
**Carné:** 202202072  
**Archivo:** `captura_wireshark_202202072.pcapng`  
**Origen:** Ubuntu en WSL 2, interfaz de red `eth0`  
**Fecha local de captura:** 23 de octubre de 2026, alrededor de las 20:12 (Guatemala, UTC−6)

## 1. Validez y alcance de la captura

El archivo fue abierto e inspeccionado como PCAPNG real. Registra **39 tramas Ethernet**, divididas en **29 TCP, 2 UDP/DNS y 8 ICMP**. Entre los 29 paquetes TCP se identifican **2 HTTP** y **7 registros/tramas clasificados como TLS**. HTTP/TLS son subconjuntos del tráfico TCP y no se suman otra vez al total.

La IP local de Ubuntu fue **172.31.15.97**; se utilizó el resolvedor público **1.1.1.1** y servidores públicos del dominio `example.com`. Esta captura demuestra el funcionamiento real de protocolos de red, **pero no** contiene el tráfico simulado de los routers de FibraNet, AndinaCom y MoonLink. El análisis de Packet Tracer Simulation debe mostrarse por separado durante la calificación.

## 2. TCP — establecimiento de conexión de tres pasos

**Filtro de Wireshark:** `tcp.port == 80` o `tcp.flags.syn == 1`.

| Trama | Origen → destino | Bandera TCP | Secuencia (absoluta) | ACK (absoluto) | Interpretación |
|---:|---|---|---:|---:|---|
| **3** | `172.31.15.97:52154` → `172.66.147.243:80` | SYN | 378567182 | 0 | Cliente solicita establecer conexión |
| **4** | `172.66.147.243:80` → `172.31.15.97:52154` | SYN, ACK | 3478624694 | 378567183 | Servidor acepta y reconoce el SYN |
| **5** | `172.31.15.97:52154` → `172.66.147.243:80` | ACK | 378567183 | 3478624695 | Cliente confirma; conexión establecida |

**Explicación de los números:** un SYN consume una posición de secuencia; por eso, el ACK del servidor es `378567182 + 1 = 378567183`. El ACK del cliente confirma a su vez el SYN del servidor (`3478624694 + 1 = 3478624695`). En Wireshark podrían aparecer números **relativos** (0, 1, etc.); los valores anteriores son números **absolutos** del encabezado TCP.

**Análisis por capas:**
- **Enlace (Ethernet):** transporta una trama entre el adaptador virtual y el siguiente salto de la red de WSL 2.
- **Red (IPv4):** identifica `172.31.15.97` como origen y `172.66.147.243` como destino.
- **Transporte (TCP):** administra puertos efímeros/del servicio (`52154 → 80`), banderas SYN/SYN-ACK/ACK, secuencias y confirmaciones.
- **Aplicación:** una vez establecida la conexión, circula HTTP.

Existe otro handshake TCP para HTTPS en las tramas **13, 14 y 15**, usando `172.31.15.97:49886` ↔ `104.20.23.154:443`.

## 3. DNS — resolución de nombres

**Filtro:** `dns`.

| Trama | IP de origen → destino | UDP | Contenido |
|---:|---|---|---|
| **1** | `172.31.15.97` → `1.1.1.1` | `34244 → 53` | Pregunta por registro **A** de `example.com` |
| **2** | `1.1.1.1` → `172.31.15.97` | `53 → 34244` | Respuesta **NOERROR**, dos registros A |

**Identificador DNS:** `31664` en la consulta y su respuesta; permite relacionar ambas transacciones.

**Registros devueltos:**

| Dominio | Tipo | Dirección IPv4 | TTL DNS |
|---|---|---|---:|
| `example.com` | A | `104.20.23.154` | 152 s |
| `example.com` | A | `172.66.147.243` | 152 s |

El **TTL DNS** de 152 segundos indica durante cuánto tiempo puede almacenarse la respuesta en caché a partir de ese momento; **no** debe confundirse con el TTL del encabezado IPv4. El transporte fue **UDP**, puerto de destino **53** en la consulta y puerto de origen **53** en la respuesta.

**Análisis por capas:** Ethernet encapsula IPv4; IPv4 direcciona el cliente y el resolvedor `1.1.1.1`; UDP transporta mensajes mediante puertos `34244 ↔ 53`; DNS traduce el nombre `example.com` a registros de tipo A.

## 4. HTTP frente a HTTPS/TLS

**Filtros:** `http`, `tcp.port == 80`, `tls` y `tcp.port == 443`.

### HTTP sin cifrado

- **Trama 6:** petición visible `HEAD / HTTP/1.1`, con cabecera `Host: example.com` y `User-Agent: curl/8.18.0`.
- **Trama 8:** respuesta visible `HTTP/1.1 200 OK`, con encabezados como `Content-Type: text/html; charset=utf-8`.
- **Puerto TCP:** `80`.

**Nota:** se utilizó `curl -I`, que envía `HEAD` y solicita solo los encabezados, **no el contenido HTML del cuerpo**. El hecho de que se lean los encabezados de solicitud y respuesta en las tramas demuestra que esta comunicación HTTP no está protegida mediante TLS.

### HTTPS con TLS

- **Tramas 13–15:** handshake TCP hacia `104.20.23.154:443`.
- **Trama 16:** mensaje TLS **ClientHello**; el nombre de servidor (SNI) indica `example.com`, y se ofrece HTTP/1.1 mediante ALPN.
- **Trama 19:** mensaje TLS **ServerHello**. La extensión `supported_versions` selecciona **TLS 1.3 (0x0304)**, aunque el campo heredado del protocolo indique `0x0303` por compatibilidad. La suite seleccionada se identifica como `0x1302` (TLS_AES_256_GCM_SHA384).
- **Tramas 24 y 26:** contienen registros TLS de tipo **Application Data**, con información cifrada que no puede leerse directamente como una petición/respuesta HTTP en el archivo.
- **Puerto TCP:** `443`.

**Comparación:** HTTP deja visibles cabeceras como `HEAD / HTTP/1.1` y `200 OK`; HTTPS establece primero una sesión TLS, negocia parámetros criptográficos y protege el contenido de aplicación. No se han descifrado las claves de sesión ni se afirma que sea posible leer el cuerpo HTTPS. En TLS 1.3 parte del intercambio posterior a ServerHello también viaja cifrada.

**Análisis por capas:** Ethernet → IPv4 → TCP (puerto 443) → TLS (negociación y protección criptográfica) → HTTP de aplicación transmitido dentro del canal protegido.

## 5. ICMP — diagnóstico de conectividad

**Filtro:** `icmp`.

| Solicitud | Respuesta | IP de origen → destino | Mensaje |
|---:|---:|---|---|
| **29** | **30** | `172.31.15.97` ↔ `1.1.1.1` | Echo Request (tipo 8) / Echo Reply (tipo 0), secuencia 1 |
| **34** | **35** | Idénticas IP | Secuencia 2 |
| **36** | **37** | Idénticas IP | Secuencia 3 |
| **38** | **39** | Idénticas IP | Secuencia 4 |

Cada pareja permite comprobar el envío y la recepción de la solicitud ICMP. Los cuatro paquetes tienen respuesta; el comando ejecutado informó **4 transmitidos, 4 recibidos y 0 % de pérdida**. Los paquetes de ICMP no utilizan puertos TCP ni UDP, sino campos como **tipo, código, identificador y número de secuencia**.

**Análisis por capas:** Ethernet transporta el paquete IPv4; IPv4 identifica origen y destino y usa número de protocolo 1; ICMP implementa mensajes Echo Request (tipo 8, código 0) y Echo Reply (tipo 0, código 0) para diagnosticar conectividad.

## 6. Demostración

Abrir `captura_wireshark_202202072.pcapng` en Wireshark y usar los filtros y números de trama anteriores. Si únicamente está instalado TShark en Ubuntu, puede inspeccionarse con los comandos:

```bash
tshark -r captura_wireshark_202202072.pcapng -Y 'dns || http || tls || icmp' -T fields -e frame.number -e _ws.col.Protocol -e _ws.col.Info

tshark -r captura_wireshark_202202072.pcapng -Y 'tcp.flags.syn == 1' -T fields -e frame.number -e ip.src -e tcp.srcport -e ip.dst -e tcp.dstport -e tcp.flags.str
```

