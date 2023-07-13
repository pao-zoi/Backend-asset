# ufv.service
## asset
```ts
import { HttpService } from '@nestjs/axios';
import { HttpException, Injectable } from '@nestjs/common';
import { Cron } from '@nestjs/schedule';
import puppeteer from 'puppeteer';
import { CronExpression, Interval } from '@nestjs/schedule';
import { InjectModel } from '@nestjs/mongoose';
import { Asset, AssetDocument } from './schema/asset.schema';
import { Model } from 'mongoose';

@Injectable()
export class UFVService {

  constructor(
    private httpService: HttpService,
    @InjectModel(Asset.name) private assetModel: Model<AssetDocument>,
  ) {}
 
  @Cron('0 0 * * *')
  // @Interval(10000)
  async ExtractUfvDateAsset() {
    const assets = await this.assetModel.find({});
  
    const browser = await puppeteer.launch(
      {
        // headless:false,
        //executablePath: '/usr/bin/chromium',
        //args: ['--no-sandbox'] 
      }
    );
    const page = await browser.newPage();

    await page.goto('https://www.bcb.gob.bo/calculadora-ufv/');
    const date = new Date();

    for (const asset of assets) {
      const formattedDateAsset = await this.formatDate(asset.dateAcquisition);
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
        asset.price.toString(),
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
        
        asset.ufv4 = CotUFV4;
        asset.ufv3 = CotUFV3;
        asset.save();
      }else{
        asset.ufv4 = '0';
        asset.ufv3 = asset.price.toString();
        asset.save();
      }            
    }
    
    await browser.close();
    return 0;
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
import puppeteer from 'puppeteer';
import { CronExpression, Interval } from '@nestjs/schedule';
import { InjectModel } from '@nestjs/mongoose';
import { Asset, AssetDocument } from './schema/asset.schema';
import { Model } from 'mongoose';
```
2. Cron job para extraer la fecha de la UFV
```ts
  @Cron('0 0 * * *')
``` 
3. Obtener todos los activos
```ts
    const assets = await this.assetModel.find({});
```
4. Iniciar una instancia de Puppeteer
```ts
    const browser = await puppeteer.launch({
      // headless: false,
      //executablePath: '/usr/bin/chromium',
      //args: ['--no-sandbox']
    });
    const page = await browser.newPage();
```
5. Abrir la página web de la calculadora UFV
```ts
    await page.goto('https://www.bcb.gob.bo/calculadora-ufv/');
    const date = new Date();
```
6. Iterar sobre cada activo
```ts
    for (const asset of assets) {
      const formattedDateAsset = await this.formatDate(asset.dateAcquisition);
      const formattedDateCurrent = await this.formatDate(date);
```
7. Establecer la fecha de adquisición en la página web
```ts
      const dateInit = await page.$eval(
        '#txtFechaIni',
        (element, value) => ((element as HTMLInputElement).value = value),
        formattedDateAsset,
      );
```
8. Establecer la fecha actual en la página web
```ts
      const dateFin = await page.$eval(
        '#txtFechaFin',
        (element, value) => ((element as HTMLInputElement).value = value),
        formattedDateCurrent,
      );
```
9. Establecer el precio del activo en la página web
```ts
      await page.$eval(
        '#txtMonto',
        (element, value) => ((element as HTMLInputElement).value = value),
        asset.price.toString(),
      );
```
10. Hacer clic en el botón para obtener la UFV actualizada
```ts
        await page.click('#butMonAct');
```
11. Obtener el valor de UFV4 de la página web
```ts
        const CotUFV4 = await page.evaluate(() => {
          const element = document.querySelector('#txtCotUFV4') as HTMLInputElement;
          return element ? element.value.trim() : null;
        });
```
12. Obtener el valor de UFV3 de la página web
```ts
        const CotUFV3 = await page.evaluate(() => {
          const element = document.querySelector('#txtCotUFV3') as HTMLInputElement;
          return element ? element.value.trim() : null;
        });
```
13. Formatear una fecha en el formato "dd/mm/yyyy"
```ts
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
