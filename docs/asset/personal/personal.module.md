# personal.module
## personal
Se utiliza para organizar y definir los módulos de la aplicación como tambien para agrupar y encapsular componentes relacionados, como controladores, servicios y proveedores.
```ts
import { Module } from '@nestjs/common';
import { PersonalController } from './personal.controller';
import { HttpModule } from '@nestjs/axios';

@Module({
  imports:[
    HttpModule
  ],
  controllers: [PersonalController],
  providers: []
})
export class PersonalModule {}
```
1. Se crean las importaciones necesarias para definir controladores, rutas y realizar solicitudes HTTP en una aplicación NestJS.
```ts
import { Module } from '@nestjs/common';
import { PersonalController } from './personal.controller';
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
imports:[
    HttpModule
],
```
4. Arreglos que se instalan dentro del contexto del módulo.
```ts
  providers: []
```
5. Importa y agrega al controlador PersonalController al arreglo de controladores.
```ts
controllers: [PersonalController],
```
6. Se exporta la clase PersonalModule.
```ts
export class PersonalModule {}
```

