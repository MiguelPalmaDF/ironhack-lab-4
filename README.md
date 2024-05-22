# ironhack-lab-4

# Lab: Application Security Techniques

### 1. Security Scanning Report

#### Scenario 1: Pseudo-Code for Authentication System

**Pseudo-Code Example:**
```pseudocode
FUNCTION authenticateUser(username, password):
  QUERY database WITH username AND password
  IF found RETURN True
  ELSE RETURN False
```

**Vulnerabilidades:**
1. **SQL Injection:** El código no sanea ni parametriza los inputs `username` y `password`, lo que lo hace susceptible a ataques de inyección SQL.
2. **Falta de Hashing de Contraseñas:** Las contraseñas se comparan en texto plano, lo cual es inseguro si la base de datos es comprometida.
3. **Lógica de Autenticación Débil:** La función solo devuelve un booleano, proporcionando un feedback mínimo y potencialmente exponiendo el sistema a ataques de fuerza bruta.

**Soluciones Propuestas:**
1. **Saneamiento y Parametrización de Inputs:**
   - Utilizar declaraciones preparadas o consultas parametrizadas para prevenir inyecciones SQL.
   ```pseudocode
   FUNCTION authenticateUser(username, password):
     QUERY database WITH parameterized query "SELECT * FROM users WHERE username = ? AND password = ?"
     IF found RETURN True
     ELSE RETURN False
   ```

2. **Hashing de Contraseñas:**
   - Almacenar contraseñas hasheadas en la base de datos y comparar los valores hasheados.
   ```pseudocode
   FUNCTION authenticateUser(username, password):
     hashedPassword = hash(password)
     QUERY database WITH parameterized query "SELECT * FROM users WHERE username = ? AND password = ?"
     IF found RETURN True
     ELSE RETURN False
   ```

3. **Lógica de Autenticación Mejorada:**
   - Implementar respuestas más detalladas y logging para los intentos de autenticación.
   ```pseudocode
   FUNCTION authenticateUser(username, password):
     hashedPassword = hash(password)
     userRecord = QUERY database WITH parameterized query "SELECT * FROM users WHERE username = ? AND password = ?"
     IF userRecord found:
       LOG successful login
       RETURN True
     ELSE:
       LOG failed login attempt
       RETURN False
   ```

### 2. JWT Authentication Design Document

#### Scenario 2: JWT Authentication Schema

**Design Outline:**
```pseudocode
DEFINE FUNCTION generateJWT(userCredentials):
  IF validateCredentials(userCredentials):
    SET tokenExpiration = currentTime + 3600 // Token expires in one hour
    SET tokenPayload = {
      "sub": userCredentials.userId,
      "name": userCredentials.username,
      "iat": currentTime,
      "exp": tokenExpiration
    }
    RETURN encrypt(tokenPayload, secretKey)
  ELSE:
    RETURN error
```

**Diseño Mejorado para la Seguridad:**
1. **Generación Segura de Tokens:**
   - Usar una clave secreta fuerte almacenada de manera segura y rotarla periódicamente.
   - Asegurarse de que el payload contenga solo la información necesaria para evitar la exposición de datos sensibles.

2. **Gestión de la Expiración del Token:**
   - Implementar refresh tokens para extender las duraciones de las sesiones de manera segura.
   - Establecer tiempos de expiración razonables y monitorear tokens expirados.

3. **Validación de Tokens:**
   - Utilizar bibliotecas seguras para validar el JWT, asegurando que no haya sido manipulado.
   - Implementar revocación de tokens para tokens comprometidos.

**Ejemplo de Diseño Mejorado:**
```pseudocode
DEFINE FUNCTION generateJWT(userCredentials):
  IF validateCredentials(userCredentials):
    SET tokenExpiration = currentTime + 3600 // Token expira en una hora
    SET tokenPayload = {
      "sub": userCredentials.userId,
      "name": userCredentials.username,
      "iat": currentTime,
      "exp": tokenExpiration
    }
    RETURN signJWT(tokenPayload, secretKey)
  ELSE:
    RETURN error

DEFINE FUNCTION validateJWT(token):
  TRY:
    DECODE token WITH secretKey
    IF token.exp < currentTime:
      RETURN error "Token expired"
    RETURN tokenPayload
  CATCH error:
    RETURN error "Invalid token"
```

### 3. Data Protection Strategy Document

#### Scenario 3: Secure Data Communication Plan

**Outline for Data Protection:**

```
PLAN secureDataCommunication:
  IMPLEMENT SSL/TLS for all data in transit
  USE encrypted storage solutions for data at rest
  ENSURE all data exchanges comply with HTTPS protocols
```

**SOLUCION**

**Datos en Tránsito:**
- **Implementar SSL/TLS:** Asegurar que todos los datos transmitidos a través de la red estén cifrados usando SSL/TLS.
  - **Ejemplo de Configuración:**
    ```pseudocode
    CONFIGURE server:
      ENABLE SSL/TLS with strong ciphers
      USE certificates from a trusted Certificate Authority (CA)
    ```

**Datos en Reposo:**
- **Utilizar Soluciones de Almacenamiento Encriptado:** Encriptar los datos sensibles almacenados en bases de datos y sistemas de archivos.
  - **Ejemplo de Configuración:**
    ```pseudocode
    CONFIGURE database:
      ENABLE encryption for data storage
      USE encryption keys managed by a secure key management system
    ```

**Protocolos de Comunicación de Datos:**
- **Asegurar Protocolos HTTPS:** Todas las comunicaciones HTTP deben estar aseguradas utilizando HTTPS.
  - **Ejemplo de Configuración:**
    ```pseudocode
    CONFIGURE web server:
      REDIRECT all HTTP traffic to HTTPS
      USE HTTP Strict Transport Security (HSTS)
    ```

**Medidas de Seguridad Adicionales:**
- **Auditorías y Monitoreo Regular:** Implementar auditorías de seguridad regulares y monitoreo continuo para actividades sospechosas.
- **Controles de Acceso Fuertes:** Asegurar que solo el personal autorizado tenga acceso a los datos y sistemas sensibles.

**Plan Estratégico para la Protección del Almacenamiento y Transmisión de Datos:**
1. **Implementar SSL/TLS para todos los datos en tránsito.**
   - Todas las comunicaciones entre clientes y servidores, y entre servicios internos, deben usar SSL/TLS.
   - Actualizar y renovar regularmente los certificados SSL/TLS.
   - Utilizar cifrados fuertes y modernos y deshabilitar los protocolos obsoletos.

2. **Usar soluciones de almacenamiento encriptado para datos en reposo.**
   - Encriptar todos los datos sensibles almacenados en bases de datos utilizando algoritmos de encriptación robustos.
   - Utilizar herramientas de encriptación dedicadas o características de encriptación integradas en la base de datos.
   - Gestionar las claves de encriptación de manera segura utilizando un servicio de gestión de claves (KMS).

3. **Asegurar que todos los intercambios de datos cumplan con los protocolos HTTPS.**
   - Configurar servidores web para forzar HTTPS en todas las comunicaciones.
   - Implementar HSTS para asegurar que los navegadores solo se comuniquen a través de HTTPS.
   - Utilizar cookies y cabeceras seguras para proteger contra vulnerabilidades web comunes.
