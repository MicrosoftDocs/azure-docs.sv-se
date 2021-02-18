---
title: Anslutningsprogram för hantering av IT-tjänster (ITSM) i Log Analytics
description: Den här artikeln innehåller en översikt över Anslutningsprogram för hantering av IT-tjänster (ITSM) (ITSMC) och information om hur du använder den för att övervaka och hantera ITSM arbets objekt i Log Analytics och lösa problem snabbt.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 05/24/2018
ms.custom: references_regions
ms.openlocfilehash: ba32cfa4bc5cd0b41a210cf88fb598afc3064495
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100623584"
---
# <a name="connect-azure-to-itsm-tools-by-using-it-service-management-connector"></a>Anslut Azure till ITSM-verktyg med hjälp av Anslutningsprogram för hantering av IT-tjänster (ITSM)

:::image type="icon" source="media/itsmc-overview/itsmc-symbol.png":::

Den här artikeln innehåller information om hur du konfigurerar Anslutningsprogram för hantering av IT-tjänster (ITSM) (ITSMC) i Log Analytics för att centralt hantera dina ITSM-arbetsobjekt (IT Service Management).

## <a name="add-it-service-management-connector"></a>Lägg till Anslutningsprogram för hantering av IT-tjänster (ITSM)

Innan du kan skapa en anslutning måste du installera ITSMC.

1. I Azure Portal väljer du **skapa en resurs**:

   ![Skärm bild som visar meny alternativet för att skapa en resurs.](media/itsmc-overview/azure-add-new-resource.png)

2. Sök efter **anslutningsprogram för hantering av IT-tjänster (ITSM)** på Azure Marketplace. Välj sedan **skapa**:

   ![Skärm bild som visar knappen Skapa i Azure Marketplace.](media/itsmc-overview/add-itsmc-solution.png)

3. I avsnittet Välj **arbets yta** väljer du Log Analytics arbets ytan där du vill installera ITSMC.
   > [!NOTE]
   > Du kan bara installera ITSMC i Log Analytics arbets ytor i följande regioner: östra USA, västra USA 2, södra centrala USA, västra centrala USA, US Gov, Arizona, US Gov, Virginia, centrala Kanada, Västeuropa, södra Storbritannien, Sydostasien, Östra Japan, centrala Indien och Australien, sydöstra.

4. I avsnittet **Log Analytics arbets yta** väljer du den resurs grupp där du vill skapa ITSMC-resursen:

   ![Skärm bild som visar avsnittet Log Analytics arbets yta.](media/itsmc-overview/itsmc-solution-workspace.png)
   
   > [!NOTE]
   > Som en del av den pågående över gången från Microsoft Operations Management Suite (OMS) till Azure Monitor kallas OMS-arbetsytor *Log Analytics arbets ytor*.

5. Välj **OK**.

När ITSMC-resursen distribueras visas ett meddelande i det övre högra hörnet i fönstret.

## <a name="create-an-itsm-connection"></a>Skapa en ITSM-anslutning

När du har installerat ITSMC måste du prepa ditt ITSM-verktyg för att tillåta anslutningen från ITSMC. Baserat på den ITSM-produkt som du ansluter till väljer du någon av följande länkar för instruktioner:

- [ServiceNow](./itsmc-connections-servicenow.md)
- [System Center Service Manager](./itsmc-connections-scsm.md)
- [Cherwell](./itsmc-connections-cherwell.md)
- [Provance](./itsmc-connections-provance.md)

När du har beredd ditt ITSM-verktyg utför du följande steg för att skapa en anslutning:

1. I **alla resurser** letar du efter **Servicedesk (*namnet på din arbets yta*)**:

   ![Skärm bild som visar de senaste resurserna i Azure Portal.](media/itsmc-definition/create-new-connection-from-resource.png)

1. Under **arbets ytans data källor** i det vänstra fönstret väljer du **ITSM-anslutningar**:

   ![Skärm bild som visar meny alternativet ITSM-anslutningar.](media/itsmc-overview/add-new-itsm-connection.png)

1. Välj **Lägg till anslutning**.

1. Ange anslutnings inställningarna enligt den ITSM-produkt som du använder:

    - [ServiceNow](./itsmc-connections-servicenow.md)
    - [System Center Service Manager](./itsmc-connections-scsm.md)
    - [Cherwell](./itsmc-connections-cherwell.md)
    - [Provance](./itsmc-connections-provance.md)

   > [!NOTE]
   > Som standard uppdaterar ITSMC anslutningens konfigurations data en gång var 24: e timme. Om du vill uppdatera din anslutnings data direkt för att avspegla eventuella ändringar eller uppdatering av mallar som du gör, väljer du knappen **Synkronisera** i anslutnings fönstret:
   >
   > ![Skärm bild som visar knappen Synkronisera i anslutningens fönster.](media/itsmc-overview/itsmc-connections-refresh.png)

## <a name="create-itsm-work-items-from-azure-alerts"></a>Skapa ITSM arbets objekt från Azure-aviseringar

När du har skapat din ITSM-anslutning kan du använda ITMC för att skapa arbets objekt i ditt ITSM-verktyg baserat på Azure-aviseringar. Om du vill skapa arbets uppgifter använder du åtgärden ITSM i åtgärds grupper.

Åtgärds grupper ger ett modulärt och återanvändbart sätt att utlösa åtgärder för dina Azure-aviseringar. Du kan använda åtgärds grupper med mått aviseringar, aktivitets logg aviseringar och Log Analytics aviseringar i Azure Portal.

> [!NOTE]
> När du har skapat ITSM-anslutningen måste du vänta 30 minuter på att synkroniseringen ska slutföras.

## <a name="define-a-template"></a>Definiera en mall

Vissa typer av arbets objekt kan använda mallar som du definierar i ITSM-verktyget. Med hjälp av mallar kan du definiera fält som fylls i automatiskt enligt fasta värden för en åtgärds grupp. Du kan definiera vilken mall du vill använda som en del av definitionen av en åtgärds grupp.

Så här skapar du en åtgärds grupp:

1. I Azure Portal väljer du  **aviseringar**.
2. På menyn längst upp på skärmen väljer du **Hantera åtgärder**:

    ![Skärm bild som visar meny alternativet hantera åtgärder.](media/itsmc-overview/action-groups-selection-big.png)

   Fönstret **skapa åtgärds grupp** visas.

3. Välj den **prenumeration** och **resurs grupp** där du vill skapa en åtgärds grupp. Ange värden i **Åtgärds gruppens namn** och **visnings namnet** för din åtgärds grupp. Välj sedan **Nästa: meddelanden**.

    ![Skärm bild som visar fönstret Skapa åtgärds grupp.](media/itsmc-overview/action-groups-details.png)

4. På fliken **meddelanden** väljer du **Nästa: åtgärder**.
5. På fliken **åtgärder** väljer du **ITSM** i listan **Åtgärds typ** . Som **namn** anger du ett namn för åtgärden. Välj sedan Penn knappen som representerar **redigerings information**.

    ![Skärm bild som visar val för att skapa en åtgärds grupp.](media/itsmc-definition/action-group-pen.png)

6. I listan **prenumeration** väljer du den prenumeration som innehåller Log Analytics arbets ytan. I listan **anslutning** väljer du namnet på din ITSM-koppling. Det kommer att följas av namnet på din arbets yta. Ett exempel är *MyITSMConnector (min arbets yta)*.

7. Välj en typ av **arbets objekt** .

8. Om du vill fylla i fält med fasta värden väljer du **Använd anpassad mall**. Annars väljer du en befintlig [mall](#define-a-template) i listan **mall** och anger de fasta värdena i fälten mall.

9. I det sista avsnittet av gränssnittet för att skapa en ITSM-åtgärds grupp kan du definiera hur många arbets objekt som ska skapas för varje avisering.

   > [!NOTE]
   > Det här avsnittet är endast relevant för loggs öknings aviseringar. För alla andra aviserings typer skapar du ett arbets objekt per avisering.

   * Om du valde **incident** eller **avisering** i list rutan **arbets objekt** har du möjlighet att skapa enskilda arbets objekt för varje konfigurations objekt.
    
     ![Skärm bild som visar biljett ytan i T S M med incidenten valt som arbets objekt.](media/itsmc-overview/itsm-action-configuration.png)
    
     * Om du markerar kryss rutan **skapa enskilda arbets objekt för varje konfigurations objekt** skapas ett nytt arbets objekt i varje konfigurations objekt i varje avisering. Eftersom flera aviseringar ska utföras för samma berörda konfigurations objekt finns det fler än ett arbets objekt för varje konfigurations objekt.

       En avisering som har tre konfigurations objekt kommer till exempel att skapa tre arbets objekt. En avisering som har ett konfigurations objekt kommer att skapa ett arbets objekt.
        
     * Om du avmarkerar kryss rutan **skapa enskilda arbets objekt för varje konfigurations objekt** skapas ett enskilt arbets objekt för varje aviserings regel i ITSMC och läggs till i alla konfigurations objekt som påverkas. Ett nytt arbets objekt skapas om föregående åtgärd stängs.

       >[!NOTE]
       > I det här fallet genererar vissa av de aktiverade varningarna inga nya arbets objekt i ITSM-verktyget.

       En avisering som har tre konfigurations objekt kommer till exempel att skapa ett arbets objekt. Om en avisering för samma aviserings regel som föregående exempel har ett konfigurations objekt, kopplas det konfigurationsobjektet till listan över påverkade konfigurations objekt i den skapade arbets uppgiften. En avisering för en annan varnings regel som har ett konfigurations objekt som skapar ett arbets objekt.

   * Om du valde **händelse** i list rutan **arbets objekt** kan du välja att skapa enskilda arbets objekt för varje loggpost eller för varje konfigurations objekt.
    
     ![Skärm bild som visar biljett ytan i T S M med händelsen valt som arbets objekt.](media/itsmc-overview/itsm-action-configuration-event.png)

     * Om du väljer **skapa enskilda arbets objekt för varje loggpost (fältet konfigurations objekt är inte ifyllt). Kan resultera i ett stort antal arbets objekt.)** skapas ett arbets objekt för varje rad i Sök resultatet av varnings frågan för loggs ökning. Egenskapen Description i arbets objektets nytto Last kommer att innehålla raden från Sök resultaten.
      
     * Om du väljer **skapa enskilda arbets objekt för varje konfigurations objekt** skapas ett nytt arbets objekt i varje konfigurations objekt i varje avisering. Varje konfigurations objekt kan ha mer än ett arbets objekt i ITSM-systemet. Det här alternativet är detsamma som att markera kryss rutan som visas när du har valt **incident** som arbets objekts typ.

10. Välj **OK**.

När du skapar eller redigerar en regel för Azure-avisering använder du en åtgärds grupp som har en ITSM-åtgärd. När aviseringen utlöses skapas eller uppdateras arbets uppgiften i ITSM-verktyget.

> [!NOTE]
> Information om prissättningen för ITSM-åtgärden finns på sidan med [priser](https://azure.microsoft.com/pricing/details/monitor/) för åtgärds grupper.
>
> Fältet kort beskrivning i varnings regel definitionen är begränsat till 40 tecken när du skickar det med hjälp av åtgärden ITSM.

## <a name="next-steps"></a>Nästa steg

* [Felsöka problem i ITSMC](./itsmc-resync-servicenow.md)
