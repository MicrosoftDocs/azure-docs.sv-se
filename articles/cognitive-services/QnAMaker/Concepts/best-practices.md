---
title: Metod tips – QnA Maker
description: Använd dessa metod tips för att förbättra din kunskaps bas och ge bättre resultat till slutanvändare för program/chatt-roboten.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/09/2020
ms.openlocfilehash: b09c36f1dce3c394ff24e3e601d773279a6ac510
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102123317"
---
# <a name="best-practices-of-a-qna-maker-knowledge-base"></a>Metod tips för en QnA Maker kunskaps bas

[Livs cykeln för kunskaps bas utveckling](../Concepts/development-lifecycle-knowledge-base.md) hjälper dig att hantera din KB från början till slut. Använd dessa metod tips för att förbättra din kunskaps bas och ge bättre resultat till klient programmet eller chattens robots slutanvändare.

## <a name="extraction"></a>Extrahering

QnA Makers tjänsten förbättrar ständigt de algoritmer som extraherar kring från innehåll och expanderar listan över fil-och HTML-format som stöds. Följ [rikt linjerna](../Concepts/data-sources-and-content.md) för data extrahering baserat på din dokument typ.

I allmänhet bör vanliga frågor och svar vara fristående och inte kombineras med annan information. Produkt handböcker bör ha tydliga rubriker och helst en index sida.

### <a name="configuring-multi-turn"></a>Konfigurera multi-turn

[Skapa din kunskaps bas med att](../how-to/multiturn-conversation.md#create-a-multi-turn-conversation-from-a-documents-structure) extrahering av flera varv har Aktiver ATS. Om din kunskaps bas stöder eller ska ha stöd för fråge hierarkier, kan den här hierarkin extraheras från dokumentet eller skapas när dokumentet har extraherats.

## <a name="creating-good-questions-and-answers"></a>Skapa lämpliga frågor och svar

### <a name="good-questions"></a>Lämpliga frågor

De bästa frågorna är enkla. Överväg att använda nyckel ordet eller frasen för varje fråga och skapa sedan en enkel fråga för nyckel ordet eller frasen.

Lägg till så många alternativa frågor som du behöver men behåll ändringarna enkla. Att lägga till fler ord eller ordföljder som inte är en del av frågans huvudsakliga mål hjälper inte QnA Maker hitta en matchning.


### <a name="add-relevant-alternative-questions"></a>Lägg till relevanta alternativa frågor

Användaren kan ange frågor med antingen ett samtals format `How do I add a toner cartridge to my printer?` eller en nyckelords sökning som `toner cartridge` . Kunskaps basen bör ha båda typer av frågor för att kunna returnera det bästa svaret på rätt sätt. Om du inte är säker på vilka nyckelord en kund anger använder du Application Insights data för att analysera frågor.

### <a name="good-answers"></a>Lämpliga svar

De bästa svaren är enkla svar, men inte för enkla. Använd inte svar som `yes` och `no` . Om ditt svar ska länkas till andra källor eller ger en omfattande upplevelse med medier och länkar, använder du [metadata-märkning](../how-to/edit-knowledge-base.md#add-metadata) för att skilja mellan svar och [skickar sedan frågan](../how-to/metadata-generateanswer-usage.md#generateanswer-request-configuration) med metadata-Taggar i `strictFilters` egenskapen för att få rätt svars version.

|Svar|Uppföljnings anvisningarna|
|--|--|
|Stäng av arbets ytans bärbara dator med strömbrytaren på tangent bordet.|* Viktiga kombinationer av ström spar läge, avstängning och omstart.<br>* Så här kan du starta en bärbar dator på en Surface<br>* Ändra BIOS för en Surface-dator<br>* Skillnader mellan ström spar läge, Stäng av och starta om|
|Kund tjänst är tillgänglig via telefon, Skype och textmeddelande dygnet runt, 24 timmar per dag.|* Kontakt information för försäljning.<br> * Kontor och lagrings platser och timmar för ett personligt besök.<br> * Tillbehör för en Surface-dator.|

## <a name="chit-chat"></a>Chit-Chat
Lägg till en CHI2TEST i roboten för att göra din robot mer konversation och engagerande med låg ansträngning. Du kan enkelt lägga till data uppsättningar för CHI2TEST från fördefinierade personliga objekt när du skapar dina KB och ändra dem när som helst. Lär dig hur du [lägger till en CHI2TEST i din KB](../How-To/chit-chat-knowledge-base.md).

CHI2TEST-chatt stöds på [många språk](../how-to/chit-chat-knowledge-base.md#language-support).

### <a name="choosing-a-personality"></a>Välja en personlighet
CHI2TEST-chatt stöds för flera fördefinierade personliga uppgifter:

|Personlighet |QnA Maker data uppsättnings fil |
|---------|-----|
|Professionell |[qna_chitchat_professional. tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_professional.tsv) |
|Eget |[qna_chitchat_friendly. tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_friendly.tsv) |
|Witty |[qna_chitchat_witty. tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_witty.tsv) |
|Caring |[qna_chitchat_caring. tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_caring.tsv) |
|Entusiastisk |[qna_chitchat_enthusiastic. tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_enthusiastic.tsv) |

Svaren sträcker sig från formell till informell och Irreverent. Du bör välja den personlighet som är närmast anpassad med den ton som du vill använda för din robot. Du kan visa [data uppsättningarna](https://github.com/Microsoft/BotBuilder-PersonalityChat/tree/master/CSharp/Datasets)och välja en som fungerar som en bas för din robot och sedan anpassa svaren.

### <a name="edit-bot-specific-questions"></a>Redigera bot-/regionsspecifika frågor
Det finns vissa robot-/regionsspecifika frågor som är en del av data uppsättningen CHI2TEST och har fyllts i med allmänna svar. Ändra Svaren till bästa möjliga information om bot.

Vi rekommenderar att du gör följande CHI2TEST kring mer information:

* Vem är du?
* Vad kan du göra?
* How old are you?
* Vem skapade dig?
* Hello

### <a name="adding-custom-chit-chat-with-a-metadata-tag"></a>Lägger till anpassad CHI2TEST-chatt med en metadata-tagg

Om du lägger till dina egna CHI2TEST QnA-par, se till att lägga till metadata så att svaren returneras. Namn/värde-paret för metadata är `editorial:chitchat` .

## <a name="searching-for-answers"></a>Söker efter svar

GenerateAnswer-API: et använder både frågor och svar för att söka efter bästa svar på användarens fråga.

### <a name="searching-questions-only-when-answer-is-not-relevant"></a>Sök bara efter frågor när svar inte är relevant

Använd [`RankerType=QuestionOnly`](#choosing-ranker-type) om du inte vill söka efter svar.

Ett exempel på detta är när kunskaps basen är en katalog med akronymer som frågor med fullständigt formulär som svar. Svarets värde kommer inte att hjälpa dig att söka efter lämpligt svar.

## <a name="rankingscoring"></a>Rangordning/Poängsättning
Kontrol lera att du har den bästa användningen av rangordnings funktionerna QnA Maker stöder. På så sätt kan du förbättra sannolikheten för att en specifik användar fråga besvaras med ett lämpligt svar.

### <a name="choosing-a-threshold"></a>Välja ett tröskelvärde

Standard utgångs [poängen](confidence-score.md) som används som tröskelvärde är 0, men du kan [Ändra tröskelvärdet](confidence-score.md#set-threshold) för dina KB utifrån dina behov. Eftersom varje KB är annorlunda bör du testa och välja det tröskelvärde som passar bäst för din KB.

### <a name="choosing-ranker-type"></a>Väljer rangordnings typ
Som standard söker QnA Maker igenom frågor och svar. Om du bara vill söka igenom frågor, för att generera ett svar, använder du `RankerType=QuestionOnly` i inläggs texten i GenerateAnswer-begäran.

### <a name="add-alternate-questions"></a>Lägg till alternativa frågor
[Alternativa frågor](../How-To/edit-knowledge-base.md) förbättrar sannolikheten för en matchning med en användar fråga. Alternativa frågor är användbara när det finns flera sätt på vilka samma fråga kan bli tillfrågad. Detta kan omfatta ändringar i menings strukturen och ord format.

|Ursprunglig fråga|Alternativa frågor|Ändra|
|--|--|--|
|Är parkeringen tillgänglig?|Har du Car-Park?|menings struktur|
 |Hej|Yo<br>Hej!|ord format eller slang|

<a name="use-metadata-filters"></a>

### <a name="use-metadata-tags-to-filter-questions-and-answers"></a>Använd metadata-taggar för att filtrera frågor och svar

[Metadata](../How-To/edit-knowledge-base.md) lägger till möjligheten för ett klient program att veta att det inte ska ta med alla svar utan i stället begränsa resultatet av en användar fråga baserat på metadata-taggar. Kunskaps bas svaret kan variera beroende på taggen metadata, även om frågan är densamma. Till exempel *"Where är parkerings plats"* kan ha ett annat svar om platsen för restaurang grenen är en annan – det vill säga att metadata är *plats: Seattle* respektive *plats: Redmond*.

### <a name="use-synonyms"></a>Använd synonymer
# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil utgåva)](#tab/v1)
Även om det finns stöd för synonymer på det engelska språket använder du Skift läges känsliga ord ändringar via [API: erna](/rest/api/cognitiveservices/qnamaker/alterations/replace) för att lägga till synonymer till nyckelord som tar olika formulär. Synonymer läggs till på QnA Maker service nivå och **delas av alla kunskaps banker i tjänsten**.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker hanterad (för hands version)](#tab/v2)
Även om det finns stöd för synonymer på det engelska språket använder du Skift läges känsliga ord ändringar via [API: erna](/rest/api/cognitiveservices/qnamaker/alterations/replace) för att lägga till synonymer till nyckelord som tar olika formulär. Synonymer i QnA Maker hanterad (för hands version) **läggs till per kunskaps bas**.

|Original ord|Synonymer|
|--|--|
|handlar|köp<br>NET-Banks<br>NET Banks|

---

### <a name="use-distinct-words-to-differentiate-questions"></a>Använd distinkta ord för att särskilja frågor
QnA Makerens rangordning, som matchar en användar fråga med en fråga i kunskaps basen, fungerar bäst om varje fråga behandlar olika behov. Upprepning av samma ord uppsättning mellan frågorna minskar sannolikheten för att rätt svar väljs för en viss användar fråga med dessa ord.

Du kan till exempel ha två separata kring med följande frågor:

|Kring|
|--|
|där är parkerings *platsen*|
|var är ATM- *platsen*|

Eftersom dessa två kring är formulerade med mycket liknande ord, kan den här likheten orsaka mycket liknande Poäng för många användar frågor som är formulerade som  *"där är `<x>` platsen"*. Försök i stället att tydligt särskilja med frågor som  *"Where är parkerings partiet"* och *"var är ATM"*, genom att undvika ord som "plats" som kan finnas i många frågor i din KB.

## <a name="collaborate"></a>Samarbeta
QnA Maker gör det möjligt för användare att samar beta i en kunskaps bas. Användare behöver åtkomst till resurs gruppen för Azure-QnA Maker för att få åtkomst till kunskaps baserna. Vissa organisationer kan vilja ta ut kunskaps bas redigeringen och underhållet och fortfarande kunna skydda åtkomsten till sina Azure-resurser. Den här redigeraren – god kännare modell görs genom att konfigurera två identiska [QNA Maker-tjänster](../How-to/set-up-qnamaker-service-azure.md) i olika prenumerationer och välja en för redigerings test cykeln. När testet är klart överförs kunskaps bas innehållet med en [import-export-](../Tutorials/migrate-knowledge-base.md) process till QNA Maker tjänsten för god kännaren som slutligen publicerar kunskaps basen och uppdaterar slut punkten.



## <a name="active-learning"></a>Aktiv inlärning

[Active Learning](../How-to/use-active-learning.md) gör det bästa sättet att föreslå alternativa frågor när det finns många olika kvalitets-och mängd användarbaserade frågor. Det är viktigt att tillåta att klient programs användar frågor deltar i den aktiva inlärnings feedback-loopen utan censorship. När frågorna föreslås i QnA Maker Portal kan du **[Filtrera efter förslag](../How-To/improve-knowledge-base.md)** och sedan granska och godkänna eller avvisa dessa förslag.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Redigera en kunskapsbas](../How-to/edit-knowledge-base.md)
