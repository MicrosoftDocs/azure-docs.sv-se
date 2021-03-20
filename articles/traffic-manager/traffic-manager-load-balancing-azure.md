---
title: Använda belastnings Utjämnings tjänster i Azure | Microsoft Docs
description: 'Den här självstudien visar hur du skapar ett scenario med hjälp av Azures belastnings Utjämnings portfölj: Traffic Manager, Application Gateway och Load Balancer.'
services: traffic-manager
documentationcenter: ''
author: duongau
manager: kumudD
ms.service: traffic-manager
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/27/2016
ms.author: duau
ms.openlocfilehash: eaf50f3bdacaf5680bc5ecb1379faff20133b5ce
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98184381"
---
# <a name="using-load-balancing-services-in-azure"></a>Använda belastningsutjämningstjänster i Azure

## <a name="introduction"></a>Introduktion

Microsoft Azure tillhandahåller flera tjänster för att hantera hur nätverks trafiken distribueras och belastnings utjämning. Du kan använda dessa tjänster individuellt eller kombinera deras metoder, beroende på dina behov, för att bygga den optimala lösningen.

I den här självstudien definierar vi först ett kund användnings fall och ser hur det kan göras mer robust och prestera med hjälp av följande Azure-belastnings Utjämnings portfölj: Traffic Manager, Application Gateway och Load Balancer. Vi tillhandahåller sedan stegvisa instruktioner för att skapa en distribution som är geografiskt redundant, distribuerar trafik till virtuella datorer och hjälper dig att hantera olika typer av begär Anden.

På en konceptuell nivå spelar var och en av dessa tjänster en tydlig roll i hierarkin för belastnings utjämning.

* **Traffic Manager** tillhandahåller global belastnings utjämning för DNS. Den söker efter inkommande DNS-begäranden och svarar med en felfri slut punkt, i enlighet med Routningsprincipen som kunden har valt. Alternativen för routningsmetoder är:
  * Routning av prestanda för att skicka begär anden till den närmaste slut punkten i svars tider.
  * Prioritera routning för att dirigera all trafik till en slut punkt, med andra slut punkter som säkerhets kopiering.
  * Viktad resursallokering, som distribuerar trafik baserat på den viktning som tilldelas varje slut punkt.
  * Geografisk routning för att distribuera trafiken till dina program slut punkter baserat på användarens geografiska plats.
  * Subnet-baserad routning för att distribuera trafiken till dina program slut punkter baserat på användarens undernät (IP-adressintervall).
  * Routning med flera värden som gör att du kan skicka IP-adresser för fler än en program slut punkt i ett enda DNS-svar.

  Klienten ansluter direkt till slut punkten som returnerades av Traffic Manager. Azure Traffic Manager identifierar när en slut punkt inte är felfri och dirigerar sedan om klienterna till en annan felordnad instans. Mer information om tjänsten hittar du i [Azure Traffic Manager-dokumentationen](traffic-manager-overview.md) .
* **Application Gateway** tillhandahåller program leverans kontroll (ADC) som en tjänst som erbjuder olika Layer 7 belastnings Utjämnings funktioner för ditt program. Det gör att kunderna kan optimera webb server gruppens produktivitet genom att avlasta CPU-intensiva TLS-avslutning till programgatewayen. Andra funktioner för Layer 7-routning omfattar resursallokerings distribution av inkommande trafik, cookie-baserad sessionsgräns, URL-sökväg baserad Routning och möjligheten att vara värd för flera webbplatser bakom en enda Programgateway. Application Gateway kan konfigureras som en Internetbaserad Gateway, en intern gateway eller en kombination av båda. Application Gateway är helt Azure-hanterad, skalbar och hög tillgänglig. För att få en bättre hantering ingår en omfattande uppsättning diagnostik- och loggningsfunktioner.
* **Load Balancer** är en integrerad del av Azure SDN-stacken som tillhandahåller högpresterande Layer 4-belastnings Utjämnings tjänster med låg latens för alla UDP-och TCP-protokoll. Den hanterar inkommande och utgående anslutningar. Du kan konfigurera offentliga och interna belastningsutjämnade slutpunkter och definiera regler för att mappa inkommande anslutningar till serverdelspooldestinationer med TCP- och HTTP-avsökningsalternativ för att hantera tjänstens tillgänglighet.

## <a name="scenario"></a>Scenario

I det här exempel scenariot använder vi en enkel webbplats som hanterar två typer av innehåll: bilder och dynamiskt återgivna webb sidor. Webbplatsen måste vara geografiskt redundant och den bör betjäna sina användare från den närmaste platsen (lägsta latens) till dem. Programutvecklaren har beslutat att alla URL: er som matchar mönstret/images/* hanteras från en dedikerad pool av virtuella datorer som skiljer sig från resten av webb server gruppen.

Dessutom måste standard-VM-poolen som hanterar det dynamiska innehållet kommunicera med en backend-databas som finns i ett kluster med hög tillgänglighet. Hela distributionen konfigureras via Azure Resource Manager.

Genom att använda Traffic Manager, Application Gateway och Load Balancer kan denna webbplats uppnå dessa design mål:

* **Multi-GEO-redundans**: om en region slutar fungera Traffic Manager dirigera trafiken sömlöst till den närmaste regionen utan att någon åtgärd från program ägaren.
* **Minskad latens**: eftersom Traffic Manager automatiskt dirigerar kunden till den närmaste regionen, får kunden lägre svars tid när innehållet i webb sidan begärs.
* **Oberoende skalbarhet**: eftersom arbets belastningen för webb program skiljs åt av innehålls typen kan program ägaren skala arbets belastningarna oberoende av varandra. Application Gateway säkerställer att trafiken dirigeras till rätt pooler baserat på de angivna reglerna och hälso tillståndet för programmet.
* **Intern belastnings utjämning**: eftersom Load Balancer ligger framför klustret med hög tillgänglighet är det bara den aktiva och felfria slut punkten för en databas som exponeras för programmet. Dessutom kan en databas administratör optimera arbets belastningen genom att distribuera aktiva och passiva repliker i klustret oberoende av klient programmet. Load Balancer levererar anslutningar till klustret med hög tillgänglighet och ser till att endast felfria databaser får anslutnings begär Anden.

Följande diagram visar arkitekturen för det här scenariot:

![Diagram över belastnings Utjämnings arkitektur](./media/traffic-manager-load-balancing-azure/scenario-diagram.png)

> [!NOTE]
> Det här exemplet är bara en av många möjliga konfigurationer av de belastnings Utjämnings tjänster som Azure erbjuder. Traffic Manager, Application Gateway och Load Balancer kan kombineras och matchas så att de passar bäst för dina belastnings Utjämnings behov. Om t. ex. TLS-avlastning eller bearbetning av lager 7 inte behövs kan Load Balancer användas i stället för Application Gateway.

## <a name="setting-up-the-load-balancing-stack"></a>Konfigurera belastnings Utjämnings stacken

### <a name="step-1-create-a-traffic-manager-profile"></a>Steg 1: skapa en Traffic Manager-profil

1. Klicka på **skapa en resurs**  >  **nätverk**  >  **Traffic Manager profil**  >  **skapa** i Azure Portal.
2. Ange följande grundläggande information:

   * **Namn**: ge din Traffic Manager-profil ett DNS-prefix.
   * **Routningsmetod**: Välj principen för trafik-routningsmetod. Mer information om metoderna finns i [about Traffic Manager Traffic routing-metoder](traffic-manager-routing-methods.md).
   * **Prenumeration**: Välj den prenumeration som innehåller profilen.
   * **Resurs grupp**: Välj den resurs grupp som innehåller profilen. Det kan vara en ny eller befintlig resurs grupp.
   * **Resurs grupps plats**: Traffic Manager tjänsten är global och inte kopplad till någon plats. Du måste dock ange en region för gruppen där de metadata som är associerade med Traffic Manager profilen finns. Den här platsen påverkar inte profilens körnings tillgänglighet.

3. Klicka på **skapa** för att generera Traffic Manager profilen.

   ![Bladet skapa Traffic Manager](./media/traffic-manager-load-balancing-azure/s1-create-tm-blade.png)

### <a name="step-2-create-the-application-gateways"></a>Steg 2: skapa programgatewayer

1. Klicka på **skapa en resurs**  >  **nätverks**  >  **Application Gateway** i den vänstra rutan i Azure Portal.
2. Ange följande grundläggande information om Application Gateway:

   * **Namn**: namnet på Application Gateway.
   * **SKU-storlek**: storleken på Application Gateway, som är tillgänglig som liten, medel eller stor.
   * **Instans antal**: antalet instanser, ett värde mellan 2 och 10.
   * **Resurs grupp**: den resurs grupp som innehåller programgatewayen. Det kan vara en befintlig resurs grupp eller en ny.
   * **Plats**: regionen för Application Gateway, som är samma plats som resurs gruppen. Platsen är viktig eftersom det virtuella nätverket och den offentliga IP-adressen måste finnas på samma plats som gatewayen.
3. Klicka på **OK**.
4. Definiera det virtuella nätverket, under nätet, klient delens IP-adress och Listener-konfigurationer för Application Gateway. I det här scenariot är klient delens IP-adress **offentlig**, vilket gör att den kan läggas till som en slut punkt till Traffic Manager profilen senare.
5. Konfigurera lyssnaren med något av följande alternativ:
    * Om du använder HTTP finns det inget att konfigurera. Klicka på **OK**.
    * Om du använder HTTPS krävs ytterligare konfiguration. Se [skapa en Programgateway](../application-gateway/quick-create-portal.md)från och med steg 9. När du har slutfört konfigurationen klickar du på **OK**.

#### <a name="configure-url-routing-for-application-gateways"></a>Konfigurera URL-routning för programgatewayer

När du väljer en backend-pool, tar en Programgateway som kon figurer ATS med en Sök vägs-baserad regel över ett Sök vägs mönster för URL: en för begäran förutom resursallokering-distribution. I det här scenariot lägger vi till en Sök vägs baserad regel för att dirigera en URL med "/images/ \* " till avbildnings serverns pool. Mer information om hur du konfigurerar URL Path-baserad routning för en Programgateway finns i [skapa en Sök vägs baserad regel för en Programgateway](../application-gateway/create-url-route-portal.md).

![Application Gateway webb nivå diagram](./media/traffic-manager-load-balancing-azure/web-tier-diagram.png)

1. Från resurs gruppen går du till den instans av Application Gateway som du skapade i föregående avsnitt.
2. Under **Inställningar** väljer du **Server dels pooler** och väljer sedan **Lägg** till för att lägga till de virtuella datorer som du vill koppla till webb nivåns backend-pooler.
3. Ange namnet på backend-poolen och alla IP-adresser för de datorer som finns i poolen. I det här scenariot ansluter vi två backend-pooler för virtuella datorer.

   ![Application Gateway "Lägg till backend-pool"](./media/traffic-manager-load-balancing-azure/s2-appgw-add-bepool.png)

4. Under **Inställningar** för Application Gateway väljer du **regler** och klickar sedan på knappen **sökväg baserad** för att lägga till en regel.

   ![Knappen "sökväg baserad" för Application Gateway regler](./media/traffic-manager-load-balancing-azure/s2-appgw-add-pathrule.png)

5. Konfigurera regeln genom att ange följande information.

   Grundläggande inställningar:

   + **Namn**: det egna namnet på den regel som är tillgänglig i portalen.
   + **Lyssnare**: den lyssnare som används för regeln.
   + **Standardpool för Server delen**: den backend-pool som ska användas med standard regeln.
   + **Standard-HTTP-inställningar**: de http-inställningar som ska användas med standard regeln.

   Sök vägs-baserade regler:

   + **Namn**: det egna namnet på den Sök vägs baserade regeln.
   + **Sökvägar**: Sök vägs regeln som används för vidarebefordran av trafik.
   + **Backend-pool**: den backend-pool som ska användas med den här regeln.
   + **Http-inställning**: de http-inställningar som ska användas med den här regeln.

   > [!IMPORTANT]
   > Sökvägar: giltiga sökvägar måste börja med "/". Jokertecknet " \* " tillåts endast i slutet. Giltiga exempel är/XYZ,/XYZ \* eller/XYZ/ \* .

   ![Application Gateway bladet "Lägg till Sök vägs regel"](./media/traffic-manager-load-balancing-azure/s2-appgw-pathrule-blade.png)

### <a name="step-3-add-application-gateways-to-the-traffic-manager-endpoints"></a>Steg 3: lägga till programgatewayer i Traffic Manager slut punkter

I det här scenariot är Traffic Manager anslutna till programgatewayer (enligt beskrivningen i föregående steg) som finns i olika regioner. Nu när programgatewayerna har kon figurer ATS är nästa steg att ansluta dem till din Traffic Manager-profil.

1. Öppna din Traffic Manager-profil. Det gör du genom att titta i din resurs grupp eller söka efter namnet på den Traffic Manager profilen från **alla resurser**.
2. I det vänstra fönstret väljer du **slut punkter** och klickar sedan på **Lägg** till för att lägga till en slut punkt.

   ![Traffic Manager slut punkts knappen Lägg till](./media/traffic-manager-load-balancing-azure/s3-tm-add-endpoint.png)

3. Skapa en slut punkt genom att ange följande information:

   * **Typ**: Välj typ av slut punkt för belastnings utjämning. I det här scenariot väljer du **Azure-slutpunkt** eftersom vi ansluter till den Application Gateway-instans som har kon figurer ATS tidigare.
   * **Namn**: Ange namnet på slut punkten.
   * **Mål resurs typ**: Välj **offentlig IP-adress** och välj sedan den offentliga IP-adressen för programgatewayen som kon figurer ATS tidigare under **mål resurs**.

   ![Traffic Manager "Lägg till slut punkt"](./media/traffic-manager-load-balancing-azure/s3-tm-add-endpoint-blade.png)

4. Nu kan du testa installationen genom att komma åt den med DNS för din Traffic Manager-profil (i det här exemplet: TrafficManagerScenario.trafficmanager.net). Du kan skicka om begär Anden, ta fram eller ta bort virtuella datorer och webb servrar som har skapats i olika regioner och ändra Traffic Manager profil inställningar för att testa installationen.

### <a name="step-4-create-a-load-balancer"></a>Steg 4: skapa en belastningsutjämnare

I det här scenariot distribuerar Load Balancer anslutningar från webb nivån till databaserna i ett kluster med hög tillgänglighet.

Om databas klustret med hög tillgänglighet använder SQL Server AlwaysOn, se [Konfigurera en eller flera Always on tillgänglighets grupps lyssnare](../azure-sql/virtual-machines/windows/availability-group-listener-powershell-configure.md) för steg-för-steg-instruktioner.

Mer information om hur du konfigurerar en intern belastningsutjämnare finns [i skapa en intern belastningsutjämnare i Azure Portal](../load-balancer/quickstart-load-balancer-standard-internal-portal.md).

1. I Azure Portal i det vänstra fönstret klickar du på **skapa en resurs**  >  **nätverks**  >  **belastnings utjämning**.
2. Välj ett namn för belastningsutjämnaren.
3. Ange **typen** **internt** och välj lämpligt virtuellt nätverk och undernät som belastningsutjämnaren ska finnas i.
4. Under **IP-adresstilldelning** väljer du antingen **dynamisk** eller **statisk**.
5. Under **resurs grupp** väljer du resurs gruppen för belastningsutjämnaren.
6. Under **plats** väljer du lämplig region för belastningsutjämnaren.
7. Klicka på **skapa** för att generera belastningsutjämnaren.

#### <a name="connect-a-back-end-database-tier-to-the-load-balancer"></a>Anslut databas nivån på Server sidan till belastningsutjämnaren

1. I din resurs grupp letar du reda på belastningsutjämnaren som skapades i föregående steg.
2. Under **Inställningar** klickar du på **backend-pooler** och klickar sedan på **Lägg** till för att lägga till en backend-pool.

   ![Load Balancer "Lägg till backend-pool"](./media/traffic-manager-load-balancing-azure/s4-ilb-add-bepool.png)

3. Ange namnet på backend-poolen.
4. Lägg till antingen enskilda datorer eller en tillgänglighets uppsättning i backend-poolen.

#### <a name="configure-a-probe"></a>Konfigurera en avsökning

1. I belastningsutjämnaren väljer du **avsökningar** under **Inställningar** och klickar sedan på **Lägg** till för att lägga till en avsökning.

   ![Load Balancer "Lägg till avsökning"](./media/traffic-manager-load-balancing-azure/s4-ilb-add-probe.png)

2. Ange namnet på avsökningen.
3. Välj **protokollet** för avsökningen. För en databas kanske du vill ha en TCP-avsökning i stället för en HTTP-avsökning. Mer information om belastnings Utjämnings avsökningar finns i [förstå belastnings Utjämnings avsökningar](../load-balancer/load-balancer-custom-probe-overview.md).
4. Ange vilken **port** i databasen som ska användas för att komma åt avsökningen.
5. Under **intervall** anger du hur ofta programmet ska avsökas.
6. Under fel **tröskel** anger du antalet kontinuerliga avsöknings fel som måste inträffa för att backend-datorn ska anses vara ohälsosam.
7. Klicka på **OK** för att skapa avsökningen.

#### <a name="configure-the-load-balancing-rules"></a>Konfigurera regler för belastnings utjämning

1. Under **Inställningar** för belastningsutjämnaren väljer du **belastnings Utjämnings regler** och klickar sedan på **Lägg till** för att skapa en regel.
2. Ange **namnet** på belastnings Utjämnings regeln.
3. Välj **klient delens IP-adress** för belastningsutjämnaren, **protokoll** och **port**.
4. Under **backend-port** anger du den port som ska användas i backend-poolen.
5. Välj **backend-poolen** och **avsökningen** som skapades i föregående steg för att tillämpa regeln på.
6. Välj hur du vill att sessionerna ska behållas under **sessionen beständighet**.
7. Under **tids gränser för inaktivitet** anger du antalet minuter innan tids gränsen för inaktivitet.
8. Under **flytande IP** väljer du antingen **inaktive rad** eller **aktive rad**.
9. Skapa regeln genom att klicka på **OK**.

### <a name="step-5-connect-web-tier-vms-to-the-load-balancer"></a>Steg 5: Anslut virtuella datorer på webb nivå till belastningsutjämnaren

Nu konfigurerar vi IP-adressen och belastnings Utjämnings klientens frontend-port i de program som körs på dina virtuella datorer på webb nivå för alla databas anslutningar. Den här konfigurationen är speciell för de program som körs på de här virtuella datorerna. Information om hur du konfigurerar målets IP-adress och port finns i program dokumentationen. Du hittar IP-adressen för klient delen genom att gå till klient delens IP-pool på **belastnings Utjämnings inställningarna** i Azure Portal.

![Navigerings fönstret för "klient delens IP-pool" Load Balancer](./media/traffic-manager-load-balancing-azure/s5-ilb-frontend-ippool.png)

## <a name="next-steps"></a>Nästa steg

* [Översikt över Traffic Manager](traffic-manager-overview.md)
* [Översikt över Application Gateway](../application-gateway/overview.md)
* [Översikt över Azure Load Balancer](../load-balancer/load-balancer-overview.md)