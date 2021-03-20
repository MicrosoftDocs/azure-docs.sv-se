---
title: Konfigurera ASE v1
description: Konfiguration, hantering och övervakning av App Service-miljön v1. Detta dokument tillhandahålls endast för kunder som använder den äldre v1-ASE.
author: ccompy
ms.assetid: b5a1da49-4cab-460d-b5d2-edd086ec32f4
ms.topic: article
ms.date: 07/11/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 598e43d07c213cfeb25f0ecbc7bd02b6ec54b7ed
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "88962595"
---
# <a name="configuring-an-app-service-environment-v1"></a>Konfigurera en App Service-miljön v1

> [!NOTE]
> Den här artikeln gäller App Service-miljön v1.  Det finns en nyare version av App Service-miljön som är enklare att använda och som körs på en kraftfullare infrastruktur. Om du vill veta mer om den nya versionen börjar [du med introduktionen till App Service-miljön](intro.md).
> 

## <a name="overview"></a>Översikt
På en hög nivå består en Azure App Service Environment av flera huvud komponenter:

* Beräknings resurser som körs i den App Service-miljön värdbaserade tjänsten
* Storage
* En databas
* En klassisk (v1) eller Resource Manager (v2) Azure-Virtual Network (VNet) 
* Ett undernät med den App Service-miljön värdbaserade tjänsten som körs i det

### <a name="compute-resources"></a>Beräknings resurser
Du använder beräknings resurserna för dina fyra resurspooler.  Varje App Service-miljön (ASE) har en uppsättning klient delar och tre möjliga pooler för arbets grupper. Du behöver inte använda alla tre Worker-poolerna, om du vill kan du bara använda en eller två.

Värdarna i resurspooler (klient delar och arbetare) är inte direkt tillgängliga för klienter. Du kan inte använda Remote Desktop Protocol (RDP) för att ansluta till dem, ändra deras etablering eller agera som administratör på dem.

Du kan ange antal och storlek för resurspoolen. I en ASE har du fyra storleks alternativ som heter P1 till P4. Mer information om de storlekar och deras priser finns i [App Service prissättning](https://azure.microsoft.com/pricing/details/app-service/).
Ändring av kvantitet eller storlek kallas för en skalnings åtgärd.  Endast en skalnings åtgärd kan pågå åt gången.

**Klient** delar: klient delens slut punkter är http/https-slutpunkterna för dina appar som lagras i din ASE. Du kör inte arbets belastningar på klient delen.

* En ASE börjar med två P2s, vilket är tillräckligt för arbets belastningar för utveckling/testning och produktions arbets belastningar på låg nivå. Vi rekommenderar starkt P3s för måttlig till tung produktions arbets belastningar.
* För måttliga till tunga produktions arbets belastningar rekommenderar vi att du har minst fyra P3s för att se till att det finns tillräckligt med klient ändar när det schemalagda underhållet sker. Schemalagda underhålls aktiviteter kommer att ta en klient del åt gången. Detta minskar den övergripande tillgängliga klient delens kapacitet under underhålls aktiviteter.
* Klient delar kan ta upp till en timme att etablera. 
* För att finjustera fin justeringen bör du övervaka processor procent, minnes procent och aktiva begär ande mått för frontend-poolen. Om processor-eller minnes procenten är över 70 procent när du kör P3s, lägger du till fler klient delar. Om värdet för aktiva begär Anden löper ut till 15 000 och 20 000 begär Anden per klient del bör du även lägga till fler klient delar. Det övergripande målet är att hålla processor-och minnes procenten under 70% och aktiva begär Anden som är i genomsnitt upp till under 15 000 begär Anden per klient del när du kör P3s.  

**Arbetare**: arbetarna är de appar som faktiskt körs. När du skalar upp dina App Service-planer, används arbets tagarna i den associerade arbets gruppen.

* Du kan inte lägga till arbetare direkt. Det kan ta upp till en timme att etablera.
* Att skala storleken på en beräknings resurs för en pool tar < 1 timme per uppdaterings domän. Det finns 20 uppdaterings domäner i en ASE. Om du skalade beräknings storleken för en arbets grupp med 10 instanser kan det ta upp till 10 timmar att slutföra.
* Om du ändrar storleken på de beräknings resurser som används i en arbets grupp kommer du att orsaka att appar som körs i den aktuella arbets gruppen startas.

Det snabbaste sättet att ändra beräknings resurs storleken för en arbets grupp som inte kör några appar är att:

* Skala ned antalet arbetare till 2.  Minimi storleken på nedskalning i portalen är 2. Det tar några minuter att frigöra dina instanser. 
* Välj den nya beräknings storleken och antalet instanser. Härifrån tar det upp till 2 timmar att slutföra.

Om dina appar kräver en större beräknings resurs storlek kan du inte utnyttja den tidigare vägledningen. I stället för att ändra storleken på den arbets grupp som är värd för dessa appar kan du fylla i en annan arbets grupp med arbetare med önskad storlek och flytta dina appar till poolen.

* Skapa ytterligare instanser av den nödvändiga beräknings storleken i en annan arbets grupp. Det tar upp till en timme att slutföra.
* Omtilldela dina App Service planer som är värdar för de appar som behöver en större storlek på den nykonfigurerade arbets gruppen. Det här är en snabb åtgärd som tar mindre än en minut att slutföra.  
* Skala ned den första Worker-poolen om du inte behöver de oanvända instanserna längre. Det tar några minuter att slutföra den här åtgärden.

Automatisk **skalning**: ett av de verktyg som kan hjälpa dig att hantera förbrukningen av beräknings resurser är automatisk skalning. Du kan använda autoskalning för klient delen eller arbets grupper. Du kan t. ex. öka dina instanser av valfri typ av pool på morgon och minska den på kvällen. Eller så kanske du kan lägga till instanser när antalet arbetare som är tillgängliga i en arbets pool sjunker under ett visst tröskelvärde.

Om du vill ange regler för autoskalning runt mått för Compute-resurspoolen ska du tänka på hur lång tid etableringen kräver. Mer information om autoskalning App Service miljöer finns i [så här konfigurerar du automatisk skalning i en app service-miljön][ASEAutoscale].

### <a name="storage"></a>Storage
Varje ASE konfigureras med 500 GB lagrings utrymme. Det här utrymmet används i alla appar i ASE. Det här lagrings utrymmet ingår i ASE och kan för närvarande inte växlas till att använda lagrings utrymmet. Om du gör justeringar av routning eller säkerhet för virtuellt nätverk måste du ändå tillåta åtkomst till Azure Storage-eller att ASE inte fungerar.

### <a name="database"></a>Databas
Databasen innehåller den information som definierar miljön, samt information om de appar som körs i den. Detta är också en del av Azure-prenumerationen. Det är inte något som du har direkt möjlighet att manipulera. Om du gör justeringar av routning eller säkerhet för virtuellt nätverk måste du ändå tillåta åtkomst till SQL Azure-eller att ASE inte fungerar.

### <a name="network"></a>Nätverk
Det virtuella nätverket som används med din ASE kan vara ett som du gjorde när du skapade ASE eller en som du gjorde i förväg. När du skapar under nätet när ASE skapas tvingar det ASE att finnas i samma resurs grupp som det virtuella nätverket. Om du behöver den resurs grupp som används av din ASE för att vara en annan än den för ditt VNet måste du skapa din ASE med hjälp av en Resource Manager-mall.

Det finns vissa begränsningar för det virtuella nätverk som används för en ASE:

* Det virtuella nätverket måste vara ett regionalt virtuellt nätverk.
* Det måste finnas ett undernät med 8 eller fler adresser där ASE distribueras.
* När ett undernät används för att vara värd för en ASE kan inte adress intervallet för under nätet ändras. Därför rekommenderar vi att under nätet innehåller minst 64 adresser för att kunna hantera framtida ASE tillväxt.
* Det kan finnas någonting annat i under nätet, men ASE.

Till skillnad från den värdbaserade tjänsten som innehåller ASE, finns det [virtuella nätverket][virtualnetwork] och under nätet under användar kontroll.  Du kan administrera ditt virtuella nätverk via Virtual Network gränssnittet eller PowerShell.  En ASE kan distribueras i ett klassiskt eller Resource Manager VNet.  Portal-och API-upplevelsen skiljer sig något från de klassiska och Resource Manager-virtuella nätverk, men ASE-upplevelsen är densamma.

Det VNet som används som värd för en ASE kan använda antingen privata RFC1918-IP-adresser eller så kan den använda offentliga IP-adresser.  Om du vill använda ett IP-intervall som inte täcks av RFC1918 (10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16) måste du skapa ditt VNet och undernät som ska användas av din ASE före skapande av ASE.

Eftersom den här funktionen placerar Azure App Service i det virtuella nätverket, innebär det att dina appar som finns i din ASE nu kan komma åt resurser som är tillgängliga via ExpressRoute eller virtuella privata nätverk (VPN) från plats till plats direkt. De appar som finns inom App Service-miljön behöver inte fler nätverksfunktioner för att få åtkomst till resurser som är tillgängliga för det virtuella nätverk som är värd för din App Service-miljön. Det innebär att du inte behöver använda VNET-integration eller Hybridanslutningar för att komma åt resurser i eller anslutna till det virtuella nätverket. Du kan fortfarande använda båda dessa funktioner för att få åtkomst till resurser i nätverk som inte är anslutna till ditt virtuella nätverk.

Du kan till exempel använda VNET-integration för att integrera med ett virtuellt nätverk som finns i din prenumeration, men inte är anslutet till det virtuella nätverk som din ASE finns i. Du kan fortfarande använda Hybridanslutningar för att få åtkomst till resurser i andra nätverk, precis som du vanligt vis kan.  

Om du har konfigurerat det virtuella nätverket med en ExpressRoute VPN bör du vara medveten om några av de olika Dirigerings behoven som en ASE har. Det finns vissa UDR-konfigurationer (User Defined Route) som är inkompatibla med en ASE. Mer information om hur du kör en ASE i ett virtuellt nätverk med ExpressRoute finns i [köra ett App Service-miljön i ett virtuellt nätverk med ExpressRoute][ExpressRoute].

#### <a name="securing-inbound-traffic"></a>Skydda inkommande trafik
Det finns två primära metoder för att styra inkommande trafik till din ASE.  Du kan använda nätverks säkerhets grupper (NSG: er) för att kontrol lera vilka IP-adresser som har åtkomst till din ASE enligt beskrivningen här [så här kontrollerar du inkommande trafik i en app service-miljön](app-service-app-service-environment-control-inbound-traffic.md) och du kan också konfigurera din ASE med en intern load BALANCER (ILB).  Dessa funktioner kan också användas tillsammans om du vill begränsa åtkomsten med NSG: er till din ILB-ASE.

När du skapar en ASE skapas en VIP i ditt VNet.  Det finns två VIP-typer, externa och interna.  När du skapar en ASE med en extern VIP kommer dina appar i din ASE att vara tillgängliga via en IP-adress med Internet. När du väljer intern kommer ASE att konfigureras med en ILB och kommer inte att vara tillgänglig direkt från Internet.  En ILB-ASE kräver fortfarande en extern VIP men den används endast för Azure-hantering och underhålls åtkomst.  

Under ILB ASE skapar du den under domän som används av ILB-ASE och måste hantera din egen DNS för den under domän som du anger.  Eftersom du anger namnet på under domänen måste du också hantera certifikatet som används för HTTPS-åtkomst.  När ASE har skapats uppmanas du att ange certifikatet.  Mer information om hur du skapar och använder en ILB-ASE finns i [använda ett internt Load Balancer med ett App Service-miljön][ILBASE]. 

## <a name="portal"></a>Portal
Du kan hantera och övervaka dina App Service-miljön med hjälp av användar gränssnittet i Azure Portal. Om du har en ASE kan du förmodligen se App Service symbolen på din marginal List. Den här symbolen används för att representera App Service miljöer i Azure Portal:

![App Service-miljön symbol][1]

Om du vill öppna användar gränssnittet som visar alla dina App Service-miljöer kan du använda ikonen eller genom att välja ikonen för» (">") längst ned på sid panelen för att välja App Service miljöer. Genom att välja ett av de ASE som visas i listan öppnar du det användar gränssnitt som används för att övervaka och hantera det.

![Användar gränssnitt för övervakning och hantering av dina App Service-miljön][2]

Det första bladet visar vissa egenskaper för din ASE, tillsammans med ett mått diagram per resurspool. Några av egenskaperna som visas i **Essentials** -blocket är också hyperlänkar som öppnar det blad som är associerat med det. Du kan till exempel välja **Virtual Network** namn för att öppna det användar gränssnitt som är associerat med det virtuella nätverk som din ASE körs i. **App Service planer** och **appar** varje öppna blad som visar de objekt som finns i din ASE.  

### <a name="monitoring"></a>Övervakning
Med diagrammen kan du se olika prestanda mått i varje resurspool. För frontend-poolen kan du övervaka genomsnitts processor och minne. För Worker-pooler kan du övervaka den kvantitet som används och den kvantitet som är tillgänglig.

Flera App Services planer kan använda arbets tagarna i en arbets grupp. Arbets belastningen distribueras inte på samma sätt som frontend-servrarna, så CPU-och minnes användningen ger inte mycket bra information. Det är viktigt att spåra hur många arbetare som du har använt och är tillgängliga – särskilt om du hanterar systemet så att andra kan använda den.  

Du kan också använda alla mät värden som kan spåras i diagrammen för att ställa in aviseringar. Att ställa in aviseringar här fungerar på samma sätt som andra i App Service. Du kan ställa in en avisering från antingen **aviserings** gränssnittets del eller från att gå in i ett mått användar gränssnitt och välja **Lägg till avisering**.

![Mått användar gränssnitt][3]

Måtten som precis har diskuterats är de App Service-miljön måtten. Det finns också mått som är tillgängliga på App Service plan nivå. Det är här som övervakar CPU och minne är mycket bra.

I en ASE är alla App Service planer dedikerade App Service planer. Det innebär att de enda appar som körs på värdarna som är allokerade till den App Service plan är apparna i App Service plan. Om du vill se information om din App Service plan kan du hämta App Service plan från någon av listorna i användar gränssnittet för ASE eller **bläddra App Service planer** (som visar en lista över alla).   

### <a name="settings"></a>Inställningar
I bladet ASE finns det ett **inställnings** avsnitt som innehåller flera viktiga funktioner:

**Inställningar**  >  **Egenskaper**: **inställnings** bladet öppnas automatiskt när du hämtar ditt ASE-blad. Högst upp är **Egenskaper**. Det finns ett antal objekt som är överflödiga till det du ser i **Essentials**, men vad är mycket användbart för **virtuella IP**-adresser, samt **utgående IP-adresser**.

![Bladet inställningar och egenskaper][4]

**Inställningar**  >  **IP-adresser**: när du skapar en IP Secure Sockets Layer-app (SSL) i din ASE behöver du en IP SSL-adress. Din ASE behöver IP SSL adresser som det äger som kan allokeras för att kunna hämta en. När en ASE skapas har den en IP SSL-adress för det här ändamålet, men du kan lägga till fler. Det finns en avgift för ytterligare IP SSL adresser, som du ser i [App Service priser][AppServicePricing] (i avsnittet om SSL-anslutningar). Det ytterligare priset är IP SSL priset.

**Inställningar**  >  **Klient dels grupp**  /  **Yrkespooler**: var och en av de här mallarna för resurspooler ger möjlighet att bara se information i den resurspoolen, förutom att tillhandahålla kontroller för att skala resurspoolen fullständigt.  

Bas bladet för varje resurspool ger ett diagram med mät värden för den resurspoolen. Precis som med diagrammen från bladet ASE kan du gå till diagrammet och ställa in aviseringar efter behov. Att ställa in en avisering från ASE-bladet för en speciell resurspool gör samma sak som att göra den från resurspoolen. Från bladet **Inställningar** för arbets grupp har du åtkomst till alla appar eller App Service planer som körs i den här arbets gruppen.

![Användar gränssnitt för arbets grupp inställningar][5]

### <a name="portal-scale-capabilities"></a>Funktioner för Portal skalning
Det finns tre skalnings åtgärder:

* Ändra antalet IP-adresser i ASE som är tillgängliga för IP SSL-användning.
* Ändra storleken på den beräknings resurs som används i en resurspool.
* Ändra antalet beräknings resurser som används i en resurspool, antingen manuellt eller genom automatisk skalning.

I portalen finns det tre sätt att kontrol lera hur många servrar som finns i dina resurspooler:

* En skalnings åtgärd från huvud bladet ASE överst. Du kan göra flera skalnings konfigurations ändringar i frontend-och Worker-poolerna. Alla används som en enda åtgärd.
* En manuell skalnings åtgärd från bladet enskilda resurspooler **skala** , som finns under **Inställningar**.
* Automatisk skalning, som du ställer in från bladet för **skalning** av en resurspool.

Om du vill använda skalnings åtgärden på bladet ASE drar du skjutreglaget till den kvantitet som du vill ha och sparar. Det här gränssnittet stöder även ändring av storleken.  

![Skalnings gränssnitt][6]

Om du vill använda funktionerna för manuell eller autoskalning i en speciell resurspool går du till **Inställningar**  >  **klient delen**  /  **Worker-pooler** efter behov. Öppna sedan den pool som du vill ändra. Gå till **Inställningar**  >  **skala ut** eller **Inställningar**  >  **skala upp**. På bladet **skala ut** kan du kontrol lera instans antal. Genom att **skala upp** kan du kontrol lera resursens storlek.  

![Skalnings inställningar gränssnitt][7]

## <a name="fault-tolerance-considerations"></a>Överväganden vid fel tolerans
Du kan konfigurera en App Service-miljön att använda upp till 55 totala beräknings resurser. För dessa 55-beräknings resurser kan endast 50 användas för att hantera arbets belastningar. Orsaken till detta är Twofold. Det finns minst 2 beräknings resurser på klient sidan.  Det innebär att upp till 53 har stöd för tilldelningen av resurspool. För att kunna tillhandahålla fel tolerans måste du ha en ytterligare beräknings resurs som tilldelas enligt följande regler:

* Varje arbets grupp måste ha minst 1 ytterligare beräknings resurs som inte är tillgänglig för att tilldelas en arbets belastning.
* När antalet beräknings resurser i en arbets grupp hamnar över ett visst värde, krävs en annan beräknings resurs för fel tolerans. Detta är inte fallet i front-end-poolen.

I en enskild arbets pool är fel tolerans kraven för ett angivet värde på X-resurser tilldelade till en arbets grupp:

* Om X är mellan 2 och 20 är mängden användbara beräknings resurser som du kan använda för arbets belastningar X-1.
* Om X är mellan 21 och 40 är mängden användbara beräknings resurser som du kan använda för arbets belastningar X-2.
* Om X är mellan 41 och 53 är mängden användbara beräknings resurser som du kan använda för arbets belastningar X-3.

Det minsta utrymmet har 2 frontend-servrar och 2 arbetare.  Med ovanstående instruktioner är det några exempel att klargöra följande:  

* Om du har 30 arbetare i en enda pool kan du använda 28 av dem som värd för arbets belastningar.
* Om du har två arbetare i en enda pool kan 1 användas som värd för arbets belastningar.
* Om du har 20 arbetare i en enda pool kan 19 användas som värd för arbets belastningar.  
* Om du har 21 arbetare i en enda pool är det fortfarande bara 19 som kan användas som värd för arbets belastningar.  

Fel tolerans aspekten är viktig, men du måste ha den i åtanke när du skalar över vissa tröskelvärden. Om du vill lägga till mer kapacitet från 20 instanser går du till 22 eller högre eftersom 21 inte lägger till fler kapaciteter. Samma sak går över 40, där nästa nummer som lägger till kapacitet är 42.  

## <a name="deleting-an-app-service-environment"></a>Ta bort en App Service-miljön
Om du vill ta bort en App Service-miljön använder du bara åtgärden **ta bort** högst upp på bladet App Service-miljön. När du gör det uppmanas du att ange namnet på din App Service-miljön för att bekräfta att du verkligen vill göra detta. Observera att när du tar bort en App Service-miljön tar du även bort allt innehåll i det.  

![Ta bort ett App Service-miljön användar gränssnitt][9]  

## <a name="getting-started"></a>Komma igång
Information om hur du kommer igång med App Service miljöer finns i [så här skapar du en app service-miljön](app-service-web-how-to-create-an-app-service-environment.md).

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!--Image references-->
[1]: ./media/app-service-web-configure-an-app-service-environment/ase-icon.png
[2]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-aseblade.png
[3]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-poolchart.png
[4]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-properties.png
[5]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-poolblade.png
[6]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-scalecommand.png
[7]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-poolscale.png
[8]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-pricingtiers.png
[9]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-deletease.png

<!--Links-->
[WhatisASE]: app-service-app-service-environment-intro.md
[Appserviceplans]: ../overview-hosting-plans.md
[HowtoCreateASE]: app-service-web-how-to-create-an-app-service-environment.md
[HowtoScale]: app-service-web-scale-a-web-app-in-an-app-service-environment.md
[ControlInbound]: app-service-app-service-environment-control-inbound-traffic.md
[virtualnetwork]: ../../virtual-network/virtual-networks-faq.md
[AppServicePricing]: https://azure.microsoft.com/pricing/details/app-service/
[ASEAutoscale]: app-service-environment-auto-scale.md
[ExpressRoute]: app-service-app-service-environment-network-configuration-expressroute.md
[ILBASE]: app-service-environment-with-internal-load-balancer.md