Pseudo-Code for Authentication System

´´´
FUNCTION authenticateUser(username, password):
  QUERY database WITH username AND password
  IF found RETURN True
  ELSE RETURN False

´´´


´´´
CREATE TABLE users (
    id INT AUTO_INCREMENT PRIMARY KEY,
    username VARCHAR(100) UNIQUE NOT NULL,
    password VARCHAR(255) NOT NULL
);

CREATE FUNCTION authenticateUser(username VARCHAR(100), password VARCHAR(200)) RETURNS BOOLEAN
BEGIN
    -- Recuperamos el hash de la contraseña del usuario
    SELECT password INTO @password_hash
    FROM users
    WHERE username = @username;

    -- Valida si existe
    IF @password_hash IS NULL THEN
        return false;
    ELSE
        -- Se genera contraseña ingresada para compararlo con el hash almacenado
        SET @input_hash = SHA2(@input_password, 256);

        -- Comparamos el hash de la contraseña ingresada con el hash almacenado en la base de datos
        IF @input_hash = @password_hash THEN
            return true;
        ELSE
            return false;
        END IF;
    END IF;
END;

´´´

1. SAST se utiliza para validar vulnerabilidades como inyeccion de SQL en campos asi como pruebas de diccionario (validar muchas contraseñas)
2. Es necesario almacenar contraseñas con seguridad con algoritmos con bcrupt o aes, donde exista llaves publicas y privadas
3. Debe exisitir un valor donde almacenen intentos y tenga bloqueos blandos y fuerte
4. La contraseña no puede ser desencriptada ni por los creadores del sistema
5. Debe existir una manera de bloquear ips por peticiones concurrentes
6. Se recomenda hacer conexiones SSL (HTTPS)