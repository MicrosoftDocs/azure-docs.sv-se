---
title: Planera din app-QnA Maker
description: Lär dig hur du planerar din QnA Maker-app. Förstå hur QnA Maker fungerar och samverkar med andra Azure-tjänster och vissa kunskaps bas begrepp.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/09/2020
ms.openlocfilehash: e523b35afca33213a40060819a1293e94d413b00
ms.sourcegitcommit: 8c8c71a38b6ab2e8622698d4df60cb8a77aa9685
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/01/2021
ms.locfileid: "99222873"
---
# <a name="plan-your-qna-maker-app"></a>Planera QnA Maker-appen

Om du vill planera din QnA Maker-app måste du förstå hur QnA Maker fungerar och samverkar med andra Azure-tjänster. Du bör också ha en solid grepp av kunskaps bas koncept.

## <a name="azure-resources"></a>Azure-resurser

Varje [Azure-resurs](azure-resources.md#resource-purposes) som skapats med QNA Maker har ett specifikt syfte. Varje resurs har sitt eget syfte, gränser och [pris nivå](azure-resources.md#pricing-tier-considerations). Det är viktigt att förstå funktionerna i dessa resurser så att du kan använda dessa kunskaper i planerings processen.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil utgåva)](#tab/v1)

| Resurs | Syfte |
|--|--|
| [QNA Maker](azure-resources.md#qna-maker-resource) resurs | Redigering och fråga förutsägelse |
| [Kognitiv sökning](azure-resources.md#cognitive-search-resource) resurs | Data lagring och sökning |
| Resurs för [resurs-och app-plan för App Service](azure-resources.md#app-service-and-app-service-plan) | Slutpunkt för frågeförutsägelse |
| [Application Insights](azure-resources.md#application-insights) resurs | Telemetri för fråga förutsägelse |

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker hanterad (för hands version)](#tab/v2)

| Resurs | Syfte |
|--|--|
| [QNA Maker](azure-resources.md#qna-maker-resource) resurs | Redigering, fråga förutsägelse slut punkt och telemetri|
| [Kognitiv sökning](azure-resources.md#cognitive-search-resource) resurs | Data lagring och sökning |

---
### <a name="resource-planning"></a>Resurs planering

Den kostnads fria nivån, `F0` , för varje resurs, fungerar och kan ge både redigering och fråga förutsägelse. Du kan använda den här nivån för att lära dig att redigera och fråga förutsägelse. När du flyttar till ett produktions-eller Live-scenario kan du utvärdera ditt resurs val igen.

#### <a name="qna-maker-resource"></a>QnA Maker resurs

En enskild QnA Maker-resurs kan vara värd för fler än en kunskaps bas. Antalet kunskaps baser bestäms av den Kognitiv sökning pris nivåns antal index som stöds. Lär dig mer om [förhållandet mellan index och kunskaps baser](azure-resources.md#index-usage).

#### <a name="knowledge-base-size-and-throughput"></a>Kunskaps bas storlek och data flöde

När du skapar en riktig app bör du planera tillräckligt med resurser för storleken på din kunskaps bas och för dina förväntade förfrågningar om fråga förutsägelse.

En kunskaps bas storlek styrs av:
* Gränser för [kognitiv sökning resurs](../../../search/search-limits-quotas-capacity.md) pris nivå
* [QnA Maker gränser](../limits.md)

Frågan förutsägelse förfrågan för kunskaps basen styrs av webbappens plan och webbapp. Se de [rekommenderade inställningarna](azure-resources.md#recommended-settings) för att planera pris nivån.

### <a name="resource-sharing"></a>Resursdelning

Om du redan har några av de här resurserna som används kan du överväga att dela resurser. Se vilka resurser som [kan delas](azure-resources.md#share-services-with-qna-maker) med förståelsen av resurs delningen är ett avancerat scenario.

Alla kunskaps baser som skapats i samma QnA Maker resurs delar samma slut punkt för **test** frågans förutsägelse.

### <a name="understand-the-impact-of-resource-selection"></a>Förstå effekten av resurs val

Med rätt resurs val menas din kunskaps bas svar på frågan förutsägelse.

Om din kunskaps bas inte fungerar korrekt är det vanligt vis ett problem med felaktig resurs hantering.

Felaktig resurs val kräver undersökning för att avgöra vilken [resurs som behöver ändras](azure-resources.md#when-to-change-a-pricing-tier).

## <a name="knowledge-bases"></a>Kunskaps banker

En kunskaps bas är direkt knuten till sin QnA Maker-resurs. Den innehåller de frågor och svar (QnA-par) som används för att besvara förfrågningar om fråga förutsägelse.

### <a name="language-considerations"></a>Språk överväganden

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil utgåva)](#tab/v1)

Den första kunskaps basen som skapas på QnA Maker-resursen anger språket för resursen. Du kan bara ha ett språk för en QnA Maker-resurs.

Du kan strukturera QnA Maker resurser efter språk, eller så kan du använda [Translator](../../translator/translator-info-overview.md) för att ändra en fråga från ett annat språk till kunskaps bas språket innan du skickar frågan till slut punkten för frågans förutsägelse.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker hanterad (för hands version)](#tab/v2)

Du kan nu ha kunskaps banker på olika språk inom samma QnA Maker-resurs. När du skapar den första kunskaps basen kan du välja om du vill använda resursen för kunskaps baser på ett enda språk eller flera språk.

![QnA Maker hanterad (för hands version) flerspråkigt kunskaps bas val](../media/concept-plan-your-knowledge-base/qnamaker-v2-select-multilanguage-knowledge-base.png)

> [!NOTE]
> Om du aktiverar språk inställningar per kunskaps bas kan du inte skapa så många kunskaps baser i din QnA Maker-resurs. [Mer information om språk inställnings begränsningar](./azure-resources.md).

---

### <a name="ingest-data-sources"></a>Mata in data källor

Du kan använda någon av följande inmatade [data källor](../Concepts/data-sources-and-content.md) för att skapa en kunskaps bas:

* Offentlig URL
* Privat SharePoint-URL
* Fil

Vid inmatnings processen konverteras [innehålls typer som stöds](../reference-document-format-guidelines.md) till markdown. All ytterligare redigering av *svaret* görs med markdown. När du har skapat en kunskaps bas kan du redigera [QNA-par](question-answer-set.md) i QNA Maker Portal med RTF- [redigering](../how-to/edit-knowledge-base.md#rich-text-editing-for-answer).

### <a name="data-format-considerations"></a>Överväganden för data format

Eftersom det slutliga formatet för ett QnA-par är markdown är det viktigt att förstå [markdown-supporten](../reference-markdown-format.md).

Länkade bilder måste vara tillgängliga från en offentlig URL som ska visas i test fönstret i QnA Maker-portalen eller i ett klient program. QnA Maker tillhandahåller inte autentisering för innehåll, inklusive bilder.

### <a name="bot-personality"></a>Bot-personlighet

Lägg till en bot-personlighet till din kunskaps bas med en [CHI2TEST-chatt](../how-to/chit-chat-knowledge-base.md). Den här informen går igenom med svar som finns i en viss konversations ton, till exempel *professionella* och *egna*. Den här CHI2TEST tillhandahålls som en konversations uppsättning, som du har total kontroll för att lägga till, redigera och ta bort.

En bot-personlighet rekommenderas om din robot ansluter till din kunskaps bas. Du kan välja att använda CHI2TEST i kunskaps basen även om du också ansluter till andra tjänster, men du bör se hur bot tjänsten interagerar för att veta om det är rätt arkitektur design för din användning.

### <a name="conversation-flow-with-a-knowledge-base"></a>Konversations flöde med en kunskaps bas

Konversations flödet börjar vanligt vis med en hälsning från en användare, till exempel `Hi` eller `Hello` . Din kunskaps bas kan svara med ett allmänt svar, t. ex `Hi, how can I help you` ., och det kan också innehålla en urval av uppföljnings anvisningar för att fortsätta konversationen.

Du bör utforma ditt konversations flöde med en loop i åtanke så att en användare vet hur du använder din robot och inte överges av roboten i konversationen. [Uppföljnings instruktioner](../how-to/multiturn-conversation.md) ger länkar mellan QNA-par som tillåter konversations flödet.

### <a name="authoring-with-collaborators"></a>Redigera med medarbetare

Medarbetare kan vara andra utvecklare som delar den fullständiga utvecklings stacken i kunskaps bas programmet eller kan vara begränsade till att bara redigera kunskaps basen.

Redigering av kunskaps bas har stöd för flera [rollbaserade åtkomst behörigheter](../reference-role-based-access-control.md) som du använder i Azure Portal för att begränsa omfattningen för en medarbetares förmågor.

## <a name="integration-with-client-applications"></a>Integrering med klient program

Integrering med [klient program](../index.yml) uppnås genom att skicka en fråga till körnings slut punkten för förutsägelse. En fråga skickas till din speciella kunskaps bas med en SDK-eller REST-baserad begäran till din QnA Makers webb programs slut punkt.

Om du vill autentisera en klientbegäran korrekt måste klient programmet skicka rätt autentiseringsuppgifter och ID för kunskaps bas. Om du använder en Azure Bot Service konfigurerar du inställningarna som en del av robot konfigurationen i Azure Portal.

### <a name="conversation-flow-in-a-client-application"></a>Konversations flöde i ett klient program

Konversations flödet i ett [klient program](../index.yml), t. ex. en Azure-robot, kan kräva funktioner innan och efter att du har interagerat i kunskaps basen.

Stöder ditt klient program konversations flödet, antingen genom att tillhandahålla alternativa metoder för att hantera uppföljnings-prompter eller inklusive CHI2TEST? I så fall kan du utforma dessa tidiga och se till att klient program frågan hanteras korrekt av en annan tjänst eller när den skickas till din kunskaps bas.

### <a name="dispatch-between-qna-maker-and-language-understanding-luis"></a>Sändning mellan QnA Maker och Language Understanding (LUIS)

Ett klient program kan tillhandahålla flera funktioner, bara en som besvaras av en kunskaps bas. Andra funktioner behöver inte förstå konversations texten och extraherings innebörden.

En vanlig klient program arkitektur är att använda både QnA Maker och [language Understanding (Luis)](../../LUIS/what-is-luis.md) tillsammans. LUIS tillhandahåller text klassificering och extrahering för alla frågor, inklusive andra tjänster. QnA Maker ger svar från din kunskaps bas.

I ett sådant scenario med [delad arkitektur](../choose-natural-language-processing-service.md) utförs [sändningen](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Dispatch) mellan de två tjänsterna av verktyget Dispatch från bot Framework.

### <a name="active-learning-from-a-client-application"></a>Aktiv inlärning från ett klient program

QnA Maker använder _Active Learning_ för att förbättra din kunskaps bas genom att föreslå alternativa frågor till ett svar. Klient programmet ansvarar för en del av den här [aktiva inlärningen](active-learning-suggestions.md). Via konversationer kan klient programmet avgöra om kunskaps basen returnerade ett svar som inte är användbart för användaren, och det kan fastställa ett bättre svar. Klient programmet måste [skicka tillbaka informationen till kunskaps basen](active-learning-suggestions.md#how-you-give-explicit-feedback-with-the-train-api) för att förbättra förutsägelse kvaliteten.

### <a name="providing-a-default-answer"></a>Ange ett standard svar

Om din kunskaps bas inte hittar något svar, returneras _standard svaret_. Detta svar kan konfigureras på sidan **Inställningar** i QNA Maker portalen eller i [API: erna](/rest/api/cognitiveservices/qnamaker/knowledgebase/update#request-body).

Det här standardsvaret skiljer sig från standard svaret för Azure bot. Du konfigurerar standard svaret för din Azure-robot i Azure Portal som en del av konfigurations inställningarna. Den returneras när Poäng tröskelvärdet inte är uppfyllt.

## <a name="prediction"></a>Förutsägelse

Förutsägelsen är svaret från din kunskaps bas och innehåller mer information än bara svaret. Använd [GenerateAnswer-API: et](query-knowledge-base.md)för att få svar på en fråga som förutsäger.

### <a name="prediction-score-fluctuations"></a>Förutsägelse Poäng

Poängen kan ändras baserat på flera faktorer:

* Antal svar som du begärt som svar på [GenerateAnswer](query-knowledge-base.md) med `top` egenskapen
* Utbud av tillgängliga alternativa frågor
* Filtrering för metadata
* Fråga som skickats till `test` eller `production` kunskaps bas

Det finns en [svars rangordning i två faser](query-knowledge-base.md#how-qna-maker-processes-a-user-query-to-select-the-best-answer):
- Kognitiv sökning-första rang. Ange antalet _svar_ som är tillräckligt högt för att de bästa svaren returneras av kognitiv sökning och sedan skickas till QNA Maker rangen.
- QnA Maker-sekund rang. Använd funktionalisering och Machine Learning för att fastställa bästa möjliga svar.

### <a name="service-updates"></a>Tjänstuppdateringar

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil utgåva)](#tab/v1)

Använd de [senaste körnings uppdateringarna](../how-to/set-up-qnamaker-service-azure.md#get-the-latest-runtime-updates) för att hantera tjänst uppdateringar automatiskt.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker hanterad (för hands version)](#tab/v2)

I QnA Maker hanterad (för hands version) hanteras körningen av själva QnA Maker-tjänsten. Därför är tjänst uppdateringar inte tillämpliga.

---

### <a name="scaling-throughput-and-resiliency"></a>Skalning, data flöde och återhämtning

Skalning, data flöde och återhämtning bestäms av Azure- [resurser](../how-to/set-up-qnamaker-service-azure.md), deras pris nivåer och alla omgivande arkitektur, till exempel [Traffic Manager](../how-to/set-up-qnamaker-service-azure.md#business-continuity-with-traffic-manager).

### <a name="analytics-with-application-insights"></a>Analys med Application Insights

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil utgåva)](#tab/v1)

Alla frågor till kunskaps basen lagras i Application Insights. Använd våra [vanligaste frågor](../how-to/get-analytics-knowledge-base.md) för att förstå dina mått.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker hanterad (för hands version)](#tab/v2)

I den hanterade distributionen erbjuds telemetri via [tjänsten Azure Monitor](../../../azure-monitor/index.yml). Använd våra [vanligaste frågor](../how-to/get-analytics-knowledge-base.md) för att förstå dina mått.


---

## <a name="development-lifecycle"></a>Utvecklingscykel

[Utvecklings livs cykeln](development-lifecycle-knowledge-base.md) för en kunskaps bas pågår: redigera, testa och publicera din kunskaps bas.

### <a name="knowledge-base-development-of-qna-maker-pairs"></a>Kunskaps bas utveckling av QnA Maker par

Dina [QNA-par](question-answer-set.md) bör utformas och utvecklas baserat på användningen av klient programmet.

Varje par kan innehålla:
* Metadata – filtrerat vid frågor så att du kan tagga dina QnA-par med ytterligare information om källa, innehåll, format och syfte för dina data.
* Uppföljnings frågor – hjälper dig att fastställa en sökväg genom kunskaps basen så att användaren kommer till rätt svar.
* Alternativa frågor – viktigt för att söka efter svar från olika former av frågan. [Aktiva utbildnings förslag](active-learning-suggestions.md) gör alternativa frågor.

### <a name="devops-development"></a>DevOps-utveckling

Att utveckla en kunskaps bas som ska infogas i en DevOps-pipeline kräver att kunskaps basen isoleras under [batch-testning](../index.yml).

En kunskaps bas delar Kognitiv sökning indexet med alla andra kunskaps baser på den QnA Maker resursen. När kunskaps basen isoleras av en partition kan delning av index orsaka en skillnad i poängen jämfört med den publicerade kunskaps basen.

För att få _samma resultat_ i `test` och `production` kunskaps baser, isolera en QNA Maker resurs till en enda kunskaps bas. I den här arkitekturen behöver resursen bara leva så länge som det isolerade batch-testet.

## <a name="next-steps"></a>Nästa steg

* [Azure-resurser](../how-to/set-up-qnamaker-service-azure.md)
* [Frågor och svar-par](question-answer-set.md)
