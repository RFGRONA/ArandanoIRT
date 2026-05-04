# Guía de Configuración de Alertas en Grafana para ArandanoIRT

  * **Versión del Documento:** 1.0
  * **Fecha:** 2025-07-20

**Objetivo:** Este documento detalla el procedimiento técnico para configurar el sistema de alertas de Grafana, con el fin de integrarlo con el webhook de la API de ArandanoIRT para la notificación de fallos de aplicación y de dispositivos.

-----

## 1\. Configuración del Punto de Contacto (Contact Point)

El primer paso consiste en definir el destino de las notificaciones, que en este caso es un endpoint de tipo webhook expuesto por la API de la aplicación.

1.  Navegar a la sección **Alerting** en el menú principal de Grafana.
2.  Seleccionar la pestaña **Contact points**.
3.  Hacer clic en **New contact point**.
4.  Configurar los siguientes parámetros:
      * **Name**: `API ArandanoIRT`
      * **Type**: `Webhook`
      * **URL**: `https://[dominio-de-produccion]/api/alerts/grafana-webhook`. Se debe reemplazar `[dominio-de-produccion]` por el nombre de dominio público del sistema.
5.  **Configurar la Autenticación mediante Clave de API:**
      * En la sección **Optional Webhook settings**, localizar el apartado **HTTP Headers**.
      * Hacer clic en **Add header** y configurar:
          * **Key**: `X-Api-Key`
          * **Value**: Introducir el valor de la clave secreta definida en el archivo de configuración `appsettings.json` de la aplicación.
6.  **Definir la Plantilla del Payload JSON:**
      * Para asegurar la compatibilidad del payload con el DTO (Data Transfer Object) esperado por la API, es necesario definir una plantilla personalizada.
      * En la sección **Message Template**, seleccionar **"Edit template"** y reemplazar el contenido con el siguiente bloque JSON:
    <!-- end list -->
    ```json
    {
      "alertName": "{{ .CommonLabels.alertname }}",
      "message": "{{ .CommonAnnotations.summary }}",
      "level": "{{ .Status }}",
      "commonLabels": {
        "alert_type": "{{ .CommonLabels.alert_type }}",
        "severity": "{{ .CommonLabels.severity }}"
      }
    }
    ```
7.  **Validar y Guardar:**
      * Utilizar la opción **Test** para enviar una notificación de prueba y verificar su correcta recepción en los logs de la API.
      * Hacer clic en **Save contact point** para persistir la configuración.

-----

## 2\. Creación de Reglas de Alerta (Alert Rules)

A continuación, se definen las condiciones que activarán las alertas, basadas en consultas LogQL sobre los logs de la aplicación recolectados por Loki.

### 2.1. Regla de Alerta para Fallos de Aplicación

Esta regla se activa al detectar la presencia de logs con nivel `Fatal`.

1.  Navegar a la sección **Alerting** -\> **Alert rules**.
2.  Hacer clic en **New alert rule**.
3.  **Configurar la regla:**
      * **Rule name**: `Fallo Crítico de Aplicación`
      * **Data source**: Seleccionar la fuente de datos correspondiente a **Loki**.
      * **Query**: Utilizando el modo **Code**, introducir la siguiente consulta LogQL, la cual cuenta el número de logs fatales en un intervalo de un minuto:
        ```logql
        count_over_time({container="arandano-app"} | json | Level=~"Fatal" [1m])
        ```
4.  **Establecer la condición de activación:**
      * La condición de la alerta debe configurarse para que se active si el resultado de la consulta es superior a cero.
5.  **Configurar Detalles y Etiquetas:**
      * Expandir la sección **Add details for your alert**.
      * **Folder**: Asignar la regla a una carpeta designada (ej. `ArandanoIRT`).
      * **Summary (Annotation)**: `Se ha detectado un error fatal en la aplicación.`
      * **Labels**: Añadir las siguientes etiquetas personalizadas, las cuales son utilizadas por la API para el enrutamiento y procesamiento de la alerta:
          * **Key**: `alert_type`, **Value**: `application_failure`
          * **Key**: `severity`, **Value**: `critical`
6.  **Guardar la regla:** Hacer clic en **Save rule**.

### 2.2. Regla de Alerta para Fallos de Dispositivo

Esta regla se activa al detectar logs de nivel `error` asociados a la comunicación con dispositivos.

1.  Crear una **New alert rule**.
2.  **Configurar la regla:**
      * **Rule name**: `Fallo de Dispositivo`
      * **Data source**: **Loki**.
      * **Query**: Introducir la siguiente consulta LogQL, que cuenta errores que contienen un identificador de dispositivo en un intervalo de cinco minutos:
        ```logql
        sum(count_over_time({container="arandano-app"} |= "Log desde DeviceId" |= "level=error" [5m]))
        ```
3.  **Establecer la condición de activación:**
      * La condición debe activarse si el resultado de la consulta es superior a cero.
4.  **Configurar Detalles y Etiquetas:**
      * **Folder**: `ArandanoIRT`.
      * **Summary (Annotation)**: `Se ha detectado un error en la comunicación con un dispositivo.`
      * **Labels**:
          * **Key**: `alert_type`, **Value**: `device_failure`
          * **Key**: `severity`, **Value**: `critical`
5.  **Guardar la regla:** Hacer clic en **Save rule**.

-----

## 3\. Configuración de Políticas de Notificación

El paso final es configurar las políticas que enrutan las alertas activadas hacia el punto de contacto definido anteriormente.

1.  Navegar a la sección **Alerting** -\> **Notification policies**.
2.  Crear una nueva política específica haciendo clic en **New specific policy** o en caso de que no se pueda, editar la politica por defecto.
3.  **Configurar la política:**
      * **Matching labels**: Establecer las condiciones para que esta política se aplique a las alertas deseadas. Se deben añadir dos condiciones con el operador `OR`:
          * `alert_type` `=` `application_failure`
          * `alert_type` `=` `device_failure`
      * **Contact point**: Seleccionar el punto de contacto `API ArandanoIRT` de la lista desplegable.
4.  **Configurar la Temporización de Notificaciones:**
      * Ajustar los siguientes parámetros de tiempo para delegar la lógica de agrupación a la API:

| Parámetro | Valor Recomendado | Descripción Técnica |
| :--- | :--- | :--- |
| **Group wait** | `10s` (10 segundos) | Minimiza la latencia en el envío de notificaciones individuales, permitiendo que la API gestione la lógica de agrupación. |
| **Group interval**| `1m` (1 minuto) | Define el intervalo para el envío de notificaciones subsecuentes si se generan nuevas instancias de alerta dentro del `Group wait`. |
| **Repeat interval**| `1h` (1 hora) | Establece el intervalo para reenviar una notificación si una alerta permanece en estado activo. Este comportamiento es compatible con la lógica de agrupación de la API. |

5.  **Guardar la política:** Hacer clic en **Save policy**.