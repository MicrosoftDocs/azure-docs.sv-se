---
title: Skapa risk utvärderings rapporter
description: Få insikt i nätverks risker som identifieras av enskilda sensorer eller en sammanställd vy med risker som upptäckts av alla sensorer.
ms.date: 12/17/2020
ms.topic: how-to
ms.openlocfilehash: 853157ef1b97fefdd15785b2a71c7ccc5d06a9a9
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/22/2021
ms.locfileid: "104784262"
---
# <a name="risk-assessment-reporting"></a>Rapportering om riskbedömning

## <a name="about-risk-assessment-reports"></a>Om riskbedömnings rapporter

Rapporter för riskbedömning ger:

- En övergripande säkerhets Poäng för de enheter som identifieras av organisationens sensorer.

- En säkerhets Poäng för varje nätverks enhet som identifieras av en enskild sensor.

- En uppdelning av antalet sårbara enheter, enheter som behöver förbättring och säkra enheter.

-  Inblick i säkerhets-och drifts problem:

    - Konfigurations problem

    - Enhets sårbarhet prioriterad av säkerhets nivå

    - Nätverks säkerhets problem

    - Problem med nätverks användning

    - Anslutningar till ICS-nätverk

    - Internet anslutningar

    - Indikatorer för industriella skadlig kod

    - Protokollproblem

    - Angrepps vektorer

### <a name="risk-mitigation"></a>Risk minskning

Rapporter ger rekommendationer som hjälper dig att förbättra din säkerhets poäng. Du kan till exempel installera de senaste säkerhets uppdateringarna, uppgradera den inbyggda program varan till den senaste versionen eller följa upp aviseringar.

## <a name="about-security-scores"></a>Om säkerhets resultat

Övergripande nätverks säkerhets Poäng skapas i varje rapport. Poängen visar procent andelen 100 procent av säkerheten. Poängen på 30% skulle till exempel indikera att nätverket är 30% säkert.

Resultatet av riskbedömningen baseras på information som har lärts från paket inspektion, beteende modellerings motorer och en SCADA-speciell tillstånds dator design.

**Säkra enheter** är enheter med en säkerhets poäng över 90%.

**Enheter som behöver förbättras**: enheter med en säkerhets poäng mellan 70 och 89%.

**Sårbara enheter** är enheter med säkerhets poängen under 70%.

## <a name="create-risk-assessment-reports"></a>Skapa risk utvärderings rapporter

Skapa en riskbedömnings rapport för PDF. Rapport namnet genereras automatiskt som risk-assessment-report-1.pdf. Numret uppdateras för varje ny rapport som du skapar.  Tiden och dagen för skapandet visas.

### <a name="create-a-sensor-risk-assessment-report"></a>Skapa en utvärderings rapport för sensor risker

Skapa en riskbedömnings rapport baserat på identifieringar som görs av sensorn som du är inloggad på.

Så här skapar du en rapport:

1. Logga in på sensor konsolen.
1. Välj **riskbedömning** på sido menyn.
1. Välj **Generera rapport**. Rapporten visas i avsnittet arkiverade rapporter.
1. Välj rapporten i avsnittet arkiverade rapporter för att ladda ned den.

:::image type="content" source="media/how-to-generate-reports/risk-assessment.png" alt-text="En vy över riskbedömningen.":::

Så här importerar du en företags logo typ:

- Välj **import-logotyp**.

### <a name="create-an-on-premises-management-console-risk-assessment-report"></a>Skapa en riskbedömnings rapport för en lokal hanterings konsol

Skapa en riskbedömnings rapport baserat på identifieringar som gjorts av de sensorer som hanteras av den lokala hanterings konsolen. 

Så här skapar du en rapport:

1. Välj **riskbedömning** på sido menyn.

2. Välj en sensor i list rutan **Välj sensor** .

3. Välj **Generera rapport**.

4. Välj **Hämta** i avsnittet **arkiverade rapporter** .

Så här importerar du en företags logo typ:

- Välj **import-logotyp**.

:::image type="content" source="media/how-to-generate-reports/import-logo-screenshot.png" alt-text="Importera din logo typ via vyn riskbedömning.":::

## <a name="see-also"></a>Se även

[Rapportering om angrepps vektor](how-to-create-attack-vector-reports.md)

