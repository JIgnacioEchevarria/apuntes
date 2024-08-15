# Apuntes
En este repositorio voy a tener todos los apuntes de diferentes tecnologías estudiadas

## Temas
- [Nodejs](#nodejs)
- [PostgreSQL](#postgresql)
- [Typescript](#typescript)

# NodeJS

## Autenticación

### Tokens
Para autenticar un usuario utilizar Json Web Tokens, y luego guardarlo en cookies. Las cookies son menos vulnerables a inyecciones maliciosas, ya que solo son accesibles por medio de http, no se pueden acceder a ellas a través de javascript. Además se puede configurar para que las cookies se envien solo con https. Lo que sí puedo devolver en la respuesta y guardar en local storage es la información del usuario que no sea sensible, como email, nombre de usuario, id, etc. No devolver contraseñas.
Los JWT se dividen en 3 partes:

- **Header**: Indica algoritmo y el tipo del token
- **Payload**: Contiene los datos codificados.
- **Signature**: Permite decodificar la información del payload.

![JwtGraphic](./assets/jwt-graphic.PNG)

### Cookies
Las cookies sirven para guardar información, la ventaja que tienen es que no pueden ser accedidas con código javascript malicioso, lo que le da más seguridad a una aplicación. La única manera de que otra persona robe información de las cookies es estando con nosotros en nuestra computadora.
Siempre un tercero, con el tiempo y las ganas suficientes, podrá desencriptar nuestra información, lo importante es que la misma ya no disponga de valor cuando éste logre hacerlo. Por eso a los token hay que darle un tiempo de expiración.

### Passwords:
Las contraseñas deben ser hasheadas antes de guardarlas en la base de datos. Ej: Bcrypt.

### Datos:
Validar los datos de entrada para que no te metan cualquier cosa en la base de datos.

### Errores:
Evitar devolver mensajes con demasiada información, porque es muy accesible para los atacantes. Crear errores propios extendiendo de la clase Error.

![ErrorInstance](./assets/error-instance.PNG)

## Cors
Los cors son aplicados en los servidores para las solicitudes http de dominio cruzado que son iniciadas por el navegador, si el server tiene determinado dominio y el cliente(navegador) tiene otro dominio distinto, debemos indicarle al servidor que acepte dicho dominio para poder realizar solicitudes http desde el mismo. En la configuración de los orígenes de cors se pueden aceptar todos los dominios o indicar específicamente cuales queremos que estén permitidos.

![CorsMiddleware](./assets/cors-middleware.PNG)

Como se ve en el ejemplo, estoy aceptando los dominios http://localhost:5173 y http://www.turns.com.

## Documentación de API'S
Siempre ir documentando los endpoints mientras se codea, así después evitar errores. Para documentar apis utilizar swagger,  un conjunto de herramientas de software de código abierto para diseñar, construir, documentar, y utilizar servicios web RESTful. 
Se utiliza de la siguiente manera:

**Se documenta arriba del endpoint**

### Get
```javascript
/**
   * @swagger
   * components:
   *  securitySchemes:
   *    cookieAuth:
   *      type: apiKey
   *      in: cookie
   *      name: access_token
   *  schemas:
   *    Service:
   *      type: object
   *      properties:
   *        id:
   *          type: string
   *          format: uuid
   *        name:
   *          type: string
   *        duration:
   *          type: integer
   *          minimum: 1
   *          decription: duration of service in minutes
   *        price:
   *          type: integer
   *          minimum: 1
   *        is_active:
   *          type: boolean
   *          default: true
   *      required:
   *        - name
   *        - duration
   *        - price
   *      example:
   *        id: "545d029b-0b2c-4099-9b91-c3a9dc6b1231"
   *        name: "Corte y barba"
   *        duration: 30
   *        price: 6500
   *        is_active: true
  */
 serviceRouter.get('/', serviceController.getAll)
```
### Post
```javascript
/**
   * @swagger
   *  /api/v1/services:
   *    post:
   *      summary: Create a new service
   *      tags: [Service]
   *      security:
   *        - cookieAuth: []
   *      requestBody:
   *        required: true
   *        content:
   *          application/json:
   *            schema:
   *              type: object
   *              properties:
   *                name:
   *                  type: string
   *                duration:
   *                  type: integer
   *                  minimum: 1
   *                price:
   *                  type: integer
   *                  minimum: 1
   *              required:
   *                - name
   *                - duration
   *                - price
   *              example:
   *                name: "Haircut"
   *                duration: 45
   *                price: 100
   *      responses:
   *        201:
   *          description: Service created successfully
   *          content:
   *            application/json:
   *              schema:
   *                type: object
   *                properties:
   *                  status:
   *                    type: integer
   *                    example: 201
   *                  statusMessage:
   *                    type: string
   *                    example: "Success"
   *                  data:
   *                    type: object
   *                    $ref: '#/components/schemas/Service'
   *        403:
   *          description: Access not authorized
   *          content:
   *            application/json:
   *              schema:
   *                type: object
   *                properties:
   *                  status:
   *                    type: integer
   *                    example: 403
   *                  statusMessage:
   *                    type: string
   *                    example: "Access Not Authorized"
   *        422:
   *          description: Validation error
   *          content:
   *            application/json:
   *              schema:
   *                type: object
   *                properties:
   *                  status:
   *                    type: integer
   *                    example: 422
   *                  statusMessage:
   *                    type: string
   *                    example: "Validation Error"
   *                  error:
   *                    type: array
   *                    items:
   *                      type: object
   *                      properties:
   *                        field:
   *                          type: string
   *                          example: "name"
   *                        message:
   *                          type: string
   *                          example: "name must be a string"
   *        409:
   *          description: The service already exists
   *          content:
   *            application/json:
   *              schema:
   *                type: object
   *                properties:
   *                  status:
   *                    type: integer
   *                    example: 409
   *                  statusMessage:
   *                    type: string
   *                    example: "Already Exists"
   *                  error:
   *                    type: string
   *                    example: "The service already exists"
   *        400:
   *          description: Bad Request
   *          content:
   *            application/json:
   *              schema:
   *                type: object
   *                properties:
   *                  status:
   *                    type: integer
   *                    example: 400
   *                  statusMessage:
   *                    type: string
   *                    example: "Syntax Error"
   *                  error:
   *                    type: string
   *                    example: "Invalid object JSON provided"
   *        500:
   *          description: Failed Connection
   *          content:
   *            application/json:
   *              schema:
   *                type: object
   *                properties:
   *                  status:
   *                    type: integer
   *                    example: 500
   *                  statusMessage:
   *                    type: string
   *                    example: "Failed Connection"
   *                  error:
   *                    type: string
   *                    example: "Database is not available"
  */
 serviceRouter.post('/', serviceController.create)
```
### Patch
```javascript
/**
   * @swagger
   *  /api/v1/services/{id}:
   *    patch:
   *      summary: Edit service
   *      tags: [Service]
   *      security:
   *        - cookieAuth: []
   *      parameters:
   *        - in: path
   *          name: id
   *          required: true
   *          schema:
   *            type: string
   *            format: uuid
   *          description: The ID of the service to edit
   *      requestBody:
   *        required: true
   *        content:
   *          application/json:
   *            schema:
   *              type: object
   *              properties:
   *                name:
   *                  type: string
   *                duration:
   *                  type: integer
   *                  minimum: 1
   *                price:
   *                  type: integer
   *                  minimum: 1
   *              example:
   *                name: "Hair and beard cut"
   *                duration: 60
   *                price: 100
   *      responses:
   *        200:
   *          description: Service updated successfully
   *          content:
   *            application/json:
   *              schema:
   *                type: object
   *                properties:
   *                  status:
   *                    type: integer
   *                    example: 200
   *                  statusMessage:
   *                    type: string
   *                    example: "Success"
   *                  data:
   *                    type: object
   *                    $ref: '#/components/schemas/Service'
   *        403:
   *          description: Access not authorized
   *          content:
   *            application/json:
   *              schema:
   *                type: object
   *                properties:
   *                  status:
   *                    type: integer
   *                    example: 403
   *                  statusMessage:
   *                    type: string
   *                    example: "Access Not Authorized"
   *        400:
   *          description: Bad Request
   *          content:
   *            application/json:
   *              schema:
   *                type: object
   *                properties:
   *                  status:
   *                    type: integer
   *                    example: 400
   *                  statusMessage:
   *                    type: string
   *                    example: "Bad Request"
   *                  error:
   *                    type: string
   *                    example: "Invalid service ID provided"
   *        422:
   *          description: Validation error
   *          content:
   *            application/json:
   *              schema:
   *                type: object
   *                properties:
   *                  status:
   *                    type: integer
   *                    example: 422
   *                  statusMessage:
   *                    type: string
   *                    example: "Validation Error"
   *                  error:
   *                    type: array
   *                    items:
   *                      type: object
   *                      properties:
   *                        field:
   *                          type: string
   *                          example: "name"
   *                        message:
   *                          type: string
   *                          example: "name must be a string"
   *        404:
   *          description: Service not found
   *          content:
   *            application/json:
   *              schema:
   *                type: object
   *                properties:
   *                  status:
   *                    type: integer
   *                    example: 404
   *                  statusMessage:
   *                    type: string
   *                    example: "Not Found"
   *                  error:
   *                    type: string
   *                    example: "Service not found"
   *        409:
   *          description: The service already exists
   *          content:
   *            application/json:
   *              schema:
   *                type: object
   *                properties:
   *                  status:
   *                    type: integer
   *                    example: 409
   *                  statusMessage:
   *                    type: string
   *                    example: "Already Exists"
   *                  error:
   *                    type: string
   *                    example: "The service already exists"
   *        500:
   *          description: Failed Connection
   *          content:
   *            application/json:
   *              schema:
   *                type: object
   *                properties:
   *                  status:
   *                    type: integer
   *                    example: 500
   *                  statusMessage:
   *                    type: string
   *                    example: "Failed Connection"
   *                  error:
   *                    type: string
   *                    example: "Database is not available"
  */

serviceRouter.patch('/', serviceController.update)
```

## Testing
Instalar las dependencias supertest y vitest. Los test se realizan de la siguiente manera:

![TestingExample](./assets/testing-example.PNG)

- **describe**: Se utiliza para agrupar pruebas relacionadas dentro de un describe puedes tener múltiples bloques it que definan pruebas individuales.
- **it**: Se utiliza para definir una prueba individual. Es donde se especifica el comportamiento esperado de una función. Cada it debería contener una única afirmación sobre lo que se está probando.

![TestingExample](./assets/testing-example-2.PNG)

- **beforeAll**: Este método se ejecuta una sola vez antes de que comiencen todas las preubas en el bloque describe. Es útil para configurar el entorno de prueba, como inicializar conexiones a bases de datos, crear datos de prueba, o configurar dependencias.
- **afterAll**: Este método se ejecuta una sola vez después de que todas las pruebas en el bloque describe hayan terminado. Se utiliza para limpiar el entorno de prueba, como cerrar conexiones a bases de datos, eliminar datos de prueba, o liberar otros recursos.

## Librerías
### Calendarios
React Date Range

### Fechas y horas
Dayjs
```javascript
dayjs('2018-08-08') // Crea una instancia de dayjs para la fecha indicada
dayjs().set('month', 3) // A la fecha actual se le cambia el més por el més índice 3 (Abril en javascript)
dayjs().add('year', 1) // A la fecha actual se le suma un año
dayjs().isBefore(dayjs()) // Indica si una fecha es menor que otra
dayjs(date, 'HH:mm') // Crea instancia de dayjs interpretando la cadena date como una hora en formato HH:mm
```

# PostgreSQL
## UUID
Para utilizar uuids en PostgreSQL, se extiende de la librería uuid-ossp.
```postgreSQL
CREATE EXTENSION IF NOT EXISTS "uuid-ossp"
```
Luego para crear un atributo uuid se llama a la función que se quiera, hay distintas versiones de uuid.
```postgreSQL
id_user UUID PRIMARY KEY DEFAULT uuid_generate_v4()
```

## Querys
### CREATE
```postgreSQL
CREATE TABLE users (
    id_user UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    name VARCHAR(255) NOT NULL,
    email VARCHAR(255) NOT NULL,
    password CHAR(60) NOT NULL,
    phone_number VARCHAR(20) NOT NULL,
    role user_role DEFAULT 'client'
)
```
### TRUNCATE
```postgreSQL
TRUNCATE TABLE services CASCADE
```
### INSERT
```postgreSQL
INSERT INTO services (name, duration, price) VALUES ('New Service Name', 30, 100)
```
### DELETE
```postgreSQL
DELETE FROM users WHERE id_user = 'ffnahbnf8aef'
```
### UPDATE
```postgreSQL
UPDATE users
SET name = 'oscar'
    phone_number = '438724834'
WHERE id_user = 'fjauehfy8q3'
```
### SELECT
```postgreSQL
SELECT * FROM users
```

## Timezone
Cambiar zona horaria de la BD, para todas las operaciones:
```postgreSQL
SET TIME ZONE 'Europe/Rome'
```
Cambiar zona horaria para un atributo date o datetime en una consulta:
```postgreSQL
SELECT date_time AT TIME ZONE 'Europe/Rome', available
FROM turns
```

## Fechas y horas
Las fechas y horas en PostgreSQL se guardan en formato UTC, cuando se inserte un objeto datetime hay que indicarle la diferencia de hora dependiendo la región '2024-08-14 14:00:00-03'
Luego cuando se obtengan las fechas y horas de la bd, hay que transformarlas a la zona horaria que quiera en el servidor, con dayjs por ejemplo.

# Typescript
Lenguaje de tipado estricto, mejora a javascript, ya que este es de tipado dinámico. Te da:
1. **Seguridad**.
2. **Mantenibilidad**.
3. **Refactorización**.

Se actualiza con la última versión de javascript.
Importante, Una vez que el código TypeScript se ha compilado en JavaScript, se ejecuta como cualquier otro código JavaScript, ya que TypeScript no añade ningún tipo de verificación en tiempo de ejecución. Todo el tipado de TypeScript se elimina en el proceso de compilación, por lo que en tiempo de ejecución, el código se comporta como JavaScript. Entonces no hay que olvidar de validar entrada de datos de usuarios, ya que typescript no es tan inteligente como para evitar errores de entrada, nada más funciona en compilación.
La compilación de TypeScript a JavaScript es realizada por el compilador de TypeScript, que comúnmente se conoce como tsc (TypeScript Compiler). Este compilador es responsable de:
- **Verificar Tipos**: Durante la compilación, tsc verifica que el código cumpla con las reglas de tipado estático especificadas en los archivos .ts o .tsx. Si hay errores de tipo, tsc los reportará.
- **Transpilar a JavaScript**: Una vez que el código ha sido verificado, tsc transpila (convierte) el código TypeScript a código JavaScript equivalente. Este JavaScript puede ser ejecutado en cualquier entorno que soporte JavaScript, como navegadores o Node.js.

![TypescriptClass](./assets/typescript-class.PNG)

Como la clase Person tiene los mismos atributos, tipos y métodos idénticos a Person2, una instancia de Person2 se puede volver una instancia de Person. Se lo conoce como Shapes. 

## Typescript con Nodejs y Express
Pasos para que funcione:
1. **Instalar dependencia de Typescript**
```bash
npm install typescript -D
```
2. **Configurar script, asegurarse de configurar uno tsc**
```javascript
"scripts": {
    "tsc": "tsc"
}
```
3. **Inicializar tsc**
```bash
npm run tsc -- --init
```
4. **Configurar tsconfig.json**
5. **Instalar dependencia de tipos de express**
```bash
npm install @types/express
```
6. **Ejecutar tsc**
```bash
npm run tsc
```
7. **Recomendado** Instalar linter **ts-standard**

# Conceptos Backend
## Arquitecturas
### Monolitos y Microservicios
#### Arquitectura Monolítica
- La Aplicación está contenida en un solo modelado de clases
- Posee una sola lógica de negocio
- Posee una sola base de datos CENTRALIZADA para toda la aplicación
- Todo el código fuente de la aplicación se encuentra en un solo proyecto, que se compila en un solo aarchivo ejecutable

###### Ventajas
- Fácil de desarrollar y mantener
- No depender de servicios o aplicaciones externas

##### Desventajas
- Gran complejidad al crecer en gran escala
- Mayor probabilidad de fallos y cuellos de botella

#### Arquitectura de Microservicos
- Las partes de la aplicación se subdividen en servicios más pequeños autónomos
- Cada microservicio tiene su propio modelado, lógica de negocio y base de datos
- Cada microservicio se comunica con otro a través de APIs

##### Ventajas
- Si una parte de la aplicación falla, no afecta a las demas.
- Desarrollo más ágil
- División de tareas sencilla

##### Desventajas
- Complejidad que puede alcanzar
- Se pueden producir fallos de comunicación entre microservicios
- Requiere mucha m´sa infraestructura
- A mayor infraestructura tenemos mayor costo

#### ¿Cuál de las dos es mejor?
Depende de las necesidades que tenga la aplicación, en resumen, para proyectos pequeños es mejor opción un Monolito y para proyectos más grandes utilizar mejor Microservicios