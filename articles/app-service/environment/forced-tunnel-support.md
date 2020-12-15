---
title: Konfigurera tvingad tunneltrafik
description: Lär dig hur du aktiverar App Service-miljön att fungera när utgående trafik tvingas tunnel trafik i det virtuella nätverket.
author: ccompy
ms.assetid: 384cf393-5c63-4ffb-9eb2-bfd990bc7af1
ms.topic: quickstart
ms.date: 05/29/2018
ms.author: ccompy
ms.custom: mvc, seodec18
ms.openlocfilehash: 95a4d00a27a0da363561f469b4c5e9e2ad16463c
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/15/2020
ms.locfileid: "97510506"
---
# <a name="configure-your-app-service-environment-with-forced-tunneling"></a>Konfigurera App Service Environment med tvingande dirigering

Azure App Service Environment (ASE) är en distribution av Azure App Service i en kunds virtuella Azure-nätverk. Många kunder konfigurerar sina virtuella Azure-nätverk så att de blir förlängningar av de lokala nätverken med VPN eller Azure ExpressRoute-anslutningar. Tvingad tunneltrafik är när du omdirigerar Internet-bunden trafik till ditt VPN eller en virtuell installation i stället. Virtuella installationer används ofta vid inspektion och granskning av utgående nätverkstrafik. 

ASE har ett antal externa beroenden som beskrivs i dokumentet [Nätverksarkitektur i App Service Environment][network]. Normalt måste all utgående ASE-beroende trafik gå igenom den VIP som har etablerats med ASE:n. Om du ändrar routningen för trafik till eller från ASE:n utan att följa informationen nedan, kommer din ASE att sluta fungera.

I ett virtuellt Azure-nätverk sker routning baserat på den längsta prefix-matchningen (LPM). Om det finns fler än en väg med samma LPM-matchning väljs en väg baserat på dess ursprung i följande ordning:

* Användardefinierad väg (UDR)
* BGP-väg (när ExpressRoute används)
* Systemväg

Mer information om routning i ett virtuellt nätverk finns i [Användardefinierade vägar och IP-vidarebefordring][routes]. 

Om du vill dirigera din utgående ASE-trafik någon annanstans än direkt till Internet, kan du välja mellan följande:

* Aktivera din ASE till direkt Internet-åtkomst
* Konfigurera ditt ASE-undernät så att BGP-vägar ignoreras
* Konfigurera att ditt ASE-undernät använder tjänstens slutpunkter till Azure SQL och Azure Storage
* Lägg till egna IP-adresser i ASE Azure SQL-brandväggen

## <a name="enable-your-app-service-environment-to-have-direct-internet-access"></a>Aktivera App Service Environment-miljön så att den har direktåtkomst till internet

Om du vill aktivera att din ASE går direkt till Internet, även om ditt virtuella Azure-nätverket har konfigurerats med ExpressRoute, kan du:

* Konfigurera ExpressRoute att annonsera 0.0.0.0/0. Som standard dirigeras all utgående trafik lokalt.
* Skapa en UDR med adressprefixet 0.0.0.0/0, samt en nexthop-typ för Internet och tillämpa det på ASE-undernätet.

Om du gör dessa två ändringar kommer trafik med Internet som mål som kommer från undernätet i App Service Environment inte tvingas till ExpressRoute-anslutningen.

Om nätverket redan dirigerar trafik lokalt måste du skapa det undernät som ska vara värd för ASE och konfigurera UDR för det innan du distribuerar ASE.  

> [!IMPORTANT]
> Vägarna som definieras i en UDR måste vara tillräckligt specifika för att få företräde framför eventuella vägar som annonseras av ExpressRoute-konfigurationen. Föregående exempel använder det breda adressintervallet 0.0.0.0/0. Det kan eventuellt åsidosättas av misstag av vägannonseringar som använder mer specifika adressintervall.
>
> App Service Environment-miljöer stöds inte med ExpressRoute-konfigurationer som korsannonserar vägar från den offentliga peering-vägen till den privata peering-vägen. ExpressRoute-konfigurationer med offentlig peering har konfigurerats för att få vägannonseringar från Microsoft. Reklamen innehåller ett stort antal adressintervall för Microsoft Azure. Om adressintervallen korsannonseras på den privata peering-vägen kommer alla utgående nätverkspaket från undernätet för App Service Environment att dirigeras till en kunds lokala nätverksinfrastruktur. Det här nätverksflödet stöds inte som standard i App Service Environment. En lösning på detta problem är att stoppa korsannonsering av vägar från den offentliga peering-vägen till den privata peering-vägen. En annan lösning är att göra det möjligt för App Service Environment att arbeta i en konfiguration med tvingande dirigering.

![Direkt Internet-åtkomst][1]

## <a name="configure-your-ase-subnet-to-ignore-bgp-routes"></a>Konfigurera ditt ASE-undernät så att BGP-vägar ignoreras ## 

Du kan konfigurera ditt ASE-undernät så att alla BGP-vägar ignoreras.  När ASE har konfigurerats att ignorera BGP-vägar kan det komma åt sina beroenden utan problem.  Du måste dock skapa UDR:er så att apparna kan komma åt lokala resurser.

Så här konfigurerar du ditt ASE-undernät så att BGP-vägar ignoreras:

* Skapa en UDR och tilldela den till ditt ASE-undernät om du inte redan har en.
* Öppna Azure Portal och öppna gränssnittet för routningstabellen som tilldelats till ASE-undernätet.  Välj Konfiguration.  Ange väg spridning för virtuell nätverks-Gateway till inaktive rad.  Klicka på Spara. Inaktiveringen är dokumenterad i dokumentet [Skapa en routningstabell][routetable].

När du har konfigurerat ASE-undernätet att ignorera alla BGP-vägar kan dina appar inte längre nå resurser lokalt. Om du vill göra så att apparna kan komma åt resurser lokalt redigerar du UDR som tilldelats till ASE-underlätet och lägger till vägar för dina lokala adressintervall. Nästa hopptyp bör ställas in som Virtuell nätverksgateway. 


## <a name="configure-your-ase-with-service-endpoints"></a>Konfigurera din ASE med tjänstens slutpunkter ##

Utför följande steg för att dirigera all utgående trafik från din ASE, förutom den som går till Azure SQL och Azure Storage:

1. Skapa en routningstabell och tilldela den till ditt ASE-undernät. Hitta adresserna som matchar din region här [Hanteringsadresser för App Service Environment][management]. Skapa vägar för dessa adresser med ett nexthop för Internet. De här vägarna behövs eftersom App Service Environments inkommande hanteringstrafik måste svara från samma adress som den skickades till.   

2. Aktivera tjänstslutpunkter med Azure SQL och Azure Storage med ASE-undernätet.  När det här steget har slutförts kan du konfigurera ditt virtuella nätverk med tvingad tunneltrafik.

Mer information om hur du distribuerar ASE med en mall finns i [Skapa en App Service-miljö med hjälp av en mall][template].

Med tjänstens slutpunkter kan du begränsa åtkomsten för tjänster med flera innehavare till en uppsättning virtuella Azure-nätverk och undernät. Du kan läsa mer om tjänstslutpunkter i dokumentationen [Tjänstslutpunkter för virtuellt nätverk][serviceendpoints]. 

När du aktiverar tjänstens slutpunkter för en resurs, finns det vägar som skapats med högre prioritet än andra vägar. Om du använder tjänstens slutpunkter med tvingad tunneltrafik för ASE, kommer hanteringstrafiken för Azure SQL och Azure Storage inte omfattas av den tvingade tunneltrafiken. Annan ASE-beroende trafik blir tvingad tunneltrafik och kan antingen förloras, eller så fungerar inte ASE ordentligt.

När tjänstens slutpunkter är aktiverade på ett undernät med en Azure SQL-instans, måste alla Azure SQL-instanser som är anslutna från undernätet ha aktiverat tjänstens slutpunkter. Om du vill ha åtkomst till flera Azure SQL-instanser från samma undernät kan du inte aktivera tjänstens slutpunkter på en Azure SQL-instans och inte på en annan.  Azure Storage fungerar inte på samma sätt som Azure SQL.  När du aktiverar tjänstens slutpunkter med Azure Storage kan du låsa åtkomsten till resursen från undernätet, men du kan ändå använda andra Azure Storage-konton även om de inte har aktiverat tjänstens slutpunkter.  

Om du konfigurerar tvingad tunneltrafik med en nätverksfilterinstallation måste du komma ihåg att ASE har ytterligare beroenden utöver Azure SQL och Azure Storage. Om trafik till dessa beroenden blockeras fungerar inte ASE korrekt.

![Tvingad tunneltrafik med tjänstens slutpunkter][2]

## <a name="add-your-own-ips-to-the-ase-azure-sql-firewall"></a>Lägg till egna IP-adresser i ASE Azure SQL-brandväggen ##

Utför följande steg för att tunnla all utgående trafik från din ASE, förutom den som går till Azure SQL och Azure Storage:

1. Skapa en routningstabell och tilldela den till ditt ASE-undernät. Hitta adresserna som matchar din region här [Hanteringsadresser för App Service Environment][management]. Skapa vägar för dessa adresser med ett nexthop för Internet. De här vägarna behövs eftersom App Service Environments inkommande hanteringstrafik måste svara från samma adress som den skickades till. 

2. Aktivera tjänstens slutpunkter för Azure Storage med ditt ASE-undernät

3. Hämta de adresser som ska användas för all utgående trafik från din App Service Environment till Internet. Om du dirigerar trafiken lokalt är dessa adresser dina NAT- eller gateway-IP-adresser. Om du vill dirigera den utgående trafiken för App Service Environment genom en NVA är den utgående adressen den offentliga IP-adressen för NVA.

4. _Ange utgående adresser i en befintlig App Service-miljön:_ Gå till resources.azure.com och gå till Subscription/ \<subscription id> /ResourceGroups/ \<ase resource group> /providers/Microsoft.Web/hostingEnvironments/ \<ase name> . Du kan då se den JSON som beskriver App Service Environment. Kontrollera att det står **läsa/skriva** längst upp. Välj **Redigera**. Gå längst ned på sidan. Ändra värdet i **userWhitelistedIpRanges** från **null** till något som liknar följande. Använd de adresser som du vill ange som utgående adressintervall. 

    ```json
    "userWhitelistedIpRanges": ["11.22.33.44/32", "55.66.77.0/24"]
    ```

   Välj **PUT** längst upp. Det här alternativet utlöser en skalningsåtgärd i App Service Environment och justerar brandväggen.

_Skapa din ASE med utgående adresser_: Följ instruktionerna i [Skapa en App Service Environment med en mall][template] och hämta lämplig mall.  Redigera avsnittet ”resurser” i filen azuredeploy.json, men inte i blocket ”egenskaper”, och inkludera en rad för **userWhitelistedIpRanges** med dina värden.

```json
"resources": [
    {
        "apiVersion": "2015-08-01",
        "type": "Microsoft.Web/hostingEnvironments",
        "name": "[parameters('aseName')]",
        "kind": "ASEV2",
        "location": "[parameters('aseLocation')]",
        "properties": {
            "name": "[parameters('aseName')]",
            "location": "[parameters('aseLocation')]",
            "ipSslAddressCount": 0,
            "internalLoadBalancingMode": "[parameters('internalLoadBalancingMode')]",
            "dnsSuffix" : "[parameters('dnsSuffix')]",
            "virtualNetwork": {
                "Id": "[parameters('existingVnetResourceId')]",
                "Subnet": "[parameters('subnetName')]"
            },
            "userWhitelistedIpRanges":  ["11.22.33.44/32", "55.66.77.0/30"]
        }
    }
]
```

Dessa ändringar skickar trafik till Azure Storage direkt från ASE:n och tillåter åtkomst till Azure SQL från fler adresser än VIP för ASE.

   ![Tvingad tunnel med SQL-tillåten][3]

## <a name="preventing-issues"></a>Förhindra problem ##

Om kommunikationen mellan ASE och dess beroenden är bruten, hamnar ASE:n i ett feltillstånd.  Om felet fortgår för länge inaktiveras ASE:n. Om du vill aktivera ASE:n igen följer du instruktionerna i ASE-portalen.

Förutom att bryta kommunikationen kan du försämra din ASE genom att ha för lång svarstid. För lång svarstid kan uppstå om din ASE är för långt ifrån ditt lokala nätverk.  Avståndet är till exempel för långt om kommunikationen måste gå över ett hav eller en kontinent för att nå det lokala nätverket. Svarstid kan också påverkas vid överbelastning av intranätet eller utgående bandbreddsbegränsningar.


<!--IMAGES-->
[1]: ./media/forced-tunnel-support/asedependencies.png
[2]: ./media/forced-tunnel-support/forcedtunnelserviceendpoint.png
[3]: ./media/forced-tunnel-support/forcedtunnelexceptstorage.png

<!--Links-->
[management]: ./management-addresses.md
[network]: ./network-info.md
[routes]: ../../virtual-network/virtual-networks-udr-overview.md
[template]: ./create-from-template.md
[serviceendpoints]: ../../virtual-network/virtual-network-service-endpoints-overview.md
[routetable]: ../../virtual-network/manage-route-table.md#create-a-route-table
