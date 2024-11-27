# ExamenFinal_Cripto
---

## **Resumen del Proyecto**
Este proyecto implementa un ransomware simulado como parte del examen final de criptografía. El ransomware aplica conceptos avanzados de criptografía, como intercambio de llaves, cifrado simétrico y asimétrico, y firma digital. Es una simulación diseñada exclusivamente con fines educativos.

---

## **Estructura del Proyecto**
El código está dividido en las siguientes secciones principales:
1. **Preparación del Entorno**: Instalación de bibliotecas necesarias.
2. **Generación de Claves y Certificados**: Creación de claves RSA y certificados digitales para el atacante y la víctima.
3. **Funciones Criptográficas**: Implementación de cifrado y descifrado, firma digital, y generación de claves derivadas.
4. **Operaciones del Ransomware**: Cifrado de archivos y notificación a la víctima.
5. **Recuperación de Archivos**: Verificación de integridad y descifrado posterior al pago.

---

## **Descripción de Funcionalidades**

### 1. **Instalación de Dependencias**
```python
!pip install pycryptodome
!pip install cryptography
```
Se asegura que las bibliotecas necesarias para la criptografía estén disponibles.

---

### 2. **Generación de Claves y Certificados**
```python
def generar_claves_rsa():
    return RSA.generate(2048)

def crear_certificado(key, identifier):
    public_key = key.publickey().export_key()
    return {'id': identifier, 'pk': public_key}
```
- **`generar_claves_rsa`**: Genera un par de claves RSA de 2048 bits.
- **`crear_certificado`**: Crea un certificado digital basado en una clave pública y un identificador.

---

### 3. **Cifrado y Descifrado RSA**
```python
def encriptar_rsa(public_key, data):
    cipher = PKCS1_OAEP.new(public_key)
    return cipher.encrypt(data)

def desencriptar_rsa(private_key, encrypted_data):
    cipher = PKCS1_OAEP.new(private_key)
    return cipher.decrypt(encrypted_data)
```
- Utiliza RSA en modo PKCS1_OAEP para cifrar y descifrar datos.

---

### 4. **Firma y Verificación de Datos**
```python
def firmar_datos(private_key, data):
    h = SHA256.new(data)
    signature = pkcs1_15.new(private_key).sign(h)
    return signature

def verificar_firma(public_key, data, signature):
    h = SHA256.new(data)
    try:
        pkcs1_15.new(public_key).verify(h, signature)
        return True
    except (ValueError, TypeError):
        return False
```
- Genera firmas digitales usando RSA con PKCS#1 v1.5 y verifica la autenticidad de los datos.

---

### 5. **Cifrado Simétrico con AES**
```python
def generar_clave_aes_a_partir_de_contrasena(password: bytes, salt: bytes = None):
    if salt is None:
        salt = b'' 
    kdf = PBKDF2HMAC(
        algorithm=hashes.SHA256(),
        length=32,
        salt=salt,
        iterations=100000,
        backend=default_backend()
    )
    return kdf.derive(password)

def encriptar_archivo(file_path, aes_key):
    block_size = AES.block_size
    with open(file_path, 'rb') as file:
        plaintext = file.read()
    cipher = AES.new(aes_key, AES.MODE_CBC)
    ciphertext = cipher.encrypt(pad(plaintext, block_size))
    return ciphertext
```
- **`generar_clave_aes_a_partir_de_contrasena`**: Deriva una clave AES de una contraseña.
- **`encriptar_archivo`**: Cifra un archivo usando AES en modo CBC.

---

### 6. **Recuperación y Verificación de Integridad**
```python
def verificar_integridad_archivo(original_hash, file_path):
    with open(file_path, 'rb') as file:
        data = file.read()
    current_hash = hashlib.sha256(data).hexdigest()
    return original_hash == current_hash
```
- Verifica que los archivos descifrados no hayan sido alterados.

---

## **Instrucciones de Ejecución**
1. Instala las dependencias ejecutando las primeras celdas.
2. Genera las claves y certificados usando las funciones proporcionadas.
3. Ejecuta el cifrado de archivos específicos simulando el ataque.
4. Utiliza las funciones de descifrado para restaurar los archivos después de simular el pago.

