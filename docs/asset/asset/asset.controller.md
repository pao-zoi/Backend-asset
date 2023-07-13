# asset.controller
# asset
El controlador realiza las importaciones necesarias para definir controladores, rutas y realizar solicitudes HTTP en una aplicación NestJS.
```ts
import { Controller, Get, Post, Body, Patch, Param, Delete,  UseInterceptors, UploadedFile, Put,  UploadedFiles, Res, UseGuards } from '@nestjs/common';
import { AssetService } from './asset.service';
import { CreateAssetDto } from './dto/create-asset.dto';
import { ApiBearerAuth, ApiTags } from '@nestjs/swagger';
import { FileFieldsInterceptor } from '@nestjs/platform-express';
import { HttpService } from '@nestjs/axios';
import getConfig from '../config/configuration'
import { Response } from 'express';
import { Roles } from 'src/guard/decorators/roles.decorator';
import { RolesGuard } from 'src/guard/roles.guard';
import { UserRole } from 'src/guard/constants/roles';
@ApiTags('asset')
@Controller('asset')
export class AssetController {
  //importa los servicios para utilizar en assetService
  constructor(
    private readonly assetService: AssetService,
    private httpService:HttpService
    ) {}
  @Post()
  async create(@Body() createAssetDto: CreateAssetDto, @Res() res: Response) {
    const { file } = createAssetDto
    if(file){
      const mimeType = file.split(';')[0].split(':')[1].split('/')[1];
      const base64 = file.split(',')[1];
      const fileObj = {
        mime: mimeType,
        base64: base64
      };

      try {
        const res = await this.httpService.post(`${getConfig().file_upload}files/upload`, { file:fileObj }).toPromise()
        createAssetDto = {...createAssetDto ,file:res.data.file._id }
      } catch (error) {
        throw error.response?.data
      }
    } 
    const createdAsset = await this.assetService.create(createAssetDto);
    res.status(200).send(createdAsset)
  }

  @Get()
  async findAll() {
    return await this.assetService.findAll();    
  }

  @Get(':id')  
  async findOne(@Param('id') id: string) {
    return await this.assetService.findOne(id);
  }

  @Put(':id') 
    async update(@Param('id') id: string, @Body() updateAssetDto: CreateAssetDto) {
    return await this.assetService.update(id, updateAssetDto);
  }

  @Delete(':id')
  async darDeBaja(@Param('id') id: string, @Res() res: Response) {
    await this.assetService.darDeBaja(id);
    res.send({message:'activo eliminado correctamente'})
  } 
}
```
1. Se crean las importaciones necesarias para definir controladores, rutas y realizar solicitudes HTTP en una aplicación NestJS 
```ts
import { Controller, Get, Post, Body, Patch, Param, Delete,  UseInterceptors, UploadedFile, Put,  UploadedFiles, Res, UseGuards } from '@nestjs/common';
import { AssetService } from './asset.service';
import { CreateAssetDto } from './dto/create-asset.dto';
import { ApiBearerAuth, ApiTags } from '@nestjs/swagger';
import { FileFieldsInterceptor } from '@nestjs/platform-express';
import { HttpService } from '@nestjs/axios';
import getConfig from '../config/configuration'
import { Response } from 'express';
import { Roles } from 'src/guard/decorators/roles.decorator';
import { RolesGuard } from 'src/guard/roles.guard';
import { UserRole } from 'src/guard/constants/roles';
```
2. Es un decorador y se utiliza para agregar etiquetas a la documentación generada por Swagger para la API.
```ts
@ApiTags('asset')
```
3. sirve para crear peticiones http
```ts
@Controller('asset')
```
4. Importa los servicios para utilizar en assetService
```ts
constructor(
  private readonly assetService: AssetService,
  private httpService:HttpService
) {}
```
5. El código carga los archivos extrayendo la información necesaria de la file, enviándola a un punto final de API para cargar y luego asociando el ID del archivo cargado con el activo que se está creando.
```ts
@Post()
  async create(@Body() createAssetDto: CreateAssetDto, @Res() res: Response) {
    const { file } = createAssetDto
    if(file){
      const mimeType = file.split(';')[0].split(':')[1].split('/')[1];
      const base64 = file.split(',')[1];
      const fileObj = {
        mime: mimeType,
        base64: base64
      };
      try {
        const res = await this.httpService.post(`${getConfig().file_upload}files/upload`, { file:fileObj }).toPromise()
        createAssetDto = {...createAssetDto ,file:res.data.file._id }
      } catch (error) {
        throw error.response?.data
      }
    } 
    const createdAsset = await this.assetService.create(createAssetDto);
    res.status(200).send(createdAsset)
  }
```
6. Obtiene todos los activos disponibles
```ts
@Get()
  async findAll() {
    return await this.assetService.findAll();    
  }
```
7. Obtiene los activos con un id
```ts
@Get()
  @Get(':id')  
  async findOne(@Param('id') id: string) {
    return await this.assetService.findOne(id);
  }
```
8. busca el activo que quiere actualizar y actuliza con los datos proporcionados y espera la actualizacion con update y se muestra el resutado
```ts
@Put(':id')
  async update(@Param('id') id: string, @Body() updateAssetDto: CreateAssetDto) {
    return await this.assetService.update(id, updateAssetDto);
  }
```
9. Buscar el id y res puesta al ususario, tambien cambia de estado del activo y envia una respuesta al usuario
```ts
@Delete(':id')
  async darDeBaja(@Param('id') id: string, @Res() res: Response) {
    await this.assetService.darDeBaja(id);
    res.send({message:'activo eliminado correctamente'})
  }
```







