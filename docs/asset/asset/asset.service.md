# asset.service
# asset
Se utiliza para organizar y definir los servicios de la aplicación.
Se crean las importaciones necesarias para definir controladores, rutas y realizar solicitudes HTTP en una aplicación NestJS
```ts
import { HttpException, Injectable, Logger } from '@nestjs/common';
import { CreateAssetDto } from './dto/create-asset.dto';
import { InjectModel } from '@nestjs/mongoose';
import { Asset, AssetDocument } from './schema/asset.schema';
import { Model } from 'mongoose';
import {
  DepreciationAssetList,
  DepreciationAssetListDocument,
} from 'src/depreciation-asset-list/schema/depreciation-asset';
import { Cron, CronExpression, Interval } from '@nestjs/schedule';
import { HttpService } from '@nestjs/axios';
import getConfig from '../config/configuration';
import { DepreciationService } from './depreciation.service';
import { UFVService } from './ufv.service';
import { HandlerUfvDepresiationService } from './handler.ufv.depres.service';
import {
  Supplier,
  SupplierDocument,
} from 'src/supplier/schema/supplier.schema';
```
```ts
@Injectable()
export class AssetService {
  constructor(
    //Modelo para interactuar con la colección de activos en la base de datos.
    @InjectModel(Asset.name) private assetModel: Model<AssetDocument>,
    //Modelo para interactuar con la colección de proveedores en la base de datos.
    @InjectModel(Supplier.name) private supplierModel: Model<SupplierDocument>,
    //Modelo para interactuar con la colección de listas de depreciación de activos en la base de datos.
    @InjectModel(DepreciationAssetList.name)
    // interactuar con la tabla en una base de datos que contiene información sobre las listas de depreciación de activos. 
    private depreciationAssetListModel: Model<DepreciationAssetListDocument>,
    //Instancia del servicio HttpService utilizado en esta clase. Proporciona funcionalidades para realizar solicitudes HTTP.
    private httpService: HttpService,
    //Instancia del servicio HandlerUfvDepresiationService utilizado en esta clase. Proporciona funcionalidades específicas para el manejo de UFV (Unidad de Fomento a la Vivienda) y depreciación.
    private handlerUfvDepresiationService: HandlerUfvDepresiationService,
  ) {}

  async create(objectAsset: CreateAssetDto) {
    //Desestructuración de las propiedades de objectAsset
    const { supplier, responsible, location } = objectAsset;
//obtener los datos del responsable 
    try {
      //Realización de una solicitud HTTP GET para recuperar datos relacionados con responsible
      const res = await this.httpService.get(
        `${getConfig().api_personal}api/personal/${responsible}`,
      );
    } catch (error) {
      //lanza un error con el estado 404  y un mensaje que indica no encontro al personal
      throw new HttpException('personal no encotrado', 404);
    }
    //buscar un proveedor basado en el id
    const findSupplier = await this.supplierModel.findOne({ _id: supplier });
    if (!findSupplier) {
      //lanza un error con el estado 404  y un mensaje que indica el proveedor no encontrado
      throw new HttpException('proveedor no encontrado', 404);
    }
// obtener los datos del organigrama 
    let foundObject = true;
    try {
      // Recuperar los datos del archivo de un servicio HTTP externo
      const res = await this.httpService
        .get(`${getConfig().api_organigrama}main/${location}`)
        .toPromise();
      // Verifica si no hay datos en la respuesta
      if (!res.data) {
        // Si no hay datos, se establece la variable foundObject en falso
        foundObject = false;
      } else {
        // Si hay datos, se establece la variable foundObject en verdadero
        foundObject = true;
      }
    } catch (error) {
      try {
        // Recuperar los datos del archivo de un servicio HTTP externo
        const res = await this.httpService
          .get(`${getConfig().api_organigrama}children1/${location}`)
          .toPromise();
        // Verifica si no hay datos en la respuesta
        if (!res.data) {
           // Si no hay datos, se establece la variable foundObject en falso
          foundObject = false;
        } else {
          // Si hay datos, se establece la variable foundObject en verdadero
          foundObject = true;
        }
      } catch (error) {
        try {
          // Recuperar los datos del archivo de un servicio HTTP externo
          const res = await this.httpService
            .get(`${getConfig().api_organigrama}children2/${location}`)
            .toPromise();
          // Verifica si no hay datos en la respuesta
          if (!res.data) {
            // Si no hay datos, se establece la variable foundObject en falso
            foundObject = false;
          } else {
            // Si hay datos, se establece la variable foundObject en verdadero
            foundObject = true;
          }
        } catch (error) {
          try {
            // Recuperar los datos del archivo de un servicio HTTP externo
            const res = await this.httpService
              .get(`${getConfig().api_organigrama}children3/${location}`)
              .toPromise();
             // Verifica si no hay datos en la respuesta
            if (!res.data) {
              // Si no hay datos, se establece la variable foundObject en falso
              foundObject = false;
            } else {
              // Si hay datos, se establece la variable foundObject en verdadero
              foundObject = true;
            }
          } catch (error) {
            try {
              // Recuperar los datos del archivo de un servicio HTTP externo
              const res = await this.httpService
                .get(`${getConfig().api_organigrama}children4/${location}`)
                .toPromise();
              // Verifica si no hay datos en la respuesta
              if (!res.data) {
                // Si no hay datos, se establece la variable foundObject en falso
                foundObject = false;
              } else {
                // Si hay datos, se establece la variable foundObject en verdadero
                foundObject = true;
              }
            } catch (error) {
              //lanzar un error en una aplicación cuando se produce una respuesta de error en una solicitud HTTP.
              throw error.response?.data;
            }
          }
        }
      }
    }

    if (!foundObject) {
      //se utiliza para lanzar una excepción HTTP personalizada en una aplicación NestJS.
      throw new HttpException('Unidad/Departamento no encontrado', 404);
    }
    //
    objectAsset.supplier = findSupplier._id.toString();
    //Cálculo de la depreciación inicial del activo
    const depreciated = await this.calculateDepreciationInitial(
      objectAsset
    );
    //Cálculo y almacenamiento de la depreciación
    const assetDepresiation =
      await this.handlerUfvDepresiationService.calculateAndStoreDepreciation(
        depreciated,
      );
    //Extrayendo la fecha UFV para el activo 
    const assetUfv =
      await this.handlerUfvDepresiationService.ExtractUfvDateAsset(
        assetDepresiation,
      );
    //Creando el activo en la base de datos y devuelve el activo  
    return await this.assetModel.create(assetUfv);
  }

  async findAll() {
    //recupera los activos de la base de datos
    const assets = await this.assetModel
      .find({})
      //rellena el campo supplier
      .populate('supplier')
      //rellena el campo typeCategoryAsset
      .populate('typeCategoryAsset');
    //realiza el conteo de los activos
    const count = await this.assetModel.estimatedDocumentCount();
    //Comprobando si el conteo es menor que 0
    if (count < 0) {
      //devuelve los activos actualizados
      return assets;
    }
    for (const asset of assets) {
      //Verifica si el asset actual tiene una propiedad
      if (asset.file) {
        try {
          // Recuperar los datos del archivo de un servicio HTTP externo
          const res = await this.httpService
            .get(`${getConfig().file_upload}file/${asset.file}`)
            .toPromise();
          //actuliza los datos obtenidos
          asset.file = res.data.file.base64;
        } catch (error) {
          //lanzar un error en una aplicación cuando se produce una respuesta de error en una solicitud HTTP.
          throw error.response?.data;
        }
      }
      try {
        // Recuperar los datos del archivo de un servicio HTTP externo
        const res = await this.httpService
          .get(`${getConfig().api_personal}api/personal/${asset.responsible}`)
          .toPromise();
        // crea una costante donde almacena los datos del responsable
        const responsibleData = res.data;
        //Actualiza la propiedad responsible del asset con los datos del responsable obtenidos
        asset.responsible = responsibleData;
      } catch (error) {
        //lanzar un error en una aplicación cuando se produce una respuesta de error en una solicitud HTTP.
        throw error.response?.data;
      }
    }
    //devuelve los activos actualizados
    return assets;
  }

  async findOne(id: string) {
    const asset = await this.assetModel.findById(id);
    //Verificar si el activo no existe en la base de datos
    if (!asset) {
      // se utiliza para lanzar una excepción HTTP personalizada en una aplicación NestJS.
      throw new HttpException('no existe activo', 404);
    }
    // comprobar si hay una activo y tiene imagen
    if (asset.file) {
      try {
        // Recuperar los datos del archivo de un servicio HTTP externo
        const res = await this.httpService
          .get(`${getConfig().file_upload}file/${asset.file}`)
          .toPromise();
        //actualiza la imagen 
        asset.file = res.data.file.base64;
      } catch (error) {
        //lanzar un error en una aplicación cuando se produce una respuesta de error en una solicitud HTTP.
        throw error.response?.data;
      }
    }
    //devuelve los activos actualizados
    return asset;
  }

  async update(id: string, updateAssetDto) {
    //crea una contate donde almacena el id del activo 
    const findAsset = await this.assetModel.findById(id);
    //Verificar si el activo no se encuentra en la base de datos
    if (!findAsset) {
      //lanza una excepción con los datos de respuesta del error
      throw new HttpException('activo no encotrado', 404);
    }
    //Desestructuración de las propiedades de updateAssetDto
    const { file } = updateAssetDto;

    if (file && file.startsWith('data')) {
      //extrae el tipo de imagen
      const mimeType = file.split(';')[0].split(':')[1].split('/')[1];
      //extrae el base64
      const base64 = file.split(',')[1];
      // crea un objeto con las propiedades mime y base64
      const fileObj = {
        mime: mimeType,
        base64: base64,
      };
      //Verificar si el activo existente y tiene una propiedad file
      if (findAsset.file) {
        try {
          // Recuperar los datos del archivo de un servicio HTTP externo
          const res = await this.httpService
            .post(`${getConfig().file_upload}file/update/${findAsset.file}`, {
              file: fileObj,
            })
            .toPromise();
          //actualizar la propiedad file de updateAssetDto con el ID del nuevo archivo cargado.
          updateAssetDto = { ...updateAssetDto, file: res.data.file._id };
        } catch (error) {
          //lanzar un error en una aplicación cuando se produce una respuesta de error en una solicitud HTTP.
          throw error.response?.data;
        }
      } else {
        try {
          // Recuperar los datos del archivo de un servicio HTTP externo
          const res = await this.httpService
            .post(`${getConfig().file_upload}files/upload`, { file: fileObj })
            .toPromise();
          //actualizar la propiedad file de updateAssetDto con el ID del nuevo archivo cargado.
          updateAssetDto = { ...updateAssetDto, file: res.data.file._id };
        } catch (error) {
          //lanzar un error en una aplicación cuando se produce una respuesta de error en una solicitud HTTP.
          throw error.response?.data;
        }
      }
    } else {
      //se asigna un file a la imagen
      updateAssetDto.file = findAsset.file;
    }
    //Calcula la depreciación inicial del activo utilizando el objeto updateAssetDto actualizado
    const depreciated = await this.calculateDepreciationInitial(updateAssetDto);
    //Calcula y almacena la depreciación utilizando el servicio handlerUfvDepresiationService
    const assetDepresiation =
      await this.handlerUfvDepresiationService.calculateAndStoreDepreciation(
        depreciated,
      );
    //Extraer la UFV del activo utilizando el servicio handlerUfvDepresiationService:
    const assetUfv =
      await this.handlerUfvDepresiationService.ExtractUfvDateAsset(
        assetDepresiation,
      );
    //devolver el activo actualizado
    return await this.assetModel.findByIdAndUpdate(id, assetUfv, { new: true });
  }

  async darDeBaja(_id: string) {
    //crea una contate donde almacena el id del activo 
    const asset = await this.assetModel.findOne({ _id });
    if (!asset) {
      //lanza una excepción con los datos de respuesta del error
      throw new HttpException('activo no encontrado', 404);
    }
    //Verificar si el activo y si tiene una imagen 
    if (asset.file) {
      try {
        //se intenta eliminar activo  
        await this.httpService
          .delete(`${getConfig().file_upload}file/${asset.file}`)
          .toPromise();
      } catch (error) {
        //lanzar un error en una aplicación cuando se produce una respuesta de error en una solicitud HTTP.
        throw error.response?.data;
      }
    }
    //canbia el estado del activo 
    asset.isDeleted = true;
    //devuelve los activos actualizados    
    return asset.save();
  }

  async updateEstado(activoId: string, estado: string): Promise<void> {
    //busca el activo por su id y cambia su estado
    await this.assetModel.findOneAndUpdate({ _id: activoId }, { estado });
  }

  async calculateDepreciationInitial(objectAsset) {
    //crea una costante donde almacena tipo de categoria
    const life = await this.depreciationAssetListModel.findOne({
      assetCategory: objectAsset.typeCategoryAsset,
    });
    //Verificar si ese tipo de activo no se encuentra en la base de datos
    if (!life) {
      //lanza una excepción con los datos de respuesta del error
      throw new HttpException('no encontro el tipo de activo', 404);
    }
    //actualiza el tipo de categoria
    objectAsset.typeCategoryAsset = life._id.toString();
    //Calcula los valores de depreciación
    let depreciatedForYear = objectAsset.price / life.usefulLife;
    //Calcula los valores de depreciación
    let depreciationPerDay = depreciatedForYear / 360;
    //asigna el valor de la depreciación
    objectAsset.depreciatedValue = depreciationPerDay;
    //retorna el valor de la depreciación
    return objectAsset;
  }
}
