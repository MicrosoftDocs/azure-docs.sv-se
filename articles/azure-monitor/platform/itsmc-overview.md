---
title: Anslutningsprogram för hantering av IT-tjänster (ITSM) i Log Analytics
description: Den här artikeln innehåller en översikt över Anslutningsprogram för hantering av IT-tjänster (ITSM) (ITSMC) och information om hur du använder den för att övervaka och hantera ITSM arbets objekt i Log Analytics och lösa problem snabbt.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 05/24/2018
ms.custom: references_regions
ms.openlocfilehash: 917eb52a7255527b55aacd488f33125ad853e894
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96932276"
---
# <a name="connect-azure-to-itsm-tools-by-using-it-service-management-connector"></a>Anslut Azure till ITSM-verktyg med hjälp av Anslutningsprogram för hantering av IT-tjänster (ITSM)

:::image type="icon" source="media/itsmc-overview/itsmc-symbol.png":::

Anslutningsprogram för hantering av IT-tjänster (ITSM) (ITSMC) gör det möjligt att ansluta Azure till en ITSM-produkt eller-tjänst (supported IT Service Management).

Azure-tjänster som Azure Log Analytics och Azure Monitor innehåller verktyg för att identifiera, analysera och felsöka problem med dina Azure-och icke-Azure-resurser. Men arbets objekt som rör ett problem finns vanligt vis i en ITSM-produkt eller-tjänst. ITSMC tillhandahåller en dubbelriktad anslutning mellan Azure-och ITSM-verktyg som hjälper dig att lösa problem snabbare.

ITSMC stöder anslutningar med följande ITSM-verktyg:

-   ServiceNow
-   System Center Service Manager
-   Styrka
-   Cherwell

   >[!NOTE]
> Vi föreslår våra Cherwell och förstyrker kunder att använda [webhook-åtgärder](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups#webhook) för att Cherwell och bevisa slut punkt som en annan lösning på integrationen.

Med ITSMC kan du:

-  Skapa arbets objekt i ITSM-verktyget baserat på dina Azure-aviseringar (mått aviseringar, aktivitets logg aviseringar och Log Analytics aviseringar).
-  Du kan också synkronisera dina incidenter och ändra begär ande data från ITSM-verktyget till en Azure Log Analytics-arbetsyta.

Information om juridiska villkor och sekretess policyn finns i [Microsofts sekretess policy](https://go.microsoft.com/fwLink/?LinkID=522330&clcid=0x9).

Du kan börja använda ITSMC genom att utföra följande steg:

1.  [Lägg till ITSMC.](#add-it-service-management-connector)
2.  [Skapa en ITSM-anslutning.](#create-an-itsm-connection)
3.  [Använd anslutningen.](#use-itsmc)


##  <a name="add-it-service-management-connector"></a>Lägg till Anslutningsprogram för hantering av IT-tjänster (ITSM)

Innan du kan skapa en anslutning måste du lägga till ITSMC.

1. I Azure Portal väljer du **skapa en resurs**:

   ![Skärm bild som visar meny alternativet Skapa en resurs.](media/itsmc-overview/azure-add-new-resource.png)

2. Sök efter **anslutningsprogram för hantering av IT-tjänster (ITSM)** på Azure Marketplace. Välj **skapa**:

   ![Skärm bild som visar knappen Skapa i Azure Marketplace.](media/itsmc-overview/add-itsmc-solution.png)

3. I avsnittet **OMS-arbetsyta** väljer du den Azure Log Analytics-arbetsyta där du vill installera ITSMC.
   >[!NOTE]
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
Med hjälp av mallar kan du definiera fält som fylls i automatiskt enligt fasta värden som definieras som en del av åtgärds gruppen. Du definierar mallar i ITSM-verktyget. Du kan definiera i vilken mall du vill använda som en del av definitionen av åtgärds gruppen.
      
## <a name="create-itsm-work-items-from-azure-alerts"></a>Skapa ITSM arbets objekt från Azure-aviseringar

När du har skapat din ITSM-anslutning kan du skapa arbets objekt i ITSM-verktyget baserat på Azure-aviseringar. Om du vill skapa arbets uppgifter använder du åtgärden ITSM i åtgärds grupper.

Åtgärds grupper ger ett modulärt och återanvändbart sätt att utlösa åtgärder för dina Azure-aviseringar. Du kan använda åtgärds grupper med mått aviseringar, aktivitets logg aviseringar och Azure Log Analytics-aviseringar i Azure Portal.

> [!NOTE]
> När du har skapat ITSM-anslutningen måste du vänta i 30 minuter tills synkroniseringen har slutförts.
> 

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

   ![Skärm bild som visar ITSM Ticket-fönstret.](media/itsmc-overview/itsm-action-configuration.png)
   
   * I ett fall som du väljer i list rutan arbets objekt "händelse": om du väljer **skapa enskilda arbets objekt för varje loggpost** i alternativ knappar, kommer varje avisering att skapa ett nytt arbets objekt. Om du väljer **skapa enskilda arbets objekt för varje konfigurations objekt** i alternativet alternativ knappar, kommer varje konfigurations objekt ha sitt eget arbets objekt.
   ![Skärm bild som visar ITSM Ticket-fönstret.](media/itsmc-overview/itsm-action-configuration-event.png)

10. Välj **OK**.

När du skapar eller redigerar en regel för Azure-avisering använder du en åtgärds grupp som har en ITSM-åtgärd. När aviseringen utlöses skapas eller uppdateras arbets uppgiften i ITSM-verktyget.

> [!NOTE]
>
>- Information om prissättningen för ITSM-åtgärden finns på sidan med [priser](https://azure.microsoft.com/pricing/details/monitor/) för åtgärds grupper.
>
>
>- Fältet kort beskrivning i varnings regel definitionen är begränsat till 40 tecken när du skickar det med hjälp av åtgärden ITSM.


## <a name="visualize-and-analyze-the-incident-and-change-request-data"></a>Visualisera och analysera incidenter och data för ändringsbegäran

Beroende på din konfiguration när du konfigurerar en anslutning kan ITSMC synkronisera upp till 120 dagar av incidenten och data för ändringsbegäran. Schemat för logg poster för dessa data finns i [Nästa avsnitt](#additional-information) i den här artikeln.

Du kan visualisera incident-och ändrings begär ande data med hjälp av ITSMC-instrument panelen:

![Skärm bild som visar ITSMC-instrumentpanelen.](media/itsmc-overview/itsmc-overview-sample-log-analytics.png)

Instrument panelen innehåller också information om anslutnings status, som du kan använda som utgångs punkt för att analysera problem med anslutningarna.

Du kan också visualisera de incidenter som synkroniseras mot de berörda datorerna i Tjänstkarta.

Tjänstkarta identifierar automatiskt program komponenterna i Windows-och Linux-system och mappar kommunikationen mellan tjänsterna. Du kan visa dina servrar när du ser dem: som sammankopplade system som levererar kritiska tjänster. Tjänstkarta visar anslutningar mellan servrar, processer och portar i alla TCP-anslutna arkitekturer. Förutom installationen av en agent krävs ingen konfiguration. Mer information finns i [använda tjänstkarta](../insights/service-map.md).

Om du använder Tjänstkarta kan du Visa Service Desk-objekten som skapats i ITSM-lösningar, som du ser här:

![Skärm bild som visar Log Analytics skärmen.](media/itsmc-overview/itsmc-overview-integrated-solutions.png)


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
- Stat
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
- Stat
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
| IncidentState_s | Stat |
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
| Description_s|  Kommentarer |
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
| CRState_s|  Stat|
| Urgency_s|  Angelägenhetsgrad |
| Priority_s| Prioritet|
| Risk_s| Risk|
| Impact_s| Påverkan|
| RequestedDate_t  | Begärd efter datum |
| ClosedDate_t | Stängnings datum |
| PlannedStartDate_t  |     Planerat start datum |
| PlannedEndDate_t  |   Planerat slutdatum |
| WorkStartDate_t  | Verkligt start datum |
| WorkEndDate_t | Verkligt slutdatum|
| Description_s | Beskrivning |
| Dator  | Konfigurations objekt |


## <a name="troubleshoot-itsm-connections"></a>Felsöka ITSM-anslutningar
- Gör så här om en anslutning Miss lyckas från den anslutna källans användar gränssnitt och du får ett **fel när du sparar anslutnings** meddelandet:
   - För ServiceNow-, Cherwell-och upphandlings anslutningar:  
     - Kontrol lera att du har angett användar namn, lösen ord, klient-ID och klient hemlighet korrekt för varje anslutning.  
     - Se till att du har tillräcklig behörighet i motsvarande ITSM-produkt för att upprätta anslutningen.  
   - För Service Manager anslutningar:  
     - Se till att webbappen har distribuerats och att hybrid anslutningen har skapats. Om du vill kontrol lera att anslutningen har upprättats med den lokala Service Manager datorn går du till webbappens URL enligt beskrivningen i dokumentationen för att skapa en [hybrid anslutning](./itsmc-connections.md#configure-the-hybrid-connection).  

- Om data från ServiceNow inte kommer att synkroniseras till Log Analytics, se till att ServiceNow-instansen inte är i vilo läge. ServiceNow dev-instanser går ibland till vilo läge när de är inaktiva under en längre tid. Rapportera problemet om det inte är vad som händer.
- Om Log Analytics-aviseringar för brand men arbets objekt inte skapas i ITSM-produkten, om konfigurations objekt inte skapas/länkas till arbets objekt, eller för annan information, se följande resurser:
   -  ITSMC: lösningen visar en sammanfattning av anslutningar, arbets objekt, datorer med mera. Välj den panel som har etiketten **kopplings status** . Då kommer du att **Logga sökningen** med den relevanta frågan. Titta på logg poster med en `LogType_S` av `ERROR` för mer information.
   - Sidan **loggs ökning** : Visa fel och relaterad information direkt med hjälp av frågan `*ServiceDeskLog_CL*` .

## <a name="troubleshoot-service-manager-web-app-deployment"></a>Felsöka Service Manager Web App-distribution
-   Om du har problem med att distribuera webbappar kontrollerar du att du har behörighet att skapa/distribuera resurser i prenumerationen.
-   Om du får en **objekt referens som inte är inställt på en instans av ett objekt** fel när du kör [skriptet](itsmc-service-manager-script.md), måste du se till att du har angett giltiga värden i avsnittet **användar konfiguration** .
-   Om du inte kan skapa Service Bus Relay-namnområdet kontrollerar du att den nödvändiga resurs leverantören är registrerad i prenumerationen. Om den inte är registrerad skapar du Service Bus Relay-namnområdet manuellt från Azure Portal. Du kan också skapa den när du [skapar hybrid anslutningen](./itsmc-connections.md#configure-the-hybrid-connection) i Azure Portal.


## <a name="contact-us"></a>Kontakta oss

Om du har frågor eller feedback om Anslutningsprogram för hantering av IT-tjänster (ITSM) kan du kontakta oss på [omsitsmfeedback@microsoft.com](mailto:omsitsmfeedback@microsoft.com) .

## <a name="next-steps"></a>Nästa steg
[Lägg till ITSM produkter/tjänster i Anslutningsprogram för hantering av IT-tjänster (ITSM)](./itsmc-connections.md)
