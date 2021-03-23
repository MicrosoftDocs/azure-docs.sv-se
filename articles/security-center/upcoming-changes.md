---
title: Viktiga ändringar som kommer till Azure Security Center
description: Kommande ändringar av Azure Security Center som du kan behöva känna till och för vilka du kan behöva planera
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: overview
ms.date: 03/18/2021
ms.author: memildin
ms.openlocfilehash: b9a93286b6a546160b6c621d084437f671eab4d3
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/22/2021
ms.locfileid: "104773580"
---
# <a name="important-upcoming-changes-to-azure-security-center"></a>Viktiga kommande ändringar i Azure Security Center

> [!IMPORTANT]
> Informationen på den här sidan avser för hands versions produkter eller funktioner som kan ändras i stort sett innan de släpps kommersiellt. Microsoft lämnar inga åtaganden eller garantier, vare sig uttryckliga eller underförstådda, med avseende på den information som anges här.

På den här sidan får du lära dig om ändringar som planeras för Security Center. Den beskriver planerade ändringar av produkten som kan påverka saker som dina säkra poäng eller arbets flöden.

Om du letar efter den senaste versions informationen hittar du dem i [vad som är nytt i Azure Security Center](release-notes.md).


## <a name="planned-changes"></a>Planerade ändringar

| Planerad ändring                                                                                                                                                        | Beräknat datum för ändring |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------|
| [Två rekommendationer från "tillämpa system uppdateringar"-säkerhets kontrollen är inaktuell](#two-recommendations-from-apply-system-updates-security-control-being-deprecated) | Mars 2021                |
| [Utfasning av 11 Azure Defender-aviseringar](#deprecation-of-11-azure-defender-alerts)                                                                                   | Mars 2021                |
| [21 rekommendationer som flyttas mellan säkerhets kontroller](#21-recommendations-moving-between-security-controls)                                                           | April 2021                |
| [Två ytterligare rekommendationer från "tillämpa system uppdateringar"-säkerhets kontrollen är inaktuell](#two-further-recommendations-from-apply-system-updates-security-control-being-deprecated)                                                                                         | April 2021                |
| [Rekommendationer från AWS kommer att lanseras för allmän tillgänglighet (GA)](#recommendations-from-aws-will-be-released-for-general-availability-ga)                     | April 2021                |
| [Förbättringar av SQL data klassificerings rekommendation](#enhancements-to-sql-data-classification-recommendation)                                                     | K2 2021                   |
|                                                                                                                                                                       |                           |


### <a name="two-recommendations-from-apply-system-updates-security-control-being-deprecated"></a>Två rekommendationer från "tillämpa system uppdateringar"-säkerhets kontrollen är inaktuell 

**Beräknat datum för ändring:** Mars 2021

Följande två rekommendationer är schemalagda att bli föråldrade i februari 2021:

- **Datorerna måste startas om för att tillämpa system uppdateringar**. Detta kan leda till en lätt inverkan på dina säkra poäng.
- **Övervaknings agenten ska installeras på datorerna**. Den här rekommendationen gäller endast för lokala datorer och en del av dess logik överförs till en annan rekommendation, **Log Analytics agent hälso problem bör lösas på dina datorer**. Detta kan leda till en lätt inverkan på dina säkra poäng.

Vi rekommenderar att du kontrollerar dina kontinuerliga export-och arbets flödes automatiserings konfigurationer för att se om dessa rekommendationer ingår i dem. Dessutom bör alla instrument paneler och andra övervaknings verktyg som kan använda dem uppdateras i enlighet med detta.

Läs mer om de här rekommendationerna på [referens sidan för säkerhets rekommendationer](recommendations-reference.md).

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
 




### <a name="21-recommendations-moving-between-security-controls"></a>21 rekommendationer som flyttas mellan säkerhets kontroller 

**Beräknat datum för ändring:** April 2021

Följande rekommendationer flyttas till en annan säkerhets kontroll. Säkerhets kontroller är logiska grupper av relaterade säkerhets rekommendationer och återspeglar dina sårbara angrepps ytor. Den här flytten säkerställer att var och en av dessa rekommendationer är i den lämpligaste kontrollen för att uppfylla målet. 

Lär dig vilka rekommendationer som finns i varje säkerhets kontroll i säkerhets kontroller och deras rekommendationer.

|Rekommendation |Ändra och påverka  |
|---------|---------|
|Sårbarhets bedömning bör vara aktiverat på dina SQL-servrar<br>Sårbarhets bedömning ska vara aktiverat på SQL-hanterade instanser<br>Säkerhets risker i SQL-databaser bör åtgärdas nya<br>Säkerhets risker i SQL-databaser i virtuella datorer bör åtgärdas     |Flytta från åtgärds sårbarheter (värt 6 Poäng)<br>åtgärda säkerhetskonfigurationer (värt 4 Poäng).<br>Beroende på din miljö får de här rekommendationerna en minskad inverkan på dina poäng.|
|Det bör finnas fler än en ägare som tilldelats din prenumeration<br>Variabler för Automation-konton ska vara krypterade<br>IoT-enheter – gransknings processen slutade skicka händelser<br>IoT-enheter – det gick inte att verifiera operativ systemets bas linje<br>IoT-enheter-TLS cipher Suite-uppgradering krävs<br>IoT-enheter – öppna portar på enheten<br>IoT-enheter – en princip för att tillåta brand vägg i en av kedjorna hittades<br>IoT-enheter-tillåtande brand Väggs regel i indatamängden hittades<br>IoT-enheter – en brand Väggs regel för att tillåtas i utmatnings kedjan hittades<br>Diagnostikloggar i IoT Hub ska vara aktive rad<br>IoT-enheter – agent som skickar underutnyttjade meddelanden<br>IoT-enheter-standard princip för IP-filter ska nekas<br>IoT-enheter – IP filter regel stora IP-intervall<br>IoT-enheter – intervall och storlek för agent meddelande ska justeras<br>IoT-enheter – identiska autentiseringsuppgifter för autentisering<br>IoT-enheter – gransknings processen slutade skicka händelser<br>Konfigurations bas linje för IoT-enheter – operativ system (OS) bör vara fast|Flyttar till **implementera rekommenderade säkerhets metoder**.<br>När en rekommendation flyttas till säkerhets kontrollen för att implementera säkerhets metoder för säkerhet, vilket inte är värt några punkter, påverkar inte längre rekommendationen dina säkra poäng.|
|||


### <a name="two-further-recommendations-from-apply-system-updates-security-control-being-deprecated"></a>Två ytterligare rekommendationer från "tillämpa system uppdateringar"-säkerhets kontrollen är inaktuell

**Beräknat datum för ändring:** April 2021

Följande två rekommendationer är föråldrade:

- **OS-versionen bör uppdateras för dina moln tjänst roller** – som standard uppdaterar Azure regelbundet ditt gäst operativ system till den senaste avbildningen som stöds i OS-familjen som du har angett i tjänst konfigurationen (. cscfg), till exempel Windows Server 2016.
- **Kubernetes Services bör uppgraderas till en icke-sårbar Kubernetes-version** – utvärderings versionen är inte lika bred som vi vill. Den aktuella versionen av den här rekommendationen kommer slutligen att ersättas med en förbättrad version som är bättre anpassad till kundens säkerhets behov.


### <a name="recommendations-from-aws-will-be-released-for-general-availability-ga"></a>Rekommendationer från AWS kommer att lanseras för allmän tillgänglighet (GA)

**Beräknat datum för ändring:** April 2021

Azure Security Center skyddar arbets belastningar i Azure, Amazon Web Services (AWS) och Google Cloud Platform (GCP).

Rekommendationerna som kommer från AWS-säkerhetshubben har varit i för hands version eftersom moln anslutningarna introducerades. Rekommendationer som har flaggats som **förhands granskning** ingår inte i beräkningarna av dina säkra poäng, men bör fortfarande åtgärdas när så är möjligt, så att när förhands gransknings perioden är slut bidrar de till dina poäng.

Med den här ändringen kommer två uppsättningar med AWS-rekommendationer att flyttas till GA:

- [Säkerhets hubbens PCI DSS kontroller](https://docs.aws.amazon.com/securityhub/latest/userguide/securityhub-pci-controls.html)
- [Säkerhets hubbens CIS-AWS grunder benchmark-kontroller](https://docs.aws.amazon.com/securityhub/latest/userguide/securityhub-cis-controls.html)

När dessa är GA och utvärderingarna körs på dina AWS-resurser påverkar resultaten dina kombinerade säkra Poäng för alla dina resurser i flera och hybrid moln. 



### <a name="enhancements-to-sql-data-classification-recommendation"></a>Förbättringar av SQL data klassificerings rekommendation

**Beräknat datum för ändring:** K2 2021

Rekommendations **känsliga data i dina SQL-databaser bör klassificeras** i säkerhets kontrollen **Använd data klassificering** ersätts med en ny version som är bättre anpassad med Microsofts strategi för data klassificering. Som ett resultat kommer rekommendationens ID också att ändras (för närvarande är det b0df6f56-862d-4730-8597-38c0fd4ebd59).



## <a name="next-steps"></a>Nästa steg

För alla nyligen gjorda ändringar av produkten, se [Vad är nytt i Azure Security Center?](release-notes.md).