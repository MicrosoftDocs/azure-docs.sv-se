---
title: Skapa anslutnings övervakare – Azure Portal
titleSuffix: Azure Network Watcher
description: I den här artikeln beskrivs hur du skapar en övervakare i anslutnings övervakaren med hjälp av Azure Portal.
services: network-watcher
documentationcenter: na
author: vinigam
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/23/2020
ms.author: vinigam
ms.openlocfilehash: edf671c8005fa67f6161f383c503ca278dba3105
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101702172"
---
# <a name="create-a-monitor-in-connection-monitor-by-using-the-azure-portal"></a>Skapa en övervakare i anslutnings övervakaren med hjälp av Azure Portal

> [!IMPORTANT]
> Från och med 1 juli 2021 kommer du inte att kunna lägga till nya tester på en befintlig arbets yta eller aktivera en ny arbets yta i Övervakare av nätverksprestanda. Du kommer inte heller att kunna lägga till nya anslutnings övervakare i anslutnings övervakaren (klassisk). Du kan fortsätta att använda de tester och anslutnings Övervakare som skapades före den 1 juli 2021. [Migrera dina tester från övervakare av nätverksprestanda](migrate-to-connection-monitor-from-network-performance-monitor.md) eller [Migrera från anslutnings övervakaren (klassisk)](migrate-to-connection-monitor-from-connection-monitor-classic.md) till den nya anslutnings övervakaren i Azure Network Watcher före den 29 februari 2024 för att minimera avbrott i tjänsten för dina aktuella arbets belastningar.

Lär dig hur du använder anslutnings övervakaren för att övervaka kommunikationen mellan dina resurser. I den här artikeln beskrivs hur du skapar en övervakare med hjälp av Azure Portal. Anslutnings övervakaren stöder hybrid-och Azure Cloud-distributioner.


## <a name="before-you-begin"></a>Innan du börjar 

I anslutnings Övervakare som du skapar med anslutnings övervakaren kan du lägga till både lokala datorer och virtuella Azure-datorer som källor. Dessa anslutnings övervakare kan också övervaka anslutningar till slut punkter. Slut punkterna kan vara på Azure eller på någon annan URL eller IP-adress.

Här följer några definitioner för att komma igång:

* **Anslutnings övervaknings resurs**. En landsspecifika Azure-resurs. Alla följande entiteter är egenskaper för en anslutnings övervaknings resurs.
* **Slut punkt**. En källa eller ett mål som deltar i anslutnings kontroller. Exempel på slut punkter är:
    * Virtuella Azure-datorer.
    * Virtuella Azure-nätverk.
    * Azure-undernät.
    * Lokala agenter.
    * Lokala undernät.
    * Lokala anpassade nätverk som innehåller flera undernät.
    * URL: er och IP-adresser.
* **Testa konfiguration**. En protokoll-speciell konfiguration för ett test. Utifrån det protokoll du väljer kan du definiera port, tröskelvärden, test frekvens och andra parametrar.
* **Test grupp**. Gruppen som innehåller käll slut punkter, mål slut punkter och testkonfigurationer. En anslutnings övervakare kan innehålla fler än en test grupp.
* **Test**. Kombinationen av en käll slut punkt, mål slut punkt och test konfiguration. Ett test är den mest detaljerade nivån där övervaknings data är tillgängliga. Övervaknings data innehåller procent andelen kontroller som misslyckades och tur och retur-tiden.

:::image type="content" source="./media/connection-monitor-2-preview/cm-tg-2.png" alt-text="Diagram som visar en anslutnings övervakare och definierar relationen mellan test grupper och tester.":::


## <a name="create-a-connection-monitor"></a>Skapa en anslutningsövervakare

Så här skapar du en övervakare i anslutnings övervakaren med hjälp av Azure Portal:

1. På Azure Portal start sida går du till **Network Watcher**.
1. I det vänstra fönstret i avsnittet **övervakning** väljer du **anslutnings övervakare**.

   Du ser alla anslutnings Övervakare som skapades i anslutnings övervakaren. Om du vill se de anslutnings Övervakare som skapades i den klassiska anslutnings övervakaren går du till fliken **anslutnings övervakare** .

   :::image type="content" source="./media/connection-monitor-2-preview/cm-resource-view.png" alt-text="Skärm bild som visar anslutnings Övervakare som skapats i anslutnings övervakaren.":::
   
    
1. På instrument panelen för **anslutnings övervakaren** , i det övre vänstra hörnet, väljer du **skapa**.

   

1. På fliken **grundläggande** anger du information för anslutnings övervakaren: 
   * **Namn på anslutnings övervakare**: Ange ett namn för anslutnings övervakaren. Använd standard namngivnings reglerna för Azure-resurser.
   * **Prenumeration**: Välj en prenumeration för anslutnings övervakaren.
   * **Region**: Välj en region för anslutnings övervakaren. Du kan bara välja de virtuella käll datorer som skapas i den här regionen.
   * **Arbets ytans konfiguration**: Välj en anpassad arbets yta eller standard arbets ytan. Din arbets yta innehåller dina övervaknings data.
       * Markera kryss rutan om du vill använda standard arbets ytan. 
       * Om du vill välja en anpassad arbets yta avmarkerar du kryss rutan. Välj sedan prenumeration och region för din anpassade arbets yta. 

   :::image type="content" source="./media/connection-monitor-2-preview/create-cm-basics.png" alt-text="Skärm bild som visar fliken grundläggande i anslutnings övervakaren.":::
   
1. Längst ned på fliken väljer du **Nästa: test grupper**.

1. Lägg till källor, destinationer och testa konfigurationer i dina test grupper. Information om hur du konfigurerar test grupper finns i [skapa test grupper i anslutnings övervakaren](#create-test-groups-in-a-connection-monitor). 

   :::image type="content" source="./media/connection-monitor-2-preview/create-tg.png" alt-text="Skärm bild som visar fliken test grupper i anslutnings övervakaren.":::

1. Längst ned på fliken väljer du **Nästa: skapa aviseringar**. Information om hur du skapar aviseringar finns i [skapa aviseringar i anslutnings övervakaren](#create-alerts-in-connection-monitor).

   :::image type="content" source="./media/connection-monitor-2-preview/create-alert.png" alt-text="Skärm bild som visar fliken Skapa avisering.":::

1. Längst ned på fliken väljer du **Nästa: granska + skapa**.

1. På fliken **Granska + skapa** granskar du grundläggande information och test grupper innan du skapar anslutnings övervakaren. Om du behöver redigera anslutnings övervakaren kan du göra det genom att gå tillbaka till respektive flik. 
   :::image type="content" source="./media/connection-monitor-2-preview/review-create-cm.png" alt-text="Skärm bild som visar fliken Granska + skapa i anslutnings övervakaren.":::
   > [!NOTE] 
   > På fliken **Granska + skapa** visas kostnaden per månad under steget för anslutnings övervakning. För närvarande visar kolumnen **aktuell kostnad/månad** ingen avgift. När anslutnings övervakaren blir allmänt tillgänglig visas en månatlig avgift i den här kolumnen. 
   > 
   > Även under fasen anslutnings övervakare gäller Log Analytics inmatnings avgifter.

1. När du är redo att skapa anslutnings övervakaren väljer du **skapa** längst ned på fliken **Granska + skapa** .

Anslutnings övervakaren skapar anslutnings övervaknings resursen i bakgrunden.

## <a name="create-test-groups-in-a-connection-monitor"></a>Skapa test grupper i en anslutnings övervakare

Varje test grupp i en anslutnings övervakare innehåller källor och mål som testas på nätverks parametrar. De testas för procent andelen kontroller som inte kan utföras och sökning efter testkonfigurationer.

Skapa en test grupp i en anslutnings övervakare genom att ange värden för följande fält i Azure Portal:

* **Inaktivera test grupp**: du kan markera den här kryss rutan om du vill inaktivera övervakning för alla källor och mål som test gruppen anger. Detta val rensas som standard.
* **Namn**: ge test gruppen ett namn.
* **Källor**: du kan ange både virtuella Azure-datorer och lokala datorer som källor om agenter är installerade på dem. Information om hur du installerar en agent för din källa finns i [Installera övervaknings agenter](./connection-monitor-overview.md#install-monitoring-agents).
   * Välj Azure-agenter genom att välja fliken **Azure-slutpunkter** . Här ser du bara virtuella datorer som är kopplade till den region som du angav när du skapade anslutnings övervakaren. Som standard grupperas de virtuella datorerna i den prenumeration som de tillhör. De här grupperna är komprimerade. 
   
       Du kan öka detalj nivån från **prenumerations** nivån till andra nivåer i hierarkin:

      **Prenumeration**  >  **Resurs grupp**  >  **VNet**  >  **Undernät**  >  **Virtuella datorer med agenter**

      Du kan också ändra **Group by** -väljaren för att starta trädet från en annan nivå. Om du till exempel grupperar efter virtuellt nätverk ser du de virtuella datorer som har agenter **i hierarkin**  >  **virtuella**  >  **datorer i virtuella nätverk med agenter**.

       När du väljer ett virtuellt nätverk, undernät eller en enskild virtuell dator anges motsvarande resurs-ID som slut punkt. Som standard deltar alla virtuella datorer i det valda VNET eller undernät som har Azure Network Watcher-tillägget i övervakning. Du kan minska omfattningen genom att antingen välja vissa undernät eller agenter eller ändra värdet för egenskapen omfattning. 

      :::image type="content" source="./media/connection-monitor-2-preview/add-azure-sources.png" alt-text="Skärm bild som visar fönstret Lägg till källor och fliken Azure-slutpunkter i anslutnings övervakaren.":::

   * Välj fliken **ej – Azure-slutpunkter** om du vill välja lokala agenter. Som standard grupperas agenter i arbets ytor efter region. De här arbets ytorna har Övervakare av nätverksprestanda kon figurer ATS. 
   
       Om du behöver lägga till Övervakare av nätverksprestanda i din arbets yta kan du hämta den från [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/solarwinds.solarwinds-orion-network-performance-monitor?tab=Overview). Information om hur du lägger till Övervakare av nätverksprestanda finns [i övervaknings lösningar i Azure Monitor](../azure-monitor/insights/solutions.md). 
   
       Under **skapa anslutnings övervakare** på fliken **grundläggande** väljs standard region. Om du ändrar region kan du välja agenter från arbets ytor i den nya regionen. Du kan välja en eller flera agenter eller undernät. I vyn **undernät** kan du välja vissa IP-adresser för övervakning. Om du lägger till flera undernät skapas ett anpassat lokalt nätverk med namnet **OnPremises_Network_1** . Du kan också ändra **Group** by-väljaren för att gruppera efter agenter.

      :::image type="content" source="./media/connection-monitor-2-preview/add-non-azure-sources.png" alt-text="Skärm bild som visar fönstret Lägg till källor och fliken icke-Azure-slutpunkter i anslutnings övervakaren.":::

   * Om du vill välja nyligen använda slut punkter kan du använda fliken **senaste slut punkt** 
   
   * När du har konfigurerat källorna väljer du **klar** längst ned på fliken. Du kan fortfarande redigera grundläggande egenskaper som slut punkts namn genom att välja slut punkten i vyn **skapa test grupp** . 

* **Mål**: du kan övervaka anslutningen till en virtuell Azure-dator, en lokal dator eller en slut punkt (en offentlig IP-adress, URL eller FQDN) genom att ange den som mål. I en enda test grupp kan du lägga till virtuella Azure-datorer, lokala datorer, Office 365-URL: er, Dynamics 365-URL: er och anpassade slut punkter.

    * Välj fliken **Azure-slutpunkter** om du vill välja virtuella Azure-datorer som mål. Som standard grupperas de virtuella Azure-datorerna i en prenumerationsbegäran i den region som du valde under **skapa anslutnings övervakare** på fliken **grundläggande** . Du kan ändra region och välja virtuella Azure-datorer från den nya regionen. Sedan kan du öka detalj nivån från **prenumerations** nivån till andra nivåer i hierarkin, precis som när du ställer in käll-Azure-slutpunkter.

      Du kan välja virtuella nätverk, undernät eller enskilda virtuella datorer, precis som när du ställer in käll-Azure-slutpunkter. När du väljer ett virtuellt nätverk, undernät eller en enskild virtuell dator anges motsvarande resurs-ID som slut punkt. Som standard deltar alla virtuella datorer i det valda VNET-nätverket eller under nätet som har Network Watcher tillägget i övervakning. Du kan minska omfattningen genom att antingen välja vissa undernät eller agenter eller ändra värdet för egenskapen omfattning. 

      :::image type="content" source="./media/connection-monitor-2-preview/add-azure-dests1.png" alt-text="<skärm bild som visar fönstret Lägg till destinationer och fliken Azure-slutpunkter. >":::

      :::image type="content" source="./media/connection-monitor-2-preview/add-azure-dests2.png" alt-text="<skärm bild som visar fönstret Lägg till destinationer på prenumerations nivån. >":::
       
    
    * Välj fliken **icke-Azure-slutpunkter** om du vill välja icke-Azure-agenter som mål. Som standard grupperas agenter i arbets ytor efter region. Alla dessa arbets ytor har Övervakare av nätverksprestanda kon figurer ATS. 
    
      Om du behöver lägga till Övervakare av nätverksprestanda i din arbets yta kan du hämta den från Azure Marketplace. Information om hur du lägger till Övervakare av nätverksprestanda finns [i övervaknings lösningar i Azure Monitor](../azure-monitor/insights/solutions.md). 

      Under **skapa anslutnings övervakare** på fliken **grundläggande**   väljs standard region. Om du ändrar region kan du välja agenter från arbets ytor i den nya regionen. Du kan välja en eller flera agenter eller undernät. I vyn **undernät** kan du välja vissa IP-adresser för övervakning. Om du lägger till flera undernät skapas ett anpassat lokalt nätverk med namnet **OnPremises_Network_1** .  

      :::image type="content" source="./media/connection-monitor-2-preview/add-non-azure-dest.png" alt-text="Skärm bild som visar fönstret Lägg till destinationer och fliken icke-Azure-slutpunkter.":::
    
    * Välj fliken **externa adresser** om du vill välja offentliga slut punkter som mål. I listan över slut punkter ingår Office 365-test-URL: er och Dynamics 365-test-URL: er, grupperat efter namn. Du kan också välja slut punkter som har skapats i andra test grupper i samma anslutnings övervakare. 
    
        Om du vill lägga till en slut punkt går du till det övre högra hörnet och väljer **Lägg till slut punkt**. Ange sedan ett slut punkts namn och en URL, IP eller FQDN.

       :::image type="content" source="./media/connection-monitor-2-preview/add-endpoints.png" alt-text="Skärm bild som visar var du kan lägga till offentliga slut punkter som mål i anslutnings övervakaren.":::

    * Om du vill välja nyligen använda slut punkter går du till fliken **senaste slut punkt**   .
    * När du är klar med att välja mål väljer du **klar**. Du kan fortfarande redigera grundläggande egenskaper som slut punkts namn genom att välja slut punkten i vyn **skapa test grupp** . 

* **Testa konfigurationer**: du kan lägga till en eller flera testkonfigurationer i en test grupp. Skapa en ny test konfiguration med hjälp av fliken **ny konfiguration** . Eller Lägg till en test konfiguration från en annan test grupp i samma anslutnings övervakare från fliken **Välj befintlig** .

    * **Test konfigurations namn**: ge test konfigurationen ett namn.
    * **Protokoll**: Välj **TCP**, **ICMP** eller **http**. Om du vill ändra HTTP till HTTPS väljer du **http** som protokoll och väljer sedan **443** som port.
        * **Skapa konfiguration för TCP-Test**: den här kryss rutan visas bara om du väljer **http** i listan **protokoll** . Markera den här kryss rutan om du vill skapa en annan test konfiguration som använder samma källor och mål som du har angett någon annan stans i konfigurationen. Den nya test konfigurationen heter **\<name of test configuration> _networkTestConfig**.
        * **Inaktivera traceroute**: den här kryss rutan gäller när protokollet är TCP eller ICMP. Markera den här rutan om du vill stoppa källor från att upptäcka topologi och hitta hopp efter hopp.
    * **Målport**: du kan ange en valfri mål Port.
        * **Lyssna på port**: den här kryss rutan gäller när protokollet är TCP. Markera den här kryss rutan om du vill öppna den valda TCP-porten om den inte redan är öppen. 
    * **Test frekvens**: i den här listan anger du hur ofta källor ska pinga mål för det protokoll och den port som du har angett. Du kan välja 30 sekunder, 1 minut, 5 minuter, 15 minuter eller 30 minuter. Välj **anpassad** om du vill ange en annan frekvens än 30 sekunder och 30 minuter. Källorna kommer att testa anslutningen till destinationer utifrån det värde som du väljer. Om du till exempel väljer 30 sekunder kommer källorna att kontrol lera anslutningen till målet minst en gång var 30: e sekund.
    * **Tröskelvärde för lyckade**: du kan ange tröskelvärden för följande nätverks parametrar:
       * **Misslyckade kontroller**: Ange procent andelen kontroller som kan Miss lyckas när källor kontrollerar anslutningen till destinationer med de villkor som du har angett. I TCP-eller ICMP-protokollet kan procent andelen misslyckade kontroller likställas med procent andelen av paket förlusten. För HTTP-protokoll representerar det här värdet den procent andel av HTTP-begäranden som inte fick något svar.
       * **Tur och retur-tid**: Ange önskad sökperiod i millisekunder för hur lång tid det tar att ansluta till målet via test konfigurationen.
       
   :::image type="content" source="./media/connection-monitor-2-preview/add-test-config.png" alt-text="Skärm bild som visar var du konfigurerar en test konfiguration i anslutnings övervakaren.":::
       
## <a name="create-alerts-in-connection-monitor"></a>Skapa aviseringar i anslutnings övervakaren

Du kan ställa in aviseringar om test som inte fungerar baserat på de tröskelvärden som angetts i testkonfigurationerna.

I Azure Portal för att skapa aviseringar för en anslutnings övervakare anger du värden för dessa fält: 

- **Skapa avisering**: du kan markera den här kryss rutan om du vill skapa en mått avisering i Azure Monitor. När du markerar den här kryss rutan aktive ras de andra fälten för redigering. Ytterligare avgifter för aviseringen gäller, baserat på [prissättningen för aviseringar](https://azure.microsoft.com/pricing/details/monitor/). 

- **Omfång**  >  **Resurs**  >  **Hierarki**: de här värdena fylls i automatiskt baserat på de värden som anges på fliken **grundläggande** .

- **Villkors namn**: aviseringen skapas på `Test Result(preview)` måttet. När resultatet av anslutnings övervaknings testet är ett misslyckat resultat, utlöses varnings regeln. 

- **Namn på åtgärds grupp**: du kan ange din e-postadress direkt eller så kan du skapa aviseringar via åtgärds grupper. Om du anger din e-postadress direkt skapas en åtgärds grupp med namnet **NPM e-ActionGroup** . E-post-ID: t läggs till i den åtgärds gruppen. Om du väljer att använda åtgärds grupper måste du välja en tidigare skapad åtgärds grupp. Information om hur du skapar en åtgärds grupp finns [i skapa åtgärds grupper i Azure Portal](../azure-monitor/alerts/action-groups.md). När aviseringen har skapats kan du [Hantera dina aviseringar](../azure-monitor/alerts/alerts-metric.md#view-and-manage-with-azure-portal). 

- **Varnings regel namn**: namnet på anslutnings övervakaren.

- **Aktivera regel vid skapande**: Markera den här kryss rutan om du vill aktivera varnings regeln baserat på villkoret. Inaktivera den här kryss rutan om du vill skapa regeln utan att aktivera den. 

:::image type="content" source="./media/connection-monitor-2-preview/create-alert-filled.png" alt-text="Skärm bild som visar fliken Skapa avisering i anslutnings övervakaren.":::

## <a name="scale-limits"></a>Skalnings gränser

Anslutnings övervakare har följande skalnings gränser:

* Högsta antal anslutnings övervakare per prenumeration per region: 100
* Högsta antal test grupper per anslutnings Övervakare: 20
* Högsta antal källor och mål per anslutnings Övervakare: 100
* Högsta antal testkonfigurationer per anslutnings Övervakare: 2 via Azure Portal

## <a name="next-steps"></a>Nästa steg

* Lär dig [hur du analyserar övervaknings data och ställer in aviseringar](./connection-monitor-overview.md#analyze-monitoring-data-and-set-alerts).
* Lär dig [hur du diagnostiserar problem i nätverket](./connection-monitor-overview.md#diagnose-issues-in-your-network).
