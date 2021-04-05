---
title: Konfigurera autoskalning för en Azure API Management-instans | Microsoft Docs
description: I det här avsnittet beskrivs hur du ställer in skalnings beteende för en Azure API Management-instans.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: anneta
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 06/20/2018
ms.author: apimpm
ms.openlocfilehash: 41a97c639d9148f2bff3e4f530363ea05a65a7b8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96018263"
---
# <a name="automatically-scale-an-azure-api-management-instance"></a>Skala en Azure API Management-instans automatiskt  

Azure API Management Service-instansen kan skalas automatiskt baserat på en uppsättning regler. Det här beteendet kan aktive ras och konfigureras via Azure Monitor och stöds endast på **standard** -och **Premium** nivåer i Azure API Management-tjänsten.

Artikeln vägleder dig genom processen med att konfigurera autoskalning och föreslår en optimal konfiguration av regler för automatisk skalning.

> [!NOTE]
> API Management tjänsten i **förbruknings** nivån skalas automatiskt utifrån trafiken – utan någon ytterligare konfiguration som behövs.

## <a name="prerequisites"></a>Förutsättningar

För att följa stegen i den här artikeln måste du:

+ En aktiv Azure-prenumeration.
+ Ha en Azure API Management-instans. Mer information finns i [skapa en Azure API Management-instans](get-started-create-service-instance.md).
+ Förstå konceptet med [kapacitet för en Azure API Management-instans](api-management-capacity.md).
+ Förstå [manuell skalnings process för en Azure API Management-instans](upgrade-and-scale.md), inklusive kostnads konsekvenser.

[!INCLUDE [premium-standard.md](../../includes/api-management-availability-premium-standard.md)]

## <a name="azure-api-management-autoscale-limitations"></a>Begränsningar för autoskalning i Azure API Management

Vissa begränsningar och konsekvenser för skalnings beslut måste beaktas innan du konfigurerar funktionen för automatisk skalning.

+ Autoskalning kan bara aktive ras för **standard** -och **Premium** nivåer i Azure API Management-tjänsten.
+ Pris nivåer anger också det maximala antalet enheter för en tjänst instans.
+ Processen för skalning tar minst 20 minuter.
+ Om tjänsten har låsts av en annan åtgärd kommer skalnings förfrågan att Miss Miss läge och försöka igen automatiskt.
+ I händelse av en tjänst med flera regionala distributioner kan endast enheter på den **primära platsen** skalas. Enheter på andra platser kan inte skalas.

## <a name="enable-and-configure-autoscale-for-azure-api-management-service"></a>Aktivera och konfigurera autoskalning för Azure API Management-tjänsten

Följ stegen nedan för att konfigurera autoskalning för en Azure API Management-tjänst:

1. Gå till **övervaknings** instansen i Azure Portal.

    ![Azure Monitor](media/api-management-howto-autoscale/01.png)

2. Välj **autoskalning** från menyn till vänster.

    ![Azure Monitor AutoScale-resurs](media/api-management-howto-autoscale/02.png)

3. Leta upp din Azure API Management-tjänst baserat på filtren i list Rute menyerna.
4. Välj önskad Azure API Management-tjänstinstans.
5. Klicka på knappen **Aktivera autoskalning** i det nyligen öppnade avsnittet.

    ![Aktivera Azure Monitor autoskalning](media/api-management-howto-autoscale/03.png)

6. Klicka på **+ Lägg till en regel** i avsnittet **regler** .

    ![Azure Monitor tilläggs regel för autoskalning](media/api-management-howto-autoscale/04.png)

7. Definiera en ny utskalning regel.

   En skalbar regel kan till exempel utlösa ett tillägg av en Azure API Management-enhet, när genomsnitts kapacitets måttet under de senaste 30 minuterna överstiger 80%. Tabellen nedan innehåller konfiguration för en sådan regel.

    | Parameter             | Värde             | Obs!                                                                                                                                                                                                                                                                           |
    |-----------------------|-------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Måttkälla         | Aktuell resurs  | Definiera regeln baserat på aktuella Azure API Management resurs mått.                                                                                                                                                                                                     |
    | *Kriterie*            |                   |                                                                                                                                                                                                                                                                                 |
    | Tidsmängd      | Medelvärde           |                                                                                                                                                                                                                                                                                 |
    | Måttnamn           | Kapacitet          | Kapacitets mått är ett Azure API Management mått som återspeglar användningen av resurser i en Azure API Management-instans.                                                                                                                                                            |
    | Tidsintervallstatistik  | Medelvärde           |                                                                                                                                                                                                                                                                                 |
    | Operator              | Större än      |                                                                                                                                                                                                                                                                                 |
    | Tröskelvärde             | 80 %               | Tröskelvärdet för det genomsnittliga kapacitets måttet.                                                                                                                                                                                                                                 |
    | Varaktighet (i minuter) | 30                | TimeSpan till genomsnitt för kapacitets måttet över är bara för användnings mönster. Den längre tids perioden är, den jämnaare reaktionen blir-periodiska toppar får mindre inverkan på det skalbara beslutet. Det kommer dock även att fördröja utlösaren. |
    | *Åtgärd*              |                   |                                                                                                                                                                                                                                                                                 |
    | Åtgärd             | Öka antalet med |                                                                                                                                                                                                                                                                                 |
    | Antal instanser        | 1                 | Skala ut Azure API Management-instansen med 1 enhet.                                                                                                                                                                                                                          |
    | Väntetid (minuter)   | 60                | Det tar minst 20 minuter för Azure API Management-tjänsten att skala ut. I de flesta fall hindras den nedkylda perioden på 60 minuter från att utlösa många skalnings gränser.                                                                                                  |

8. Klicka på **Lägg till** för att spara regeln.

    ![Regel för Azure Monitor skala ut](media/api-management-howto-autoscale/05.png)

9. Klicka på igen på **+ Lägg till en regel**.

    Den här gången måste en skala i regeln definieras. Det ser till att resurserna inte slösas om, när användningen av API: er minskar.

10. Definiera en ny skala i regeln.

    En skalning i regeln kan till exempel utlösa en borttagning av en Azure API Management-enhet när genomsnitts kapacitets måttet under de senaste 30 minuterna har varit lägre än 35%. Tabellen nedan innehåller konfiguration för en sådan regel.

    | Parameter             | Värde             | Obs!                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
    |-----------------------|-------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Måttkälla         | Aktuell resurs  | Definiera regeln baserat på aktuella Azure API Management resurs mått.                                                                                                                                                                                                                                                                                                                                                                                                                         |
    | *Kriterie*            |                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
    | Tidsmängd      | Medelvärde           |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
    | Måttnamn           | Kapacitet          | Samma mått som det som används för skala ut-regeln.                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
    | Tidsintervallstatistik  | Medelvärde           |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
    | Operator              | Mindre än         |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
    | Tröskelvärde             | 35 %               | På samma sätt som för skala ut-regeln beror detta värde kraftigt på användnings mönstren för Azure-API Management. |
    | Varaktighet (i minuter) | 30                | Samma värde som det som används för skala ut-regeln.                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
    | *Åtgärd*              |                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
    | Åtgärd             | Minska antalet med | Motsatt till vad som användes för skala ut-regeln.                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
    | Antal instanser        | 1                 | Samma värde som det som används för skala ut-regeln.                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
    | Väntetid (minuter)   | 90                | Skala i måste vara mer restriktiv än att skala ut, så den nedrullningsbara perioden ska vara längre.                                                                                                                                                                                                                                                                                                                                                                                                    |

11. Klicka på **Lägg till** för att spara regeln.

    ![Azure Monitor skala i regel](media/api-management-howto-autoscale/06.png)

12. Ange det **maximala** antalet Azure API Management-enheter.

    > [!NOTE]
    > Azure API Management har en gräns för enheter som en instans kan skala ut till. Gränsen är beroende av en tjänst nivå.

    ![Skärm bild som visar var du anger det maximala antalet Azure API Management-enheter.](media/api-management-howto-autoscale/07.png)

13. Klicka på **Spara**. Autoskalning har kon figurer ATS.

## <a name="next-steps"></a>Nästa steg

- [Så distribuerar du en Azure API Management-tjänstinstans till flera Azure-regioner](api-management-howto-deploy-multi-region.md)
- [Optimera och Spara på dina moln utgifter](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)