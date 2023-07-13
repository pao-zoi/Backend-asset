# asset.depreciation.service
# asset

Se crean las importaciones necesarias para definir controladores, rutas y realizar solicitudes HTTP en una aplicación NestJS
```ts
import { HttpService } from '@nestjs/axios';
import { HttpException, Injectable } from '@nestjs/common';
import { Cron } from '@nestjs/schedule';
import { InjectModel } from '@nestjs/mongoose';
import { Asset, AssetDocument } from './schema/asset.schema';
import { Model } from 'mongoose';
import { DepreciationAssetList, DepreciationAssetListDocument } from 'src/depreciation-asset-list/schema/depreciation-asset';

@Injectable()
export class DepreciationService {

  constructor(
    private httpService: HttpService,
    @InjectModel(Asset.name) private assetModel: Model<AssetDocument>,
    @InjectModel(DepreciationAssetList.name)
    private depreciationAssetListModel: Model<DepreciationAssetListDocument>
  ) {}

  @Cron('0 0 * * *')
  async calculateAndStoreDepreciation() {
    //crea una constante donde recupera los datos de los activos
    const assets = await this.assetModel.find({})
    //recorre cada activo
    for (const asset of assets) {
      //Calcula los segundos transcurridos desde la adquisición del activo
      const elapsedSeconds = this.calculateElapsedSeconds(asset.dateAcquisition);
      //Obtiene la vida útil del activo
      const lifespan = await this.lifeSpan(asset.typeCategoryAsset)
      //Calcula el valor de depreciación 
      const depreciation = this.calculateDepreciationValue(
        asset.price,
        lifespan,
        elapsedSeconds,
      ).toFixed(5);
      //Asigna el valor de depreciación
      asset.depreciatedValue = Number(depreciation);
      //guarda los cambios 
      asset.save();
    }
  }
  //Calcula el número de segundos transcurridos desde la fecha de adquisición hasta la fecha actual.
  private calculateElapsedSeconds(dateOfAcquisition: Date): number {
    //crea una constante que tiene la fecha actual.
    const currentDate = new Date();
    //Calcula la diferencia en milisegundos entre la fecha actual y la fecha de adquisición.
    const elapsedMilliseconds = currentDate.getTime() - dateOfAcquisition.getTime();
    //Convierte los milisegundos en segundos dividiendo por 1000.
    const elapsedSeconds = elapsedMilliseconds / 1000;
    //retorna los segundos trancurridos
    return Math.floor(elapsedSeconds);
  }
  
  private async lifeSpan(typeCategoryAsset): Promise<number> {
    ////Busca y recupera el objeto findUsefulLife del modelo depreciationAssetListModel que coincide con la categoría de activo proporcionada typeCategoryAsset.
    const findUsefulLife = await this.depreciationAssetListModel.findOne({ assetCategory: typeCategoryAsset });
    //Obtiene el valor de vida útil del objeto findUsefulLife.
    const usefulLifeInYears = findUsefulLife.usefulLife;
    //Calcula la vida útil en segundos multiplicando usefulLifeInYears por el número de segundos en un año (360 * 24 * 60 * 60).
    const usefulLifeInSeconds = usefulLifeInYears * 360 * 24 * 60 * 60; 
    //retorna a vida util en segundos 
    return usefulLifeInSeconds;
  }

  private calculateDepreciationValue(
    initialValue: number,
    lifespan: number,
    elapsedSeconds: number,
  ): number {
    //Calcula los segundos restantes restando numero de segundos de la vida util.
    const remainingSeconds = lifespan - elapsedSeconds;
    //|Calcula la depreciación por segundo dividiendo el valor inicial por la vida útil.
    const depreciationPerSecond = initialValue / lifespan;
    //verifica que los segundos sean mayos a 0
    const depreciation = remainingSeconds > 0 ? remainingSeconds * depreciationPerSecond : 0;  
    //retorna la depreciación
    return depreciation;
    
  }
}
```