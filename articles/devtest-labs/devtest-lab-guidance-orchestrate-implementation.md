---
title: Samordna implementeringen av Azure DevTest Labs
description: Den här artikeln innehåller rikt linjer för att dirigera implementering av Azure DevTest Labs i din organisation.
ms.topic: article
ms.date: 06/26/2020
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: 1958e818f014b7419a1a33e9453fbad460dfc159
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92330622"
---
# <a name="orchestrate-the-implementation-of-azure-devtest-labs"></a>Dirigera implementeringen av Azure DevTest Labs
Den här artikeln innehåller en rekommenderad metod för snabb distribution och implementering av Azure DevTest Labs. Följande bild betonar den övergripande processen som vägledning och ger flexibilitet för att stödja olika bransch krav och scenarier.

![Steg för att implementera Azure DevTest Labs](./media/devtest-lab-guidance-orchestrate-implementation/implementation-steps.png)

## <a name="assumptions"></a>Antaganden
Den här artikeln förutsätter att du har följande objekt på plats innan du implementerar en DevTest Labs-pilot:

- **Azure-prenumeration**: pilot teamet har åtkomst till att distribuera resurser till en Azure-prenumeration. Om arbets belastningarna endast är utveckling och testning rekommenderar vi att du väljer Enterprise DevTest-erbjudandet för ytterligare tillgängliga avbildningar och lägre priser på virtuella Windows-datorer.
- **Lokal åtkomst**: vid behov har lokal åtkomst redan kon figurer ATS. Den lokala åtkomsten kan utföras via en VPN-anslutning från plats till plats eller via Express Route. Anslutning via Express Route kan vanligt vis ta många veckor att upprätta, vi rekommenderar att du har en Express-väg på plats innan du startar projektet.
- **Pilot team**: de första utvecklings projekt teamet som använder DevTest Labs har identifierats tillsammans med lämpliga utvecklings-eller testnings aktiviteter och upprättar krav/mål/mål för dessa team.

## <a name="milestone-1-establish-initial-network-topology-and-design"></a>Mil stolpe 1: upprätta inledande nätverkstopologi och design
Det första avsnittet i fokus när du distribuerar en Azure DevTest Labs-lösning är att upprätta den planerade anslutningen för de virtuella datorerna. Följande steg beskriver de nödvändiga procedurerna:

1. Definiera **första IP-adressintervall** som har tilldelats DevTest Labs-prenumerationen i Azure. Det här steget kräver en prognos av förväntad användning i antal virtuella datorer så att du kan tillhandahålla ett tillräckligt stort block för framtida expansion.
2. Identifiera **metoder för önskad åtkomst** i DevTest Labs (till exempel extern/intern åtkomst). En viktig punkt i det här steget är att fastställa om virtuella datorer har offentliga IP-adresser (som är tillgängliga direkt från Internet).
3. Identifiera och upprätta **anslutnings metoder** med resten av moln miljön i Azure och lokalt. Om den tvingade routningen med Express Route är aktive rad, är det troligt att de virtuella datorerna behöver rätt proxykonfiguration för att passera företags brand väggen.
4. Om de virtuella datorerna **ska vara domänanslutna, avgör** du om de ansluter till en molnbaserad domän (t. ex. katalog tjänster av AAD) eller en lokal domän. För lokal, Bestäm vilken organisationsenhet (OU) i Active Directory som de virtuella datorerna ansluter till. Bekräfta dessutom att användarna har åtkomst till (eller upprätta ett tjänst konto som har möjlighet att skapa dator poster i domänen)

## <a name="milestone-2-deploy-the-pilot-lab"></a>Mil stolpe 2: Distribuera pilot labbet
När nätverk sto pol Ogin är på plats kan du skapa det första/pilot labbet genom att vidta följande steg:

1. Skapa en första DevTest Labs-miljö.
2. Bestäm vilka tillåtna VM-avbildningar och storlekar som ska användas med labb. Bestäm om anpassade avbildningar kan överföras till Azure för användning med DevTest Labs.
3. Skydda åtkomsten till labbet genom att skapa en första Azure-rollbaserad åtkomst kontroll (Azure RBAC) för labbet (labb ägare och labb användare). Vi rekommenderar att du använder synkroniserade Active Directory-konton med Azure Active Directory för identitet med DevTest Labs.
4. Konfigurera DevTest Labs för att använda principer som scheman, kostnads hantering, virtuella datorer som kan krävas, anpassade bilder eller formler.
5. Upprätta en online-lagringsplats, till exempel Azure databaser/git.
6. Bestäm om du vill använda offentliga eller privata databaser eller kombinationen av båda. Organisera JSON-mallar för distributioner och långsiktigt skydd.
7. Skapa anpassade artefakter om det behövs. Det här är valfritt. 

## <a name="milestone-3-documentation-support-learn-and-improve"></a>Mil stolpe 3: dokumentation, support, lära och förbättra
De ursprungliga pilot teamen kan kräva djupgående support för att komma igång. Använd deras upplevelser för att se till att rätt dokumentation och support är på plats för fortsatt distribution av Azure DevTest Labs.

1. Presentera pilot teamen för de nya DevTest Labs-resurserna (demonstrationer, dokumentation)
2. Utifrån pilot Teams erfarenheter, planera och leverera dokumentation efter behov
3. Formalisera process för att registrera nya team (skapa och konfigurera labb, ge åtkomst osv.)
4. Under den första upptagningen verifierar du att den ursprungliga prognosen för IP-adressutrymmet fortfarande är rimlig och korrekt
5. Se till att lämpliga efterlevnads-och säkerhets granskningar har slutförts

## <a name="next-steps"></a>Nästa steg
Se nästa artikel i den här serien: [styrning av Azure DevTest Labs infrastruktur](devtest-lab-guidance-governance-resources.md)
