---
title: Anslutnings arkitektur – Azure Database for MySQL
description: Beskriver anslutnings arkitekturen för din Azure Database for MySQL-server.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: conceptual
ms.date: 03/16/2020
ms.openlocfilehash: 2a557bb436b3bc10cf83beb450761465b43f621f
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/17/2020
ms.locfileid: "97655364"
---
# <a name="connectivity-architecture-in-azure-database-for-mysql"></a>Anslutnings arkitektur i Azure Database for MySQL
Den här artikeln förklarar Azure Database for MySQL anslutnings arkitektur och hur trafiken dirigeras till din Azure Database for MySQL-instans från klienter både inom och utanför Azure.

## <a name="connectivity-architecture"></a>Anslutningsarkitektur
Anslutning till din Azure Database for MySQL upprättas via en gateway som ansvarar för att dirigera inkommande anslutningar till serverns fysiska plats i våra kluster. Följande diagram illustrerar trafikflödet.

:::image type="content" source="./media/concepts-connectivity-architecture/connectivity-architecture-overview-proxy.png" alt-text="Översikt över anslutnings arkitekturen":::

När klienten ansluter till databasen matchas anslutnings strängen till-servern med IP-adressen för gatewayen. Gatewayen lyssnar på IP-adressen på port 3306. I databas klustret vidarebefordras trafiken till lämpliga Azure Database for MySQL. För att kunna ansluta till servern, t. ex. från företags nätverk, är det därför nödvändigt att öppna **brand väggen på klient sidan så att utgående trafik kan komma åt våra gateways**. Nedan hittar du en fullständig lista över de IP-adresser som används av våra gatewayer per region.

## <a name="azure-database-for-mysql-gateway-ip-addresses"></a>Azure Database for MySQL Gateway-IP-adresser

Gateway-tjänsten finns i en grupp av tillstånds lösa Compute-noder som ligger bakom en IP-adress, som klienten skulle uppnå först vid försök att ansluta till en Azure Database for MySQL-server. 

Som en del av det löpande underhållet av tjänsten kommer vi regelbundet att uppdatera beräknings maskin varan som är värd för gatewayerna för att säkerställa att vi tillhandahåller den säkraste och bästa upplevelsen. När Gateway-maskinvaran uppdateras, skapas en ny instans av Compute-noderna först. Den här nya ringen hanterar trafiken för alla nyskapade Azure Database for MySQL-servrar och den har en annan IP-adress än äldre Gateway-ringar i samma region för att särskilja trafiken. När den nya ringen är helt funktionell, planeras den äldre Gateway-maskinvaran som betjänar befintliga servrar för inaktive ring. Innan kunder som kör sina servrar och ansluter till äldre Gateway-ringar inaktive ras, meddelas du via e-post och i Azure Portal tre månader innan inaktive ring. Inaktive ring av gatewayer kan påverka anslutningen till dina servrar om 

* Du hårdkodar IP-adresser för gatewayen i anslutnings strängen för ditt program. Vi **rekommenderar inte** detta. Du bör använda ett fullständigt kvalificerat domän namn (FQDN) på servern i formatet <servername> . mysql.Database.Azure.com i anslutnings strängen för ditt program. 
* Du uppdaterar inte de nyare Gateway-IP-adresserna i brand väggen på klient sidan så att utgående trafik kan komma åt våra nya gateway-ringar.

I följande tabell visas gatewayens IP-adresser för den Azure Database for MySQL gatewayen för alla data områden. Den senaste informationen om gatewayens IP-adresser för varje region behålls i tabellen nedan. I tabellen nedan representerar kolumnerna följande:

* **IP-adresser för Gateway:** Den här kolumnen visar de aktuella IP-adresserna för de gateways som är värd för den senaste generationen maskin vara. Om du konfigurerar en ny server rekommenderar vi att du öppnar brand väggen på klient sidan för att tillåta utgående trafik för de IP-adresser som anges i den här kolumnen.
* **IP-adresser för Gateway (inaktive ring):** Den här kolumnen listar IP-adresserna för de gateways som finns på en äldre generation av maskin vara som inaktive ras just nu. Om du konfigurerar en ny server kan du ignorera de här IP-adresserna. Om du har en befintlig server fortsätter du att behålla den utgående regeln för brand väggen för dessa IP-adresser eftersom vi inte har inaktiverat den ännu. Om du tar bort brand Väggs reglerna för dessa IP-adresser kan du få anslutnings fel. I stället förväntas du proaktivt lägga till de nya IP-adresserna som anges i kolumnen Gateway IP-adresser i den utgående brand Väggs regeln så snart du får meddelandet om inaktive ring. Detta säkerställer att när servern migreras till den senaste Gateway-maskinvaran finns det inga avbrott i anslutningen till servern.
* **IP-adresser för Gateway (** inaktive rad): I den här kolumnen visas IP-adresserna för gateway-ringar, som inaktive ras och inte längre är i drift. Du kan ta bort dessa IP-adresser från den utgående brand Väggs regeln på ett säkert sätt. 


| **Region namn** | **IP-adresser för gateway** |**IP-adresser för Gateway (inaktive ring)** | **IP-adresser för Gateway (inaktive rad)** |
|:----------------|:-------------------------|:-------------------------------------------|:------------------------------------------|
| Australien, centrala| 20.36.105.0  | | |
| Australien, Central2     | 20.36.113.0  | | |
| Australien, östra | 13.75.149.87, 40.79.161.1     |  | |
| Sydöstra Australien |191.239.192.109, 13.73.109.251   |  | |
| Brasilien, södra |191.233.201.8, 191.233.200.16    |  | 104.41.11.5|
| Kanada, centrala |40.85.224.249  | | |
| Kanada, östra | 40.86.226.166    | | |
| Central US | 23.99.160.139, 13.67.215.62, 52.182.136.37, 52.182.136.38 | | |
| Kina, östra | 139.219.130.35    | | |
| Kina, östra 2 | 40.73.82.1  | | |
| Kina, norra | 139.219.15.17    | | |
| Kina, norra 2 | 40.73.50.0     | | |
| Asien, östra | 191.234.2.139, 52.175.33.150, 13.75.33.20, 13.75.33.21     | | |
| East US |40.71.8.203, 40.71.83.113 |40.121.158.30|191.238.6.43 |
| USA, östra 2 |40.79.84.180, 191.239.224.107, 52.177.185.181, 40.70.144.38, 52.167.105.38  | | |
| Frankrike, centrala | 40.79.137.0, 40.79.129.1  | | |
| Frankrike, södra | 40.79.177.0     | | |
| Tyskland, centrala | 51.4.144.100     | | |
| Tyskland, norra öst | 51.5.144.179  | | |
| Indien, centrala | 104.211.96.159     | | |
| Södra Indien | 104.211.224.146  | | |
| Indien, västra | 104.211.160.80    | | |
| Japan, östra | 13.78.61.196, 191.237.240.43, 40.79.192.23 | | |
| Japan, västra | 104.214.148.156, 191.238.68.11, 40.74.96.6, 40.74.96.7    | | |
| Sydkorea, centrala | 52.231.32.42   | | |
| Sydkorea, södra | 52.231.200.86    | | |
| USA, norra centrala | 23.96.178.199, 23.98.55.75, 52.162.104.35, 52.162.104.36    | | |
| Norra Europa | 52.138.224.6, 52.138.224.7  |40.113.93.91 |191.235.193.75 |
| Sydafrika, norra  | 102.133.152.0    | | |
| Sydafrika, västra | 102.133.24.0   | | |
| USA, södra centrala |104.214.16.39, 20.45.120.0  |13.66.62.124  |23.98.162.75 |
| Sydostasien | 104.43.15.0, 23.100.117.95, 40.78.233.2, 23.98.80.12     | | |
| Förenade Arabemiraten Central | 20.37.72.64  | | |
| Förenade Arabemiraten, norra | 65.52.248.0    | | |
| Storbritannien, södra | 51.140.184.11   | | |
| Storbritannien, västra | 51.141.8.11  | | |
| USA, västra centrala | 13.78.145.25     | | |
| Europa, västra |13.69.105.208,104.40.169.187 |40.68.37.158 | 191.237.232.75|
| USA, västra |13.86.216.212, 13.86.217.212 |104.42.238.205  | 23.99.34.75|
| USA, västra 2 | 13.66.226.202  | | |
||||

## <a name="connection-redirection"></a>Omdirigering av anslutning

Azure Database for MySQL stöder ytterligare en anslutnings princip, **omdirigering** som hjälper till att minska nätverks fördröjningen mellan klient program och MySQL-servrar. Med den här funktionen returnerar servern den nod som är värd för MySQL-servern till klienten när den första TCP-sessionen har upprättats till Azure Database for MySQL servern. Därefter flödar alla efterföljande paket direkt till servern, vilket kringgår gatewayen. När paket flödar direkt till servern har svars tiden och data flödet bättre prestanda.

Den här funktionen stöds i Azure Database for MySQL servrar med motor versionerna 5,6, 5,7 och 8,0.

Stöd för omdirigering är tillgängligt i PHP [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) -tillägget, som har utvecklats av Microsoft och finns på [PECL](https://pecl.php.net/package/mysqlnd_azure). I artikeln [Konfigurera omdirigering](./howto-redirection.md) finns mer information om hur du använder omdirigering i dina program.

> [!IMPORTANT]
> Stöd för omdirigering i PHP [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) -tillägget är för närvarande en för hands version.

## <a name="next-steps"></a>Nästa steg

* [Skapa och hantera Azure Database for MySQL brand Väggs regler med hjälp av Azure Portal](./howto-manage-firewall-using-portal.md)
* [Skapa och hantera Azure Database for MySQL brand Väggs regler med hjälp av Azure CLI](./howto-manage-firewall-using-cli.md)
* [Konfigurera omdirigering med Azure Database for MySQL](./howto-redirection.md)
