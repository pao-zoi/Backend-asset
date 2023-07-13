
# create-supplier.dto
## supplier
El DTO define la estructura de un objeto que se pasa en el cuerpo de una petición HTTP.
```ts
import { ApiProperty } from "@nestjs/swagger";

export class CreateSupplierDto {
    @ApiProperty()
    name: string; 
    
    @ApiProperty()
    address: string;
    
    @ApiProperty()
    phone: number
    
    @ApiProperty()
    ci: string

    isDeleted: boolean

    @ApiProperty()
    email: string

    @ApiProperty()
    amountAsset: number

    @ApiProperty()
    finalPrice: number

    @ApiProperty()
    NIT: string
}
``` 
1. Import permite utilizar la clase ApiProperty para decorar propiedades de clases DTO y proporcionar metadatos utilizados por Swagger para generar la documentación de la API.
```ts
import { ApiProperty } from "@nestjs/swagger";   
```
2. Se utiliza el decorador ApiProperty, para generar documentación automática de la API utilizando Swagger.
```ts
@ApiProperty()
    name: string; 
    
    @ApiProperty()
    address: string;
    
    @ApiProperty()
    phone: number
    
    @ApiProperty()
    ci: string

    isDeleted: boolean

    @ApiProperty()
    email: string

    @ApiProperty()
    amountAsset: number

    @ApiProperty()
    finalPrice: number

    @ApiProperty()
    NIT: string
```
