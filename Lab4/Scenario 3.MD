Secure Data Communication Plan

´´´
PLAN secureDataCommunication:
  IMPLEMENT SSL/TLS for all data in transit
  USE encrypted storage solutions for data at rest
  ENSURE all data exchanges comply with HTTPS protocols

´´´

´´´

PLAN secureDataCommunication:
  IMPLEMENT SSL/TLS for all data in transit
  USE encrypted storage solutions for data at rest
  ENSURE all data exchanges comply with HTTPS protocols

´´´

1. Lo recomendable es tener un dominio el cual contenga HTTPS, de esta manera no se transmite la información de forma clara
2. Se debe generar un certificado SSL/TLS para el dominio con cortos tiempos de expiración (6 meses a 1 año)
3. En las soluciones se debe habilitar CORS, se configura el nombre del dominio o dominios y los verbos por ejemplo los de API Rest  (POST, GET, PUT, PATCH, DELETE)
4. Se debe redireccionar las peticiones de HTTP a HTTPS
5. Validar que puertos no esten activos para cerrarlos
