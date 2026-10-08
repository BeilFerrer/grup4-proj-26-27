# VLSM - Empresa de Policía

## 1. Introducción

Este documento recoge el diseño del direccionamiento IP de la red de una comisaría de policía. Se trata de una organización que maneja información sensible y necesita una red segura y disponible de forma continua, ya que servicios como la Sala de Operaciones 091 no pueden permitirse interrupciones. El direccionamiento es la base sobre la que se desplegarán después el resto de servidores y servicios del proyecto.

Se detalla la organización de los departamentos de la empresa de policía, que servirá para definir las diferentes subredes.

Departamentos:

- Jefatura
- RRHH y Administración
- Sala de Operaciones 091
- Atención al Ciudadano
- Seguridad Ciudadana (Patrullas)
- Policía Científica
- Policía Judicial (Investigación)

Además de los departamentos, se definen las subredes de **Intranet**, **Red de administración** y **DMZ**.

Cada departamento tiene su propia subred y su propia VLAN, lo que permite al router MikroTik controlar el tráfico entre ellos, proteger la información sensible y reducir los dominios de difusión. Para el cálculo se ha utilizado **VLSM**, que asigna a cada subred un prefijo ajustado al número de equipos que necesita y evita desperdiciar direcciones.

El diseño parte de una red privada (10.100.0.0/23) y de una red de DMZ (192.168.144.192/26), con VLAN del rango 3120-3159. En los apartados siguientes se detallan el cálculo de los prefijos, la tabla de subredes y la asignación de direcciones a las interfaces del router.

## 2. Direcciones IP de la red

| Red | Dirección |
|---|---|
| Red pública (DMZ) | 192.168.144.192/26 |
| Red privada | 10.100.0.0/23 |

Rango de VLANs asignado: **3120 - 3159**.


## 3. Cálculo del prefijo

| Departamento | Hosts | IPs útiles necesarias | Bloque | Prefijo | Máscara |
|---|---|---|---|---|---|
| Sala de Operaciones 091 | 60 | 60 | 64 | /26 | 255.255.255.192 |
| Atención al Ciudadano | 60 | 60 | 64 | /26 | 255.255.255.192 |
| Seguridad Ciudadana | 60 | 60 | 64 | /26 | 255.255.255.192 |
| Policía Judicial | 30 | 30 | 32 | /27 | 255.255.255.224 |
| Policía Científica | 20 | 20 | 32 | /27 | 255.255.255.224 |
| Intranet | 20 | 20 | 32 | /27 | 255.255.255.224 |
| Red de administración | 20 | 20 | 32 | /27 | 255.255.255.224 |
| DMZ | 20 | 20 | 32 | /27 | 255.255.255.224 |
| RRHH y Administración | 7 | 7 | 16 | /28 | 255.255.255.240 |
| Jefatura | 5 | 5 | 8 | /29 | 255.255.255.248 |

## 4. Tabla de subredes (VLSM)

Las subredes se asignan de mayor a menor tamaño, cada una justo a continuación de la anterior.

| Departamento / Subred | # hosts | Dirección de red | VLAN | Puerta de enlace | Dirección de difusión |
|---|---|---|---|---|---|
| Jefatura | 5 | 10.100.1.80/29 | 3120 | 10.100.1.81 | 10.100.1.87 |
| RRHH y Administración | 7 | 10.100.1.64/28 | 3121 | 10.100.1.65 | 10.100.1.79 |
| Sala de Operaciones 091 | 60 | 10.100.0.0/26 | 3122 | 10.100.0.1 | 10.100.0.63 |
| Atención al Ciudadano | 60 | 10.100.0.64/26 | 3123 | 10.100.0.65 | 10.100.0.127 |
| Seguridad Ciudadana | 60 | 10.100.0.128/26 | 3124 | 10.100.0.129 | 10.100.0.191 |
| Policía Científica | 20 | 10.100.0.224/27 | 3125 | 10.100.0.225 | 10.100.0.255 |
| Policía Judicial | 30 | 10.100.0.192/27 | 3126 | 10.100.0.193 | 10.100.0.223 |
| Intranet | 20 | 10.100.1.0/27 | 3127 | 10.100.1.1 | 10.100.1.31 |
| Red de administración | 20 | 10.100.1.32/27 | 3128 | 10.100.1.33 | 10.100.1.63 |
| DMZ | 20 | 192.168.144.192/27 | 3129 | 192.168.144.193 | 192.168.144.223 |

Espacio libre:

- Red privada: 10.100.1.88 - 10.100.1.255
- Red pública: 192.168.144.224/27

## 5. Asignación de direcciones IP al router MikroTik

Direcciones IP del router MikroTik de la red.

| Interfaz | IP/máscara | VLAN | Subred |
|---|---|---|---|
| ether1 | 192.168.144.194/27 | 3129 | DMZ |
| ether2 | 192.168.144.195/27 | 3129 | DMZ |
| ether3 | 10.100.1.81/29 | 3120 | Jefatura |
| ether4 | 10.100.1.65/28 | 3121 | RRHH y Administración |
| ether5 | 10.100.0.1/26 | 3122 | Sala de Operaciones 091 |
| ether6 | 10.100.0.65/26 | 3123 | Atención al Ciudadano |
| ether7 | 10.100.0.129/26 | 3124 | Seguridad Ciudadana |
| ether8 | 10.100.0.225/27 | 3125 | Policía Científica |
| ether9 | 10.100.0.193/27 | 3126 | Policía Judicial |
| ether10 | 10.100.1.1/27 | 3127 | Intranet |
| ether11 | 10.100.1.33/27 | 3128 | Red de administración |
