# Bitácora de Pruebas y Hallazgos Técnicos

**Estudiante:** Santiago Arcos Velasco  
**Taller:** Pruebas de API REST con Postman y JSONPlaceholder  

---

## Tabla comparativa de peticiones

| # | Petición | Método HTTP | Endpoint / URL | Código Esperado | Código Obtenido | ¿Coincide? |
| :- | :--- | :--- | :--- | :--- | :--- | :--- |
| **1** | Leer recurso individual | `GET` | `https://jsonplaceholder.typicode.com/posts/1` | `200 OK` | `200 OK` | Sí |
| **2** | Leer colección completa | `GET` | `https://jsonplaceholder.typicode.com/posts` | `200 OK` | `200 OK` | Sí |
| **3** | Consultar recurso inexistente | `GET` | `https://jsonplaceholder.typicode.com/posts/9999` | `404 Not Found` | `404 Not Found` | Sí |
| **4** | Crear nuevo recurso | `POST` | `https://jsonplaceholder.typicode.com/posts` | `201 Created` | `201 Created` | Sí |
| **5** | Actualizar recurso existente | `PUT` | `https://jsonplaceholder.typicode.com/posts/1` | `200 OK` | `200 OK` | Sí |
| **6** | Eliminar recurso | `DELETE` | `https://jsonplaceholder.typicode.com/posts/1` | `200 OK` | `200 OK` | Sí |

---

## Análisis técnico de resultados

### 1. Lectura individual (`GET /posts/1`) frente a lectura masiva (`GET /posts`)
* **Estructura de datos:** La consulta individual retorna un objeto JSON delimitado por llaves `{}` con cuatro atributos (`userId`, `id`, `title`, `body`). La consulta a la colección completa retorna un arreglo delimitado por corchetes `[]` que contiene una lista de 100 objetos.
* **Carga de red:** La respuesta individual consumió ~1.27 KB, mientras que la lista completa transfirió ~7.98 KB, evidenciando el impacto del volumen de datos en el ancho de banda y la necesidad de paginación en entornos de producción.

### 2. Comportamiento ante fallos del cliente (`GET /posts/9999`)
* **Código devuelto:** `404 Not Found`.
* **Cuerpo de respuesta:** Objeto JSON vacío `{}`.
* **Diagnóstico de responsabilidad:** El error pertenece estrictamente a la familia `4xx` (responsabilidad del cliente). La sintaxis de la petición y la conexión con el servidor fueron correctas, pero el identificador `9999` no existe en la base de datos. El servidor actuó según el estándar HTTP al rechazar la solicitud sin comprometer la integridad del backend.

### 3. Creación y asignación de identificadores (`POST /posts`)
* **Código devuelto:** `201 Created`.
* **Cuerpo enviado vs obtenido:** Se envió un payload con `title`, `body` y `userId: 1`. El backend procesó el ingreso y retornó el mismo objeto adjuntando la clave autoincremental `"id": 101`.
* **Persistencia simulada:** Al tratarse de una API pública de pruebas (*mock API*), el recurso no se guarda de forma permanente en una base de datos real, pero el contrato de respuesta simula el ciclo de vida completo de creación en un entorno RESTful.

### 4. Reemplazo integral mediante `PUT` (`PUT /posts/1`)
* **Código devuelto:** `200 OK`.
* **Efecto de la operación:** A diferencia de `PATCH` (que altera atributos aislados), el método `PUT` sobreescribe el recurso en su totalidad con la representación enviada en el cuerpo de la petición, confirmando la actualización con el eco de los datos modificados.

### 5. Eliminación de recursos mediante `DELETE` (`DELETE /posts/1`)
* **Código devuelto:** `200 OK`.
* **Comportamiento:** El servidor procesa la solicitud de remoción del registro y responde con un cuerpo vacío `{}`, validando que la entidad identificada ha sido eliminada del ciclo de vida del sistema sin necesidad de retornar datos adicionales en el payload.