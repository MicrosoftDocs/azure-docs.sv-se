---
title: Microsoft Security code Analysis-versioner
description: Den här artikeln beskriver kommande versioner för tillägget Microsoft Security code Analysis
author: sukhans
manager: sukhans
ms.author: terrylan
ms.date: 04/24/2020
ms.topic: article
ms.service: security
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: 33ce2a496caa52609d8bdf8c92e29064ca4ae349
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "85362048"
---
# <a name="microsoft-security-code-analysis-releases-and-roadmap"></a>Microsoft Security code Analysis-versioner och-översikt

Microsoft Security code Analysis team i partnerskap med Developer Support är stolt över att meddela nya och kommande förbättringar i vårt MSCA-tillägg.


## <a name="credential-scanner-v20-released-in-april-2020"></a>Credential Scanner v 2.0: lanserades i april 2020

### <a name="innovations--improvements"></a>Innovationer & förbättringar

- **Kärn motor**

   - Genomsnittlig prestanda uppgradering av 25% med nära linjära körnings tider
   - Kontext/bevis-baserad sökning och rangordning för ökad noggrannhet
   - Förbättringar av allmänna lösen ords identifieringar och matchnings logik för uppenbara plats hållare (till exempel fakePassword)

- **Täcknings** stöd för 25 + hemliga typer, inklusive följande mest efterfrågade:

   - Lösen fras för infrastruktur konto certifikat
   - Klient hemlighet/API-nyckel
   - HTTP-Authorization-huvud
   - Amazon S3-klient hemlig åtkomst nyckel
   - Azure Active Directory klientens åtkomsttoken
   - Azure Function Master/API-nyckel
   - Power BI åtkomst nyckel
   - Lösen ords mönster för Azure Resource Manager mall

- **Utdata**

   - Stöd för fil format för fil formatet SARIF 2,1 och CSV

## <a name="binskim-v160-released-in-april-2020"></a>BinSkim v-1.6.0: lanserades i april 2020

### <a name="improvements"></a>Bättre

- FUNKTION: uppdatera till final SARIF v2 (version 2.1.16). Den här uppdateringen möjliggör cachelagring av resultat vid överföring av--hash-värden på kommando raden, en betydande prestanda förbättring vid rekursiv analys av kataloger med flera kopior av Sök mål.
- FEL korrigering: korrigera skrivfel i BA2021. DoNotMarkWritableSectionsAsExecutable-utdata.
- PRESTANDA: eliminerar PDB-inläsning för alla icke-blandade läge för hanterade sammansättningar, inklusive IL-bibliotek (i förväg kompilerade) binärfiler.
- FELAKTIG negativ korrigering: kontrol lera att en PDB som placeras bredvid en binär faktiskt matchar binärfilen under analys
- FUNKTION: Ange--Local-symbol-katalog argument om du vill ange ytterligare (lokala, icke-symbol-Server) PDB-sökplatser
- FELAKTIG positiv korrigering: hoppa över PDB-driven analys för den genererade .NET Core Native bootstrap-exe (som inte är en användar kod).

## <a name="whats-next-in-q3-cy20"></a>Vad är nästa i Q3 CY20?

- Java Security Analysis-verktyg
- Säkerhets analys verktyg för python
- ES luddfri för att ersätta TS luddfri för TypeScript och Java Script
- Analys verktyg för Resource Manager-mallar

## <a name="tool-deprecation-notification"></a>Meddelande om föråldrat verktyg

### <a name="microsoft-security-risk-detection-msrd-is-deprecated-on-june-26-2020"></a>Microsofts säkerhets risk identifiering (MSRD) är föråldrad den 26 2020 juni.

Den föråldrade MSRD-tjänsten kommer att ersättas med en lokal källa med egen värd för utvecklare som är suddig för Azure. Den här plattformen håller på att utvecklas och testas i partnerskap med många av Microsofts kärn produkt team. Den här suddiga plattformen integrerar sanerare och möjliggör anpassningsbara, inlärnings fuzz-tester som är inbyggda i CI/CD-pipeliner som växer med tiden för program varu projekt. Den här plattformens version av öppen källkod har schemalagts för den senare hälften av 2020.

## <a name="next-steps"></a>Nästa steg

Instruktioner för hur du installerar och installerar Microsofts säkerhets kod analys finns i vår [onboarding-och installations guide](security-code-analysis-onboard.md).

Om du har fler frågor om tillägget och de verktyg som erbjuds kan du kolla in vår [vanliga frågor och svar](security-code-analysis-faq.md).
