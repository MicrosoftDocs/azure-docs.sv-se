---
title: Viktiga ändringar som kommer till Azure Security Center
description: Kommande ändringar av Azure Security Center som du kan behöva känna till och för vilka du kan behöva planera
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: overview
ms.date: 03/10/2021
ms.author: memildin
ms.openlocfilehash: 49141f7f11c0e8ead090459238e15b56f57b990b
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/11/2021
ms.locfileid: "102633724"
---
# <a name="important-upcoming-changes-to-azure-security-center"></a>Viktiga kommande ändringar i Azure Security Center

> [!IMPORTANT]
> Informationen på den här sidan avser för hands versions produkter eller funktioner som kan ändras i stort sett innan de släpps kommersiellt. Microsoft lämnar inga åtaganden eller garantier, vare sig uttryckliga eller underförstådda, med avseende på den information som anges här.

På den här sidan får du lära dig om ändringar som planeras för Security Center. Den beskriver planerade ändringar av produkten som kan påverka saker som dina säkra poäng eller arbets flöden.

Om du letar efter den senaste versions informationen hittar du dem i [vad som är nytt i Azure Security Center](release-notes.md).


## <a name="planned-changes"></a>Planerade ändringar

- [Rekommendationer från AWS kommer att lanseras för allmän tillgänglighet (GA)](#recommendations-from-aws-will-be-released-for-general-availability-ga)
- [Två rekommendationer från "tillämpa system uppdateringar"-säkerhets kontrollen är inaktuell](#two-recommendations-from-apply-system-updates-security-control-being-deprecated)
- [Förbättringar av SQL data klassificerings rekommendation](#enhancements-to-sql-data-classification-recommendation)
- [Utfasning av 11 Azure Defender-aviseringar](#deprecation-of-11-azure-defender-alerts)


### <a name="recommendations-from-aws-will-be-released-for-general-availability-ga"></a>Rekommendationer från AWS kommer att lanseras för allmän tillgänglighet (GA)

**Beräknat datum för ändring:** April 2021

Azure Security Center skyddar arbets belastningar i Azure, Amazon Web Services (AWS) och Google Cloud Platform (GCP).

Rekommendationerna som kommer från AWS-säkerhetshubben har varit i för hands version eftersom moln anslutningarna introducerades. Rekommendationer som har flaggats som **förhands granskning** ingår inte i beräkningarna av dina säkra poäng, men bör fortfarande åtgärdas när så är möjligt, så att när förhands gransknings perioden är slut bidrar de till dina poäng.

Med den här ändringen kommer två uppsättningar med AWS-rekommendationer att flyttas till GA:

- [Säkerhets hubbens PCI DSS kontroller](https://docs.aws.amazon.com/securityhub/latest/userguide/securityhub-pci-controls.html)
- [Säkerhets hubbens CIS-AWS grunder benchmark-kontroller](https://docs.aws.amazon.com/securityhub/latest/userguide/securityhub-cis-controls.html)

När dessa är GA och utvärderingarna körs på dina AWS-resurser påverkar resultaten dina kombinerade säkra Poäng för alla dina resurser i flera och hybrid moln. 


### <a name="two-recommendations-from-apply-system-updates-security-control-being-deprecated"></a>Två rekommendationer från "tillämpa system uppdateringar"-säkerhets kontrollen är inaktuell 

**Beräknat datum för ändring:** Mars 2021

Följande två rekommendationer är schemalagda att bli föråldrade i februari 2021:

- **Datorerna måste startas om för att tillämpa system uppdateringar**. Detta kan leda till en lätt inverkan på dina säkra poäng.
- **Övervaknings agenten ska installeras på datorerna**. Den här rekommendationen gäller endast för lokala datorer och en del av dess logik överförs till en annan rekommendation, **Log Analytics agent hälso problem bör lösas på dina datorer**. Detta kan leda till en lätt inverkan på dina säkra poäng.

Vi rekommenderar att du kontrollerar dina kontinuerliga export-och arbets flödes automatiserings konfigurationer för att se om dessa rekommendationer ingår i dem. Dessutom bör alla instrument paneler och andra övervaknings verktyg som kan använda dem uppdateras i enlighet med detta.

Läs mer om de här rekommendationerna på [referens sidan för säkerhets rekommendationer](recommendations-reference.md).


### <a name="enhancements-to-sql-data-classification-recommendation"></a>Förbättringar av SQL data klassificerings rekommendation

**Beräknat datum för ändring:** K2 2021

Rekommendations **känsliga data i dina SQL-databaser bör klassificeras** i säkerhets kontrollen **Använd data klassificering** ersätts med en ny version som är bättre anpassad med Microsofts strategi för data klassificering. Som ett resultat kommer rekommendationens ID också att ändras (för närvarande är det b0df6f56-862d-4730-8597-38c0fd4ebd59).


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
