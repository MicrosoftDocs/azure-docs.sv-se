---
title: Viktiga ändringar kommer till Azure Security Center
description: Kommande ändringar av Azure Security Center som du kan behöva känna till och för vilka du kan behöva planera
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: overview
ms.date: 04/20/2021
ms.author: memildin
ms.openlocfilehash: 3307d3aed422c3eab63412388244ef14ef3be699
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107751010"
---
# <a name="important-upcoming-changes-to-azure-security-center"></a>Viktiga kommande ändringar i Azure Security Center

> [!IMPORTANT]
> Informationen på den här sidan relaterar till förhandsprodukter eller funktioner, som kan ändras avsevärt innan de släpps kommersiellt, om någonsin. Microsoft gör inga åtaganden eller garantier, uttryckliga eller underförstådda, med avseende på den information som anges här.

På den här sidan får du lära dig om ändringar som planeras för Security Center. Den beskriver planerade ändringar i produkten som kan påverka saker som säkerhetspoäng eller arbetsflöden.

Om du letar efter de senaste versionerna hittar du dem i Nyheter i [Azure Security Center](release-notes.md).


## <a name="planned-changes"></a>Planerade ändringar

| Planerad ändring                                                                                                                                                        | Beräknat datum för ändring |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------|
| [21 rekommendationer flyttas mellan säkerhetskontroller](#21-recommendations-moving-between-security-controls)                                                           | April 2021                |
| [Två rekommendationer från säkerhetskontrollen "Tillämpa systemuppdateringar" är inaktuell](#two-recommendations-from-apply-system-updates-security-control-being-deprecated) | April 2021                |
| [Den äldre implementeringen av ISO 27001 ersätts med ny ISO 27001:2013](#legacy-implementation-of-iso-27001-is-being-replaced-with-new-iso-270012013)          | Juni 2021                 |
| [Rekommendationer från AWS släpps för allmän tillgänglighet (GA)](#recommendations-from-aws-will-be-released-for-general-availability-ga)                     | **Augusti** 2021           |
| [Förbättringar av sql-dataklassificeringsrekommendation](#enhancements-to-sql-data-classification-recommendation)                                                     | Andra kvartalet 2021                   |
|                                                                                                                                                                       |                           |


### <a name="21-recommendations-moving-between-security-controls"></a>21 rekommendationer flyttas mellan säkerhetskontroller 

**Beräknat datum för ändring:** April 2021

Följande rekommendationer flyttas till en annan säkerhetskontroll. Säkerhetskontroller är logiska grupper med relaterade säkerhetsrekommendationer och återspeglar dina sårbara angreppsytor. Den här flytten säkerställer att var och en av dessa rekommendationer har den lämpligaste kontrollen för att uppfylla sitt mål. 

Lär dig vilka rekommendationer som finns i varje säkerhetskontroll i Säkerhetskontroller och deras rekommendationer.

|Rekommendation |Ändra och påverka  |
|---------|---------|
|Sårbarhetsbedömning bör aktiveras på SQL-servrarna<br>Sårbarhetsbedömning bör aktiveras på dina SQL-hanterade instanser<br>Sårbarheter i DINA SQL-databaser bör åtgärdas ny<br>Sårbarheter i DINA SQL-databaser på virtuella datorer bör åtgärdas     |Flytta från Åtgärda sårbarheter (värd 6 punkter)<br>för att åtgärda säkerhetskonfigurationer (värd 4 punkter).<br>Beroende på din miljö har dessa rekommendationer en minskad inverkan på dina poäng.|
|Det bör finnas fler än en ägare tilldelad till din prenumeration<br>Automation-kontovariabler ska vara krypterade<br>IoT-enheter – Granskningsprocessen slutade skicka händelser<br>IoT-enheter – Valideringsfel vid baslinje för operativsystem<br>IoT-enheter – uppgradering av TLS-chiffersvit krävs<br>IoT-enheter – Öppna portar på enheten<br>IoT-enheter – En tillåtande brandväggsprincip i en av kedjorna hittades<br>IoT-enheter – En tillåten brandväggsregel i indatakedjan hittades<br>IoT-enheter – En tillåten brandväggsregel i utdatakedjan hittades<br>Diagnostikloggar i IoT Hub ska vara aktiverade<br>IoT-enheter – Agent som skickar underutnyttjade meddelanden<br>IoT-enheter – Standardprincipen för IP-filter ska vara Neka<br>IoT-enheter – IP-filterregel för stort IP-intervall<br>IoT-enheter – Intervall och storlek för agentmeddelanden bör justeras<br>IoT-enheter – identiska autentiseringsuppgifter<br>IoT-enheter – Den granskade processen slutade skicka händelser<br>IoT-enheter – Baslinjekonfigurationen för operativsystem (OS) bör vara fast|Flytta till **Implementera metodtips för säkerhet.**<br>När en rekommendation flyttas till Säkerhetskontroll med metodtips för implementerad säkerhet, vilket inte är värt några poäng, påverkar rekommendationen inte längre dina säkerhetspoäng.|
|||


### <a name="two-recommendations-from-apply-system-updates-security-control-being-deprecated"></a>Två rekommendationer från säkerhetskontrollen "Tillämpa systemuppdateringar" är inaktuell

**Beräknat datum för ändring:** April 2021

Följande två rekommendationer håller på att bli inaktuella:

- **Operativsystemversionen** bör uppdateras för dina molntjänstroller – Som standard uppdaterar Azure regelbundet gästoperativsystemet till den senaste avbildningen som stöds i operativsystemfamiljen som du har angett i tjänstkonfigurationen (.cscfg), till exempel Windows Server 2016.
- Kubernetes Services bör uppgraderas till en **icke-sårbar Kubernetes-version** – den här rekommendationens utvärderingar är inte så omfattande som vi vill att de ska vara. Den aktuella versionen av den här rekommendationen kommer så småningom att ersättas med en förbättrad version som är bättre anpassad efter våra kunders säkerhetsbehov.


### <a name="legacy-implementation-of-iso-27001-is-being-replaced-with-new-iso-270012013"></a>Den äldre implementeringen av ISO 27001 ersätts med ny ISO 27001:2013

Den äldre implementeringen av ISO 27001 tas bort från Security Center instrumentpanel för regelefterlevnad. Om du spårar din ISO 27001-efterlevnad med Security Center kan du registrera den nya ISO 27001:2013-standarden för alla relevanta hanteringsgrupper eller prenumerationer, och den aktuella äldre ISO 27001 tas snart bort från instrumentpanelen.

:::image type="content" source="media/upcoming-changes/removing-iso-27001-legacy-implementation.png" alt-text="Security Center instrumentpanel för regelefterlevnad som visar meddelandet om borttagning av den äldre implementeringen av ISO 27001." lightbox="media/upcoming-changes/removing-iso-27001-legacy-implementation.png":::

### <a name="recommendations-from-aws-will-be-released-for-general-availability-ga"></a>Rekommendationer från AWS släpps för allmän tillgänglighet (GA)

**Beräknat datum för ändring:** Augusti 2021

Azure Security Center skyddar arbetsbelastningar i Azure, Amazon Web Services (AWS) och Google Cloud Platform (GCP).

Rekommendationerna från AWS Security Hub har varit i förhandsversion sedan molnanslutningsapparna introducerades. Rekommendationer som  flaggats som förhandsversion ingår inte i beräkningarna av dina säkerhetspoäng, men bör fortfarande åtgärdas när det är möjligt, så att de bidrar till dina poäng när förhandsversionsperioden är slut.

Med den här ändringen flyttas två uppsättningar AWS-rekommendationer till GA:

- [Security Hubs PCI DSS kontroller](https://docs.aws.amazon.com/securityhub/latest/userguide/securityhub-pci-controls.html)
- [Security Hubs CIS AWS Foundations Benchmark-kontroller](https://docs.aws.amazon.com/securityhub/latest/userguide/securityhub-cis-controls.html)

När dessa är GA och utvärderingarna körs på dina AWS-resurser påverkar resultatet din kombinerade säkerhetspoäng för alla dina multi- och hybridmolnresurser.



### <a name="enhancements-to-sql-data-classification-recommendation"></a>Förbättringar av sql-dataklassificeringsrekommendation

**Beräknat datum för ändring:** Andra kvartalet 2021

Rekommendationen **Känsliga data i dina SQL-databaser** ska klassificeras i Tillämpa säkerhetskontroll för **dataklassificering** och ersättas med en ny version som är bättre anpassad efter Microsofts strategi för dataklassificering. Därför ändras även rekommendationens ID (för närvarande är det b0df6f56-862d-4730-8597-38c0fd4ebd59).



## <a name="next-steps"></a>Nästa steg

Alla nya ändringar av produkten finns i [Vad är nytt i Azure Security Center?](release-notes.md).