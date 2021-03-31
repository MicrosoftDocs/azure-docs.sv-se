---
title: Vanliga frågor och svar – LUIS
description: Den här artikeln innehåller svar på vanliga frågor om Language Understanding (LUIS).
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: troubleshooting
ms.date: 05/06/2020
ms.openlocfilehash: b5e25e9ed25ced96d38994928bcb6275ce79420f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "102612804"
---
# <a name="language-understanding-frequently-asked-questions-faq"></a>Vanliga frågor och svar om Language Understanding

Den här artikeln innehåller svar på vanliga frågor om Language Understanding (LUIS).

## <a name="whats-new"></a>Nyheter

[Läs mer](whats-new.md) om vad som är nytt i language Understanding (Luis).

<a name="luis-authoring"></a>

## <a name="authoring"></a>Redigering

### <a name="what-are-the-luis-best-practices"></a>Vad är bästa praxis för LUIS?
Börja med [redigerings cykeln](luis-concept-app-iteration.md)och Läs sedan [metod tipsen](luis-concept-best-practices.md).

### <a name="what-is-the-best-way-to-start-building-my-app-in-luis"></a>Vad är det bästa sättet att börja skapa min app i LUIS?

Det bästa sättet att bygga din app är genom en [stegvis process](luis-concept-app-iteration.md).

### <a name="what-is-a-good-practice-to-model-the-intents-of-my-app-should-i-create-more-specific-or-more-generic-intents"></a>Vad är en bra idé att modellera syftet med min app? Bör jag skapa mer detaljerade eller mer generella avsikter?

Välj avsikter som inte är så generella som överlappande, men inte så särskilt att det gör det svårt för LUIS att skilja mellan liknande syften. Att skapa Discriminative-specificerade avsikter är en av de bästa metoderna för LUIS-modellering.

### <a name="is-it-important-to-train-the-none-intent"></a>Är det viktigt att träna none-avsikten?

Ja, det är enkelt att träna **ingen** avsikt med fler yttranden när du lägger till fler etiketter till andra syften. Ett positivt förhållande är 1 eller 2 etiketter som lagts till **ingen** för varje 10-etiketter till ett avsikts sätt. Det här förhållandet ökar Discriminative-kraften hos LUIS.

### <a name="how-can-i-correct-spelling-mistakes-in-utterances"></a>Hur kan jag korrigera stavfel i yttranden?

Se själv studie kursen [API för stavningskontroll i Bing v7](luis-tutorial-bing-spellcheck.md) . LUIS tillämpar gränser från API för stavningskontroll i Bing v7.

### <a name="how-do-i-edit-my-luis-app-programmatically"></a>Hur gör jag för att redigera min LUIS-app program mässigt?
Om du vill redigera LUIS-appen program mässigt använder du [redigerings-API: et](https://go.microsoft.com/fwlink/?linkid=2092087). Se [anropa Luis Authoring API](./get-started-get-model-rest-apis.md) och [skapa en Luis-app program mässigt med hjälp av Node.js](./luis-tutorial-node-import-utterances-csv.md) för att få exempel på hur du anropar REDIGERINGS-API: et. Redigerings-API: n kräver att du använder en [redigerings nyckel](luis-how-to-azure-subscription.md#azure-resources-for-luis) i stället för en slut punkts nyckel. Med programmerings redigering får upp till 1 000 000 anrop per månad och fem transaktioner per sekund. Mer information om de nycklar som du använder med LUIS finns i [Hantera nycklar](./luis-how-to-azure-subscription.md).

### <a name="where-is-the-pattern-feature-that-provided-regular-expression-matching"></a>Var finns mönster funktionen som tillhandahöll matchning av reguljärt uttryck?
Den föregående **mönster funktionen** är för närvarande inaktuell, ersatt av **[mönster](luis-concept-patterns.md)**.

### <a name="how-do-i-use-an-entity-to-pull-out-the-correct-data"></a>Vill du Hur gör jag för att använda en entitet för att hämta rätt data?
Se [entiteter](luis-concept-entity-types.md) och [data extrahering](luis-concept-data-extraction.md).

### <a name="should-variations-of-an-example-utterance-include-punctuation"></a>Ska variationer i ett exempel uttryck inkludera skiljetecken?
Använd någon av följande lösningar:
* Ignorera [interpunktion](luis-reference-application-settings.md#punctuation-normalization)
* Lägg till de olika variationerna som exempel yttranden i avsikten
* Lägg till mönstret i exemplet uttryck med [syntaxen för att ignorera](luis-concept-patterns.md#pattern-syntax) interpunktionen.

### <a name="does-luis-currently-support-cortana"></a>Stöder LUIS för närvarande Cortana?

De förinställda Cortana-apparna var inaktuella i 2017. De stöds inte längre.

### <a name="how-do-i-transfer-ownership-of-a-luis-app"></a>Hur gör jag för att du överföra ägarskapet för en LUIS-app?
Om du vill överföra en LUIS-app till en annan Azure-prenumeration exporterar du LUIS-appen och importerar den med ett nytt konto. Uppdatera app-ID: t för LUIS i klient programmet som anropar det. Den nya appen kan returnera något annorlunda LUIS-resultat från den ursprungliga appen.

### <a name="a-prebuilt-entity-is-tagged-in-an-example-utterance-instead-of-my-custom-entity-how-do-i-fix-this"></a>En fördefinierad entitet är taggad i ett exempel-uttryck i stället för min anpassade entitet. Hur gör jag för att åtgärda detta?

I LUIS-portalen kan du märka text för den exakta entitet som du är intresse rad av. Om LUIS-portalen inte visar rätt enhets förutsägelse kan du behöva lägga till fler yttranden och namnge entiteten i texten eller lägga till en funktion.

### <a name="i-tried-to-import-an-app-or-version-file-but-i-got-an-error-what-happened"></a>Jag försökte importera en app-eller versions fil, men jag fick ett fel meddelande, vad hände?

Läs mer om [versions import fel](luis-how-to-manage-versions.md#import-errors).

<a name="luis-collaborating"></a>

## <a name="collaborating-and-contributing"></a>Samar beta och bidrag

### <a name="how-do-i-give-collaborators-access-to-luis-with-azure-active-directory-azure-ad-or-azure-role-based-access-control-azure-rbac"></a>Hur gör jag för att ge medarbetarna åtkomst till LUIS med Azure Active Directory (Azure AD) eller rollbaserad åtkomst kontroll i Azure (Azure RBAC)?

Se [Azure Active Directory resurser](luis-how-to-collaborate.md#azure-active-directory-resources)  och [Azure Active Directory klient användare](luis-how-to-collaborate.md#azure-active-directory-tenant-user) för att lära dig hur du ger samarbets åtkomst.

<a name="luis-endpoint"></a>

## <a name="endpoint"></a>Slutpunkt

### <a name="i-received-an-http-403-error-status-code-how-do-i-fix-it"></a>Jag har fått en HTTP 403-fel status kod. Vad kan jag göra?

Du får status koderna 403 och 429 när du överskrider transaktionerna per sekund eller transaktioner per månad för din pris nivå. Öka pris nivån eller Använd Language Understanding [behållare](luis-container-howto.md).

När du använder alla de kostnads fria 1000-slutpunkts frågorna eller om du överskrider din pris nivås kvot för månads transaktioner får du en HTTP 403-fel status kod.

För att åtgärda det här felet måste du antingen [ändra pris nivån](luis-how-to-azure-subscription.md#change-the-pricing-tier) till en högre nivå eller [skapa en ny resurs](get-started-portal-deploy-app.md#create-the-endpoint-resource) och [tilldela den till din app](get-started-portal-deploy-app.md#assign-the-resource-key-to-the-luis-app-in-the-luis-portal).

Lösningar för det här felet är:

* I [Azure Portal](https://portal.azure.com)i language Understanding resurs, på **pris nivån resurs hantering->**, ändrar du pris nivån till en högre TPS-nivå. Du behöver inte göra något i Language Understanding portal om din resurs redan har tilldelats till din Language Understanding-app.
*  Om din användning överskrider den högsta pris nivån lägger du till fler Language Understanding resurser med en belastningsutjämnare framför dem. [Language Understanding containern](luis-container-howto.md) med Kubernetes eller Docker Compose kan hjälpa dig med detta.

### <a name="i-received-an-http-429-error-status-code-how-do-i-fix-it"></a>Jag har fått en HTTP 429-fel status kod. Vad kan jag göra?

Du får status koderna 403 och 429 när du överskrider transaktionerna per sekund eller transaktioner per månad för din pris nivå. Öka pris nivån eller Använd Language Understanding [behållare](luis-container-howto.md).

Den här status koden returneras när transaktionerna per sekund överskrider pris nivån.

Lösningarna omfattar:

* Du kan [öka pris nivån](luis-how-to-azure-subscription.md#change-the-pricing-tier)om du inte är på den högsta nivån.
* Om din användning överskrider den högsta pris nivån lägger du till fler Language Understanding resurser med en belastningsutjämnare framför dem. [Language Understanding containern](luis-container-howto.md) med Kubernetes eller Docker Compose kan hjälpa dig med detta.
* Du kan Grinda klient program begär Anden med en [princip för återförsök](/azure/architecture/best-practices/transient-faults#general-guidelines) som du implementerar själv när du får den här status koden.

### <a name="my-endpoint-query-returned-unexpected-results-what-should-i-do"></a>Min slut punkts fråga returnerade oväntade resultat. Vad ska jag göra?

Oväntade frågeresultat baseras på den publicerade modellens tillstånd. Om du vill korrigera modellen kan du behöva ändra modellen, träna och publicera igen.

Korrigeringen av modellen börjar med [aktiv inlärning](luis-how-to-review-endpoint-utterances.md).

Du kan ta bort icke-deterministisk utbildning genom att uppdatera [API för program versions inställningar](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) för att kunna använda alla tränings data.

Se [metod tipsen](luis-concept-best-practices.md) för andra tips.

### <a name="why-does-luis-add-spaces-to-the-query-around-or-in-the-middle-of-words"></a>Varför lägger LUIS till blank steg i frågan runt eller i mitten av ord?
LUIS [tokenizes](luis-glossary.md#token) uttryck baserat på [kulturen](luis-language-support.md#tokenization). Både det ursprungliga värdet och det token-värde som är tillgängligt för [data extrahering](luis-concept-data-extraction.md#tokenized-entity-returned).

### <a name="how-do-i-create-and-assign-a-luis-endpoint-key"></a>Hur gör jag för att skapa och tilldela en slut punkts nyckel för LUIS?
[Skapa slut punkts nyckeln](luis-how-to-azure-subscription.md) i Azure för din [service](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/) nivå. [Tilldela nyckeln](luis-how-to-azure-subscription.md) på sidan **[Azure-resurser](luis-how-to-azure-subscription.md)** . Det finns inget motsvarande API för den här åtgärden. Sedan måste du ändra HTTP-begäran till slut punkten för att [använda den nya slut punkts nyckeln](luis-how-to-azure-subscription.md).

### <a name="how-do-i-interpret-luis-scores"></a>Hur gör jag för att tolka LUIS resultat?
Ditt system bör använda den bästa bedömnings avsikten oavsett dess värde. Till exempel en poäng under 0,5 (mindre än 50%) innebär inte nödvändigt vis att LUIS har låg exakthet. Att tillhandahålla fler utbildnings data kan öka [resultatet](luis-concept-prediction-score.md) av den mest sannolika avsikten.

### <a name="why-dont-i-see-my-endpoint-hits-in-my-apps-dashboard"></a>Varför visas inte mina slut punkts träffar i min Apps-instrumentpanel?
De totala slut punkts träffarna i appens instrument panel uppdateras regelbundet, men måtten som associeras med din LUIS-slutpunkt nyckel i Azure Portal uppdateras oftare.

Om du inte ser uppdaterade slut punkts träffar på instrument panelen loggar du in på Azure Portal och letar upp den resurs som är kopplad till din LUIS-slutpunkt nyckel och öppnar **mått** för att välja det **totala anrops** måttet. Om slut punkts nyckeln används för mer än en LUIS-app, visar måttet i Azure Portal det sammanlagda antalet anrop från alla LUIS-appar som använder den.

### <a name="is-there-a-powershell-command-get-to-the-endpoint-quota"></a>Finns det ett PowerShell-kommando som får slut punkts kvoten?

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Du kan använda ett PowerShell-kommando för att Visa slut punkts kvoten:

```powershell
Get-AzCognitiveServicesAccountUsage -ResourceGroupName <your-resource-group> -Name <your-resource-name>
```

### <a name="my-luis-app-was-working-yesterday-but-today-im-getting-403-errors-i-didnt-change-the-app-how-do-i-fix-it"></a>Min LUIS-app fungerade igår men idag får du 403-fel. Jag ändrade inte appen. Vad kan jag göra?
Följ de här [anvisningarna](#how-do-i-create-and-assign-a-luis-endpoint-key) för att skapa en Luis-slutpunkt-nyckel och tilldela den till appen. Sedan måste du ändra klient programmets HTTP-begäran till slut punkten för att kunna [använda den nya slut punkts nyckeln](luis-how-to-azure-subscription.md). Om du har skapat en ny resurs i en annan region, ändrar du även HTTP-klientbegärans region.

### <a name="how-do-i-secure-my-luis-endpoint"></a>Hur gör jag för att säker min LUIS-slutpunkt?
Se [skydda slut punkten](luis-how-to-azure-subscription.md#securing-the-endpoint).

## <a name="working-within-luis-limits"></a>Arbeta inom LUIS-gränser

### <a name="what-is-the-maximum-number-of-intents-and-entities-that-a-luis-app-can-support"></a>Vilket är det maximala antalet syften och entiteter som en LUIS-app har stöd för?
Se [gränser](luis-limits.md) -referensen.

### <a name="i-want-to-build-a-luis-app-with-more-than-the-maximum-number-of-intents-what-should-i-do"></a>Jag vill bygga en LUIS-app med fler än det maximala antalet syften. Vad ska jag göra?

Se [metod tips för avsikter](luis-concept-intent.md#if-you-need-more-than-the-maximum-number-of-intents).

### <a name="i-want-to-build-an-app-in-luis-with-more-than-the-maximum-number-of-entities-what-should-i-do"></a>Jag vill bygga en app i LUIS med fler än det maximala antalet entiteter. Vad ska jag göra?

Se [metod tips för entiteter](luis-concept-entity-types.md#if-you-need-more-than-the-maximum-number-of-entities)

### <a name="what-are-the-limits-on-the-number-and-size-of-phrase-lists"></a>Vilka är gränserna för fras listornas antal och storlek?
För den maximala längden för en [fras lista](./luis-concept-feature.md), se [gräns](luis-limits.md) referensen.

### <a name="what-are-the-limits-on-example-utterances"></a>Vilka är gränserna för exempel yttranden?
Se [gränser](luis-limits.md) -referensen.

## <a name="testing-and-training"></a>Testning och utbildning

### <a name="i-see-some-errors-in-the-batch-testing-pane-for-some-of-the-models-in-my-app-how-can-i-address-this-problem"></a>Jag ser några fel i fönstret batch-test för några av modellerna i appen. Hur kan jag lösa det här problemet?

Felen indikerar att det finns en avvikelse mellan etiketterna och förutsägelserna från dina modeller. Åtgärda problemet genom att utföra en eller båda av följande uppgifter:
* För att hjälpa LUIS att förbättra diskrimineringen mellan olika syften, Lägg till fler etiketter.
* För att hjälpa LUIS att lära dig snabbare kan du lägga till funktioner för fras listor som introducerar en domänbaserad vokabulär.

Se själv studie kursen för [batch-testning](./luis-how-to-batch-test.md) .

### <a name="when-an-app-is-exported-then-reimported-into-a-new-app-with-a-new-app-id-the-luis-prediction-scores-are-different-why-does-this-happen"></a>När en app exporteras och sedan importeras om till en ny app (med ett nytt app-ID), är LUIS förutsägelse poängen annorlunda. Varför sker det här?

Se [förutsägelse skillnader mellan kopior av samma app](luis-concept-prediction-score.md#review-intents-with-similar-scores).

### <a name="some-utterances-go-to-the-wrong-intent-after-i-made-changes-to-my-app-the-issue-seems-to-disappear-at-random-how-do-i-fix-it"></a>Vissa yttranden går till fel avsikt när jag har gjort ändringar i min app. Problemet verkar försvinna slumpmässigt. Vad kan jag göra?

Se [träna med alla data](luis-how-to-train.md#train-with-all-data).

## <a name="app-publishing"></a>Publicera appar

### <a name="what-is-the-tenant-id-in-the-add-a-key-to-your-app-window"></a>Vad är klient-ID: t i fönstret Lägg till en nyckel i din app?
I Azure representerar en klient organisation eller organisation som är associerad med en tjänst. Hitta ditt klient-ID i Azure Portal i rutan **katalog-ID** genom att välja **Azure Active Directory**  >  **Hantera**  >  **Egenskaper**.

![Klient-ID i Azure Portal](./media/luis-manage-keys/luis-assign-key-tenant-id.png)

<a name="why-are-there-more-subscription-keys-on-my-apps-publish-page-than-i-assigned-to-the-app"></a>
<a name="why-are-there-more-endpoint-keys-on-my-apps-publish-page-than-i-assigned-to-the-app"></a>


### <a name="why-are-there-more-endpoint-keys-assigned-to-my-app-than-i-assigned"></a>Varför har fler slut punkts nycklar tilldelats till min app än jag har tilldelat?
Varje LUIS-app har redigerings-/start nyckeln i slut punkts listan som en bekvämlighet. Den här nyckeln tillåter bara några få slut punkts träffar, så du kan prova LUIS.

Om din app fanns innan LUIS var allmänt tillgänglig (GA), tilldelas LUIS slut punkts nycklar i prenumerationen automatiskt. Detta skedde för att göra en allmän enkel migrering enklare. Eventuella nya LUIS slut punkts nycklar i Azure Portal tilldelas _inte_ automatiskt till Luis.

## <a name="key-management"></a>Nyckelhantering

### <a name="how-do-i-know-what-key-i-need-where-i-get-it-and-what-i-do-with-it"></a>Hur gör jag för att vet du vilken nyckel jag behöver, var jag får den och vad jag gör med det?

Se [Redigera och fråga efter slut punkts nycklar i Luis](luis-how-to-azure-subscription.md) för att lära dig mer om skillnaderna mellan redigerings nyckeln och den förutsägelse körnings nyckeln.

### <a name="i-got-an-error-about-being-out-of-quota-how-do-i-fix-it"></a>Jag har fått ett fel meddelande om att kvoten är felaktig. Vad kan jag göra?

Se, korrigera HTTP-statuskod [403](#i-received-an-http-403-error-status-code-how-do-i-fix-it) och [429](#i-received-an-http-429-error-status-code-how-do-i-fix-it) för mer information.

### <a name="i-need-to-handle-more-endpoint-queries-how-do-i-do-that"></a>Jag behöver hantera fler slut punkts frågor. Hur gör jag det?

Se, korrigera HTTP-statuskod [403](#i-received-an-http-403-error-status-code-how-do-i-fix-it) och [429](#i-received-an-http-429-error-status-code-how-do-i-fix-it) för mer information.

### <a name="i-created-an-authoring-key-but-it-isnt-showing-in-the-luis-portal-what-happened"></a>Jag skapade en redigerings nyckel men den visas inte i LUIS-portalen. Vad hände?

Redigerings nycklar är tillgängliga i LUIS-portalen när [du har migrerat till redigerings nyckel upplevelsen](luis-migration-authoring.md).

## <a name="app-management"></a>Apphantering

### <a name="how-do-i-download-a-log-of-user-utterances"></a>Hur gör jag för att ladda ned en logg med User yttranden?
Som standard loggar LUIS-appen yttranden från användare. Om du vill ladda ned en logg över yttranden som användarna skickar till din LUIS-app går du till **Mina appar** och väljer appen. I kontext verktygsfältet väljer du **Exportera slut punkts loggar**. Loggen är formaterad som en fil med kommaavgränsade värden (CSV).

### <a name="how-can-i-disable-the-logging-of-utterances"></a>Hur kan jag inaktivera loggning av yttranden?
Du kan inaktivera loggning av användar-yttranden genom att ange `log=false` i slut punkts-URL: en som ditt klient program använder för att fråga Luis. Att stänga av loggning inaktiverar dock LUIS-appens möjlighet att föreslå yttranden eller förbättra prestandan som baseras på [aktiv inlärning](luis-concept-review-endpoint-utterances.md#what-is-active-learning). Om du anger `log=false` på grund av data sekretess problem kan du inte ladda ned en post med dessa användare yttranden från Luis eller använda dessa yttranden för att förbättra din app.

Loggning är den enda lagringen av yttranden.

### <a name="why-dont-i-want-all-my-endpoint-utterances-logged"></a>Varför vill jag inte att alla slut punkts yttranden ska loggas?
Om du använder din logg för förutsägelse analys ska du inte samla in test-yttranden i loggen.

## <a name="data-management"></a>Datahantering

### <a name="can-i-delete-data-from-luis"></a>Kan jag ta bort data från LUIS?

* Du kan alltid ta bort exempel yttranden som används för utbildning LUIS. Om du tar bort ett exempel på uttryck från din LUIS-app, tas den bort från LUIS-webbtjänsten och är inte tillgänglig för export.
* Du kan ta bort yttranden från listan över användare yttranden som LUIS föreslår på sidan **Granska slut punkt yttranden** . Att ta bort yttranden från den här listan förhindrar att de föreslås, men tar inte bort dem från loggar.
* Om du tar bort ett konto tas alla appar bort, tillsammans med deras exempel yttranden och loggar. Data sparas på servrarna i 60 dagar innan de tas bort permanent.

### <a name="how-does-microsoft-manage-data-i-send-to-luis"></a>Hur hanterar Microsoft data som jag skickar till LUIS?

[Trust Center](https://www.microsoft.com/trustcenter) förklarar våra åtaganden och dina alternativ för data hantering och åtkomst i Azure-tjänster.

## <a name="language-and-translation-support"></a>Stöd för språk och översättning

### <a name="i-have-an-app-in-one-language-and-want-to-create-a-parallel-app-in-another-language-what-is-the-easiest-way-to-do-so"></a>Jag har en app på ett språk och vill skapa en parallell app på ett annat språk. Vad är det enklaste sättet att göra detta?
1. Exportera din app.
2. Översätt den märkta yttranden i JSON-filen för den exporterade appen till mål språket.
3. Du kan behöva ändra namnen på avsikten och entiteterna eller lämna dem som de är.
4. Importera slutligen appen så att den har en LUIS-app på mål språket.

## <a name="app-notification"></a>Meddelande om app

### <a name="why-did-i-get-an-email-saying-im-almost-out-of-quota"></a>Varför fick jag ett e-postmeddelande som säger att jag är nästan slut på kvoten?
Din redigerings-/start nyckel är endast tillåten 1000-slut punkts frågor per månad. Skapa en slut punkts nyckel för LUIS (kostnads fri eller betald) och Använd den nyckeln när du gör slut punkts frågor. Om du gör slut punkts frågor från en robot eller något annat klient program måste du ändra LUIS-slut punkts nyckeln där.

## <a name="bots"></a>Robotar

### <a name="my-luis-bot-isnt-working-what-do-i-do"></a>Min LUIS-robot fungerar inte. Vad gör jag nu?

Det första problemet är att isolera om problemet är relaterat till LUIS eller sker utanför LUIS mellan.

#### <a name="resolve-issue-in-luis"></a>Lös problem i LUIS
Skicka samma uttryck till LUIS från Luis- [slutpunkten](luis-get-started-create-app.md#query-the-v2-api-prediction-endpoint). Om du får ett fel meddelande kan du lösa problemet i LUIS tills felet inte längre returneras. Vanliga fel är:

* `Out of call volume quota. Quota will be replenished in <time>.` – Det här problemet indikerar att du antingen måste ändra från en redigerings nyckel till en [slut punkts nyckel](luis-how-to-azure-subscription.md) eller ändra [tjänst nivåerna](luis-how-to-azure-subscription.md#change-the-pricing-tier).

#### <a name="resolve-issue-in-azure-bot-service"></a>Lös problem i Azure Bot Service

Om du använder Azure Bot Service och problemet är att **testet i Web Chat** returnerar `Sorry, my bot code is having an issue` , kontrollerar du loggarna:

1. I Azure Portal för din robot, i avsnittet **robot Management** , väljer du **build**.
1. Öppna kod redigeraren online.
1. I det övre, blå navigerings fältet väljer du namnet på bot (det andra objektet till höger).
1. I den resulterande List rutan väljer du **Öppna kudu-konsolen**.
1. Välj **loggfiler** och välj sedan **program**. Granska alla loggfiler. Om du inte ser felet i programmappen granskar du alla loggfiler under **loggfiler**.
1. Kom ihåg att återskapa projektet om du använder ett kompilerat språk, till exempel C#.

> [!Tip]
> -Konsolen kan även installera paket.

#### <a name="resolve-issue-while-debugging-on-local-machine-with-bot-framework"></a>Lös problemet vid fel sökning på den lokala datorn med bot Framework.

Mer information om lokal fel sökning av en bot finns i [Felsöka en bot](/azure/bot-service/bot-service-debug-bot).

## <a name="integrating-luis"></a>Integrera LUIS

### <a name="where-is-my-luis-app-created-during-the-azure-web-app-bot-subscription-process"></a>Var skapas min LUIS-app under prenumerations processen för Azure Web App bot?
Om du väljer en LUIS-mall och väljer knappen **Välj** i rutan mall ändras den vänstra rutan så att den innehåller Malltyp och frågar i vilken region som du vill skapa Luis-mallen. Web App bot-processen skapar ingen LUIS-prenumeration.

![LUIS-mall Web App bot region](./media/luis-faq/web-app-bot-location.png)

### <a name="what-luis-regions-support-bot-framework-speech-priming"></a>Vilka LUIS-regioner stöder bot Framework Speech Prima?
[Tal-Prima](/bot-framework/bot-service-manage-speech-priming) stöds bara för Luis-appar i den centrala (amerikanska) instansen.

## <a name="api-programming-strategies"></a>API-programmerings strategier

### <a name="how-do-i-programmatically-get-the-luis-region-of-a-resource"></a>Hur gör jag för att program mässigt Hämta LUIS-regionen för en resurs?

Använd LUIS-exemplet för att [hitta region](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/find-region) program mässigt med C# eller Node.Js.

## <a name="luis-service"></a>LUIS-tjänst

### <a name="is-language-understanding-luis-available-on-premises-or-in-private-cloud"></a>Är Language Understanding (LUIS) tillgängligt lokalt eller i ett privat moln?

Ja, du kan använda LUIS- [behållaren](luis-container-howto.md) för dessa scenarier om du har den anslutning som krävs för att mäta användningen.

## <a name="migrating-to-the-next-version"></a>Migrera till nästa version

### <a name="how-do-i-migrate-to-preview-v3-api"></a>Hur gör jag för att migrera till Preview v3 API?

Se [API v2 till v3 migration guide för Luis-appar](luis-migration-api-v3.md)

## <a name="build-2019-conference-announcements"></a>Bygg 2019 konferens meddelanden

Följande funktioner släpptes på Build 2019-konferensen:

* [För hands version av v3 API migration guide](luis-migration-api-v3.md)
* [Förbättrad analys instrument panel](luis-how-to-use-dashboard.md)
* [Förbättrade fördefinierade domäner](luis-reference-prebuilt-domains.md)
* [Dynamiska List enheter](schema-change-prediction-runtime.md#dynamic-lists-passed-in-at-prediction-time)
* [Externa entiteter](luis-migration-api-v3.md#external-entities-passed-in-at-prediction-time)

Videor:

* [Så här använder du Azure Conversation AI för att skala din verksamhet för nästa generation](https://www.youtube.com/watch?v=_k97jd-csuk&feature=youtu.be)

## <a name="next-steps"></a>Nästa steg

Mer information om LUIS finns i följande resurser:
* [Stack Overflow frågor taggade med LUIS](https://stackoverflow.com/questions/tagged/luis)
* [Sidan Microsoft Q&en fråga för MSDN Language Understanding intelligent Services (LUIS)](/answers/topics/azure-language-understanding.html)