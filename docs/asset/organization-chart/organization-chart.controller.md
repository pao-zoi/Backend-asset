# organization-chart.controller
## organization-chart
El controlador realiza las importaciones necesarias para definir controladores, rutas y realizar solicitudes HTTP en una aplicación NestJS.
```ts
import { Controller, Get, Post, Body, Patch, Param, Delete} from '@nestjs/common';
import { HttpService } from '@nestjs/axios';
import { getConfigToken } from '@nestjs/config';
import { ApiTags } from '@nestjs/swagger';

@ApiTags('organigrama')
@Controller('organization-chart')
export class OrganizationChartController {
  constructor(private httpService: HttpService) {}
  @Get()
  async findAll() {
    const response = await this.httpService.get(`${getConfig().api_organigrama}main/${location}`).toPromise();

    const arrayInicial = [...response.data];
    const arrayFinal = [];

    await this.obtenerIdsYNames(arrayInicial, arrayFinal);

    return arrayFinal;
  }

  async obtenerIdsYNames(objetos, arrayFinal) {
    for (const objeto of objetos) {
      await this.procesarObjeto(objeto, arrayFinal);
    }
  }

  async procesarObjeto(objeto, arrayFinal) {
    if (objeto.hasOwnProperty('_id') && objeto.hasOwnProperty('name')) {
      const item = {
        _id: objeto._id,
        name: objeto.name,
      };
      arrayFinal.push(item);
    }

    if (objeto.hasOwnProperty('children') && Array.isArray(objeto.children)) {
      for (const subhijo of objeto.children) {
        await this.procesarObjeto(subhijo, arrayFinal);
      }
    }
  }
}
```
1. Se crean las importaciones necesarias para definir controladores, rutas y realizar solicitudes HTTP en una aplicación NestJS
```ts
import { Controller, Get, Post, Body, Patch, Param, Delete} from '@nestjs/common';
import { HttpService } from '@nestjs/axios';
import { getConfigToken } from '@nestjs/config';
import { ApiTags } from '@nestjs/swagger';
```
2. Es un decorador y se utiliza para agregar etiquetas a la documentación generada por Swagger para la API.
```ts
@ApiTags('organigrama')
```
3. sirve para crear peticiones http
```ts
@Controller('organization-chart')
```
4. Es un servicio proporcionado por Nest.js que permite realizar solicitudes HTTP a otros servicios
```ts
constructor(private httpService: HttpService) {}
```
5. El método findAll en el controlador se encarga de obtener todos los elementos disponibles haciendo una solicitud HTTP.
- Crea una copia del arreglo de datos obtenido de la respuesta HTTP y lo asigna a la variable arrayInicial.
- const arrayFinal = []: Crea un arreglo vacío llamado arrayFinal, que se utilizará para almacenar los resultados finales.

- await this.obtenerIdsYNames(arrayInicial, arrayFinal): Llama al método obtenerIdsYNames y espera a que se resuelva. Este método toma el arrayInicial y arrayFinal como argumentos y realiza alguna lógica de procesamiento para llenar arrayFinal.

- return arrayFinal: Devuelve el arrayFinal como resultado de la solicitud HTTP.
```ts
@Get()
  async findAll() {
    const response = await this.httpService.get(`${getConfig().api_organigrama}main/${location}`).toPromise();

    const arrayInicial = [...response.data];
    const arrayFinal = [];

    await this.obtenerIdsYNames(arrayInicial, arrayFinal);

    return arrayFinal;
  }
```
6. Es una función asincrónica que recibe un arreglo de objetos y un arrayFinal
- Con el For iteramos sobre cada objeto
- Devuelve una promesa 
```ts
async obtenerIdsYNames(objetos, arrayFinal) {
    for (const objeto of objetos) {
      await this.procesarObjeto(objeto, arrayFinal);
    }
  }
```
7. Es una función asincrónica que recibe un objeto y un arrayFinal.
- Verifica si el objeto tiene las propiedades _id y name.
- Crea un nuevo objeto item con las propiedades _id y name 
- Almacena los resultados
- Verifica que las condiciones se cumplan 
- Devuelve una promesa 
```ts
async procesarObjeto(objeto, arrayFinal) {
    if (objeto.hasOwnProperty('_id') && objeto.hasOwnProperty('name')) {
      const item = {
        _id: objeto._id,
        name: objeto.name,
      };
      arrayFinal.push(item);
    }

    if (objeto.hasOwnProperty('children') && Array.isArray(objeto.children)) {
      for (const subhijo of objeto.children) {
        await this.procesarObjeto(subhijo, arrayFinal);
      }
    }
  }
```

