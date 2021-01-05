---
title: Anslutningsprogram för hantering av IT-tjänster (ITSM) i Log Analytics
description: Den här artikeln innehåller en översikt över Anslutningsprogram för hantering av IT-tjänster (ITSM) (ITSMC) och information om hur du använder den för att övervaka och hantera ITSM arbets objekt i Log Analytics och lösa problem snabbt.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 05/24/2018
ms.custom: references_regions
ms.openlocfilehash: 1f7a493c071e86114afd7d4a9e08e204bbab509d
ms.sourcegitcommit: 31d242b611a2887e0af1fc501a7d808c933a6bf6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/29/2020
ms.locfileid: "97809487"
---
# <a name="connect-azure-to-itsm-tools-by-using-it-service-management-connector"></a>Anslut Azure till ITSM-verktyg med hjälp av Anslutningsprogram för hantering av IT-tjänster (ITSM)

:::image type="icon" source="media/itsmc-overview/itsmc-symbol.png":::

Den här artikeln innehåller information om hur du konfigurerar Anslutningsprogram för hantering av IT-tjänster (ITSM) (ITSMC) i Log Analytics för att centralt hantera dina arbets uppgifter.

## <a name="add-it-service-management-connector"></a>Lägg till Anslutningsprogram för hantering av IT-tjänster (ITSM)

Innan du kan skapa en anslutning måste du lägga till ITSMC.

1. I Azure Portal väljer du **skapa en resurs**:

   ![Skärm bild som visar meny alternativet Skapa en resurs.](media/itsmc-overview/azure-add-new-resource.png)

2. Sök efter **anslutningsprogram för hantering av IT-tjänster (ITSM)** på Azure Marketplace. Välj **skapa**:

   ![Skärm bild som visar knappen Skapa i Azure Marketplace.](media/itsmc-overview/add-itsmc-solution.png)

3. I avsnittet **La arbets yta** väljer du den Azure Log Analytics-arbetsyta där du vill installera ITSMC.
   >[!NOTE]
   >
   > * ITSMC kan bara installeras i Log Analytics arbets ytor i följande regioner: östra USA, västra USA 2, södra centrala USA, västra centrala USA, US Gov, Arizona, US Gov, Virginia, centrala Kanada, Västeuropa, södra Storbritannien, Sydostasien, Östra Japan, centrala Indien och Australien, sydöstra.

4. I avsnittet **Log Analytics arbets yta** väljer du den resurs grupp där du vill skapa ITSMC-resursen:

   ![Skärm bild som visar avsnittet Log Analytics arbets yta.](media/itsmc-overview/itsmc-solution-workspace.png)
   >[!NOTE]
   >Som en del av den pågående över gången från Microsoft Operations Management Suite (OMS) till Azure Monitor, kallas OMS-arbetsytor nu *Log Analytics arbets ytor*.

5. Välj **OK**.

När ITSMC-resursen distribueras visas ett meddelande i det övre högra hörnet i fönstret.

## <a name="create-an-itsm-connection"></a>Skapa en ITSM-anslutning

När du har installerat ITSMC kan du skapa en anslutning.

Om du vill skapa en anslutning måste du utföra förberedelse av ITSM-verktyget för att tillåta anslutningen från ITSMC.  

Baserat på den ITSM-produkt som du ansluter till väljer du någon av följande länkar för instruktioner:

- [ServiceNow](./itsmc-connections-servicenow.md)
- [System Center Service Manager](./itsmc-connections-scsm.md)
- [Cherwell](./itsmc-connections-cherwell.md)
- [Styrka](./itsmc-connections-provance.md)

När du har beredd dina ITSM-verktyg utför du följande steg för att skapa en anslutning:

1. I **alla resurser** letar du efter **Servicedesk (*namnet på din arbets yta*)**:

   ![Skärm bild som visar de senaste resurserna i Azure Portal.](media/itsmc-overview/itsm-connections.png)

1. Under **arbets ytans data källor** i det vänstra fönstret väljer du **ITSM-anslutningar**:

   ![Skärm bild som visar meny alternativet ITSM-anslutningar.](media/itsmc-overview/add-new-itsm-connection.png)
1. Välj **Lägg till anslutning**.

1. Ange anslutnings inställningarna enligt beskrivningen enligt ITSM Products/Services:

    - [ServiceNow](./itsmc-connections-servicenow.md)
    - [System Center Service Manager](./itsmc-connections-scsm.md)
    - [Cherwell](./itsmc-connections-cherwell.md)
    - [Styrka](./itsmc-connections-provance.md)

   > [!NOTE]
   >
   > Som standard uppdaterar ITSMC anslutningens konfigurations data en gång var 24: e timme. Om du vill uppdatera din anslutnings data direkt för att avspegla eventuella ändringar eller uppdatering av mallar som du gör, väljer du knappen **Synkronisera** på bladet anslutning:
   >
   > ![Skärm bild som visar knappen Synkronisera på bladet anslutning.](media/itsmc-overview/itsmc-connections-refresh.png)

## <a name="use-itsmc"></a>Använd ITSMC

   Du kan använda ITSMC för att skapa aviseringar från Azure Monitor aviseringar till ITSM-verktyget.

## <a name="create-itsm-work-items-from-azure-alerts"></a>Skapa ITSM arbets objekt från Azure-aviseringar

När du har skapat din ITSM-anslutning kan du skapa arbets objekt i ITSM-verktyget baserat på Azure-aviseringar. Om du vill skapa arbets uppgifter använder du åtgärden ITSM i åtgärds grupper.

Åtgärds grupper ger ett modulärt och återanvändbart sätt att utlösa åtgärder för dina Azure-aviseringar. Du kan använda åtgärds grupper med mått aviseringar, aktivitets logg aviseringar och Azure Log Analytics-aviseringar i Azure Portal.

> [!NOTE]
> När du har skapat ITSM-anslutningen måste du vänta i 30 minuter tills synkroniseringen har slutförts.

### <a name="template-definitions"></a>Mall definitioner

   Det finns arbets objekts typer som kan använda mallar som definieras av ITSM-verktyget.
Med hjälp av mallar kan du definiera fält som fylls i automatiskt enligt fasta värden som definieras som en del av åtgärds gruppen. Du definierar mallar i ITSM-verktyget.
Du kan definiera vilken mall du vill använda som en del av definitionen av åtgärds gruppen.

Använd följande procedur för att skapa åtgärds grupper:

1. I Azure Portal väljer du  **aviseringar**.
2. På menyn längst upp på skärmen väljer du **Hantera åtgärder**:

    ![Skärm bild som visar meny alternativet hantera åtgärder.](media/itsmc-overview/action-groups-selection-big.png)

   Fönstret **skapa åtgärds grupp** visas.

3. Välj den **prenumeration** och **resurs grupp** där du vill skapa en åtgärds grupp. Ange ett **namn** och **visnings namn** för åtgärds gruppen. Välj **Nästa: meddelanden**.

    ![Skärm bild som visar fönstret Skapa åtgärds grupp.](media/itsmc-overview/action-groups-details.png)

4. I meddelande listan väljer du **Nästa: åtgärder**.
5. I listan åtgärder väljer du **ITSM** i listan **Åtgärds typ** . Ange ett **namn** för åtgärden. Välj Penn knappen som representerar **redigerings information**.

    ![Skärm bild som visar definition för åtgärds grupp.](media/itsmc-definition/action-group-pen.png)

6. I listan **prenumeration** väljer du den prenumeration där Log Analytics arbets ytan finns. I listan **anslutning** väljer du namnet på din ITSM-koppling. Det kommer att följas av namnet på din arbets yta. Till exempel MyITSMConnector (min arbets yta).

7. Välj en typ av **arbets objekt** .

8. Om du vill fylla i fält med fasta värden väljer du **Använd anpassad mall**. Annars väljer du en befintlig [mall](#template-definitions) i listan **mall** och anger de fasta värdena i fälten mall.

9. Om du väljer **skapa enskilda arbets objekt för varje konfigurations objekt** kommer varje konfigurations objekt ha sitt eget arbets objekt. Det innebär att det finns en arbets uppgift per konfigurations objekt.

    * I ett fall som du väljer i list rutan "incident" eller "varning" i arbets objekt: om du avmarkerar kryss rutan **skapa enskilda arbets objekt för varje konfigurations objekt** skapas ett nytt arbets objekt i varje avisering. Det kan finnas mer än en avisering per konfigurations objekt.

       ![Skärm bild som visar fönstret ITSM incident.](media/itsmc-overview/itsm-action-configuration.png)

    * I ett fall som du väljer i list rutan arbets objekt "händelse": om du väljer **skapa enskilda arbets objekt för varje loggpost** i alternativ knappar, kommer varje avisering att skapa ett nytt arbets objekt. Om du väljer **skapa enskilda arbets objekt för varje konfigurations objekt** i alternativet alternativ knappar, kommer varje konfigurations objekt ha sitt eget arbets objekt.
   ![Skärm bild som visar händelse fönstret för ITSM.](media/itsmc-overview/itsm-action-configuration-event.png)

10. Välj **OK**.

När du skapar eller redigerar en regel för Azure-avisering använder du en åtgärds grupp som har en ITSM-åtgärd. När aviseringen utlöses skapas eller uppdateras arbets uppgiften i ITSM-verktyget.

> [!NOTE]
>
>- Information om prissättningen för ITSM-åtgärden finns på sidan med [priser](https://azure.microsoft.com/pricing/details/monitor/) för åtgärds grupper.
>
>
>- Fältet kort beskrivning i varnings regel definitionen är begränsat till 40 tecken när du skickar det med hjälp av åtgärden ITSM.

## <a name="next-steps"></a>Nästa steg

* [Felsöka problem med anslutningsprogram för hantering av IT-tjänster (ITSM)](./itsmc-resync-servicenow.md)
