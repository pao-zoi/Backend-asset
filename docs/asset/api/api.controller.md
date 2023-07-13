# api.controller
## api 
El controlador realiza las importaciones necesarias para definir controladores, rutas y realizar solicitudes HTTP en una aplicación NestJS
```ts
import { Body, Controller, Get, HttpException, HttpStatus, Post, Req, Res } from '@nestjs/common';
import { ApiBearerAuth, ApiTags } from '@nestjs/swagger';
import { Request, Response } from 'express';
import { HttpService } from '@nestjs/axios';
import { VerifyTokenDTO } from './dto/verify.token.dto';
import getConfig from '../config/configuration'
export class ApiController {
    constructor(
       private readonly httpService:HttpService, 
    ){}
	@Post('/redirect-to-main')
	async verifyToken(@Body() tokenObject:VerifyTokenDTO, @Res() res:Response){
		
		try {
			const {app, token} = tokenObject  
			const response = await this.httpService.post(`${getConfig().verify_token}auth/verify-app-token`,tokenObject).toPromise();		
			res.status(200).send(response.data)	
		} 
		catch (error) {
			
			if (error.response) {
				const { status, data } = error.response;
				res.status(status).send(data);
			} 
			else {
				throw new HttpException('ocurrio un error', HttpStatus.INTERNAL_SERVER_ERROR);
			}
		}		
	}
}
```
1. Se crean las importaciones necesarias para definir controladores, rutas y realizar solicitudes HTTP en una aplicación NestJS
```ts
import { Body, Controller, Get, HttpException, HttpStatus, Post, Req, Res } from '@nestjs/common';
import { ApiBearerAuth, ApiTags } from '@nestjs/swagger';
import { Request, Response } from 'express';
import { HttpService } from '@nestjs/axios';
import { VerifyTokenDTO } from './dto/verify.token.dto';
import getConfig from '../config/configuration'
```
2. Es un decorador y se utiliza para agregar etiquetas a la documentación generada por Swagger para la API.
```ts
@ApiTags('apiAuth')
```
3. Sirve para crear peticiones HTTP.
```ts
@Controller('api')
```
4. Declara un constructor para la clase ApiController. Recibe una instancia de HttpServicey la asigna a la propiedad privada httpServicede la clase.
```ts
constructor(
       private readonly httpService:HttpService, 
){}
```
5. Decora con @Post('/redirect-to-main')para indicar que maneja las solicitudes POST en la ruta '/redirect-to-main'.
```ts
@Post('/redirect-to-main')
```
6. La función del controlador se nombra verifyTokeny acepta tres parámetros:tokenObject, res y HttpException.
```ts
async verifyToken(@Body() tokenObject:VerifyTokenDTO, @Res() res:Response){}
```
7. Destruye el tockenObject y obtiene la app y tocken.
```ts
const {app, token} = tokenObject
```
8. Luego, se realiza una solicitud POST utilizando HttpServicepara verificar el token en una URL específica.
```ts
const response = await this.httpService.post(`${getConfig().verify_token}auth/verify-app-token`,tokenObject).toPromise();
```
9. Esta línea utiliza la desestructuración de objetos para extraer las propiedades status y data del objeto.
```ts
const { status, data } = error.response;
```
10. Si la solicitud es exitosa, se envía la respuesta al cliente con un estado HTTP y los datos de la respuesta.
```ts
res.status(status).send(data);
```
11. Excepción HttpException con un estado HTTP 500 (Error interno del servidor).
```ts
throw new HttpException('ocurrio un error', HttpStatus.INTERNAL_SERVER_ERROR);
```
