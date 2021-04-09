---
title: Fas av kund godkännande för processens livs cykel för grupp data vetenskap
description: Mål, uppgifter och slut produkter för kundernas godkännande steg för dina data vetenskaps projekt
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: f2294ccb1d958b229a71e45bb502b8134d8d5c7f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "93305664"
---
# <a name="customer-acceptance-stage-of-the-team-data-science-process-lifecycle"></a>Fas av kund godkännande för processens livs cykel för grupp data vetenskap

Den här artikeln beskriver de mål, uppgifter och slut produkter som är kopplade till kund godkännande fasen för team data science-processen (TDSP). Den här processen ger en rekommenderad livs cykel som du kan använda för att strukturera data vetenskaps projekt. Livs cykeln beskriver de viktigaste faserna som projekt vanligt vis körs, ofta iterativt:

   1. **Förståelse för verksamheten**
   2. **Förvärv och förståelse av data**
   3. **Modellering**
   4. **Distribution**
   5. **Kundgodkännande**

Här är en visuell representation av TDSP-livs cykeln: 

![TDSP-livscykel](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goal"></a>Mål
**Slutför projektets slut produkter**: bekräfta att pipelinen, modellen och deras distribution i en produktions miljö uppfyller kundens mål.

## <a name="how-to-do-it"></a>Gör så här
Det finns två huvudsakliga uppgifter i det här steget:

   * **System verifiering**: bekräfta att den distribuerade modellen och pipelinen uppfyller kundens behov.
   * **Projektets behållning**: gör om projektet till den entitet som ska köra systemet i produktion.

Kunden bör kontrol lera att systemet uppfyller deras affärs behov och att det svarar på frågor med acceptabel exakthet för att distribuera systemet till produktion för användning av klientens program. All dokumentation har slutförts och granskats. Projektet har överlämnats till den enhet som ansvarar för åtgärder. Den här entiteten kan t. ex. vara en IT-eller Customer data-science-grupp eller en agent för kunden som ansvarar för att köra systemet i produktion. 

## <a name="artifacts"></a>Artifacts
Huvud artefakten som skapas i det här slutliga skedet är **avslutnings rapporten för projektet för kunden**. Den här tekniska rapporten innehåller all information om projektet som är användbart för att lära dig hur du använder systemet. TDSP tillhandahåller en mall för att [Avsluta rapporter](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Project/Exit%20Report.md) . Du kan använda mallen som den är, eller så kan du anpassa den efter vissa klient behov. 


## <a name="next-steps"></a>Nästa steg

Här följer länkar till varje steg i livs cykeln för TDSP:

   1. [Förståelse för verksamheten](lifecycle-business-understanding.md)
   2. [Förvärv och förståelse av data](lifecycle-data.md)
   3. [Modellering](lifecycle-modeling.md)
   4. [Distribution](lifecycle-deployment.md)
   5. [Kundgodkännande](lifecycle-acceptance.md)

Vi tillhandahåller fullständiga genom gångar som demonstrerar alla steg i processen för olika scenarier. [Exempel](walkthroughs.md) artikeln innehåller en lista över scenarier med länkar och miniatyr beskrivningar. Genom gången illustrerar hur du kombinerar moln, lokala verktyg och tjänster till ett arbets flöde eller en pipeline för att skapa ett intelligent program. 

Exempel på hur du kör steg i TDSPs som använder Azure Machine Learning Studio finns i [använda TDSP med Azure Machine Learning](./index.yml).