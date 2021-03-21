---
title: Azure Security benchmark v2 – data skydd
description: Azure Security benchmark v2 data skydd
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 50358eed580bbd83f25386feb0068a252060672b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102037123"
---
# <a name="security-control-v2-data-protection"></a>Säkerhets kontroll v2: data skydd

Data skydd täcker kontroll av data skydd i vila, under överföring och via auktoriserade åtkomst metoder. Detta omfattar identifiering, klassificering, skydd och övervakning av känsliga data till gångar med hjälp av åtkomst kontroll, kryptering och loggning i Azure.

Om du vill se vilka inbyggda Azure Policy som finns kan du läsa [mer i information om det inbyggda initiativet för Azure Security benchmark-hälsokompatibilitet: data skydd](../../governance/policy/samples/azure-security-benchmark.md#data-protection)

## <a name="dp-1-discovery-classify-and-label-sensitive-data"></a>DP-1: Identifiera, klassificera och märk upp känsliga data

| Azure-ID | CIS-kontroller v 7.1-ID: n | NIST SP 800-53 R4-ID: n |
|--|--|--|--|
| DP-1 | 13,1, 14,5, 14,7 | SC-28 |

Identifiera, klassificera och märk känsliga data så att du kan utforma lämpliga kontroller för att se till att känslig information lagras, bearbetas och överförs säkert av organisationens teknik system.

Använd Azure Information Protection (och dess associerade skanningsverktyg) för känslig information i Office-dokument på Azure, lokalt, i Office 365 och på andra platser.

Du kan använda Azure SQL-Information Protection för att hjälpa till med klassificering och märkning av information som lagras i Azure SQL-databaser.

- [Tagga känslig information med Azure Information Protection](/azure/information-protection/what-is-information-protection) 

- [Så här implementerar du identifiering av Azure SQL-data](../../azure-sql/database/data-discovery-and-classification-overview.md)

**Ansvar**: Delad

**Kund säkerhets intressenter** ([Läs mer](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Program säkerhet och DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Data säkerhet](/azure/cloud-adoption-framework/organize/cloud-security-data-security) 

- [Infrastruktur- och slutpunktssäkerhet](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

## <a name="dp-2-protect-sensitive-data"></a>DP-2: Skydda känsliga data

| Azure-ID | CIS-kontroller v 7.1-ID: n | NIST SP 800-53 R4-ID: n |
|--|--|--|--|
| DP-2 | 13,2, 2,10 | SC-7, AC-4 |

Skydda känsliga data genom att begränsa åtkomsten med hjälp av rollbaserad åtkomst kontroll i Azure (Azure RBAC), nätverksbaserade åtkomst kontroller och vissa kontroller i Azure-tjänster (till exempel kryptering i SQL och andra databaser). 

För att säkerställa konsekvent åtkomstkontroll bör alla typer av åtkomstkontroll justeras mot din strategi för företagssegmentering. Strategin för företagssegmentering bör också informeras om platsen för känsliga eller affärskritiska data och system.

När det gäller den underliggande plattformen, som hanteras av Microsoft, behandlar Microsoft allt kundinnehåll som känsligt och skyddar det mot förlust och exponering av kunddata. För att säkerställa att kunddata i Azure alltid är skyddade har Microsoft implementerat vissa standardkontroller och funktioner för dataskydd.

- [Azure RBAC (rollbaserad åtkomstkontroll)](../../role-based-access-control/overview.md)

- [Förstå skydd av kunddata i Azure](../fundamentals/protection-customer-data.md)

**Ansvar**: Delad

**Kund säkerhets intressenter** ([Läs mer](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Program säkerhet och DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

- [Data säkerhet](/azure/cloud-adoption-framework/organize/cloud-security-data-security)

- [Infrastruktur- och slutpunktssäkerhet](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

## <a name="dp-3-monitor-for-unauthorized-transfer-of-sensitive-data"></a>DP-3: Övervaka obehörig överföring av känsliga data

| Azure-ID | CIS-kontroller v 7.1-ID: n | NIST SP 800-53 R4-ID: n |
|--|--|--|--|
| DP-3 | 13,3 | AC-4, SI-4 |

Övervaka för obehörig överföring av data till platser utanför företags synlighet och kontroll. Detta omfattar vanligtvis övervakning av avvikande aktiviteter (stora eller ovanliga överföringar) som kan tyda på obehörig dataexfiltrering. 

Azure Storage Advanced Threat Protection (ATP) och Azure SQL ATP kan varna vid avvikande överföring av information som kan tyda på obehörig överföring av känslig information. 

Azure Information Protection (AIP) innehåller övervakningsfunktioner för information som har klassificerats och märkts. 

Om det krävs för att förhindra skydd mot dataförlust (DLP) kan du använda en värdbaserad DLP-lösning för att genomdriva detektionskontroller och/eller förebyggande kontroller för att förhindra dataexfiltrering.

- [Azure Defender för SQL](../../azure-sql/database/azure-defender-for-sql.md)

- [Azure Defender för Storage](../../storage/common/azure-defender-storage-configure.md?tabs=azure-security-center)

**Ansvar**: Delad

**Kund säkerhets intressenter** ([Läs mer](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Säkerhetsåtgärder](/azure/cloud-adoption-framework/organize/cloud-security) 

- [Program säkerhet och DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

- [Infrastruktur- och slutpunktssäkerhet](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

## <a name="dp-4-encrypt-sensitive-information-in-transit"></a>DP-4: Kryptera känslig information under överföring

| Azure-ID | CIS-kontroller v 7.1-ID: n | NIST SP 800-53 R4-ID: n |
|--|--|--|--|
| DP-4 | 14,4 | SC-8 |

För att komplettera åtkomst kontroller bör data i överföring skyddas mot "out of band"-attacker (t. ex. trafik avbildning) med kryptering för att säkerställa att angripare inte kan läsa eller ändra data.

Även om det är valfritt för trafik i privata nätverk, är detta avgörande för trafik på externa och offentliga nätverk. För HTTP-trafik ser du till att alla klienter som ansluter till dina Azure-resurser kan förhandla TLS v 1.2 eller senare. För fjärrhantering använder du SSH (för Linux) eller RDP/TLS (för Windows) i stället för ett okrypterat protokoll. Inaktuella SSL-, TLS-och SSH-versioner och-protokoll och svaga chiffer bör inaktive ras.

Som standard tillhandahåller Azure kryptering för data i överföring mellan Azures Data Center.

- [Förstå kryptering i överföring med Azure](../fundamentals/encryption-overview.md#encryption-of-data-in-transit)

- [Information om TLS-säkerhet](/security/engineering/solving-tls1-problem)

- [Double Encryption för Azure-data under överföring](../fundamentals/double-encryption.md#data-in-transit)

**Ansvar**: Delad

**Kund säkerhets intressenter** ([Läs mer](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Säkerhetsarkitektur](/azure/cloud-adoption-framework/organize/cloud-security-architecture) 

- [Infrastruktur- och slutpunktssäkerhet](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [Program säkerhet och DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

- [Data säkerhet](/azure/cloud-adoption-framework/organize/cloud-security-data-security)

## <a name="dp-5-encrypt-sensitive-data-at-rest"></a>DP-5: Kryptera känsliga data i vila

| Azure-ID | CIS-kontroller v 7.1-ID: n | NIST SP 800-53 R4-ID: n |
|--|--|--|--|
| DP-5 | 14,8 | SC-28, SC-12 |

För att komplettera åtkomst kontroller bör data i vila skyddas mot "out of band"-attacker (till exempel åtkomst till underliggande lagrings enheter) med kryptering. Detta säkerställer att angripare inte enkelt kan läsa eller ändra data. 

Azure tillhandahåller kryptering för data i vila som standard. För mycket känsliga data har du alternativ för att implementera ytterligare kryptering i vila på alla Azure-resurser där det är tillgängligt. Azure hanterar dina krypterings nycklar som standard, men Azure tillhandahåller alternativ för att hantera dina egna nycklar (kundens hanterade nycklar) för vissa Azure-tjänster.

- [Förstå kryptering vid vila i Azure](../fundamentals/encryption-atrest.md#encryption-at-rest-in-microsoft-cloud-services)

- [Konfigurera kundens hanterade krypterings nycklar](../../storage/common/customer-managed-keys-configure-key-vault.md)

- [Krypterings modell och nyckel hanterings tabell](../fundamentals/encryption-models.md)

- [Data i vila dubbel kryptering i Azure](../fundamentals/double-encryption.md#data-at-rest)

**Ansvar**: Delad

**Kund säkerhets intressenter** ([Läs mer](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Säkerhetsarkitektur](/azure/cloud-adoption-framework/organize/cloud-security-architecture) 

- [Infrastruktur- och slutpunktssäkerhet](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [Program säkerhet och DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Data säkerhet](/azure/cloud-adoption-framework/organize/cloud-security-data-security)