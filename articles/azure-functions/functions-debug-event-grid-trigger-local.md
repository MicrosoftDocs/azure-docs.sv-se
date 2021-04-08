---
title: Azure Functions Event Grid lokal fel sökning
description: Lär dig att lokalt felsöka Azure Functions som utlöses av en Event Grid-händelse
author: craigshoemaker
ms.topic: conceptual
ms.date: 10/18/2018
ms.author: cshoe
ms.openlocfilehash: be05d237d2799404c3fd8b5733464e23eeb49aa3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94833069"
---
# <a name="azure-function-event-grid-trigger-local-debugging"></a>Lokal felsökning av Event Grid-utlösare i Azure Functions

Den här artikeln visar hur du felsöker en lokal funktion som hanterar en Azure Event Grid-händelse som aktive ras av ett lagrings konto. 

## <a name="prerequisites"></a>Förutsättningar

- Skapa eller Använd en befintlig Function-app
- Skapa eller Använd ett befintligt lagrings konto
- Ladda ned [ngrok](https://ngrok.com/) så att Azure kan anropa din lokala funktion

## <a name="create-a-new-function"></a>Skapa en ny funktion

Öppna din Function-app i Visual Studio och högerklicka på projekt namnet i Solution Explorer och klicka på **Lägg till > ny Azure-funktion**.

I den *nya Azure Function* -fönstret väljer du **Event Grid utlösare** och klickar på **OK**.

![Skapa ny funktion](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-add-function.png)

När funktionen har skapats öppnar du kod filen och kopierar URL: en som är kommenterad överst i filen. Den här platsen används när du konfigurerar Event Grid-utlösaren.

![Kopierings plats](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-copy-location.png)

Ange sedan en Bryt punkt på raden som börjar med `log.LogInformation` .

![Ange Bryt punkt](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-set-breakpoint.png)


Tryck sedan **på F5** för att starta en felsökningssession.

## <a name="allow-azure-to-call-your-local-function"></a>Tillåt Azure att anropa din lokala funktion

Om du vill dela upp en funktion som felsöks på datorn måste du aktivera ett sätt för Azure att kommunicera med din lokala funktion från molnet.

Verktyget [ngrok](https://ngrok.com/) är ett sätt för Azure att anropa funktionen som körs på din dator. Starta *ngrok* med följande kommando:

```bash
ngrok http -host-header=localhost 7071
```
När verktyget har kon figurer ATS bör kommando fönstret se ut ungefär som på följande skärm bild:

![Skärm bild som visar kommando tolken när du har startat verktyget "ngrok".](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-ngrok.png)

Kopiera **https** -URL: en som genererades när *ngrok* körs. Det här värdet används när du konfigurerar händelse slut punkten för Event Grid.

## <a name="add-a-storage-event"></a>Lägg till en lagrings händelse

Öppna Azure Portal och navigera till ett lagrings konto och klicka på alternativet **händelser** .

![Lägg till lagrings konto händelse](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-add-event.png)

I fönstret *händelser* klickar du på knappen **händelse prenumeration** . I fönstret *händelse prenumeration* klickar du på list rutan *slut punkts typ* och väljer **Web Hook**.

![Välj prenumerations typ](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-event-subscription-type.png)

När du har konfigurerat slut punkts typen klickar du på **Välj en slut punkt** för att konfigurera slut punkt svärdet.

![Välj typ av slut punkt](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-event-subscription-endpoint.png)

Värdet för *prenumerantens slut punkt* består av tre olika värden. Prefixet är HTTPS-URL: en som genereras av *ngrok*. Resten av URL: en kommer från den URL som finns i funktions kod filen, med funktions namnet tillagt i slutet. Från och med URL: en från funktions kod filen ersätts *ngrok* URL `http://localhost:7071` och funktions namnet ersätts `{functionname}` .

Följande skärm bild visar hur den sista URL: en ska se ut:

![Slut punkts val](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-event-subscription-endpoint-selection.png)

När du har angett rätt värde klickar du på **Bekräfta markering**.

> [!IMPORTANT]
> Varje gång du startar *ngrok* återskapas HTTPS-URL: en och värdet ändras. Därför måste du skapa en ny händelse prenumeration varje gången du exponerar din funktion för Azure via *ngrok*.

## <a name="upload-a-file"></a>Ladda upp en fil

Nu kan du ladda upp en fil till ditt lagrings konto för att utlösa en Event Grid händelse för din lokala funktion att hantera. 

Öppna [Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) och Anslut till ditt lagrings konto. 

- Expandera **BLOB-behållare** 
- Högerklicka och välj **skapa BLOB-behållare**.
- Namnge container **testet**
- Välj *test* behållare
- Klicka på knappen **överför**
- Klicka på **överför filer**
- Välj en fil och överför den till BLOB-behållaren

## <a name="debug-the-function"></a>Felsöka funktionen

När Event Grid identifierat en ny fil överförs till lagrings behållaren, trycks Bryt punkten i din lokala funktion.

![Starta ngrok](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-breakpoint.png)

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill rensa resurserna som skapats i den här artikeln tar du bort **test** behållaren i ditt lagrings konto.

## <a name="next-steps"></a>Nästa steg

- [Automatisera storleksändring av överförda bilder med Event Grid](../event-grid/resize-images-on-storage-blob-upload-event.md)
- [Event Grid utlösare för Azure Functions](./functions-bindings-event-grid.md)
