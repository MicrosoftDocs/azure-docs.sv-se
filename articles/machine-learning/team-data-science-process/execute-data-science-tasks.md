---
title: Köra data vetenskaps uppgifter – team data science process
description: Hur en data expert kan köra ett data vetenskaps projekt i ett spårat, versions beskrivande och samverkande sätt.
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/17/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: e180ecbf5c68dbd9c179244083a641ac6ed42de0
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100371965"
---
# <a name="execute-data-science-tasks-exploration-modeling-and-deployment"></a>Köra data vetenskaps uppgifter: utforskning, modellering och distribution

Vanliga uppgifter för data vetenskap inkluderar data utforskning, modellering och distribution. Den här artikeln beskriver aktiviteter för att utföra flera vanliga data vetenskaps uppgifter, till exempel interaktiv data granskning, data analys, rapportering och skapande av modeller. Alternativ för att distribuera en modell i en produktions miljö kan vara:

- [Azure Machine Learning](../index.yml)
- [SQL-Server med ML-tjänster](/sql/advanced-analytics/r/r-services)
- [Microsoft Machine Learning Server](/machine-learning-server/what-is-machine-learning-server)


## <a name="1--exploration"></a>1. <a name='DataQualityReportUtility-1'></a> utforskning 

En data expert kan utföra utforskning och rapportering på flera olika sätt: genom att använda bibliotek och paket som är tillgängliga för python (t. ex. matplotlib) eller med R (ggplot eller rutindex till exempel). Data experter kan anpassa sådan kod så att den passar behoven för data utforskning för vissa scenarier. Behovet av att hantera strukturerade data är olika för ostrukturerade data, till exempel text eller bilder. 

Produkter som Azure Machine Learning även tillhandahålla [Avancerad data förberedelse](../how-to-create-register-datasets.md) för data datatransformering och utforskning, inklusive att skapa funktioner. Användaren bör bestämma vilka verktyg, bibliotek och paket som bäst motsvarar deras behov. 

Slut produkten i slutet av den här fasen är en data utforsknings rapport. Rapporten bör ge en ganska omfattande vy över de data som ska användas för modellering och en bedömning av om data är lämpliga för att gå vidare till modell steget. 

## <a name="2--modeling"></a>2. <a name='ModelingUtility-2'></a> modellering

Det finns flera olika verktyg och paket för utbildnings modeller på flera olika språk. Data experter bör vara kostnads fria att använda, så länge de är bekanta med, så länge prestanda överväganden gällande precision och svars tid är uppfyllda för relevanta affärs användnings fall och produktions scenarier.

### <a name="model-management"></a>Modellhantering
När flera modeller har skapats, behöver du vanligt vis ett system för att registrera och hantera modeller. Vanligt vis behöver du en kombination av skript eller API: er och en server dels databas eller versions hanterings system. Några alternativ som du kan tänka på för dessa hanterings uppgifter är:

1. [Azure Machine Learning-modell hanterings tjänst](../index.yml)
2. [ModelDB från MIT](https://people.csail.mit.edu/mvartak/papers/modeldb-hilda.pdf) 
3. [SQL-Server som ett modell hanterings system](https://blogs.technet.microsoft.com/dataplatforminsider/2016/10/17/sql-server-as-a-machine-learning-model-management-system/)
4. [Microsoft Machine Learning Server](/sql/advanced-analytics/r/r-server-standalone)

## <a name="3--deployment"></a>3. <a name='Deployment-3'></a> distribution

Med produktions distributionen kan en modell spela en aktiv roll i ett företag. Förutsägelser från en distribuerad modell kan användas för affärs beslut.

### <a name="production-platforms"></a>Produktions plattformar
Det finns olika metoder och plattformar för att ställa in modeller i produktion. Här följer några alternativ:


- [Modell distribution i Azure Machine Learning](../how-to-deploy-and-where.md)
- [Distribution av en modell i SQL-Server](/sql/advanced-analytics/tutorials/sqldev-py6-operationalize-the-model)
- [Microsoft Machine Learning Server](/sql/advanced-analytics/r/r-server-standalone)

> [!NOTE]
> Innan distributionen är det en som måste kontrol lera svars tiden för att modell poängen är tillräckligt låg för att kunna användas i produktionen.
>
>

Fler exempel finns i genom gångar som demonstrerar alla steg i processen för **olika scenarier**. De visas och länkas med miniatyr beskrivningar i [exempel](walkthroughs.md) artikeln. De illustrerar hur du kombinerar moln, lokala verktyg och tjänster till ett arbets flöde eller en pipeline för att skapa ett intelligent program.

> [!NOTE]
> Information om distribution med hjälp av Azure Machine Learning Studio finns i [distribuera en Azure Machine Learning-webbtjänst](../classic/deploy-a-machine-learning-web-service.md).
>
>

### <a name="ab-testing"></a>A/B-testning
När flera modeller är i produktion kan det vara praktiskt att utföra [ett/B-test](https://en.wikipedia.org/wiki/A/B_testing) för att jämföra modellernas prestanda. 

 
## <a name="next-steps"></a>Nästa steg

[Spåra förloppet för data vetenskaps projekt](track-progress.md) visar hur en data expert kan spåra förloppet för ett data vetenskaps projekt.

[Modell åtgärden och CI/CD](ci-cd-flask.md) visar hur CI/CD kan utföras med utvecklade modeller.
