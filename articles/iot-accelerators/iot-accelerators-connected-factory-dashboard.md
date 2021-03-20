---
title: Använd den anslutna fabriks instrument panelen – Azure | Microsoft Docs
description: Den här artikeln beskriver hur du använder funktioner i den anslutna fabriks instrument panelen för att övervaka och hantera dina industriella IoT-enheter.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 07/10/2018
ms.author: dobett
ms.openlocfilehash: 797bd97940aa49db87b4ca4dd96f140208cd69b6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96011225"
---
# <a name="use-features-in-the-connected-factory-solution-accelerator-dashboard"></a>Använd funktioner på instrument panelen för ansluten fabriks lösnings Accelerator

> [!IMPORTANT]
> Medan vi uppdaterar den här artikeln kan du läsa mer i [Azures industriella IoT](https://azure.github.io/Industrial-IoT/) .

Genom att [distribuera en molnbaserad lösning för att hantera min snabb start för industriella IoT-enheter](quickstart-connected-factory-deploy.md) visade du hur du navigerar på instrument panelen och svarar på larm. I den här instruktions guiden visas några ytterligare instrument panels funktioner som du kan använda för att övervaka och hantera dina industriella IoT-enheter.

## <a name="apply-filters"></a>Använda filter

Du kan filtrera informationen som visas på instrument panelen antingen på panelen **fabriks platser** eller **larm** panelen:

1. Klicka på **tratten** för att visa en lista över tillgängliga filter på panelen med fabriksplatser eller på larmpanelen.

1. Panelen filter visas:

    [![Filter för lösningsacceleratorn Ansluten fabrik](./media/iot-accelerators-connected-factory-dashboard/filterpanel-inline.png)](./media/iot-accelerators-connected-factory-dashboard/filterpanel-expanded.png#lightbox)

1. Välj det filter du behöver och klicka på **Använd**. Det är också möjligt att skriva fri text i filter fälten.

1. Filtret används sedan. Ikonen extra tratt indikerar att ett filter appliceras:

    [![Filtret för ansluten fabriks lösnings Accelerator används](./media/iot-accelerators-connected-factory-dashboard/filterapplied-inline.png)](./media/iot-accelerators-connected-factory-dashboard/filterapplied-expanded.png#lightbox)

    > [!NOTE]
    > Ett aktivt filter påverkar inte de visade OEE och KPI-värdena, utan filtrerar bara List innehållet.

1. Ta bort ett filter genom att klicka på tratten och klicka på **Rensa** i filter panelen.

## <a name="browse-an-opc-ua-server"></a>Navigera i en OPC UA-serverläsare

När du distribuerar Solution Accelerator etablerar du automatiskt en uppsättning simulerade OPC UA-servrar som du kan bläddra från instrument panelen. Simulerade servrar gör det enkelt för dig att experimentera med lösnings acceleratorn utan att behöva distribuera riktiga servrar.

1. Klicka på **webb läsar ikonen** i navigerings fältet i instrument panelen:

    [![Serverwebbläsare för lösningsacceleratorn Ansluten fabrik](./media/iot-accelerators-connected-factory-dashboard/browser-inline.png)](./media/iot-accelerators-connected-factory-dashboard/browser-expanded.png#lightbox)

1. Välj en av de servrar i listan som visar de servrar som distribueras åt dig i Solution Accelerator:

    [![Lista över anslutna fabriks lösnings acceleratorer](./media/iot-accelerators-connected-factory-dashboard/serverlist-inline.png)](./media/iot-accelerators-connected-factory-dashboard/serverlist-expanded.png#lightbox)

1. Klicka på **Connect** (Anslut) för att visa en dialogruta för säkerhet. För simuleringen är det säkert att klicka på **Fortsätt**.

1. Om du vill expandera någon av noderna i serverträdet klickar du på noden. Noder som publicerar telemetri har en bock bredvid sig:

    [![Serverträd för lösningsacceleratorn Ansluten fabrik](./media/iot-accelerators-connected-factory-dashboard/servertree-inline.png)](./media/iot-accelerators-connected-factory-dashboard/servertree-expanded.png#lightbox)

1. Högerklicka på ett objekt om du vill läsa, skriva, publicera eller anropa noden. Vilka åtgärder som är tillgängliga beror på dina behörigheter och nodens attribut. Läsningsalternativet visar en kontextpanel med värdet för den specifika noden. Skrivalternativet visar en kontextpanel där du kan ange ett nytt värde. Anropsalternativet visar en nod där du kan ange parametrar för anropet.

## <a name="publish-a-node"></a>Publicera en nod

När du navigerar i en *simulerad OPC UA-server* kan du välja att publicera nya noder. Du kan analysera telemetri från dessa noder i lösningen. Dessa *simulerade OPC UA-servrar* gör det enkelt att experimentera med Solution Accelerator utan att distribuera riktiga enheter:

1. Bläddra till en nod i OPC UA-serverläsarträdet som du vill publicera.

1. Högerklicka på noden. Klicka på **publicera**:

    [![Publicera nod för lösnings Accelerator för ansluten fabrik](./media/iot-accelerators-connected-factory-dashboard/publishnode-inline.png)](./media/iot-accelerators-connected-factory-dashboard/publishnode-expanded.png#lightbox)

1. En kontextpanel visas som anger att publiceringen är klar. Noden visas i vyn på Stations nivå med en bock bredvid den:

    [![Lyckad publicering av lösnings Accelerator för ansluten fabrik](./media/iot-accelerators-connected-factory-dashboard/publishsuccess-inline.png)](./media/iot-accelerators-connected-factory-dashboard/publishsuccess-expanded.png#lightbox)

## <a name="command-and-control"></a>Kommando och kontroll

Med Ansluten fabrik kan du styra dina industriella enheter direkt från molnet. Du kan använda funktionen till att vidta åtgärder vid larm som genereras av enheten. Du kan till exempel skicka ett kommando till enheten för att öppna en tryck lanserings ventil. Tillgängliga kommandon finns i noden **StationCommands** i OPC UA-serverläsarträdet. I det här scenariot öppnar du en övertrycksventil på en monteringsstation i en produktionslinje i München. Om du vill använda kommando-och kontroll funktionen måste du ha rollen **administratör** för distributionen av Solution Accelerator:

1. Bläddra till noden **StationCommands** i trädet OPC UA Server Browser för München, produktions rad 0, sammansättnings Station.

1. Välj det kommando som du vill använda. Högerklicka på noden **OpenPressureReleaseValve** . Klicka på **anropa**:

    [![Anropskommando för lösningsacceleratorn Ansluten fabrik](./media/iot-accelerators-connected-factory-dashboard/callcommand-inline.png)](./media/iot-accelerators-connected-factory-dashboard/callcommand-expanded.png#lightbox)

1. En kontext panel visas som talar om vilken metod du är på väg att anropa och eventuell parameter information. Klicka på **anropa**:

    [![Anrops parametrar för Connected Factory Solution Accelerator](./media/iot-accelerators-connected-factory-dashboard/callpanel-inline.png)](./media/iot-accelerators-connected-factory-dashboard/callpanel-expanded.png#lightbox)

1. Kontextpanelen uppdateras för att informera dig att metodanropet har slutförts. Du kan verifiera att anropet har slutförts genom att läsa av värdet för trycknoden som uppdaterades av anropet.

    [![Lyckat anrop för lösningsacceleratorn Ansluten fabrik](./media/iot-accelerators-connected-factory-dashboard/callsuccess-inline.png)](./media/iot-accelerators-connected-factory-dashboard/callsuccess-expanded.png#lightbox)

## <a name="behind-the-scenes"></a>I bakgrunden

När du distribuerar en lösningsaccelerator skapar distributionsprocessen flera resurser i den valda Azure-prenumerationen. Du kan visa dessa resurser på Azure-[portalen](https://portal.azure.com). Under distributionsprocessen skapas en **resursgrupp** med ett namn som baseras på det namn som du valde för lösningsacceleratorn :

[![Resurs grupp för lösningen för ansluten fabriks Accelerator](./media/iot-accelerators-connected-factory-dashboard/resourcegroup-inline.png)](./media/iot-accelerators-connected-factory-dashboard/resourcegroup-expanded.png#lightbox)

Du kan visa inställningarna för varje resurs genom att välja resursen i listan över resurser i resursgruppen.

Du kan också Visa käll koden för Solution Accelerator i GitHub-lagringsplatsen [Azure-IoT-Connected-Factory](https://github.com/Azure/azure-iot-connected-factory) .

När du är klar kan du ta bort Solution Accelerator från din Azure-prenumeration på [azureiotsolutions.com](https://www.azureiotsolutions.com/Accelerators#dashboard) -webbplatsen. På den här webbplatsen kan du enkelt ta bort alla resurser som etablerades när du skapade lösningsacceleratorn.

> [!NOTE]
> För att se till att du tar bort allt som är relaterat till Solution Accelerator tar du bort det på [azureiotsolutions.com](https://www.azureiotsolutions.com/Accelerators#dashboard) -webbplatsen. Ta inte bort resursgruppen i portalen.

## <a name="next-steps"></a>Nästa steg

Nu när du har distribuerat en fungerande lösningsaccelerator kan du fortsätta lära dig mer om IoT-lösningsacceleratorer genom att läsa följande artiklar:

* [Konfigurera den anslutna fabriks lösnings acceleratorn](iot-accelerators-connected-factory-configure.md)
* [Behörigheter för azureiotsolutions.com-webbplatsen](iot-accelerators-permissions.md)
