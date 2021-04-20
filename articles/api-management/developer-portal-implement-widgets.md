---
title: Implementera widgetar i utvecklarportalen
titleSuffix: Azure API Management
description: Lär dig hur du implementerar widgetar som använder data från externa API:er och visar dem API Management utvecklarportalen.
author: dlepow
ms.author: apimpm
ms.date: 04/15/2021
ms.service: api-management
ms.topic: how-to
ms.openlocfilehash: 4bda136a1abe8f9ffb443973731ebbe13b56ac3b
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107741853"
---
# <a name="implement-widgets-in-the-developer-portal"></a>Implementera widgetar i utvecklarportalen

I den här självstudien implementerar du en widget som använder data från ett externt API och visar den på API Management utvecklarportalen.

Widgeten hämtar sessionsbeskrivningar från [exempelkonferens-API:et](https://conferenceapi.azurewebsites.net/?format=json). Sessionsidentifieraren anges via en särskild widgetredigerare.

För att hjälpa dig i utvecklingsprocessen kan du gå till den slutförda widgeten som finns i mappen `examples` på API Management-utvecklarportalens [GitHub-lagringsplats:](https://github.com/Azure/api-management-developer-portal/) `/examples/widgets/conference-session` .

:::image type="content" source="media/developer-portal-implement-widgets/widget-published.png" alt-text="Skärmbild av publicerad widget":::

## <a name="prerequisites"></a>Förutsättningar

* Konfigurera en lokal [miljö för](developer-portal-self-host.md#step-1-set-up-local-environment) den senaste versionen av utvecklarportalen.

* Du bör förstå [paperbits-widgetens uppbyggnad.](https://paperbits.io/wiki/widget-anatomy)


## <a name="copy-the-scaffold"></a>Kopiera scaffold

Använd ett `widget` scaffold från mappen `/scaffolds` som utgångspunkt för att skapa den nya widgeten.

1. Kopiera mappen `/scaffolds/widget` till `/community/widgets` .
1. Byt namn på mappen till `conference-session` .

## <a name="rename-exported-module-classes"></a>Byta namn på exporterade modulklasser

Byt namn på de exporterade modulklasserna genom att `Widget` ersätta prefixet `ConferenceSession` med i följande filer:

- `widget.design.module.ts`

- `widget.publish.module.ts`

- `widget.runtime.module.ts`
    
I filen kan du `widget.design.module.ts` till exempel ändra till `WidgetDesignModule` `ConferenceSessionDesignModule` :
    
```typescript
export class WidgetDesignModule implements IInjectorModule {
```
på 
    
```typescript
export class ConferenceSessionDesignModule implements IInjectorModule {
```
    
   
## <a name="register-the-widget"></a>Registrera widgeten

Registrera widgetens moduler i portalens rotmoduler genom att lägga till följande rader i respektive filer:

1. `src/apim.design.module.ts` – en modul som registrerar designtidsberoenden.

   ```typescript
   import { ConferenceSessionDesignModule } from "../community/widgets/conference-session/widget.design.module";
   
   ...
   injector.bindModule(new ConferenceSessionDesignModule());
   ```
1. `src/apim.publish.module.ts` – en modul som registrerar publish-time-beroenden.

   ```typescript
   import { ConferenceSessionPublishModule } from "../community/widgets/conference-session/widget.publish.module";

    ...

    injector.bindModule(new ConferenceSessionPublishModule());
    ```

1. `src/apim.runtime.module.ts` – körningsberoenden.

    ```typescript
    import { ConferenceSessionRuntimeModule } from "../community/widgets/conference-session/widget.runtime.module";

    ...

    injector.bindModule(new ConferenceSessionRuntimeModule());
    ```

## <a name="place-the-widget-in-the-portal"></a>Placera widgeten i portalen

Nu är du redo att ansluta det duplicerade scaffold-et och använda det i utvecklarportalen.

1. Kör kommandot `npm start`.

1. När programmet läses in placerar du den nya widgeten på en sida. Du hittar den under namnet `Your widget` i kategorin `Community` i widgetväljaren.

    :::image type="content" source="media/developer-portal-implement-widgets/widget-selector.png" alt-text="Skärmbild av widgetväljaren":::

1. Spara sidan genom att trycka **på** + **Ctrl S** (eller **⌘** + **S** på macOS).

    > [!NOTE]
    > I designtid kan du fortfarande interagera med webbplatsen genom att hålla ned **tangenten Ctrl** **(eller ⌘).**

## <a name="add-custom-properties"></a>Lägga till anpassade egenskaper

För att widgeten ska kunna hämta sessionsbeskrivningar måste den vara medveten om sessionsidentifieraren. Lägg till `Session ID` egenskapen i respektive gränssnitt och klasser:

För att widgeten ska kunna hämta sessionsbeskrivningen måste den vara medveten om sessionsidentifieraren. Lägg till egenskapen sessions-ID i respektive gränssnitt och klasser:

1. `widgetContract.ts` – datakontrakt (dataskikt) som definierar hur widgetkonfigurationen bevaras.

    ```typescript
    export interface WidgetContract extends Contract {
        sessionNumber: string;
    }
    ```

1. `widgetModel.ts` – modell (affärsskikt) – en primär representation av widgeten i systemet. Den uppdateras av redigerare och återges av presentationslagret.

    ```typescript
    export class WidgetModel {
        public sessionNumber: string;
    }
    ```

1. `ko/widgetViewModel.ts` – viewmodel (presentationslager) – ett UI-ramverksspecifikt objekt som utvecklarportalen återger med HTML-mallen.

    > [!NOTE]
    > Du behöver inte ändra något i den här filen.

## <a name="configure-binders"></a>Konfigurera binders

Aktivera flödet för från `sessionNumber` datakällan till widgetpresentationen. Redigera `ModelBinder` entiteterna `ViewModelBinder` och :

1. `widgetModelBinder.ts` hjälper till att förbereda modellen med hjälp av de data som beskrivs i kontraktet.

    ```typescript
    export class WidgetModelBinder implements IModelBinder<WidgetModel> {
        public async contractToModel(contract: WidgetContract): Promise<WidgetModel> {
            model.sessionNumber = contract.sessionNumber || "107"; // 107 is the default session id
            ...
        }
    
        public modelToContract(model: WidgetModel): Contract {
            const contract: WidgetContract = {
                sessionNumber: model.sessionNumber
                ...
            };
            ...
        }
    }
    ```

1. `ko/widgetViewModelBinder.ts` vet hur utvecklarportalen behöver presentera modellen (som en viewmodel) i ett specifikt UI-ramverk.

    ```typescript
    ...
    public async updateViewModel(model: WidgetModel, viewModel: WidgetViewModel): Promise<void> {
            viewModel.runtimeConfig(JSON.stringify({
                sessionNumber: model.sessionNumber
            }));
        }
    }
    ...
    ```

## <a name="adjust-design-time-widget-template"></a>Justera mall för designtidswidget

Komponenterna i varje omfång körs oberoende av varandra. De har separata containrar för beroendeinjektion, sin egen konfiguration, livscykel osv. De kan till och med drivas av olika UI-ramverk (i det här exemplet är det Dockout JS).

Från designtidsperspektivet är alla körningskomponenter bara en HTML-tagg med vissa attribut och/eller innehåll. Konfigurationen skickas vid behov med oformaterad kod. I enkla fall, som i det här exemplet, skickas parametern i attributet . Om konfigurationen är mer komplex kan du använda en identifierare för de obligatoriska inställningar som hämtas av en angiven konfigurationsprovider (till exempel `ISettingsProvider` ).

1. Uppdatera `ko/widgetView.html` filen:

    ```html
    <widget-runtime data-bind="attr: { params: runtimeConfig }"></widget-runtime>
    ```

    När utvecklarportalen `attr` kör *bindningen i design-* eller publiceringstid är *html-koden* följande:

    ```html
    <widget-runtime params="{ sessionNumber: 107 }"></widget-runtime>
    ```

    I körningen kommer komponenten `widget-runtime` sedan att läsa och använda den i `sessionNumber` initieringskoden (se nedan).

1. Uppdatera filen `widgetHandlers.ts` för att tilldela sessions-ID:t när den skapas:

    ```typescript
    ...
    createModel: async () => {
        var model = new ConferenceSessionModel();
        model.sessionNumber = "107";
            return model;
        }
    ...
    ```

## <a name="revise-runtime-view-model"></a>Ändra körningsvymodell

Körningskomponenter är den kod som körs på själva webbplatsen. I till exempel API Management-utvecklarportalen är de alla skript bakom dynamiska komponenter (till exempel *API-information,* *API-konsol),* hanteringsåtgärder som generering av kodexempel, sändning av begäranden osv.

Körningskomponentens visningsmodell måste ha följande metoder och egenskaper:

- Egenskapen (markerad med decorator) som används som en parameter för komponentindata som skickas utifrån (markeringen som genererades under `sessionNumber` `Param` designtiden; se föregående steg).
- Egenskapen `sessionDescription` som är bunden till widgetmallen (se `widget-runtime.html` senare i den här artikeln).
- Metoden `initialize` (med `OnMounted` decorator) anropas när widgeten har skapats och alla dess parametrar har tilldelats. Det är en bra plats att läsa och `sessionNumber` anropa API:et med hjälp av `HttpClient` . `HttpClient`är ett beroende som matas in av IoC-containern (inversion av kontroll).

- Först skapar utvecklarportalen widgeten och tilldelar alla dess parametrar. Sedan anropas `initialize` metoden .

    ```typescript
    ...
    import * as ko from "knockout";
    import { Component, RuntimeComponent, OnMounted, OnDestroyed, Param } from "@paperbits/common/ko/decorators";
    import { HttpClient, HttpRequest } from "@paperbits/common/http";
    ...

    export class WidgetRuntime {
        public readonly sessionDescription: ko.Observable<string>;

        constructor(private readonly httpClient: HttpClient) {
            ...
            this.sessionNumber = ko.observable();
            this.sessionDescription = ko.observable();
            ...
        }

        @Param()
        public readonly sessionNumber: ko.Observable<string>;

        @OnMounted()
        public async initialize(): Promise<void> {
            ...
            const sessionNumber = this.sessionNumber();

            const request: HttpRequest = {
                url: `https://conferenceapi.azurewebsites.net/session/${sessionNumber}`,
                method: "GET"
            };

            const response = await this.httpClient.send<string>(request);
            const sessionDescription = response.toText();
    
            this.sessionDescription(sessionDescription);
            ...
        }
        ...
    }
    ```

## <a name="tweak-the-widget-template"></a>Justera widgetmallen

Uppdatera widgeten för att visa sessionsbeskrivningen.

Använd en stycketagg och en `markdown` (eller `text` ) bindning i filen för att återge `ko/runtime/widget-runtime.html` beskrivningen:

```html
<p data-bind="markdown: sessionDescription"></p>
```

## <a name="add-the-widget-editor"></a>Lägga till widgetredigeraren

Widgeten har nu konfigurerats för att hämta beskrivningen av sessionen `107` . Du har `107` angett i koden som standardsession. Kontrollera att du har gjort allt rätt genom att `npm start` köra och bekräfta att utvecklarportalen visar beskrivningen på sidan.

Utför nu dessa steg så att användaren kan konfigurera sessions-ID:t via en widgetredigerare:

1. Uppdatera `ko/widgetEditorViewModel.ts` filen:

    ```typescript
    export class WidgetEditor implements WidgetEditor<WidgetModel> {
        public readonly sessionNumber: ko.Observable<string>;

        constructor() {
            this.sessionNumber = ko.observable();
        }

        @Param()
        public model: WidgetModel;

        @Event()
        public onChange: (model: WidgetModel) => void;

        @OnMounted()
        public async initialize(): Promise<void> {
            this.sessionNumber(this.model.sessionNumber);
            this.sessionNumber.subscribe(this.applyChanges);
        }

        private applyChanges(): void {
            this.model.sessionNumber = this.sessionNumber();
            this.onChange(this.model);
        }
    }
    ```

    Redigeringsvymodellen använder samma metod som du har sett tidigare, men det finns en ny egenskap `onChange` , som är utsmyckad med `@Event()` . Den binder återanropet för att meddela lyssnarna (i det här fallet en innehållsredigerare) om ändringar i modellen.

1. Uppdatera `ko/widgetEditorView.html` filen:

    ```html
    <input type="text" class="form-control" data-bind="textInput: sessionNumber" />
    ```

1. Kör `npm start` igen. Du bör kunna ändra i `sessionNumber` widgetredigeraren. Ändra `108` ID:t till , spara ändringarna och uppdatera webbläsarens flik. Om du har problem kan du behöva lägga till widgeten på sidan igen.

    :::image type="content" source="media/developer-portal-implement-widgets/widget-editor.png" alt-text="Skärmbild av widgetredigeraren":::

## <a name="rename-the-widget"></a>Byt namn på widgeten

Ändra widgetnamnet i `constants.ts` filen:

```typescript
...
export const widgetName = "conference-session";
export const widgetDisplayName = "Conference session";
...
```

> [!NOTE]
> Om du bidrar widgeten till lagringsplatsen måste vara samma som dess mappnamn och måste härleds från visningsnamnet (gemener och blanksteg ersätts med `widgetName` bindestreck). Kategorin bör förbli `Community` .

## <a name="next-steps"></a>Nästa steg


Läs mer om utvecklarportalen:

- [Översikt över Azure API Management-utvecklarportalen](api-management-howto-developer-portal.md)

- [Bidra med widgetar](developer-portal-widget-contribution-guidelines.md) – vi tar gärna emot och uppmuntrar communitybidrag.

- Se [Använda communitywidgetar](developer-portal-use-community-widgets.md) för att lära dig hur du använder widgetar som har bidragit från communityn.
