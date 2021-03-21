---
title: Skapa ett vinkel-applösenord med Azure Cosmos DB s API för MongoDB (part3)
description: Del 3 i självstudieserien om hur du skapar en MongoDB-app med Angular och Node i Azure Cosmos DB med exakt samma API:er som du använder för MongoDB.
author: johnpapa
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 12/26/2018
ms.author: jopapa
ms.custom: seodec18, devx-track-js
ms.reviewer: sngun
ms.openlocfilehash: 181af5cf26d19a9f51e8d456e777badf7efa224d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93097830"
---
# <a name="create-an-angular-app-with-azure-cosmos-dbs-api-for-mongodb---build-the-ui-with-angular"></a>Skapa en Angular-app med Azure Cosmos DB:s API för MongoDB – skapa användargränssnittet med Angular
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Den här självstudien i flera delar visar hur du skapar en ny app skriven i Node.js med Express och Angular och sedan ansluter den till ditt [Cosmos-konto konfigurerat med Cosmos DB:s API för MongoDB](mongodb-introduction.md).

Del 3 av självstudiekursen bygger vidare på [del 2](tutorial-develop-mongodb-nodejs-part2.md) och består av följande uppgifter:

> [!div class="checklist"]
> * Skapa Angular-användargränssnittet
> * Skapa rätt känsla och design med CSS
> * Testa appen lokalt

## <a name="video-walkthrough"></a>Videogenomgång

> [!VIDEO https://www.youtube.com/embed/MnxHuqcJVoM]

## <a name="prerequisites"></a>Förutsättningar

Utför stegen i [del 2](tutorial-develop-mongodb-nodejs-part2.md) av självstudiekursen innan du påbörjar den här delen.

> [!TIP]
> Den här självstudiekursen beskriver steg för steg hur du skapar programmet. Om du vill hämta det färdiga projektet kan du ladda ned programmet från [angular-cosmosdb-databasen](https://github.com/Azure-Samples/angular-cosmosdb) på GitHub.

## <a name="build-the-ui"></a>Skapa användargränssnittet

1. Stoppa Node-appen genom att klicka på stopp knappen i Visual Studio Code :::image type="icon" source="./media/tutorial-develop-mongodb-nodejs-part3/stop-button.png"::: . 

2. Generera en heroes-komponent genom att skriva följande kommando i kommandotolken i Windows eller i terminalfönstret på en Mac. I den här koden är g = generera, c = komponent och heroes = namnet på komponenten. Koden har en platt filstruktur (--flat) så att ingen undermapp skapas för den.

    ```
    ng g c heroes --flat 
    ```

    En bekräftelse av de nya komponenterna visas i terminalfönstret.

    :::image type="content" source="./media/tutorial-develop-mongodb-nodejs-part3/install-heros-component.png" alt-text="Installera hero-komponenter":::

    Nu ska vi titta på filerna som skapats och uppdaterats. 

3. Gå till den nya **src\app**-mappen i **Explorer**-fönstret i Visual Studio Code och öppna den nya **heroes.component.ts**-filen som skapades i appmappen. Den här TypeScript-komponentfilen skapades med det föregående kommandot.

    > [!TIP]
    > Om mappen inte visas i Visual Studio Code trycker du på CMD + SKIFT P på en Mac-dator eller på CTRL + SKIFT + P i Windows för att öppna kommandopaletten och hämtar sedan systemändringen genom att skriva *Reload Window*.

4. Öppna filen **app.module.ts** i samma mapp och notera att `HeroesComponent` har lagts till i deklarationerna på rad 5 och att den även har importerats på rad 10.

    :::image type="content" source="./media/tutorial-develop-mongodb-nodejs-part3/app-module-file.png" alt-text="Öppna filen app-module.ts":::

5. Gå tillbaka till filen **heroes.component.html** och kopiera in den här koden. `<div>` är containern för hela sidan. Containern innehåller en lista med heroes-komponenter som vi måste skapa, så att du när du klickar på en komponent kan markera den och redigera eller ta bort den i användargränssnittet. I HTML-koden använder vi en del formatering så att du vet vilken komponent som har markerats. Det finns också ett redigeringsområde så att du kan lägga till en ny hero-komponent eller redigera en befintlig. 

    ```html
    <div>
      <ul class="heroes">
        <li *ngFor="let hero of heroes" (click)="onSelect(hero)" [class.selected]="hero === selectedHero">
          <button class="delete-button" (click)="deleteHero(hero)">Delete</button>
          <div class="hero-element">
            <div class="badge">{{hero.id}}</div>
            <div class="name">{{hero.name}}</div>
            <div class="saying">{{hero.saying}}</div>
          </div>
        </li>
      </ul>
      <div class="editarea">
        <button (click)="enableAddMode()">Add New Hero</button>
        <div *ngIf="selectedHero">
          <div class="editfields">
            <div>
              <label>id: </label>
              <input [(ngModel)]="selectedHero.id" placeholder="id" *ngIf="addingHero" />
              <label *ngIf="!addingHero" class="value">{{selectedHero.id}}</label>
            </div>
            <div>
              <label>name: </label>
              <input [(ngModel)]="selectedHero.name" placeholder="name" />
            </div>
            <div>
              <label>saying: </label>
              <input [(ngModel)]="selectedHero.saying" placeholder="saying" />
            </div>
          </div>
          <button (click)="cancel()">Cancel</button>
          <button (click)="save()">Save</button>
        </div>
      </div>
    </div>
    ```

7. Nu när HTML-koden är på plats måste vi lägga till den i **heroes.component.ts**-filen så att vi kan interagera med mallen. Mallen läggs till i komponentfilen med följande kod: En konstruktor har lagts till som hämtar några heroes-komponenter och initierar hero-tjänstkomponenten för att hämta alla data. Den här koden lägger också till alla nödvändiga metoder för hanteringen av händelser i användargränssnittet. Du kan kopiera följande kod över den befintliga koden i **heroes.component.ts**. Det är normalt att det finns fel i områdena Hero och HeroService eftersom motsvarande komponenter inte importerats än, men du kommer att åtgärda dessa fel i nästa avsnitt. 

    ```ts
    import { Component, OnInit } from '@angular/core';

    @Component({
      selector: 'app-heroes',
      templateUrl: './heroes.component.html',
      styleUrls: ['./heroes.component.scss']
    })
    export class HeroesComponent implements OnInit {
      addingHero = false;
      heroes: any = [];
      selectedHero: Hero;
    
      constructor(private heroService: HeroService) {}
    
      ngOnInit() {
       this.getHeroes();
      }

      cancel() {
        this.addingHero = false;
        this.selectedHero = null;
      }

      deleteHero(hero: Hero) {
        this.heroService.deleteHero(hero).subscribe(res => {
          this.heroes = this.heroes.filter(h => h !== hero);
          if (this.selectedHero === hero) {
            this.selectedHero = null;
          }
        });
      }

      getHeroes() {
        return this.heroService.getHeroes().subscribe(heroes => {
          this.heroes = heroes;
        });
      }

      enableAddMode() {
        this.addingHero = true;
        this.selectedHero = new Hero();
      }

      onSelect(hero: Hero) {
        this.addingHero = false;
        this.selectedHero = hero;
      }

      save() {
        if (this.addingHero) {
          this.heroService.addHero(this.selectedHero).subscribe(hero => {
            this.addingHero = false;
            this.selectedHero = null;
            this.heroes.push(hero);
          });
        } else {
          this.heroService.updateHero(this.selectedHero).subscribe(hero => {
            this.addingHero = false;
            this.selectedHero = null;
          });
        }
      }
    }
    ```

8. Öppna filen **app/app.module.ts** i **Explorer** och uppdatera importavsnittet för att lägga till en import för en `FormsModule`. Importavsnittet bör se ut så här nu:

    ```
    imports: [
      BrowserModule,
      FormsModule
    ],
    ```

9. Lägg till en import för den nya FormsModule-modulen på rad 3 i filen **app/app.module.ts**. 

    ```
    import { BrowserModule } from '@angular/platform-browser';
    import { NgModule } from '@angular/core';
    import { FormsModule } from '@angular/forms';
    ```

## <a name="use-css-to-set-the-look-and-feel"></a>Skapa rätt känsla och design med CSS

1. Öppna filen **src/styles.scss** i Explorer-fönstret.

2. Kopiera följande kod till filen **styles.scss** genom att ersätta det befintliga innehållet i filen.

    ```css
    /* You can add global styles to this file, and also import other style files */

    * {
      font-family: Arial;
    }
    h2 {
      color: #444;
      font-weight: lighter;
    }
    body {
      margin: 2em;
    }
    
    body,
    input[text],
    button {
      color: #888;
      // font-family: Cambria, Georgia;
    }
    button {
      font-size: 14px;
      font-family: Arial;
      background-color: #eee;
      border: none;
      padding: 5px 10px;
      border-radius: 4px;
      cursor: pointer;
      cursor: hand;
      &:hover {
        background-color: #cfd8dc;
      }
      &.delete-button {
        float: right;
        background-color: gray !important;
        background-color: rgb(216, 59, 1) !important;
        color: white;
        padding: 4px;
        position: relative;
        font-size: 12px;
      }
    }
    div {
      margin: .1em;
    }

    .selected {
      background-color: #cfd8dc !important;
      background-color: rgb(0, 120, 215) !important;
      color: white;
    }

    .heroes {
      float: left;
      margin: 0 0 2em 0;
      list-style-type: none;
      padding: 0;
      li {
        cursor: pointer;
        position: relative;
        left: 0;
        background-color: #eee;
        margin: .5em;
        padding: .5em;
        height: 3.0em;
        border-radius: 4px;
        width: 17em;
        &:hover {
          color: #607d8b;
          color: rgb(0, 120, 215);
          background-color: #ddd;
          left: .1em;
        }
        &.selected:hover {
          /*background-color: #BBD8DC !important;*/
          color: white;
        }
      }
      .text {
        position: relative;
        top: -3px;
      }
      .saying {
        margin: 5px 0;
      }
      .name {
        font-weight: bold;
      }
      .badge {
        /* display: inline-block; */
        float: left;
        font-size: small;
        color: white;
        padding: 0.7em 0.7em 0 0.5em;
        background-color: #607d8b;
        background-color: rgb(0, 120, 215);
        background-color:rgb(134, 183, 221);
        line-height: 1em;
        position: relative;
        left: -1px;
        top: -4px;
        height: 3.0em;
        margin-right: .8em;
        border-radius: 4px 0 0 4px;
        width: 1.2em;
      }
    }

    .header-bar {
      background-color: rgb(0, 120, 215);
      height: 4px;
      margin-top: 10px;
      margin-bottom: 10px;
    }

    label {
      display: inline-block;
      width: 4em;
      margin: .5em 0;
      color: #888;
      &.value {
        margin-left: 10px;
        font-size: 14px;
      }
    }

    input {
      height: 2em;
      font-size: 1em;
      padding-left: .4em;
      &::placeholder {
          color: lightgray;
          font-weight: normal;
          font-size: 12px;
          letter-spacing: 3px;
      }
    }

    .editarea {
      float: left;
      input {
        margin: 4px;
        height: 20px;
        color: rgb(0, 120, 215);
      }
      button {
        margin: 8px;
      }
      .editfields {
        margin-left: 12px;
      }
    }
    ``` 
3. Spara filen. 

## <a name="display-the-component"></a>Visa komponenten

Nu när vi har komponenten, hur gör vi så att den visas på skärmen? Vi ska ändra standardkomponenterna i **app.component.ts**.

1. Öppna filen **/app/app.component.ts** i Explorer-fönstret och ändra rubriken till Heroes och lägg sedan till namnet på komponenten som vi skapade i **heroes.components.ts** (app-heroes) så att vi refererar till den nya komponenten. Innehållet i filen bör nu se ut så här: 

    ```ts
    import { Component } from '@angular/core';

    @Component({
      selector: 'app-root',
      templateUrl: './app.component.html',
      styleUrls: ['./app.component.scss'],
      template: `
      <h1>Heroes</h1>
      <div class="header-bar"></div>
      <app-heroes></app-heroes>
    `
    })
    export class AppComponent {
      title = 'app';
    }

    ```

2. Det finns andra komponenter i **heroes.components.ts** som vi refererar till, t.ex. Hero-komponenten, så vi måste skapa även detta. Kör följande kommando i kommandotolken i Angular CLI för att skapa en hero-modell och en fil med namnet **hero.ts**, där g = generera, cl = klass och hero = namnet på klassen.

    ```bash
    ng g cl hero
    ```

3. Öppna **src\app\hero.ts** i Explorer-fönstret. I **hero.ts** ersätter du innehållet i filen med följande kod, som lägger till en Hero-klass med ett id, ett name och en saying.

    ```ts
      export class Hero {
      id: number;
      name: string;
      saying: string;
    }
    ```

4. Gå tillbaka till **heroes.components.ts** och observera att `Hero` på raden `selectedHero: Hero;` (rad 10) har en röd understrykning. 

5. Vänsterklicka på termen `Hero` så visas en ikon av en glödlampa i Visual Studio till vänster om kodblocket. 

    :::image type="content" source="./media/tutorial-develop-mongodb-nodejs-part3/light-bulb.png" alt-text="Glödlampa i Visual Studio Code":::

6. Klicka på glödlampan och sedan på **Importera Hero från "/app/hero".** eller **Importera Hero från ”./hero”.** (Meddelandet varierar beroende på din konfiguration)

    En ny rad med kod visas på rad 2. Om rad 2 refererar till /app/hero ändrar du den så att den refererar till hero-filen från den lokala mappen (./hero). Rad 2 bör se ut så här:

   ```
   import { Hero } from "./hero";
   ``` 

    Nu är modellen på plats, men vi måste fortfarande skapa tjänsten.

## <a name="create-the-service"></a>Skapa tjänsten

1. Skriv följande kommando i kommandotolken i Angular CLI för att skapa en hero-tjänst i **app.module.ts**, där g = generera, s = tjänst, hero = namnet på tjänsten och -m = lägg till i app.module.

    ```bash
    ng g s hero -m app.module
    ```

2. Gå tillbaka till **heroes.components.ts** i Visual Studio Code. Observera att `HeroService` på raden `constructor(private heroService: HeroService) {}` (rad 13) har en röd understrykning. Klicka på `HeroService` så visas glödlampan till vänster om kodblocket. Klicka på glödlampan och sedan på **Importera HeroService från ”./hero.service ”** eller till **Importera HeroService från ”/app/hero.service”.**

    När du klickar på glödlampan infogas en ny rad med kod på rad 2. Om rad 2 refererar till mappen /app/hero.service ändrar du den så att den refererar till hero-filen från den lokala mappen (./hero.service). Rad 2 bör se ut så här:
    
    ```javascript
    import { HeroService } from "./hero.service"
    ```

3. Öppna **hero.service.ts** i Visual Studio Code och kopiera följande kod genom att ersätta innehållet i filen.

    ```ts
    import { Injectable } from '@angular/core';
    import { HttpClient } from '@angular/common/http';
    
    import { Hero } from './hero';
    
    const api = '/api';

    @Injectable()
    export class HeroService {
      constructor(private http: HttpClient) {}

      getHeroes() {
        return this.http.get<Array<Hero>>(`${api}/heroes`)
      }

      deleteHero(hero: Hero) {
        return this.http.delete(`${api}/hero/${hero.id}`);
      }

      addHero(hero: Hero) {
        return this.http.post<Hero>(`${api}/hero/`, hero);
      }

      updateHero(hero: Hero) {
        return this.http.put<Hero>(`${api}/hero/${hero.id}`, hero);
      }
    }
    ```

    Den här koden använder den senaste versionen av HttpClient som Angular tillhandahåller, som är en modul som du måste ange. Och det ska vi göra nu.

4. Öppna **app.module.ts** i Visual Studio Code och importera HttpClientModule genom att uppdatera importavsnittet så att det innehåller HttpClientModule.

    ```ts
    imports: [
      BrowserModule,
      FormsModule,
      HttpClientModule
    ],
    ```

5. I **app.module.ts** lägger du till HttpClientModule-importinstruktionen till listan med importer.

    ```ts
    import { HttpClientModule } from '@angular/common/http';
    ```

6. Spara alla filer i Visual Studio Code.

## <a name="build-the-app"></a>Skapa appen

1. Skapa Angular-programmet genom att skriva följande kommando i kommandotolken. 

    ```bash
    ng b
    ``` 

    Om det uppstår problem visas information om de filer som behöver åtgärdas. När appen har skapats läggs de nya filerna till i mappen **dist**. Du kan granska de nya filerna i mappen **dist** om du vill.

    Nu ska vi köra appen.

2. I Visual Studio Code klickar du på  knappen Felsök :::image type="icon" source="./media/tutorial-develop-mongodb-nodejs-part2/debug-button.png"::: till vänster och klickar sedan på knappen **Starta fel sökning** :::image type="icon" source="./media/tutorial-develop-mongodb-nodejs-part3/start-debugging-button.png"::: .

3. Öppna en webbläsare och gå till **localhost:3000** och se när appen körs lokalt.

     :::image type="content" source="./media/tutorial-develop-mongodb-nodejs-part3/azure-cosmos-db-mongodb-mean-app.png" alt-text="Hero-programmet körs lokalt":::

## <a name="next-steps"></a>Nästa steg

I den här delen av självstudiekursen har du gjort följande:

> [!div class="checklist"]
> * Skapat Angular-användargränssnittet
> * Testat appen lokalt

Fortsätt till nästa del av självstudiekursen och skapa ett Azure Cosmos DB-konto.

> [!div class="nextstepaction"]
> [Skapa ett Azure Cosmos DB-konto med hjälp av Azure CLI](tutorial-develop-mongodb-nodejs-part4.md)
