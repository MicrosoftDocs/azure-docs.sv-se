---
title: Hämta drönarbilder
description: Den här artikeln beskriver hur du får drönare-bilder från partner.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 04f0c3c63d00ea49bf43f00f256266599a73d6c0
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88508812"
---
# <a name="get-drone-imagery-from-drone-partners"></a>Hämta drönare-bilder från drönare-partners

Den här artikeln beskriver hur du kan hämta orthomosaic-data från dina drönare bilder-partner till Azure FarmBeats Datahub. En orthomosaic är en flyg bild eller en bild som är geometriskt löst och Häftad från data som samlas in av en drönare.

För närvarande stöds följande bilder-partner.

  ![FarmBeats drönare bilder partners](./media/get-drone-imagery-from-drone-partner/drone-partner-1.png)

Genom att integrera drönare bilder-data med Azure FarmBeats kan du få orthomosaic-data från drönare-flygningar som du utför i Server gruppen i Datahub. När data är tillgängliga kan du visa dem i FarmBeats-acceleratorn. Data kan användas för data fusion och artificiell intelligens och utveckling av maskin inlärnings modeller.

## <a name="before-you-begin"></a>Innan du börjar

  - Kontrol lera att du har installerat Azure-FarmBeats. Information om hur du installerar FarmBeats finns i [Installera Azure FarmBeats](install-azure-farmbeats.md).
  - Kontrol lera att du har den server grupp som du vill att drönare bilder ska definieras i FarmBeats-systemet.

## <a name="enable-drone-imagery-integration-with-farmbeats"></a>Aktivera drönare bilder-integrering med FarmBeats

Ange följande information för din enhets leverantör för att aktivera integrering med FarmBeats:
 - API-slutpunkt
 - Klientorganisations-ID
 - Klient-ID
 - Klienthemlighet

Gör så här:

1. Hämta det här [skriptet](https://aka.ms/farmbeatspartnerscript)och extrahera det till den lokala enheten. Två filer finns inuti zip-filen.
2. Logga in i [Azure-portalen](https://portal.azure.com/) och öppna Azure Cloud Shell. Det här alternativet är tillgängligt i verktygsfältet i det övre högra hörnet i portalen.

    ![Öppna Azure Cloud Shell längst upp till höger i portalen](./media/get-drone-imagery-from-drone-partner/navigation-bar-1.png)

3. Kontrol lera att miljön är inställd på **PowerShell**.

    ![PowerShell-inställning](./media/get-drone-imagery-from-drone-partner/power-shell-new-1.png)

4. Ladda upp de två filerna som du laddade ned från steg 1 i Cloud Shell-instansen.

    ![Överföra filer](./media/get-drone-imagery-from-drone-partner/power-shell-two-1.png)

5. Gå till den katalog där filerna överfördes. Som standard överförs de till hem katalogen under användar namnet.
6. Kör följande skript:

    ```azurepowershell-interactive

    ./generateCredentials.ps1

    ```

7. Följ anvisningarna på skärmen för att avbilda värdena för API-slutpunkt, klient-ID, klient-ID, klient hemlighet och EventHub-anslutningssträng.

    När du har angett de autentiseringsuppgifter som krävs i partnerns drönare-programsystem kan du importera alla Server grupper från FarmBeats-systemet. Sedan kan du använda Server grupps informationen för att planera och drönare bild samling.

    När RAW-avbildningarna har bearbetats av drönare-leverantörens program vara laddar drönare-programvaran upp de häftade orthomosaic och andra bearbetade avbildningar till Datahub.

## <a name="view-drone-imagery"></a>Visa drönare bilder

När data har skickats till FarmBeats-Datahub kan du fråga scen lagret med hjälp av FarmBeats Datahub-API: er.

Du kan också visa den senaste drönare-avbildningen på sidan **Server grupps information** . Följ stegen om du vill visa avbildningen.

1. Välj den server grupp som din bilder laddats upp till. Sidan **Server grupp** information visas.
2. Rulla ned till det senaste **precision Maps** -avsnittet.
3. Visa bilden i avsnittet **drönare bilder** .

    ![Drönare bilder-avsnitt](./media/get-drone-imagery-from-drone-partner/drone-imagery-1.png)

## <a name="download-drone-imagery"></a>Ladda ned drönare bilder

När du väljer avsnittet drönare bilder öppnas ett popup-fönster för att visa en hög upplöst bild av drönare-orthomosaic.

![Hög upplöst orthomosaic](./media/get-drone-imagery-from-drone-partner/download-drone-imagery-1.png)

## <a name="view-all-drone-maps"></a>Visa alla drönare Maps

Filer och avbildningar som laddats upp av drönare-providern visas i avsnittet **Maps** . Välj avsnittet **Maps** , filtrera efter **Server grupp**och Välj lämpliga filer att visa och ladda ned.

  ![Avsnittet Maps](./media/get-drone-imagery-from-drone-partner/view-drone-maps-1.png)

## <a name="next-steps"></a>Nästa steg

Lär dig hur du använder FarmBeats Datahub- [API: er](rest-api-in-azure-farmbeats.md) för att hämta din drönare-bilder.
