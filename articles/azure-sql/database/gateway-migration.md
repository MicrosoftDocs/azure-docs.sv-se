---
title: Meddelande om gatewaytrafikmigrering
description: Artikeln innehåller meddelanden till användare om migrering av IP Azure SQL Database-adresser för gateway
services: sql-database
ms.service: sql-db-mi
ms.subservice: service
ms.custom: sqldbrb=1
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: vanto
ms.date: 07/01/2019
ms.openlocfilehash: 07611a3620a2fd8efe0da075b03b55a5be3a5be9
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107505385"
---
# <a name="azure-sql-database-traffic-migration-to-newer-gateways"></a>Azure SQL Database trafikmigrering till nyare gatewayer
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

I och med att Azure-infrastrukturen förbättras uppdaterar Microsoft regelbundet maskinvaran för att säkerställa att vi ger bästa möjliga kundupplevelse. Under de kommande månaderna planerar vi att lägga till gatewayer som bygger på nyare maskinvarugenerationer, migrera trafik till dem och så småningom inaktivera gatewayer som bygger på äldre maskinvara i vissa regioner.  

Kunder meddelas via service health-meddelanden i förväg om eventuella ändringar av gatewayer som är tillgängliga i varje region. Kunder kan [använda Azure Portal för att konfigurera aktivitetsloggaviseringar.](../../service-health/alerts-activity-log-service-notifications-portal.md)

Den senaste informationen finns i tabellen med IP-Azure SQL Database [gateway.](connectivity-architecture.md#gateway-ip-addresses)

## <a name="status-updates"></a>Statusuppdateringar

# <a name="in-progress"></a>[Pågår](#tab/in-progress-ip)
## <a name="may-2021"></a>Maj 2021
Nya SQL Gateways läggs till i följande regioner:
- Storbritannien, södra: 51.140.144.36, 51.105.72.32  
- USA, västra centrala: 13.71.193.32, 13.71.193.33 

Denna SQL Gateway ska börja acceptera kundtrafik den 17 maj 2021.

## <a name="april-2021"></a>April 2021
Nya SQL Gateways läggs till i följande regioner:
- USA, östra 2: 40.70.144.193

Denna SQL Gateway ska börja acceptera kundtrafik den 30 april 2021.

Nya SQL Gateways läggs till i följande regioner:
- Mellanöstern: 51.120.96.33
- Asien, sydöstra: 13.67.16.193
- Sydafrika, norra: 102.133.152.32
- Sydkorea, södra: 52.231.151.96
- Norra centrala: USA 52.162.105.9
- Australien, sydöstra: 13.77.49.32 

Dessa SQL Gateways börjar acceptera kundtrafik den 5 april 2021.

## <a name="march-2021"></a>Mars 2021
Följande SQL-gatewayer i flera regioner håller på att inaktiveras:
- Brasilien, södra: 104.41.11.5
- Asien, östra: 191.234.2.139
- USA, östra: 191.238.6.43
- Japan, östra: 191.237.240.43
- Japan, västra: 191.238.68.11
- Europa, norra: 191.235.193.75
- USA, södra centrala: 23.98.162.75
- Sydostasien: 23.100.117.95
- Europa, västra: 191.237.232.75
- USA, västra: 23.99.34.75

Ingen kundpåverkan förväntas eftersom dessa gatewayer (som körs på äldre maskinvara) inte dirigerar någon kundtrafik. IP-adresserna för dessa gatewayer ska inaktiveras den 15 mars 2021.

# <a name="completed"></a>[Slutfört](#tab/completed-ip)
Följande gateway-migreringar är slutförda: 

## <a name="february-2021"></a>Februari 2021
Nya SQL Gateways läggs till i följande regioner:

- USA, centrala: 13.89.169.20

Dessa SQL Gateways bör börja acceptera kundtrafik den 28 februari 2021.

## <a name="january-2021"></a>Januari 2021
Nya SQL Gateways läggs till i följande regioner:

- Australien, centrala: 20.36.104.6 , 20.36.104.7 
- Australien, centrala 2: 20.36.112.6 
- Brasilien, södra: 191.234.144.16 ,191.234.152.3 
- Kanada, östra: 40.69.105.9 ,40.69.105.10
- Indien, centrala: 104.211.86.30 , 104.211.86.31 
- Asien, östra: 13.75.32.14 
- Frankrike, centrala: 40.79.137.8, 40.79.145.12 
- Frankrike, södra: 40.79.177.10 ,40.79.177.12
- Sydkorea, centrala: 52.231.17.22 ,52.231.17.23
- Indien, västra: 104.211.144.4

Dessa SQL Gateways börjar acceptera kundtrafik den 31 januari 2021.



### <a name="october-2020"></a>Oktober 2020

Nya SQL Gateways läggs till i följande regioner:

- Tyskland, västra centrala: 51.116.240.0, 51.116.248.0

Dessa SQL Gateways bör börja acceptera kundtrafik den 12 oktober 2020. 

### <a name="september-2020"></a>September 2020
Nya SQL Gateways läggs till i följande regioner. Dessa SQL Gateways ska börja acceptera kundtrafik den **15 september 2020:**

- Australien, sydöstra: 13.77.48.10
- Kanada, östra: 40.86.226.166, 52.242.30.154
- Storbritannien, södra: 51.140.184.11, 51.105.64.0

Befintliga SQL Gateways börjar ta emot trafik i följande regioner. Dessa SQL Gateways ska börja ta emot kundtrafik **den 15 september 2020:**

- Australien, sydöstra: 191.239.192.109 och 13.73.109.251
- USA, centrala: 13.67.215.62, 52.182.137.15, 23.99.160.139, 104.208.16.96 och 104.208.21.1
- Asien, östra: 191.234.2.139, 52.175.33.150 och 13.75.32.4
- USA, östra: 40.121.158.30, 40.79.153.12, 191.238.6.43 och 40.78.225.32
- USA, östra 2: 40.79.84.180, 52.177.185.181, 52.167.104.0, 191.239.224.107 och 104.208.150.3
- Frankrike, centrala: 40.79.137.0 och 40.79.129.1
- Japan, västra: 104.214.148.156, 40.74.100.192, 191.238.68.11 och 40.74.97.10
- USA, norra centrala: 23.96.178.199, 23.98.55.75 och 52.162.104.33
- Sydostasien: 104.43.15.0, 23.100.117.95 och 40.78.232.3
- USA, västra: 104.42.238.205, 23.99.34.75 och 13.86.216.196

Nya SQL Gateways läggs till i följande regioner. Dessa SQL Gateways ska börja acceptera kundtrafik den **10 september 2020:**

- USA, västra centrala: 13.78.248.43 
- Sydafrika, norra: 102.133.120.2  

Nya SQL Gateways läggs till i följande regioner. Dessa SQL Gateways ska börja acceptera kundtrafik den **1 september 2020:**

- Europa, norra: 13.74.104.113 
- USA, västra 2: 40.78.248.10 
- Europa, västra: 52.236.184.163 
- USA, södra centrala: 20.45.121.1, 20.49.88.1 

Befintliga SQL Gateways börjar acceptera trafik i följande regioner. Dessa SQL Gateways ska börja acceptera kundtrafik den **1 september 2020:**
- Japan, östra: 40.79.184.8, 40.79.192.5


### <a name="august-2020"></a>Augusti 2020

Nya SQL Gateways läggs till i följande regioner:

- Australien, östra: 13.70.112.9
- Kanada, centrala: 52.246.152.0, 20.38.144.1 
- USA, västra 2: 40.78.240.8

Dessa SQL Gateways bör börja acceptera kundtrafik den 10 augusti 2020. 

### <a name="october-2019"></a>Oktober 2019
- Brasilien, södra
- USA, västra
- Europa, västra
- East US
- Central US
- Sydostasien
- USA, södra centrala
- Europa, norra
- USA, norra centrala
- Japan, västra
- Japan, östra
- USA, östra 2
- Asien, östra

---

## <a name="impact-of-this-change"></a>Effekten av den här ändringen

Trafikmigrering kan ändra den offentliga IP-adress som DNS matchar för din databas i Azure SQL Database.
Du kan påverkas om du:

- Hårdkodade IP-adressen för en viss gateway i din lokala brandvägg
- Ha några undernät som använder Microsoft.SQL som tjänstslutpunkt, men de kan inte kommunicera med gatewayens IP-adresser
- Använd [zonredundant konfiguration för nivån generell användning](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)
- Använda [zonredundant konfiguration för premium-& affärskritiska nivåer](high-availability-sla.md#premium-and-business-critical-service-tier-zone-redundant-availability)

Du påverkas inte om du har:
 
- Omdirigering som anslutningsprincipen
- Anslutningar till SQL Database inifrån Azure och med tjänsttaggar
- Anslutningar som görs med JDBC-drivrutinen som stöds för SQL Server påverkar inte. Information om JDBC-versioner som stöds [finns i Ladda ned Microsoft JDBC-drivrutin för SQL Server](/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server).

## <a name="what-to-do-you-do-if-youre-affected"></a>Vad du gör om du påverkas

Vi rekommenderar att du tillåter utgående trafik till IP-adresser för alla [gateway-IP-adresser](connectivity-architecture.md#gateway-ip-addresses) i regionen på TCP-port 1433 och portintervallet 11000–11999. Den här rekommendationen gäller för klienter som ansluter från lokala platser och de som ansluter via tjänstslutpunkter. Mer information om portintervall finns i [Anslutningsprincip.](connectivity-architecture.md#connection-policy)

Anslutningar som görs från program som använder Microsoft JDBC-drivrutinen under version 4.0 kan misslyckas med certifikatverifieringen. Lägre versioner av Microsoft JDBC förlitar sig på eget namn (CN) i fältet Ämne för certifikatet. Du kan åtgärda detta genom att se till att egenskapen hostNameInCertificate är inställd på *.database.windows.net. Mer information om hur du anger egenskapen hostNameInCertificate finns i [Ansluta med kryptering.](/sql/connect/jdbc/connecting-with-ssl-encryption)

Om ovanstående åtgärd inte fungerar kan du skicka en supportbegäran för SQL Database eller SQL Managed Instance med hjälp av följande URL: https://aka.ms/getazuresupport

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [Azure SQL anslutningsarkitektur](connectivity-architecture.md)