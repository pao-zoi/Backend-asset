# asset.schema
## asset
Los esquemas se pueden crear con los decoradores de NestJS o con Mongoose de forma manual. El uso de decoradores para crear esquemas reduce en gran medida la repetición y mejora la legibilidad general del código. 
```ts
import { Prop, Schema, SchemaFactory } from '@nestjs/mongoose';
import mongoose, { HydratedDocument } from 'mongoose';

export type AssetDocument = HydratedDocument<Asset>;

@Schema()
export class Asset {
    @Prop()
    name: string; 

    @Prop()
    description: string;
    
    @Prop({ required: true})
    responsible:string[]; 
    
    @Prop({ type: mongoose.Schema.Types.ObjectId, ref: 'Supplier' ,required: true})
    supplier:string; 

    @Prop({required: true})
    location:string; 

    @Prop()
    price: number; 

    @Prop()
    dateAcquisition: Date; 
   
   @Prop()
    warrantyExpirationDate: Date;
    
    @Prop({ default: false })
    isDeleted?: boolean;  
    
    @Prop()
    ufv3:string

    @Prop()
    ufv4:string
    
    @Prop({default:0})
    depreciatedValue: number;

    @Prop({ type: mongoose.Schema.Types.ObjectId, ref: 'DepreciationAssetList', required: true})
    typeCategoryAsset: string; //recibe el nombre del tipo de activo 

    @Prop({default:null})
    file?:string
}

export const AssetSchema = SchemaFactory.createForClass(Asset);
```
1. Importación de Document desde Mongoose
```ts
import { Prop, Schema, SchemaFactory } from '@nestjs/mongoose';
import mongoose, { HydratedDocument } from 'mongoose';
```
2. Definición del tipo de un documento Asset
```ts
export type AssetDocument = HydratedDocument<Asset>;
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
@Prop()
name: string; 

@Prop()
description: string;
    
@Prop({ required: true})
responsible:string[]; 
    
@Prop({ type: mongoose.Schema.Types.ObjectId, ref: 'Supplier' ,required: true})
supplier:string; 

@Prop({required: true})
location:string; 

@Prop()
price: number; 

@Prop()
dateAcquisition: Date; 
   
@Prop()
warrantyExpirationDate: Date;
    
@Prop({ default: false })
isDeleted?: boolean;  
    
@Prop()
ufv3:string

@Prop()
ufv4:string
    
@Prop({default:0})
depreciatedValue: number;

@Prop({ type: mongoose.Schema.Types.ObjectId, ref: 'DepreciationAssetList', required: true})
typeCategoryAsset: string; //recibe el nombre del tipo de activo 

@Prop({default:null})
file?:string
```
5. Esquema Mongoose creado a partir de la clase Asset
```ts
export const AssetSchema = SchemaFactory.createForClass(Asset);
```
