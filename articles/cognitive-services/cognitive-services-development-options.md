---
title: Alternativ för Azure Cognitive Services-utveckling
description: 'Lär dig hur du använder Azure Cognitive Services med olika alternativ för utveckling och distribution, till exempel klient bibliotek, REST-API: er, Logic Apps, energi automatisering, Azure Functions, Azure App Service, Azure Databricks och många fler.'
services: cognitive-services
manager: nitinme
author: erhopf
ms.author: erhopf
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 10/22/2020
ms.openlocfilehash: 975f7eec31839aefcb1782f573d0210df29a4d00
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "98873577"
---
# <a name="cognitive-services-development-options"></a>Utvecklingsalternativ för Cognitive Services

Det här dokumentet innehåller en översikt över utvecklings-och distributions alternativ som hjälper dig att komma igång med Azure Cognitive Services.

Azure Cognitive Services är molnbaserade AI-tjänster som gör det möjligt för utvecklare att bygga in information i sina program och produkter utan att känna till maskin inlärning. Med Cognitive Services har du till gång till AI-funktioner eller modeller som skapats, tränas och uppdateras av Microsoft-redo att användas i dina program. I många fall har du också möjlighet att anpassa modellerna efter dina affärs behov. 

Cognitive Services är indelade i fyra kategorier: beslut, språk, tal och vision. Normalt skulle du ha åtkomst till dessa tjänster via REST-API: er, klient bibliotek och anpassade verktyg (t. ex. kommando rads gränssnitt) som tillhandahålls av Microsoft. Detta är dock bara en sökväg till lyckades. Via Azure har du också till gång till flera utvecklings alternativ, till exempel:

* Automatiserings-och integrations verktyg som Logic Apps och Energis par automatisering.
* Distributions alternativ som Azure Functions och App Service. 
* Cognitive Services Docker-behållare för säker åtkomst.
* Verktyg som Apache Spark, Azure Databricks, Azure Synapse Analytics och Azure Kubernetes-tjänsten för stora data scenarier. 

Innan vi går vidare är det viktigt att veta att Cognitive Services främst används för två olika aktiviteter. Utifrån den uppgift som du vill utföra har du olika utvecklings-och distributions alternativ att välja mellan. 

* [Utvecklings alternativ för förutsägelse och analys](#development-options-for-prediction-and-analysis)
* [Verktyg för att anpassa och konfigurera modeller](#tools-to-customize-and-configure-models)

## <a name="development-options-for-prediction-and-analysis"></a>Utvecklings alternativ för förutsägelse och analys 

De verktyg du ska använda för att anpassa och konfigurera modeller skiljer sig från de som du använder för att anropa Cognitive Services. Med de flesta Cognitive Services kan du skicka data och ta emot insikter utan att behöva göra några ändringar. Exempel: 

* Du kan skicka en avbildning till Visuellt innehåll tjänsten för att identifiera ord och fraser eller räkna antalet personer i ramen
* Du kan skicka en ljudfil till tal tjänsten och få avskrifter och översätta talet till text på samma gång
* Du kan skicka en PDF-fil till formulär igenkännings tjänsten och identifiera tabeller, celler och text i de cellerna och du får ett JSON-utdata med koordinater och information

Azure erbjuder ett brett utbud av verktyg som är utformade för olika typer av användare, många som kan användas med Cognitive Services. Designer-drivna verktyg är det enklaste sättet att använda och är snabbt att konfigurera och automatisera, men kan ha begränsningar när det kommer till anpassning. Våra REST-API: er och klient bibliotek ger användare större kontroll och flexibilitet, men kräver mer ansträngning, tid och expertis för att skapa en lösning. Om du använder REST-API: er och klient bibliotek, finns det en förväntan att du är van att arbeta med moderna programmeringsspråk som C#, Java, python, java script eller något annat populärt programmeringsspråk. 

Låt oss ta en titt på de olika sätt som du kan arbeta med Cognitive Services.

### <a name="client-libraries-and-rest-apis"></a>Klientbibliotek och REST-API:er

Cognitive Services klient bibliotek och REST-API: er ger dig direkt åtkomst till din tjänst. Dessa verktyg ger programmerings åtkomst till Cognitive Services, deras bas linje modeller och i många fall kan du anpassa dina modeller och lösningar program mässigt. 

* **Mål användare**: utvecklare och data forskare
* **Förmåner**: ger största möjliga flexibilitet för att anropa tjänsterna från alla språk och miljöer. 
* **Gränssnitt**: saknas-kod
* **Prenumeration (er)**: Azure-konto + Cognitive Services resurser

Om du vill veta mer om tillgängliga klient bibliotek och REST API: er kan du använda vår [Cognitive Services översikt](index.yml) för att välja och komma igång med en av våra snabb starter för syn, beslut, språk och tal.

### <a name="cognitive-services-for-big-data"></a>Cognitive Services för stordata

Med Cognitive Services för Big data kan du bädda in kontinuerligt och intelligenta modeller direkt i Apache Spark- &trade; och SQL-beräkningar. Dessa verktyg utvecklar utvecklare från lågnivå information på låg nivå, så att de kan fokusera på att skapa smarta, distribuerade program. Cognitive Services för Big data stöder följande plattformar och anslutningar: Azure Databricks, Azure Synapse, Azure Kubernetes service och data anslutningar.

* **Mål användare**: data forskare och data tekniker
* **Fördelar**: Azure-Cognitive Services för Big data gör det möjligt för användarna att channela terabyte data genom att Cognitive Services använda Apache Spark &trade; . Det är enkelt att skapa storskaliga intelligenta program med alla data lager.
* **Gränssnitt**: saknas-kod
* **Prenumeration (er)**: Azure-konto + Cognitive Services resurser

Om du vill lära dig mer om Big data för Cognitive Services är det en bra idé att börja med [översikten](./big-data/cognitive-services-for-big-data.md). Om du är redo att börja skapa kan du prova våra [python](./big-data/samples-python.md) -eller [Scala](./big-data/samples-scala.md) -exempel.

### <a name="azure-functions-and-azure-service-web-jobs"></a>Azure Functions och Azure service Web Jobs

[Azure Functions](../azure-functions/index.yml) [-och Azure App Service-webbjobben](../app-service/index.yml) ger både kod-och integrerings tjänster utformade för utvecklare och bygger på [Azure App tjänster](../app-service/index.yml). Dessa produkter tillhandahåller en server lös infrastruktur för att skriva kod. I den koden kan du ringa till våra tjänster med hjälp av våra klient bibliotek och REST-API: er. 

* **Mål användare**: utvecklare och data forskare
* **Fördelar**: Server lös beräknings tjänst som gör att du kan köra händelse utlöst kod. 
* **UI**: Ja
* **Prenumeration (er)**: Azure-konto + Cognitive Services resurs + Azure Functions prenumeration

### <a name="azure-logic-apps"></a>Azure Logic Apps 

[Azure Logic Apps](../logic-apps/index.yml) dela samma arbets flödes designer och kopplingar som Power automatisering men ger mer avancerad och kontroll, inklusive integreringar med Visual Studio och DevOps. Med automatisk energi förbrukning kan du enkelt integrera med dina kognitiva tjänst resurser via tjänstspecifika anslutningar som tillhandahåller en proxy eller en omslutning runt API: erna. Detta är samma kopplingar som de som är tillgängliga i energi spar läge. 

* **Mål användare**: utvecklare, integrerare, IT-proffs, DevOps
* **Fördelar**: design modell-First (deklarativ) utvecklings modell som tillhandahåller avancerade alternativ och integrering i en lösning med låg kod
* **UI**: Ja
* **Prenumeration (er)**: Azure-konto + Cognitive Services resurs + Logic Apps distribution

### <a name="power-automate"></a>Power Automate 

Energis par är en tjänst i den [energi plattform](/power-platform/) som hjälper dig att skapa automatiserade arbets flöden mellan appar och tjänster utan att skriva kod. Vi erbjuder flera kopplingar för att göra det enkelt att interagera med din Cognitive Services-resurs i en automatiserad lösning. Automatisk energi förbrukning bygger på Logic Apps. 

* **Mål användare**: företags användare (analytiker) och SharePoint-administratörer
* **Fördelar**: automatisera återkommande manuella uppgifter genom att bara spela in mus klickningar, tangenttryckningar och kopiera klistra in steg från Skriv bordet!
* **Gränssnitts verktyg**: Ja – endast användar gränssnitt
* **Prenumeration (er)**: Azure-konto + Cognitive Services resurs + Power automatiserings prenumeration + Office 365-prenumeration

### <a name="ai-builder"></a>AI Builder 

[AI Builder](/ai-builder/overview) är en Microsoft Power Platform-funktion som du kan använda för att förbättra affärs prestanda genom att automatisera processer och förutsäga resultat. AI-verktyget ger dig kraften hos AI för dina lösningar genom en punkt-och-klickning-upplevelse. Många kognitiva tjänster, till exempel formulär igenkänning, Textanalys och Visuellt innehåll har integrerats direkt här och du behöver inte skapa dina egna Cognitive Services. 

* **Mål användare**: företags användare (analytiker) och SharePoint-administratörer
* **Fördelar**: en nyckel färdig lösning som ger dig kraften hos AI genom en punkt-och klicknings upplevelse. Inga kodnings-eller data vetenskaps kunskaper krävs.
* **Gränssnitts verktyg**: Ja – endast användar gränssnitt
* **Prenumeration (er)**: AI-verktyg

### <a name="continuous-integration-and-deployment"></a>Kontinuerlig integrering och distribution

Du kan använda Azure-DevOps och GitHub-åtgärder för att hantera dina distributioner. I [avsnittet nedan](#continuous-integration-and-delivery-with-devops-and-github-actions) innehåller vi två exempel på CI/CD-integreringar för att träna och distribuera anpassade modeller för tal-och Luis-tjänsten (Language Understanding). 

* **Mål användare**: utvecklare, data forskare och data tekniker
* **Fördelar**: gör att du kontinuerligt kan justera, uppdatera och distribuera program och modeller program mässigt. Det finns avsevärda fördelar när du regelbundet använder dina data för att förbättra och uppdatera modeller för tal, syn, språk och beslut. 
* **Gränssnitts verktyg**: ej tillämpligt-endast kod 
* **Prenumeration (er)**: Azure-konto + Cognitive Services resurs + GitHub-konto

## <a name="tools-to-customize-and-configure-models"></a>Verktyg för att anpassa och konfigurera modeller

När du går vidare med att skapa ett program eller ett arbets flöde med Cognitive Services kan du se att du behöver anpassa modellen för att uppnå önskad prestanda. Många av våra tjänster gör att du kan bygga ovanpå de färdiga modeller som uppfyller dina specifika affärs behov. För alla våra anpassningsbara tjänster ger vi både en GRÄNSSNITTs driven upplevelse för att flytta genom processen och API: er för kod driven utbildning. Exempel:

* Du vill träna en Custom Speech modell att korrekt identifiera medicinska villkor med en ord Fels frekvens (WER) under 3%
* Du vill bygga en bild klassificerare med Custom Vision som kan visa skillnaden mellan Barr träd och lövträde träd
* Du vill bygga en anpassad röst för neurala med dina personliga röst uppgifter för en förbättrad automatiserad kund upplevelse

De verktyg som du ska använda för att träna och konfigurera modeller skiljer sig från de som du använder för att anropa Cognitive Services. I många fall kan Cognitive Services som stöder anpassning tillhandahålla portaler och GRÄNSSNITTs verktyg som är utformade för att hjälpa dig att träna, utvärdera och distribuera modeller. Vi tar snabbt en titt på några alternativ:<br><br>

| Grundpelare | Tjänst | Anpassnings gränssnitt | Snabbstart |
|--------|---------|------------------|------------|
| Visuellt innehåll | Custom Vision | https://www.customvision.ai/ | [Snabbstart](./custom-vision-service/quickstarts/image-classification.md?pivots=programming-language-csharp) | 
| Visuellt innehåll | Formigenkänning | [Exempel på etikett verktyg](https://fott-preview.azurewebsites.net/) | [Snabbstart](./form-recognizer/quickstarts/label-tool.md?tabs=v2-0) |
| Beslut | Content Moderator | https://contentmoderator.cognitive.microsoft.com/dashboard | [Snabbstart](./content-moderator/review-tool-user-guide/human-in-the-loop.md) |
| Beslut | Metrics Advisor | https://metricsadvisor.azurewebsites.net/  | [Snabbstart](./metrics-advisor/quickstarts/web-portal.md) |
| Beslut | Personanpassning | UI är tillgängligt i Azure Portal under din personanpassa resurs. | [Snabbstart](./personalizer/quickstart-personalizer-sdk.md) |
| Språk | Language Understanding (LUIS) | https://www.luis.ai/ | |
| Språk | QnA Maker | https://www.qnamaker.ai/ | [Snabbstart](./qnamaker/quickstarts/create-publish-knowledge-base.md) |
| Språk | Translator/anpassad översättare | https://portal.customtranslator.azure.ai/ | [Snabbstart](./translator/custom-translator/quickstart-build-deploy-custom-model.md) |
| Speech | Anpassade kommandon | https://speech.microsoft.com/ | [Snabbstart](./speech-service/custom-commands.md) |
| Speech | Custom Speech | https://speech.microsoft.com/ | [Snabbstart](./speech-service/custom-speech-overview.md) |
| Speech | Anpassad röst | https://speech.microsoft.com/ | [Snabbstart](./speech-service/how-to-custom-voice.md) |  

### <a name="continuous-integration-and-delivery-with-devops-and-github-actions"></a>Kontinuerlig integrering och leverans med DevOps-och GitHub-åtgärder

Language Understanding och tal tjänsten erbjuder kontinuerlig integrering och lösningar för kontinuerlig distribution som drivs av Azure-DevOps och GitHub-åtgärder. Dessa verktyg används för automatisk utbildning, testning och versions hantering av anpassade modeller. 

* [CI/CD för Custom Speech](./speech-service/how-to-custom-speech-continuous-integration-continuous-deployment.md)
* [CI/CD för LUIS](./luis/luis-concept-devops-automation.md)

## <a name="on-prem-containers"></a>Lokal behållare 

Många av de Cognitive Services kan distribueras i behållare för lokal åtkomst och användning. Med hjälp av de här behållarna får du flexibiliteten att ta Cognitive Services närmare dina data för efterlevnad, säkerhet eller andra drift orsaker. En fullständig lista över Cognitive Services behållare finns [i lokal behållare för Cognitive Services](./cognitive-services-container-support.md).

## <a name="next-steps"></a>Nästa steg
<!--
* Learn more about low code development options for Cognitive Services -->
* [Skapa en Cognitive Services resurs och börja bygga](./cognitive-services-apis-create-account.md?tabs=multiservice%252clinux)
