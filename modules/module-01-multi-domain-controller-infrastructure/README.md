# Module 01 - Multi Domain Controller Infrastructure

## Objetivo

Implementar una infraestructura Active Directory con múltiples Domain Controllers para reducir el riesgo asociado a un Single Point of Failure (SPoF), mejorar la disponibilidad de los servicios de identidad y establecer una base sólida para futuras capacidades de administración, auditoría y hardening.

---

## Riesgo Identificado

La infraestructura actual depende de un único Domain Controller.

La indisponibilidad de este servidor puede afectar:

* Autenticación de usuarios.
* Resolución de nombres mediante DNS.
* Disponibilidad de Active Directory.
* Administración centralizada de identidades.
* Aplicación de políticas de dominio.

La incorporación de un Domain Controller adicional permitirá introducir mecanismos de redundancia y replicación para reducir este riesgo.

---

## Estado Inicial

### Situación Inicial

La organización dispone actualmente de una infraestructura Active Directory operativa basada en un único Domain Controller.

#### Infraestructura Existente

| Componente   | Valor                                         |
| ------------ | --------------------------------------------- |
| Hostname     | gabriel-dc01                                  |
| Dominio      | gabriel.local                                 |
| Dirección IP | 192.168.10.10                                 |
| Servicios    | Active Directory Domain Services (AD DS), DNS |

Antes de realizar modificaciones sobre la arquitectura, se efectuará una validación del estado de salud de la infraestructura existente.

---

## Architecture & Design

### Business Context

La organización simulada para este laboratorio representa una empresa mediana con múltiples áreas operativas que requieren una administración centralizada de identidades, autenticación y control de acceso a recursos corporativos.

A medida que la organización crece, la administración manual de usuarios, grupos y permisos se vuelve más compleja, incrementando el riesgo de errores operativos, inconsistencias de configuración y dificultades para aplicar controles de seguridad de forma uniforme.

Para resolver estos desafíos, se utiliza Active Directory Domain Services (AD DS) como plataforma central de identidad.

Debido a que los servicios de directorio son componentes críticos para las operaciones diarias de la organización, la infraestructura deberá evolucionar hacia un modelo que reduzca la dependencia de un único servidor.

---

### Technical Requirements

La infraestructura deberá proporcionar las siguientes capacidades:

#### Centralized Authentication

Permitir la autenticación centralizada de usuarios mediante Active Directory.

#### Centralized Authorization

Administrar permisos y acceso a recursos desde una ubicación centralizada.

#### DNS Integration

Integrar los servicios DNS con Active Directory para garantizar la correcta resolución de nombres.

#### Fault Tolerance

Reducir el impacto operativo ante la falla de un único Domain Controller.

#### Active Directory Replication

Mantener la consistencia de la información mediante replicación entre controladores de dominio.

#### Administrative Scalability

Proporcionar una base adecuada para futuras tareas de administración, delegación, auditoría y hardening.

---

### Forest Design

La infraestructura mantiene un modelo Single Forest y Single Domain.

#### Justificación Técnica

* Menor complejidad administrativa.
* Administración centralizada de identidades.
* Menor consumo de recursos asociados a replicación.
* Escalabilidad mediante Organizational Units (OUs).

---

### Naming Strategy

La infraestructura existente utiliza actualmente el dominio:

**gabriel.local**

#### Consideraciones

Si bien Microsoft recomienda actualmente el uso de dominios basados en nombres válidos y registrables, el objetivo de este laboratorio consiste en trabajar sobre una infraestructura ya desplegada y comprender escenarios habituales de administración, mantenimiento y expansión de Active Directory.

Por este motivo, el dominio existente será conservado durante el desarrollo del laboratorio.

---

### Domain Controller Strategy

La arquitectura evolucionará desde un único Domain Controller hacia una infraestructura compuesta por dos controladores de dominio.

#### DC01

Servidor actualmente operativo.

Aloja Active Directory y DNS.

Actúa como controlador principal de la infraestructura existente.

#### DC02

Será incorporado como controlador de dominio adicional.

Su función será proporcionar redundancia, replicación y continuidad operativa ante la indisponibilidad de DC01.

#### Justificación

La incorporación de un segundo controlador de dominio elimina el Single Point of Failure existente y permite validar mecanismos reales de replicación, disponibilidad y recuperación.

---

### Network Architecture

La infraestructura opera dentro del segmento:

`192.168.10.0/24`

Todos los servidores críticos utilizan direccionamiento IP estático.

#### Infrastructure Hosts

| Hostname     | IP Address    | Estado      |
| ------------ | ------------- | ----------- |
| gabriel-dc01 | 192.168.10.10 | Existente   |
| DC02         | 192.168.10.20 | Planificado |

#### Justificación

La utilización de direcciones IP estáticas garantiza estabilidad operativa para Active Directory, DNS y los mecanismos de replicación.

---

### DNS Strategy

Los servicios DNS se encuentran integrados con Active Directory.

La resolución de nombres constituye un componente fundamental para:

* Autenticación.
* Localización de Domain Controllers.
* Replicación.
* Aplicación de políticas de dominio.

Durante la implementación del segundo Domain Controller se validará la replicación de las zonas DNS integradas en Active Directory.

---

## Implementación

### Fase 1 - Health Assessment de la Infraestructura Existente

Antes de incorporar un nuevo Domain Controller se realizará una evaluación del estado actual de Active Directory.

#### Objetivos

* Verificar el estado operativo del Domain Controller existente.
* Validar el funcionamiento de DNS.
* Confirmar la disponibilidad de Active Directory.
* Detectar posibles errores antes de introducir replicación.

#### Herramientas Utilizadas

```powershell
dcdiag /v
dcdiag /test:dns /v
```

---

### Validaciones Ejecutadas
Se realizó una evaluación del estado operativo del Domain Controller existente utilizando herramientas de diagnóstico nativas de Active Directory.
```powershell
dcdiag
dcdiag /test:dns
```

### Resultados Obtenidos

| Validación     | Resultado |
| -------------- | --------- |
| Advertising    | Passed    |
| SysVolCheck    | Passed    |
| NetLogons      | Passed    |
| Services       | Passed    |
| Replications   | Passed    |
| RidManager     | Passed    |
| MachineAccount | Passed    |
| DNS            | Passed    |

### Observaciones

Se identificaron advertencias relacionadas con configuraciones de seguridad LDAP y eventos históricos del sistema. No se detectaron errores críticos que afecten la operación actual de Active Directory, DNS o SYSVOL.

### Conclusión de la Validación

La infraestructura existente se encuentra operativa y apta para continuar con la incorporación de un segundo Domain Controller.


