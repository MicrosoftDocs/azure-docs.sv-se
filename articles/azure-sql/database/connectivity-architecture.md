---
title: Arkitektur för Azure SQL Database anslutning
description: I det här dokumentet beskrivs Azure SQL Database anslutnings arkitektur för databas anslutningar från Azure eller utanför Azure.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: fasttrack-edit, sqldbrb=1
titleSuffix: Azure SQL Database and Azure Synapse Analytics
ms.devlang: ''
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: sstein, vanto
ms.date: 01/25/2021
ms.openlocfilehash: c589f270fe623f1e4e4bbde4fd353ba95249d1b5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102426515"
---
# <a name="azure-sql-database-and-azure-synapse-analytics-connectivity-architecture"></a>Anslutningsarkitektur för Azure SQL Database och Azure Synapse Analytics
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

Den här artikeln förklarar arkitekturen i olika komponenter som dirigerar nätverks trafik till en server i Azure SQL Database-eller Azure Synapse-analys. Den förklarar också olika anslutnings principer och hur de påverkar klienter som ansluter från Azure och klienter som ansluter utanför Azure.

> [!IMPORTANT]
> Den här artikeln gäller *inte* för **Azure SQL-hanterade instanser**. Se [anslutnings arkitekturen för en hanterad instans](../managed-instance/connectivity-architecture-overview.md).

## <a name="connectivity-architecture"></a>Anslutningsarkitektur

Följande diagram ger en översikt över anslutnings arkitekturen på hög nivå.

![Diagram som visar en översikt över anslutnings arkitekturen på hög nivå.](./media/connectivity-architecture/connectivity-overview.png)

Följande steg beskriver hur en anslutning upprättas till Azure SQL Database:

- Klienter ansluter till gatewayen, som har en offentlig IP-adress och lyssnar på port 1433.
- Gatewayen, beroende på principen gällande anslutning, omdirigerar eller proxyservrarar trafiken till rätt databas kluster.
- I databas klustret vidarebefordras trafiken till rätt databas.

## <a name="connection-policy"></a>Anslutnings princip

Servrar i SQL Database och Azure Synapse stöder följande tre alternativ för serverns anslutnings princip inställning:

- **Omdirigera (rekommenderas):** Klienter upprättar anslutningar direkt till noden som är värd för databasen, vilket leder till minskad svars tid och bättre data flöde. För att anslutningar ska kunna använda det här läget måste klienterna:
  - Tillåt utgående kommunikation från klienten till alla Azure SQL IP-adresser i regionen på portarna i intervallet 11000 11999. Använd tjänst taggarna för SQL för att göra det enklare att hantera.  
  - Tillåt utgående kommunikation från klienten till Azure SQL Database Gateway-IP-adresser på port 1433.

- **Proxy:** I det här läget är alla anslutningar via proxy via Azure SQL Database gatewayer, vilket leder till ökad latens och minskat data flöde. För att anslutningar ska kunna använda det här läget måste klienterna tillåta utgående kommunikation från klienten till Azure SQL Database Gateway-IP-adresser på port 1433.

- **Standard:** Detta är anslutnings principen som används på alla servrar när den har skapats, såvida du inte uttryckligen ändrar anslutnings principen till antingen `Proxy` eller `Redirect` . Standard principen är `Redirect` för alla klient anslutningar som har sitt ursprung i Azure (till exempel från en virtuell Azure-dator) och `Proxy` för alla klient anslutningar som kommer från utsidan (till exempel anslutningar från din lokala arbets Station).

Vi rekommenderar starkt `Redirect` anslutnings principen över `Proxy` anslutnings principen för den lägsta svars tiden och det högsta data flödet. Du måste dock uppfylla de ytterligare kraven för att tillåta nätverks trafik enligt beskrivningen ovan. Om klienten är en virtuell Azure-dator kan du göra detta med hjälp av nätverks säkerhets grupper (NSG) med [service märken](../../virtual-network/network-security-groups-overview.md#service-tags). Om klienten ansluter från en lokal arbets Station kan du behöva arbeta med nätverks administratören för att tillåta nätverks trafik via företags brand väggen.

## <a name="connectivity-from-within-azure"></a>Anslutning inifrån Azure

Om du ansluter inifrån Azure får anslutningarna som standard en anslutnings princip `Redirect` . En princip på `Redirect` innebär att när TCP-sessionen har upprättats till Azure SQL Database omdirigeras-klientsessionen sedan till rätt databas kluster med en ändring av den virtuella mål-IP-adressen från den Azure SQL Database gatewayen till klustret. Därefter flödar alla efterföljande paket direkt till klustret och hoppar över Azure SQL Database Gateway. Följande diagram illustrerar det här trafikflödet.

![arkitektur översikt](./media/connectivity-architecture/connectivity-azure.png)

## <a name="connectivity-from-outside-of-azure"></a>Anslutning utanför Azure

Om du ansluter från en plats utanför Azure har anslutningarna som standard en anslutnings princip `Proxy` . En princip i `Proxy` innebär att TCP-sessionen upprättas via Azure SQL Database gateway och alla efterföljande paket flöden via gatewayen. Följande diagram illustrerar det här trafikflödet.

![Diagram som visar hur TCP-sessionen upprättas via Azure SQL Database gateway och alla efterföljande paket flöden via gatewayen.](./media/connectivity-architecture/connectivity-onprem.png)

> [!IMPORTANT]
> Öppna även TCP-portarna 1434 och 14000-14999 för att aktivera [anslutning med DAC](/sql/database-engine/configure-windows/diagnostic-connection-for-database-administrators#connecting-with-dac)

## <a name="gateway-ip-addresses"></a>IP-adresser för gateway

I tabellen nedan visas IP-adresserna för gatewayer per region. Om du vill ansluta till SQL Database eller Azure-Synapse måste du tillåta nätverks trafik till och från **alla** gatewayer i regionen.

Information om hur trafiken ska migreras till nya gateways i vissa regioner finns i följande artikel: [Azure SQL Database trafikmigrering till nyare gateways](gateway-migration.md)

| Region namn          | IP-adresser för gateway |
| --- | --- |
| Australien, centrala    | 20.36.105.0, 20.36.104.6, 20.36.104.7 |
| Australien, centrala 2   | 20.36.113.0, 20.36.112.6 |
| Australien, östra       | 13.75.149.87, 40.79.161.1, 13.70.112.9 |
| Sydöstra Australien | 191.239.192.109, 13.73.109.251, 13.77.48.10, 13.77.49.32 |
| Brasilien, södra         | 191.233.200.14, 191.234.144.16, 191.234.152.3 |
| Kanada, centrala       | 40.85.224.249, 52.246.152.0, 20.38.144.1 |
| Kanada, östra          | 40.86.226.166, 52.242.30.154, 40.69.105.9 , 40.69.105.10 |
| Central US           | 13.67.215.62, 52.182.137.15, 23.99.160.139, 104.208.16.96, 104.208.21.1, 13.89.169.20 |
| Kina, östra           | 139.219.130.35     |
| Kina, östra 2         | 40.73.82.1         |
| Kina, norra          | 139.219.15.17      |
| Kina, norra 2        | 40.73.50.0         |
| Asien, östra            | 52.175.33.150, 13.75.32.4, 13.75.32.14 |
| East US              | 40.121.158.30, 40.79.153.12, 40.78.225.32 |
| USA, östra 2            | 40.79.84.180, 52.177.185.181, 52.167.104.0, 191.239.224.107, 104.208.150.3 |
| Frankrike, centrala       | 40.79.137.0, 40.79.129.1, 40.79.137.8, 40.79.145.12 |
| Frankrike, södra         | 40.79.177.0, 40.79.177.10 ,40.79.177.12 |
| Tyskland, centrala      | 51.4.144.100       |
| Tyskland, norra öst   | 51.5.144.179       |
| Tyskland, västra centrala | 51.116.240.0, 51.116.248.0, 51.116.152.0 |
| Indien, centrala        | 104.211.96.159, 104.211.86.30 , 104.211.86.31 |
| Södra Indien          | 104.211.224.146    |
| Indien, västra           | 104.211.160.80, 104.211.144.4 |
| Japan, östra           | 13.78.61.196, 40.79.184.8, 13.78.106.224, 40.79.192.5 |
| Japan, västra           | 104.214.148.156, 40.74.100.192, 40.74.97.10 |
| Sydkorea, centrala        | 52.231.32.42, 52.231.17.22 ,52.231.17.23 |
| Sydkorea, södra          | 52.231.200.86, 52.231.151.96 |
| USA, norra centrala     | 23.96.178.199, 23.98.55.75, 52.162.104.33, 52.162.105.9 |
| Europa, norra         | 40.113.93.91, 52.138.224.1, 13.74.104.113 |
| Östra Norge          | 51.120.96.0, 51.120.96.33 |
| Norge, väst          | 51.120.216.0       |
| Sydafrika, norra   | 102.133.152.0, 102.133.120.2, 102.133.152.32 |
| Sydafrika, västra    | 102.133.24.0       |
| USA, södra centrala     | 13.66.62.124, 104.214.16.32, 20.45.121.1, 20.49.88.1   |
| Sydostasien      | 104.43.15.0, 40.78.232.3, 13.67.16.193 |
| Schweiz, norra    | 51.107.56.0, 51.107.57.0 |
| Schweiz, västra     | 51.107.152.0, 51.107.153.0 |
| Förenade Arabemiraten Central          | 20.37.72.64        |
| Förenade Arabemiraten, norra            | 65.52.248.0        |
| Storbritannien, södra             | 51.140.184.11, 51.105.64.0 |
| Storbritannien, västra              | 51.141.8.11        |
| USA, västra centrala      | 13.78.145.25, 13.78.248.43        |
| Europa, västra          | 40.68.37.158, 104.40.168.105, 52.236.184.163  |
| USA, västra              | 104.42.238.205, 13.86.216.196   |
| USA, västra 2            | 13.66.226.202, 40.78.240.8, 40.78.248.10  |
| USA, västra 2            | 13.66.226.202, 40.78.240.8, 40.78.248.10  |
|                      |                    |


## <a name="next-steps"></a>Nästa steg

- Information om hur du ändrar Azure SQL Database anslutnings princip för en server finns i avsnittet om att ansluta [-princip](/cli/azure/sql/server/conn-policy).
- Information om hur du Azure SQL Database anslutnings beteende för klienter som använder ADO.NET 4,5 eller en senare version finns i [portar bortom 1433 för ADO.NET 4,5](adonet-v12-develop-direct-route-ports.md).
- Allmän översikts information om program utveckling finns i [Översikt över SQL Database program utveckling](develop-overview.md).
