# Informe de auditoría de red Wi-Fi insegura

## Introducción

Este informe analiza qué información puede quedar expuesta al abrir una página HTTP desde una red Wi-Fi pública. La práctica se limita a inspeccionar la solicitud del propio navegador y a evaluar cómo reducir los riesgos.

## Sitio analizado

La consigna de exploración indica `http://neverssl.com`. El esquema de esa URL es HTTP. Si la solicitud se establece mediante HTTP, el navegador y el servidor intercambian la solicitud y la respuesta sin cifrado TLS. HTTPS usa TLS para cifrar y proteger la integridad de ese intercambio y para que el navegador pueda verificar la identidad del servidor.

## Evidencia observada

Abrí la pestaña Red de Chrome, desactivé la caché y registré la solicitud al documento final `online/`. Repetí la navegación con la VPN de McAfee LiveSafe activa, con Argentina como ubicación virtual. Desactivar la caché permitió superar la primera observación, que mostraba una respuesta guardada y encabezados provisionales.

| Campo | Sin VPN | Con VPN |
| --- | --- | --- |
| URL final | `http://funlushclearsecret.neverssl.com/online/` | `http://wonderfultranscendentsublimebirds.neverssl.com/online/` |
| Método, protocolo y estado | GET · HTTP/1.1 · 200 OK | GET · HTTP/1.1 · 200 OK |
| Encabezados | Host, Referer, Accept-Language, Cache-Control y User-Agent | Los mismos campos. Cambió el valor de Host junto con el subdominio. |

En ambas pruebas, `Referer` fue `http://neverssl.com/` y `Accept-Language` fue `es-ES,es;q=0.9`. El `User-Agent` mostró el perfil móvil emulado. El cambio de subdominio se observó durante la navegación. Las capturas de Chrome muestran la solicitud que procesó el navegador y el estado HTTP recibido. La protección del tramo local se explica por el cifrado del túnel.

- Sin VPN: [solicitud](sin-vpn-general.png) y [encabezados](sin-vpn-headers.png).
- Con VPN: [estado de McAfee](vpn-activa.png), [solicitud](con-vpn-general.png) y [encabezados](con-vpn-headers.png).

## Riesgos encontrados

- **Intercepción de tráfico (sniffing).** Quien pueda observar el tráfico HTTP puede leer el host, la ruta, los parámetros de la URL y los encabezados enviados. Si la solicitud contiene credenciales, cookies de sesión o datos de un formulario, también pueden quedar expuestos.
- **Ataque de intermediario (MITM).** Si un tercero se sitúa en la ruta de la conexión, puede leer y modificar solicitudes o respuestas HTTP. Podría, por ejemplo, alterar los enlaces que recibe el navegador.
- **Red gemela maligna (evil twin).** Una red que imita el nombre de un punto de acceso legítimo puede atraer al usuario. Si se conecta, el operador de esa red puede observar o modificar el tráfico HTTP que pase por ella.

## Cómo ayuda una VPN

Una VPN encapsula y cifra el tráfico que enruta a través de un túnel entre el dispositivo y el servidor VPN. En la red Wi-Fi local, un observador puede ver la conexión hacia el servidor VPN y metadatos como el volumen y el momento del tráfico. El cifrado del túnel protege los datos que viajan dentro de él en ese tramo.

El túnel termina en el servidor VPN. Si el sitio usa HTTP, el tráfico que sale de ese servidor hacia el sitio puede seguir expuesto. Usar HTTPS protege la comunicación entre el navegador y el sitio incluso después de que el tráfico sale del túnel.

## 3 Reglas de Oro

1. Confirmar con el establecimiento el nombre de la red y desactivar la conexión automática a redes públicas.
2. Usar HTTPS y respetar las advertencias de certificados. Reservar las operaciones sensibles para una conexión de confianza, como los datos móviles.
3. Activar una VPN de confianza al usar una Wi-Fi pública y comprobar que el túnel está conectado antes de navegar.
