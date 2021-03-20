---
title: Felsöka anslutningar – storskalig (citus) – Azure Database for PostgreSQL
description: Lär dig hur du felsöker anslutnings problem till Azure Database for PostgreSQL-storskalig (citus)
keywords: postgresql anslutning, anslutnings sträng, anslutnings problem, tillfälligt fel, anslutnings fel
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 10/8/2019
ms.openlocfilehash: e1c6825820ae943d10157279dfe93922a7521b75
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91295625"
---
# <a name="troubleshoot-connection-issues-to-azure-database-for-postgresql---hyperscale-citus"></a>Felsöka anslutnings problem till Azure Database for PostgreSQL-storskalig (citus)

Anslutnings problem kan bero på flera saker, till exempel:

* Brandväggsinställningar
* Anslutnings-timeout
* Felaktig inloggnings information
* Anslutnings gränsen har nåtts för Server gruppen
* Problem med tjänstens infrastruktur
* Underhåll av tjänst
* Koordinator noden växlar över till ny maskin vara

I allmänhet kan anslutnings problem till storskalig (citus) klassificeras enligt följande:

* Tillfälliga fel (kort livs längd eller intermittent)
* Beständiga eller icke-tillfälliga fel (fel som upprepas regelbundet)

## <a name="troubleshoot-transient-errors"></a>Felsök tillfälliga fel

Tillfälliga fel uppstår av flera orsaker. Det vanligaste är system underhåll, fel med maskin vara eller program vara och vCore uppgraderingar av koordinatorer.

Om du aktiverar hög tillgänglighet för citus-noder kan du undvika dessa typer av problem automatiskt. Ditt program bör dock fortfarande vara för berett för att förlora anslutningen i korthet. Även andra händelser kan ta längre tid att undvika, till exempel när en stor transaktion orsakar en tids krävande återställning.

### <a name="steps-to-resolve-transient-connectivity-issues"></a>Steg för att lösa problem med tillfälliga anslutningar

1. Kontrol lera [instrument panelen för Microsoft Azures tjänsten](https://azure.microsoft.com/status) för eventuella kända avbrott som uppstod under den tid då programmet rapporterade fel.
2. Program som ansluter till en moln tjänst, t. ex. storskalig (citus), bör förvänta sig tillfälliga fel och reagera på ett smidigt sätt. Till exempel bör program implementera logik för omprövning för att hantera dessa fel i stället för att visa dem som program fel till användare.
3. Eftersom Server gruppen närmar sig resurs gränserna kan fel uppstå som övergående anslutnings problem. Att öka nodens RAM-minne eller lägga till arbetsnoder och ombalansering av data kan hjälpa dig.
4. Om anslutnings problem fortsätter eller längre än 60 sekunder eller om det inträffar mer än en gång per dag, kan du skicka en support förfrågan till Azure genom att välja **få support** på support webbplatsen för [Azure](https://azure.microsoft.com/support/options) .

## <a name="troubleshoot-persistent-errors"></a>Felsöka beständiga fel

Om programmet inte kan ansluta till storskalig (citus) är de vanligaste orsakerna till fel i brand väggen eller användar fel.

* Konfiguration av koordinator-nodens brand vägg: se till att citus-serverns brand vägg är konfigurerad för att tillåta anslutningar från klienten, inklusive proxyservrar och gatewayer.
* Konfiguration av klient brand väggen: brand väggen på klienten måste tillåta anslutningar till din databas server. Vissa brand väggar kräver att inte bara program efter namn, utan att tillåta IP-adresser och portar för-servern.
* Användar fel: kontrol lera anslutnings strängen. Du kan ha felangede parametrar som server namn. Du kan hitta anslutnings strängar för olika språk ramverk och psql i Azure Portal. Gå till sidan **anslutnings strängar** i citus-servergruppen (för Server gruppen). Tänk också på att citus-kluster bara har en databas och dess fördefinierade namn är **citus**.

### <a name="steps-to-resolve-persistent-connectivity-issues"></a>Steg för att lösa problem med beständiga anslutningar

1. Konfigurera [brand Väggs regler](howto-hyperscale-manage-firewall-using-portal.md) som tillåter KLIENTens IP-adress. För temporär testning kan du konfigurera en brand Väggs regel med 0.0.0.0 som första IP-adress och använda 255.255.255.255 som sista IP-adress. Regeln öppnar servern för alla IP-adresser. Om regeln löser anslutnings problemet tar du bort den och skapar en brand Väggs regel för en lämplig, begränsad IP-adress eller ett IP-adressintervall.
2. Kontrol lera att port 5432 är öppen för utgående anslutningar på alla brand väggar mellan klienten och Internet.
3. Kontrol lera anslutnings strängen och andra anslutnings inställningar.
4. Kontrol lera tjänstens hälso tillstånd på instrument panelen.

## <a name="next-steps"></a>Nästa steg

* Lär dig begrepp för [brand Väggs regler i Azure Database for PostgreSQL-storskalig (citus)](concepts-hyperscale-firewall-rules.md)
* Se [Hantera brand Väggs regler för Azure Database for PostgreSQL-storskalig (citus)](howto-hyperscale-manage-firewall-using-portal.md)
