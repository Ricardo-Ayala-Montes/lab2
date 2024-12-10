### Designing and Simulating AWS Cloud Architectures

**Designing Cloud Infrastructure**
* Para la creación de la infraestructura básica de nube en AWS para una aplicación web, necesitamos crear un AWS EC2, S3 y VPC.
*   VPC: VPC es una red virtual privada que nos permite ejecutar recursos de AWS de manera segura a través de networking.
    * Necesitamos definir un rango de IPs, para tener una mejor disponibilidad podemos tener 2 subredes públicas (Internet gateway para salir a internet) y 2 subredes privadas.
    * Las subredes públicas nos ayudan a que los balanceadores de carga (AWS ELB) estén expuestos a internet y pueda ser consumido por clientes.
    Las subredes privadas donde vivirán las instancias EC2 para no exponerlas a internet.
    * AWS Elastic load balancing (ELB) no está especificado pero nos viene bien para controlar la entrada y configuramos enrutamiento seguro hacia el microservicio EC2 mediante Security Groups.
    * Para S3 se recomienda un VPC endpoint para no exponer el servicio y EC2 pueda conectarse de manera segura sin pasar por internet.


**IAM Configuration**
1. Desarrolladores:
Permisos: Acceso restringido a los recursos necesarios para el desarrollo, como acceso a las instancias EC2, S3 (solo lectura), y logs en CloudWatch.
Política recomendada: AmazonEC2ReadOnlyAccess, AmazonS3ReadOnlyAccess, CloudWatchLogsReadOnlyAccess.
2. Administradores:
Permisos: Acceso total a todos los recursos, incluyendo la capacidad de gestionar EC2, VPC, IAM, y servicios de almacenamiento.
Política recomendada: AdministratorAccess.
3. Instancias de Aplicación:
Permisos: Acceso limitado a servicios específicos como S3 para cargar o recuperar archivos y bases de datos (aurora o DynamoDB), con permisos de solo lectura o escritura según sea necesario.
Política recomendada: Crear una política personalizada que permita acceso solo a los buckets de S3 requeridos y a las bases de datos específicas.
Principio de menor privilegio:
Crear políticas de IAM que otorguen solo los permisos mínimos necesarios para cada tipo de usuario o componente.
Las instancias EC2, deben tener un rol IAM que solo permita el acceso a los recursos que necesita para la ejecución de la aplicación.


**Resource Management Strategy**
Es importante la creación de estrategias de gestión de recursos para ofrecer una mejor disponibilidad y escalado.
* Auto Scaling Group para las instancias EC2 se requiere 2 zonas de disponibilidad con el uso de las subredes privadas que especifiqué en la parte 1 y se podrá observar en el diagrama de la parte 4. El tamaño del grupo se puede configurar para tener un mínumo número de instancias y un máximo, dependerá del consumo esperado y crece en automático, por ejemplo cuando aumenta el tráfico.
Podemos identificar patrones, horas pico y durante la noche para accionar cuantos recursos tener.
* AWS Elastic load balancing, necesitamos definir una revisión de salud mediante un endpoint health. El mismo Auto Scaling puede mejora el rendimiento del balanceador de carga.
* AWS Budgets, podemos monitorear los comportamientos de los recursos, por ejemplo, si estamos creando demasiados objetos en S3, podemos monitorear y definir alertas. También se puede definir un límite de gasto mensual.

**Theoretical Implementation**
Arquitectura:
VPC: La infraestructura de red está aislada en una VPC, con subredes públicas y privadas para separar los servicios accesibles desde internet y los recursos internos.
EC2: Instancias EC2 en subredes privadas, que ejecutan la aplicación web y se comunican con la base de datos.
S3: Usado para almacenar archivos estáticos como imágenes, videos, y documentos.
ELB: Un ALB que distribuye el tráfico web hacia las instancias EC2.
IAM: Roles IAM para los usuarios y servicios, implementando el principio de menor privilegio.
Auto Scaling: Se utiliza para ajustar automáticamente el número de instancias EC2 en función de la carga.
Flujo de datos:
Los usuarios acceden a la aplicación a través del ALB, que distribuye el tráfico a las instancias EC2 que ejecutan la aplicación.
Las instancias EC2 acceden a S3 para almacenar y recuperar archivos, y a la base de datos (RDS o DynamoDB) para almacenar datos persistentes.
Los desarrolladores y administradores gestionan la infraestructura utilizando permisos IAM específicos, asegurando que solo tengan acceso a los recursos que necesitan.

** Discussion and Evaluation**
* Decidí usar una VPC para poder aislar toda nuestra infraestructura del internet. A través de configuraciones de red, subredes, enrutamientos, grupos de seguridad mantenemos el tráfico de manera segura.
* Se requieren 4 subredes, 2 públicas para el balanceador de carga y 2 privadas para las instancias EC2, 4 instancias en lugar de dos para poder disponibilizar el servicio en diferentes regiones.
* Se requiere implementar Internet Gateway para permitir que los clientes puedan comunicarse con nuestra VPC, entrando por el balanceador de carga.
* Se requieren configuraciones de Auto Scaling para escalar de manera automática el número de instancias y optimizar los costos en función de las necesidades.
* Se decide usar VPC endpoint para S3 para permitir la comunicación entre nuestros microservicios que corren en las EC2 hacia S3, de tal forma que el trafico de información no se exponga a internet.
* Además se usa correctamente las políticas mediante el principio del mínimo privilegio para tener cuidado y evitar malos manejos en la infraestructura.