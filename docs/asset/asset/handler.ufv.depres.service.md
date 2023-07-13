# handler.ufv.depres.service
## asset
Esta clase tiene varios métodos que se encargan de calcular y almacenar la depreciación de activos, así como extraer información de la Unidad de Fomento a la Vivienda (UFV) para dichos activos.
```ts
import { HttpService } from '@nestjs/axios';
import { HttpException, Injectable } from '@nestjs/common';
import { Cron } from '@nestjs/schedule';
import { InjectModel } from '@nestjs/mongoose';
import { Asset, AssetDocument } from './schema/asset.schema';
import { Model } from 'mongoose';
import { DepreciationAssetList, DepreciationAssetListDocument } from 'src/depreciation-asset-list/schema/depreciation-asset';
import puppeteer from 'puppeteer';

@Injectable()
export class HandlerUfvDepresiationService {

  constructor(
    private httpService: HttpService,
    @InjectModel(Asset.name) private assetModel: Model<AssetDocument>,
    @InjectModel(DepreciationAssetList.name)
    private depreciationAssetListModel: Model<DepreciationAssetListDocument>
  ) {}

  async calculateAndStoreDepreciation(newAsset) {
      const elapsedSeconds = this.calculateElapsedSeconds(newAsset.dateAcquisition);
      const lifespan = await this.lifeSpan(newAsset.typeCategoryAsset)
      const depreciation = this.calculateDepreciationValue(
        newAsset.price,
        lifespan,
        elapsedSeconds,
      ).toFixed(5);
      newAsset.depreciatedValue = Number(depreciation);
  return newAsset;
  }

  private calculateElapsedSeconds(dateOfAcquisition: string): number {
    const currentDate = new Date();
    const acquisitionDate = new Date(dateOfAcquisition);
    const elapsedMilliseconds = currentDate.getTime() - acquisitionDate.getTime();
    const elapsedSeconds = elapsedMilliseconds / 1000;
    return Math.floor(elapsedSeconds);
    
  }

  private async lifeSpan(typeCategoryAsset): Promise<number> {
    const findUsefulLife = await this.depreciationAssetListModel.findOne({ _id: typeCategoryAsset });
    const usefulLifeInYears = findUsefulLife.usefulLife;
    const usefulLifeInSeconds = usefulLifeInYears * 360 * 24 * 60 * 60; 
    
    return usefulLifeInSeconds;
  }

  private calculateDepreciationValue(
    initialValue: number,
    lifespan: number,
    elapsedSeconds: number,
  ): number {
    
    const remainingSeconds = lifespan - elapsedSeconds;
    const depreciationPerSecond = initialValue / lifespan;
    const depreciation = remainingSeconds > 0 ? remainingSeconds * depreciationPerSecond : 0;  
    return depreciation;
  }

  async ExtractUfvDateAsset(assetDepresiation) {
    const browser = await puppeteer.launch(
      {
        headless:false,
        //executablePath: '/usr/bin/chromium',
        //args: ['--no-sandbox'] 
      }
    );
    const page = await browser.newPage();

    await page.goto('https://www.bcb.gob.bo/calculadora-ufv/');
    const date = new Date();

      const formattedDateAsset = await this.formatDate(assetDepresiation.dateAcquisition);
      const formattedDateCurrent = await this.formatDate(date);
      
      const dateInit = await page.$eval(
        '#txtFechaIni',
        (element, value) => ((element as HTMLInputElement).value = value),
        formattedDateAsset,
      );

      const dateFin = await page.$eval(
        '#txtFechaFin',
        (element, value) => ((element as HTMLInputElement).value = value),
        formattedDateCurrent,
      );

      await page.$eval(
        '#txtMonto',
        (element, value) => ((element as HTMLInputElement).value = value),
        assetDepresiation.price.toString(),
      );

      const partsInit = dateInit.split('/');
      const partsFin = dateFin.split('/');

      const date_init = new Date(parseFloat(partsInit[2]), parseFloat(partsInit[1]) - 1, parseFloat(partsInit[0]));
      const date_fin = new Date(parseFloat(partsFin[2]), parseFloat(partsFin[1]) - 1, parseFloat(partsFin[0]));


      let differenceInMs = date_fin.getTime()- date_init.getTime();

      const differenceInDays = Math.floor(differenceInMs / (1000 * 60 * 60 * 24));

      if( differenceInDays > 0 ){
        await page.click('#butMonAct');
        const CotUFV4 = await page.evaluate(() => {
          const element = document.querySelector(
            '#txtCotUFV4',
          ) as HTMLInputElement;
          return element ? element.value.trim() : null;
        });
  
        const CotUFV3 = await page.evaluate(() => {
          const element = document.querySelector(
            '#txtCotUFV3',
          ) as HTMLInputElement;
          return element ? element.value.trim() : null;
        });
        
        assetDepresiation.ufv4 = CotUFV4;
        assetDepresiation.ufv3 = CotUFV3;
      }else{
        assetDepresiation.ufv4 = '0';
        assetDepresiation.ufv3 = assetDepresiation.price.toString();
      }      
    
    await browser.close();
    return assetDepresiation;
  }

  async formatDate(date) {
    const formattedDate = new Date(date);
    const day = formattedDate.getDate();
    const month = formattedDate.getMonth() + 1;
    const year = formattedDate.getFullYear();
    return `${day.toString().padStart(2, '0')}/${month
      .toString()
      .padStart(2, '0')}/${year.toString()}`;
  }
}
```
1. Se crean las importaciones necesarias para definir controladores, rutas y realizar solicitudes HTTP en una aplicación NestJS 
```ts 
import { HttpService } from '@nestjs/axios';
import { HttpException, Injectable } from '@nestjs/common';
import { Cron } from '@nestjs/schedule';
import { InjectModel } from '@nestjs/mongoose';
import { Asset, AssetDocument } from './schema/asset.schema';
import { Model } from 'mongoose';
import { DepreciationAssetList, DepreciationAssetListDocument } from 'src/depreciation-asset-list/schema/depreciation-asset';
import puppeteer from 'puppeteer';
```
2. Importa los servicios para utilizar 
```ts
  constructor(
    private httpService: HttpService,
    @InjectModel(Asset.name) private assetModel: Model<AssetDocument>,
    @InjectModel(DepreciationAssetList.name)
    private depreciationAssetListModel: Model<DepreciationAssetListDocument>,
  ) {}
```
3. Calcula y almacena la depreciación de un activo
```ts
  async calculateAndStoreDepreciation(newAsset) {
    .....
  }
```
4. Calcula los segundos transcurridos desde la adquisición del activo
```ts 
    const elapsedSeconds = this.calculateElapsedSeconds(newAsset.dateAcquisition);
```
5. Obtiene la vida útil del activo según su categoría
```ts
    const lifespan = await this.lifeSpan(newAsset.typeCategoryAsset);
```
6. Calcula el valor de depreciación del activo
```ts
    const depreciation = this.calculateDepreciationValue(
      newAsset.price,
      lifespan,
      elapsedSeconds,
    ).toFixed(5);
    newAsset.depreciatedValue = Number(depreciation);
    return newAsset;
```
7. Calcula los segundos transcurridos desde la fecha de adquisición
```ts
  private calculateElapsedSeconds(dateOfAcquisition: string): number {
    const currentDate = new Date();
    const acquisitionDate = new Date(dateOfAcquisition);
    const elapsedMilliseconds = currentDate.getTime() - acquisitionDate.getTime();
    const elapsedSeconds = elapsedMilliseconds / 1000;
    return Math.floor(elapsedSeconds);
  }
```
8. Obtiene la vida útil de un activo según su categoría
```ts
  private async lifeSpan(typeCategoryAsset): Promise<number> {
    const findUsefulLife = await this.depreciationAssetListModel.findOne({ _id: typeCategoryAsset });
    const usefulLifeInYears = findUsefulLife.usefulLife;
    const usefulLifeInSeconds = usefulLifeInYears * 360 * 24 * 60 * 60;
    return usefulLifeInSeconds;
  }
```
9. Calcula el valor de depreciación de un activo
```ts
  private calculateDepreciationValue(
    initialValue: number,
    lifespan: number,
    elapsedSeconds: number,
  ): number {
    const remainingSeconds = lifespan - elapsedSeconds;
    const depreciationPerSecond = initialValue / lifespan;
    const depreciation = remainingSeconds > 0 ? remainingSeconds * depreciationPerSecond : 0;
    return depreciation;
  }
```
9. Extrae la fecha de UFV (Unidad de Fomento a la Vivienda) de un activo
```ts
  async ExtractUfvDateAsset(assetDepresiation) {
    .......
  }
```
10. Inicia una instancia de Puppeteer
```ts
    const browser = await puppeteer.launch({
      headless: false,
      //executablePath: '/usr/bin/chromium',
      //args: ['--no-sandbox']
    });
```
11. Establece la fecha de adquisición en la página web
```ts
    const dateInit = await page.$eval(
      '#txtFechaIni',
      (element, value) => ((element as HTMLInputElement).value = value),
      formattedDateAsset,
    );
```
12. Establece la fecha actual en la página web
```ts
    const dateFin = await page.$eval(
      '#txtFechaFin',
      (element, value) => ((element as HTMLInputElement).value = value),
      formattedDateCurrent,
    );
```
13. Establece el precio del activo en la página web
```ts
    await page.$eval(
      '#txtMonto',
      (element, value) => ((element as HTMLInputElement).value = value),
      assetDepresiation.price.toString(),
    );

    const partsInit = dateInit.split('/');
    const partsFin = dateFin.split('/');

    const date_init = new Date(
      parseFloat(partsInit[2]),
      parseFloat(partsInit[1]) - 1,
      parseFloat(partsInit[0]),
    );
    const date_fin = new Date(
      parseFloat(partsFin[2]),
      parseFloat(partsFin[1]) - 1,
      parseFloat(partsFin[0]),
    );

    let differenceInMs = date_fin.getTime() - date_init.getTime();
    const differenceInDays = Math.floor(differenceInMs / (1000 * 60 * 60 * 24));
```
14. Hace clic en el botón para obtener la UFV actualizada
```ts
      await page.click('#butMonAct');
```
15. Obtiene el valor de UFV4 de la página web
```ts
      const CotUFV4 = await page.evaluate(() => {
        const element = document.querySelector('#txtCotUFV4') as HTMLInputElement;
        return element ? element.value.trim() : null;
      });
```
16. Obtiene el valor de UFV3 de la página web
```ts
      const CotUFV3 = await page.evaluate(() => {
        const element = document.querySelector('#txtCotUFV3') as HTMLInputElement;
        return element ? element.value.trim() : null;
      });
```

17. Formatea una fecha en el formato dd/mm/yyyy
```ts
  async formatDate(date) {
    const formattedDate = new Date(date);
    const day = formattedDate.getDate();
    const month = formattedDate.getMonth() + 1;
    const year = formattedDate.getFullYear();
    return `${day.toString().padStart(2, '0')}/${month.toString().padStart(2, '0')}/${year.toString()}`;
  }
```