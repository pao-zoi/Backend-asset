# configuration
## config
Exporta una función predeterminada que devuelve un objeto con varias propiedades de configuración.
```ts
export default () => ({
    port: process.env.PORT || 8080,
    
    api_seguridad: process.env.API_SEGURIDAD,
    
    mongodb: process.env.DATABASE_URL,
    
    db_name:process.env.DB_NAME,

    verify_token: process.env.API_MAIN_VERIFY_TOKEN,

    file_upload:process.env.API_FILE_UPLOAD,

    api_personal:process.env.API_PERSONAL,

    api_organigrama:process.env.API_ORGANIGRAMA,
  
  });
  ```
1. Esta propiedad representa el número de puerto en el que correra la aplicación.
```ts
port: process.env.PORT || 8080,
```
2. Toma el valor de la API_SEGURIDAD que es el punto final de una API relacionada con la segurida.
```ts
api_seguridad: process.env.API_SEGURIDAD,
```
3. Utiliza el valor de la DATABASE_URLvariable de entorno para la cadena de conexión para la base de datos.
```ts
mongodb: process.env.DATABASE_URL,
```
4. Esta propiedad representa el nombre de la base de datos que se usará dentro de MongoDB. Utiliza el valor de la DB_NAME variable de entorno.
```ts
db_name:process.env.DB_NAME,
```
3. Toma el valor de la API_MAIN_VERIFY_TOKEN variable de entorno.
```ts
 verify_token: process.env.API_MAIN_VERIFY_TOKEN,
```
4. Toma el valor de la API_FILE_UPLOAD variable de entorno.
```ts
file_upload:process.env.API_FILE_UPLOAD,
```
5. Toma el valor de la API_PERSONAL variable de entorno.
```ts
api_personal:process.env.API_PERSONAL,
```
6. Toma el valor de la API_ORGANIGRAMAvariable de entorno.
```ts
api_organigrama:process.env.API_ORGANIGRAMA,
```


