---
title: Onboarding-guide för Microsoft Security code Analysis
description: Lär dig hur du installerar och installerar tillägget Microsoft Security code analysis. Se krav och visa ytterligare resurser.
author: sukhans
manager: sukhans
ms.author: terrylan
ms.date: 03/22/2021
ms.topic: article
ms.service: security
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: c827dc81c1ef1ab03dd6c9178a609b512e59ef15
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2021
ms.locfileid: "104801222"
---
# <a name="onboarding-and-installing"></a>Registrering och installation

> [!Note]
> Från och med den 1 mars 2022 kommer tillägget Microsoft Security code Analysis (MSCA) att dras tillbaka. Befintliga MSCA-kunder behåller sin åtkomst till MSCA till och med den 1 mars 2022. Mer information om alternativa alternativ i Azure-DevOps finns i [OWASP för käll kods analys](https://owasp.org/www-community/Source_Code_Analysis_Tools) . För att kunderna ska kunna migrera till GitHub kan du kolla [GitHub Advanced Security](https://docs.github.com/github/getting-started-with-github/about-github-advanced-security).

Krav för att komma igång med Microsofts säkerhets kod analys:

- Ett berättigat Microsoft Unified Support erbjudande, enligt beskrivningen i följande avsnitt.
- En Azure DevOps-organisation.
- Behörighet att installera tillägg i Azure DevOps-organisationen.
- Källkod som kan synkroniseras till en Azure-värdbaserad Azure DevOps-pipeline.

## <a name="onboarding-the-microsoft-security-code-analysis-extension"></a>Onboarding The Microsoft Security code Analysis extension

### <a name="interested-in-purchasing-the-microsoft-security-code-analysis-extension"></a>Är du intresse rad av att köpa Microsoft Security code Analysis-tillägget?

Om du har något av följande Support erbjudanden kontaktar du din tekniska konto ansvarig för att köpa eller byta ut befintliga timmar för att få åtkomst till tillägget:

- Enhetlig support avancerad nivå
- Enhetlig support prestanda nivå
- Premier Support för utvecklare
- Premier Support för partner
- Premier Support för företag

Om du inte har något av ovanstående support avtal kan du köpa tillägget från en av våra partner.

**Nästa steg:**

Om du uppfyller ovanstående krav kontaktar du en partner i listan nedan för att köpa tillägget Microsoft Security code analysis. Annars kontaktar du [Microsoft Security code Analysis support](mailto:mscahelp@microsoft.com?Subject=Microsoft%20Security%20Code%20Analysis%20Support%20Request).

>**Partner**

- Zoner – kontakt information: cloudsupport@zones.com
- Wortell – kontakt information: info@wortell.nl
- Logisk kontakt – kontakt uppgifter: logicalisleads@us.logicalis.com

### <a name="become-a-partner"></a>Bli en partner

Microsoft Security code Analysis-teamet kommer att titta på partners med en Premier Support för partner avtal. Partner hjälper Azure DevOps-kunder att utveckla säkrare genom att sälja tillägget till kunder som vill köpa det, men har inte något Enterprise support-avtal med Microsoft. Intresserade partner kan registrera sig [här](http://www.microsoftpartnersupport.com/msrd/opin).

## <a name="installing-the-microsoft-security-code-analysis-extension"></a>Installera tillägget Microsoft Security code Analysis

1. När tillägget har delats med din Azure DevOps-organisation går du till din Azure DevOps-organisations sida. En exempel-URL för en sådan sida är `https://dev.azure.com/contoso` .
1. Välj shopping bag-ikonen i det övre högra hörnet bredvid ditt namn och välj sedan **Hantera tillägg**.
1. Välj **delad**.
1. Välj tillägget Microsoft säkerhets kod analys och välj **Installera**.
1. I list rutan väljer du Azure DevOps-organisationen för att installera tillägget på.
1. Välj **installera**. När installationen är klar kan du börja använda tillägget.

>[!NOTE]
> Även om du inte har åtkomst för att installera tillägget fortsätter du med installations stegen. Du kan begära åtkomst från din Azure DevOps-organisations administratör under installations processen.

När du har installerat tillägget visas de säkra utvecklings uppgifterna och de är tillgängliga för att läggas till i dina Azure-pipeliner.

## <a name="adding-specific-build-tasks-to-your-azure-devops-pipeline"></a>Lägga till specifika bygg uppgifter till din Azure DevOps-pipeline

1. Öppna ditt team projekt från din Azure DevOps-organisation.
1. Välj **pipeline**-  >  **versioner**.
1. Välj den pipeline som du vill lägga till tillägg för build-uppgifter för:
   - Ny pipeline: Välj **ny** och följ stegen som beskrivs för att skapa en ny pipeline.
   - Redigera pipeline: Välj en befintlig pipeline och välj sedan **Redigera** för att börja redigera pipelinen.
1. Välj **+** och gå till fönstret **Lägg till aktiviteter** .
1. Leta upp den build-uppgift som du vill lägga till från antingen listan eller med hjälp av sökrutan. Välj **Lägg till**.
1. Ange de parametrar som krävs för uppgiften.
1. Köa en ny version.
   >[!NOTE]
   >Fil-och mappsökvägar är relativa i förhållande till käll lagrings platsens rot. Om du anger utdatafiler och mappar som parametrar ersätts de med den gemensamma plats som vi har definierat på Build-agenten.

> [!TIP]
>
> - Om du vill köra en analys efter din version kan du placera Microsoft Security code Analysis build-uppgifter efter steget publicera build-artefakter i din version. På så sätt kan din build slutföra och publicera resultat innan du kör statiska analys verktyg.
> - Välj alltid **Fortsätt vid fel** för bygg uppgifter med säker utveckling. Även om ett verktyg Miss lyckas kan de andra köra. Det finns inga beroenden mellan verktyg.
> - Microsoft Security code Analysis build-uppgifter Miss lyckas bara om ett verktyg inte kan köras utan problem. Men de lyckas även om ett verktyg identifierar problem i koden. Genom att använda åtgärden för att bygga efter analyser kan du konfigurera din version så att den inte fungerar när ett verktyg identifierar problem i koden.
> - Vissa Azure DevOps build-uppgifter stöds inte när de körs via en versions pipeline. Mer specifikt stöder Azure DevOps inte uppgifter som publicerar artefakter inifrån en versions pipeline.
> - En lista över fördefinierade variabler i Azure DevOps team build som du kan ange som parametrar finns i [Azure DevOps build-variabler](/azure/devops/pipelines/build/variables?tabs=batch).

## <a name="next-steps"></a>Nästa steg

Mer information om hur du konfigurerar build-aktiviteter finns i vår [konfigurations guide](security-code-analysis-customize.md) eller i [konfigurations guiden för yaml](yaml-configuration.md).

Om du har fler frågor om tillägget och de verktyg som erbjuds kan du kolla in vår [vanliga frågor och svar](security-code-analysis-faq.md).