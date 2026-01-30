# Documentación final Tomcat
---

## 1. Arquitectura Básica de Tomcat

Tomcat funciona como un contenedor de Servlets y páginas JSP. Sus componentes principales interactúan de la siguiente manera:

* **Catalina:** Es el "corazón" de Tomcat. Es el contenedor de Servlets que implementa las especificaciones de Jakarta EE. Se encarga de manejar el ciclo de vida de las aplicaciones.
* **Coyote:** Es el conector que actúa de intermediario con el exterior. Escucha las peticiones entrantes (por el puerto 8080 u otros) y se las pasa a Catalina para que las procese.
* **Jasper:** Es el motor de JSP. Su función es leer los archivos `.jsp`, traducirlos a código Java (Servlets) y compilarlos para que Catalina pueda ejecutarlos.
* **Manager y Host Manager:** Son aplicaciones web internas que permiten gestionar aplicaciones (desplegar, parar, recargar) y dominios virtuales desde una interfaz gráfica.

**Estructura de Directorios:**
* `/bin`: Scripts de arranque (`startup.sh`) y parada (`shutdown.sh`).
* `/conf`: Archivos de configuración (XML).
* `/webapps`: Donde se colocan las aplicaciones (WAR) para su despliegue.
* `/lib`: Librerías Java (.jar) compartidas por Tomcat y las aplicaciones.
* `/logs`: Registros de actividad y errores (catalina.out).

---

## 2. Configuración del Servidor y Archivos Clave

El comportamiento de Tomcat se define en archivos XML ubicados en `conf/`:

| Archivo | Función Principal |
| :--- | :--- |
| **server.xml** | Configuración global del servidor. Aquí se definen los **puertos** (8080, 8443), los **conectores** (HTTP, AJP) y los hilos de ejecución. |
| **web.xml** | Configuración por defecto para todas las aplicaciones (MIME types, tiempos de sesión, servlets comunes). |
| **tomcat-users.xml** | Base de datos de usuarios, contraseñas y roles (ej. `manager-gui`) para la autenticación. |
| **context.xml** | Configuración del contexto de las aplicaciones. Aquí se definen recursos globales o restricciones de acceso por IP (Válvulas). |

---

## 3. Integración con Servidor Web (Apache)

Se configuró Apache HTTP Server como **Proxy Inverso** para ocultar Tomcat detrás del puerto 80 estándar.

**Configuración en Apache (`/etc/apache2/sites-enabled/000-default.conf`):**
Se habilitaron los módulos `proxy` y `proxy_http` y se añadieron las directivas:
```apache
ProxyPreserveHost On
ProxyPass / http://localhost:8080/
ProxyPassReverse / http://localhost:8080/
