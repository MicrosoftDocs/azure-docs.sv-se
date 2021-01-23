---
title: Anslut ditt program till SQL-hanterad instans
titleSuffix: Azure SQL Managed Instance
description: Den här artikeln beskriver hur du ansluter ditt program till en Azure SQL-hanterad instans.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, vanto
ms.date: 11/09/2018
ms.openlocfilehash: 965e765e22a4da8f2ac3b7151337cf62b65be4fe
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2021
ms.locfileid: "98732619"
---
# <a name="connect-your-application-to-azure-sql-managed-instance"></a>Anslut ditt program till Azure SQL Managed Instance
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Idag har du flera val när du bestämmer hur och var du är värd för ditt program.

Du kan välja att vara värd för program i molnet genom att använda Azure App Service eller vissa av Azures virtuella nätverks integrerade alternativ, t. ex. Azure App Service Environment, Azure Virtual Machines och skalnings uppsättningar för virtuella datorer. Du kan också ta hybrid moln och se till att dina program är lokalt.

Oavsett vilket alternativ du gör kan du ansluta den till en Azure SQL-hanterad instans. 

![Hög tillgänglighet](./media/connect-application-instance/application-deployment-topologies.png)

I den här artikeln beskrivs hur du ansluter ett program till Azure SQL Managed Instance i ett antal olika programscenarier. 

## <a name="connect-inside-the-same-vnet"></a>Anslut inuti samma VNet

Att ansluta ett program i samma virtuella nätverk som SQL-hanterad instans är det enklaste scenariot. Virtuella datorer i det virtuella nätverket kan ansluta till varandra direkt även om de finns i olika undernät. Det innebär att allt du behöver för att ansluta ett program i App Service-miljön eller en virtuell dator är att ange anslutnings strängen på lämpligt sätt.  

## <a name="connect-inside-a-different-vnet"></a>Anslut i ett annat virtuellt nätverk

Att ansluta ett program när det finns i ett annat virtuellt nätverk från SQL Managed instance är lite mer komplext eftersom SQL-hanterad instans har privata IP-adresser i ett eget virtuellt nätverk. För att ansluta måste ett program ha åtkomst till det virtuella nätverk där SQL-hanterad instans distribueras. Så du måste upprätta en anslutning mellan programmet och det virtuella SQL Managed instance-nätverket. De virtuella nätverken behöver inte vara i samma prenumeration för att det här scenariot ska fungera.

Det finns två alternativ för att ansluta virtuella nätverk:

- [Azure VNet-peering](../../virtual-network/virtual-network-peering-overview.md)
- VNet-till-VNet VPN-gateway ([Azure Portal](../../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md), [POWERSHELL](../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md), [Azure CLI](../../vpn-gateway/vpn-gateway-howto-vnet-vnet-cli.md))

Peering är att föredra eftersom det använder Microsoft stamnät nätverket, så från anslutnings perspektivet, finns det ingen märkbar skillnad i fördröjning mellan virtuella datorer i ett peer-kopplat virtuellt nätverk och i samma virtuella nätverk. Peering av virtuella nätverk stöds mellan nätverken i samma region. Global peering för virtuella nätverk stöds också med begränsningen som beskrivs i Obs!  

> [!IMPORTANT]
> [Den 9/22/2020 vi presenterade global peering för virtuella nätverk för nyligen skapade virtuella kluster](https://azure.microsoft.com/en-us/updates/global-virtual-network-peering-support-for-azure-sql-managed-instance-now-available/). Det innebär att global virtuell nätverks-peering stöds för SQL-hanterade instanser som skapats i tomma undernät efter meddelandets datum, även för alla efterföljande hanterade instanser som skapats i dessa undernät. För alla andra SQL-hanterade instanser är peering-stödet begränsat till nätverken i samma region på grund av [begränsningarna i den globala virtuella nätverks-peeringen](../../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints). Se även det relevanta avsnittet i artikeln [vanliga frågor och svar om Azure Virtual Networks](../../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers) för mer information. 

## <a name="connect-from-on-premises"></a>Ansluta lokalt 

Du kan också ansluta ditt lokala program till SQL-hanterad instans. SQL-hanterad instans kan bara nås via en privat IP-adress. För att komma åt den från en lokal plats måste du skapa en plats-till-plats-anslutning mellan programmet och det virtuella SQL Managed instance-nätverket.

Det finns två alternativ för att ansluta lokalt till ett virtuellt Azure-nätverk:

- Plats-till-plats-VPN-anslutning ([Azure Portal](../../vpn-gateway/tutorial-site-to-site-portal.md), [POWERSHELL](../../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md), [Azure CLI](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md))
- [Azure ExpressRoute](../../expressroute/expressroute-introduction.md) -anslutning  

Om du har upprättat en lokal anslutning till Azure-anslutningen och du inte kan upprätta en anslutning till SQL-hanterad instans kontrollerar du om brand väggen har en öppen utgående anslutning på SQL-port 1433 samt 11000-11999-intervallet för portar för omdirigering.

## <a name="connect-the-developer-box"></a>Anslut rutan utvecklare

Du kan också ansluta din utvecklings ruta till SQL-hanterad instans. SQL-hanterad instans kan endast nås via en privat IP-adress, så för att få åtkomst till den från din utvecklare måste du först skapa en anslutning mellan din utvecklings ruta och det virtuella SQL Managed instance-nätverket. Det gör du genom att konfigurera en punkt-till-plats-anslutning till ett virtuellt nätverk med intern Azure-certifikatautentisering. Mer information finns i  [Konfigurera en punkt-till-plats-anslutning för att ansluta till en Azure SQL-hanterad instans från en lokal dator](point-to-site-p2s-configure.md).

## <a name="connect-with-vnet-peering"></a>Anslut med VNet-peering

Ett annat scenario som implementeras av kunder är var en VPN-gateway installeras i ett separat virtuellt nätverk och en prenumeration från den som är värd för SQL-hanterad instans. De två virtuella nätverken är sedan peer-kopplade. Följande exempel arkitektur diagram visar hur detta kan implementeras.

![Virtuell nätverkspeering](./media/connect-application-instance/vnet-peering.png)

När du har konfigurerat den grundläggande infrastrukturen måste du ändra vissa inställningar så att VPN-gatewayen kan se IP-adresserna i det virtuella nätverket som är värd för SQL-hanterad instans. Det gör du genom att göra följande mycket speciella ändringar under **peering-inställningarna**.

1. I det virtuella nätverk som är värd för VPN-gatewayen går du till **peering**, går till peer-ansluten virtuell nätverks anslutning för SQL-hanterad instans och klickar sedan på **Tillåt Gateway-överföring**.
2. I det virtuella nätverk som är värd för SQL-hanterad instans går du till **peering**, går till peer-kopplat virtuell nätverks anslutning för VPN-gatewayen och klickar sedan på **Använd fjärrgatewayer**.

## <a name="connect-azure-app-service"></a>Anslut Azure App Service 

Du kan också ansluta ett program som finns på Azure App Service. SQL-hanterad instans kan endast nås via en privat IP-adress, så för att få åtkomst till den från Azure App Service måste du först upprätta en anslutning mellan programmet och det virtuella SQL Managed instance-nätverket. Se [integrera din app med ett virtuellt Azure-nätverk](../../app-service/web-sites-integrate-with-vnet.md).  

Information om fel sökning finns i [Felsöka virtuella nätverk och program](../../app-service/web-sites-integrate-with-vnet.md#troubleshooting). Försök att [Synkronisera nätverks konfigurationen](azure-app-sync-network-configuration.md)om det inte går att upprätta en anslutning.

Ett särskilt fall av att ansluta Azure App Service till SQL-hanterad instans är när du integrerar Azure App Service till ett nätverk som är peer-kopplat till ett virtuellt SQL-hanterat instans nätverk. Det innebär att följande konfiguration måste konfigureras:

- Det virtuella SQL Managed instance-nätverket får inte ha en gateway  
- Det virtuella nätverket för SQL-hanterad instans måste ha `Use remote gateways` alternativ uppsättningen
- Ett peer-kopplat virtuellt nätverk måste ha `Allow gateway transit` alternativ uppsättningen

Det här scenariot illustreras i följande diagram:

![integrerad app-peering](./media/connect-application-instance/integrated-app-peering.png)

>[!NOTE]
>Funktionen för integrering av virtuella nätverk integrerar inte en app med ett virtuellt nätverk som har en ExpressRoute-Gateway. Även om ExpressRoute-gatewayen har kon figurer ATS i läget för samexistens fungerar inte integrering av virtuella nätverk. Om du behöver åtkomst till resurser via en ExpressRoute-anslutning kan du använda App Service-miljön som körs i det virtuella nätverket.

## <a name="troubleshooting-connectivity-issues"></a>Felsökning av anslutningsproblem

För fel sökning av anslutnings problem, se följande:

- Om du inte kan ansluta till SQL-hanterad instans från en virtuell Azure-dator i samma virtuella nätverk, men ett annat undernät, kontrollerar du om du har en nätverks säkerhets grupp som är inställd på VM-undernät som kan blockera åtkomst. Öppna även utgående anslutningar på SQL-port 1433 samt portar i intervallet 11000-11999, eftersom de behövs för att ansluta via omdirigering i Azure-gränser.
- Se till att BGP-spridningen är **aktive rad** för den routningstabell som är associerad med det virtuella nätverket.
- Om du använder P2S VPN kontrollerar du konfigurationen i Azure Portal för att se om du ser ingångs **-/** utgångs nummer. Siffror som inte är noll anger att Azure dirigerar trafik till/från lokalt.

   ![inkommande/utgående nummer](./media/connect-application-instance/ingress-egress-numbers.png)

- Kontrol lera att klient datorn (som kör VPN-klienten) har väg poster för alla virtuella nätverk som du behöver komma åt. Vägarna lagras i `%AppData%\ Roaming\Microsoft\Network\Connections\Cm\<GUID>\routes.txt` .

   ![route.txt](./media/connect-application-instance/route-txt.png)

   Som du ser i den här bilden finns det två poster för varje virtuellt nätverk och en tredje post för VPN-slutpunkten som har kon figurer ATS i portalen.

   Ett annat sätt att kontrol lera vägarna är via följande kommando. Utdata visar vägarna till olika undernät:

   ```cmd
   C:\ >route print -4
   ===========================================================================
   Interface List
   14...54 ee 75 67 6b 39 ......Intel(R) Ethernet Connection (3) I218-LM
   57...........................rndatavnet
   18...94 65 9c 7d e5 ce ......Intel(R) Dual Band Wireless-AC 7265
   1...........................Software Loopback Interface 1
   Adapter===========================================================================

   IPv4 Route Table
   ===========================================================================
   Active Routes:
   Network Destination        Netmask          Gateway       Interface  Metric
          0.0.0.0          0.0.0.0       10.83.72.1     10.83.74.112     35
         10.0.0.0    255.255.255.0         On-link       172.26.34.2     43
         10.4.0.0    255.255.255.0         On-link       172.26.34.2     43
   ===========================================================================
   Persistent Routes:
   None
   ```

- Om du använder peering för virtuella nätverk kontrollerar du att du har följt anvisningarna för att ställa in [Tillåt Gateway-överföring och använda Fjärrgatewayer](#connect-from-on-premises).

- Om du använder peering för virtuella nätverk för att ansluta ett Azure App Service värdbaserade program och det virtuella SQL-hanterade instans nätverket har ett offentligt IP-adressintervall, kontrollerar du att dina värd program inställningar tillåter att den utgående trafiken dirigeras till offentliga IP-nätverk. Följ instruktionerna i [regional integrering av virtuella nätverk](../../app-service/web-sites-integrate-with-vnet.md#regional-vnet-integration).

## <a name="required-versions-of-drivers-and-tools"></a>Nödvändiga versioner av driv rutiner och verktyg

Följande minimala versioner av verktygen och driv rutinerna rekommenderas om du vill ansluta till SQL-hanterad instans:

| Driv rutin/verktyg | Version |
| --- | --- |
|.NET Framework | 4.6.1 (eller .NET Core) |
|ODBC-drivrutin| v17 |
|PHP-drivrutin| 5.2.0 |
|JDBC-drivrutin| 6.4.0 |
|Node.js-drivrutin| 2.1.1 |
|OLEDB-drivrutin| 18.0.2.0 |
|SSMS| 18,0 eller [högre](/sql/ssms/download-sql-server-management-studio-ssms) |
|[SMO](/sql/relational-databases/server-management-objects-smo/sql-server-management-objects-smo-programming-guide) | [150](https://www.nuget.org/packages/Microsoft.SqlServer.SqlManagementObjects) eller högre |

## <a name="next-steps"></a>Nästa steg

- Information om SQL-hanterad instans finns i [Vad är SQL-hanterad instans?](sql-managed-instance-paas-overview.md).
- En själv studie kurs som visar hur du skapar en ny hanterad instans finns i [skapa en hanterad instans](instance-create-quickstart.md).