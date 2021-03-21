---
title: Nätverks åtkomst kontroller
titleSuffix: Azure SQL Database & Azure Synapse Analytics
description: Översikt över hur du hanterar och styr nätverks åtkomsten för Azure SQL Database och Azure Synapse Analytics.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: sqldbrb=3
ms.devlang: ''
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: vanto
ms.date: 03/09/2020
ms.openlocfilehash: 90bc57af3aaf0d11cd354bfe7163014f836a72e8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96460005"
---
# <a name="azure-sql-database-and-azure-synapse-analytics-network-access-controls"></a>Azure SQL Database och Azure Synapse Analytics Network Access Controls

När du skapar en logisk SQL-Server från [Azure Portal](single-database-create-quickstart.md) för Azure SQL Database och Azure Synapse Analytics är resultatet en offentlig slut punkt i formatet *yourservername.Database.Windows.net*.

Du kan använda följande kontroller för nätverks åtkomst för att selektivt tillåta åtkomst till en databas via den offentliga slut punkten:

- Tillåt Azure-tjänster: när det är inställt på på, kan andra resurser inom Azure-gränser, till exempel en virtuell Azure-dator, komma åt SQL Database
- IP-brandväggs regler: Använd den här funktionen för att uttryckligen tillåta anslutningar från en specifik IP-adress, till exempel från lokala datorer

Du kan också tillåta privat åtkomst till databasen från [virtuella nätverk](../../virtual-network/virtual-networks-overview.md) via:

- Brand Väggs regler för virtuella nätverk: Använd den här funktionen för att tillåta trafik från ett angivet virtuellt nätverk inom Azure-gränsen
- Privat länk: Använd den här funktionen för att skapa en privat slut punkt för [logisk SQL Server](logical-servers.md) inom ett angivet virtuellt nätverk

> [!IMPORTANT]
> Den här artikeln gäller *inte* för **SQL-hanterad instans**. Mer information om nätverks konfigurationen finns i [ansluta till Azure SQL-hanterad instans](../managed-instance/connect-application-instance.md) .

Se videon nedan för en övergripande förklaring av dessa åtkomst kontroller och vad de gör:

> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/Data-Exposed--SQL-Database-Connectivity-Explained/player?WT.mc_id=dataexposed-c9-niner]

## <a name="allow-azure-services"></a>Tillåt Azure-tjänster

Som standard när en ny logisk SQL-Server skapas [från Azure Portal](single-database-create-quickstart.md)anges den här inställningen till **av**. Den här inställningen visas när anslutningen tillåts med hjälp av den offentliga tjänst slut punkten.

Du kan också ändra den här inställningen via brand Väggs fönstret när den logiska SQL-servern har skapats på följande sätt.
  
![Skärm bild av hantera Server brand vägg][2]

När den är inställd **på på**, tillåter servern kommunikation från alla resurser inom Azure-gränser, som kan vara en del av din prenumeration.

I många fall är inställningen **för** att vara mer beviljad än vad kunderna vill ha. Du kanske vill ställa in den här inställningen på **av** och ersätta den med mer restriktiva regler för IP-brandvägg eller brand Väggs regler för virtuella nätverk. 

Detta påverkar dock följande funktioner som körs på virtuella datorer i Azure och som inte är en del av ditt virtuella nätverk och därmed ansluter till databasen via en Azure IP-adress:

### <a name="import-export-service"></a>Importera export tjänst

Import av export tjänsten fungerar inte när **Tillåt åtkomst till Azure-tjänster** är inställt på **av**. Du kan dock lösa problemet genom att [manuellt köra sqlpackage.exe från en virtuell Azure-dator eller utföra exporten](./database-import-export-azure-services-off.md) direkt i din kod med hjälp av DACFx-API: et.

### <a name="data-sync"></a>Datasynkronisering

Om du vill använda funktionen för datasynkronisering med **Tillåt åtkomst till Azure-tjänster** som är inställd på **av**, måste du skapa enskilda brand Väggs regel poster för att [lägga till IP-adresser](firewall-create-server-level-portal-quickstart.md) från **SQL-tjänstetaggen** för den region som är värd för **nav** databasen.
Lägg till dessa brand Väggs regler på server nivå till servrarna som är värdar för både **nav** -och **medlems** databaser (som kan finnas i olika regioner)

Använd följande PowerShell-skript för att skapa IP-adresser som motsvarar SQL-tjänstetaggen för regionen Västra USA

```powershell
PS C:\>  $serviceTags = Get-AzNetworkServiceTag -Location eastus2
PS C:\>  $sql = $serviceTags.Values | Where-Object { $_.Name -eq "Sql.WestUS" }
PS C:\> $sql.Properties.AddressPrefixes.Count
70
PS C:\> $sql.Properties.AddressPrefixes
13.86.216.0/25
13.86.216.128/26
13.86.216.192/27
13.86.217.0/25
13.86.217.128/26
13.86.217.192/27
```

> [!TIP]
> Get-AzNetworkServiceTag returnerar det globala intervallet för SQL Service-taggen trots att ange plats parametern. Se till att du filtrerar den till den region som är värd för NAV databasen som används av din Sync-grupp

Observera att resultatet av PowerShell-skriptet är i CIDR-notation (Classless Inter-Domain routing). Detta måste konverteras till ett format av en start-och slut-IP-adress med hjälp av [Get-IPrangeStartEnd.ps1](https://gallery.technet.microsoft.com/scriptcenter/Start-and-End-IP-addresses-bcccc3a9) så här:

```powershell
PS C:\> Get-IPrangeStartEnd -ip 52.229.17.93 -cidr 26
start        end
-----        ---
52.229.17.64 52.229.17.127
```

Du kan använda det här ytterligare PowerShell-skriptet för att konvertera alla IP-adresser från CIDR till start-och slut-IP-format.

```powershell
PS C:\>foreach( $i in $sql.Properties.AddressPrefixes) {$ip,$cidr= $i.split('/') ; Get-IPrangeStartEnd -ip $ip -cidr $cidr;}
start          end
-----          ---
13.86.216.0    13.86.216.127
13.86.216.128  13.86.216.191
13.86.216.192  13.86.216.223
```

Nu kan du lägga till dessa som särskilda brand Väggs regler och sedan ange **Tillåt att Azure-tjänster får åtkomst till servern**  .

## <a name="ip-firewall-rules"></a>Regler för IP-brandvägg

IP-baserad brand vägg är en funktion i den logiska SQL-servern i Azure som förhindrar all åtkomst till servern förrän du uttryckligen [lägger till IP-adresser](firewall-create-server-level-portal-quickstart.md) för klient datorerna.

## <a name="virtual-network-firewall-rules"></a>Brandväggsregler för virtuella nätverk

Förutom IP-regler kan du med hjälp av serverns brand vägg definiera *regler för virtuella nätverk*.  
Mer information finns i [tjänst slut punkter för virtuella nätverk och regler för Azure SQL Database](vnet-service-endpoint-rule-overview.md) eller titta på den här videon:

> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/Data-Exposed--Demo--Vnet-Firewall-Rules-for-SQL-Database/player?WT.mc_id=dataexposed-c9-niner]

### <a name="azure-networking-terminology"></a>Terminologi för Azure-nätverk

Observera följande villkor för Azure-nätverk när du utforskar brand Väggs reglerna för virtuella nätverk

**Virtuellt nätverk:** Du kan ha virtuella nätverk kopplade till din Azure-prenumeration

**Undernät:** Ett virtuellt nätverk innehåller **undernät**. Alla virtuella datorer i Azure (VM) som du har tilldelats till undernät. Ett undernät kan innehålla flera virtuella datorer eller andra Compute-noder. Compute-noder utanför det virtuella nätverket kan inte komma åt ditt virtuella nätverk om du inte konfigurerar din säkerhet att tillåta åtkomst.

**Tjänst slut punkt för virtuellt nätverk:** En [tjänst slut punkt för virtuellt nätverk](../../virtual-network/virtual-network-service-endpoints-overview.md) är ett undernät vars egenskaps värden innehåller ett eller flera formella namn för Azure-tjänst typen. I den här artikeln är vi intresserade av typ namnet **Microsoft. SQL**, som refererar till Azure-tjänsten med namnet SQL Database.

**Regel för virtuellt nätverk:** En virtuell nätverks regel för servern är ett undernät som listas i åtkomst kontrol listan (ACL) på servern. För att vara med i ACL för databasen i SQL Database måste under nätet innehålla namnet **Microsoft. SQL** -typ. En regel för virtuella nätverk instruerar servern att acceptera kommunikation från varje nod som finns på under nätet.

## <a name="ip-vs-virtual-network-firewall-rules"></a>Brand Väggs regler för IP-jämfört med virtuella nätverk

Med Azure SQL Database brand väggen kan du ange IP-adressintervall från vilka kommunikationen godkänns i SQL Database. Den här metoden är bra för stabila IP-adresser som ligger utanför Azures privata nätverk. Virtuella datorer (VM) i Azures privata nätverk konfigureras dock med *dynamiska* IP-adresser. Dynamiska IP-adresser kan ändras när den virtuella datorn startas om och den IP-baserade brand Väggs regeln i tur är ogiltig. Det skulle vara Folly att ange en dynamisk IP-adress i en brand Väggs regel i en produktions miljö.

Du kan undvika den här begränsningen genom att skaffa en *statisk* IP-adress för den virtuella datorn. Mer information finns i [skapa en virtuell dator med en statisk offentlig IP-adress med hjälp av Azure Portal](../../virtual-network/virtual-network-deploy-static-pip-arm-portal.md). Den statiska IP-metoden kan dock bli svår att hantera, och det är kostsamt när du har gjort det i stor skala.

Regler för virtuella nätverk är enklare att upprätta och hantera åtkomst från ett särskilt undernät som innehåller dina virtuella datorer.

> [!NOTE]
> Du kan inte ha SQL Database i ett undernät än. Om din server var en nod i ett undernät i det virtuella nätverket kan alla noder i det virtuella nätverket kommunicera med din SQL Database. I det här fallet kan de virtuella datorerna kommunicera med SQL Database utan att behöva några regler för virtuella nätverk eller IP-regler.

## <a name="private-link"></a>Private Link

Med privat länk kan du ansluta till en server via en **privat slut punkt**. En privat slut punkt är en privat IP-adress inom ett särskilt [virtuellt nätverk](../../virtual-network/virtual-networks-overview.md) och undernät.

## <a name="next-steps"></a>Nästa steg

- En snabb start för att skapa en regel för IP-brandvägg på server nivå finns [i skapa en databas i SQL Database](single-database-create-quickstart.md).

- En snabb start för att skapa en brand Väggs regel på server nivå för virtuella nätverk finns i [Virtual Network tjänst slut punkter och regler för Azure SQL Database](vnet-service-endpoint-rule-overview.md).

- Om du vill ha hjälp med att ansluta till en databas i SQL Database från program med öppen källkod eller tredjepartsprogram, se [kod exempel för klient snabb start för att SQL Database](/previous-versions/azure/ee336282(v=azure.100)).

- Mer information om ytterligare portar som du kan behöva öppna finns i avsnittet **SQL Database: utanför vs i** [portar utöver 1433 för ADO.NET 4,5 och SQL Database](adonet-v12-develop-direct-route-ports.md)

- En översikt över Azure SQL Database-anslutningar finns i [arkitektur för Azure SQL-anslutning](connectivity-architecture.md)

- En översikt över Azure SQL Database säkerhet finns i [skydda databasen](security-overview.md)

<!--Image references-->
[1]: media/quickstart-create-single-database/new-server2.png
[2]: media/quickstart-create-single-database/manage-server-firewall.png
