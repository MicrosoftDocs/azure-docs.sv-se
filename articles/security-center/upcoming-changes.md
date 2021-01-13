---
title: Viktiga ändringar som kommer till Azure Security Center
description: Kommande ändringar av Azure Security Center som du kan behöva känna till och för vilka du kan behöva planera
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/05/2021
ms.author: memildin
ms.openlocfilehash: 071f1974e09d2754fe20f3ac2cc90929c4a2eb98
ms.sourcegitcommit: c136985b3733640892fee4d7c557d40665a660af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/13/2021
ms.locfileid: "98180225"
---
# <a name="important-upcoming-changes-to-azure-security-center"></a>Viktiga kommande ändringar i Azure Security Center

> [!IMPORTANT]
> Informationen på den här sidan avser för hands versions produkter eller funktioner som kan ändras i stort sett innan de släpps kommersiellt. Microsoft lämnar inga åtaganden eller garantier, vare sig uttryckliga eller underförstådda, med avseende på den information som anges här.

På den här sidan får du lära dig om ändringar som planeras för Security Center. Den beskriver planerade ändringar av produkten som kan påverka saker som dina säkra poäng eller arbets flöden.

Om du letar efter den senaste versions informationen hittar du dem i [vad som är nytt i Azure Security Center](release-notes.md).


## <a name="planned-changes"></a>Planerade ändringar

- [Förbättringar av SQL data klassificerings rekommendation](#enhancements-to-sql-data-classification-recommendation)
- ["Ej tillämpligt" resurser som ska rapporteras som "kompatibla" i Azure Policy bedömningar](#not-applicable-resources-to-be-reported-as-compliant-in-azure-policy-assessments)
- [35 för hands versioner har lagts till för att öka täckningen av Azures säkerhets prestanda](#35-preview-recommendations-being-added-to-increase-coverage-of-azure-security-benchmark)



### <a name="enhancements-to-sql-data-classification-recommendation"></a>Förbättringar av SQL data klassificerings rekommendation

**Beräknat datum för ändring:** K2 2021

Den aktuella versionen av rekommendations **känsliga data i dina SQL-databaser bör klassificeras** i säkerhets kontrollen **Använd data klassificering** och ersätts med en ny version som är bättre anpassad med Microsofts strategi för data klassificering. Därför:

- Rekommendationen kommer inte längre att påverka dina säkra Poäng
- Säkerhets kontrollen ("Använd data klassificering") kommer inte längre att påverka dina säkra Poäng
- Rekommendationens ID kommer också att ändras (för närvarande b0df6f56-862d-4730-8597-38c0fd4ebd59)



### <a name="not-applicable-resources-to-be-reported-as-compliant-in-azure-policy-assessments"></a>"Ej tillämpligt" resurser som ska rapporteras som "kompatibla" i Azure Policy bedömningar

**Beräknat datum för ändring:** Januari 2021

För närvarande visas resurser som utvärderas för en rekommendation och som inte är **tillämpliga** i Azure policy som "icke-kompatibla". Inga användar åtgärder kan ändra deras status till "kompatibel". Från den här planerade ändringen rapporteras de som "kompatibla" för förbättrad klarhet.

Den enda påverkan visas i Azure Policy där antalet kompatibla resurser kommer att öka. Det kommer inte att påverka dina säkra poäng i Azure Security Center.

### <a name="35-preview-recommendations-being-added-to-increase-coverage-of-azure-security-benchmark"></a>35 för hands versions rekommendationer läggs till för att öka täckningen av Azures säkerhets prestanda

**Beräknat datum för ändring:** Januari 2021

Azures säkerhets prestanda är Microsofts-skapade, Azure-/regionsspecifika uppsättning rikt linjer för säkerhets-och efterlevnads metod tips baserade på vanliga ramverk för efterlevnad. [Läs mer om Azure Security Benchmark](../security/benchmarks/introduction.md).

Följande 35 för hands versions rekommendationer kommer att läggas till Security Center för att öka täckningen för detta benchmark.

För hands versions rekommendationer återger inte en resurs som inte är felfri och ingår inte i beräkningarna av dina säkra poäng. Åtgärda dem när så är möjligt, så att när förhands gransknings perioden är slut bidrar de till dina poäng. Lär dig mer om hur du svarar på dessa rekommendationer i att [åtgärda rekommendationer i Azure Security Center](security-center-remediate-recommendations.md).

| Säkerhets kontroll                     | Nya rekommendationer                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
|--------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Aktivera kryptering i vila            | -Azure Cosmos DB konton ska använda Kundhanterade nycklar för att kryptera data i vila<br>-Azure Machine Learning arbets ytor ska vara krypterade med en kundhanterad nyckel (CMK)<br>– Ta med din egen nyckel data skydd måste vara aktiverat för MySQL-servrar<br>– Ta med din egen nyckel data skydd måste vara aktiverat för PostgreSQL-servrar<br>-Cognitive Services konton ska aktivera data kryptering med en kundhanterad nyckel (CMK)<br>– Behållar register ska krypteras med en kundhanterad nyckel (CMK)<br>-SQL-hanterade instanser bör använda Kundhanterade nycklar för att kryptera data i vila<br>– SQL-servrar bör använda Kundhanterade nycklar för att kryptera data i vila<br>-Lagrings konton ska använda kundhanterad nyckel (CMK) för kryptering                                                                                                                                                              |
| Implementera rekommenderade säkerhets metoder    | -Prenumerationer måste ha en e-postadress till en kontakt med säkerhets problem<br> – Automatisk etablering av den Log Analytics agenten ska vara aktive rad för din prenumeration<br> – E-postmeddelande för aviseringar med hög allvarlighets grad ska aktive ras<br> – E-postmeddelande till Prenumerationens ägare för aviseringar med hög allvarlighets grad måste vara aktiverat<br> – Nyckel valv ska ha rensnings skyddet aktiverat<br> – Nyckel valv ska ha mjuk borttagning aktiverat |
| Hantera åtkomst och behörigheter        | -Funktions appar ska ha "klient certifikat (inkommande klient certifikat)" aktiverat |
| Skydda program mot DDoS-attacker | – Brand vägg för webbaserade program (WAF) måste vara aktive rad för Application Gateway<br> – Brand vägg för webbaserade program (WAF) ska vara aktive rad för Azure-tjänsten för front dörr tjänsten |
| Begränsa obehörig nätverks åtkomst | – Brand väggen ska vara aktive rad på Key Vault<br> -Privat slut punkt ska konfigureras för Key Vault<br> – App-konfigurationen ska använda privat länk<br> – Azure cache för Redis bör finnas i ett virtuellt nätverk<br> -Azure Event Grid domäner ska använda privat länk<br> -Azure Event Grid ämnen ska använda privat länk<br> -Azure Machine Learning arbets ytor ska använda privat länk<br> – Azure SignalR-tjänsten bör använda privat länk<br> – Azure våren Cloud ska använda nätverks inmatning<br> – Behållar register ska inte tillåta obegränsad nätverks åtkomst<br> – Behållar register ska använda privat länk<br> – Åtkomst till offentligt nätverk ska inaktive ras för MariaDB-servrar<br> – Åtkomst till offentligt nätverk ska inaktive ras för MySQL-servrar<br> – Åtkomst till offentligt nätverk ska inaktive ras för PostgreSQL-servrar<br> – Lagrings kontot bör använda en anslutning för privat anslutning<br> -Lagrings konton bör begränsa nätverks åtkomst med hjälp av regler för virtuella nätverk<br> -VM Image Builder-mallar ska använda privat länk|
|                                      |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |

Relaterade länkar:

- [Läs mer om Azures säkerhets prestanda](../security/benchmarks/introduction.md)
- [Läs mer om Azure Database for MariaDB](../mariadb/overview.md)
- [Läs mer om Azure Database for MySQL](../mysql/overview.md)
- [Läs mer om Azure Database for PostgreSQL](../postgresql/overview.md)





## <a name="next-steps"></a>Nästa steg

För alla nyligen gjorda ändringar av produkten, se [Vad är nytt i Azure Security Center?](release-notes.md).