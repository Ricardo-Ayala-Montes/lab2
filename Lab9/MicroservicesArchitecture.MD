### Designing and Implementing a Microservices Architecture

### Explicación de la Interacción entre Servicios y Flujo de Comunicación
1. **Frontend**: El usuario interactúa con la interfaz, que a su vez se conecta al API GATEWAY.
2. **API Gateway**: el componente actúa como el punto de entrada principal, redirigiendo las solicitudes del usuario/frontend a los servicios correspondientes y permitiendo el balanceo de carga.
3. **Microservicios**:
    - **User Management**: Este servicio se encarga de la administración de los perfiles de usuario, autenticación y autorización.
    - **Product Catalog**: Este servicio maneja el catálogo de los productos y sus datos mas importantes como son: descripcion, precio, imágenes e inventario.
    - **Order Processing**: Adminstra todo el proceso de pedidos, por ejemplo la selección del producto, pago y la gestión de órdenes.
    - **Customer Support**: Maneja las consultas y problemas de los clientes a través de un sistema de tickets.

#### Migration Roadmap and Architecture Documentation

**Analisis**
* Analizar las funcionalidades del backend monolito para determinar cuales son las responsabilidades de cada dominio.
* Identificar y definir las operaciones de cada uno de los microservicios identificados.

**Implementación**
* Configuración de API Gateway que será responsable de la orquestación y enrutamiento de peticiones hacia los 4 microservicios.
* Creación del microservicio Users (AWS EKS) que se encarga de la autenticación y autorización de usuarios, además de la administración de perfil de usuarios.
    * Implementación de AWS Cognito para la gestión de inicio de sesión generando un JWT, el refreshToken para mantener la sesión activa, logout para revocar las sesiones.
    * Creación de base de datos relacional AWS RDS Maria DB que contiene la información del usuarios.
* Continuamos con el microservicio Products para gestionar el catálogo de productos e inventario de productos.
    * Requiere una base de datos relacional con dos tablas, una de productos y otra que tenga relación con el inventario de estos productos.
    * Además se requiere que el microservicio se suscriba a una SQS donde va a recibir eventos de los pedidos de los usuarios, de tal forma que pueda eliminar productos y cantidades de productos del inventario.
* Seguimos con el microservicio Orders que gestiona los pedidos y transacciones de pagos, pero para poder acceder a los endpoints de este microservicio, en API Gateway necesitamos validar que el usuario tiene un JWT haciendo una validación con la llave pública jwks.
    * Requiere una base de datos relacional con tres tablas relacionadas entre sí, Order, OrderDetail, OrderPaymentDetail.
    * Requiere la publicación del evento Orden en un EventBus de AWS EventBridge, además de una rule que pueda llevar el evento a una SQS que será consumida por el microservicio Products para que pueda hacer la baja del inventario de los productos en la orden pagada. Tener una cola de mensajes y dejar que lo gestione AWS agrega resiliencia y escalabilidad.
* Por ultimo la creación del microservicio Soporte para la atención al cliente que genera tickets para que los agentes puedan tomar estos casos, este microservicio también necesita que el usuario haya iniciado sesión para tener el ID del usuario y como medida de seguridad.
    * Se requiere una base de datos relacional para administrar los tickets.
* Finalmente se realizan las pruebas funcionales, pruebas automatizadas, durante los despliegues a Develop, QA, Stage y Main vamos haciendo pruebas de penetración y hemos creado una integración continua CICD con github actions con Docker, Kubernetes gestionado por AWS con EKS, sus respectivas pruebas SAST y DAST. Además de la creación de la infraestructura como código con Terraform.

### Decisiones de Migración
1. **Uso de APIs para Comunicación**: Decidimos que la comunicación entre microservicios debería ser principalmente a través de APIs, dado que esto permite comunicación de servicios de manera flexible.
2. **Descomposición de la Base de Datos**: Optamos por una migración incremental de la base de datos monolítica a bases de datos independientes para cada microservicio. Esto permite aislar problemas y reducir la complejidad de la migración.
3. **Escalabilidad y Alta Disponibilidad**: La prioridad en la arquitectura de microservicios es mejorar la escalabilidad.
