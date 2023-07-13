# depreciation-asset-list.controller
# depreciation-asset-list
El controlador realiza las importaciones necesarias para definir controladores, rutas y realizar solicitudes HTTP en una aplicación NestJS.
```ts
import { Controller, Get, Post, Body, Patch, Param, Delete, Put, UseGuards, Res, HttpStatus } from '@nestjs/common';
import { DepreciationAssetListService } from './depreciation-asset-list.service';
import { CreateDepreciationAssetListDto } from './dto/create-depreciation-asset-list.dto';
import { ApiBearerAuth, ApiTags } from '@nestjs/swagger';
import { Roles } from 'src/guard/decorators/roles.decorator';
import { UserRole } from 'src/guard/constants/roles';
import { RolesGuard } from 'src/guard/roles.guard';

@ApiTags('depreciation')
@Controller('depreciation-asset-list')
export class DepreciationAssetListController {
  constructor(private readonly depreciationAssetListService: DepreciationAssetListService) {}

  @Post()
  create(@Body() createDepreciationAssetListDto: CreateDepreciationAssetListDto) {
    return this.depreciationAssetListService.create(createDepreciationAssetListDto);
  }

  @Get()
  findAll() {
    return this.depreciationAssetListService.findAll();
  }

  @Get(':id')
  findOne(@Param('id') id: string) {
    return this.depreciationAssetListService.findOne(id);
  }

  @Put(':id')
  update(@Param('id') id: string, @Body() updateDepreciationAssetListDto) {
    return this.depreciationAssetListService.update(id, updateDepreciationAssetListDto);
  }
  
  @Delete(':id')
  async darDeBaja(@Param('id') id: string, @Res() res: any) {
    await this.depreciationAssetListService.darDeBaja(id);
    res.status(HttpStatus.OK).send({ message: 'Activo dado de baja correctamente' });
  }
}
```
1. Se crean las importaciones necesarias para definir controladores, rutas y realizar solicitudes HTTP en una aplicación NestJS
```ts
import { Controller, Get, Post, Body, Patch, Param, Delete, Put, UseGuards } from '@nestjs/common';
import { DepreciationAssetListService } from './depreciation-asset-list.service';
import { CreateDepreciationAssetListDto } from './dto/create-depreciation-asset-list.dto';
import { ApiBearerAuth, ApiTags } from '@nestjs/swagger';
import { Roles } from 'src/guard/decorators/roles.decorator';
import { UserRole } from 'src/guard/constants/roles';
import { RolesGuard } from 'src/guard/roles.guard';
```
2. Es un decorador y se utiliza para agregar etiquetas a la documentación generada por Swagger para la API.
```ts
@ApiTags('depreciation')
```
3. sirve para crear peticiones http
```ts
@Controller('depreciation-asset-list')
```
4. Importa los servicios para utilizar en DepreciationAssetListService.
```ts
constructor(private readonly depreciationAssetListService: DepreciationAssetListService) {}
```
5. El método create en el controlador recibe los datos de una nueva lista de depreciación de activos, los pasa al servicio correspondiente y devuelve el resultado del proceso de creación.
```ts
@Post()
  create(@Body() createDepreciationAssetListDto: CreateDepreciationAssetListDto) {
    return this.depreciationAssetListService.create(createDepreciationAssetListDto);
  }
```
6. El método findAll en el controlador simplemente devuelve el resultado obtenido del servicio, sin realizar ninguna manipulación adicional. 
```ts
@Get()
  findAll() {
    return this.depreciationAssetListService.findAll();
  }
```
7. El método findOne en el controlador simplemente devuelve el resultado obtenido del servicio, sin realizar ninguna manipulación adicional pero por el ID.
```ts
@Get(':id')
  findOne(@Param('id') id: string) {
    return this.depreciationAssetListService.findOne(id);
  }
```
8. El método update en el controlador permite que la lista de depreciación de activos actualizada se devuelva como respuesta.
```ts
@Put(':id')
  update(@Param('id') id: string, @Body() updateDepreciationAssetListDto) {
    return this.depreciationAssetListService.update(id, updateDepreciationAssetListDto);
  }
```
9. El método remove en el controlador devuelve el activo cambiado de false a true.
```ts
  @Delete(':id')
  remove(@Param('id') id: string) {
    return this.depreciationAssetListService.remove(id);
  }
```