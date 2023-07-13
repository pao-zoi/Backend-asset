# create-depreciacion-asset-list.dto
# depreciacion-asset-list
El DTO define la estructura de un objeto que se pasa en el cuerpo de una petición HTTP.
```ts
import { ApiProperty } from "@nestjs/swagger";
export class CreateDepreciationAssetListDto {
  
  @ApiProperty()
  assetCategory: string; 
  
  @ApiProperty()
  usefulLife: number;
}
``` 
1. Import permite utilizar la clase ApiProperty para decorar propiedades de clases DTO y proporcionar metadatos utilizados por Swagger para generar la documentación de la API.
```ts
import { ApiProperty } from "@nestjs/swagger";   
```
2. Se utiliza el decorador ApiProperty, para generar documentación automática de la API utilizando Swagger.
```ts
@ApiProperty()
assetCategory: string; 

@ApiProperty()
usefulLife: number;
```
