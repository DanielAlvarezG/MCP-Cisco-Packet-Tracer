# "Guía para la instalación y configuración automatizada de MCP y Cisco Packet Tracer."

> Guía completa y gratuita para replicar una topología de red empresarial con 16 subredes, 15 switches, Router on a Stick con VLANs, y enrutamiento dinámico OSPF usando VLSM sobre el espacio `192.168.168.0/20`.

---

## 📋 Tabla de Contenidos

1. [Requisitos previos](#requisitos-previos)
2. [Instalación gratuita de Cisco Packet Tracer](#instalación-gratuita-de-cisco-packet-tracer)
3. [Descripción de la topología](#descripción-de-la-topología)
4. [Cálculo VLSM](#cálculo-vlsm)
5. [Construcción paso a paso](#construcción-paso-a-paso)
6. [Configuración de Router on a Stick (R1)](#configuración-de-router-on-a-stick-r1)
7. [Configuración de routers R2–R15](#configuración-de-routers-r2r15)
8. [Configuración de switches](#configuración-de-switches)
9. [Configuración de PCs](#configuración-de-pcs)
10. [Verificación de conectividad](#verificación-de-conectividad)
11. [Solución de problemas comunes](#solución-de-problemas-comunes)

---

## ✅ Requisitos previos

- Computadora con Windows, macOS o Linux
- Cuenta gratuita en **Cisco Networking Academy** (NetAcad)
- Cisco Packet Tracer (versión 8.x o superior) — **completamente gratis**
- Conocimientos básicos de redes (direccionamiento IP, subredes)

---

## 🆓 Instalación gratuita de Cisco Packet Tracer

Cisco Packet Tracer es **100% gratuito** para uso educativo. Sigue estos pasos:

### Paso 1: Crear cuenta en Cisco NetAcad

1. Ve a **[https://www.netacad.com](https://www.netacad.com)**
2. Haz clic en **"Sign up"** (esquina superior derecha)
3. Completa el formulario con tu correo electrónico
4. Verifica tu correo y activa la cuenta

### Paso 2: Inscribirse al curso gratuito

1. Una vez con sesión iniciada, busca el curso **"Networking Essentials"** o **"Introduction to Packet Tracer"**
2. Haz clic en **"Get Started"** — es completamente gratuito
3. Esto te dará acceso a la descarga de Packet Tracer

### Paso 3: Descargar Packet Tracer

1. Ingresa a **[https://www.netacad.com/resources/lab-downloads](https://www.netacad.com/resources/lab-downloads)**
2. Selecciona tu sistema operativo (Windows / macOS / Linux)
3. Descarga el instalador (aprox. 200 MB)
4. Instala con las opciones predeterminadas

### Paso 4: Primer inicio de sesión

1. Abre Packet Tracer
2. Se te pedirá iniciar sesión con tu cuenta NetAcad
3. Una vez dentro, ¡ya estás listo!

> 💡 **Tip:** Si no quieres crear una cuenta, puedes usar el modo **"Guest Login"** con funcionalidad limitada.

---

## 🗺️ Descripción de la topología

Esta red implementa lo siguiente:

| Elemento | Cantidad | Modelo |
|----------|---------|--------|
| Routers | 15 | Cisco 2911 |
| Switches | 15 | Cisco 2960-24TT |
| PCs | 45 (3 por LAN) | PC-PT |
| Redes totales | 16 | — |
| Router on a Stick | 1 (R1) | Con VLAN 10 y VLAN 20 |
| Protocolo de enrutamiento | OSPF | Proceso 1, Área 0 |
| Espacio de direcciones | 192.168.168.0/20 | 4096 hosts totales |

### Diagrama lógico

```
[PC1-3]─[SW1]─[R1]─(WAN)─[R2]─(WAN)─[R3]─...─[R15]─[SW15]─[PC43-45]
                ↑
         VLAN10 / VLAN20
         (Router on a Stick)
```

Los routers están conectados en **cadena lineal** (R1↔R2↔R3↔...↔R15) con enlaces `/30` punto a punto. Cada router tiene un switch con 3 PCs conectados.

---

## 📐 Cálculo VLSM

Red base: **`192.168.168.0/20`** → Rango: `192.168.168.0 – 192.168.175.255`

Las subredes se asignan de **mayor a menor** requerimiento de hosts:

| # | Uso | Subred | Máscara | Gateway | Hosts útiles |
|---|-----|--------|---------|---------|-------------|
| 1 | LAN R1 – VLAN 10 | 192.168.168.0 | /24 | 192.168.168.1 | 254 |
| 2 | LAN R1 – VLAN 20 | 192.168.169.0 | /24 | 192.168.169.1 | 254 |
| 3 | LAN R2 | 192.168.170.0 | /24 | 192.168.170.1 | 254 |
| 4 | LAN R3 | 192.168.171.0 | /24 | 192.168.171.1 | 254 |
| 5 | LAN R4 | 192.168.172.0 | /24 | 192.168.172.1 | 254 |
| 6 | LAN R5 | 192.168.173.0 | /24 | 192.168.173.1 | 254 |
| 7 | LAN R6 | 192.168.174.0 | /24 | 192.168.174.1 | 254 |
| 8 | LAN R7 | 192.168.175.0 | /24 | 192.168.175.1 | 254 |
| 9 | LAN R8 | 192.168.168.128 | /25 | 192.168.168.129 | 126 |
| 10 | LAN R9 | 192.168.169.128 | /25 | 192.168.169.129 | 126 |
| 11 | LAN R10 | 192.168.170.0 | /26 | 192.168.170.1 | 62 |
| 12 | LAN R11 | 192.168.170.64 | /26 | 192.168.170.65 | 62 |
| 13 | LAN R12 | 192.168.170.128 | /27 | 192.168.170.129 | 30 |
| 14 | LAN R13 | 192.168.170.160 | /27 | 192.168.170.161 | 30 |
| 15 | Enlace R14–R15 | 192.168.170.192 | /30 | — | 2 |
| 16 | Enlace R13–R14 | 192.168.170.196 | /30 | — | 2 |

> 💡 **¿Qué es VLSM?** Variable Length Subnet Masking permite usar máscaras de diferente longitud dentro del mismo espacio de red, optimizando el uso de direcciones IP.

---

## 🔨 Construcción paso a paso

### Paso 1: Abrir Packet Tracer y crear nuevo proyecto

1. Abre Cisco Packet Tracer
2. Ve a **File → New** para crear un proyecto vacío
3. Guárdalo con un nombre descriptivo: `red_15routers_ospf_vlsm.pkt`

### Paso 2: Agregar los 15 routers

1. En el panel inferior izquierdo, selecciona la categoría **"Routers"**
2. Busca el modelo **Cisco 2911**
3. Arrastra 15 routers al área de trabajo
4. Nómbralos R1, R2, R3... R15 haciendo doble clic sobre cada uno

> 💡 **Tip de organización:** Coloca los routers en línea horizontal para que la topología sea fácil de leer.

### Paso 3: Agregar los 15 switches

1. En el panel inferior, selecciona la categoría **"Switches"**
2. Busca el modelo **Cisco 2960-24TT**
3. Arrastra 15 switches al área de trabajo
4. Coloca cada switch debajo de su router correspondiente
5. Nómbralos SW1, SW2... SW15

### Paso 4: Agregar los PCs

1. En el panel inferior, selecciona **"End Devices"**
2. Selecciona **PC-PT**
3. Agrega 3 PCs debajo de cada switch (45 PCs en total)
4. Nómbralos PC1–PC45

### Paso 5: Conectar los cables

**Entre routers (cable cruzado):**
- En la barra de herramientas, selecciona el ícono de **cable** → **Copper Cross-Over**
- Conecta: R1 Gi0/0 ↔ R2 Gi0/0
- Conecta: R2 Gi0/1 ↔ R3 Gi0/0
- Repite el patrón hasta R14 Gi0/1 ↔ R15 Gi0/0

**Entre routers y switches (cable directo):**
- Selecciona **Copper Straight-Through**
- Conecta: R1 Gi0/1 ↔ SW1 Gi0/1
- Conecta: R2 Gi0/2 ↔ SW2 Gi0/1
- Repite para todos los routers

**Entre switches y PCs (cable directo):**
- Conecta: SW1 Fa0/1 ↔ PC1 Fa0
- Conecta: SW1 Fa0/2 ↔ PC2 Fa0
- Conecta: SW1 Fa0/3 ↔ PC3 Fa0
- Repite para todos los switches

> ⚠️ **Nota importante para R1:** R1 usa **Router on a Stick**, por lo que su interfaz Gi0/1 se conecta al SW1 y se configurará con subinterfaces (no con IP directa).

---

## 🔀 Configuración de Router on a Stick (R1)

El Router on a Stick permite que **una sola interfaz física** maneje múltiples VLANs usando subinterfaces con encapsulación 802.1Q.

### En R1 — Abrir la CLI:
1. Haz clic sobre **R1**
2. Ve a la pestaña **CLI**
3. Presiona Enter si aparece el prompt
4. Pega la siguiente configuración:

```cisco
enable
configure terminal
hostname R1
no ip domain-lookup

! Enlace WAN hacia R2 (subred /30)
interface GigabitEthernet0/0
 ip address 10.0.0.1 255.255.255.252
 no shutdown
 exit

! Interfaz física hacia SW1 - SIN IP directa (RoaS)
interface GigabitEthernet0/1
 no shutdown
 exit

! Subinterfaz VLAN 10 → Red 192.168.168.0/24
interface GigabitEthernet0/1.10
 encapsulation dot1Q 10
 ip address 192.168.168.1 255.255.255.0
 no shutdown
 exit

! Subinterfaz VLAN 20 → Red 192.168.169.0/24
interface GigabitEthernet0/1.20
 encapsulation dot1Q 20
 ip address 192.168.169.1 255.255.255.0
 no shutdown
 exit

! DHCP para VLAN 10
ip dhcp excluded-address 192.168.168.1
ip dhcp pool VLAN10
 network 192.168.168.0 255.255.255.0
 default-router 192.168.168.1
 dns-server 8.8.8.8
 exit

! DHCP para VLAN 20
ip dhcp excluded-address 192.168.169.1
ip dhcp pool VLAN20
 network 192.168.169.0 255.255.255.0
 default-router 192.168.169.1
 dns-server 8.8.8.8
 exit

! OSPF - anuncia ambas redes y el enlace WAN
router ospf 1
 router-id 1.1.1.1
 network 192.168.168.0 0.0.0.255 area 0
 network 192.168.169.0 0.0.0.255 area 0
 network 10.0.0.0 0.0.0.3 area 0
 exit

end
write memory
```

### En SW1 — Configurar VLANs y trunk:

```cisco
enable
configure terminal
hostname SW1

! Crear VLANs
vlan 10
 name DEPARTAMENTO_A
 exit
vlan 20
 name DEPARTAMENTO_B
 exit

! Puerto trunk hacia R1 (permite ambas VLANs)
interface GigabitEthernet0/1
 switchport mode trunk
 switchport trunk allowed vlan 10,20
 no shutdown
 exit

! PC1 y PC2 en VLAN 10
interface FastEthernet0/1
 switchport mode access
 switchport access vlan 10
 no shutdown
 exit

interface FastEthernet0/2
 switchport mode access
 switchport access vlan 10
 no shutdown
 exit

! PC3 en VLAN 20
interface FastEthernet0/3
 switchport mode access
 switchport access vlan 20
 no shutdown
 exit

end
write memory
```

---

## ⚙️ Configuración de routers R2–R15

Cada router intermedio tiene el mismo patrón: dos interfaces WAN y una LAN. A continuación se muestra la configuración completa para cada uno.

### R2

```cisco
enable
configure terminal
hostname R2
no ip domain-lookup

interface GigabitEthernet0/0
 ip address 10.0.0.2 255.255.255.252
 no shutdown
 exit

interface GigabitEthernet0/1
 ip address 10.0.0.5 255.255.255.252
 no shutdown
 exit

interface GigabitEthernet0/2
 ip address 192.168.1.1 255.255.255.0
 no shutdown
 exit

ip dhcp excluded-address 192.168.1.1
ip dhcp pool LAN_R2
 network 192.168.1.0 255.255.255.0
 default-router 192.168.1.1
 dns-server 8.8.8.8
 exit

router ospf 1
 router-id 2.2.2.2
 network 10.0.0.0 0.0.0.3 area 0
 network 10.0.0.4 0.0.0.3 area 0
 network 192.168.1.0 0.0.0.255 area 0
 exit

end
write memory
```

### R3

```cisco
enable
configure terminal
hostname R3
no ip domain-lookup

interface GigabitEthernet0/0
 ip address 10.0.0.6 255.255.255.252
 no shutdown
 exit

interface GigabitEthernet0/1
 ip address 10.0.0.9 255.255.255.252
 no shutdown
 exit

interface GigabitEthernet0/2
 ip address 192.168.2.1 255.255.255.0
 no shutdown
 exit

ip dhcp excluded-address 192.168.2.1
ip dhcp pool LAN_R3
 network 192.168.2.0 255.255.255.0
 default-router 192.168.2.1
 dns-server 8.8.8.8
 exit

router ospf 1
 router-id 3.3.3.3
 network 10.0.0.4 0.0.0.3 area 0
 network 10.0.0.8 0.0.0.3 area 0
 network 192.168.2.0 0.0.0.255 area 0
 exit

end
write memory
```

### R4–R14 (patrón repetitivo)

Para los routers R4 hasta R14, sigue el mismo patrón incrementando las IPs:

| Router | Gi0/0 | Gi0/1 | Gi0/2 (LAN) | OSPF router-id |
|--------|-------|-------|-------------|----------------|
| R4 | 10.0.0.10/30 | 10.0.0.13/30 | 192.168.3.1/24 | 4.4.4.4 |
| R5 | 10.0.0.14/30 | 10.0.0.17/30 | 192.168.4.1/24 | 5.5.5.5 |
| R6 | 10.0.0.18/30 | 10.0.0.21/30 | 192.168.5.1/24 | 6.6.6.6 |
| R7 | 10.0.0.22/30 | 10.0.0.25/30 | 192.168.6.1/24 | 7.7.7.7 |
| R8 | 10.0.0.26/30 | 10.0.0.29/30 | 192.168.7.1/24 | 8.8.8.8 |
| R9 | 10.0.0.30/30 | 10.0.0.33/30 | 192.168.8.1/24 | 9.9.9.9 |
| R10 | 10.0.0.34/30 | 10.0.0.37/30 | 192.168.9.1/24 | 10.10.10.10 |
| R11 | 10.0.0.38/30 | 10.0.0.41/30 | 192.168.10.1/24 | 11.11.11.11 |
| R12 | 10.0.0.42/30 | 10.0.0.45/30 | 192.168.11.1/24 | 12.12.12.12 |
| R13 | 10.0.0.46/30 | 10.0.0.49/30 | 192.168.12.1/24 | 13.13.13.13 |
| R14 | 10.0.0.50/30 | 10.0.0.53/30 | 192.168.13.1/24 | 14.14.14.14 |

Plantilla para cada uno (reemplaza los valores según la tabla):

```cisco
enable
configure terminal
hostname RX
no ip domain-lookup

interface GigabitEthernet0/0
 ip address <Gi0/0> 255.255.255.252
 no shutdown
 exit

interface GigabitEthernet0/1
 ip address <Gi0/1> 255.255.255.252
 no shutdown
 exit

interface GigabitEthernet0/2
 ip address <LAN>.1 255.255.255.0
 no shutdown
 exit

ip dhcp excluded-address <LAN>.1
ip dhcp pool LAN_RX
 network <LAN>.0 255.255.255.0
 default-router <LAN>.1
 dns-server 8.8.8.8
 exit

router ospf 1
 router-id X.X.X.X
 network <Gi0/0-subnet> 0.0.0.3 area 0
 network <Gi0/1-subnet> 0.0.0.3 area 0
 network <LAN>.0 0.0.0.255 area 0
 exit

end
write memory
```

### R15

```cisco
enable
configure terminal
hostname R15
no ip domain-lookup

interface GigabitEthernet0/0
 ip address 10.0.0.54 255.255.255.252
 no shutdown
 exit

interface GigabitEthernet0/1
 ip address 192.168.14.1 255.255.255.0
 no shutdown
 exit

ip dhcp excluded-address 192.168.14.1
ip dhcp pool LAN_R15
 network 192.168.14.0 255.255.255.0
 default-router 192.168.14.1
 dns-server 8.8.8.8
 exit

router ospf 1
 router-id 15.15.15.15
 network 10.0.0.52 0.0.0.3 area 0
 network 192.168.14.0 0.0.0.255 area 0
 exit

end
write memory
```

---

## 🔌 Configuración de switches

Los switches SW2–SW15 son switches de acceso simples (sin VLANs adicionales):

```cisco
enable
configure terminal
hostname SW2
! (cambiar el número según corresponda)
end
write memory
```

> Los puertos de los switches accederán automáticamente en modo acceso sin configuración adicional en este caso, ya que solo tienen una VLAN por defecto.

---

## 💻 Configuración de PCs

Cada PC puede obtener IP automáticamente por DHCP:

1. Haz clic en el PC
2. Ve a la pestaña **Desktop**
3. Abre **IP Configuration**
4. Selecciona **DHCP**
5. El PC obtendrá automáticamente: IP, máscara, gateway y DNS

**Alternativamente, configuración manual** (ejemplo para PC1):

| Campo | Valor |
|-------|-------|
| IP Address | 192.168.168.2 |
| Subnet Mask | 255.255.255.0 |
| Default Gateway | 192.168.168.1 |
| DNS Server | 8.8.8.8 |

---

## ✅ Verificación de conectividad

### 1. Verificar OSPF en cualquier router

En la CLI de cualquier router:

```cisco
show ip ospf neighbor
show ip route
show ip ospf database
```

Deberías ver **14 vecinos OSPF** en los routers intermedios y las rutas aprendidas dinámicamente.

### 2. Verificar ping entre extremos

Desde PC1 (VLAN 10 de R1), abre Desktop → Command Prompt:

```
ping 192.168.14.4
```

Un resultado exitoso confirma que OSPF está propagando rutas por toda la cadena.

### 3. Verificar Router on a Stick

En R1:

```cisco
show ip interface brief
```

Deberías ver las subinterfaces `GigabitEthernet0/1.10` y `GigabitEthernet0/1.20` en estado **up/up**.

### 4. Verificar VLANs en SW1

```cisco
show vlan brief
show interfaces trunk
```

### 5. Verificar DHCP

En cualquier router con pool DHCP:

```cisco
show ip dhcp binding
show ip dhcp pool
```

---

## 🛠️ Solución de problemas comunes

### Las interfaces no suben (estado down/down)

```cisco
! Verificar estado
show ip interface brief

! Forzar activación
interface GigabitEthernet0/0
 no shutdown
```

### OSPF no establece vecindades

Causas comunes:
- Las IPs de los enlaces no están en la misma subred `/30`
- El comando `network` tiene wildcard incorrecto
- Las interfaces están apagadas

```cisco
! Verificar configuración OSPF
show ip ospf neighbor
show running-config | section ospf
```

### Router on a Stick no funciona

- Verificar que el puerto del switch hacia R1 esté en modo **trunk**
- Verificar que la encapsulación en la subinterfaz coincida con la VLAN del switch

```cisco
! En el switch
show interfaces trunk
show vlan brief

! En R1
show interfaces GigabitEthernet0/1.10
show interfaces GigabitEthernet0/1.20
```

### Los PCs no obtienen IP por DHCP

- Verificar que el pool DHCP esté configurado en el router correcto
- Verificar que la IP del gateway esté excluida del pool
- Verificar conectividad física (cables)

```cisco
show ip dhcp pool
show ip dhcp binding
debug ip dhcp server events
```

---

## 📚 Conceptos clave explicados

### ¿Qué es OSPF?
**Open Shortest Path First** es un protocolo de enrutamiento dinámico de estado de enlace. Permite que los routers aprendan automáticamente las rutas hacia todas las redes sin configurarlas manualmente. Todos los routers en la misma "área" comparten una base de datos topológica completa.

### ¿Qué es VLSM?
**Variable Length Subnet Masking** permite dividir un bloque de IPs en subredes de diferente tamaño según las necesidades reales. En vez de despilfarrar una subred /24 para un enlace de 2 hosts, se usa una /30.

### ¿Qué es Router on a Stick?
Técnica que permite a **un solo router** enrutar entre múltiples VLANs usando **una sola interfaz física** dividida en subinterfaces virtuales. Cada subinterfaz maneja una VLAN con encapsulación 802.1Q (dot1Q).

---

## 📁 Archivos incluidos en este repositorio

```
📦 red-ospf-vlsm-packet-tracer/
├── 📄 README.md              ← Esta guía
├── 📄 topology.pkt           ← Archivo Packet Tracer listo para abrir
├── 📁 configs/
│   ├── R1_router_on_stick.txt
│   ├── R2_config.txt
│   ├── ...
│   └── R15_config.txt
└── 📁 docs/
    └── vlsm_tabla.md
```

---

## 🤝 Contribuciones

Si encuentras errores o tienes mejoras, abre un **Issue** o envía un **Pull Request**.

---

## 📄 Licencia

Este proyecto es de libre uso educativo. Puedes copiarlo, modificarlo y distribuirlo libremente.

---


