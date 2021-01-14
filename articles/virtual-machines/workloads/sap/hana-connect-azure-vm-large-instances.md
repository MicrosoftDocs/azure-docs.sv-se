---
title: Anslutnings konfiguration från virtuella datorer till SAP HANA på Azure (stora instanser) | Microsoft Docs
description: Anslutnings inställningar från virtuella datorer för att använda SAP HANA på Azure (stora instanser).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/25/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: af248b4ee3ccd079d41317fdb1db3de82ad0f835
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2021
ms.locfileid: "98199044"
---
# <a name="connecting-azure-vms-to-hana-large-instances"></a>Ansluta virtuella Azure-datorer till HANA – stora instanser

Artikeln [Vad är SAP HANA på Azure (stora instanser)?](./hana-overview-architecture.md) nämner att den minimala distributionen av HANA-stora instanser med SAP-programlagret i Azure ser ut så här:

![Azure VNet är anslutet till SAP HANA på Azure (stora instanser) och lokalt](./media/hana-overview-architecture/image1-architecture.png)

Titta närmare på Azure Virtual Network-sidan, det finns ett behov av:

- Definitionen av ett virtuellt Azure-nätverk som du ska distribuera de virtuella datorerna i SAP-program skiktet till.
- Definitionen av ett standard-undernät i det virtuella Azure-nätverket som verkligen är det som de virtuella datorerna distribueras till.
- Det virtuella Azure-nätverket som skapas måste ha minst ett virtuellt dator under nät och ett Azure ExpressRoute-undernät för virtuell nätverksgateway. Dessa undernät ska tilldelas de IP-adressintervall som anges och beskrivs i följande avsnitt.


## <a name="create-the-azure-virtual-network-for-hana-large-instances"></a>Skapa det virtuella Azure-nätverket för HANA-stora instanser

>[!Note]
>Det virtuella Azure-nätverket för HANA-stora instanser måste skapas med hjälp av Azure Resource Manager distributions modell. Den äldre Azure-distributions modellen, som ofta kallas för den klassiska distributions modellen, stöds inte av lösningen HANA stor instans.

Du kan använda Azure Portal, PowerShell, en Azure-mall eller Azure CLI för att skapa det virtuella nätverket. (Mer information finns i [skapa ett virtuellt nätverk med hjälp av Azure Portal](../../../virtual-network/manage-virtual-network.md#create-a-virtual-network)). I följande exempel tittar vi på ett virtuellt nätverk som har skapats med hjälp av Azure Portal.

När du refererar till **adress utrymmet** i den här dokumentationen, till det adress utrymme som det virtuella Azure-nätverket kan använda. Det här adress utrymmet är också det adress intervall som det virtuella nätverket använder för att sprida BGP-vägar. Det här **adress utrymmet** kan visas här:

![Adress utrymmet för ett virtuellt Azure-nätverk som visas i Azure Portal](./media/hana-overview-connectivity/image1-azure-vnet-address-space.png)

I föregående exempel med 10.16.0.0/16 angavs det virtuella Azure-nätverket i stället för ett stort och brett IP-adressintervall som ska användas. Därför kan alla IP-adressintervall för efterföljande undernät i det här virtuella nätverket ha sina intervall inom det adress utrymmet. Vi rekommenderar vanligt vis inte ett stort adress intervall för enskilda virtuella nätverk i Azure. Men vi ska titta på de undernät som har definierats i det virtuella Azure-nätverket:

![Undernät för virtuella nätverk i Azure och deras IP-adressintervall](./media/hana-overview-connectivity/image2b-vnet-subnets.png)

Vi tittar på ett virtuellt nätverk med ett första VM-undernät (här kallas "standard") och ett undernät med namnet "GatewaySubnet".

I de två föregående bilderna täcker det **virtuella nätverkets adress utrymme** både **under nätets IP-adressintervall för den virtuella Azure-datorn** och den virtuella Nätverksgatewayen.

Du kan begränsa det **virtuella nätverkets adress utrymme** till de angivna intervallen som används av varje undernät. Du kan också definiera det **virtuella nätverkets adress utrymme** i ett virtuellt nätverk som flera olika intervall, som du ser här:

![Adress utrymme för virtuella Azure-nätverk med två blank steg](./media/hana-overview-connectivity/image3-azure-vnet-address-space_alternate.png)

I det här fallet har det **virtuella nätverkets adress utrymme** två definierade utrymmen. De är samma som de IP-adressintervall som definieras för IP-adressintervallet för under nätet för den virtuella Azure-datorn och den virtuella Nätverksgatewayen. 

Du kan använda valfri namngivnings standard som för dessa klient under nät (VM-undernät). **Det måste dock alltid vara ett, och endast ett Gateway-undernät för varje virtuellt nätverk** som ansluter till SAP HANA på Azure (stora instanser) ExpressRoute-kretsen. **Det här Gateway-undernätet måste ha namnet "GatewaySubnet"** för att säkerställa att ExpressRoute-gatewayen är korrekt placerad.

> [!WARNING] 
> Det är viktigt att Gateway-undernätet alltid heter "GatewaySubnet".

Du kan använda flera virtuella dator under nät och icke sammanhängande adress intervall. Dessa adress intervall måste täckas av det virtuella nätverkets **adress utrymme** . De kan vara i ett sammanställt formulär. De kan också finnas i en lista med de exakta intervallen för de virtuella dator under näten och gateway-undernätet.

Nedan följer en sammanfattning av viktiga fakta om ett virtuellt Azure-nätverk som ansluter till HANA-stora instanser:

- Du måste skicka det **virtuella nätverkets adress utrymme** till Microsoft när du utför en första distribution av Hana-stora instanser. 
- Det **virtuella nätverkets adress utrymme** kan vara ett större intervall som täcker intervallen för både under nätets IP-adressintervall för den virtuella Azure-datorn och den virtuella Nätverksgatewayen.
- Eller så kan du skicka flera intervall som tar upp de olika IP-adressintervall för virtuella IP-adressintervall och IP-adressintervallet för den virtuella nätverks-gatewayen.
- Det definierade **virtuella nätverkets adress utrymme** används för spridning av BGP-routning.
- Namnet på Gateway-undernätet måste vara: **"GatewaySubnet"**.
- Adress utrymmet används som ett filter på den stora instans sidan i HANA för att tillåta eller neka trafik till de många HANA-instans enheter från Azure. BGP-routningsinformationen för det virtuella Azure-nätverket och de IP-adressintervall som har kon figurer ATS för filtrering på den stora instans sidan i HANA ska matcha. I annat fall kan det uppstå anslutnings problem.
- Det finns vissa detaljer om Gateway-undernätet som diskuteras senare, i avsnittet **ansluta ett virtuellt nätverk till Hana stor instans ExpressRoute.**



## <a name="different-ip-address-ranges-to-be-defined"></a>Olika IP-adressintervall som ska definieras 

Några av de IP-adressintervall som krävs för att distribuera HANA stora instanser introducerades redan. Men det finns även fler IP-adressintervall som är viktiga. Inte alla följande IP-adressintervall måste skickas till Microsoft. Du måste dock definiera dem innan du skickar en begäran om inledande distribution:

- **Adress utrymme för virtuellt nätverk**: det virtuella nätverkets **adress utrymme** är de IP-adressintervall som du tilldelar din adress utrymmes parameter i virtuella Azure-nätverk. Dessa nätverk ansluter till SAP HANA stor instans miljö. Vi rekommenderar att den här adress utrymmes parametern är ett värde med flera rader. Det bör bestå av under näts intervallet för den virtuella Azure-datorn och under nätets intervall i Azure-gatewayen. Det här under näts intervallet visades i föregående bild. Det får inte överlappa din lokala eller Server-IP-pool eller ER-P2P-adressintervall. Hur får du följande IP-adressintervall? Företagets nätverks team eller tjänst leverantör bör tillhandahålla ett eller flera IP-adressintervall som inte används i nätverket. Till exempel är under nätet för den virtuella Azure-datorn 10.0.1.0/24 och under nätet för ditt Azure Gateway-undernät är 10.0.2.0/28.  Vi rekommenderar att det virtuella Azure-nätverkets adress utrymme definieras som: 10.0.1.0/24 och 10.0.2.0/28. Även om värdena för adress utrymme kan aggregeras rekommenderar vi att du matchar dem i under nätets intervall. På så sätt kan du oavsiktligt undvika att återanvända oanvända IP-adressintervall inom större adress utrymmen på annan plats i nätverket. **Det virtuella nätverkets adress utrymme är ett IP-adressintervall. Den måste skickas till Microsoft när du ber om en inledande distribution**.
- **IP-adressintervall för Azure VM-undernät:** Detta IP-adressintervall är det som du tilldelar till under nätet för Azures virtuella nätverk. Den här parametern finns i ditt virtuella Azure-nätverk och ansluter till SAP HANA stor instans miljö. Det här IP-adressintervallet används för att tilldela IP-adresser till dina virtuella Azure-datorer. IP-adresserna från intervallet är tillåtna för att ansluta till dina SAP HANA stora instans servrar. Om det behövs kan du använda flera virtuella Azure-undernät. Vi rekommenderar ett/24 CIDR-block för varje Azure VM-undernät. Adress intervallet måste vara en del av de värden som används i det virtuella Azure-nätverkets adress utrymme. Hur får du det här IP-adressintervallet? Företagets nätverks team eller tjänst leverantör bör ange ett IP-adressintervall som inte används i nätverket.
- **IP-adressintervall för undernät för virtuell nätverksgateway:** Beroende på vilka funktioner du planerar att använda är den rekommenderade storleken:
   - ExpressRoute-Gateway med ultralåg prestanda:/26 adress block--krävs för typ II-klassen för SKU: er.
   - Samexistens med VPN och ExpressRoute med en ExpressRoute för virtuella nätverk med höga prestanda (eller mindre):/27-Adressblock.
   - Alla andra situationer:/28-Adressblock. Adress intervallet måste vara en del av de värden som används i värdena för VNet-adressutrymmet. Adress intervallet måste vara en del av de värden som används i det virtuella Azure-nätverkets adress utrymmes värden som du skickar till Microsoft. Hur får du det här IP-adressintervallet? Företagets nätverks team eller tjänst leverantör bör ange ett IP-adressintervall som för närvarande inte används i nätverket. 
- **Adress intervall för er-P2P-anslutning:** Intervallet är IP-intervallet för din SAP HANA stora ExpressRoute (ER) P2P-anslutning. Det här intervallet av IP-adresser måste vara ett/29 CIDR IP-adressintervall. Intervallet får inte överlappa dina lokala eller andra Azure IP-adressintervall. Detta IP-adressintervall används för att konfigurera ER-anslutningen från din virtuella ExpressRoute-Gateway till SAP HANA stora instans servrar. Hur får du det här IP-adressintervallet? Företagets nätverks team eller tjänst leverantör bör ange ett IP-adressintervall som för närvarande inte används i nätverket. **Det här intervallet är ett IP-adressintervall. Den måste skickas till Microsoft när du ber om en inledande distribution**.  
- **Adress intervall för serverns IP-adresspool:** Det här IP-adressintervallet används för att tilldela den enskilda IP-adressen till HANA-stora instans servrar. Den rekommenderade under näts storleken är ett/24 CIDR-block. Vid behov kan det vara mindre, med så få som 64 IP-adresser. Från det här intervallet är de första 30 IP-adresserna reserverade för användning av Microsoft. Se till att du har ett konto för detta faktum när du väljer storlek på intervallet. Intervallet får inte överlappa dina lokala eller andra Azure IP-adresser. Hur får du det här IP-adressintervallet? Företagets nätverks team eller tjänst leverantör bör ange ett IP-adressintervall som för närvarande inte används i nätverket.  **Det här intervallet är ett IP-adressintervall som måste skickas till Microsoft när du begär en inledande distribution**.

Valfria IP-adressintervall som slutligen måste skickas till Microsoft:

- Om du väljer att använda [ExpressRoute Global Reach](../../../expressroute/expressroute-global-reach.md) för att aktivera dirigerad routning från lokal till Hana stora instans enheter måste du reservera ett annat/29 IP-adressintervall. Det här intervallet får inte överlappa något av de andra IP-adressintervall som du definierade tidigare.
- Om du väljer att använda [ExpressRoute Global Reach](../../../expressroute/expressroute-global-reach.md) för att aktivera dirigerad routning från en-stor instans klient i en Azure-region till en annan Hana stor instans-klient i en annan Azure-region måste du reservera ett annat/29 IP-adressintervall. Det här intervallet får inte överlappa något av de andra IP-adressintervall som du definierade tidigare.

Mer information om ExpressRoute Global Reach och användning av HANA-stora instanser hittar du i dokumenten:

- [SAP HANA (stora instanser) nätverks arkitektur](./hana-network-architecture.md)
- [Anslut ett virtuellt nätverk till HANA-stora instanser](./hana-connect-vnet-express-route.md)
 
Du måste definiera och planera IP-adressintervall som beskrevs tidigare. Du behöver dock inte skicka alla till Microsoft. De IP-adressintervall som du måste namnge till Microsoft är:

- Adress utrymme för virtuella Azure-nätverk
- Adress intervall för ER-P2P-anslutning
- Adress intervall för serverns IP-adresspool

Om du lägger till ytterligare virtuella nätverk som behöver ansluta till HANA-stora instanser måste du skicka det nya adress utrymmet för det virtuella Azure-nätverket som du lägger till i Microsoft. 

Följande är ett exempel på olika intervall och några exempel intervall när du behöver konfigurera och slutligen ge Microsoft. Värdet för adress utrymmet för det virtuella Azure-nätverket sammanställs inte i det första exemplet. Det definieras dock från intervallen för det första IP-adressintervallet för Azure VM-undernätet och IP-adressintervallet för det virtuella nätverkets gateway-undernät. 

Du kan använda flera virtuella dator under nät i det virtuella Azure-nätverket när du konfigurerar och skickar ytterligare IP-adressintervall för de ytterligare virtuella dator under näten som en del av adress utrymmet för det virtuella Azure-nätverket.

![IP-adressintervall som krävs i SAP HANA på Azure (stora instanser) minimal distribution](./media/hana-overview-connectivity/image4b-ip-addres-ranges-necessary.png)

Bilden visar inte de ytterligare IP-adressintervall som krävs för den valfria användningen av ExpressRoute Global Reach.

Du kan också samla in de data som du skickar till Microsoft. I så fall innehåller det virtuella Azure-nätverkets adress utrymme bara ett blank steg. Med hjälp av IP-adressintervall från det tidigare exemplet kan det sammanställda virtuella nätverkets adress utrymme se ut som följande bild:

![Den andra möjligheten för IP-adressintervall som krävs i SAP HANA på Azure (stora instanser) minimal distribution](./media/hana-overview-connectivity/image5b-ip-addres-ranges-necessary-one-value.png)

I exemplet, i stället för två mindre intervall som definierar adress utrymmet för det virtuella Azure-nätverket, har vi ett större intervall som täcker 4096 IP-adresser. En sådan stor definition av adress utrymmet innebär att några hellre stora områden inte används. Eftersom det virtuella nätverkets adress utrymmes värde (er) används för att sprida BGP-vägar kan användningen av de oanvända intervallen lokalt eller någon annan i nätverket orsaka problem med routningen. Bilden visar inte de ytterligare IP-adressintervall som krävs för den valfria användningen av ExpressRoute Global Reach.

Vi rekommenderar att du behåller adress utrymmet tätt justerat med det faktiska adress utrymmet för under nätet som du använder. Om det behövs, utan att det uppstår avbrott i det virtuella nätverket, kan du alltid lägga till nya adress utrymmes värden senare.
 
> [!IMPORTANT] 
> Varje IP-adressintervall i ER-P2P, serverns IP-pool och det virtuella Azure-nätverkets adress utrymme får **inte** överlappa varandra eller med något annat intervall som används i nätverket. Var och en måste vara diskret. I takt med att de två föregående bilderna visas kan de inte heller vara ett undernät i något annat intervall. Om överlappar inträffar mellan intervallen kanske det virtuella Azure-nätverket inte kan ansluta till ExpressRoute-kretsen.

## <a name="next-steps-after-address-ranges-have-been-defined"></a>Nästa steg efter att adress intervall har definierats
När IP-adressintervall har definierats måste följande saker inträffa:

1. Skicka IP-adressintervall för adress utrymmet för det virtuella Azure-nätverket, ER-P2P-anslutningen och adress intervallet för serverns IP-adresspool, tillsammans med andra data som har listats i början av dokumentet. Nu kan du också börja skapa det virtuella nätverket och de virtuella dator under näten. 
2. En ExpressRoute-krets skapas av Microsoft mellan din Azure-prenumeration och den många HANA-instans stämpeln.
3. Ett klient nätverk skapas på den stora instans stämplingen av Microsoft.
4. Microsoft konfigurerar nätverk i den SAP HANA på Azure (stora instanser)-infrastrukturen för att acceptera IP-adresser från ditt Azure Virtual Network-adressutrymme som kommunicerar med HANA-stora instanser.
5. Beroende på den aktuella SAP HANA på Azure-SKU: n (stor instans) som du har köpt tilldelar Microsoft en beräknings enhet i ett klient nätverk. Dessutom allokeras och monteras lagringen, och operativ systemet installeras (SUSE eller Red Hat Linux). IP-adresserna för de här enheterna tas bort från adress intervallet för serverns IP-adresspool som du skickade till Microsoft.

I slutet av distributions processen levererar Microsoft följande data till dig:
- Information som krävs för att ansluta dina virtuella Azure-nätverk till ExpressRoute-kretsen som ansluter virtuella Azure-nätverk till HANA-stora instanser:
     - Nyckel (er)
     - ExpressRoute PeerID
- Data för att komma åt HANA-stora instanser efter att du upprättat ExpressRoute-krets och Azure Virtual Network.

Du kan också hitta sekvensen för att ansluta HANA-stora instanser i dokumentet [SAP HANA på Azure (stora instanser)-installationen](https://azure.microsoft.com/resources/sap-hana-on-azure-large-instances-setup/). Många av följande steg visas i en exempel distribution i dokumentet. 

## <a name="next-steps"></a>Nästa steg

- Se [ansluta ett virtuellt nätverk till Hana stor instans ExpressRoute](hana-connect-vnet-express-route.md).
