---
title: Anpassa den anslutna fabriks lösningen – Azure | Microsoft Docs
description: En beskrivning av hur du anpassar beteendet för den anslutna fabriks lösnings acceleratorn.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.devlang: csharp
ms.topic: conceptual
ms.date: 12/14/2017
ms.author: dobett
ms.openlocfilehash: a092859517ee6ac41e62e55fe95f72b8bfdf87d5
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105646252"
---
# <a name="customize-how-the-connected-factory-solution-displays-data-from-your-opc-ua-servers"></a>Anpassa hur den anslutna fabriks lösningen visar data från OPC UA-servrar

> [!IMPORTANT]
> Medan vi uppdaterar den här artikeln kan du läsa mer i [Azures industriella IoT](https://azure.github.io/Industrial-IoT/) .

Den anslutna fabriks lösningen sammanställer och visar data från OPC UA-servrar som är anslutna till lösningen. Du kan bläddra och skicka kommandon till OPC UA-servrar i din lösning. Mer information om OPC UA finns i [vanliga frågor och svar om ansluten fabrik](iot-accelerators-faq-cf.md).

Exempel på sammanställda data i lösningen är OEE (total Equipment effektivitet) och KPI: er (nyckeltal) som du kan visa i instrument panelen på fabriks-, linje-och Stations nivåerna. Följande skärm bild visar OEE och KPI-värden för **monterings** stationen på **produktions rad 1** i **München** -fabriken:

![Exempel på OEE och KPI-värden i lösningen][img-oee-kpi]

Lösningen gör att du kan visa detaljerad information från särskilda data objekt från OPC UA-servrar, som kallas *stationer*. Följande skärm bild visar observationer av antalet tillverkade objekt från en speciell Station:

![Observationer av antalet tillverkade objekt][img-manufactured-items]

Om du klickar på någon av diagrammen kan du utforska data ytterligare med hjälp av Time Series Insights (TSD):

![Utforska data med Time Series Insights][img-tsi]

I den här artikeln beskrivs:

- Hur data görs tillgängliga för de olika vyerna i lösningen.
- Hur du kan anpassa hur lösningen visar data.

## <a name="data-sources"></a>Datakällor

Den anslutna fabriks lösningen visar data från OPC UA-servrar som är anslutna till lösningen. Standard installationen omfattar flera OPC UA-servrar som kör en fabriks simulering. Du kan lägga till dina egna OPC UA-servrar som [ansluter via en gateway] [lnk-Connect-CF] till din lösning.

Du kan bläddra bland de data objekt som en ansluten OPC UA-Server kan skicka till din lösning på instrument panelen:

1. Välj **webbläsare** för att navigera till vyn **Välj en OPC UA-Server** :

    ![Navigera till vyn Välj en OPC UA-Server][img-select-server]

1. Välj en server och klicka på **Anslut**. Klicka på **Fortsätt** när säkerhets varningen visas.

    > [!NOTE]
    > Den här varningen visas bara en gång för varje server och upprättar en förtroende relation mellan lösningens instrument panel och servern.

1. Nu kan du bläddra bland de data objekt som servern kan skicka till lösningen. Objekt som skickas till lösningen har en bock markering:

    ![Publicerade objekt][img-published]

1. Om du är *administratör* i lösningen kan du välja att publicera ett data objekt så att det blir tillgängligt i den anslutna fabriks lösningen. Som administratör kan du också ändra värdet för data objekt och anropa metoder i OPC UA-servern.

## <a name="map-the-data"></a>Mappa data

Den anslutna fabriks lösningen mappar och sammanställer de publicerade data objekten från OPC UA-servern till de olika vyerna i lösningen. Den anslutna fabriks lösningen distribuerar till ditt Azure-konto när du etablerar lösningen. En JSON-fil i den Visual Studio-anslutna fabriks lösningen lagrar mappnings informationen. Du kan visa och ändra den här JSON-konfigurationsfilen i den anslutna fabrikens Visual Studio-lösningen. Du kan distribuera om lösningen när du har gjort en ändring.

Du kan använda konfigurations filen för att:

- Redigera befintliga simulerade fabriker, produktions linjer och stationer.
- Mappa data från verkliga OPC UA-servrar som du ansluter till lösningen.

Mer information om hur du mappar och aggregerar data så att de uppfyller dina särskilda krav finns i [så här konfigurerar du den anslutna fabriks lösnings acceleratorn ](iot-accelerators-connected-factory-configure.md).

## <a name="deploy-the-changes"></a>Distribuera ändringarna

När du är klar med att göra ändringar i **ContosoTopologyDescription.js** filen måste du distribuera om den anslutna fabriks lösningen till ditt Azure-konto.

**Azure-IoT-Connected-Factory-** databasen innehåller ett **build.ps1** PowerShell-skript som du kan använda för att återskapa och distribuera lösningen.

## <a name="next-steps"></a>Nästa steg

Läs mer om den anslutna fabriks lösnings acceleratorn genom att läsa följande artiklar:

* [Behörigheter för azureiotsolutions.com-webbplatsen][lnk-permissions]
* [Vanliga frågor och svar om ansluten fabrik](iot-accelerators-faq-cf.md)
* [Vanliga frågor och svar][lnk-faq]


[img-oee-kpi]: ./media/iot-accelerators-connected-factory-customize/oeenadkpi.png
[img-manufactured-items]: ./media/iot-accelerators-connected-factory-customize/manufactured.png
[img-tsi]: ./media/iot-accelerators-connected-factory-customize/tsi.png
[img-select-server]: ./media/iot-accelerators-connected-factory-customize/selectserver.png
[img-published]: ./media/iot-accelerators-connected-factory-customize/published.png


[lnk-permissions]: iot-accelerators-permissions.md
[lnk-faq]: iot-accelerators-faq.md