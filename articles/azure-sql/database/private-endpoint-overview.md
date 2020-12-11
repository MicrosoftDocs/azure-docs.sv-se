---
title: Azure Private Link
description: Översikt över privat slut punkts funktion.
author: rohitnayakmsft
ms.author: rohitna
titleSuffix: Azure SQL Database and Azure Synapse Analytics
ms.service: sql-database
ms.topic: overview
ms.custom: sqldbrb=1
ms.reviewer: vanto
ms.date: 03/09/2020
ms.openlocfilehash: 7bc15b369bfa4964384d4f7910d6953bdfeaa664
ms.sourcegitcommit: 5db975ced62cd095be587d99da01949222fc69a3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97094173"
---
# <a name="azure-private-link-for-azure-sql-database-and-azure-synapse-analytics"></a>Azure privat länk för Azure SQL Database och Azure Synapse Analytics
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

Med privat länk kan du ansluta till olika PaaS-tjänster i Azure via en **privat slut punkt**. En lista över PaaS-tjänster som stöder funktionen för privat länk finns på [dokumentations sidan för privat länk](../../private-link/index.yml) . En privat slut punkt är en privat IP-adress inom ett särskilt [VNet](../../virtual-network/virtual-networks-overview.md) och undernät.

> [!IMPORTANT]
> Den här artikeln gäller för både Azure SQL Database-och Azure Synapse-analys. För enkelhetens skull refererar termen "databas" till båda databaserna i Azure SQL Database och Azure Synapse Analytics. På samma sätt refererar alla referenser till "Server" till den [logiska SQL-Server](logical-servers.md) som är värd för Azure SQL Database och Azure Synapse Analytics. Den här artikeln gäller *inte* för **Azure SQL-hanterade instanser**.

## <a name="how-to-set-up-private-link-for-azure-sql-database"></a>Så här konfigurerar du en privat länk för Azure SQL Database 

### <a name="creation-process"></a>Skapande process
Privata slut punkter kan skapas med hjälp av Azure Portal, PowerShell eller Azure CLI:
- [Portalen](../../private-link/create-private-endpoint-portal.md)
- [PowerShell](../../private-link/create-private-endpoint-powershell.md)
- [CLI](../../private-link/create-private-endpoint-cli.md)

### <a name="approval-process"></a>Godkännande process
När nätverks administratören skapar den privata slut punkten (PE) kan SQL-administratören hantera den privata slut punkts anslutningen (PEC) för att SQL Database.

1. Navigera till Server resursen i Azure Portal enligt stegen som visas i skärm bilden nedan

    - (1) Välj de privata slut punkts anslutningarna i det vänstra fönstret
    - (2) visar en lista över alla anslutningar för privata slut punkter (PECs)
    - (3) motsvarande privat slut punkt (PE) skapade ![ skärm bild av alla Pecs][3]

1. Välj en enskild PEC från listan genom att markera den.
![Skärm bild som valts PEC][6]

1. SQL-administratören kan välja att godkänna eller avvisa en PEC och eventuellt lägga till ett kort text svar.
![Skärm bild av PEC-godkännande][4]

1. Efter godkännande eller avvisande visar listan lämpligt tillstånd tillsammans med svars texten.
![Skärm bild av alla PECs efter godkännande][5]

## <a name="on-premises-connectivity-over-private-peering"></a>Lokal anslutning via privat peering

När kunderna ansluter till den offentliga slut punkten från lokala datorer måste deras IP-adress läggas till i den IP-baserade brand väggen med hjälp av en [brand Väggs regel på server nivå](firewall-create-server-level-portal-quickstart.md). Även om den här modellen fungerar bra för att tillåta åtkomst till enskilda datorer för utveckling eller testning av arbets belastningar, är det svårt att hantera i en produktions miljö.

Med privat länk kan kunder aktivera åtkomst till lokala slut punkter via [ExpressRoute](../../expressroute/expressroute-introduction.md), privat peering eller VPN-tunnlar. Kunder kan sedan inaktivera all åtkomst via den offentliga slut punkten och inte använda den IP-baserade brand väggen för att tillåta alla IP-adresser.

## <a name="use-cases-of-private-link-for-azure-sql-database"></a>Använd fall av privat länk för Azure SQL Database 

Klienter kan ansluta till den privata slut punkten från samma virtuella nätverk, peer-kopplat virtuella nätverk i samma region eller via virtuellt nätverk till virtuell nätverks anslutning mellan regioner. Dessutom kan klienter ansluta lokalt med ExpressRoute, privat peering eller VPN-tunnlar. Nedan visas ett förenklat diagram som visar vanliga användnings fall.

 ![Diagram över anslutnings alternativ][1]

## <a name="test-connectivity-to-sql-database-from-an-azure-vm-in-same-virtual-network"></a>Testa anslutningen till SQL Database från en virtuell Azure-dator i samma virtuella nätverk

I det här scenariot förutsätter vi att du har skapat en virtuell Azure-dator (VM) som kör Windows Server 2016. 

1. [Starta en fjärr skrivbords-session (RDP) och Anslut till den virtuella datorn](../../virtual-machines/windows/connect-logon.md#connect-to-the-virtual-machine). 
1. Du kan sedan utföra vissa grundläggande anslutnings kontroller för att säkerställa att den virtuella datorn ansluter till SQL Database via den privata slut punkten med hjälp av följande verktyg:
    1. Telnet
    1. Psping
    1. Nmap
    1. SQL Server Management Studio (SSMS)

### <a name="check-connectivity-using-telnet"></a>Kontrol lera anslutningen med Telnet

[Telnet-klienten](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc754293%28v%3dws.10%29) är en Windows-funktion som kan användas för att testa anslutningen. Beroende på vilken version av Windows OS du har kan du behöva aktivera den här funktionen explicit. 

Öppna ett kommando tolks fönster när du har installerat telnet. Kör Telnet-kommandot och ange IP-adressen och den privata slut punkten för databasen i SQL Database.

```
>telnet 10.1.1.5 1433
```

När Telnet ansluter ser du en tom skärm i kommando fönstret som bilden nedan:

 ![Diagram över Telnet][2]

### <a name="check-connectivity-using-psping"></a>Kontrol lera anslutningen med Psping

[Psping](/sysinternals/downloads/psping) kan användas på följande sätt för att kontrol lera att den privata slut punkts anslutningen (PEC) lyssnar efter anslutningar på port 1433.

Kör psping enligt följande genom att ange det fullständiga domän namnet för den logiska SQL-servern och port 1433:

```
>psping.exe mysqldbsrvr.database.windows.net:1433

PsPing v2.10 - PsPing - ping, latency, bandwidth measurement utility
Copyright (C) 2012-2016 Mark Russinovich
Sysinternals - www.sysinternals.com

TCP connect to 10.6.1.4:1433:
5 iterations (warmup 1) ping test:
Connecting to 10.6.1.4:1433 (warmup): from 10.6.0.4:49953: 2.83ms
Connecting to 10.6.1.4:1433: from 10.6.0.4:49954: 1.26ms
Connecting to 10.6.1.4:1433: from 10.6.0.4:49955: 1.98ms
Connecting to 10.6.1.4:1433: from 10.6.0.4:49956: 1.43ms
Connecting to 10.6.1.4:1433: from 10.6.0.4:49958: 2.28ms
```

Utdata visar att Psping kan pinga den privata IP-adress som är associerad med PEC.

### <a name="check-connectivity-using-nmap"></a>Kontrol lera anslutningen med nmap

Nmap (Network Mapper) är ett kostnads fritt verktyg med öppen källkod som används för nätverks identifiering och säkerhets granskning. Mer information och nedladdnings länken finns på https://nmap.org . Du kan använda det här verktyget för att se till att den privata slut punkten lyssnar efter anslutningar på port 1433.

Kör nmap enligt följande genom att tillhandahålla adress intervallet för under nätet som är värd för den privata slut punkten.

```
>nmap -n -sP 10.1.1.0/24
...
...
Nmap scan report for 10.1.1.5
Host is up (0.00s latency).
Nmap done: 256 IP addresses (1 host up) scanned in 207.00 seconds
```

Resultatet visar att en IP-adress är upp. som motsvarar IP-adressen för den privata slut punkten.

### <a name="check-connectivity-using-sql-server-management-studio-ssms"></a>Kontrol lera anslutningen med SQL Server Management Studio (SSMS)
> [!NOTE]
> Använd det **fullständigt kvalificerade domän namnet (FQDN)** för servern i anslutnings strängar för dina klienter ( `<server>.database.windows.net` ). Alla inloggnings försök som görs direkt till IP-adressen eller med hjälp av den privata länkens FQDN ( `<server>.privatelink.database.windows.net` ) Miss lyckas. Det här beteendet är avsiktligt eftersom den privata slut punkten dirigerar trafik till SQL-gatewayen i regionen och rätt FQDN måste anges för att inloggningar ska lyckas.

Följ anvisningarna här för att använda [SSMS för att ansluta till den SQL Database](connect-query-ssms.md). När du har anslutit till SQL Database med SSMS kontrollerar du att du ansluter från den privata IP-adressen för den virtuella Azure-datorn genom att köra följande fråga:

````
select client_net_address from sys.dm_exec_connections 
where session_id=@@SPID
````

## <a name="data-exfiltration-prevention"></a>Dataexfiltreringsskydd

Data exfiltrering i Azure SQL Database är när en behörig användare, till exempel en databas administratör, kan extrahera data från ett system och flytta den till en annan plats eller ett system utanför organisationen. Användaren kan till exempel flytta data till ett lagrings konto som ägs av en tredje part.

Tänk dig ett scenario med en användare som kör SQL Server Management Studio (SSMS) i en virtuell Azure-dator som ansluter till en databas i SQL Database. Den här databasen är i data centret västra USA. Exemplet nedan visar hur du begränsar åtkomsten med offentliga slut punkter på SQL Database med hjälp av nätverks åtkomst kontroller.

1. Inaktivera all Azure Service-trafik för att SQL Database via den offentliga slut punkten genom att ställa in Tillåt att Azure-tjänster **stängs** av. Se till att inga IP-adresser är tillåtna i brand Väggs reglerna för Server och databas nivå. Mer information finns i [Azure SQL Database och Azure Synapse Analytics Network Access Controls](network-access-controls-overview.md).
1. Tillåt endast trafik till databasen i SQL Database att använda den virtuella datorns privata IP-adress. Mer information finns i artiklarna om [tjänstens slut punkt](vnet-service-endpoint-rule-overview.md) och [brand Väggs regler för virtuella nätverk](firewall-configure.md).
1. På den virtuella Azure-datorn begränsar du omfattningen av utgående anslutning genom att använda [nätverks säkerhets grupper (NSG: er)](../../virtual-network/manage-network-security-group.md) och service märken enligt följande
    - Ange en NSG-regel för att tillåta trafik för service tag = SQL. Västra USA – endast tillåta anslutning till SQL Database i västra USA
    - Ange en NSG-regel (med en **högre prioritet**) om du vill neka trafik för service tag = SQL-neka anslutningar till SQL Database i alla regioner

I slutet av den här installationen kan den virtuella Azure-datorn bara ansluta till en databas i SQL Database i regionen USA, västra. Anslutningen är dock inte begränsad till en enda databas i SQL Database. Den virtuella datorn kan fortfarande ansluta till en databas i regionen USA, västra, inklusive de databaser som inte ingår i prenumerationen. Vi har minskat omfattningen av data exfiltrering i scenariot ovan till en speciell region, men vi har inte eliminerat det helt.

Med privat länk kan kunder nu konfigurera nätverks åtkomst kontroller som NSG: er för att begränsa åtkomsten till den privata slut punkten. Enskilda Azure PaaS-resurser mappas sedan till specifika privata slut punkter. En skadlig Insider kan bara komma åt den mappade PaaS-resursen (till exempel en databas i SQL Database) och ingen annan resurs. 

## <a name="limitations"></a>Begränsningar 
Anslutningar till privat slut punkt stöder bara **proxy** som [anslutnings princip](connectivity-architecture.md#connection-policy)


## <a name="connecting-from-an-azure-vm-in-peered-virtual-network"></a>Ansluta från en virtuell Azure-dator i peer-Virtual Network 

Konfigurera [peering för virtuella nätverk](../../virtual-network/tutorial-connect-virtual-networks-powershell.md) för att upprätta anslutning till SQL Database från en virtuell Azure-dator i ett peer-kopplat virtuellt nätverk.

## <a name="connecting-from-an-azure-vm-in-virtual-network-to-virtual-network-environment"></a>Ansluta från en virtuell Azure-dator i virtuellt nätverk till en virtuell nätverks miljö

Konfigurera [virtuellt nätverk till virtuellt nätverk VPN gateway-anslutning](../../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) för att upprätta en anslutning till en databas i SQL Database från en virtuell Azure-dator i en annan region eller prenumeration.

## <a name="connecting-from-an-on-premises-environment-over-vpn"></a>Ansluta från en lokal miljö via VPN

Om du vill upprätta en anslutning från en lokal miljö till databasen i SQL Database väljer du och implementerar något av alternativen:
- [Punkt-till-plats-anslutning](../../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)
- [Plats-till-plats-anslutning via VPN](../../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)
- [ExpressRoute-krets](../../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md)


## <a name="connecting-from-azure-synapse-analytics-to-azure-storage-using-polybase-and-the-copy-statement"></a>Ansluta från Azure Synapse Analytics till Azure Storage med PolyBase och KOPIERINGs instruktionen

PolyBase och KOPIERINGs instruktionen används ofta för att läsa in data i Azure Synapse Analytics från Azure Storage-konton. Om Azure Storage konto som du läser in data från begränsar åtkomsten till en uppsättning virtuella nätverks under nät via privata slut punkter, tjänst slut punkter eller IP-baserade brand väggar, kommer anslutningen från PolyBase och KOPIERINGs instruktionen till kontot att avbrytas. Om du vill aktivera både import-och export scenarier med Azure Synapse Analytics ansluter du till Azure Storage som är skyddade till ett virtuellt nätverk följer du stegen som visas [här](vnet-service-endpoint-rule-overview.md#impact-of-using-virtual-network-service-endpoints-with-azure-storage). 

## <a name="next-steps"></a>Nästa steg

- En översikt över Azure SQL Database säkerhet finns i [skydda databasen](security-overview.md)
- En översikt över Azure SQL Database-anslutningar finns i [arkitektur för Azure SQL-anslutning](connectivity-architecture.md)

<!--Image references-->
[1]: media/quickstart-create-single-database/pe-connect-overview.png
[2]: media/quickstart-create-single-database/telnet-result.png
[3]: media/quickstart-create-single-database/pec-list-before.png
[4]: media/quickstart-create-single-database/pec-approve.png
[5]: media/quickstart-create-single-database/pec-list-after.png
[6]: media/quickstart-create-single-database/pec-select.png