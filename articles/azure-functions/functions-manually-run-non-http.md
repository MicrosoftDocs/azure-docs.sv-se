---
title: Köra en Azure-funktion som inte utlösts av HTTP manuellt
description: Använda en HTTP-begäran för att köra icke-HTTP-utlöst Azure Functions
author: craigshoemaker
ms.topic: article
ms.date: 04/23/2020
ms.author: cshoe
ms.openlocfilehash: 79aebf7ed80fea370ff7a5d5cc40911da4144414
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91537709"
---
# <a name="manually-run-a-non-http-triggered-function"></a>Köra en funktion som inte utlösts av HTTP manuellt

Den här artikeln visar hur du manuellt kör en icke HTTP-utlöst funktion via en särskilt formaterad HTTP-begäran.

I vissa sammanhang kan du behöva köra en Azure-funktion ”på begäran” som utlöses indirekt.  Exempel på indirekta utlösare är [funktioner enligt ett schema](./functions-create-scheduled-function.md) eller funktioner som fungerar som ett resultat av [en annan resursåtgärd](./functions-create-storage-blob-triggered-function.md). 

[Postman](https://www.getpostman.com/) används i följande exempel, men du kan använda [cURL](https://curl.haxx.se/), [Fiddler](https://www.telerik.com/fiddler) eller något annat liknande verktyg för att skicka HTTP-begäranden.

## <a name="define-the-request-location"></a>Definiera platsen för begäran

Om du vill köra en icke-HTTP-utlöst funktion behöver du ett sätt att skicka en begäran till Azure för att köra funktionen. Den URL som används för att göra denna begäran kräver ett visst formulär.

![Definiera platsen för begäran: värdnamn och mappsökväg + funktionsnamn](./media/functions-manually-run-non-http/azure-functions-admin-url-anatomy.png)

- **Värdnamn:** Funktions appens offentliga plats som består av Function-appens namn plus *azurewebsites.net* eller din anpassade domän.
- **Mappsökväg:** För att få åtkomst till icke-HTTP-utlösta funktioner via en HTTP-begäran måste du skicka begäran via mapparna *admin/Functions*.
- **Funktions namn:** Namnet på den funktion som du vill köra.

Du kan använda den här platsen för begäran i Postman tillsammans med funktionens huvudnyckel i förfrågan till Azure om att köra funktionen.

> [!NOTE]
> När du kör lokalt krävs inte funktionens huvudnyckel. Du kan [anropa funktionen](#call-the-function) direkt genom att utesluta `x-functions-key`-rubriken.

## <a name="get-the-functions-master-key"></a>Hämta funktionens huvudnyckel

1. Navigera till din Function-app i [Azure Portal](https://portal.azure.com), Välj **app-nycklar** och sedan `_master` nyckeln. 

    :::image type="content" source="./media/functions-manually-run-non-http/azure-portal-functions-master-key.png" alt-text="Leta upp huvud nyckeln som ska kopieras." border="true":::

1. I avsnittet **Redigera nyckel** kopierar du nyckelvärdet till Urklipp och väljer sedan **OK**.

    :::image type="content" source="./media/functions-manually-run-non-http/azure-portal-functions-master-key-copy.png" alt-text="Kopiera huvud nyckeln till Urklipp." border="true":::

1. När du har kopierat *_master* -nyckeln väljer du **kod + test** och väljer sedan **loggar**. Du ser loggade meddelanden från funktionen här när du kör funktionen manuellt från Postman.

    :::image type="content" source="./media/functions-manually-run-non-http/azure-portal-function-log.png" alt-text="Skärm bild som visar sidan &quot;kod + test&quot; med ett meddelande från de loggar som visas." border="true":::

> [!CAUTION]  
> På grund av de utökade behörigheterna i din funktionsapp som beviljats av huvudnyckeln bör du inte dela den här nyckeln med tredje part eller distribuera den i ett program. Nyckeln ska bara skickas till en HTTPS-slutpunkt.

## <a name="call-the-function"></a>Anropa funktionen

Öppna Postman och följ de här stegen:

1. Ange din **begäran om plats i URL-textrutan**.
1. Kontrollera att HTTP-metoden är inställd på **POST**.
1. Välj fliken **sidhuvud** .
1. Skriv **x-Functions-Key** som den första nyckeln och klistra in huvud nyckeln (från Urklipp) som värde.
1. Skriv **Content-Type** som den andra nyckeln och skriv **Application/JSON** som värde.

    :::image type="content" source="./media/functions-manually-run-non-http/functions-manually-run-non-http-headers.png" alt-text="Inställningar för Postman-rubriker." border="true":::

1. Välj fliken **brödtext**.
1. Skriv **{"indatamängd": "test"}** som brödtext för begäran.

    :::image type="content" source="./media/functions-manually-run-non-http/functions-manually-run-non-http-body.png" alt-text="Inställningar för Postman-brödtext." border="true":::

1. Välj **Skicka**.
        
    :::image type="content" source="./media/functions-manually-run-non-http/functions-manually-run-non-http-send.png" alt-text="Skicka en begäran med Postman." border="true":::

    Postman rapporterar statusen **202 - Godkänd**.

1. Gå därefter tillbaka till din funktion på Azure-portalen. Granska loggarna så kommer du att se meddelanden från det manuella anropet till funktionen.

    :::image type="content" source="./media/functions-manually-run-non-http/azure-portal-functions-master-key-logs.png" alt-text="Visa loggarna för att se test resultaten för huvud nyckeln." border="true":::

## <a name="next-steps"></a>Nästa steg

- [Strategier för att testa din kod i Azure Functions](./functions-test-a-function.md)
- [Lokal felsökning av Event Grid-utlösare i Azure Functions](./functions-debug-event-grid-trigger-local.md)
