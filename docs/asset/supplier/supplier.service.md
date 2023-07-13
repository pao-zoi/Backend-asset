# supplier.service
## supplier
Se almacenan las clases que implementan los servicios de la aplicación, encapsulando la lógica de negocio y proporcionando funcionalidades específicas. Esta estructura ayuda a mantener un código modular, reutilizable y fácilmente testeable.
```ts
import { HttpException, Injectable } from '@nestjs/common';
import { CreateSupplierDto } from './dto/create-supplier.dto';
import { HttpService } from '@nestjs/axios';
import { InjectModel } from '@nestjs/mongoose';
import { Supplier, SupplierDocument } from './schema/supplier.schema';
import { Model } from 'mongoose';
import getConfig from '../config/configuration'
import { Asset, AssetDocument } from 'src/asset/schema/asset.schema';

@Injectable()
export class SupplierService {
  constructor(    
    @InjectModel(Supplier.name) private supplierModel: Model<SupplierDocument>,  
    @InjectModel(Asset.name) private assetModel: Model<AssetDocument>,  
    ){}

  async create(createSupplierDto: CreateSupplierDto) {
    return await this.supplierModel.create(createSupplierDto);
  }

  async findAll() {
    return await this.supplierModel.find({isDeleted:false}
      );
  }

  async findOne(id: string) {
    const findSupplier = await this.supplierModel.findOne({_id:id})
    if(!findSupplier){
      throw new HttpException('no se encontro al proveedor',404)
    }
    return await this.supplierModel.findById(id)
  }

  async update(id: string, updateSupplierDto) {
    const findSupplier = await this.supplierModel.findOne({_id:id})
    if(!findSupplier){
      throw new HttpException('no se encontro al proveedor',404)
    }
    return await this.supplierModel.findByIdAndUpdate(id,updateSupplierDto,{new:true});
  }

  async remove(id: string) {
    const findSupplier = await this.supplierModel.findOne({_id:id})
    if(!findSupplier){
      throw new HttpException('no se encontro al proveedor',404)
    }
    findSupplier.isDeleted= true
    return findSupplier.save();
  }

  async restartsupplier(id:string){
    const restartSupplier = await this.supplierModel.findOne({ _id: id })
    if (!restartSupplier) {
      throw new HttpException('la aplicacion no existe',404)
    }
    if (restartSupplier.isDeleted==false) {
      throw new HttpException('el proveedor no esta eliminado',409)
    }
    restartSupplier.isDeleted = false 
    return restartSupplier.save()
  }
}
```
1. Se crean las importaciones necesarias para definir controladores, rutas y realizar solicitudes HTTP en una aplicación NestJS
```ts
import { HttpException, Injectable } from '@nestjs/common';
import { CreateSupplierDto } from './dto/create-supplier.dto';
import { HttpService } from '@nestjs/axios';
import { InjectModel } from '@nestjs/mongoose';
import { Supplier, SupplierDocument } from './schema/supplier.schema';
import { Model } from 'mongoose';
import getConfig from '../config/configuration'
import { Asset, AssetDocument } from 'src/asset/schema/asset.schema';
```
2. Añadimos los constructores que necesitamos.
```ts 
constructor(    
  ..............   
  ){}
```
3. Definir un modelo para proveedores y activos mediante inyección de dependencias
``` ts
@InjectModel(Supplier.name) private supplierModel: Model<SupplierDocument>, 
@InjectModel(Asset.name) private assetModel: Model<AssetDocument>,
```
4. Es un método asíncrono llamado createque crea un documento en la supplierModel en MongoDB basado en el createSupplierDto objeto proporcionado.
```ts
 async create(createSupplierDto: CreateSupplierDto) {
    return await this.supplierModel.create(createSupplierDto);
  }
```
5. Devuelva una lista de proveedores mientras este no fue borrado
```ts  
 async findAll() {
    return await this.supplierModel.find({isDeleted:false}
      );
  }
``` 
7. Obtiene un proveedor de una colección de MongoDB en función del archivo id. Si encuentra al proveedor, devuelve el documento de proveedor; de lo contrario, arroja un HttpException código de estado 404 para indicar que no se encontró al proveedor.
```ts
async findOne(id: string) {
  const findSupplier = await this.supplierModel.findOne({_id:id})
  if(!findSupplier){
    throw new HttpException('no se encontro al proveedor',404)
  }
  return await this.supplierModel.findById(id)
}
```
8. Llamada al método de actualización de documentos por id pasándole el JSON con las modificaciones en caso de no existir proveedor manda un mensaje de error.
``` ts
async update(id: string, updateSupplierDto) {
  const findSupplier = await this.supplierModel.findOne({_id:id})
  if(!findSupplier){
    throw new HttpException('no se encontro al proveedor',404)
  }
  return await this.supplierModel.findByIdAndUpdate(id,updateSupplierDto,{new:true});
}
```
9.  Para dar de baja un activo se crea una constante donde almacena el ID y le cambia el estado del activo, retorna un error si no encuentra el proveedor.
```ts
async remove(id: string) {
  const findSupplier = await this.supplierModel.findOne({_id:id})
  if(!findSupplier){
    throw new HttpException('no se encontro al proveedor',404)
  }
  findSupplier.isDeleted= true
  return findSupplier.save();
}
```
10. Para restaurar un activo se crea una constante donde almacena el, ID y le cambia el estado del activo, retorna un error si no encuentra el proveedor. 
```ts
async restartsupplier(id:string){
  const restartSupplier = await this.supplierModel.findOne({ _id: id })
  if (!restartSupplier) {
    throw new HttpException('la aplicacion no existe',404)
  }
  if (restartSupplier.isDeleted==false) {
    throw new HttpException('el proveedor no esta eliminado',409)
  }
  restartSupplier.isDeleted = false 
  return restartSupplier.save()
}
```
