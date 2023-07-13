# depreciation-asset
## depreciation-asset_list
Los esquemas se pueden crear con los decoradores de NestJS o con Mongoose de forma manual. El uso de decoradores para crear esquemas reduce en gran medida la repetición y mejora la legibilidad general del código.
```ts
import { Prop, Schema, SchemaFactory } from '@nestjs/mongoose';
import { HydratedDocument } from 'mongoose';

export type DepreciationAssetListDocument = HydratedDocument<DepreciationAssetList>;

@Schema()
export class DepreciationAssetList {
    @Prop({require:true})
    assetCategory: string; 
    
    @Prop({require:true})
    usefulLife: number;
}
export const DepreciationAssetListSchema = SchemaFactory.createForClass(DepreciationAssetList);
```
1. Importación de Document desde Mongoose
```ts
import { Prop, Schema, SchemaFactory } from '@nestjs/mongoose';
import { HydratedDocument } from 'mongoose';
```
2. Definición del tipo de un documento DepreciationAssetList
```ts
export type DepreciationAssetListDocument = HydratedDocument<DepreciationAssetList>;
```
3. Decorador para crear una colección MongoDB para la clase
```ts
@Schema()
export class DepreciationAssetList {
    ......
}
```
4. Decorador para añadir un campo a la colección.
```ts
@Prop({require:true})
assetCategory: string; 
    
@Prop({require:true})
usefulLife: number;
```
5. Esquema Mongoose creado a partir de la clase DepreciationAssetList
```ts
export const DepreciationAssetListSchema = SchemaFactory.createForClass(DepreciationAssetList);
```

