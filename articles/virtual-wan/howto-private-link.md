---
title: Dela en privat länk-tjänst över virtuella WAN-nätverk
titleSuffix: Azure Virtual WAN
description: Steg för att konfigurera privat länk i virtuellt WAN
services: virtual-wan
author: erjosito
ms.service: virtual-wan
ms.topic: how-to
ms.date: 08/18/2020
ms.author: jomore
ms.custom: fasttrack-new
ms.openlocfilehash: 98142e3a8904bcbb0352fa768fc72966412dae0b
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2020
ms.locfileid: "88590687"
---
# <a name="use-private-link-in-virtual-wan"></a>Använd privat länk i virtuellt WAN

[Azures privata länk](../private-link/private-link-overview.md) är en teknik som gör att du kan ansluta Azures tjänster som tjänst erbjudanden med hjälp av privat IP-adress anslutning genom att exponera [privata slut punkter](../private-link/private-endpoint-overview.md). Med Azure Virtual WAN kan du distribuera en privat slut punkt i något av de virtuella nätverk som är anslutna till en virtuell hubb. Detta ger anslutning till andra virtuella nätverk eller förgreningar som är anslutna till samma virtuella WAN-nätverk.

## <a name="before-you-begin"></a>Innan du börjar

Stegen i den här artikeln förutsätter att du redan har distribuerat ett virtuellt WAN med ett eller flera hubbar, samt minst två virtuella nätverk som är anslutna till det virtuella WAN-nätverket.

Om du vill skapa ett nytt virtuellt WAN-nätverk och en ny hubb använder du stegen i följande artiklar:

* [Skapa ett virtuellt WAN](virtual-wan-site-to-site-portal.md#openvwan)
* [Skapa en hubb](virtual-wan-site-to-site-portal.md#hub)
* [Ansluta ett virtuellt nätverk till en hubb](virtual-wan-site-to-site-portal.md#hub)

## <a name="create-a-private-link-endpoint"></a><a name="endpoint"></a>Skapa en privat länk slut punkt

Du kan skapa en privat länk slut punkt för många olika tjänster. I det här exemplet ska vi använda Azure SQL Database. Du hittar mer information om hur du skapar en privat slut punkt för en Azure SQL Database i [snabb start: skapa en privat slut punkt med hjälp av Azure Portal](../private-link/create-private-endpoint-portal.md). Följande bild visar nätverks konfigurationen för Azure SQL Database:

:::image type="content" source="./media/howto-private-link/create-private-link.png" alt-text="Skapa privat länk" lightbox="./media/howto-private-link/create-private-link.png":::

När du har skapat Azure SQL Database kan du kontrol lera den privata slut punktens IP-adress bläddra bland dina privata slut punkter:

:::image type="content" source="./media/howto-private-link/endpoints.png" alt-text="privata slut punkter" lightbox="./media/howto-private-link/endpoints.png":::

När du klickar på den privata slut punkten som vi har skapat bör du se dess privata IP-adress, samt dess fullständigt kvalificerade domän namn (FQDN). Observera att den privata slut punkten har en IP-adress i intervallet för det VNet där den har distribuerats (10.1.3.0/24):

:::image type="content" source="./media/howto-private-link/sql-endpoint.png" alt-text="SQL-slutpunkt" lightbox="./media/howto-private-link/sql-endpoint.png":::

## <a name="verify-connectivity-from-the-same-vnet"></a><a name="connectivity"></a>Kontrol lera anslutningen från samma VNet

I det här exemplet kommer vi att verifiera anslutningen till Azure SQL Database från en virtuell Ubuntu-dator med MS SQL tools installerat. Det första steget är att kontrol lera att DNS-matchning fungerar och att det Azure SQL Database fullständigt kvalificerade domän namnet matchas till en privat IP-adress, i samma VNet där den privata slut punkten har distribuerats (10.1.3.0/24):

```bash
$ nslookup wantest.database.windows.net
Server:         127.0.0.53
Address:        127.0.0.53#53

Non-authoritative answer:
wantest.database.windows.net    canonical name = wantest.privatelink.database.windows.net.
Name:   wantest.privatelink.database.windows.net
Address: 10.1.3.228
```

Som du kan se i föregående utdata `wantest.database.windows.net` mappas FQDN till `wantest.privatelink.database.windows.net` , som den privata DNS-zon som skapats längs den privata slut punkten kommer att matcha till den privata IP-adressen `10.1.3.228` . Om du tittar på den privata DNS-zonen bekräftas att det finns en post för den privata slut punkten som är mappad till den privata IP-adressen:

:::image type="content" source="./media/howto-private-link/dns-zone.png" alt-text="DNS-zon" lightbox="./media/howto-private-link/dns-zone.png":::

När du har verifierat rätt DNS-matchning kan vi försöka ansluta till databasen:

```bash
$ query="SELECT CONVERT(char(15), CONNECTIONPROPERTY('client_net_address'));"
$ sqlcmd -S wantest.database.windows.net -U $username -P $password -Q "$query"

10.1.3.75
```

Som du kan se använder vi en särskild SQL-fråga som ger oss käll-IP-adressen som SQL-servern ser från klienten. I det här fallet ser servern klienten med dess privata IP-adress ( `10.1.3.75` ), vilket innebär att trafiken inte färdas via det offentliga Internet, utan går direkt till den privata slut punkten.

Observera att du måste ange variablerna `username` och `password` matcha de autentiseringsuppgifter som definierats i Azure SQL Database för att se att exemplen i den här hand boken fungerar.

## <a name="connect-from-a-different-vnet"></a><a name="vnet"></a>Anslut från ett annat VNet

Nu när ett VNet i Azure Virtual WAN har anslutning till den privata slut punkten, kan alla andra virtuella nätverk och grenar som är anslutna till det virtuella WAN-nätverket också ha åtkomst till den. Du måste tillhandahålla anslutning via någon av de modeller som stöds av Azure Virtual WAN, till exempel [scenariot alla-till-alla](scenario-any-to-any.md) eller [delade tjänster i VNet](scenario-shared-services-vnet.md), för att ge två exempel.

När du har anslutit till VNet eller grenen till det virtuella nätverk där den privata slut punkten har distribuerats måste du konfigurera DNS-matchning:

* Om du ansluter till den privata slut punkten från ett virtuellt nätverk kan du använda samma privata zon som skapades med Azure SQL Database.
* Om du ansluter till den privata slut punkten från en gren (plats-till-plats-VPN, punkt-till-plats-VPN eller ExpressRoute), måste du använda en lokal DNS-matchning.

I det här exemplet ansluter vi från ett annat virtuellt nätverk, så vi ska först koppla den privata DNS-zonen till det nya virtuella nätverket så att dess arbets belastningar kan matcha det Azure SQL Database fullständiga domän namnet till den privata IP-adressen. Detta görs genom länkning av den privata DNS-zonen till det nya VNet:

:::image type="content" source="./media/howto-private-link/dns-link.png" alt-text="DNS-länk" lightbox="./media/howto-private-link/dns-link.png":::

Nu bör den virtuella datorn i det anslutna VNet-nätverket matcha Azure SQL Database FQDN till den privata länkens privata IP-adress:

```bash
$ nslookup wantest.database.windows.net
Server:         127.0.0.53
Address:        127.0.0.53#53

Non-authoritative answer:
wantest.database.windows.net    canonical name = wantest.privatelink.database.windows.net.
Name:   wantest.privatelink.database.windows.net
Address: 10.1.3.228
```

För att kunna kontrol lera att detta VNet (10.1.1.0/24) har anslutning till det ursprungliga VNet där den privata slut punkten har kon figurer ATS (10.1.3.0/24), kan du kontrol lera den effektiva routningstabellen i alla virtuella datorer i VNet:

:::image type="content" source="./media/howto-private-link/effective-routes.png" alt-text="effektiva vägar" lightbox="./media/howto-private-link/effective-routes.png":::

Som du kan se finns en väg som pekar på VNet-10.1.3.0/24 som injiceras av Virtual Network gateways i Azure Virtual WAN. Nu kan vi slutligen testa anslutningen till databasen:

```bash
$ query="SELECT CONVERT(char(15), CONNECTIONPROPERTY('client_net_address'));"
$ sqlcmd -S wantest.database.windows.net -U $username -P $password -Q "$query"

10.1.1.75
```

I det här exemplet har vi sett hur man skapar en privat slut punkt i någon av de virtuella nätverk som är kopplade till ett virtuellt WAN-nätverk som ger anslutning till resten av virtuella nätverk och grenar i det virtuella WAN-nätverket.

## <a name="next-steps"></a>Nästa steg

Mer information om virtuellt WAN finns i [vanliga frågor och svar](virtual-wan-faq.md).
