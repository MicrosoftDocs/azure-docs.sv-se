---
title: Nätverksöverväganden
description: Lär dig om nätverks trafiken i ASE och hur du ställer in nätverks säkerhets grupper och användardefinierade vägar med din ASE.
author: ccompy
ms.assetid: 955a4d84-94ca-418d-aa79-b57a5eb8cb85
ms.topic: article
ms.date: 07/27/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 91b6134e7c809a8af75aa1cf23523e352e0a1a0e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "95997349"
---
# <a name="networking-considerations-for-an-app-service-environment"></a>Nätverksöverväganden för en App Service-miljö #

## <a name="overview"></a>Översikt ##

 Azure [App Service-miljön][Intro] är en distribution av Azure App Service till ett undernät i ditt virtuella Azure-nätverk (VNet). Det finns två distributions typer för en App Services miljö (ASE):

- **Extern ASE**: exponerar ASE-appar på en IP-adress med Internet åtkomst. Mer information finns i [skapa en extern ASE][MakeExternalASE].
- **ILB ASE**: exponerar de ASE-värdbaserade apparna på en IP-adress i ditt VNet. Den interna slut punkten är en intern belastningsutjämnare (ILB), vilket är anledningen till att den kallas för ett ILB-ASE. Mer information finns i [skapa och använda en ILB-ASE][MakeILBASE].

Alla ASE, externa och ILB har en offentlig VIP som används för inkommande hanterings trafik och som från-adressen när du gör anrop från ASE till Internet. Anrop från en ASE som går till Internet lämnar VNet via den VIP som tilldelats för ASE. Den offentliga IP-adressen för denna VIP är käll-IP för alla anrop från ASE som går till Internet. Om apparna i din ASE gör anrop till resurser i ditt VNet eller via VPN, är käll-IP: en av IP-adresserna i under nätet som används av din ASE. Eftersom ASE är i det virtuella nätverket kan det också komma åt resurser inom VNet utan ytterligare konfiguration. Om det virtuella nätverket är anslutet till ditt lokala nätverk har appar i din ASE också åtkomst till resurser där utan ytterligare konfiguration.

![Extern ASE][1] 

Om du har en extern ASE är den offentliga VIP också den slut punkt som dina ASE-appar matchar till för:

* HTTP/S 
* FTP/S
* Webb distribution
* Fjärrfelsökning

![ILB ASE][2]

Om du har en ILB-ASE är adressen till ILB-adressen slut punkten för HTTP/S, FTP/S, webb distribution och fjärrfelsökning.

## <a name="ase-subnet-size"></a>ASE under näts storlek ##

Storleken på det undernät som används som värd för en ASE kan inte ändras efter att ASE har distribuerats.  ASE använder en adress för varje infrastruktur roll samt för varje isolerad App Service plan instans.  Det finns även fem adresser som används av Azure-nätverk för varje undernät som skapas.  En ASE som inte har några App Services planer kommer att använda 12 adresser innan du skapar en app.  Om det är en ILB-ASE använder den 13 adresser innan du skapar en app i den ASE. När du skalar dina ASE läggs infrastruktur roller till varje multipel av 15 och 20 av dina App Service plan instanser.

   > [!NOTE]
   > Inget annat kan finnas i under nätet, men ASE. Se till att välja ett adress utrymme som möjliggör framtida tillväxt. Du kan inte ändra den här inställningen senare. Vi rekommenderar en storlek på `/24` med 256 adresser.

När du skalar upp eller ned läggs nya roller i rätt storlek till och sedan migreras arbets belastningarna från den aktuella storleken till mål storleken. De ursprungliga virtuella datorerna togs bort först när arbets belastningarna har migrerats. Om du har en ASE med 100 ASP-instanser, kan det finnas en period där du behöver dubbla antalet virtuella datorer.  Det beror på att vi rekommenderar att du använder "/24" för att hantera eventuella ändringar som du kan behöva.  

## <a name="ase-dependencies"></a>ASE-beroenden ##

### <a name="ase-inbound-dependencies"></a>ASE inkommande beroenden ###

För att ASE ska fungera kräver ASE att följande portar är öppna:

| Användning | Från | Om du vill |
|-----|------|----|
| Hantering | App Service hanterings adresser | ASE-undernät: 454, 455 |
|  Intern kommunikation med ASE | ASE-undernät: alla portar | ASE-undernät: alla portar
|  Tillåt inkommande Azure Load Balancer | Azure-lastbalanserare | ASE-undernät: 16001

Det finns två andra portar som kan visas som öppna på en ports ökning, 7654 och 1221. De svarar med en IP-adress och inget annat. De kan blockeras om du vill. 

Inkommande hanterings trafik tillhandahåller kommando-och kontroll över ASE, förutom system övervakning. Käll adresserna för den här trafiken visas i dokumentet [ASE Management addresses][ASEManagement] . Nätverks säkerhets konfigurationen måste tillåta åtkomst från ASE-hanterings adresserna på portarna 454 och 455. Om du blockerar åtkomst från de här adresserna blir ASE felaktiga och pausas sedan. TCP-trafiken som kommer in på portarna 454 och 455 måste gå tillbaka från samma VIP eller så har du ett problem med asymmetrisk routning. 

I ASE-undernätet finns många portar som används för intern komponent kommunikation och de kan ändras. Detta kräver att alla portar i ASE-undernätet kan nås från ASE-undernätet. 

För kommunikationen mellan Azure Load Balancer och ASE-undernätet är de lägsta portarna som måste vara öppna 454, 455 och 16001. 16001-porten används för att föra Alive-trafik mellan belastningsutjämnaren och ASE. Om du använder en ILB-ASE kan du låsa trafiken ned till bara 454, 455, 16001 portar.  Om du använder en extern ASE måste du ta hänsyn till de vanliga portarna för åtkomst till appar.  

De andra portarna som du behöver för dig själv med är program portarna:

| Användning | Portar |
|----------|-------------|
|  HTTP/HTTPS  | 80, 443 |
|  FTP-FTPS    | 21, 990, 10001-10020 |
|  Visual Studio Remote-felsökning  |  4020, 4022, 4024 |
|  Webb distributions tjänst | 8172 |

Om du blockerar program portarna kan ASE fortfarande fungera, men appen kanske inte är det.  Om du använder tilldelade IP-adresser med en extern ASE måste du tillåta trafik från de IP-adresser som har tilldelats dina appar till ASE-undernätet på de portar som visas på sidan ASE Portal > IP-adresser.

### <a name="ase-outbound-dependencies"></a>ASE utgående beroenden ###

För utgående åtkomst är en ASE beroende av flera externa system. Många av dessa system beroenden definieras med DNS-namn och mappas inte till en fast uppsättning IP-adresser. Det innebär att ASE kräver utgående åtkomst från ASE-undernätet till alla externa IP-adresser över flera olika portar. 

ASE kommunicerar ut till Internet-tillgängliga adresser på följande portar:

| Användningar | Portar |
|-----|------|
| DNS | 53 |
| NTP | 123 |
| CRL, Windows-uppdateringar, Linux-beroenden, Azure-tjänster | 80/443 |
| Azure SQL | 1433 | 
| Övervakning | 12000 |

De utgående beroendena visas i det dokument som beskriver hur du [låser App Service-miljön utgående trafik](./firewall-integration.md). Om ASE förlorar åtkomsten till dess beroenden slutar den att fungera. När detta sker tillräckligt länge är ASE inaktiverat. 

### <a name="customer-dns"></a>Kund-DNS ###

Om VNet har kon figurer ATS med en kunddefinierad DNS-Server använder klient arbets belastningarna. ASE använder Azure DNS i hanterings syfte. Om det virtuella nätverket har kon figurer ATS med en kundvald DNS-server måste DNS-servern gå att gå att komma åt från det undernät som innehåller ASE.

Om du vill testa DNS-matchning från din webbapp kan du använda konsol kommandot *nameresolver*. Gå till fel söknings fönstret på SCM-platsen för din app eller gå till appen i portalen och välj konsol. I Shell-prompten kan du utfärda kommandot *nameresolver* tillsammans med det DNS-namn som du vill söka efter. Resultatet du får tillbaka är detsamma som vad appen skulle få när du utför samma sökning. Om du använder nslookup gör du en sökning med Azure DNS i stället.

Om du ändrar DNS-inställningen för det virtuella nätverk som din ASE finns i måste du starta om din ASE. För att undvika att starta om ASE, rekommenderar vi starkt att du konfigurerar dina DNS-inställningar för ditt VNet innan du skapar din ASE.  

<a name="portaldep"></a>

## <a name="portal-dependencies"></a>Portalberoenden ##

Förutom de ASE funktionella beroendena finns det några extra objekt relaterade till Portal upplevelsen. Några av funktionerna i Azure Portal är beroende av direkt åtkomst till _SCM-webbplatsen_. Det finns två URL: er för varje app i Azure App Service. Den första URL: en är för att komma åt din app. Den andra URL: en är åtkomst till SCM-webbplatsen, som även kallas _kudu-konsolen_. Funktioner som använder SCM-platsen är:

-   Webbjobb
-   Functions
-   Logg strömning
-   Kudu
-   Tillägg
-   Process Utforskaren
-   Konsol

När du använder en ILB-ASE går det inte att komma åt SCM-platsen utanför VNet. Vissa funktioner fungerar inte från App-portalen eftersom de behöver åtkomst till SCM-platsen för en app. Du kan ansluta till SCM-platsen direkt i stället för att använda portalen. 

Om din ILB-ASE är domän namnet *contoso.appserviceenvironment.net* och ditt app-namn är *testapp*, nås appen på *testapp.contoso.appserviceenvironment.net*. Den SCM-webbplats som hör till nås på *testapp.scm.contoso.appserviceenvironment.net*.

## <a name="ase-ip-addresses"></a>ASE IP-adresser ##

En ASE har några IP-adresser att vara medveten om. De är:

- **Offentlig inkommande IP-adress**: används för app-trafik i en extern ASE och hanterings trafik i både en extern ASE och en ILB-ASE.
- **Utgående offentlig IP-adress**: används som "från"-IP för utgående anslutningar från ASE som lämnar det virtuella nätverket, som inte dirigeras nedåt i en VPN.
- **ILB IP-adress**: IP-adressen ILB finns bara i en ILB-ASE.
- **App-tilldelade IP-baserade SSL-adresser**: endast möjligt med en extern ASE och när IP-baserad SSL har kon figurer ATS.

Alla dessa IP-adresser visas i Azure Portal från användar gränssnittet för ASE. Om du har en ILB-ASE visas IP-adressen för ILB.

   > [!NOTE]
   > De här IP-adresserna ändras inte så länge din ASE håller på att vara igång.  Om din ASE blir pausad och återställs, kommer de adresser som används av din ASE att ändras. Den normala orsaken till att en ASE blir pausad är om du blockerar inkommande hanterings åtkomst eller blockerar åtkomsten till ett ASE-beroende. 

![IP-adresser][3]

### <a name="app-assigned-ip-addresses"></a>App-tilldelade IP-adresser ###

Med en extern ASE kan du tilldela IP-adresser till enskilda appar. Du kan inte göra det med en ILB-ASE. Mer information om hur du konfigurerar din app så att den har sin egen IP-adress finns i [skydda ett anpassat DNS-namn med en TLS/SSL-bindning i Azure App Service](../configure-ssl-bindings.md).

När en app har sin egen IP-baserade SSL-adress reserverar ASE två portar för att mappa till den IP-adressen. En port är för HTTP-trafik och den andra porten är för HTTPS. Dessa portar anges i ASE-ANVÄNDARGRÄNSSNITTET i avsnittet IP-adresser. Trafiken måste kunna nå dessa portar från VIP: en eller så är apparna inte tillgängliga. Detta krav är viktigt att komma ihåg när du konfigurerar nätverks säkerhets grupper (NSG: er).

## <a name="network-security-groups"></a>Nätverkssäkerhetsgrupper ##

[Nätverks säkerhets grupper][NSGs] ger möjlighet att styra nätverks åtkomst i ett VNet. När du använder portalen finns det en implicit neka-regel som lägst prioritet för att neka allt. Det du skapar är dina tillåtna regler.

I en ASE har du inte åtkomst till de virtuella datorer som används som värd för själva ASE. De är i en Microsoft-hanterad prenumeration. Om du vill begränsa åtkomsten till apparna på ASE anger du NSG: er i ASE-undernätet. När du gör detta bör du vara noggrann med ASE-beroendena. Om du blockerar eventuella beroenden slutar ASE att fungera.

NSG: er kan konfigureras via Azure Portal eller via PowerShell. Informationen här visar Azure Portal. Du skapar och hanterar NSG: er i portalen som en resurs på den översta nivån under **nätverk**.

De obligatoriska posterna i en NSG, för att en ASE ska fungera, är att tillåta trafik:

**Inkommande**
* TCP från IP-AppServiceManagement på portarna 454 455
* TCP från belastningsutjämnaren på port 16001
* från ASE-undernätet till ASE-undernätet på alla portar

**Utgående**
* UDP till alla IP-adresser på port 53
* UDP till alla IP-adresser på port 123
* TCP till alla IP-adresser på portarna 80, 443
* TCP till IP-taggen `Sql` på portarna 1433
* TCP till alla IP-adresser på port 12000
* till ASE-undernätet på alla portar

Dessa portar omfattar inte de portar som dina appar behöver för att kunna användas. Din app kan till exempel behöva anropa en MySQL-server på port 3306. Network Time Protocol (NTP) på port 123 är det tidssynkroniserings protokoll som används av operativ systemet. NTP-slutpunkterna är inte speciella för App Services, kan variera med operativ systemet och inte i en väldefinierad lista med adresser. För att förhindra synkroniseringsproblem måste du tillåta UDP-trafik till alla adresser på port 123. Utgående TCP till port 12000-trafik är för system support och-analys. Slut punkterna är dynamiska och är inte i en väl definierad uppsättning adresser.

De normala port åtkomst portarna är:

| Användning | Portar |
|----------|-------------|
|  HTTP/HTTPS  | 80, 443 |
|  FTP-FTPS    | 21, 990, 10001-10020 |
|  Visual Studio Remote-felsökning  |  4020, 4022, 4024 |
|  Webb distributions tjänst | 8172 |

När de inkommande och utgående kraven tas i beaktande bör NSG: er se ut ungefär som NSG: er som visas i det här exemplet. 

![Ingående säkerhetsregler][4]

En standard regel gör att IP-adresser i det virtuella nätverket kan kommunicera med ASE-undernätet. En annan standard regel aktiverar belastningsutjämnaren, även kallat offentlig VIP, för att kommunicera med ASE. Om du vill se standard reglerna väljer du **standard regler** bredvid ikonen **Lägg till** . Om du anger en neka alla Else-regler innan standard reglerna förhindras trafik mellan VIP-och ASE. Om du vill förhindra trafik som kommer inifrån VNet lägger du till en egen regel för att tillåta inkommande. Använd en källa som är lika med AzureLoadBalancer med målet **och ett** port intervall för **\*** . Eftersom NSG-regeln används för ASE-undernätet behöver du inte vara särskilt i målet.

Om du har tilldelat en IP-adress till din app, se till att du behåller portarna öppna. Om du vill se portarna väljer du **App Service-miljön**  >  **IP-adresser**.  

Alla objekt som visas i följande utgående regler behövs, förutom det sista objektet. De ger nätverks åtkomst till ASE-beroenden som nämnts tidigare i den här artikeln. Om du blockerar någon av dem slutar ASE att fungera. Det sista objektet i listan gör att ASE kan kommunicera med andra resurser i ditt VNet.

![Säkerhetsregler för utgående trafik][5]

När dina NSG: er har definierats tilldelar du dem till det undernät som din ASE är på. Om du inte kommer ihåg ASE VNet eller under nätet kan du se det från ASE Portal-sidan. Om du vill tilldela NSG till ditt undernät går du till under nätets användar gränssnitt och väljer NSG.

## <a name="routes"></a>Vägar ##

Tvingad tunnel trafik är när du ställer in vägar i ditt VNet så att utgående trafik inte går direkt till Internet, utan någon annan som en ExpressRoute gateway eller en virtuell installation.  Om du behöver konfigurera ASE på ett sådant sätt kan du läsa dokumentet om hur du [konfigurerar app service-miljön med Tvingad tunnel trafik][forcedtunnel].  I det här dokumentet får du veta vilka alternativ som finns tillgängliga för att arbeta med ExpressRoute och Tvingad tunnel trafik.

När du skapar en ASE i portalen skapar vi också en uppsättning routningstabeller i under nätet som skapas med ASE.  De vägarna säger bara att du skickar utgående trafik direkt till Internet.  
Följ dessa steg om du vill skapa samma vägar manuellt:

1. Gå till Azure-portalen. Välj **nätverks**  >  **väg tabeller**.

2. Skapa en ny routningstabell i samma region som ditt VNet.

3. I användar gränssnittet för din routningstabell väljer du **vägar**  >  **Lägg till**.

4. Ange **nästa hopp typ** till **Internet** och **adressprefixet** till **0.0.0.0/0**. Välj **Spara**.

    Sedan ser du något som liknar följande:

    ![Funktionella vägar][6]

5. När du har skapat den nya routningstabellen går du till det undernät som innehåller din ASE. Välj routningstabellen från listan i portalen. När du har sparat ändringen bör du se NSG: er och vägarna som antecknas med ditt undernät.

    ![NSG: er och vägar][7]

## <a name="service-endpoints"></a>Tjänstslutpunkter ##

Med tjänstens slutpunkter kan du begränsa åtkomsten för tjänster med flera innehavare till en uppsättning virtuella Azure-nätverk och undernät. Du kan läsa mer om tjänstslutpunkter i dokumentationen [Tjänstslutpunkter för virtuellt nätverk][serviceendpoints]. 

När du aktiverar tjänstens slutpunkter för en resurs, finns det vägar som skapats med högre prioritet än andra vägar. Om du använder tjänst slut punkter på en Azure-tjänst, med en Tvingad tunnel ASE, kommer trafiken till dessa tjänster inte att tvingas tunnel trafik. 

När tjänstens slutpunkter är aktiverade på ett undernät med en Azure SQL-instans, måste alla Azure SQL-instanser som är anslutna från undernätet ha aktiverat tjänstens slutpunkter. Om du vill ha åtkomst till flera Azure SQL-instanser från samma undernät kan du inte aktivera tjänstens slutpunkter på en Azure SQL-instans och inte på en annan. Ingen annan Azure-tjänst fungerar som Azure SQL med avseende på tjänst slut punkter. När du aktiverar tjänstens slutpunkter med Azure Storage kan du låsa åtkomsten till resursen från undernätet, men du kan ändå använda andra Azure Storage-konton även om de inte har aktiverat tjänstens slutpunkter.  

![Tjänstslutpunkter][8]

<!--Image references-->
[1]: ./media/network_considerations_with_an_app_service_environment/networkase-overflow.png
[2]: ./media/network_considerations_with_an_app_service_environment/networkase-overflow2.png
[3]: ./media/network_considerations_with_an_app_service_environment/networkase-ipaddresses.png
[4]: ./media/network_considerations_with_an_app_service_environment/networkase-inboundnsg.png
[5]: ./media/network_considerations_with_an_app_service_environment/networkase-outboundnsg.png
[6]: ./media/network_considerations_with_an_app_service_environment/networkase-udr.png
[7]: ./media/network_considerations_with_an_app_service_environment/networkase-subnet.png
[8]: ./media/network_considerations_with_an_app_service_environment/serviceendpoint.png

<!--Links-->
[Intro]: ./intro.md
[MakeExternalASE]: ./create-external-ase.md
[MakeASEfromTemplate]: ./create-from-template.md
[MakeILBASE]: ./create-ilb-ase.md
[ASENetwork]: ./network-info.md
[UsingASE]: ./using-an-ase.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/network-security-groups-overview.md
[ConfigureASEv1]: app-service-web-configure-an-app-service-environment.md
[ASEv1Intro]: app-service-app-service-environment-intro.md
[mobileapps]: /previous-versions/azure/app-service-mobile/app-service-mobile-value-prop
[Functions]: ../../azure-functions/index.yml
[Pricing]: https://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/management/overview.md
[ConfigureSSL]: ../configure-ss-cert.md
[Kudu]: https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../web-application-firewall/ag/ag-overview.md
[ASEManagement]: ./management-addresses.md
[serviceendpoints]: ../../virtual-network/virtual-network-service-endpoints-overview.md
[forcedtunnel]: ./forced-tunnel-support.md
[serviceendpoints]: ../../virtual-network/virtual-network-service-endpoints-overview.md