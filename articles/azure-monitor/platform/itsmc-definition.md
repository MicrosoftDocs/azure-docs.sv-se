---
title: Anslutningsprogram för hantering av IT-tjänster (ITSM) i Log Analytics
description: Den här artikeln innehåller en översikt över Anslutningsprogram för hantering av IT-tjänster (ITSM) (ITSMC) och information om hur du använder den för att övervaka och hantera ITSM arbets objekt i Log Analytics och lösa problem snabbt.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 05/24/2018
ms.custom: references_regions
ms.openlocfilehash: c6bac44e6f7212344463665840f180732970c3d4
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/17/2020
ms.locfileid: "97657166"
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

3. I avsnittet **OMS-arbetsyta** väljer du den Azure Log Analytics-arbetsyta där du vill installera ITSMC.
   >[!NOTE]
   >
   > * Som en del av den pågående över gången från Microsoft Operations Management Suite (OMS) till Azure Monitor, kallas OMS-arbetsytor nu *Log Analytics arbets ytor*.
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

- [System Center Service Manager](./itsmc-connections.md#connect-system-center-service-manager-to-it-service-management-connector-in-azure)
- [ServiceNow](./itsmc-connections.md#connect-servicenow-to-it-service-management-connector-in-azure)
- [Styrka](./itsmc-connections.md#connect-provance-to-it-service-management-connector-in-azure)  
- [Cherwell](./itsmc-connections.md#connect-cherwell-to-it-service-management-connector-in-azure)

När du har beredd dina ITSM-verktyg utför du följande steg för att skapa en anslutning:

1. I **alla resurser** letar du efter **Servicedesk (*namnet på din arbets yta*)**:

   ![Skärm bild som visar de senaste resurserna i Azure Portal.](media/itsmc-overview/itsm-connections.png)

1. Under **arbets ytans data källor** i det vänstra fönstret väljer du **ITSM-anslutningar**:

   ![Skärm bild som visar meny alternativet ITSM-anslutningar.](media/itsmc-overview/add-new-itsm-connection.png)
   Den här sidan visar listan över anslutningar.
1. Välj **Lägg till anslutning**.

4. Ange anslutnings inställningarna enligt beskrivningen i [Konfigurera ITSMC-anslutningen med dina ITSM-produkter/-tjänster](./itsmc-connections.md).

   > [!NOTE]
   >
   > Som standard uppdaterar ITSMC anslutningens konfigurations data en gång var 24: e timme. Om du vill uppdatera din anslutnings data direkt för att avspegla eventuella ändringar eller uppdatering av mallar som du gör, väljer du knappen **Synkronisera** på bladet anslutning:
   >
   > ![Skärm bild som visar knappen Synkronisera på bladet anslutning.](media/itsmc-overview/itsmc-connections-refresh.png)

## <a name="use-itsmc"></a>Använd ITSMC

   Du kan använda ITSMC för att skapa arbets objekt från Azure-aviseringar, Log Analytics aviseringar och Log Analytics logg poster.

## <a name="template-definitions"></a>Mall definitioner

   Det finns arbets objekts typer som kan använda mallar som definieras av ITSM-verktyget.
Med hjälp av mallar kan du definiera fält som fylls i automatiskt enligt fasta värden som definieras som en del av åtgärds gruppen. Du definierar mallar i ITSM-verktyget.
Du kan definiera i vilken mall du vill använda som en del av definitionen av åtgärds gruppen.

## <a name="create-itsm-work-items-from-azure-alerts"></a>Skapa ITSM arbets objekt från Azure-aviseringar

När du har skapat din ITSM-anslutning kan du skapa arbets objekt i ITSM-verktyget baserat på Azure-aviseringar. Om du vill skapa arbets uppgifter använder du åtgärden ITSM i åtgärds grupper.

Åtgärds grupper ger ett modulärt och återanvändbart sätt att utlösa åtgärder för dina Azure-aviseringar. Du kan använda åtgärds grupper med mått aviseringar, aktivitets logg aviseringar och Azure Log Analytics-aviseringar i Azure Portal.

> [!NOTE]
> När du har skapat ITSM-anslutningen måste du vänta i 30 minuter tills synkroniseringen har slutförts.

Använd följande procedur för att skapa arbets objekt:

1. I Azure Portal väljer du  **aviseringar**.
2. På menyn längst upp på skärmen väljer du **Hantera åtgärder**:

    ![Skärm bild som visar meny alternativet hantera åtgärder.](media/itsmc-overview/action-groups-selection-big.png)

   Fönstret **skapa åtgärds grupp** visas.

3. Välj den **prenumeration** och **resurs grupp** där du vill skapa en åtgärds grupp. Ange ett **namn** och **visnings namn** för åtgärds gruppen. Välj **Nästa: meddelanden**.

    ![Skärm bild som visar fönstret Skapa åtgärds grupp.](media/itsmc-overview/action-groups-details.png)

4. I meddelande listan väljer du **Nästa: åtgärder**.
5. I listan åtgärder väljer du **ITSM** i listan **Åtgärds typ** . Ange ett **namn** för åtgärden. Välj Penn knappen som representerar **redigerings information**.
6. I listan **prenumeration** väljer du den prenumeration där Log Analytics arbets ytan finns. I listan **anslutning** väljer du namnet på din ITSM-koppling. Det kommer att följas av namnet på din arbets yta. Till exempel MyITSMConnector (min arbets yta).

7. Välj en typ av **arbets objekt** .

8. Om du vill fylla i fält med fasta värden väljer du **Använd anpassad mall**. Annars väljer du en befintlig [mall](#template-definitions) i listan **mall** och anger de fasta värdena i fälten mall.

9. Om du väljer **skapa enskilda arbets objekt för varje konfigurations objekt** kommer varje konfigurations objekt ha sitt eget arbets objekt. Det kommer att finnas ett arbets objekt per konfigurations objekt. Det kommer att uppdateras enligt de aviseringar som kommer att skapas.

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

## <a name="additional-information"></a>Ytterligare information

### <a name="data-synced-from-your-itsm-product"></a>Data har synkroniserats från din ITSM-produkt

Incidenter och ändrings begär Anden synkroniseras från din ITSM-produkt till din Log Analytics-arbetsyta, baserat på anslutningens konfiguration.

I det här avsnittet visas några exempel på data som samlas in av ITSMC.

Fälten i **ServiceDesk_CL** varierar beroende på vilken typ av arbets objekt som du importerar till Log Analytics. Här är en lista över fält för två arbets objekts typer:

**Arbets objekt:** **incidenter**  
ServiceDeskWorkItemType_s = "incident"

**Fält**

- ServiceDeskConnectionName
- Service Desk-ID
- Tillstånd
- Angelägenhetsgrad
- Påverkan
- Prioritet
- Eskalering
- Skapad av
- Löst av
- Stängd av
- Källa
- Tilldelad till
- Kategori
- Rubrik
- Beskrivning
- Skapad datum
- Stängningsdatum
- Lösningsdatum
- Senast ändrad datum
- Dator

**Arbets objekt:** **ändrings begär Anden**

ServiceDeskWorkItemType_s = "ändringsbegäran"

**Fält**
- ServiceDeskConnectionName
- Service Desk-ID
- Skapad av
- Stängd av
- Källa
- Tilldelad till
- Rubrik
- Typ
- Kategori
- Tillstånd
- Eskalering
- Konflikt status
- Angelägenhetsgrad
- Prioritet
- Risk
- Påverkan
- Tilldelad till
- Skapad datum
- Stängningsdatum
- Senast ändrad datum
- Begärt datum
- Planerat start datum
- Planerat slutdatum
- Start datum för arbete
- Slutdatum för arbete
- Beskrivning
- Dator

## <a name="output-data-for-a-servicenow-incident"></a>Utdata för en ServiceNow-incident

| Log Analytics fält | ServiceNow-fält |
|:--- |:--- |
| ServiceDeskId_s| Antal |
| IncidentState_s | Tillstånd |
| Urgency_s |Angelägenhetsgrad |
| Impact_s |Påverkan|
| Priority_s | Prioritet |
| CreatedBy_s | Öppnad av |
| ResolvedBy_s | Löst av|
| ClosedBy_s  | Stängd av |
| Source_s| Kontakt typ |
| AssignedTo_s | Tilldelad  |
| Category_s | Kategori |
| Title_s|  Kort beskrivning |
| Description_s|  Obs! |
| CreatedDate_t|  Inleddes |
| ClosedDate_t| stängd|
| ResolvedDate_t|Matchat|
| Dator  | Konfigurationsobjekt |

## <a name="output-data-for-a-servicenow-change-request"></a>Utdata för en ServiceNow-ändringsbegäran

| Log Analytics | ServiceNow-fält |
|:--- |:--- |
| ServiceDeskId_s| Antal |
| CreatedBy_s | Begärd av |
| ClosedBy_s | Stängd av |
| AssignedTo_s | Tilldelad  |
| Title_s|  Kort beskrivning |
| Type_s|  Typ |
| Category_s|  Kategori |
| CRState_s|  Tillstånd|
| Urgency_s|  Angelägenhetsgrad |
| Priority_s| Prioritet|
| Risk_s| Risk|
| Impact_s| Påverkan|
| RequestedDate_t  | Begärd efter datum |
| ClosedDate_t | Stängnings datum |
| PlannedStartDate_t  | Planerat start datum |
| PlannedEndDate_t  | Planerat slutdatum |
| WorkStartDate_t  | Verkligt start datum |
| WorkEndDate_t | Verkligt slutdatum|
| Description_s | Beskrivning |
| Dator  | Konfigurations objekt |

## <a name="next-steps"></a>Nästa steg

[Översikt över ITSM-anslutningsprogram](./itsmc-overview.md) 
 [Lägg till ITSM produkter/tjänster i anslutningsprogram för hantering av IT-tjänster (ITSM)](./itsmc-connections.md) 
 [Felsöka problem i ITSM-anslutningsprogram](./itsmc-resync-servicenow.md)
