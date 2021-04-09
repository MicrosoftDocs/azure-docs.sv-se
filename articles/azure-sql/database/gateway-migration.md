---
title: Meddelande om migrering av Gateway-trafik
description: Artikeln innehåller information om migrering av Azure SQL Database Gateway-IP-adresser
services: sql-database
ms.service: sql-db-mi
ms.subservice: service
ms.custom: sqldbrb=1
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: vanto
ms.date: 07/01/2019
ms.openlocfilehash: 62e2306abc2d3132651fb40aab8c2f5b4d5043f4
ms.sourcegitcommit: edc7dc50c4f5550d9776a4c42167a872032a4151
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105960885"
---
# <a name="azure-sql-database-traffic-migration-to-newer-gateways"></a>Azure SQL Database trafikmigrering till nyare gateways
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

När Azure-infrastrukturen förbättras kommer Microsoft regelbundet att uppdatera maskin varan så att vi ger bästa möjliga kund upplevelse. Under de kommande månaderna planerar vi att lägga till gatewayer som skapats på nyare maskin varu generationer, migrera trafik till dem och sedan göra inprovisionade gatewayer byggda på äldre maskin vara i vissa regioner.  

Kunder kommer att meddelas via aviseringar på tjänstens hälso tillstånd i förväg om eventuella ändringar av gatewayerna som är tillgängliga i varje region. Kunder kan [använda Azure Portal för att ställa in aktivitets logg aviseringar](../../service-health/alerts-activity-log-service-notifications-portal.md).

Den senaste informationen kommer att behållas i tabellen [Azure SQL Database Gateway IP-adresser](connectivity-architecture.md#gateway-ip-addresses) .

## <a name="status-updates"></a>Status uppdateringar

# <a name="in-progress"></a>[Pågår](#tab/in-progress-ip)

## <a name="april-2021"></a>April 2021
Nya SQL-gatewayer läggs till i följande regioner:
- USA, östra 2:40.70.144.193 denna SQL-gateway ska börja acceptera kund trafik den 30 april 2021.

Nya SQL-gatewayer läggs till i följande regioner:
- Norge, öst: 51.120.96.33
- Asien, sydöstra: 13.67.16.193
- Södra Afrika: 102.133.152.32
- Republiken Korea: 52.231.151.96
- Norra centrala USA: US 52.162.105.9
- Sydöstra Australien: 13.77.49.32 

Dessa SQL-gatewayer ska börja acceptera kund trafik den 5 april 2021.

## <a name="march-2021"></a>Mars 2021
Följande SQL-gatewayer i flera regioner håller på att inaktive ras:

- Södra Brasilien: 104.41.11.5
- Asien, östra: 191.234.2.139
- USA, östra: 191.238.6.43
- Japan, öst: 191.237.240.43
- Västra Japan: 191.238.68.11
- Nord Europa: 191.235.193.75
- Södra centrala USA: 23.98.162.75
- Sydostasien: 23.100.117.95
- Västeuropa: 191.237.232.75
- Västra USA: 23.99.34.75

Ingen kund påverkan förväntas eftersom dessa gatewayer (som körs på äldre maskin vara) inte dirigerar någon kund trafik. IP-adresserna för dessa gateways måste inaktive ras den 15 mars 2021.

## <a name="february-2021"></a>Februari 2021
Nya SQL-gatewayer läggs till i följande regioner:

- Centrala USA: 13.89.169.20

Dessa SQL-gatewayer ska börja acceptera kund trafik den 28 februari 2021.

## <a name="january-2021"></a>Januari 2021
Nya SQL-gatewayer läggs till i följande regioner:

- Australien, centrala: 20.36.104.6, 20.36.104.7 
- Australien, centrala 2:20.36.112.6 
- Södra Brasilien: 191.234.144.16, 191.234.152.3 
- Kanada, öst: 40.69.105.9, 40.69.105.10
- Indien, centrala: 104.211.86.30, 104.211.86.31 
- Asien, östra: 13.75.32.14 
- Centrala Frankrike: 40.79.137.8, 40.79.145.12 
- Frankrike, södra: 40.79.177.10, 40.79.177.12
- Korea, centrala: 52.231.17.22, 52.231.17.23
- Indien, väst: 104.211.144.4

Dessa SQL-gatewayer ska börja acceptera kund trafik den 31 januari 2021.

# <a name="completed"></a>[Slutfört](#tab/completed-ip)
Följande Gateway-migreringar har slutförts: 

### <a name="october-2020"></a>Oktober 2020

Nya SQL-gatewayer läggs till i följande regioner:

- Tyskland, västra centrala: 51.116.240.0, 51.116.248.0

Dessa SQL-gatewayer ska börja acceptera kund trafik den 12 oktober 2020. 

### <a name="september-2020"></a>September 2020
Nya SQL-gatewayer läggs till i följande regioner. Dessa SQL-gatewayer ska börja acceptera kund trafik den **15 September 2020**:

- Australien, sydöstra: 13.77.48.10
- Kanada, öst: 40.86.226.166, 52.242.30.154
- Storbritannien, södra: 51.140.184.11, 51.105.64.0

Befintliga SQL-gatewayer börjar att acceptera trafik i följande regioner. Dessa SQL-gatewayer ska börja acceptera kund trafik den **15 September 2020** :

- Australien, sydöstra: 191.239.192.109 och 13.73.109.251
- Centrala USA: 13.67.215.62, 52.182.137.15, 23.99.160.139, 104.208.16.96 och 104.208.21.1
- Asien, östra: 191.234.2.139, 52.175.33.150 och 13.75.32.4
- USA, östra: 40.121.158.30, 40.79.153.12, 191.238.6.43 och 40.78.225.32
- USA, östra 2:40.79.84.180, 52.177.185.181, 52.167.104.0, 191.239.224.107 och 104.208.150.3
- Centrala Frankrike: 40.79.137.0 och 40.79.129.1
- Västra Japan: 104.214.148.156, 40.74.100.192, 191.238.68.11 och 40.74.97.10
- Norra centrala USA: 23.96.178.199, 23.98.55.75 och 52.162.104.33
- Sydostasien: 104.43.15.0, 23.100.117.95 och 40.78.232.3
- Västra USA: 104.42.238.205, 23.99.34.75 och 13.86.216.196

Nya SQL-gatewayer läggs till i följande regioner. Dessa SQL-gatewayer ska börja acceptera kund trafik den **10 September 2020**:

- Västra centrala USA: 13.78.248.43 
- Södra Afrika: 102.133.120.2  

Nya SQL-gatewayer läggs till i följande regioner. Dessa SQL-gatewayer ska börja acceptera kund trafik den **1 September 2020**:

- Nord Europa: 13.74.104.113 
- Västra 2; USA: 40.78.248.10 
- Västeuropa: 52.236.184.163 
- Södra centrala USA: 20.45.121.1, 20.49.88.1 

Befintliga SQL-gatewayer börjar att acceptera trafik i följande regioner. Dessa SQL-gatewayer ska börja acceptera kund trafik den **1 September 2020**:
- Japan, öst: 40.79.184.8, 40.79.192.5


### <a name="august-2020"></a>Augusti 2020

Nya SQL-gatewayer läggs till i följande regioner:

- Östra Australien: 13.70.112.9
- Kanada, centrala: 52.246.152.0, 20.38.144.1 
- USA, västra 2:40.78.240.8

Dessa SQL-gatewayer ska börja acceptera kund trafik den 10 augusti 2020. 

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

## <a name="impact-of-this-change"></a>Effekt av den här ändringen

Trafik migreringen kan ändra den offentliga IP-adress som DNS matchar för din databas i Azure SQL Database.
Du kan påverkas om du:

- Hårdkoda IP-adressen för en viss gateway i din lokala brand vägg
- Ha undernät som använder Microsoft. SQL som en tjänst slut punkt men inte kan kommunicera med IP-adresser för gateway
- Använd [zonens redundanta konfiguration för generell användnings nivå](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)
- Använd [zonens redundanta konfiguration för premium & affärs kritiska nivåer](high-availability-sla.md#premium-and-business-critical-service-tier-zone-redundant-availability)

Du påverkas inte om du har:
 
- Omdirigering som anslutnings princip
- Anslutningar till SQL Database inifrån Azure och med service märken
- Anslutningar som görs med stödda versioner av JDBC-drivrutinen för SQL Server kommer inte att se någon påverkan. Information om vilka JDBC-versioner som stöds finns i [Hämta Microsoft JDBC-drivrutin för SQL Server](/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server).

## <a name="what-to-do-you-do-if-youre-affected"></a>Vad du gör om du påverkas

Vi rekommenderar att du tillåter utgående trafik till IP-adresser för alla [Gateway-IP-adresser](connectivity-architecture.md#gateway-ip-addresses) i regionen på TCP-port 1433 och port intervall 11000-11999. Den här rekommendationen gäller för klienter som ansluter lokalt och som också ansluter via tjänst slut punkter. Mer information om Port intervall finns i [anslutnings princip](connectivity-architecture.md#connection-policy).

Anslutningar som görs från program som använder Microsoft JDBC driv rutinen under version 4,0 kan inte verifiera certifikat verifiering. Lägre versioner av Microsoft JDBC förlitar sig på eget namn (CN) i certifikatets ämnes fält. Minskningen är att se till att egenskapen hostNameInCertificate är inställd på *. database.windows.net. Mer information om hur du ställer in egenskapen hostNameInCertificate finns i [ansluta med kryptering](/sql/connect/jdbc/connecting-with-ssl-encryption).

Om ovanstående minskning inte fungerar kan du skicka en supportbegäran för SQL Database-eller SQL-hanterad instans med hjälp av följande URL: https://aka.ms/getazuresupport

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [Azure SQL Connectivity-arkitekturen](connectivity-architecture.md)