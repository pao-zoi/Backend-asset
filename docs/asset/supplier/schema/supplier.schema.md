# supplier.schema
## supplier

Los esquemas se pueden crear con los decoradores de NestJS o con Mongoose de forma manual. El uso de decoradores para crear esquemas reduce en gran medida la repetición y mejora la legibilidad general del código.
```ts
import { Prop, Schema, SchemaFactory } from '@nestjs/mongoose';
import mongoose, { HydratedDocument } from 'mongoose';

export type SupplierDocument = HydratedDocument<Supplier>;

@Schema()
export class Supplier {
    @Prop()
    name: string; 
    
    @Prop()
    address: string;
    
    @Prop()
    phone: number 
    
    @Prop()
    ci: string
    
    @Prop({default:false})
    isDeleted: boolean

    @Prop({unique:true})
    email: string
    
    @Prop()
    amountAsset: number
    
    @Prop()
    finalPrice: number
    
    @Prop({unique:true})
    NIT: string
}

export const SupplierSchema = SchemaFactory.createForClass(Supplier);
```
1. Importación de Document desde Mongoose
```ts
import { Prop, Schema, SchemaFactory } from '@nestjs/mongoose';
import { HydratedDocument } from 'mongoose';
```
2. Definición del tipo de un Supplier
```ts
export type SupplierDocument = HydratedDocument<Supplier>;
```
3. Decorador para crear una colección MongoDB para la clase
```ts
@Schema()
export class Supplier {
    ......
}
```
4. Decorador para añadir un campo a la colección.
```ts
@Prop()
    name: string; 
    
    @Prop()
    address: string;
    
    @Prop()
    phone: number 
    
    @Prop()
    ci: string
    
    @Prop({default:false})
    isDeleted: boolean

    @Prop({unique:true})
    email: string
    
    @Prop()
    amountAsset: number
    
    @Prop()
    finalPrice: number
    
    @Prop({unique:true})
    NIT: string
```
5. Esquema Mongoose creado a partir de la clase Supplier.
```ts
export const SupplierSchema = SchemaFactory.createForClass(Supplier);
```



