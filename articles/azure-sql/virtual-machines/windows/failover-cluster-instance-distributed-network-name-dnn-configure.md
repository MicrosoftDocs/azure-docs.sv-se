---
title: Konfigurera DNN för instansen av redundanskluster
description: Lär dig hur du konfigurerar ett distribuerat nätverks namn (DNN) för att dirigera trafik till din SQL Server på Azure VM-redundanskluster (FCI).
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: jroth
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 10/07/2020
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 8549592ace00e712929ebc76045a32531b9db659
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "97358324"
---
# <a name="configure-a-dnn-for-failover-cluster-instance"></a>Konfigurera en DNN för instansen av redundanskluster
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

På Azure Virtual Machines dirigerar det distribuerade nätverks namnet (DNN) trafik till lämplig klustrad resurs. Det ger ett enklare sätt att ansluta till SQL Server redundans för kluster instans (FCI) än det virtuella nätverks namnet (VNN), utan att det behövs något Azure Load Balancer. 

I den här artikeln lär du dig att konfigurera en DNN-resurs för att dirigera trafik till din kluster instans för växling vid fel med SQL Server på virtuella Azure-datorer för hög tillgänglighet och haveri beredskap (HADR). 

DNN-funktionen är för närvarande bara tillgänglig på SQL Server 2019 CU2 och senare på Windows Server 2016 och senare. 

För ett alternativt anslutnings alternativ bör du överväga ett [virtuellt nätverks namn och Azure Load Balancer](failover-cluster-instance-vnn-azure-load-balancer-configure.md) i stället. 

## <a name="overview"></a>Översikt

Det distribuerade nätverks namnet (DNN) ersätter det virtuella nätverks namnet (VNN) som anslutnings punkt när det används med en [Always on Cluster-instans på SQL Server virtuella datorer](failover-cluster-instance-overview.md). Detta eliminerar behovet av att en Azure Load Balancer dirigera trafik till VNN, vilket fören klar distributionen, underhållet och förbättringen av redundansväxlingen. 

Med en FCI-distribution finns VNN fortfarande kvar, men klienten ansluter till DNN DNS-namn i stället för VNN namn. 

## <a name="prerequisites"></a>Förutsättningar 

Innan du slutför stegen i den här artikeln bör du redan ha:

- SQL Server 2019 på CU2 eller senare, på Windows Server 2016 och senare
- Beslut om det distribuerade nätverks namnet är det lämpliga [anslutnings alternativet för din hadr-lösning](hadr-cluster-best-practices.md#connectivity).
- Konfigurerat [instanserna av klustret för växling vid fel](failover-cluster-instance-overview.md). 
- Den senaste versionen av [PowerShell](/powershell/azure/install-az-ps)har installerats. 

## <a name="create-dnn-resource"></a>Skapa DNN-resurs 

DNN-resursen skapas i samma kluster grupp som SQL Server-FCI. Använd PowerShell för att skapa DNN-resursen i kluster gruppen FCI. 

Följande PowerShell-kommando lägger till en DNN-resurs till kluster gruppen SQL Server FCI med resurs namnet `<dnnResourceName>` . Resurs namnet används för att unikt identifiera en resurs. Använd ett som passar dig och är unikt i klustret. Resurs typen måste vara `Distributed Network Name` . 

`-Group`Värdet måste vara namnet på den kluster grupp som motsvarar den SQL Server FCI där du vill lägga till det distribuerade nätverks namnet. För en standard instans är det typiska formatet `SQL Server (MSSQLSERVER)` . 


```powershell
Add-ClusterResource -Name <dnnResourceName> `
-ResourceType "Distributed Network Name" -Group "<WSFC role of SQL server instance>"
```

Om du till exempel vill skapa en DNN `dnn-demo` -resurs för en standard SQL Server FCI använder du följande PowerShell-kommando:

```powershell
Add-ClusterResource -Name dnn-demo `
-ResourceType "Distributed Network Name" -Group "SQL Server (MSSQLSERVER)"

```

## <a name="set-cluster-dnn-dns-name"></a>Ange kluster DNN DNS-namn

Ange DNS-namnet för DNN-resursen i klustret. Klustret använder sedan det här värdet för att dirigera trafik till den nod som för närvarande är värd för SQL Server FCI. 

Klienter använder DNS-namnet för att ansluta till SQL Server FCI. Du kan välja ett unikt värde. Eller, om du redan har en befintlig FCI och inte vill uppdatera klient anslutnings strängarna, kan du konfigurera DNN för att använda de aktuella VNN som klienterna redan använder. För att göra det måste du [byta namn på VNN](#rename-the-vnn) innan du anger DNN i DNS.

Använd det här kommandot för att ange DNS-namnet för din DNN: 

```powershell
Get-ClusterResource -Name <dnnResourceName> | `
Set-ClusterParameter -Name DnsName -Value <DNSName>
```

`DNSName`Värdet är det som klienter använder för att ansluta till SQL Server FCI. Använd till exempel följande PowerShell-kommando för att klienter ska kunna ansluta till `FCIDNN` :

```powershell
Get-ClusterResource -Name dnn-demo | `
Set-ClusterParameter -Name DnsName -Value FCIDNN
```

Klienterna kommer nu att anges `FCIDNN` i anslutnings strängen när de ansluter till SQL Server FCI. 

   > [!WARNING]
   > Ta inte bort det aktuella virtuella nätverks namnet (VNN) eftersom det är en nödvändig komponent i FCI-infrastrukturen. 


### <a name="rename-the-vnn"></a>Byt namn på VNN 

Om du har ett befintligt virtuellt nätverks namn och du vill att klienter ska fortsätta att använda det här värdet för att ansluta till SQL Server FCI, måste du byta namn på den aktuella VNN till ett plats hållarens värde. När den aktuella VNN har bytt namn kan du ange DNS-värdet för DNN till VNN. 

Vissa begränsningar gäller när du byter namn på VNN. Mer information finns i [byta namn på en FCI](/sql/sql-server/failover-clusters/install/rename-a-sql-server-failover-cluster-instance).

Om du inte behöver använda den aktuella VNN för ditt företag kan du hoppa över det här avsnittet. När du har bytt namn på VNN [anger du klustrets DNN DNS-namn](#set-cluster-dnn-dns-name). 

   
## <a name="set-dnn-resource-online"></a>Ange DNN-resursen online

När din DNN-resurs har fått rätt namn och du har angett värdet DNS-namn i klustret, använder du PowerShell för att ange DNN-resursen online i klustret: 

```powershell
Start-ClusterResource -Name <dnnResourceName>
```

Om du till exempel vill starta DNN `dnn-demo` -resursen använder du följande PowerShell-kommando: 

```powershell
Start-ClusterResource -Name dnn-demo
```

## <a name="configure-possible-owners"></a>Konfigurera möjliga ägare

Klustret binder som standard DNS-namnet DNN till alla noder i klustret. Noder i klustret som inte är en del av SQL Server FCI bör dock undantas från listan över möjliga ägare av DNN. 

Följ dessa steg om du vill uppdatera möjliga ägare:

1. Gå till DNN-resursen i Klusterhanteraren för växling vid fel. 
1. Högerklicka på resursen DNN och välj **Egenskaper**. 

   :::image type="content" source="media/hadr-distributed-network-name-dnn-configure/fci-dnn-properties.png" alt-text="Snabb menyn för DNN-resursen med kommandot Egenskaper markerat.":::

1. Avmarkera kryss rutan för alla noder som inte ingår i instansen av redundanskluster. Listan över möjliga ägare för DNN-resursen ska matcha listan över möjliga ägare för den SQL Server instans resursen. Om du till exempel antar att DATA3 inte ingår i FCI, är följande bild ett exempel på att ta bort DATA3 från listan över möjliga ägare för DNN-resursen: 

   :::image type="content" source="media/hadr-distributed-network-name-dnn-configure/clear-check-for-nodes-not-in-fci.png" alt-text="Avmarkera kryss rutan bredvid de noder som inte ingår i FCI för möjliga ägare till DNN-resursen":::

1. Spara inställningarna genom att välja **OK**. 


## <a name="restart-sql-server-instance"></a>Starta om SQL Server instans 

Använd Klusterhanteraren för växling vid fel för att starta om SQL Server-instansen. Följ de här stegen:

1. Gå till din SQL Server-resurs i Klusterhanteraren för växling vid fel.
1. Högerklicka på SQL Server resursen och ta den offline. 
1. När alla associerade resurser är offline högerklickar du på den SQL Server resursen och tar den online igen. 

## <a name="update-connection-string"></a>Uppdatera anslutnings sträng

För att säkerställa snabb anslutning vid redundans lägger `MultiSubnetFailover=True` du till anslutnings strängen om SQL-klientens version är tidigare än 4.6.1. 

Om DNN inte använder den ursprungliga VNN måste dessutom SQL-klienter som ansluter till SQL Server FCI uppdatera sin anslutnings sträng till DNN DNS-namnet. För att undvika det här kravet kan du uppdatera DNS-namnet-värdet till namnet på VNN. Men du måste [ersätta de befintliga VNN med en plats hållare](#rename-the-vnn) först. 

## <a name="test-failover"></a>Redundanstest


Testa redundansväxlingen för den klustrade resursen för att verifiera kluster funktionen. 


Följ dessa steg om du vill testa redundans: 

1. Anslut till en av SQL Server klusternoder med hjälp av RDP.
1. Öppna **Klusterhanteraren för växling vid fel**. Välj **roller**. Lägg märke till vilken nod som äger rollen SQL Server FCI.
1. Högerklicka på rollen SQL Server FCI. 
1. Välj **Flytta** och välj sedan **bästa möjliga nod**.

**Klusterhanteraren för växling vid fel** visar rollen och dess resurser går offline. Resurserna flyttar sedan och kommer tillbaka online på den andra noden.

## <a name="test-connectivity"></a>Testa anslutning

Om du vill testa anslutningen loggar du in på en annan virtuell dator i samma virtuella nätverk. Öppna **SQL Server Management Studio** och anslut till SQL Server-FCI med DNS-namnet DNN.

Om du behöver kan du [hämta SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms).


## <a name="avoid-ip-conflict"></a>Undvik IP-konflikt

Detta är ett valfritt steg för att förhindra att den virtuella IP-adressen (VIP) som används av FCI-resursen tilldelas till en annan resurs i Azure som en dubblett. 

Även om kunderna nu använder DNN för att ansluta till SQL Server FCI, kan inte det virtuella nätverks namnet (VNN) och den virtuella IP-adressen tas bort eftersom de är nödvändiga komponenter i FCI-infrastrukturen. Eftersom det inte längre finns en belastningsutjämnare som reserverar den virtuella IP-adressen i Azure, finns det en risk att en annan resurs i det virtuella nätverket tilldelas samma IP-adress som den virtuella IP-adressen som används av FCI. Detta kan potentiellt leda till en dubblett av IP-konflikter. 

Konfigurera en APIPA-adress eller ett dedikerat nätverkskort för att reservera IP-adressen. 

### <a name="apipa-address"></a>APIPA-adress

Om du vill undvika att använda dubbla IP-adresser konfigurerar du en APIPA-adress (kallas även länk lokal adress). Detta gör du genom att köra följande kommando:

```powershell
Get-ClusterResource "virtual IP address" | Set-ClusterParameter 
    –Multiple @{"Address”=”169.254.1.1”;”SubnetMask”=”255.255.0.0”;"OverrideAddressMatch"=1;”EnableDhcp”=0}
```

I det här kommandot är "virtuell IP-adress" namnet på den klustrade VIP-adressresursen och "169.254.1.1" är APIPA-adressen som valts för VIP-adressen. Välj den adress som passar din verksamhet bäst. Ange `OverrideAddressMatch=1` att IP-adressen ska tillåtas i alla nätverk, inklusive APIPA-adressutrymmet. 

### <a name="dedicated-network-adapter"></a>Dedikerat nätverkskort

Alternativt kan du konfigurera ett nätverkskort i Azure för att reservera IP-adressen som används av den virtuella IP-adressresursen. Detta förbrukar dock adressen i adress utrymmet för under nätet och det finns ytterligare kostnader för att säkerställa att nätverkskortet inte används i något annat syfte.

## <a name="limitations"></a>Begränsningar

- För närvarande stöds endast en DNN med FCI för SQL Server 2019 CU2 och senare på Windows Server 2016 och senare. 
- Det kan vara mer att tänka på när du arbetar med andra SQL Server funktioner och en FCI med en DNN. Mer information finns i [FCI med DNN-interoperabilitet](failover-cluster-instance-dnn-interoperability.md). 

## <a name="next-steps"></a>Nästa steg

Läs mer om hur du SQL Server HADR-funktioner i Azure i [tillgänglighets grupper](availability-group-overview.md) och [instansen av redundanskluster](failover-cluster-instance-overview.md). Du kan också lära dig [metod tips](hadr-cluster-best-practices.md) för att konfigurera din miljö för hög tillgänglighet och haveri beredskap. 


