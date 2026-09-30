# Taller de APIs y Postman

**Estudiante:** Santiago Arcos Velasco  
**Documento:** 1115242020  
**Asignatura:** Ingeniería de Software II — Cotecnova  

## Marco conceptual

Una API REST (Representational State Transfer) es un estilo de arquitectura de software para sistemas distribuidos basado en el protocolo HTTP, donde el cliente y el servidor se comunican intercambiando representaciones de datos (comúnmente en formato JSON) de forma desacoplada y sin mantener estado entre peticiones (*stateless*). Un **recurso** es cualquier información o entidad expuesta por el sistema (como un usuario, producto o publicación), mientras que un **endpoint** es la URL específica a través de la cual el cliente accede a dicho recurso para operar sobre él (por ejemplo, `/posts/1`). Un ejemplo cotidiano es Spotify: la interfaz en el teléfono no consulta directamente la base de datos central; envía peticiones HTTP a los endpoints del servidor para obtener los datos de canciones y listas en formato JSON y representarlos en pantalla.

*Fuente consultada:* [MDN Web Docs - Definición de API REST](https://developer.mozilla.org/es/docs/Glossary/REST)

## Métodos HTTP

| Método | Operación CRUD | Qué hace |
| :--- | :--- | :--- |
| **GET** | Read (Lectura) | Solicita la representación de un recurso existente sin alterar el estado del servidor (operación segura y de solo lectura). |
| **POST** | Create (Creación) | Envía datos en el cuerpo de la petición al servidor para crear un nuevo recurso dependiente del endpoint especificado. |
| **PUT** | Update (Actualización total) | Reemplaza íntegramente el recurso de destino con la representación enviada en el cuerpo de la petición. Si no existe, puede crearlo según la API. |
| **PATCH** | Update (Actualización parcial) | Aplica modificaciones parciales a un recurso existente, modificando únicamente los campos enviados sin sobrescribir el resto. |
| **DELETE** | Delete (Eliminación) | Solicita al servidor la eliminación del recurso especificado en el endpoint. |

*Fuente consultada:* [RFC 9110 - HTTP Semantics: Method Definitions](https://www.rfc-editor.org/rfc/rfc9110.html#section-9.3)

## Códigos de estado

Los códigos de estado HTTP son respuestas numéricas estandarizadas emitidas por el servidor para indicar el resultado de una petición cliente. Se dividen en cinco familias:

* **1xx (Informativos):** Indican que la petición fue recibida y el proceso continúa.  
  *Ejemplo:* `101 Switching Protocols` (solicitud de cambio a protocolo WebSocket).
* **2xx (Éxito):** Confirman que la petición fue recibida, entendida y procesada satisfactoriamente.  
  *Ejemplo:* `200 OK` (petición exitosa estándar) o `201 Created` (recurso creado con éxito).
* **3xx (Redirección):** Indican que el cliente debe tomar medidas adicionales para completar la solicitud, generalmente cambiar de URL.  
  *Ejemplo:* `301 Moved Permanently` (el recurso cambió de dirección de forma definitiva).
* **4xx (Errores del cliente):** Indican que la solicitud contiene un error atribuible a quien la envía (sintaxis inválida, falta de autenticación o recurso inexistente).  
  *Ejemplo:* `404 Not Found` (el endpoint o ID solicitado no existe) o `400 Bad Request` (payload malformado).
* **5xx (Errores del servidor):** Indican que el servidor falló al intentar procesar una petición aparentemente válida por un problema interno.  
  *Ejemplo:* `500 Internal Server Error` (fallo de ejecución o excepción no controlada en el backend).

### Distinción técnica y de responsabilidad entre 4xx y 5xx

La separación entre estas dos familias se basa estrictamente en la **atribución de la falla**:

* **Errores 4xx (Responsabilidad del cliente):** La causa raíz está en la petición. El cliente envió parámetros incorrectos, un JSON inválido, no incluyó credenciales de acceso o apuntó a una ruta que no existe. El servidor operó correctamente al rechazarla; el cliente debe modificar la petición para que tenga éxito.
* **Errores 5xx (Responsabilidad del servidor):** La petición del cliente cumplió con el contrato de la API, pero el servidor no pudo procesarla debido a caídas de bases de datos, fallos en la infraestructura, errores no capturados en el código o tiempos de espera agotados en servicios internos. El cliente no puede corregir el problema modificando su petición; la solución requiere intervención en el backend.

*Fuente consultada:* [RFC 9110 - HTTP Semantics: Status Codes](https://www.rfc-editor.org/rfc/rfc9110.html#section-15)