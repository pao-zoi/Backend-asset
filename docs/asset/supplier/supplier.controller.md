# supplier.controller
## supplier
El controlador realiza las importaciones necesarias para definir controladores, rutas y realizar solicitudes HTTP en una aplicación NestJS.
```ts
import { Controller, Get, Post, Body, Patch, Param, Delete, Put, Res, HttpException } from '@nestjs/common';
import { SupplierService } from './supplier.service';
import { CreateSupplierDto } from './dto/create-supplier.dto';
import { UpdateSupplierDto } from './dto/update-supplier.dto';
import { ApiTags } from '@nestjs/swagger';
import { Response } from 'express';

@ApiTags('supplier')
@Controller('supplier')
export class SupplierController {
  constructor(private readonly supplierService: SupplierService) {}

  @Post()
  async create(@Body() createSupplierDto: CreateSupplierDto) {
    return await this.supplierService.create(createSupplierDto);
  }

  @Get()
  async findAll() {
     const data = await this.supplierService.findAll();
     return data
  }

  @Get('/:id')
  async findOne(@Param('id') id: string) {
    try {
      return await this.supplierService.findOne(id);
    } catch (error) {
      if(error instanceof HttpException){
        throw error
      }
    }
  }

  @Put('update/:id')
  async update(@Param('id') id: string, @Body() updateSupplierDto: UpdateSupplierDto) {
    return await this.supplierService.update(id, updateSupplierDto);
  }

  @Delete(':id')
  async remove(@Param('id') id: string,@Res() res:Response) {
    const remove = await this.supplierService.remove(id);
    res.status(200).send({message:'eliminado correctamente'})
  }

  @Put('restart-supplier/:id')
  async restartSupplier(@Param('id') id: string,@Res() res:Response) {
     await this.supplierService.restartsupplier(id);
     res.status(200).send({message:'restaurado correctamente'})
  }
}
```
1. Se crean las importaciones necesarias para definir controladores, rutas y realizar solicitudes HTTP en una aplicación NestJS
```ts
import { Controller, Get, Post, Body, Patch, Param, Delete, Put, Res, HttpException } from '@nestjs/common';
import { SupplierService } from './supplier.service';
import { CreateSupplierDto } from './dto/create-supplier.dto';
import { UpdateSupplierDto } from './dto/update-supplier.dto';
import { ApiTags } from '@nestjs/swagger';
import { Response } from 'express';
```
2. Es un decorador y se utiliza para agregar etiquetas a la documentación generada por Swagger para la API.
```ts
@ApiTags('supplier')
```
3. sirve para crear peticiones http
```ts
  constructor(private readonly supplierService: SupplierService) {}
```
4. Importa los servicios para utilizar en SupplierService.
```ts
constructor(private readonly supplierService: SupplierService) {}
```
5. El método create en el controlador recibe los datos de una nueva lista de proveedores de activos, los pasa al servicio correspondiente y devuelve el resultado del proceso de creación.
```ts
@Post()
  async create(@Body() createSupplierDto: CreateSupplierDto) {
    return await this.supplierService.create(createSupplierDto);
  }
```
7. El método findOne en el controlador simplemente devuelve el resultado obtenido del servicio, sin realizar ninguna manipulación adicional pero por el ID, como tambien verifica si existe el proveedor.
```ts
@Get('/:id')
  async findOne(@Param('id') id: string) {
    try {
      return await this.supplierService.findOne(id);
    } catch (error) {
      if(error instanceof HttpException){
        throw error
      }
    }
  }
```
8. El método update en el controlador permite que la lista de proveedores de activos actualizada se devuelva como respuesta.
```ts
@Put('update/:id')
  async update(@Param('id') id: string, @Body() updateSupplierDto: UpdateSupplierDto) {
    return await this.supplierService.update(id, updateSupplierDto);
  }
```
9. El método remove en el controlador devuelve el activo cambiado de false a true por el ID.
```ts
  @Delete(':id')
  async remove(@Param('id') id: string,@Res() res:Response) {
    const remove = await this.supplierService.remove(id);
    res.status(200).send({message:'eliminado correctamente'})
  }
```
10. Restablecer el estado del proveedor de true a false.
```ts
@Put('restart-supplier/:id')
  async restartSupplier(@Param('id') id: string,@Res() res:Response) {
     await this.supplierService.restartsupplier(id);
     res.status(200).send({message:'restaurado correctamente'})
  }
```