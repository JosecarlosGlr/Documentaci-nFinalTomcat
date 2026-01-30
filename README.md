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
```
---

## 4. Seguridad Aplicada

Para garantizar la integridad del servidor y de los datos, se han implementado tres capas de seguridad fundamentales:

* **Gestión de Accesos:** Se han definido roles específicos (`manager-gui` y `admin-gui`) en `tomcat-users.xml`, restringiendo el acceso a las consolas de administración solo a usuarios autorizados con contraseñas seguras.
* **Cifrado de Comunicaciones (HTTPS):** Se ha configurado un conector SSL en el puerto **8443**. Para ello, se generó un almacén de claves (*keystore*) con la herramienta `keytool` y se vinculó en el `server.xml`.
* **Restricciones de Red:** Gracias a la integración con Apache, Tomcat no necesita estar expuesto directamente a internet, actuando Apache como una barrera de seguridad adicional.

---

## 5. Pruebas de Rendimiento y Optimización

Se evaluó la capacidad de respuesta del servidor bajo condiciones de estrés utilizando la herramienta **ApacheBench (ab)**.

**Proceso de optimización:**
1.  **Línea base:** Se lanzaron 2000 peticiones concurrentes para medir los tiempos de respuesta iniciales.
2.  **Ajuste de hilos:** Se modificó el `server.xml` aumentando el `maxThreads` (hilos máximos) y configurando `maxConnections` para soportar picos de tráfico.
3.  **Resultado:** Los ajustes permiten que el servidor gestione las colas de peticiones de forma más estable, evitando caídas del servicio ante una alta demanda de usuarios.

---

## 6. Recomendaciones de Administración

Para mantener un entorno de Tomcat saludable, se recomienda seguir estas pautas:

* **Monitorización:** Revisar periódicamente el archivo `logs/catalina.out` para detectar errores de despliegue o fugas de memoria.
* **Limpieza de Aplicaciones:** Utilizar el **Manager App** para replegar aplicaciones que ya no estén en uso, liberando así memoria RAM del servidor.
* **Actualizaciones:** Mantener siempre Tomcat actualizado a la última versión estable (actualmente 11.0.14) para corregir vulnerabilidades de seguridad conocidas.

---

## 7. Despliegue en Contenedores (Docker)

Como fase final, se ha migrado la infraestructura a un modelo basado en contenedores. 

He configurado Tomcat en un contenedor Docker para facilitar el despliegue y la portabilidad de mis aplicaciones. Esto me ha permitido comprobar cómo el aislamiento de contenedores simplifica la gestión frente a una instalación tradicional, permitiendo levantar entornos idénticos en cuestión de segundos mediante el comando `docker pull tomcat` y el montaje de volúmenes para las aplicaciones.

---
