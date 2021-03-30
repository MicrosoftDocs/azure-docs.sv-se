---
title: Anslutningstyper
titleSuffix: Azure SQL Managed Instance
description: Läs om anslutnings typer för Azure SQL Managed instance
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: vanto
ms.date: 10/07/2019
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: f1c4fe8268d24026609f55d76a102a5c9a4e8295
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91356324"
---
# <a name="azure-sql-managed-instance-connection-types"></a>Anslutningstyper för Azure SQL Managed Instance
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Den här artikeln förklarar hur klienter ansluter till en hanterad Azure SQL-instans beroende på anslutnings typen. Skript exempel för att ändra anslutnings typer anges nedan, tillsammans med överväganden om hur du ändrar standard anslutnings inställningarna.

## <a name="connection-types"></a>Anslutningstyper

Azure SQL Managed instance stöder följande två anslutnings typer:

- **Omdirigera (rekommenderas):** Klienter upprättar anslutningar direkt till noden som är värd för databasen. Om du vill aktivera anslutning med omdirigering måste du öppna brandväggar och nätverkssäkerhetsgrupper (NSG) för att tillåta åtkomst via portarna 1433 och 11000–11999. Paket skickas direkt till databasen och därför får du bättre svarstider och dataflöde än vid omdirigering via proxy.
- **Proxy (standard):** I det här läget använder alla anslutningar en proxy-Gateway-komponent. Om du vill aktivera anslutningar behöver du bara öppna port 1433 för privata nätverk och port 3342 för offentliga anslutningar. Det här läget kan ge längre svarstider och lägre dataflöde beroende på arbetsbelastningen. Vi rekommenderar att du använder policyn omdirigering snarare än proxyanslutning så att du får bästa möjliga svarstider och dataflöde.

## <a name="redirect-connection-type"></a>Anslutnings typ för omdirigering

När TCP-sessionen har upprättats till SQL-motorn i anslutnings typen för omdirigering hämtar klientsessionen den virtuella mål-IP-adressen för den virtuella klusternoden från belastningsutjämnaren. Efterföljande paket flödar direkt till den virtuella klusternoden och kringgår gatewayen. Följande diagram illustrerar det här trafikflödet.

![Diagram visar ett lokalt nätverk med omdirigerade-find-DB ansluten till en gateway i ett virtuellt Azure-nätverk och en omdirigerings fråga som är ansluten till en primär databas i det virtuella nätverket.](./media/connection-types-overview/redirect.png)

> [!IMPORTANT]
> Anslutnings typen för omdirigering fungerar för närvarande endast för en privat slut punkt. Oavsett inställningen för anslutnings typen, kommer anslutningar via den offentliga slut punkten att ske via en proxy.

## <a name="proxy-connection-type"></a>Typ av anslutnings proxy

I anslutnings typen proxy upprättas TCP-sessionen med hjälp av gatewayen och alla efterföljande paket flödar genom den. Följande diagram illustrerar det här trafikflödet.

![Diagram visar ett lokalt nätverk med en proxy som är ansluten till en gateway i ett virtuellt Azure-nätverk. Anslut sedan till en primär databas i det virtuella nätverket.](./media/connection-types-overview/proxy.png)

## <a name="changing-connection-type"></a>Ändrar Anslutnings typ

- **Använda portalen:** Om du vill ändra anslutnings typen med hjälp av Azure Portal öppnar du sidan Virtual Network och använder **anslutnings typs** inställningen för att ändra anslutnings typen och spara ändringarna.

- **Skript för att ändra inställningar för Anslutnings typ med PowerShell:**

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Följande PowerShell-skript visar hur du ändrar anslutnings typen för en hanterad instans till `Redirect` .

```powershell
Install-Module -Name Az
Import-Module Az.Accounts
Import-Module Az.Sql

Connect-AzAccount
# Get your SubscriptionId from the Get-AzSubscription command
Get-AzSubscription
# Use your SubscriptionId in place of {subscription-id} below
Select-AzSubscription -SubscriptionId {subscription-id}
# Replace {rg-name} with the resource group for your managed instance, and replace {mi-name} with the name of your managed instance
$mi = Get-AzSqlInstance -ResourceGroupName {rg-name} -Name {mi-name}
$mi = $mi | Set-AzSqlInstance -ProxyOverride "Redirect" -force
```

## <a name="next-steps"></a>Nästa steg

- [Återställa en databas till SQL-hanterad instans](restore-sample-database-quickstart.md)
- Lär dig hur du [konfigurerar en offentlig slut punkt på SQL-hanterad instans](public-endpoint-configure.md)
- Läs mer om [anslutnings arkitekturen för SQL-hanterade instanser](connectivity-architecture-overview.md)
