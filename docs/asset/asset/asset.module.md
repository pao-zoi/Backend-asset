# asset.module
# asset
Se utiliza para organizar y definir los módulos de la aplicación como tambien para agrupar y encapsular componentes relacionados, como controladores, servicios y proveedores.
```ts
import { Module } from '@nestjs/common';
import { AssetService } from './asset.service';
import { AssetController } from './asset.controller';
import { MongooseModule } from '@nestjs/mongoose';
import { Asset, AssetSchema } from './schema/asset.schema';
import { DepreciationAssetList, DepreciationAssetListSchema } from 'src/depreciation-asset-list/schema/depreciation-asset';
import { UFVService } from './ufv.service';
import { HttpModule } from '@nestjs/axios';
import { DepreciationService } from './depreciation.service';
import { HandlerUfvDepresiationService } from './handler.ufv.depres.service';
import { Supplier, SupplierSchema } from 'src/supplier/schema/supplier.schema';
```
definir módulos en una aplicación NestJS
```ts
@Module({
  imports: [MongooseModule.forFeature(
    [
      { name: Asset.name, schema: AssetSchema},
      { name: DepreciationAssetList.name, schema: DepreciationAssetListSchema},
      { name: Supplier.name, schema:SupplierSchema}
    ]
  ),
  HttpModule,
],
  controllers: [AssetController],
  providers: [AssetService, UFVService, DepreciationService,HandlerUfvDepresiationService]
})
export class AssetModule {}
```
1. Se crean las importaciones necesarias para definir controladores, rutas y realizar solicitudes HTTP en una aplicación NestJS.
```ts
import { Module } from '@nestjs/common';
import { AssetService } from './asset.service';
import { AssetController } from './asset.controller';
import { MongooseModule } from '@nestjs/mongoose';
import { Asset, AssetSchema } from './schema/asset.schema';
import { DepreciationAssetList, DepreciationAssetListSchema } from 'src/depreciation-asset-list/schema/depreciation-asset';
import { UFVService } from './ufv.service';
import { HttpModule } from '@nestjs/axios';
import { DepreciationService } from './depreciation.service';
import { HandlerUfvDepresiationService } from './handler.ufv.depres.service';
import { Supplier, SupplierSchema } from 'src/supplier/schema/supplier.schema';
```
2. Define módulos en una aplicación NestJS.
```ts
@Module({
  .......
  })
```
3. Importa servicios utilizados por el módulo.
```ts
imports: [MongooseModule.forFeature(
    [
      { name: Asset.name, schema: AssetSchema},
      { name: DepreciationAssetList.name, schema: DepreciationAssetListSchema},
      { name: Supplier.name, schema:SupplierSchema}
    ]
  ),
  HttpModule,
],
```
4. Arreglos que se instalan dentro del contexto del módulo.
```ts
providers: [AssetService, UFVService, DepreciationService,HandlerUfvDepresiationService]
```
5. Importa y agrega al controlador AssetController al arreglo de controladores.
```ts
controllers: [AssetController],
```
6. Se exporta la AssetModule
```ts
export class AssetModule {}
```

