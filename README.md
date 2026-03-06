# DMVPN Fase 3 con IKEv2 y OSPF

## Video demostrativo

[![Ver video en YouTube](https://img.youtube.com/vi/IBXvo4clKMQ/maxresdefault.jpg)](https://youtu.be/IBXvo4clKMQ)

---

## I-) Descripción general
En esta práctica se implementó una VPN DMVPN Fase 3 utilizando IKEv2 para la protección IPsec del túnel y OSPF como protocolo de enrutamiento dinámico. La arquitectura utilizada corresponde a un modelo hub-and-spoke punto a multipunto, donde un router central HUB permite la conexión dinámica de dos sucursales representadas por los routers SPOKE-A y SPOKE-B.

<img width="915" height="284" alt="Screenshot 2026-03-06 020823" src="https://github.com/user-attachments/assets/f2209ef7-be84-4eb7-87fc-058106a60963" />

DMVPN Fase 3 permite optimizar la comunicación entre spokes mediante el uso de NHRP redirect en el HUB y NHRP shortcut en los spokes, facilitando que el tráfico entre sucursales se resuelva de forma más eficiente sin depender permanentemente del HUB para el reenvío.

La topología está compuesta por un router HUB, dos routers SPOKE, un router ISP que simula la red pública y una red LAN en cada sitio. Cada spoke se registra dinámicamente en el HUB a través de la red del túnel 10.201.9.0/24, mientras que las LAN locales corresponden a las redes 10.9.20.0/24, 10.9.28.0/24 y 10.9.29.0/24.

---

## II-) Configuración del túnel DMVPN

<img width="324" height="284" alt="Screenshot 2026-03-06 021309" src="https://github.com/user-attachments/assets/428b676f-5022-486c-be6e-efd266b532f5" />

El túnel se implementó mediante GRE multipoint y NHRP, utilizando la interfaz Tunnel200 en cada router. El HUB fue configurado como servidor NHRP y los spokes fueron configurados para registrar dinámicamente sus direcciones NBMA en el HUB.

Para la seguridad del túnel se empleó IPsec con IKEv2, utilizando cifrado AES-256, integridad SHA-256 y autenticación por clave precompartida. El túnel fue protegido mediante un IPsec profile aplicado directamente sobre la interfaz Tunnel200.

En el HUB se utilizó el comando ip nhrp redirect, mientras que en los spokes se configuró ip nhrp shortcut, lo cual caracteriza el comportamiento de DMVPN Fase 3.

<img width="335" height="211" alt="Screenshot 2026-03-06 021552" src="https://github.com/user-attachments/assets/7ad46b4f-0d46-4764-867a-fe8501ae243c" />

---

## III-) Enrutamiento dinámico

Para el intercambio de rutas se configuró OSPF proceso 200, permitiendo anunciar las redes LAN y la red del túnel entre el HUB y los spokes.
La interfaz Tunnel200 fue configurada como OSPF point-to-multipoint, lo que permitió formar adyacencias estables sobre el entorno multipunto de DMVPN.

Gracias a esta configuración, el HUB aprendió dinámicamente las redes 10.9.28.0/24 y 10.9.29.0/24, mientras que los spokes aprendieron la red LAN del HUB y las redes remotas correspondientes.

<img width="574" height="93" alt="Screenshot 2026-03-06 021720" src="https://github.com/user-attachments/assets/9a7c5601-327e-48ed-b491-2a8613552fb0" />

---

## IV-) Verificación del funcionamiento.

El correcto funcionamiento de la VPN se verificó mediante comandos de diagnóstico aplicados en el HUB y los spokes.

<img width="493" height="126" alt="Screenshot 2026-03-06 021743" src="https://github.com/user-attachments/assets/563c34bc-29d3-4ba8-9735-ae0646772315" />

El comando show dmvpn permitió confirmar que el HUB registró correctamente a ambos spokes mediante NHRP.
El comando show ip nhrp mostró los registros dinámicos asociados a las direcciones NBMA y a las direcciones de túnel.

<img width="338" height="155" alt="Screenshot 2026-03-06 021806" src="https://github.com/user-attachments/assets/bcf5ac57-f638-433d-bc17-a0c3d390a0f5" />

La negociación de seguridad fue validada con show crypto ikev2 sa, mostrando el estado READY, lo cual confirma que el túnel IPsec se estableció correctamente.

El enrutamiento dinámico se verificó con show ip ospf neighbor, donde el HUB presentó dos adyacencias en estado FULL, una con cada spoke.

Finalmente, la tabla de enrutamiento validó que las redes remotas fueron aprendidas dinámicamente a través de OSPF.

---

## V-) Pruebas de conectividad

Se realizaron pruebas de conectividad entre las redes LAN para validar el funcionamiento integral de la arquitectura.

<img width="413" height="167" alt="Screenshot 2026-03-06 022124" src="https://github.com/user-attachments/assets/15a36081-7427-46f0-a09e-7df4465dfb90" />

Desde VPC-A se comprobó conectividad hacia la red LAN del HUB.
Desde PC-B se validó conectividad hacia la red LAN de SPOKE-A, demostrando comunicación entre sucursales.
Desde VPC-HUB se verificó el alcance hacia las redes LAN de ambos spokes.

<img width="415" height="270" alt="Screenshot 2026-03-06 022254" src="https://github.com/user-attachments/assets/9bbfba86-939b-4459-844f-7901d261a39f" />

Estas pruebas confirmaron el correcto funcionamiento del túnel DMVPN, la protección IPsec con IKEv2 y el intercambio dinámico de rutas mediante OSPF.

---

## VI-) Conclusión

En esta práctica se implementó correctamente una VPN DMVPN Fase 3 con IKEv2 y OSPF.
El HUB permitió el registro dinámico de los spokes mediante NHRP, IKEv2 estableció la protección segura del túnel IPsec y OSPF permitió el aprendizaje automático de rutas entre todas las redes LAN de la topología.

Las pruebas de conectividad confirmaron que la comunicación entre el HUB y las sucursales, así como entre los propios spokes, se realizó exitosamente a través de la infraestructura DMVPN.
