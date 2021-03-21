---
title: Check lista för Azure Database Security | Microsoft Docs
description: Använd check listan för Azure Database-säkerhet för att se till att du hanterar viktiga säkerhets problem med moln data behandling.
services: security
documentationcenter: na
author: unifycloud
manager: barbkess
editor: tomsh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: tomsh
ms.openlocfilehash: 80455b442bbfb9c8a7d40799b2ddd5fc25460578
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100595578"
---
# <a name="azure-database-security-checklist"></a>Check lista för Azure Database Security

För att förbättra säkerheten innehåller Azure Database ett antal inbyggda säkerhets kontroller som du kan använda för att begränsa och kontrol lera åtkomst.

Dessa omfattar:

-    En brand vägg som gör att du kan skapa [brand Väggs regler](../../azure-sql/database/firewall-configure.md) som begränsar anslutningarna med IP-adress,
-    Brand vägg för server nivå som är tillgänglig från Azure Portal
-    Brand Väggs regler på databas nivå som är tillgängliga från SSMS
-    Säker anslutning till databasen med hjälp av säkra anslutnings strängar
-    Använd åtkomst hantering
-    Datakryptering
-    Granskning av SQL Database
-    SQL Database hot identifiering

## <a name="introduction"></a>Introduktion
Molnbaserad data behandling kräver nya säkerhets paradigm som är okända för många program användare, databas administratörer och programmerare. Därför är vissa organisationer tveka för att implementera en moln infrastruktur för data hantering på grund av uppfattade säkerhets risker. Mycket av detta kan dock minskas genom en bättre förståelse av säkerhetsfunktionerna som är inbyggda i Microsoft Azure och Microsoft Azure SQL Database.

## <a name="checklist"></a>Checklista
Vi rekommenderar att du läser artikeln [metod tips för Azure Database Security](../../azure-sql/database/security-best-practice.md)  innan du läser igenom check listan. Du kommer att kunna få ut det mesta av check listan när du förstår de bästa metoderna. Du kan sedan använda den här check listan för att kontrol lera att du har åtgärdat de viktiga problemen i Azure Database Security.


|Check lista kategori| Beskrivning|
| ------------ | -------- |
|**Skydda data**||
| <br> Kryptering i rörelse/överföring| <ul><li>[Transport Layer Security](/windows-server/security/tls/transport-layer-security-protocol)för data kryptering när data flyttas till nätverken.</li><li>Databasen kräver säker kommunikation från klienter som baseras på [TDS-protokollet (tabell data ström)](/openspecs/windows_protocols/ms-tds/893fcc7e-8a39-4b3c-815a-773b7b982c50) över TLS (Transport Layer Security).</li></ul> |
|<br>Kryptering i vila| <ul><li>[Transparent datakryptering](../../azure-sql/database/transparent-data-encryption-tde-overview.md)när inaktiva data lagras fysiskt i ett digitalt formulär.</li></ul>|
|**Styr åtkomsten**||  
|<br> Åtkomst till databasen | <ul><li>[Autentisering](../../azure-sql/database/logins-create-manage.md) (Azure Active Directory autentisering) AD-autentisering använder identiteter som hanteras av Azure Active Directory.</li><li>[Auktorisera](../../azure-sql/database/logins-create-manage.md) användare de lägsta privilegier som krävs.</li></ul> |
|<br>Program åtkomst| <ul><li>[Säkerhet på radnivå](/sql/relational-databases/security/row-level-security) (med hjälp av säkerhets principer, samtidigt som den begränsar åtkomsten på radnivå baserat på en användares identitet, roll eller körnings kontext).</li><li>[Dynamisk data maskning](../../azure-sql/database/dynamic-data-masking-overview.md) (med behörighet & principen begränsar känslig data exponering genom att maskera den till icke-privilegierade användare)</li></ul>|
|**Proaktiv övervakning**||  
| <br>Spåra & att identifiera| <ul><li>[Granskning](../../azure-sql/database/auditing-overview.md) spårar databas händelser och skriver dem till en Gransknings logg/aktivitets logg i ditt [Azure Storage konto](../../storage/common/storage-account-create.md).</li><li>Spåra Azures databas hälsa med [Azure Monitor aktivitets loggar](../../azure-monitor/essentials/platform-logs-overview.md).</li><li>[Hot identifiering](../../azure-sql/database/threat-detection-configure.md) identifierar avvikande databas aktiviteter som indikerar potentiella säkerhetshot mot databasen. </li></ul> |
|<br>Azure Security Center| <ul><li>[Data övervakning](../../security-center/security-center-remediate-recommendations.md) Använd Azure Security Center som en centraliserad lösning för säkerhets övervakning för SQL och andra Azure-tjänster.</li></ul>|        

## <a name="conclusion"></a>Slutsats
Azure Database är en robust databas plattform med en fullständig uppsättning säkerhetsfunktioner som uppfyller många krav på organisation och regelefterlevnad. Du kan enkelt skydda data genom att kontrol lera den fysiska åtkomsten till dina data och använda en mängd olika alternativ för data säkerhet på fil-, kolumn-eller rad nivå med transparent datakryptering, Cell-Level kryptering eller Row-Level säkerhet. Always Encrypted aktiverar också åtgärder mot krypterade data, vilket fören klar processen med program uppdateringar. I sin tur ger åtkomst till gransknings loggar för SQL Database-aktivitet dig den information du behöver, så att du kan veta hur och när data nås.

## <a name="next-steps"></a>Nästa steg
Med några få enkla steg kan du förbättra databasens skydd mot obehöriga användare och obehörig åtkomst. I den här självstudien får du lära du dig att:

- Konfigurera [brand Väggs regler](../../azure-sql/database/firewall-configure.md) för servern och eller databasen.
- Skydda dina data med [kryptering](/sql/relational-databases/security/encryption/sql-server-encryption).
- Aktivera [SQL Database granskning](../../azure-sql/database/auditing-overview.md).