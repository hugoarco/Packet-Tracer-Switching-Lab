<h1 Packet-Tracer-Switching-Lab <h1>

## Laboratorio de Conmutación y Aprendizaje de Direcciones MAC con Cisco Packet Tracer

## 📋 Descripción
Esta práctica de laboratorio se centra en comprender los fundamentos de la conmutación en Capa 2 utilizando Cisco Packet Tracer. El laboratorio explora cómo los switches aprenden direcciones MAC, gestionan tramas y la importancia de una configuración IP adecuada para la comunicación en red.

## 🎯 Objetivos de Aprendizaje
- Comprender el funcionamiento de la conmutación en Capa 2
- Aprender cómo los switches construyen y mantienen tablas de direcciones MAC
- Entender la diferencia entre switches y hubs
- Configurar direccionamiento IP básico en PCs
- Verificar la conectividad usando ICMP (ping)
- Comprender el proceso de resolución ARP y su efecto en la latencia del ping
- Reconocer la importancia del direccionamiento en la misma subred para la comunicación local

## 🛠️ Requisitos
- Cisco Packet Tracer 8.x o superior
- Conocimientos básicos de direccionamiento IP y subredes

## 📁 Estructura del Laboratorio

### Parte 1: Red con un Solo Switch

| Dispositivo | Dirección IP | Máscara de Subred |
|-------------|-------------|-------------------|
| PC Iván | 192.168.0.2 | 255.255.255.0 |
| PC Iván F | 192.168.0.3 | 255.255.255.0 |
| PC Mia | 192.168.0.4 | 255.255.255.0 |
| PC Chaidi | 192.168.0.5 | 255.255.255.0 |
| PC Diego | 192.168.0.6 | 255.255.255.0 |

**Preguntas Clave Exploradas:**
1. ¿Por qué no se le asigna una IP al switch?
2. ¿Debemos asignar IPs de la misma subred? ¿Por qué?
3. ¿Por qué el primer envío se reenvía a todos los puertos?
4. ¿Cómo selecciona el switch por qué puertos enviar los mensajes?
5. Mostrar y explicar la tabla de direcciones MAC usando `show mac-address-table`
6. ¿Con qué tipo de mensajes trabaja un switch?

### Parte 2: Red con Dos Switches

**Switch 1 - Red 192.168.1.0/24**

| Dispositivo | Dirección IP | Máscara de Subred |
|-------------|-------------|-------------------|
| PC Xabi | 192.168.1.11 | 255.255.255.0 |
| PC Diego | 192.168.1.12 | 255.255.255.0 |
| PC Adrián | 192.168.1.13 | 255.255.255.0 |
| PC Chaidi | 192.168.1.14 | 255.255.255.0 |

**Switch 2 - Red 192.168.2.0/24**

| Dispositivo | Dirección IP | Máscara de Subred |
|-------------|-------------|-------------------|
| PC Sergio | 192.168.2.21 | 255.255.255.0 |
| PC Barroso | 192.168.2.22 | 255.255.255.0 |
| PC Rubén | 192.168.2.24 | 255.255.255.0 |
| PC Hugo | 192.168.2.25 | 255.255.255.0 |

## 🔬 Conceptos Clave

### 1. ¿Por qué un switch no necesita una dirección IP?
- Un switch opera en la **Capa 2 (Nivel de Enlace de Datos)**
- Utiliza **direcciones MAC** para reenviar tramas
- Una dirección IP solo es necesaria para fines de gestión (acceso remoto vía SSH/Telnet), no para el funcionamiento básico de conmutación

### 2. ¿Por qué las IPs deben pertenecer a la misma subred?
- Los dispositivos deben estar en el **mismo rango IP** para comunicarse directamente
- Ejemplo: `192.168.0.1` puede comunicarse con `192.168.0.2` ✅
- Ejemplo: `192.168.0.1` NO puede comunicarse con `192.168.1.2` ❌ (diferentes subredes)
- En el laboratorio, los PCs en `192.168.1.0/24` no pueden comunicarse con los PCs en `192.168.2.0/24` sin un router

### 3. ¿Por qué el primer envío se reenvía a todos los puertos (flooding)?
- Un switch opera en Capa 2 utilizando direcciones MAC
- Cuando el switch no conoce la dirección MAC de destino, no sabe a qué puerto reenviar
- El switch realiza **flooding** (envía la trama a todos los puertos excepto al que la recibió)
- Esto es parte del proceso **ARP (Address Resolution Protocol)**

### 4. ¿Cómo selecciona el switch por qué puertos enviar los mensajes?

**Paso 1: Aprendizaje de direcciones MAC**
- Cuando llega una trama a un puerto, el switch lee la **dirección MAC de origen**
- Almacena la relación MAC-puerto en su tabla CAM (Content Addressable Memory)
- Ejemplo:
  - `AA:BB:CC:DD:EE:01` → Puerto 1
  - `AA:BB:CC:DD:EE:02` → Puerto 2

**Paso 2: Decisión de reenvío**
- Cuando llega una trama con una dirección MAC de destino, el switch consulta su tabla:
  - **MAC encontrada** → Reenviar solo al puerto correspondiente
  - **MAC no encontrada** → Flooding a todos los puertos (excepto el de entrada)

**Paso 3: Actualización constante**
- Las entradas de la tabla MAC tienen un temporizador de envejecimiento (típicamente 300 segundos)
- Si no se recibe tráfico de una dirección MAC, la entrada se elimina
- Esto evita que la tabla conserve información obsoleta

### 5. Ejemplo de Tabla de Direcciones MAC

**Comando:** `show mac-address-table`

| Vlan | Dirección MAC | Tipo | Puertos |
|------|---------------|------|---------|
| 1 | 0009.7c0a.cd89 | DYNAMIC | Fa0/5 |
| 1 | 0060.3e0b.01e1 | DYNAMIC | Fa0/1 |

**Explicación de Columnas:**
- **Vlan:** VLAN a la que pertenece la dirección MAC (VLAN 1 por defecto)
- **Dirección MAC:** Dirección física del dispositivo conectado
- **Tipo:** Cómo se aprendió la entrada:
  - `DYNAMIC` → Aprendida automáticamente, caduca tras inactividad
  - `STATIC` → Configurada manualmente
  - `SECURE` → Asociada a seguridad de puertos
- **Puertos:** Puerto del switch donde se vio por última vez esa dirección MAC

### 6. Protocolos Involucrados

**ICMP (Internet Control Message Protocol)**
- **Capa OSI:** Capa 3 (Red)
- **Propósito:** Diagnóstico y control de red
- **Ejemplo:** `ping` comprueba si un dispositivo está accesible
- **Funcionamiento:**
  - Origen envía: ICMP Echo Request
  - Destino responde: ICMP Echo Reply

**ARP (Address Resolution Protocol)**
- **Capa OSI:** Entre Capa 2 y Capa 3
- **Propósito:** Traducir direcciones IP a MAC en una red local
- **Funcionamiento:**
  1. El dispositivo envía ARP Request (broadcast): "¿Quién tiene esta IP?"
  2. El propietario responde con ARP Reply: "Yo, mi MAC es XX:XX:XX:XX:XX:XX"
  3. El resultado se almacena en la caché ARP del dispositivo

### 7. ¿Por qué el primer ping es más lento?

- **Antes del primer ping:** El dispositivo origen no conoce la dirección MAC del destino
- **El proceso ARP añade latencia:**
  1. El origen envía un ARP Request en broadcast
  2. El destino responde con un ARP Reply
  3. Esto añade aproximadamente 1-2 ms de latencia
- **Pings siguientes:** La MAC ya está en la caché ARP, no hay retardo adicional

### 8. Comparativa Switch vs Hub

| Característica | Switch | Hub |
|----------------|--------|-----|
| Capa OSI | Capa 2 (Enlace de Datos) | Capa 1 (Física) |
| Inteligencia | Dispositivo Inteligente | Dispositivo No Inteligente |
| Gestión de MACs | Aprende y usa direcciones MAC | No entiende direcciones MAC |
| Entrega de Tramas | Unicast a puerto específico | Broadcast a todos los puertos |
| Dominio de Colisión | Cada puerto es su propio dominio | Dominio de colisión único |
| Modo Dúplex | Full-Dúplex soportado | Half-Dúplex únicamente |
| Rendimiento | Alto (reenvío selectivo) | Bajo (ancho de banda compartido) |

## 🚀 Cómo Usar Este Laboratorio

### Instrucciones de Configuración:
1. Abrir Cisco Packet Tracer
2. Abrir el archivo `*.pkt`
3. Revisar la topología y las configuraciones IP
4. Verificar la conectividad usando `ping`

### Comandos de Ejemplo:

```cisco
# Mostrar tabla de direcciones MAC en el switch
Switch> enable
Switch# show mac-address-table

# Mostrar tabla de direcciones MAC con detalles
Switch# show mac-address-table dynamic
Switch# show mac-address-table count
bash
# Verificar conectividad desde el PC
ping 192.168.1.12
# Verificar caché ARP
arp -a
📊 Topología
Topología 1: Un Solo Switch
text
     [Switch]
    /    |    \
   /     |     \
PC-1   PC-2   PC-3 (todos en la misma subred)
Topología 2: Dos Switches
text
[Switch 1] --- (Crossover) --- [Switch 2]
   |    |                      |    |
  PC-A PC-B                   PC-C PC-D
(192.168.1.0/24)         (192.168.2.0/24)
📝 Conclusiones Clave
Direccionamiento IP: Para comunicación directa, los dispositivos deben estar en la misma subred

Conmutación Capa 2: Los switches usan direcciones MAC, no IP, para reenviar tramas

Aprendizaje MAC: Los switches construyen dinámicamente tablas MAC inspeccionando direcciones de origen

Flooding: Las MAC destino desconocidas provocan flooding para asegurar la entrega

Resolución ARP: Causa mayor latencia en el primer ping

Switch vs Hub: Los switches son inteligentes (Capa 2), los hubs no (Capa 1)

Dominios de Colisión: Los switches reducen colisiones aislando cada puerto

🧪 Lista de Verificación
Todos los PCs configurados con direcciones IP correctas

PCs en la misma subred pueden comunicarse

PCs en diferentes subredes NO pueden comunicarse

La tabla de direcciones MAC muestra las direcciones aprendidas

El primer ping tiene mayor latencia que los siguientes

El switch no necesita IP para reenviar tramas

👨‍💻 Autor
Hugo Arcones

📝 Licencia
MIT License - Para fines educativos únicamente
