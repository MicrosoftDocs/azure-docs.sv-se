---
title: Nätverksfunktioner
description: Lär dig mer om nätverksfunktionerna i Azure App Service och lär dig vilka funktioner du behöver för säkerhet eller andra funktioner.
author: ccompy
ms.assetid: 5c61eed1-1ad1-4191-9f71-906d610ee5b7
ms.topic: article
ms.date: 03/26/2021
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 9ba85ecfe2b57ceb1eed5c51929107a95f5a4669
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104889152"
---
# <a name="app-service-networking-features"></a>App Service nätverksfunktioner

Du kan distribuera program i Azure App Service på flera sätt. Som standard är appar som är värdbaserade i App Service tillgängliga direkt via Internet och kan bara nå Internet-värdbaserade slut punkter. Men för många program måste du kontrol lera inkommande och utgående nätverks trafik. Det finns flera funktioner i App Service som hjälper dig att möta dessa behov. Utmaningen är att veta vilken funktion som ska användas för att lösa ett specifikt problem. I den här artikeln får du hjälp att avgöra vilken funktion som ska användas, baserat på några exempel på användnings fall.

Det finns två huvudsakliga distributions typer för Azure App Service: 
- Den offentliga tjänsten för flera innehavare är värd för App Service planer i pris listorna kostnads fri, delad, Basic, standard, Premium, PremiumV2 och PremiumV3. 
- ASE (Single-Tenant App Service-miljön) är värd för isolerade SKU App Service-planer direkt i ditt virtuella Azure-nätverk. 

Vilka funktioner du använder beror på om du befinner dig i tjänsten för flera innehavare eller i en ASE. 

## <a name="multitenant-app-service-networking-features"></a>Funktioner för flera innehavare App Service nätverksfunktioner 

Azure App Service är ett distribuerat system. Rollerna som hanterar inkommande HTTP-eller HTTPS-begäranden kallas *klient* delar. De roller som är värdar för kundens arbets belastning kallas för *arbetare*. Alla roller i en App Service-distribution finns i ett nätverk med flera innehavare. Eftersom det finns många olika kunder i samma App Service skalnings enhet kan du inte ansluta App Service nätverket direkt till nätverket. 

I stället för att ansluta nätverken behöver du funktioner för att hantera olika aspekter av program kommunikationen. De funktioner som hanterar begär Anden *till* din app kan inte användas för att lösa problem när du ringer *från* din app. På samma sätt kan inte de funktioner som löser problem med anrop från din app användas för att lösa problem med din app.  

| Inkommande funktioner | Utgående funktioner |
|---------------------|-------------------|
| App-tilldelad adress | Hybridanslutningar |
| Åtkomst begränsningar | Gateway – nödvändig VNet-integrering |
| Tjänstslutpunkter | VNET-integration |
| Privata slutpunkter ||

Förutom noterade undantag kan du använda alla dessa funktioner tillsammans. Du kan blanda funktionerna för att lösa dina problem.

## <a name="use-cases-and-features"></a>Använda ärenden och funktioner

För alla typer av användnings fall kan det finnas några sätt att lösa problemet. Att välja den bästa funktionen är ibland mer än själva användnings fallet. I följande fall av inkommande användning föreslås hur du använder App Service nätverksfunktioner för att lösa problem med att styra trafik till din app:
 
| Inkommande användnings fall | Funktion |
|---------------------|-------------------|
| Stöd för IP-baserade SSL-behov för din app | App-tilldelad adress |
| Stöd för delning av en dedikerad inkommande adress för din app | App-tilldelad adress |
| Begränsa åtkomsten till din app från en uppsättning väldefinierade adresser | Åtkomst begränsningar |
| Begränsa åtkomsten till din app från resurser i ett virtuellt nätverk | Tjänstslutpunkter </br> ILB ASE </br> Privata slutpunkter |
| Exponera din app på en privat IP-adress i ditt virtuella nätverk | ILB ASE </br> Privata slutpunkter </br> Privat IP för inkommande trafik på en Application Gateway-instans med tjänst slut punkter |
| Skydda din app med en brand vägg för webbaserade program (WAF) | Application Gateway-och ILB-ASE </br> Application Gateway med privata slut punkter </br> Application Gateway med tjänstslutpunkter </br> Azures frontend-dörr med åtkomst begränsningar |
| Belastnings Utjämnings trafik till dina appar i olika regioner | Azures frontend-dörr med åtkomst begränsningar | 
| Belastnings Utjämnings trafik i samma region | [Application Gateway med tjänstslutpunkter][appgwserviceendpoints] | 

I följande fall av utgående användning föreslås hur du använder App Service nätverksfunktioner för att lösa utgående åtkomst behov för din app:

| Utgående användnings fall | Funktion |
|---------------------|-------------------|
| Få åtkomst till resurser i ett virtuellt Azure-nätverk i samma region | VNET-integration </br> ASE |
| Få åtkomst till resurser i ett virtuellt Azure-nätverk i en annan region | Gateway – nödvändig VNet-integrering </br> ASE och virtuell nätverks-peering |
| Åtkomst till resurser som skyddas med tjänst slut punkter | VNET-integration </br> ASE |
| Få åtkomst till resurser i ett privat nätverk som inte är anslutna till Azure | Hybridanslutningar |
| Få åtkomst till resurser över Azure ExpressRoute-kretsar | VNET-integration </br> ASE | 
| Säker utgående trafik från din webbapp | VNet-integrering och nätverks säkerhets grupper </br> ASE | 
| Dirigera utgående trafik från din webbapp | VNet-integrering och routningstabeller </br> ASE | 


### <a name="default-networking-behavior"></a>Standard nätverks beteende

Azure App Service skalnings enheter har stöd för många kunder i varje distribution. De kostnads fria och delade SKU-planerna är värd för kund arbets belastningar på flera klient arbetare. De grundläggande och högre planerna är värdar för kund arbets belastningar som endast är avsedda för en App Service plan. Om du har en standard App Service plan kommer alla appar i det schemat att köras på samma arbets tagare. Om du skalar ut arbets tagaren replikeras alla appar i den App Service plan på en ny arbets tagare för varje instans i App Service plan. 

#### <a name="outbound-addresses"></a>Utgående adresser

De virtuella datorerna i arbets gruppen är uppdelade i stor del av App Services planerna. De kostnads fria, delade, Basic-, standard-och Premium-planerna använder samma VM-typ för arbetare. PremiumV2-planen använder en annan VM-typ. PremiumV3 använder ännu en annan VM-typ. När du ändrar VM-serien får du en annan uppsättning utgående adresser. Om du skalar från standard till PremiumV2 kommer dina utgående adresser att ändras. Om du skalar från PremiumV2 till PremiumV3 kommer dina utgående adresser att ändras. I vissa äldre skalnings enheter ändras både inkommande och utgående adresser när du skalar från standard till PremiumV2. 

Det finns ett antal adresser som används för utgående samtal. De utgående adresser som används av din app för att göra utgående samtal visas i egenskaperna för din app. De här adresserna delas av alla appar som körs på samma VM-serie för arbetare i App Service distributionen. Om du vill se alla adresser som din app kan använda i en skalnings enhet, finns det en egenskap `possibleOutboundAddresses` med namnet som visar dem. 

![Skärm bild som visar egenskaper för appar.](media/networking-features/app-properties.png)

App Service har ett antal slut punkter som används för att hantera tjänsten.  De här adresserna publiceras i ett separat dokument och ingår också i `AppServiceManagement` IP-taggen. `AppServiceManagement`Taggen används endast i App Service miljöer där du behöver tillåta sådan trafik. App Service inkommande adresser spåras i `AppService` IP-tjänstetaggen. Det finns ingen IP-tjänstetagg som innehåller de utgående adresser som används av App Service. 

![Diagram som visar App Service inkommande och utgående trafik.](media/networking-features/default-behavior.png)

### <a name="app-assigned-address"></a>App-tilldelad adress 

Funktionen app-Assigned Address är en offshoot av den IP-baserade SSL-funktionen. Du kommer åt det genom att konfigurera SSL med din app. Du kan använda den här funktionen för IP-baserade SSL-anrop. Du kan också använda den för att ge din app en adress som bara innehåller den. 

![Diagram som illustrerar en app-tilldelad adress.](media/networking-features/app-assigned-address.png)

När du använder en app-tilldelad adress, går trafiken fortfarande genom samma klient dels roller som hanterar all inkommande trafik i App Service skalnings enhet. Men adressen som är tilldelad till din app används endast av din app. Användnings fall för den här funktionen:

* Stöd för IP-baserade SSL-behov för din app.
* Ange en dedikerad adress för din app som inte delas.

Information om hur du anger en adress i din app finns i [lägga till ett TLS/SSL-certifikat i Azure App Service][appassignedaddress]. 

### <a name="access-restrictions"></a>Åtkomst begränsningar 

Med åtkomst begränsningar kan du filtrera *inkommande* begär Anden. Filtrerings åtgärden utförs på de frontend-roller som är överordnade från arbets rollerna där dina appar körs. Eftersom klient dels rollerna är överordnade från arbets tagarna kan du tänka på åtkomst begränsningar som skydd på nätverks nivå för dina appar. 

Med den här funktionen kan du bygga en lista över regler för att tillåta och neka som utvärderas i prioritetsordning. Det liknar funktionen nätverks säkerhets grupp (NSG) i Azure-nätverk. Du kan använda den här funktionen i en ASE eller i tjänsten flera innehavare. När du använder det med en ILB-ASE eller en privat slut punkt kan du begränsa åtkomsten från privata adress block.
> [!NOTE]
> Upp till 512 åtkomst begränsnings regler kan konfigureras per app. 

![Diagram som illustrerar åtkomst begränsningar.](media/networking-features/access-restrictions.png)

#### <a name="ip-based-access-restriction-rules"></a>Regler för IP-baserad åtkomst begränsning

Funktionen IP-baserade åtkomst begränsningar hjälper när du vill begränsa de IP-adresser som kan användas för att nå din app. Det finns stöd för både IPv4 och IPv6. Några användnings fall för den här funktionen:
* Begränsa åtkomsten till din app från en uppsättning väldefinierade adresser. 
* Begränsa åtkomsten till trafik som kommer via en extern belastnings Utjämnings tjänst eller andra nätverks enheter med kända utgående IP-adresser. 

Information om hur du aktiverar den här funktionen finns i [Konfigurera åtkomst begränsningar][iprestrictions].

> [!NOTE]
> IP-baserade regler för åtkomst begränsning hanterar endast virtuella nätverks adress intervall när din app finns i en App Service-miljön. Om din app finns i tjänsten flera innehavare måste du använda [tjänstens slut punkter](../virtual-network/virtual-network-service-endpoints-overview.md) för att begränsa trafiken till att välja undernät i det virtuella nätverket.

#### <a name="access-restriction-rules-based-on-service-endpoints"></a>Åtkomst begränsnings regler baserade på tjänst slut punkter 

Med tjänst slut punkter kan du låsa *inkommande* åtkomst till din app så att käll adressen måste komma från en uppsättning undernät som du väljer. Den här funktionen fungerar tillsammans med begränsningar för IP-åtkomst. Tjänst slut punkter är inte kompatibla med fjärrfelsökning. Om du vill använda fjärrfelsökning med din app kan din klient inte finnas i ett undernät där tjänstens slut punkter är aktiverade. Processen för att ställa in tjänst slut punkter liknar processen för att ställa in begränsningar för IP-åtkomst. Du kan bygga en lista över tillåtna/nekade åtkomst regler som innehåller offentliga adresser och undernät i dina virtuella nätverk. 

Några användnings fall för den här funktionen:

* Konfigurera en Application Gateway med din app för att låsa inkommande trafik till din app.
* Begränsa åtkomsten till din app till resurser i det virtuella nätverket. Dessa resurser kan vara virtuella datorer, ASE eller till och med andra appar som använder VNet-integrering. 

![Diagram som illustrerar användningen av tjänst slut punkter med Application Gateway.](media/networking-features/service-endpoints-appgw.png)

Mer information om hur du konfigurerar tjänst slut punkter med din app finns i [Azure App Service åtkomst begränsningar][serviceendpoints].

#### <a name="access-restriction-rules-based-on-service-tags"></a>Regler för åtkomst begränsning baserat på tjänst Taggar

[Azure Service-Taggar][servicetags] är väl definierade uppsättningar IP-adresser för Azure-tjänster. Service tag-grupp de IP-intervall som används i olika Azure-tjänster och är ofta ytterligare begränsade till vissa regioner. På så sätt kan du filtrera *inkommande* trafik från vissa Azure-tjänster. 

En fullständig lista över taggar och mer information finns i länken för service tag ovan. Information om hur du aktiverar den här funktionen finns i [Konfigurera åtkomst begränsningar][iprestrictions].

#### <a name="http-header-filtering-for-access-restriction-rules"></a>Filtrering av HTTP-huvud för åtkomst begränsnings regler

För varje regel för begränsning av åtkomst kan du lägga till ytterligare http-huvudfiltrering. På så sätt kan du ytterligare kontrol lera inkommande begär Anden och filtrera utifrån vissa http-huvudvärden. Varje rubrik kan innehålla upp till 8 värden per regel. Följande lista över HTTP-rubriker stöds för närvarande: 
* X-vidarebefordrad – för
* X-vidarebefordrad-värd
* X-Azure-FDID
* X-FD-HealthProbe

Vissa användnings fall för HTTP-huvud filtrering är:
* Begränsa åtkomsten till trafik från proxyservrar som vidarebefordrar värd namnet
* Begränsa åtkomsten till en speciell instans av Azures frontend-dörr med en service tag-regel och begränsning för X-FDID-huvud

### <a name="private-endpoint"></a>Privat slutpunkt

Privat slut punkt är ett nätverks gränssnitt som ansluter dig privat och säkert till din webbapp via en privat Azure-länk. Privat slut punkt använder en privat IP-adress från det virtuella nätverket, vilket effektivt tar webb programmet till det virtuella nätverket. Den här funktionen är endast för *inkommande* flöden till din webbapp.
Mer information finns i [använda privata slut punkter för Azure Web App][privateendpoints].

Några användnings fall för den här funktionen:

* Begränsa åtkomsten till din app från resurser i ett virtuellt nätverk. 
* Exponera din app på en privat IP-adress i det virtuella nätverket. 
* Skydda din app med en WAF.

Privata slut punkter förhindrar data exfiltrering eftersom det enda du kan uppnå över den privata slut punkten är den app som den är konfigurerad med. 
 
### <a name="hybrid-connections"></a>Hybridanslutningar

App Service Hybridanslutningar gör det möjligt för dina appar att göra *utgående* anrop till angivna TCP-slutpunkter. Slut punkten kan vara lokal, i ett virtuellt nätverk eller var som helst som tillåter utgående trafik till Azure på port 443. Om du vill använda funktionen måste du installera en relay agent som kallas Hybridanslutningshanteraren på en Windows Server 2012 eller en nyare värd. Hybridanslutningshanteraren måste kunna uppnå Azure Relay på port 443. Du kan ladda ned Hybridanslutningshanteraren från App Service Hybridanslutningar gränssnittet i portalen. 

![Diagram som visar Hybridanslutningar nätverks flödet.](media/networking-features/hybrid-connections.png)

App Service Hybridanslutningar bygger på Azure Relay Hybridanslutningar-funktionen. App Service använder en specialiserad form av funktionen som endast stöder utgående samtal från din app till en TCP-värd och port. Denna värd och port behöver bara matcha den värd där Hybridanslutningshanteraren är installerat. 

När appen, i App Service, en DNS-sökning på värden och porten som definierats i din hybrid anslutning, omdirigeras trafiken automatiskt för att gå igenom hybrid anslutningen och från Hybridanslutningshanteraren. Mer information finns i [App Service hybridanslutningar][hybridconn].

Den här funktionen används ofta för att:

* Få åtkomst till resurser i privata nätverk som inte är anslutna till Azure med en VPN-eller ExpressRoute.
* Stöd för migrering av lokala appar till App Service utan att du behöver flytta stöd databaser.  
* Ge åtkomst med förbättrad säkerhet till en enda värd och port per hybrid anslutning. De flesta nätverksfunktioner har öppen åtkomst till ett nätverk. Med Hybridanslutningar kan du bara komma åt den enda värden och porten.
* Täck scenarier som inte omfattas av andra metoder för utgående anslutning.
* Utför utveckling i App Service på ett sätt som gör att apparna enkelt kan använda lokala resurser. 

Eftersom den här funktionen ger åtkomst till lokala resurser utan ett inkommande brand Väggs hål är det populär med utvecklare. De andra funktionerna för utgående App Service nätverk är relaterade till Azure Virtual Network. Hybridanslutningar är inte beroende av att gå igenom ett virtuellt nätverk. Den kan användas för en större mängd olika nätverks behov. 

Observera att App Service Hybridanslutningar inte är medveten om vad du gör på det. Så du kan använda den för att få åtkomst till en databas, en webb tjänst eller en godtycklig TCP-socket på en stordator. Funktionen i huvudsak tunnel TCP-paket. 

Hybridanslutningar är populär för utveckling, men den används också i produktions program. Det är bra att komma åt en webb tjänst eller databas, men det är inte lämpligt för situationer där flera anslutningar skapas. 

### <a name="gateway-required-vnet-integration"></a>Gateway – nödvändig VNet-integrering 

Gateway – krävs App Service VNet-integrering gör att din app kan göra *utgående* förfrågningar till ett virtuellt Azure-nätverk. Funktionen fungerar genom att ansluta värden som din app körs på till en Virtual Network gateway i det virtuella nätverket med hjälp av en punkt-till-plats-VPN. När du konfigurerar funktionen hämtar appen en av de punkt-till-plats-adresser som tilldelats varje instans. Med den här funktionen kan du komma åt resurser i antingen klassiska eller Azure Resource Manager virtuella nätverk i vilken region som helst. 

![Diagram som illustrerar Gateway-nödvändig VNet-integrering.](media/networking-features/gw-vnet-integration.png)

Den här funktionen löser problemet med att komma åt resurser i andra virtuella nätverk. Det kan även användas för att ansluta via ett virtuellt nätverk till antingen andra virtuella nätverk eller lokalt. Det fungerar inte med ExpressRoute virtuella nätverk, men det fungerar med plats-till-plats-VPN-anslutna nätverk. Det är vanligt vis olämpligt att använda den här funktionen från en app i en App Service-miljön (ASE) eftersom ASE redan finns i det virtuella nätverket. Användnings fall för den här funktionen:

* Få åtkomst till resurser i privata IP-adresser i dina virtuella Azure-nätverk. 
* Få åtkomst till resurser lokalt om det finns en plats-till-plats-VPN. 
* Få åtkomst till resurser i peer-kopplat virtuella nätverk. 

När den här funktionen är aktive rad använder appen den DNS-server som det virtuella mål nätverket är konfigurerat med. Mer information om den här funktionen finns i [App Service VNet-integrering][vnetintegrationp2s]. 

### <a name="vnet-integration"></a>VNET-integration

Gateway-nödvändig VNet-integrering är användbar, men det löser inte problemet med att komma åt resurser i ExpressRoute. Om du behöver komma åt över ExpressRoute-anslutningar måste apparna kunna ringa till tjänster som skyddas av tjänstens slut punkt. En annan VNet-integrations funktion kan uppfylla dessa behov. 

Med den nya funktionen för VNet-integrering kan du placera appens Server del i ett undernät i ett virtuellt Resource Manager-nätverk i samma region som din app. Den här funktionen är inte tillgänglig från en App Service-miljön, som redan finns i ett virtuellt nätverk. Användnings fall för den här funktionen:

* Få åtkomst till resurser i virtuella Resource Manager-nätverk i samma region.
* Åtkomst till resurser som skyddas med tjänst slut punkter. 
* Åtkomst till resurser som är tillgängliga via ExpressRoute eller VPN-anslutningar.
* Hjälp för att skydda all utgående trafik. 
* Tvinga tunnel all utgående trafik. 

![Diagram som illustrerar VNet-integrering.](media/networking-features/vnet-integration.png)

Läs mer i [App Service VNet-integrering][vnetintegration].

### <a name="app-service-environment"></a>App Service Environment 

En App Service-miljön (ASE) är en distribution av en enda klient av de Azure App Service som körs i det virtuella nätverket. Några exempel på sådana funktioner:

* Få åtkomst till resurser i det virtuella nätverket.
* Få åtkomst till resurser över ExpressRoute.
* Exponera dina appar med en privat adress i det virtuella nätverket. 
* Få åtkomst till resurser över tjänst slut punkter. 

Med en ASE behöver du inte använda funktioner som VNet-integrering eller tjänst slut punkter eftersom ASE redan finns i det virtuella nätverket. Om du vill komma åt resurser som SQL eller Azure Storage över tjänst slut punkter aktiverar du tjänst slut punkter i ASE-undernätet. Om du vill ha åtkomst till resurser i det virtuella nätverket behöver du inte göra någon ytterligare konfiguration. Om du vill få åtkomst till resurser över ExpressRoute är du redan i det virtuella nätverket och behöver inte konfigurera något på ASE eller apparna i det. 

Eftersom appar i en ILB-ASE kan exponeras på en privat IP-adress kan du enkelt lägga till WAF-enheter för att exponera enbart de appar som du vill ha på Internet och hålla resten säker. Den här funktionen kan hjälpa till att utveckla program på flera nivåer enklare. 

Vissa saker är för närvarande inte möjliga från tjänsten för flera innehavare, men är möjliga från en ASE. Här är några exempel:

* Exponera dina appar på en privat IP-adress.
* Skydda all utgående trafik med nätverks kontroller som inte är en del av din app.
* Vara värd för dina appar i en tjänst för en enda klient. 
* Skala upp till många fler instanser än vad som är möjligt i tjänsten för flera innehavare. 
* Läs in privata CA-klientcertifikat för användning av dina appar med privat CA-säkrade slut punkter.
* Framtvinga TLS 1,1 i alla appar som finns i systemet utan möjlighet att inaktivera det på App-nivå. 
* Tillhandahålla en dedikerad utgående adress för alla appar i din ASE som inte delas med kunderna. 

![Diagram som illustrerar en ASE i ett virtuellt nätverk.](media/networking-features/app-service-environment.png)

ASE ger den bästa artikeln runt isolerad och dedikerad app-värd, men det innebär vissa hanterings utmaningar. Några saker att tänka på innan du använder en fungerande ASE:
 
 * En ASE körs i det virtuella nätverket, men har beroenden utanför det virtuella nätverket. Dessa beroenden måste vara tillåtna. Mer information finns i [nätverks överväganden för en app service-miljön][networkinfo].
 * En ASE skalas inte direkt som tjänsten för flera innehavare. Du måste förutse skalnings behoven snarare än att skalas om. 
 * En ASE har en högre första kostnad. För att få ut mesta möjliga av din ASE bör du planera att placera många arbets belastningar i en ASE i stället för att använda dem för små ansträngningar.
 * Apparna i en ASE kan inte selektivt begränsa åtkomsten till vissa appar i ASE och inte andra.
 * En ASE finns i ett undernät och alla nätverks regler gäller för all trafik till och från den ASE. Använd åtkomst begränsningar om du vill tilldela regler för inkommande trafik för bara en app. 

## <a name="combining-features"></a>Kombinera funktioner 

De funktioner som anges för tjänsten för flera innehavare kan användas tillsammans för att lösa mer avancerade användnings fall. Två av de vanligaste användnings fallen beskrivs här, men de är bara exempel. Genom att förstå vad de olika funktionerna gör kan du möta nästan alla system arkitektur behov.

### <a name="place-an-app-into-a-virtual-network"></a>Placera en app i ett virtuellt nätverk

Du kanske undrar hur du ska lagra en app i ett virtuellt nätverk. Om du sätter din app i ett virtuellt nätverk finns inkommande och utgående slut punkter för appen i det virtuella nätverket. En ASE är det bästa sättet att lösa det här problemet. Men du kan möta de flesta av dina behov i tjänsten för flera innehavare genom att kombinera funktioner. Du kan till exempel vara värd för program med enbart intranät med privata inkommande och utgående adresser genom att:

* Skapa en Programgateway med privat inkommande och utgående adresser.
* Skydda inkommande trafik till din app med tjänstens slut punkter. 
* Använd den nya funktionen för VNet-integrering så att Server delen av din app finns i det virtuella nätverket. 

Det här distributions formatet ger dig inte en dedikerad adress för utgående trafik till Internet eller möjligheten att låsa all utgående trafik från din app. Det ger dig en stor del av vad du bara skulle kunna få i övrigt med en ASE. 

### <a name="create-multitier-applications"></a>Skapa program på flera nivåer

Ett program på flera nivåer är ett program där API-backend-apparna bara kan nås från front-end-nivån. Det finns två sätt att skapa ett program på flera nivåer. Börja med att använda VNet-integrering för att ansluta din frontend-webbapp till ett undernät i ett virtuellt nätverk. På så sätt kan din webbapp göra anrop till ditt virtuella nätverk. När din frontend-app är ansluten till det virtuella nätverket måste du bestämma hur du vill låsa åtkomsten till ditt API-program. Du kan:

* Var värd för både klient-och API-appen i samma ILB-ASE och exponerar frontend-appen på Internet med hjälp av en Programgateway.
* Värd för klient delen i tjänsten för flera innehavare och Server delen i en ILB-ASE.
* Värd för både klient delen och API-appen i tjänsten för flera innehavare.

Om du är värd för både klient delen och API-appen för ett program med flera nivåer kan du:

- Exponera ditt API-program med hjälp av privata slut punkter i det virtuella nätverket:

  ![Diagram som illustrerar användningen av privata slut punkter i en app med två nivåer.](media/networking-features/multi-tier-app-private-endpoint.png)

- Använd tjänstens slut punkter för att säkerställa att inkommande trafik till din API-app endast kommer från det undernät som används av din frontend-webbapp:

  ![Diagram som illustrerar användningen av tjänstens slut punkter för att skydda en app.](media/networking-features/multi-tier-app.png)

Här följer några överväganden som hjälper dig att bestämma vilken metod du ska använda:

* När du använder tjänst slut punkter behöver du bara skydda trafiken till din API-app till integrations under nätet. Detta hjälper till att skydda API-appen, men du kan fortfarande ha data exfiltrering från din frontend-app till andra appar i App Service.
* När du använder privata slut punkter har du två undernät i spelet, vilket ökar komplexiteten. Dessutom är den privata slut punkten en resurs på den översta nivån och lägger till hanterings kostnader. Fördelen med att använda privata slut punkter är att du inte har möjlighet att exfiltrering data. 

Antingen fungerar metoden med flera klient delar. I en liten skala är det enklare att använda tjänst slut punkter eftersom du bara aktiverar tjänstens slut punkter för API-appen i klient delens integrerings undernät. När du lägger till fler frontend-appar måste du justera varje API-app för att inkludera tjänstens slut punkter med integrations under nätet. När du använder privata slut punkter är det mer komplicerat, men du behöver inte ändra något i dina API-appar när du har angett en privat slut punkt. 

### <a name="line-of-business-applications"></a>Branschspecifika program

LOB-program (Line-of-Business) är interna program som normalt inte är tillgängliga för åtkomst från Internet. Dessa program anropas inifrån företags nätverk där åtkomsten kan kontrol leras strikt. Om du använder en ILB-ASE är det enkelt att vara värd för branschspecifika program. Om du använder tjänsten flera innehavare kan du antingen använda privata slut punkter eller använda tjänst slut punkter tillsammans med en Application Gateway. Det finns två orsaker till att använda en Programgateway med tjänst slut punkter i stället för att använda privata slut punkter:
* Du behöver WAF-skydd på dina LOB-appar.
* Du vill belastningsutjämna till flera instanser av dina LOB-appar.

Om inget av dessa krav gäller är det bättre att använda privata slut punkter. Med privata slut punkter som är tillgängliga i App Service kan du exponera dina appar på privata adresser i det virtuella nätverket. Den privata slut punkten du placerar i det virtuella nätverket kan nås via ExpressRoute och VPN-anslutningar. 

Om du konfigurerar privata slut punkter exponeras dina appar på en privat adress, men du måste konfigurera DNS för att komma åt adressen lokalt. För att den här konfigurationen ska fungera måste du vidarebefordra den Azure DNS privata zon som innehåller dina privata slut punkter till dina lokala DNS-servrar. Azure DNS privata zoner inte stöder zon vidarebefordran, men du kan stödja zon vidarebefordran genom att använda en DNS-server för detta ändamål. Med mallen [DNS-vidarebefordrare](https://azure.microsoft.com/resources/templates/301-dns-forwarder/) blir det enklare att vidarebefordra din Azure DNS privata zon till dina lokala DNS-servrar.

## <a name="app-service-ports"></a>App Service portar

Om du skannar App Service hittar du flera portar som exponeras för inkommande anslutningar. Det finns inget sätt att blockera eller kontrol lera åtkomsten till dessa portar i tjänsten för flera innehavare. Här är listan över exponerade portar:

| Användning | Port eller portar |
|----------|-------------|
|  HTTP/HTTPS  | 80, 443 |
|  Hantering | 454, 455 |
|  FTP-FTPS    | 21, 990, 10001-10020 |
|  Visual Studio Remote-felsökning  |  4020, 4022, 4024 |
|  Webb distributions tjänst | 8172 |
|  Infrastruktur användning | 7654, 1221 |

<!--Links-->
[appassignedaddress]: ./configure-ssl-certificate.md
[iprestrictions]: ./app-service-ip-restrictions.md
[serviceendpoints]: ./app-service-ip-restrictions.md
[hybridconn]: ./app-service-hybrid-connections.md
[vnetintegrationp2s]: ./web-sites-integrate-with-vnet.md
[vnetintegration]: ./web-sites-integrate-with-vnet.md
[networkinfo]: ./environment/network-info.md
[appgwserviceendpoints]: ./networking/app-gateway-with-service-endpoints.md
[privateendpoints]: ./networking/private-endpoint.md
[servicetags]: ../virtual-network/service-tags-overview.md