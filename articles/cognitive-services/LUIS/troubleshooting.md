---
title: Vanliga frågor och svar – LUIS
description: Den här artikeln innehåller svar på vanliga frågor om Language Understanding (LUIS).
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: troubleshooting
ms.date: 04/13/2021
ms.openlocfilehash: 97b7c02a418a87a0700414e19bc939bda899d073
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107503838"
---
# <a name="language-understanding-frequently-asked-questions-faq"></a>Vanliga frågor och svar om Language Understanding

Den här artikeln innehåller svar på vanliga frågor om Language Understanding (LUIS).

## <a name="whats-new"></a>Nyheter

[Läs mer](whats-new.md) om vad som är nytt i Language Understanding (LUIS).

<a name="luis-authoring"></a>

## <a name="authoring"></a>Redigering

### <a name="what-are-the-luis-best-practices"></a>Vilka är LUIS-metodtipsen?
Börja med [redigeringscykeln och](luis-concept-app-iteration.md)läs sedan [metodtipsen.](luis-concept-best-practices.md)

### <a name="what-is-the-best-way-to-start-building-my-app-in-luis"></a>Vad är det bästa sättet att börja skapa min app i LUIS?

Det bästa sättet att skapa din app är genom en [inkrementell process](luis-concept-app-iteration.md).

### <a name="what-is-a-good-practice-to-model-the-intents-of-my-app-should-i-create-more-specific-or-more-generic-intents"></a>Vad är en bra idé att modellera avsikten med min app? Bör jag skapa mer specifika eller mer allmänna avsikter?

Välj avsikter som inte är så allmänna som överlappande, men inte så specifika att det gör det svårt för LUIS att skilja mellan liknande avsikter. Att skapa diskriminativa specifika avsikter är en av de bästa metoderna för LUIS-modellering.

### <a name="is-it-important-to-train-the-none-intent"></a>Är det viktigt att träna avsikten Ingen?

Ja, det är bra att träna avsikten **None** (Ingen) med fler yttranden när du lägger till fler etiketter i andra avsikter. Ett bra förhållande är 1 eller 2 etiketter som läggs till i **Ingen för** varje 10 etiketter som läggs till i en avsikt. Det här förhållandet ökar den diskriminativa kraften i LUIS.

### <a name="how-can-i-correct-spelling-mistakes-in-utterances"></a>Hur kan jag korrigera stavfel i yttranden?

Se [självstudien Stavningskontroll i Bing API V7.](luis-tutorial-bing-spellcheck.md) LUIS tillämpar begränsningar som införts av Stavningskontroll i Bing API V7.

### <a name="how-do-i-edit-my-luis-app-programmatically"></a>Hur gör jag för att redigera min LUIS-app programmatiskt?
Om du vill redigera LUIS-appen programmatiskt använder du [redigerings-API:et](https://go.microsoft.com/fwlink/?linkid=2092087). Se [Anropa LUIS-redigerings-API:et](./get-started-get-model-rest-apis.md) och Skapa en [LUIS-app programmatiskt ](./luis-tutorial-node-import-utterances-csv.md) med Node.jsför exempel på hur du anropar redigerings-API:et. Redigerings-API:et kräver att du [använder en redigeringsnyckel](luis-how-to-azure-subscription.md#azure-resources-for-luis) i stället för en slutpunktsnyckel. Programmatisk redigering tillåter upp till 1 000 000 anrop per månad och fem transaktioner per sekund. Mer information om de nycklar som du använder med LUIS finns i [Hantera nycklar.](./luis-how-to-azure-subscription.md)

### <a name="where-is-the-pattern-feature-that-provided-regular-expression-matching"></a>Var finns funktionen Pattern som tillhandahöll matchning av reguljära uttryck?
Den tidigare **funktionen Pattern** är för närvarande inaktuell och ersätts **[av Patterns](luis-concept-patterns.md)**.

### <a name="how-do-i-use-an-entity-to-pull-out-the-correct-data"></a>Hur gör jag för att du använda en entitet för att hämta rätt data?
Se [entiteter](luis-concept-entity-types.md) och [extrahering av data.](luis-concept-data-extraction.md)

### <a name="should-variations-of-an-example-utterance-include-punctuation"></a>Ska varianter av ett exempelyttrande innehålla skiljetecken?
Använd någon av följande lösningar:
* Ignorera [skiljetecken](luis-reference-application-settings.md#punctuation-normalization)
* Lägg till de olika variationerna som exempelyttranden i avsikten
* Lägg till mönstret för exempelyttrande med [syntaxen för att](luis-concept-patterns.md#pattern-syntax) ignorera skiljetecken.

### <a name="does-luis-currently-support-cortana"></a>Stöder LUIS för närvarande Cortana?

Fördefinierade Cortana-appar blev inaktuella 2017. De stöds inte längre.

### <a name="how-do-i-transfer-ownership-of-a-luis-app"></a>Hur gör jag för att överföra ägarskapet för en LUIS-app?
Om du vill överföra en LUIS-app till en annan Azure-prenumeration exporterar du LUIS-appen och importerar den med ett nytt konto. Uppdatera LUIS-app-ID:t i klientprogrammet som anropar det. Den nya appen kan returnera något annorlunda LUIS-poäng från den ursprungliga appen.

### <a name="a-prebuilt-entity-is-tagged-in-an-example-utterance-instead-of-my-custom-entity-how-do-i-fix-this"></a>En förbyggd entitet taggas i ett exempelyttrande i stället för i min anpassade entitet. Hur gör jag för att åtgärda detta?

I LUIS-portalen kan du märka text för den exakta entitet som du är intresserad av att extrahera. Om LUIS-portalen inte visar rätt entitetsförutsägelse kan du behöva lägga till fler yttranden och märka entiteten i texten eller lägga till en funktion.

### <a name="i-tried-to-import-an-app-or-version-file-but-i-got-an-error-what-happened"></a>Jag försökte importera en app- eller versionsfil men jag fick ett felmeddelande. Vad hände?

Läs mer om [versionsimportfel.](luis-how-to-manage-versions.md#import-errors)

<a name="luis-collaborating"></a>

## <a name="collaborating-and-contributing"></a>Samarbeta och bidra

### <a name="how-do-i-give-collaborators-access-to-luis-with-azure-active-directory-azure-ad-or-azure-role-based-access-control-azure-rbac"></a>Hur gör jag för att ge medarbetare åtkomst till LUIS med Azure Active Directory (Azure AD) eller rollbaserad åtkomstkontroll i Azure (Azure RBAC)?

Se [Azure Active Directory och](luis-how-to-collaborate.md#azure-active-directory-resources)  Azure Active Directory [klientanvändare för att](luis-how-to-collaborate.md#azure-active-directory-tenant-user) lära dig hur du ger medarbetare åtkomst.

<a name="luis-endpoint"></a>

## <a name="endpoint"></a>Slutpunkt

### <a name="i-received-an-http-403-error-status-code-how-do-i-fix-it"></a>Jag fick felstatuskoden HTTP 403. Vad kan jag göra?

Du får felstatuskoderna 403 och 429 när du överskrider transaktionerna per sekund eller transaktioner per månad för din prisnivå. Öka prisnivån eller använd [Language Understanding containrar](luis-container-howto.md).

När du använder alla de kostnadsfria 1 000 slutpunktsfrågorna eller om du överskrider din prisnivås kvot för månatliga transaktioner får du en HTTP 403-felstatuskod.

För att åtgärda det här [](luis-how-to-azure-subscription.md#change-the-pricing-tier) felet måste du antingen ändra prisnivån till en högre nivå eller skapa en ny [resurs](get-started-portal-deploy-app.md#create-the-endpoint-resource) och [tilldela den till din app.](get-started-portal-deploy-app.md#assign-the-resource-key-to-the-luis-app-in-the-luis-portal)

Lösningar på det här felet är:

* I [Azure Portal](https://portal.azure.com)på din Language Understanding resurs på prisnivån **Resource Management ->** ändrar du prisnivån till en högre TPS-nivå. Du behöver inte göra något i Language Understanding portalen om din resurs redan har tilldelats till Language Understanding appen.
*  Om din användning överskrider den högsta prisnivån kan du lägga Language Understanding fler resurser med en lastbalanserare framför sig. Det [Language Understanding med](luis-container-howto.md) Kubernetes eller Docker Compose.

### <a name="i-received-an-http-429-error-status-code-how-do-i-fix-it"></a>Jag har fått en HTTP 429-felstatuskod. Vad kan jag göra?

Du får felstatuskoderna 403 och 429 när du överskrider transaktionerna per sekund eller transaktioner per månad för din prisnivå. Öka prisnivån eller använd [Language Understanding containrar](luis-container-howto.md).

Den här statuskoden returneras när dina transaktioner per sekund överskrider din prisnivå.

Lösningar omfattar:

* Du kan [öka din prisnivå](luis-how-to-azure-subscription.md#change-the-pricing-tier)om du inte är på den högsta nivån.
* Om din användning överskrider den högsta prisnivån kan du lägga Language Understanding fler resurser med en lastbalanserare framför sig. Den [Language Understanding containern](luis-container-howto.md) med Kubernetes eller Docker Compose kan hjälpa dig med detta.
* Du kan porta klientprogrambegäranden med en [återförsöksprincip som](/azure/architecture/best-practices/transient-faults#general-guidelines) du implementerar själv när du får den här statuskoden.

### <a name="my-endpoint-query-returned-unexpected-results-what-should-i-do"></a>Min slutpunktsfråga returnerade oväntade resultat. Vad ska jag göra?

Oväntade frågeförutsägelseresultat baseras på tillståndet för den publicerade modellen. För att korrigera modellen kan du behöva ändra modellen, träna och publicera igen.

Att korrigera modellen börjar med aktiv [inlärning.](luis-how-to-review-endpoint-utterances.md)

Du kan ta bort icke-deterministisk träning genom att uppdatera [API:et för inställningar för programversion](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) för att kunna använda alla träningsdata.

Läs igenom [metodtipsen](luis-concept-best-practices.md) för andra tips.

### <a name="why-does-luis-add-spaces-to-the-query-around-or-in-the-middle-of-words"></a>Varför lägger LUIS till blanksteg i frågan runt eller mitt i orden?
LUIS [tokeniserar](luis-glossary.md#token) yttranden baserat på [kulturen](luis-language-support.md#tokenization). Både det ursprungliga värdet och det tokeniserade värdet är tillgängliga för [extrahering av data.](luis-concept-data-extraction.md#tokenized-entity-returned)

### <a name="how-do-i-create-and-assign-a-luis-endpoint-key"></a>Hur gör jag för att skapa och tilldela en LUIS-slutpunktsnyckel?
[Skapa slutpunktsnyckeln](luis-how-to-azure-subscription.md) i Azure för [din servicenivå.](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/) [Tilldela nyckeln på](luis-how-to-azure-subscription.md) sidan **[Azure-resurser.](luis-how-to-azure-subscription.md)** Det finns inget motsvarande API för den här åtgärden. Sedan måste du ändra HTTP-begäran till slutpunkten för att [använda den nya slutpunktsnyckeln](luis-how-to-azure-subscription.md).

### <a name="how-do-i-interpret-luis-scores"></a>Hur gör jag för att tolka LUIS-poäng?
Systemet bör använda avsikten med högst poäng oavsett dess värde. Till exempel en poäng under 0,5 (mindre än 50 %) innebär inte nödvändigtvis att LUIS har lågt förtroende. Genom att tillhandahålla mer träningsdata kan du [öka poängen](luis-concept-prediction-score.md) för den mest sannolika avsikten.

### <a name="why-dont-i-see-my-endpoint-hits-in-my-apps-dashboard"></a>Varför visas inte mina slutpunktsträffar på instrumentpanelen för min app?
De totala slutpunktsträffarna i appens instrumentpanel uppdateras regelbundet, men de mått som är associerade med din LUIS-slutpunktsnyckel i Azure Portal uppdateras oftare.

Om du inte ser uppdaterade **slutpunktsträffar** i instrumentpanelen loggar du in på Azure Portal och hittar resursen som är associerad med din LUIS-slutpunktsnyckel och öppnar Mått för att välja måttet **Totalt antal** anrop. Om slutpunktsnyckeln används för mer än en LUIS-app visar måttet i Azure Portal det aggregerade antalet anrop från alla LUIS-appar som använder den.

### <a name="is-there-a-powershell-command-get-to-the-endpoint-quota"></a>Finns det ett PowerShell-kommando som kommer till slutpunktskvoten?

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Du kan använda ett PowerShell-kommando för att se slutpunktskvoten:

```powershell
Get-AzCognitiveServicesAccountUsage -ResourceGroupName <your-resource-group> -Name <your-resource-name>
```

### <a name="my-luis-app-was-working-yesterday-but-today-im-getting-403-errors-i-didnt-change-the-app-how-do-i-fix-it"></a>Min LUIS-app fungerade i går men idag får jag 403-fel. Jag ändrade inte appen. Vad kan jag göra?
Följ de [här anvisningarna](#how-do-i-create-and-assign-a-luis-endpoint-key) för att skapa en LUIS-slutpunktsnyckel och tilldela den till appen. Sedan måste du ändra klientprogrammets HTTP-begäran till slutpunkten för att använda [den nya slutpunktsnyckeln](luis-how-to-azure-subscription.md). Om du har skapat en ny resurs i en annan region ändrar du även regionen för HTTP-klientbegäran.

### <a name="how-do-i-secure-my-luis-endpoint"></a>Hur gör jag för att skydda min LUIS-slutpunkt?
Se [Skydda slutpunkten](luis-how-to-azure-subscription.md#securing-the-endpoint).

## <a name="working-within-luis-limits"></a>Arbeta inom LUIS-gränser

### <a name="what-is-the-maximum-number-of-intents-and-entities-that-a-luis-app-can-support"></a>Vilket är det maximala antalet avsikter och entiteter som en LUIS-app har stöd för?
Se [referensen för](luis-limits.md) gränser.

### <a name="i-want-to-build-a-luis-app-with-more-than-the-maximum-number-of-intents-what-should-i-do"></a>Jag vill skapa en LUIS-app med fler än det maximala antalet avsikter. Vad ska jag göra?

Se [Metodtips för avsikter.](luis-concept-intent.md#if-you-need-more-than-the-maximum-number-of-intents)

### <a name="what-are-the-limits-on-the-number-and-size-of-phrase-lists"></a>Vilka är gränserna för antalet och storleken på fraslistor?
Den maximala längden på en [fraslista finns](./luis-concept-feature.md)i [referensen för](luis-limits.md) gränser.

### <a name="what-are-the-limits-on-example-utterances"></a>Vilka begränsningar finns för exempelyttranden?
Se [referensen för](luis-limits.md) gränser.

## <a name="testing-and-training"></a>Testning och träning

### <a name="i-see-some-errors-in-the-batch-testing-pane-for-some-of-the-models-in-my-app-how-can-i-address-this-problem"></a>Jag ser några fel i fönstret för batchtestning för några av modellerna i min app. Hur kan jag lösa det här problemet?

Felen indikerar att det finns en avvikelse mellan etiketterna och förutsägelserna från dina modeller. Åtgärda problemet genom att utföra en eller båda av följande uppgifter:
* Lägg till fler etiketter för att hjälpa LUIS att förbättra effektiviteten bland avsikter.
* För att hjälpa LUIS att lära sig snabbare lägger du till funktioner för fraslistor som introducerar domänspecifik vokabulär.

Se [självstudien om Batch-testning.](./luis-how-to-batch-test.md)

### <a name="when-an-app-is-exported-then-reimported-into-a-new-app-with-a-new-app-id-the-luis-prediction-scores-are-different-why-does-this-happen"></a>När en app exporteras och sedan importeras igen till en ny app (med ett nytt app-ID) skiljer sig LUIS-förutsägelsepoängen. Varför sker det här?

Se [Förutsägelseskillnader mellan kopior av samma app.](luis-concept-prediction-score.md#review-intents-with-similar-scores)

### <a name="some-utterances-go-to-the-wrong-intent-after-i-made-changes-to-my-app-the-issue-seems-to-disappear-at-random-how-do-i-fix-it"></a>Vissa yttranden går till fel avsikt när jag har gjort ändringar i min app. Problemet verkar försvinna slumpmässigt. Vad kan jag göra?

Se [Träna med alla data.](luis-how-to-train.md#train-with-all-data)

## <a name="app-publishing"></a>Apppublicering

### <a name="what-is-the-tenant-id-in-the-add-a-key-to-your-app-window"></a>Vad är klientorganisations-ID:t i fönstret "Lägg till en nyckel i din app"?
I Azure representerar en klient den klient eller organisation som är associerad med en tjänst. Leta upp ditt klientorganisations-ID Azure Portal i rutan **Katalog-ID** genom att **välja Azure Active Directory**  >  **Hantera**  >  **egenskaper.**

![Klientorganisations-ID i Azure Portal](./media/luis-manage-keys/luis-assign-key-tenant-id.png)

<a name="why-are-there-more-subscription-keys-on-my-apps-publish-page-than-i-assigned-to-the-app"></a>
<a name="why-are-there-more-endpoint-keys-on-my-apps-publish-page-than-i-assigned-to-the-app"></a>


### <a name="why-are-there-more-endpoint-keys-assigned-to-my-app-than-i-assigned"></a>Varför är det fler slutpunktsnycklar tilldelade till min app än vad jag har tilldelat?
Varje LUIS-app har redigerings-/startnyckeln i slutpunktslistan som en bekvämlighet. Den här nyckeln tillåter bara några slutpunktsträffar så att du kan prova LUIS.

Om din app fanns innan LUIS var allmänt tillgänglig (GA) tilldelas LUIS-slutpunktsnycklar i din prenumeration automatiskt. Detta gjordes för att göra det enklare att migrera till ga-et. Nya LUIS-slutpunktsnycklar i Azure Portal _tilldelas inte_ automatiskt till LUIS.

## <a name="key-management"></a>Nyckelhantering

### <a name="how-do-i-know-what-key-i-need-where-i-get-it-and-what-i-do-with-it"></a>Hur gör jag för att vet vilken nyckel jag behöver, var jag får den och vad jag gör med den?

Mer [information om skillnaderna mellan redigeringsnyckeln](luis-how-to-azure-subscription.md) och prediktionskörningsnyckeln finns i Slutpunktsnycklar för redigering och frågeförutsägelse i LUIS.

### <a name="i-got-an-error-about-being-out-of-quota-how-do-i-fix-it"></a>Jag får ett felmeddelande om att jag är utanför kvoten. Vad kan jag göra?

Mer information finns i Åtgärda HTTP-statuskod [403](#i-received-an-http-403-error-status-code-how-do-i-fix-it) och [429.](#i-received-an-http-429-error-status-code-how-do-i-fix-it)

### <a name="i-need-to-handle-more-endpoint-queries-how-do-i-do-that"></a>Jag behöver hantera fler slutpunktsfrågor. Hur gör jag det?

Mer information finns i Åtgärda HTTP-statuskod [403](#i-received-an-http-403-error-status-code-how-do-i-fix-it) och [429.](#i-received-an-http-429-error-status-code-how-do-i-fix-it)

### <a name="i-created-an-authoring-key-but-it-isnt-showing-in-the-luis-portal-what-happened"></a>Jag har skapat en redigeringsnyckel men den visas inte i LUIS-portalen. Vad hände?

Redigeringsnycklar är tillgängliga i LUIS-portalen [när du har migrerat till redigeringsnyckeln.](luis-migration-authoring.md)

## <a name="app-management"></a>Apphantering

### <a name="how-do-i-download-a-log-of-user-utterances"></a>Hur gör jag för att ladda ned en logg med användaryttranden?
Som standard loggar LUIS-appen yttranden från användare. Om du vill ladda ned en logg med yttranden som användare skickar till luis-appen går du **till Mina appar** och väljer appen. I det sammanhangsbaserade verktygsfältet väljer du **Exportera slutpunktsloggar.** Loggen formateras som en fil med kommaavgränsade värden (CSV).

### <a name="how-can-i-disable-the-logging-of-utterances"></a>Hur inaktiverar jag loggning av yttranden?
Du kan inaktivera loggning av användaryttranden genom att ange i `log=false` slutpunkts-URL:en som klientprogrammet använder för att fråga LUIS. Om du inaktiverar loggning inaktiveras dock luis-appens möjlighet att föreslå yttranden eller förbättra prestanda som baseras på [aktiv inlärning.](luis-concept-review-endpoint-utterances.md#what-is-active-learning) Om du anger på grund av datasekretessproblem kan du inte ladda ned en post med dessa användaryttranden från LUIS eller använda dessa yttranden för att `log=false` förbättra din app.

Loggning är den enda lagringen av yttranden.

### <a name="why-dont-i-want-all-my-endpoint-utterances-logged"></a>Varför vill jag inte att alla mina slutpunktsyttranden loggas?
Om du använder loggen för förutsägelseanalys ska du inte samla in testyttrantran i loggen.

## <a name="data-management"></a>Datahantering

### <a name="can-i-delete-data-from-luis"></a>Kan jag ta bort data från LUIS?

* Du kan alltid ta bort exempelyttranden som används för luis-träning. Om du tar bort ett exempelyttrande från LUIS-appen tas det bort från LUIS-webbtjänsten och är inte tillgängligt för export.
* Du kan ta bort yttranden från listan över användaryttranden som LUIS föreslår på **sidan Granska slutpunktsyttranden.** Om du tar bort yttranden från den här listan förhindras de från att föreslås, men de tas inte bort från loggarna.
* Om du tar bort ett konto tas alla appar bort, tillsammans med exempelyttranden och loggar. Data sparas på servrarna i 60 dagar innan de tas bort permanent.

### <a name="how-does-microsoft-manage-data-i-send-to-luis"></a>Hur hanterar Microsoft data som jag skickar till LUIS?

Säkerhetscenter [förklarar](https://www.microsoft.com/trustcenter) våra åtaganden och dina alternativ för datahantering och åtkomst i Azure-tjänster.

## <a name="language-and-translation-support"></a>Stöd för språk och översättning

### <a name="i-have-an-app-in-one-language-and-want-to-create-a-parallel-app-in-another-language-what-is-the-easiest-way-to-do-so"></a>Jag har en app på ett språk och vill skapa en parallell app på ett annat språk. Vad är det enklaste sättet att göra det?
1. Exportera din app.
2. Översätt de märkta yttrandena i JSON-filen för den exporterade appen till målspråket.
3. Du kan behöva ändra namnen på avsikterna och entiteterna eller lämna dem som de är.
4. Slutligen importerar du appen för att ha en LUIS-app på målspråket.

## <a name="app-notification"></a>Appmeddelande

### <a name="why-did-i-get-an-email-saying-im-almost-out-of-quota"></a>Varför fick jag ett e-postmeddelande om att jag nästan är slut på kvot?
Din redigerings-/startnyckel tillåts endast 1 000 slutpunktsfrågor per månad. Skapa en LUIS-slutpunktsnyckel (kostnadsfri eller betald) och använd den nyckeln när du skapar slutpunktsfrågor. Om du gör slutpunktsfrågor från en robot eller ett annat klientprogram måste du ändra LUIS-slutpunktsnyckeln där.

## <a name="bots"></a>Robotar

### <a name="my-luis-bot-isnt-working-what-do-i-do"></a>Min LUIS-robot fungerar inte. Vad gör jag nu?

Det första problemet är att isolera om problemet är relaterat till LUIS eller sker utanför LUIS-mellanprogram.

#### <a name="resolve-issue-in-luis"></a>Lösa problem i LUIS
Skicka samma uttryck till LUIS från [LUIS-slutpunkten](luis-get-started-create-app.md#query-the-v2-api-prediction-endpoint). Om du får ett felmeddelande kan du lösa problemet i LUIS tills felet inte längre returneras. Vanliga fel är:

* `Out of call volume quota. Quota will be replenished in <time>.`– Det här problemet anger att du antingen [](luis-how-to-azure-subscription.md) behöver ändra från en redigeringsnyckel till en slutpunktsnyckel eller att du behöver [ändra tjänstnivåer.](luis-how-to-azure-subscription.md#change-the-pricing-tier)

#### <a name="resolve-issue-in-azure-bot-service"></a>Lösa problem i Azure Bot Service

Om du använder Azure Bot Service problemet är att **testet** i den Webbchatt returnerar `Sorry, my bot code is having an issue` kontrollerar du loggarna:

1. I Azure Portal väljer du Skapa i avsnittet **Robothantering** för **din robot.**
1. Öppna onlinekodredigeraren.
1. I det övre blå navigeringsfältet väljer du robotnamnet (det andra objektet till höger).
1. I den resulterande listrutan väljer du **Öppna Kudu-konsolen**.
1. Välj **LogFiles** och sedan **Program**. Granska alla loggfiler. Om du inte ser felet i programmappen granskar du alla loggfiler under **LogFiles**.
1. Kom ihåg att återskapa projektet om du använder ett kompilerat språk som C#.

> [!Tip]
> -konsolen kan också installera paket.

#### <a name="resolve-issue-while-debugging-on-local-machine-with-bot-framework"></a>Lös problemet vid felsökning på en lokal dator med Bot Framework.

Mer information om lokal felsökning av en robot finns i [Felsöka en robot.](/azure/bot-service/bot-service-debug-bot)

## <a name="integrating-luis"></a>Integrera LUIS

### <a name="where-is-my-luis-app-created-during-the-azure-web-app-bot-subscription-process"></a>Var skapas min LUIS-app under prenumerationsprocessen för Azure-webbappsroboten?
Om du väljer en LUIS-mall och väljer knappen Välj i mallfönstret ändras fönstret till vänster så att malltypen inkluderas och frågar i vilken region LUIS-mallen ska skapas.  Webbappsrobotprocessen skapar dock ingen LUIS-prenumeration.

![Region för LUIS-mallwebbappsrobot](./media/luis-faq/web-app-bot-location.png)

### <a name="what-luis-regions-support-bot-framework-speech-priming"></a>Vilka LUIS-regioner stöder Bot Framework för talprimering?
[Talprimering stöds](/bot-framework/bot-service-manage-speech-priming) endast för LUIS-appar i den centrala instansen (USA).

## <a name="api-programming-strategies"></a>API-programmeringsstrategier

### <a name="how-do-i-programmatically-get-the-luis-region-of-a-resource"></a>Hur gör jag för att hämta LUIS-regionen för en resurs programmatiskt?

Använd LUIS-exemplet för [att hitta regionen](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/find-region) programmatiskt med hjälp av C# eller Node.Js.

## <a name="luis-service"></a>LUIS-tjänst

### <a name="is-language-understanding-luis-available-on-premises-or-in-private-cloud"></a>Är Language Understanding (LUIS) tillgängligt lokalt eller i ett privat moln?

Ja, du kan använda [LUIS-containern för](luis-container-howto.md) dessa scenarier om du har nödvändig anslutning till mätaranvändning.

## <a name="migrating-to-the-next-version"></a>Migrera till nästa version

### <a name="how-do-i-migrate-to-preview-v3-api"></a>Hur gör jag för att migrera till förhandsversionen av V3 API?

Se [Migreringsguide för API v2 till v3 för LUIS-appar](luis-migration-api-v3.md)

## <a name="build-2019-conference-announcements"></a>Tillkännagivanden om Build 2019-konferensen

Följande funktioner släpptes på Build 2019-konferensen:

* [Förhandsversion av V3 API-migreringsguide](luis-migration-api-v3.md)
* [Förbättrad analysinstrumentpanel](luis-how-to-use-dashboard.md)
* [Förbättrade fördefinierade domäner](luis-reference-prebuilt-domains.md)
* [Dynamiska listentiteter](schema-change-prediction-runtime.md#dynamic-lists-passed-in-at-prediction-time)
* [Externa entiteter](luis-migration-api-v3.md#external-entities-passed-in-at-prediction-time)

Videor:

* [Så här använder du Azure Conversational AI för att skala din verksamhet för nästa generation](https://www.youtube.com/watch?v=_k97jd-csuk&feature=youtu.be)

## <a name="next-steps"></a>Nästa steg

Mer information om LUIS finns i följande resurser:
* [Stack Overflow frågor taggade med LUIS](https://stackoverflow.com/questions/tagged/luis)
* [Microsoft Q&En frågesida för MSDN Language Understanding Intelligent Services (LUIS)](/answers/topics/azure-language-understanding.html)