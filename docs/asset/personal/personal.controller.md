# personal.controller
## personal
El controlador realiza las importaciones necesarias para definir controladores, rutas y realizar solicitudes HTTP en una aplicación NestJS.
```ts
import { HttpService } from '@nestjs/axios';
import { Controller, Get } from '@nestjs/common';
import { ApiTags } from '@nestjs/swagger';
import getConfig from '../config/configuration'
@ApiTags('personals')
@Controller('personal')
export class PersonalController {

  constructor(private httpService:HttpService ) {}

  @Get()
  async findAll() {
    const response = await this.httpService.get(`${getConfig().api_personal}api/personal/activos`).toPromise();
    console.log(response.data)
    return response.data
  }

}
```
1. Se crean las importaciones necesarias para definir controladores, rutas y realizar solicitudes HTTP en una aplicación NestJS
```ts
import { HttpService } from '@nestjs/axios';
import { Controller, Get } from '@nestjs/common';
import { ApiTags } from '@nestjs/swagger';
import getConfig from '../config/configuration'
```
2. Es un decorador y se utiliza para agregar etiquetas a la documentación generada por Swagger para la API.
```ts
@ApiTags('personals')
```
3. sirve para crear peticiones http
```ts
@Controller('personal')
```
4. Importa los servicios para utilizar en DepreciationAssetListService.
```ts
  constructor(private httpService:HttpService ) {}
```
5. El método findAll en el controlador simplemente devuelve el resultado obtenido del servicio, sin realizar ninguna manipulación adicional. 
```ts
@Get()
  async findAll() {
    const response = await this.httpService.get(`${getConfig().api_personal}api/personal/activos`).toPromise();
    console.log(response.data)
    return response.data
  }
```
