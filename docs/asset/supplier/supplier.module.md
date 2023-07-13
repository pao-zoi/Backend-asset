# supplier.module
## supplier
Se utiliza para organizar y definir los módulos de la aplicación como tambien para agrupar y encapsular componentes relacionados, como controladores, servicios y proveedores.
```ts
import { Module } from '@nestjs/common';
import { SupplierService } from './supplier.service';
import { SupplierController } from './supplier.controller';
import { HttpModule } from '@nestjs/axios';
import { MongooseModule } from '@nestjs/mongoose';
import { Supplier, SupplierSchema } from './schema/supplier.schema';
import { Asset, AssetSchema } from 'src/asset/schema/asset.schema';

@Module({
  imports:[
    MongooseModule.forFeature([
      {name:Supplier.name, schema: SupplierSchema},
      {name:Asset.name, schema: AssetSchema},
    ]),  
    HttpModule
  ],
  controllers: [SupplierController],
  providers: [SupplierService]
})
export class SupplierModule {}
```
1. Se crean las importaciones necesarias para definir controladores, rutas y realizar solicitudes HTTP en una aplicación NestJS.
```ts
import { Module } from '@nestjs/common';
import { SupplierService } from './supplier.service';
import { SupplierController } from './supplier.controller';
import { HttpModule } from '@nestjs/axios';
import { MongooseModule } from '@nestjs/mongoose';
import { Supplier, SupplierSchema } from './schema/supplier.schema';
import { Asset, AssetSchema } from 'src/asset/schema/asset.schema';
```
2. Define módulos en una aplicación NestJS.
```ts
@Module({
  .......
  })
```
3. Importa servicios utilizados por el módulo.
```ts
imports:[
    MongooseModule.forFeature([
      {name:Supplier.name, schema: SupplierSchema},
      {name:Asset.name, schema: AssetSchema},
    ]),  
    HttpModule
],
```
4. Arreglos que se instalan dentro del contexto del módulo.
```ts
  providers: [SupplierService]
```
5. Importa y agrega al controlador SupplierController al arreglo de controladores.
```ts
controllers: [SupplierController],
```
6. Se exporta la SupplierModule.
```ts
export class SupplierModule {}
```

