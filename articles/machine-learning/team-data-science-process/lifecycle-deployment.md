---
title: Distributions steg i livs cykeln för team data science process
description: Mål, uppgifter och slut produkter för distributions fasen för dina data vetenskaps projekt
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
ms.openlocfilehash: c3bf8e5f81ae7bf35ff34039fa1e81c9fd4a406b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93324539"
---
# <a name="deployment-stage-of-the-team-data-science-process-lifecycle"></a>Distributions steg i livs cykeln för team data science process

Den här artikeln beskriver de mål, uppgifter och slut produkter som är kopplade till distributionen av team data science-processen (TDSP). Den här processen ger en rekommenderad livs cykel som du kan använda för att strukturera data vetenskaps projekt. Livs cykeln beskriver de viktigaste faserna som projekt vanligt vis körs, ofta iterativt:

   1. **Förståelse för verksamheten**
   2. **Förvärv och förståelse av data**
   3. **Modellering**
   4. **Distribution**
   5. **Kundgodkännande**

Här är en visuell representation av TDSP-livs cykeln: 

![TDSP-livscykel](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goal"></a>Mål
Distribuera modeller med en datapipeline till en produktions-eller produktions miljö för slutlig användar godkännande. 

## <a name="how-to-do-it"></a>Gör så här
Huvud uppgiften adresseras i det här steget:

**Operationalisera-modellen**: Distribuera modellen och pipelinen till en produktions-eller produktions miljö för program förbrukning.

### <a name="operationalize-a-model"></a>Operationalisera en modell
När du har en uppsättning modeller som fungerar bra kan du operationalisera dem för andra program att använda. Beroende på affärs kraven görs förutsägelserna antingen i real tid eller på grund av en batch. För att distribuera modeller exponerar du dem med ett öppet API-gränssnitt. Gränssnittet gör att modellen lätt kan användas från olika program, t. ex.:

   * Online-webbplatser
   * Kalkylblad 
   * Instrumentpaneler
   * Branschspecifika program 
   * Server dels program 

Exempel på modell driftsättning med en Azure Machine Learning-webbtjänst finns i [distribuera en Azure Machine Learning-webb tjänst](../classic/deploy-a-machine-learning-web-service.md). Det är en bra idé att bygga telemetri och övervakning i produktions modellen och den datapipeline som du distribuerar. Den här metoden hjälper till med efterföljande system status rapportering och fel sökning.  

## <a name="artifacts"></a>Artifacts

* En status instrument panel som visar systemets hälso tillstånd och nyckel mått
* En slutlig modell rapport med distributions information
* Ett dokument för slutlig lösnings arkitektur


## <a name="next-steps"></a>Nästa steg

Här följer länkar till varje steg i livs cykeln för TDSP:

   1. [Förståelse för verksamheten](lifecycle-business-understanding.md)
   2. [Data hämtning och förståelse](lifecycle-data.md)
   3. [Modellering](lifecycle-modeling.md)
   4. [Distribution](lifecycle-deployment.md)
   5. [Kundgodkännande](lifecycle-acceptance.md)

Vi tillhandahåller fullständiga genom gångar som demonstrerar alla steg i processen för olika scenarier. [Exempel](walkthroughs.md) artikeln innehåller en lista över scenarier med länkar och miniatyr beskrivningar. Genom gången illustrerar hur du kombinerar moln, lokala verktyg och tjänster till ett arbets flöde eller en pipeline för att skapa ett intelligent program. 

Exempel på hur du kör steg i TDSPs som använder Azure Machine Learning Studio finns i [använda TDSP med Azure Machine Learning](./index.yml).