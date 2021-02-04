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
ms.date: 01/25/2021
ms.author: memildin
ms.openlocfilehash: a2c29049decc056f0d3c8083d21574456610c124
ms.sourcegitcommit: f82e290076298b25a85e979a101753f9f16b720c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/04/2021
ms.locfileid: "99555144"
---
# <a name="important-upcoming-changes-to-azure-security-center"></a>Viktiga kommande ändringar i Azure Security Center

> [!IMPORTANT]
> Informationen på den här sidan avser för hands versions produkter eller funktioner som kan ändras i stort sett innan de släpps kommersiellt. Microsoft lämnar inga åtaganden eller garantier, vare sig uttryckliga eller underförstådda, med avseende på den information som anges här.

På den här sidan får du lära dig om ändringar som planeras för Security Center. Den beskriver planerade ändringar av produkten som kan påverka saker som dina säkra poäng eller arbets flöden.

Om du letar efter den senaste versions informationen hittar du dem i [vad som är nytt i Azure Security Center](release-notes.md).


## <a name="planned-changes"></a>Planerade ändringar

- [Två rekommendationer från "tillämpa system uppdateringar"-säkerhets kontrollen är inaktuell](#two-recommendations-from-apply-system-updates-security-control-being-deprecated)
- [Förbättringar av SQL data klassificerings rekommendation](#enhancements-to-sql-data-classification-recommendation)
- [Utfasning av 11 Azure Defender-aviseringar](#deprecation-of-11-azure-defender-alerts)

### <a name="two-recommendations-from-apply-system-updates-security-control-being-deprecated"></a>Två rekommendationer från "tillämpa system uppdateringar"-säkerhets kontrollen är inaktuell 

**Beräknat datum för ändring:** Februari 2021

Följande två rekommendationer är schemalagda att bli föråldrade i februari 2021:

- **Datorerna måste startas om för att tillämpa system uppdateringar**. Detta kan leda till en lätt inverkan på dina säkra poäng.
- **Övervaknings agenten ska installeras på datorerna**. Den här rekommendationen gäller endast för lokala datorer och en del av dess logik överförs till en annan rekommendation, **Log Analytics agent hälso problem bör lösas på dina datorer**. Detta kan leda till en lätt inverkan på dina säkra poäng.

Vi rekommenderar att du kontrollerar dina kontinuerliga export-och arbets flödes automatiserings konfigurationer för att se om dessa rekommendationer ingår i dem. Dessutom bör alla instrument paneler och andra övervaknings verktyg som kan använda dem uppdateras i enlighet med detta.

Läs mer om de här rekommendationerna på [referens sidan för säkerhets rekommendationer](recommendations-reference.md).


### <a name="enhancements-to-sql-data-classification-recommendation"></a>Förbättringar av SQL data klassificerings rekommendation

**Beräknat datum för ändring:** K2 2021

Rekommendations **känsliga data i dina SQL-databaser bör klassificeras** i säkerhets kontrollen **Använd data klassificering** ersätts med en ny version som är bättre anpassad med Microsofts strategi för data klassificering. Som ett resultat kommer rekommendationens ID också att ändras (för närvarande b0df6f56-862d-4730-8597-38c0fd4ebd59).


### <a name="deprecation-of-11-azure-defender-alerts"></a>Utfasning av 11 Azure Defender-aviseringar

**Beräknat datum för ändring:** Mars 2021

Nästa månad kommer de elva Azure Defender-aviseringarna som anges nedan att bli föråldrade.

- Nya aviseringar ersätter dessa två aviseringar och ger bättre täckning:

    | AlertType                | AlertDisplayName                                                         |
    |--------------------------|--------------------------------------------------------------------------|
    | ARM_MicroBurstDomainInfo | PREVIEW – mikroburst Toolkit "Get-AzureDomainInfo"-funktionen körs upptäckt |
    | ARM_MicroBurstRunbook    | PREVIEW – mikroburst Toolkit "Get-AzurePasswords"-funktionen körs upptäckt  |
    |                          |                                                                          |

- Dessa nio aviseringar avser en Azure Active Directory Identity Protection koppling som redan är föråldrad:

    | AlertType           | AlertDisplayName              |
    |---------------------|-------------------------------|
    | UnfamiliarLocation  | Obekanta inloggningsegenskaper |
    | AnonymousLogin      | Anonym IP-adress          |
    | InfectedDeviceLogin | Länkad IP-adress för skadlig kod     |
    | ImpossibleTravel    | Ovanlig resa               |
    | MaliciousIP         | Skadlig IP-adress          |
    | LeakedCredentials   | Läckta autentiseringsuppgifter            |
    | PasswordSpray       | Lösen ords sprayning                |
    | LeakedCredentials   | Azure AD Threat Intelligence  |
    | AADAI               | Azure AD AI                   |
    |                     |                               |
 



## <a name="next-steps"></a>Nästa steg

För alla nyligen gjorda ändringar av produkten, se [Vad är nytt i Azure Security Center?](release-notes.md).
