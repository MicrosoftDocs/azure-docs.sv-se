---
title: CI/CD för Custom Speech tal-tjänst
titleSuffix: Azure Cognitive Services
description: Använd DevOps med Custom Speech-och CI/CD-arbetsflöden. Implementera en befintlig DevOps-lösning för ditt eget projekt.
services: cognitive-services
author: KatieProchilo
manager: cmayomsft
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/09/2020
ms.author: kaprochi
ms.openlocfilehash: f7f11ef097d3abee2b4c18c32a1cb215d9fe8ce8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "98939889"
---
# <a name="cicd-for-custom-speech"></a>CI/CD för Custom Speech

Implementera automatisk utbildning, testning och versions hantering för att möjliggöra kontinuerlig förbättring av Custom Speech modeller när du tillämpar uppdateringar av utbildning och testning av data. Genom effektiv implementering av CI/CD-arbetsflöden kan du se till att slut punkten för den bästa prestanda Custom Speechs modellen alltid är tillgänglig.

[Kontinuerlig integrering](/azure/devops/learn/what-is-continuous-integration) (CI) är den teknik som används ofta för att genomföra uppdateringar i en delad lagrings plats och utföra en automatiserad version av den. CI-arbetsflöden för Custom Speech träna en ny modell från sina data källor och utföra automatiserad testning av den nya modellen för att säkerställa att den fungerar bättre än den tidigare modellen.

[Kontinuerlig leverans](/azure/devops/learn/what-is-continuous-delivery) (CD) tar modeller från CI-processen och skapar en slut punkt för varje förbättrad Custom Speech modell. CD gör slut punkterna lätt att integrera i lösningar.

Anpassade CI/CD-lösningar är möjliga, men för en robust, förbyggd lösning använder du [DevOps-mallen](https://github.com/Azure-Samples/Speech-Service-DevOps-Template)för att köra CI/CD-arbetsflöden med GitHub-åtgärder.

## <a name="cicd-workflows-for-custom-speech"></a>CI/CD-arbetsflöden för Custom Speech

Syftet med dessa arbets flöden är att säkerställa att varje Custom Speech modell har bättre igenkännings precision än den tidigare versionen. Om uppdateringarna av testnings-och/eller tränings data förbättrar noggrannheten skapar de här arbets flödena en ny Custom Speech-slutpunkt.

Git-servrar som GitHub och Azure DevOps kan köra automatiserade arbets flöden när vissa git-händelser inträffar, till exempel sammanfogningar eller pull-begäranden. Ett CI-arbetsflöde kan till exempel aktive ras när uppdateringar av test data skickas till *huvud* grenen. Olika git-servrar har olika verktyg, men kommer att tillåta skript kommando rads gränssnitt (CLI) så att de kan köras på en build-Server.

På det här sättet bör arbets flöden namnge och lagra data, tester, testfiler, modeller och slut punkter så att de kan spåras tillbaka till den incheckning eller version de kom från. Det är också användbart att ge dessa till gångar ett namn så att det är enkelt att se vilka som har skapats efter att ha uppdaterat test data jämfört med tränings data.

### <a name="ci-workflow-for-testing-data-updates"></a>CI-arbetsflöde för att testa data uppdateringar

Huvud syftet med CI/CD-arbetsflöden är att skapa en ny modell med hjälp av tränings data och testa den modellen med hjälp av test data för att fastställa om [fel frekvensen för Word](how-to-custom-speech-evaluate-data.md#evaluate-custom-speech-accuracy) har förbättrats jämfört med den tidigare bästa modellen ("benchmark-modellen"). Om den nya modellen fungerar bättre blir den den nya benchmark-modellen mot vilken framtida modeller jämförs.

CI-arbetsflödet för att testa data uppdateringar bör testa om den aktuella benchmark-modellen med uppdaterade test data för att beräkna den ändrade WER. Detta säkerställer att när WER för en ny modell jämförs med WER för benchmark, har båda modellerna testats mot samma test data och du jämför som med vad som helst.

Det här arbets flödet bör utlösa uppdateringar för att testa data och:

- Testa benchmark-modellen mot uppdaterade test data.
- Lagra test resultatet som innehåller WER för benchmark-modellen med hjälp av uppdaterade data.
- WER från dessa tester blir den nya benchmark-WER som framtida modeller måste takta.
- CD-arbetsflödet för CD körs inte för uppdateringar för att testa data.

### <a name="ci-workflow-for-training-data-updates"></a>CI-arbetsflöde för tränings data uppdateringar

Uppdateringar av tränings data indikerar uppdateringar av den anpassade modellen.

Det här arbets flödet ska utlösas för uppdateringar av tränings data och:

- Träna en ny modell med uppdaterade tränings data.
- Testa den nya modellen mot test data.
- Lagra test resultatet, som innehåller WER.
- Jämför WER från den nya modellen till WER från benchmark-modellen.
- Stoppa arbets flödet om WER inte förbättras.
- Om WER har förbättrats, kör du CD-arbetsflödet för att skapa en Custom Speech slut punkt.

### <a name="cd-workflow"></a>CD-arbetsflöde

När en uppdatering av tränings data har förbättrat en modells igenkänning, ska CD-arbetsflödet automatiskt köras för att skapa en ny slut punkt för modellen och göra slut punkten tillgänglig så att den kan användas i en lösning.

#### <a name="release-management"></a>Versionshantering

De flesta team kräver en manuell gransknings-och godkännande process för distribution till en produktions miljö. För en produktions distribution kanske du vill se till att det sker när viktiga personer i utvecklings teamet är tillgängliga för support, eller under låg trafik perioder.

### <a name="tools-for-custom-speech-workflows"></a>Verktyg för Custom Speech arbets flöden

Använd följande verktyg för automatiserings arbets flöden för CI/CD för Custom Speech:

- [Azure CLI](/cli/azure/) för att skapa en Azure-tjänstens huvud namns autentisering, fråga Azure-prenumerationer och lagra test resultat i Azure blob.
- [Azure tal CLI](spx-overview.md) för att interagera med tal tjänsten från kommando raden eller ett automatiserat arbets flöde.

## <a name="devops-solution-for-custom-speech-using-github-actions"></a>DevOps-lösning för Custom Speech med GitHub-åtgärder

För en redan implementerad DevOps-lösning för Custom Speech går du till [DevOps-mallen för tal-lagrings platsen](https://github.com/Azure-Samples/Speech-Service-DevOps-Template). Skapa en kopia av mallen och börja utveckla anpassade modeller med ett robust DevOps-system som omfattar testning, utbildning och versions hantering med hjälp av GitHub-åtgärder. Lagrings platsen innehåller exempel på testning och träning av data som hjälper dig att konfigurera och förklara arbets flödet. Ersätt exempel data med dina projekt data efter den första installationen.

[DevOps-mallen för tal i lagrings platsen](https://github.com/Azure-Samples/Speech-Service-DevOps-Template) innehåller infrastrukturen och detaljerad vägledning för att:

- Kopiera mallens lagrings plats till ditt GitHub-konto och skapa sedan Azure-resurser och ett [tjänst huvud namn](../../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) för GitHub-åtgärdens CI/CD-arbetsflöden.
- Gå igenom "dev-den[inre slingan](/dotnet/architecture/containerized-lifecycle/design-develop-containerized-apps/docker-apps-inner-loop-workflow)". Uppdatera utbildning och testning av data från en funktions gren, testa ändringarna med en tillfällig utvecklings modell och generera en pull-begäran om att föreslå och granska ändringarna.
- När tränings data uppdateras i en pull-begäran till *huvud*, träna modeller med GitHub Actions CI-arbetsflöde.
- Utför automatiserad precisions testning för att upprätta en modells [ord fel frekvens](how-to-custom-speech-evaluate-data.md#evaluate-custom-speech-accuracy) (WER). Lagra test resultatet i Azure blob.
- Kör ett CD-arbetsflöde för att skapa en slut punkt när WER förbättras.

## <a name="next-steps"></a>Nästa steg

Lär dig mer om DevOps med tal:

- Använd [DevOps-mallen för tal](https://github.com/Azure-Samples/Speech-Service-DevOps-Template) -och lagrings platsen för att implementera DevOps för Custom Speech med GitHub-åtgärder.
