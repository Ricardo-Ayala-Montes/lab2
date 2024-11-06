JWT Authentication Schema

´´´
DEFINE FUNCTION generateJWT(userCredentials):
  IF validateCredentials(userCredentials):
    SET tokenExpiration = currentTime + 3600 // Token expires in one hour
    RETURN encrypt(userCredentials + tokenExpiration, secretKey)
  ELSE:
    RETURN error
´´´

´´´

CREATE FUNCTION getSecretKey() RETURNS VARCHAR
BEGIN

    -- Valor unico para key
    SELECT secretKey INTO @secretKey
    FROM TABLE_CONFIG;

    return @secretKey;

END;

CREATE FUNCTION validateCredentialsSecurely(userCredencials VARCHAR(500)) RETURNS BOOLEAN
BEGIN

    -- Valida las credenciales
    IF validateCredentials(userCredentials) THEN
        return true;
    ELSE
        return false;
    END IF;

END;


CREATE FUNCTION generateJWT(userCredencials VARCHAR(500)) RETURNS VARCHAR
BEGIN
    -- Se validan las credenciales
    IF validateCredentialsSecurely(userCredencials) THEN
        -- Configuración de tiempo de expiración
        SET @tokenExpiration =  currentTime + 3600;
        -- Valor de secretKey
        SET @secretKey = getSecretKey();

        return encrypt(userCredencials + tokenExpiration, @secretKey);
    ELSE
        return 'Error';
    END IF;

END;

´´´

1. El JWT conforma por 3 partes que es el Header, Payload (Claims) y Firma.
2. En los datos de Payload que son los claims no se debe colocar información confidencial
3. La firma (signature) contiene un secretkey que no se puede modificar si no lo conoces
4. utilizar jwt.io para validar jwt

