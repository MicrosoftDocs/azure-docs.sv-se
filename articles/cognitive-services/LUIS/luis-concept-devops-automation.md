---
title: Kontinuerliga arbets flöden för LUIS-appar
description: Implementera CI/CD-arbetsflöden för DevOps för Language Understanding (LUIS).
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 06/5/2020
ms.openlocfilehash: 215399e4b131162097e54c15b84cb6fa7dac72e3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "98932544"
---
# <a name="continuous-integration-and-continuous-delivery-workflows-for-luis-devops"></a>Kontinuerlig integrering och kontinuerliga leverans arbets flöden för LUIS-DevOps

Program varu tekniker som utvecklar en Language Understanding-app (LUIS) kan tillämpa DevOps-metoder kring [käll kontroll](luis-concept-devops-sourcecontrol.md), [automatiserade versioner](luis-concept-devops-automation.md), [testning](luis-concept-devops-testing.md)och [versions hantering](luis-concept-devops-automation.md#release-management). I den här artikeln beskrivs begrepp för att implementera automatiska versioner för LUIS.

## <a name="build-automation-workflows-for-luis"></a>Bygg Automation-arbetsflöden för LUIS

![CI-arbetsflöden](./media/luis-concept-devops-automation/luis-automation.png)

I ditt system för käll kods hantering (SCM) konfigurerar du automatiska Bygg pipelines för att köras vid följande händelser:

1. **PR-arbetsflöde** som utlöses när en [pull-begäran](https://help.github.com/github/collaborating-with-issues-and-pull-requests/about-pull-requests) (PR) höjs. Det här arbets flödet verifierar innehållet i PR *innan* uppdateringarna blir sammanfogade i huvud grenen.
1. **CI/CD-arbetsflöde** utlöses när uppdateringar skickas till huvud grenen, till exempel vid sammanslagning av ändringar från en PR. Det här arbets flödet säkerställer kvaliteten på alla uppdateringar till huvud grenen.

**CI/CD-arbetsflödet** kombinerar två kompletterande utvecklings processer:

* [Kontinuerlig integrering](/azure/devops/learn/what-is-continuous-integration) (CI) är den tekniska praxis som ofta utför kod i en delad lagrings plats och som gör en automatiserad version av den. En kontinuerlig integrering med en automatiserad [testnings](luis-concept-devops-testing.md) metod gör att vi kan verifiera att för varje uppdatering, LUDown-källan fortfarande är giltig och kan importeras till en Luis-app, men även att den skickar en grupp tester som kontrollerar att den tränade appen kan identifiera de intentor och entiteter som krävs för din lösning.

* [Kontinuerlig leverans](/azure/devops/learn/what-is-continuous-delivery) (CD) använder det kontinuerliga integrerings konceptet för att automatiskt distribuera programmet till en miljö där du kan göra mer djupgående testning. CD gör det möjligt för oss att lära sig tidigt om eventuella oförutsedda problem som uppstår från våra ändringar så snabbt som möjligt och även för att lära sig om luckor i vår test omfattning.

Målet med kontinuerlig integrering och kontinuerlig leverans är att säkerställa att "main alltid kan levereras". För en LUIS-app innebär detta att vi kunde, om vi behövde, ta alla versioner från LUIS-appen för huvud kontoret och leverera dem i produktion.

### <a name="tools-for-building-automation-workflows-for-luis"></a>Verktyg för att skapa Automation-arbetsflöden för LUIS

Det finns olika bygg funktioner för att skapa Automation-arbetsflöden. Alla kräver att du kan skript steg med hjälp av ett kommando rads gränssnitt (CLI) eller REST-anrop så att de kan köras på en build-Server.

Använd följande verktyg för att skapa Automation-arbetsflöden för LUIS:

* [Bot Framework tools Luis CLI](https://github.com/microsoft/botbuilder-tools/tree/master/packages/LUIS) för att fungera med Luis-appar och-versioner, träna, testa och publicera dem i Luis-tjänsten.

* [Azure CLI](/cli/azure/) för att fråga Azure-prenumerationer, Hämta Luis-redigerings-och förutsägelse nycklar och för att skapa ett Azure [-tjänstens huvud namn](/cli/azure/ad/sp) som används för Automation-autentisering.

* [NLU. DevOps](https://github.com/microsoft/NLU.DevOps) -verktyg för att [testa en Luis-app](luis-concept-devops-testing.md) och analysera test resultaten.

### <a name="the-pr-workflow"></a>PR-arbetsflödet

Som nämnts konfigurerar du det här arbets flödet så att det körs när en utvecklare genererar en PR för att föreslå ändringar som ska slås samman från en funktions gren till huvud grenen. Syftet med detta är att kontrol lera kvaliteten på ändringarna i PR innan de slås samman till huvud grenen.

Arbets flödet bör:

* Skapa en tillfällig LUIS-app genom att importera `.lu` källan i PR.
* Träna och publicera LUIS-versionen av appen.
* Kör alla [enhets test](luis-concept-devops-testing.md) mot det.
* Skicka arbets flödet om alla testerna passerar, annars att de inte fungerar.
* Rensa och ta bort den tillfälliga appen.

Om du har stöd för SCM konfigurerar du gren skydds regler så att arbets flödet måste slutföras innan PR kan slutföras.

### <a name="the-main-branch-cicd-workflow"></a>Huvud gren CI/CD-arbetsflöde

Konfigurera det här arbets flödet så att det körs när uppdateringarna i PR har slagits samman i huvud grenen. Syftet med detta är att behålla kvalitets fältet för huvud grenen högt genom att testa uppdateringarna. Om uppdateringarna uppfyller kvalitets fältet distribuerar det här arbets flödet den nya versionen av LUIS-appen till en miljö där du kan göra mer djupgående testning.

Arbets flödet bör:

* Bygg en ny version i din primära LUIS-app (den app som du upprätthåller för huvud grenen) med hjälp av den uppdaterade käll koden.

* Träna och publicera LUIS-versionen av appen.

  > [!NOTE]
  > Som förklaras i [köra tester i ett automatiserat Bygg arbets flöde](luis-concept-devops-testing.md#running-tests-in-an-automated-build-workflow) måste du publicera Luis-appen under test så att verktyg som NLU. DevOps har åtkomst till den. LUIS stöder endast två namngivna publicerings platser, *mellanlagring* och *produktion* för en Luis-app, men du kan också [publicera en version direkt](https://github.com/microsoft/botframework-cli/blob/master/packages/luis/README.md#bf-luisapplicationpublish) och [fråga efter version](./luis-migration-api-v3.md#changes-by-slot-name-and-version-name). Använd direkt versions publicering i dina automatiserade arbets flöden för att undvika att använda de namngivna publicerings platserna.

* Kör alla [enhets test](luis-concept-devops-testing.md).

* Du kan också köra [batch-test](luis-concept-devops-testing.md#how-to-do-unit-testing-and-batch-testing) för att mäta kvalitet och precision för Luis-appens version och jämföra den med vissa bas linjer.

* Om testerna har slutförts:
  * Tagga källan i lagrings platsen.
  * Kör jobbet för kontinuerlig leverans (CD) för att distribuera LUIS app-versionen till miljöer för ytterligare testning.

### <a name="continuous-delivery-cd"></a>Kontinuerlig leverans (CD)

CD-jobbet i ett CI/CD-arbetsflöde körs villkorligt på framgångs rik av testerna build och automatisk enhet. Sitt jobb är att automatiskt distribuera LUIS-programmet till en miljö där du kan utföra fler tester.

Det finns ingen Rekommenderad lösning för hur du bäst distribuerar LUIS-appen och du måste implementera den process som är lämplig för ditt projekt. [Luis DevOps Template](https://github.com/Azure-Samples/LUIS-DevOps-Template) lagrings platsen implementerar en enkel lösning som är att [publicera den nya versionen av Luis-appen](./luis-how-to-publish-app.md) till *produktions* publicerings platsen. Detta är bra för en enkel installation. Men om du behöver stöd för ett antal olika produktions miljöer samtidigt, till exempel *utveckling*, *mellanlagring* och *UAT*, blir gränsen för två namngivna publicerings fack per app otillräcklig.

Andra alternativ för att distribuera en app-version är:

* Lämna app-versionen publicerad till slut versionen av slut punkten och implementera en process för att konfigurera nedströms produktions miljöer med slut versions slut punkten vid behov.
* Underhåll olika LUIS-appar för varje produktions miljö och skriv automatiserings steg för att importera `.lu` till en ny version i Luis-appen för mål produktions miljön, för att träna och publicera den.
* Exportera den testade LUIS app-versionen till en [Luis Docker-behållare](./luis-container-howto.md?tabs=v3) och distribuera Luis-behållaren till Azure [container instances](../../container-instances/index.yml).

## <a name="release-management"></a>Versionshantering

Vanligt vis rekommenderar vi att du utför kontinuerlig leverans enbart till miljöer som inte är för produktion, till exempel utveckling och mellanlagring. De flesta team kräver en manuell gransknings-och godkännande process för distribution till en produktions miljö. För en produktions distribution kanske du vill se till att det sker när viktiga personer i utvecklings teamet är tillgängliga för support, eller under låg trafik perioder.

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [implementerar DevOps för Luis med GitHub](luis-how-to-devops-with-github.md)
* Lär dig hur du skriver ett [arbets flöde för GitHub-åtgärder med NLU. DevOps](https://github.com/Azure-Samples/LUIS-DevOps-Template/blob/master/docs/4-pipeline.md)
