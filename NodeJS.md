# NodeJS

## Autenticación

### Tokens
Para autenticar un usuario utilizar Json Web Tokens, y luego guardarlo en cookies. Las cookies son menos vulnerables a inyecciones maliciosas, ya que solo son accesibles por medio de http, no se pueden acceder a ellas a través de javascript. Además se puede configurar para que las cookies se envien solo con https. Lo que sí puedo devolver en la respuesta y guardar en local storage es la información del usuario que no sea sensible, como email, nombre de usuario, id, etc. No devolver contraseñas.
Los JWT se dividen en 3 partes:

- **Header**: Indica algoritmo y el tipo del token
- **Payload**: Contiene los datos codificados.
- **Signature**: Permite decodificar la información del payload.

![JwtGraphic](./assets/jwt-graphic.PNG)