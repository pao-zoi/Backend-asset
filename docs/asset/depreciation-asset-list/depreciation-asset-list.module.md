# depreciation-asset-list.module
# depreciation-asset-list
Se utiliza para organizar y definir los módulos de la aplicación como tambien para agrupar y encapsular componentes relacionados, como controladores, servicios y proveedores.
```ts
import { Module } from '@nestjs/common';
import { DepreciationAssetListService } from './depreciation-asset-list.service';
import { DepreciationAssetListController } from './depreciation-asset-list.controller';
import { MongooseModule } from '@nestjs/mongoose';
import { DepreciationAssetList, DepreciationAssetListSchema } from './schema/depreciation-asset';
import { HttpModule } from '@nestjs/axios';

@Module({
    imports:[MongooseModule.forFeature([
    {name: DepreciationAssetList.name, schema: DepreciationAssetListSchema}
    ]),
    HttpModule,
    ],
    controllers: [DepreciationAssetListController],
    providers: [DepreciationAssetListService]
})

export class DepreciationAssetListModule {}
```
1. Se crean las importaciones necesarias para definir controladores, rutas y realizar solicitudes HTTP en una aplicación NestJS.
```ts
import { Module } from '@nestjs/common';
import { DepreciationAssetListService } from './depreciation-asset-list.service';
import { DepreciationAssetListController } from './depreciation-asset-list.controller';
import { MongooseModule } from '@nestjs/mongoose';
import { DepreciationAssetList, DepreciationAssetListSchema } from './schema/depreciation-asset';
import { HttpModule } from '@nestjs/axios';
```
2. Define módulos en una aplicación NestJS.
```ts
@Module({
  .......
  })
```
3. Importa servicios utilizados por el módulo.
```ts
imports:[MongooseModule.forFeature([
    {name: DepreciationAssetList.name, schema: DepreciationAssetListSchema}
    ]),
    HttpModule,
],
```
4. Arreglos que se instalan dentro del contexto del módulo.
```ts
providers: [DepreciationAssetListService]
```
5. Importa y agrega al controlador DepreciationAssetListController al arreglo de controladores.
```ts
controllers: [DepreciationAssetListController],
```
6. Se exporta la DepreciationAssetListModule.
```ts
export class DepreciationAssetListModule {}
```
