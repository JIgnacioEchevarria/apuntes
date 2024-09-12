# Apuntes
En este repositorio voy a tener todos los apuntes de diferentes tecnologías estudiadas

## Temas
- [Nodejs](#nodejs)
- [PostgreSQL](#postgresql)
- [Typescript](#typescript)
- [Java](#java)
- [SpringBoot](#spring-boot)
- [ConceptosBackend](#conceptos-backend)
- [BasesDeDatos](#bases-de-datos)

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

## Producción
Cuando se lanza una web fullstack a poproducción en Vercel, al estar todo ruteado con React Router Dom, cuando se refresque una pagina esta no sera encontrada, para eso hay una solución, en el cliente crear un json ed esta manera con mismo nombre.

![VercelJSON](./assets/vercel.PNG)

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

# Java
## JDBC
API de Java para permite conectarse a una base de datos, se puede utilizar con cualquier tipo de base de datos.

### Componentes de JDBC
- **DriverManager**: Administra una lista de controladores de bases de datos. Se utiliza para estblecer la conexión con la base de datos.
- **Connection**: Representa una conexión a una base de datos específica.
- **Statement**: Se utiliza para ejecutar una consulta SQL.
- **ResultSet**: Contiene los resultados de una consulta SQL.
- **PreparedStatement**: Similar a Statement pero permite la ejecución de consultas precompiladas y es más eficiente y seguro frente a inyecciones SQL.
- **CallableStatement**: Se utiliza para jecutar procedimientos almacenados en la base de datos.

## JPA
Es una API estándar de Java para implementar mapeo objeto-relacional. Para utilizarla se necesita una implementación o proveedor de la misma, como Hibernate, Eclipselink, OpenJPA, etc. JPA se configura a través de metadatos en archivos xml.

Para un proyecto con JPA se necesita:
- **Driver JDBC + Base de datos**
- **Proveedor JPA**. Ej: Hibernate.
- **Archivo de configuración JPA**. (persistence.xml)
- **Entidades**: Clases Java extendidas con metadatos que describen el mapeo de sus atributos a tablas, se puede mediante:
    - Archivos de mapeo (entity.xml)-
    - Anotaciones (@Entity)
- Código de la aplicación, que manipula las entidades a través de un **EntityManager**.

### Anotaciones JPA
#### Entidades
En JPA se tiene que indicar que una clase es una entidad de la siguiente manera, luego JPA se encarga de mapearla para convertirla en una tabla en la base de datos.
```java
@Entity // Anotacion para indicar que es una entidad
@Table(name = "person") // Nombre de la tabla
public class Person {
    // Atributos y métodos
}
```

#### Atributos de entidades
Las anotaciones de atributos se indican encima del atributo que se quiere anotar.
```java
@Entity
@Table(name = "person")
public class Person {
    @Id // Indica que el atributo es el iidentificador único de la entidad.
    private int id;

    @Column(nullable = false) // Indica que es una columna que no puede ser nula.
    private String name;

    @Column(name = "email_address") // Indica que es una columna y le indica el nombre.
    private String email;

    private int age; // Cuando no hay nada adicional que indicar (nombre, not null, etc...) no hace falta indicar la anotacion @Column
}
```

##### IDS Autoincrementales
```java
@Entity
@Table(name = "person")
public class Person {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private int id;
}
```

#### Anotaciones en Clases Abstractas
```java
@Entity
@Inheritance(strategy = InheritanceType.SINGLE_TABLE)
@DiscriminatorColumn(name = "role")
public abstract class Person {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private int id;

    private String name;
}

@Entity
@DiscriminatorValue("employee")
public class Employee extends Person {
    // Atributos y métodos
}

@Entity
@DiscriminatorValue("client")
public class Client extends Person {
    // Atributos y métodos
}
```

### Relaciones entre entidades
- **Navegaciones de las relaciones**: Representa la visibilidad entre los objetos.
    - **Unidireccionales**: Reflejan un objeto que tiene una referencia a otro objeto.
    - **Bidireccionales**: Representan dos objetos que mantienen referencias al objeto contrario.
- **Multiplicidad**: La multiplicidad de un asociación determina cuantos objetos de cada tipo intervienen en la asociación.
    - **Uno a uno**
    - **Uno a muchos**
    - **Muchos a muchos**

### Anotaciones en JPA para las asociaciones
- @ManyToOne
- @OneToMany
- @OneToOne
- @ManyToMany

### Ejemplos de Relaciones
- Relación entre Empleado y Departamento, es una relación de uno(Departamento) a muchos(Empleado).

#### ManyToOne unidireccional
```java
@Entity
public class Empleado {
    @Id
    private int id;
    private String nombre;

    @ManyToOne // Referencia a Departamento con un atributo del tipo Departamento
    @JoinColumn(name = "id_departamento") // Indica el nombre de la columna de la fk.
    private Departamento departamento;
}

@Entity
public class Departamento {
    @Id
    private int id;
    private String nombre;
}
```

#### OneToMany unidireccional
```java
@Entity
public class Departamento {
    @Id
    private int id;
    private String nombre;

    @OneToMany(fetch = FetchType.LAZY) // Referencia a empleado con una lista de los empleados que estan en ese departamento
    private List<Empleado> listaEmpleados;
}

@Entity
public class Empleado {
    @Id
    private int id;
    private String nombre;
}
```

#### OneToMany y ManyToOne bidireccional
Ambos mantienen la referencia al otro, identicas a los ejemplos anteriores.
```java
@Entity
@Table(name = "departamento")
public class Departamento {
    @Id
    private int id;
    private String nombre;

    @OneToMany(mappedBy = "departamento", fetch = FetchType.LAZY) // En mappedBy va el nombre que se le pone a la referencia en el otro objeto(Empleado)
    private List listaEmpleado; // Referencia a Empleado
}

@Entity
public class Empleado {
    @Id
    private int id;
    private String nombre;

    @ManyToOne
    @JoinColumn(name = "id_departamento")
    private Departamento departamento; // Referencia a Departamento
}
```

### OneToOne unidireccional
- Marido es dueño de la relación, ya que tiene la FK, en la BD la tabla Marido tendra una columna que representa la relación con mujer (FK).

```java
@Entity
public class Marido {
    @Id
    private int id;

    @OneToOne
    private Mujer mujer;
}

@Entity
public class Mujer {
    @Id
    private int id;
}
```

### OneToOne bidireccional
- Ambos mantienen una referencia
- El dueño de la relación se especifica explicitamente
- Uno de ellos tiene que indicar que la parte contraria es la dueña de la relacion con el mappedBy

```java
@Entity
public class Mujer {
    @Id
    private int id;

    @OneToOne
    private Marido marido;
}

@Entity
public class Marido {
    @Id
    private int id;

    @OneToOne(mappedBy = "marido") // Indica que mujer es dueñad de la relación, se indica el nombre del atributo de la referencia en el objeto Mujer.
    private Mujer mujer;
}
```

### ManyToMany unidireccional
```java
@Entity
public class Alumno {
    @Id
    private int id;
    private String nombre;

    @ManyToMany
    private List<Profesor> profesores;
}

@Entity
public class Profesor {
    @Id
    private int id;
    private String nombre;
}
```

### ManyToMany bidireccional
```java
@Entity
public class Alumno {
    @Id
    private int id;
    private String nombre;

    @ManyToMany(mappedBy = "alumnos") // Indica que Profesor es el dueño de la relación, con el mappedBy alumnos (atributo de Profesor)
    private List<Profesor> profesores;
}

@Entity
public class Profesor {
    @Id
    private int id;
    private String nombre;

    @ManyToMany
    private List<Alumno> alumnos;
}
```

### Propiedades de las anotaciones de Asociación
- **targetEntity**: Para especificar el tipo (clase) de la entidad a la que hace referencia. Por defecto infiere el tipo.
- **cascade**: Para especificar que las operaciones de persistencia pueden abarcar tambien a las referencias. Por defecto es vacío.
- **fetch**: Para especificar el tipo de lectura de la referencia.
    - **EAGER**: La lista de la referencia se carga de inmediato cuando se carga la entidad. JPA realiza una consulta adicional a la base de datos para cargar la entidad relacionada al mismo tiempo que la entidad principal. Esto significa que se obtienen todos los datos relacionados de una vez.
    - **LAZY**: La asociación no se carga automáticamente, se carga solo cuando se accede a ella por primera vez. utilizado para mejorar el rendimiento, no carga los datos hasta que realmente se los necesite. Se la inicializa accediendo a la lista con .size(), .filter(), etc...
- **mappedBy**: Para especificar el dueño de la relacion en las asociaciones bidireccionales (ManyToOne no tiene esta propiedad)

# Spring Boot
Extensión del framework Spring de JAVA que simplifica la configuración y el desarrollo de aplicaciones basadas en Spring. Su objetivo principal es hacer que el desarrollo de aplicaciones JAVA se más rápido y sencillo, especialmente para aplicaciones web y microservicios.

## ¿Qué es Spring?
Es un framework para el desarrollo de aplicaciones y contenedor de inversión de control, de código abierto para JAVA. Hace que la programación en JAVA sea más rápida, fácil y segura para todos. El enfoque de Spring esta en la velocidad, la simplicidad y la productividad. Fácilita la configuración y el trabajo con JAVA.

## Beneficios de utilizar Spring Boot
1. **Configuración Automática**: Ofrece una configuración automática que ajusta la aplicación a las dependencias y las bibliotecasque hayas incluido. Esto reduce la necesidad de configuraciones manuales y permite que la aplicación arranque con una configuración predeterminada que funciona bien en la mayoría de los casos.

2. **Servidor Incorporado**: Incluye servidores web como Tomcat, Jetty o Undertow, lo que significa que no necesitas desplegar tu aplicación en un servidor externo. Puedes ejecutar tu aplicación como una aplicación JAVA independiente.

3. **Spring Boot Starters**: Proporciona starters, son dependencias preconfiguradas para diversas funcionalidades como seguridad, acceso a datos, y más. Esto fácilita agregar soporte para diferentes funcionalidades de tu aplicación.

4. **Spring Boot Actuator**: Ofrece características de monitoreo y administración para aplicaciones en producción, como métricas, información del estado de la aplicación y la capacidad de realizar cambios de configuración en tiempo real.

5. **Opiniones por Defecto**: Tiene una serie de configuraciones predeterminadas y convenciones que simplifican el desarrollo. Aunque puedes personalizar la configuración, las opciones predeterminadas ayudan a evitar configuraciones complejas.

6. **Soporte para Microservicios**: Es especialmente útil apra construir microservicios debido a su configuración sencilla y su capacidad para ejecutarse de manera independiente.

## Maven
Apache Maven es una herramienta de gestión de proyectos que se utiliza para gestión de dependencias, como herramienta de compilación e incluso como herramienta de documentación.
Las dependencias se indican dentro del archivo **pom.xml**, que dentro de el ademas de las dependencias, hay versión de Spring Boot, plugins, perfiles, etc...

## Dependencias útiles
- **Spring Boot DevTools**: Sirve para no tener que relevantar el servidor cada vez que se hacen cambios en el código.
- **Spring Web**: La dependencia más importante para crear una API.

## Sintaxis Spring Boot API

### Controller
Es un componente clave de la aplicación que se encarga de manejar las solicitudes HTTP y devolver las respuestas adecuadas. Gestiona la lógica de presentación y coordina el flujo de los datos entre el modelo (entidades y servicios) y la vista (Frontend).

#### Ejemplo
```java
package com.jie.firstapi.controller;

import com.jie.firstapi.entity.Person;
import org.springframework.web.ErrorResponseException;
import org.springframework.web.bind.annotation.*;

import java.util.ArrayList;
import java.util.List;

@RestController
@RequestMapping("/api/v1") // Path para que todas los endPoints del controlador comienzen con el mismo Path.
public class UsersController {
    private List<Person> users;

    public UsersController() {
        this.users = new ArrayList<>();
    }

    @GetMapping("/")
    public List<Person> getUsers() {
        return users;
    }

    @GetMapping("/{id}")
    public Person getUserById(@PathVariable("id") int idUser) {
        if (
            idUser > this.users.size() ||
            this.users.get(idUser) == null
        ) {
            throw new Error("Not Found");
        }

        return this.users.get(idUser - 1);
    }

    @PostMapping("/")
    public void addUser(@RequestBody Person user) {
        this.users.add(user);
    }
}
```

### Verbos, Parametros y Cuerpos de Consultas

#### Verbos

Los diferentes verbos se escriben todos de la misma forma, @ seguido del verbo comenzando en mayuscula, seguido de Mapping y dentro de los parentesis el path: **@VerboMapping("/path")**

Por ejemplo:

**GET**: @GetMapping("/")

**POST**: @PostMapping("/")

**ETC...**

#### Parametros
**PathVariable**: Utilizado para extraer valores de la URL que forman parte del propio camino. Por ejemplo:

```bash
http://localhost:8080/api/v1/users/123
```
Aca 123 es un valor que forma parte del camino de la URL y podría representar el ID de un usuario. Se utiliza @PathVariable para extraer este valor:

```java
@RestController
public class UserController {
    @GetMapping("/api/users/{id}")
    public String getUserById(@PathVariable("id") String userId) {
        return "User ID is " + userId;
    }
}
```

Luego de indicar PathVariable se indica al tipo de dato que se convertira este parametro, como String, int, boolean, etc...

Dentro de los parentesis en PathVariable se pueden indicar diferentes cosas:

- value: nombre del parametro.
- required: indica si es requerido (por defecto en true).
- defaultValue: valor por defecto en caso de que no se proporcione nada.

**RequestParam**: Utilizado para extraer valores de los parametros de consulta en una URL. Estos parametros se envian en la cadena de la consulta despues del signo ? y estan separados por &. Por ejemplo:

```bash
http://localhost:8080/api/v1/users?page=2&size=10
```

page y size son parametros de la query, generalmente son utiilizados para indicar filtros opcionales.

```java
@RestController
@RequestMapping("/api/v1/users")
public class UserController {

    @GetMapping("/")
    public ResponseEntity<List<User>> getUsers(
            @RequestParam(value = "page", defaultValue = "1") int page,
            @RequestParam(value = "size", defaultValue = "10") int size) {
        // Aquí 'page' tomará el valor '2' y 'size' tomará el valor '10' de la URL
        List<User> users = userService.getUsers(page, size);
        return ResponseEntity.ok(users);
    }
}
```

Luego de indicar RequestParam se indica al tipo de dato que se convertira este parametro, como String, int, boolean, etc...

Dentro de los parentesis en RequestParam se pueden indicar diferentes cosas:

- value: nombre del parametro.
- required: indica si es requerido (por defecto en true).
- defaultValue: valor por defecto en caso de que no se proporcione nada.



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

## Patrones de Diseño
Los patrones de diseño son soluciones probadas y reutilizables para resolver problemas comunes que surgen en el desarrollo de software. Se trata de plantillas que identifican problemas en el sistema y proporcionan soluciones apropiadas a problemas generales.

### Tipos de Patrones de Diseño
- **Patrones Creacionales**: Los patrones creacionales proporcionan varios mecanismos de creación de objetos que incrementan la flexibilidad y la reutilización del código existente, como por ejemplo: **Singleton**, **Factory Method**, **Abstract Method**, **Builder**, **Prototype**.

- **Patrones Estructurales**: Los patrones estructurales explican cómo ensamblar objetos y clases en estructuras más grandes, a la vez que se mantiene la flexibilidad y eficiencia de estas estructuras, como por ejemplo: **Adapter**, **Composite**, **Proxy**.

- **Patrones de Comportamiento**: Los patrones de comportamiento tratan con algoritmos y la asignación de responsabilidades entre objetos, como por ejemplo: **Iterator**, **Command**, **Observer**, **Strategy**

## API
Application Programming Interface, es el punto de interconexión entre dos partes, por ejemplo si se quiere conectar un Frontend con React y un Backend con Spring Boot, se comunican a travez de una API, que utiliza un lenguaje universal para transmitir datos.

### API REST
Es un tipo de servicio que se caracteriza por no tener estado y por lograr interconexiones mediante el protocolo http con mensajes de tipo XML o JSON.

#### Características clave de una API REST:
- **Cliente-Servidor**: El modelo REST separa las responsabilidades entre el cliente, que realiza las solicitudes, y el servidor, que maneja y responde a esas solicitudes. Esto permite que ambos evolucionen de manera independiente.

- **Stateless (Sin Estado)**: Cada solicitud de cliente a servidor debe contener toda la información necesaria para que el servidor entienda y procese la solicitud. El servidor no guarda estado entre las solicitudes, lo que hace que cada petición sea independiente.

- **Cacheable**: Las respuestas de las solicitudes pueden ser cacheadas por el cliente o por intermediarios (como proxies), lo que puede mejorar la eficiencia al reducir el número de solicitudes que se envían al servidor.

- **Interfaz uniforme**: REST utiliza métodos HTTP estándar (como GET, POST, PUT, DELETE) para interactuar con los recursos, lo que permite una manipulación consistente y predecible de los datos.

- **Uso de Recursos**: En REST, se considera a todo un recurso, que es una entidad de datos o un objeto, identificada por una URL única. Por ejemplo, un recurso podría ser un "usuario" en un sistema, y se accedería a él a través de una URL como /usuarios/{id}.

- **Representaciones**: Los recursos pueden ser representados en diferentes formatos, como JSON o XML. El cliente y el servidor pueden negociar el formato que prefieren para enviar y recibir datos.

# Bases de Datos
## Bases de Datos NoSql
### REDIS
Almacén de datos clave-valor en memoria, de código abierto y rápido. Ofrece tiempos de respuesta inferiores al milisegundo, lo que permite que se realicen millones de solicitudes por segundo para aplicaciones en tiempo real de la industria. Redis es muy utilizado en aplicaciones de almacenamiento en caché, administración de sesiones, videojuegos, tablas de clasificación, análisis en tiempo real, datos geoespaciales, servicios de vehículos compartidos, chats, streaming de contenido multimedia y publicación/suscripción.

#### Motores en memoria
El almacenamiento en memoria caché ofrece un mayor rendimiento en la recuperación de datos y una reducción de los costos de escalabilidad.

#### Almacenamiento en Caché
Se puede utilizar para reducir la latencia y mejorar las IOPS de muchas cargas de trabajo de aplicaciones de alto nivel de lectura, como portales de preguntas y respuesteas, videojuegos, uso compartido de contenido y redes sociales.

#### Beneficios de REDIS
- **Rendimiento**: Todos los datos de REDIS residen en memoria, lo que permite un acceso a datos de baja latencia y alto rendimiento. Los almacenes de datos en memoria no requieren un viaje al disco, lo que reduce la latencia del motor.

- **Estructuras de datos flexibles**: Cuenta con una amplia variedad de estructuras de datos para satisfacer los requisitos de sus aplicaciones. Los distintos tipos de datos de REDIS son:
    - **Cadenas**: Datos de texto o binarios de hasta 512 MB de tamaño.
    - **Listas**: Una colección de cadenas en el orden en que se agregaron.
    - **Conjuntos**: Una colección desordenada de cadenas con la capacidad para intercalarse, unirse y diferenciarse de otros tipos de conjuntos.
    - **Conjuntos ordenados**: Conjuntos ordenados por un valor.
    - **Hashes**: Una estructura de datos para almacenar una lista de campos y valores.
    - **Mapas de bits**: Un tipo de datos que ofrece operaciones a nivel de bits.
    - **HyperLogLogs**: Una estructura de datos probabilísticos para estimar los elementos únicos en un conjunto de datos.
    - **Secuencias**: Una cola de mensajes de estructura de datos de registro.
    - **Geoespacial**: Mapas de entradas basados en longitud / latitud, "cercanía".
    - **JSON**: Un objeto anidado y semiestructurado de valores con nombre que admite números, cadenas, booleanos, matrices y otros objetos.
- **Simplicidad y facilidad de uso**: REDIS permite escribir códio complejo con menos línea y más simples.
- **Replicación y persistencia**: REDIS utiliza una arquitectura con servidor principal y réplica y admite la replicación asíncrona en la que los datos se replican en numerosos servidores de réplicas. Se logra un mejor nivel de rendimiento de lectura, ya que las solicitudes de pueden repartir en varios servidores, y menores tiempos de recuperación cuando el servidor principal sufre un corte. REDIS admite copias de seguridad puntuales para persistencia (Copia el conjunto de datos REDIS en el disco).
- **Alto nivel de disponibilidad y escalabilidad**: Ofrece una arquitectura con servidor principal y réplica en una topología en clústeres o principal con un único nodo. Esto permite crear soluciones con un alto nivel de disponibilidad, lo que ofrece fiabilidad y rendimiento estables. Cuando se necesita ajustar el tamaño de un clúster, se encuentran disponibles diferentes opciones de escalado. Esto permite que el tamaño del clúster se ajuste a sus necesidades.

#### Usos de REDIS
- Es una excelente opción para implementar una caché en memoria de alta disponibilidad a fin de reducir los tiempos de latencia. Algunos ejemplos:
    - Chat, mensajería y colas.
    - Tablas de clasificación de videojuegos.
- Resguardo de sesiones.
- Streaming completo de contenido multimedia.
- Análisis geoespacial.
- Machine Learning (ChatGPT).
- Análisis en tiempo real.

# POO - Programación Orientada a Objetos
## Abstracción
## Encapsulamiento
## Polimorfismo
## Herencia
## UML