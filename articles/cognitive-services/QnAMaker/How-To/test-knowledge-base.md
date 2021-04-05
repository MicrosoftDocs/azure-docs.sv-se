---
title: Så här testar du en kunskaps bas – QnA Maker
description: Att testa din QnA Maker kunskaps bas är en viktig del av en iterativ process för att förbättra noggrannheten i svaren som returneras. Du kan testa kunskaps basen via ett förbättrat chatt-gränssnitt som gör det möjligt att redigera.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/09/2020
ms.openlocfilehash: 273548ec095ce04772438a2d732b914d80d976cc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "96353161"
---
# <a name="test-your-knowledge-base-in-qna-maker"></a>Testa din kunskaps bas i QnA Maker

Att testa din QnA Maker kunskaps bas är en viktig del av en iterativ process för att förbättra noggrannheten i svaren som returneras. Du kan testa kunskaps basen via ett förbättrat chatt-gränssnitt som gör det möjligt att redigera.

## <a name="interactively-test-in-qna-maker-portal"></a>Testa interaktivt i QnA Maker Portal

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil utgåva)](#tab/v1)

1. Gå till kunskaps basen genom att välja namnet på sidan **Mina kunskaps baser** .
1. Välj **testa** i programmets övre panel för att komma åt test-bildspel-panelen.
1. Ange en fråga i text rutan och välj RETUR.
1. Det bäst matchade svaret från kunskaps basen returneras som svar.

### <a name="clear-test-panel"></a>Rensa test panelen

Om du vill rensa alla angivna test frågor och resultat från test konsolen väljer du **börja** i det övre vänstra hörnet på test panelen.

### <a name="close-test-panel"></a>Stäng test panelen

Välj knappen **testa** igen för att stänga test panelen. Medan test panelen är öppen kan du inte redigera innehållet i kunskaps basen.

### <a name="inspect-score"></a>Granska Poäng

Du har granskat informationen om test resultatet i inspektions panelen.

1.  Med test-out-panelen öppen väljer du **Granska** för mer information om svaret.

    ![Inspektera svar](../media/qnamaker-how-to-test-knowledge-bases/inspect.png)

2.  Inspektions panelen visas. Panelen innehåller den översta bedömnings avsikten samt alla identifierade entiteter. Panelen visar resultatet av den valda uttryck.

### <a name="correct-the-top-scoring-answer"></a>Korrigera det främsta bedömnings svaret

Om det översta bedömnings svaret är felaktigt väljer du rätt svar i listan och väljer **Spara och träna**.

![Korrigera det främsta bedömnings svaret](../media/qnamaker-how-to-test-knowledge-bases/choose-answer.png)

### <a name="add-alternate-questions"></a>Lägg till alternativa frågor

Du kan lägga till alternativa formulär för en fråga till ett angivet svar. Skriv de alternativa svaren i text rutan och välj RETUR för att lägga till dem. Välj **Spara och träna** för att lagra uppdateringarna.

![Lägg till alternativa frågor](../media/qnamaker-how-to-test-knowledge-bases/add-alternate-question.png)

### <a name="add-a-new-answer"></a>Lägg till ett nytt svar

Du kan lägga till ett nytt svar om något av de befintliga svaren som matchades är felaktigt eller om svaret inte finns i kunskaps basen (ingen lämplig matchning hittades i KB).

Längst ned i listan med svar använder du text rutan för att ange ett nytt svar och trycker på RETUR för att lägga till den.

Välj **Spara och träna** för att spara det här svaret. Ett nytt fråge-/svar-par har nu lagts till i din kunskaps bas.

> [!NOTE]
> Alla redigeringar av kunskaps basen sparas bara när du trycker på knappen **Spara och träna** .

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker hanterad (för hands version)](#tab/v2)

1. Gå till kunskaps basen genom att välja namnet på sidan **Mina kunskaps baser** .
1. Välj **testa** i programmets övre panel för att komma åt test-bildspel-panelen. 
1. En kryss ruta visas i det övre **visnings kort svaret**, som är markerat som standard. Det här alternativet används för att aktivera identifiering av MRC-baserade svars intervall i test panelen. 
1. Ange en fråga i text rutan och välj RETUR. 
1. Om det finns ett exakt svars-/kort svar i svars passage för varje fråga, tillsammans med bäst matchade svars passage, som finns i kunskaps basen, kommer du även att ha ett kort svar på din fråga.
    ![Hanterat aktiverat test fönster](../media/qnamaker-how-to-test-knowledge-bases/test-pane-with-managed-detail.png)
1. Om du avmarkerar det **korta visnings kortets svar** returneras bara det bästa matchade svars resultatet från kunskaps basen som svar.

### <a name="clear-test-panel"></a>Rensa test panelen

Om du vill rensa alla angivna test frågor och resultat från test konsolen väljer du **börja** i det övre vänstra hörnet på test panelen.

### <a name="close-test-panel"></a>Stäng test panelen

Välj knappen **testa** igen för att stänga test panelen. Medan test panelen är öppen kan du inte redigera innehållet i kunskaps basen.

### <a name="inspect-score"></a>Granska Poäng

Du har granskat informationen om test resultatet i inspektions panelen.

1.  Med test-out-panelen öppen väljer du **Granska** för mer information om svaret.

    ![Granska svar för för hands version](../media/qnamaker-how-to-test-knowledge-bases/inspect-with-managed.png)

2.  Inspektions panelen visas. Panelen innehåller den översta bedömnings avsikten samt alla identifierade entiteter. Panelen visar resultatet av den valda uttryck.
3. Panelen visar förtroende poängen för svars passage tillsammans med poängen för svars intervallet upptäckt.

### <a name="correct-the-top-scoring-answer"></a>Korrigera det främsta bedömnings svaret

Om det översta bedömnings svaret är felaktigt väljer du rätt svar i listan och väljer **Spara och träna**.

![Korrigera den främsta för hands versionen av bedömnings svar](../media/qnamaker-how-to-test-knowledge-bases/choose-answer-managed.png)

### <a name="add-alternate-questions"></a>Lägg till alternativa frågor

Du kan lägga till alternativa formulär för en fråga till ett angivet svar. Skriv de alternativa svaren i text rutan och välj RETUR för att lägga till dem. Välj **Spara och träna** för att lagra uppdateringarna.

![Lägg till förhands granskning av alternativa frågor](../media/qnamaker-how-to-test-knowledge-bases/add-alternate-question-with-managed.png)

### <a name="add-a-new-answer"></a>Lägg till ett nytt svar

Du kan lägga till ett nytt svar om något av de befintliga svaren som matchades är felaktigt eller om svaret inte finns i kunskaps basen (ingen lämplig matchning hittades i KB).

Längst ned i listan med svar använder du text rutan för att ange ett nytt svar och trycker på RETUR för att lägga till den.

Välj **Spara och träna** för att spara det här svaret. Ett nytt fråge-/svar-par har nu lagts till i din kunskaps bas.

---

### <a name="test-the-published-knowledge-base"></a>Testa den publicerade kunskaps basen

Du kan testa den publicerade versionen av kunskaps basen i test fönstret. När du har publicerat KB väljer du rutan **publicerad KB** och skickar en fråga för att få resultat från den publicerade KB.

![Testa mot en publicerad KB](../media/qnamaker-how-to-test-knowledge-bases/test-against-published-knowledge-base.png)

## <a name="batch-test-with-tool"></a>Batch-test med verktyget

Använd batch-testverktyget när du vill:

* Fastställ bästa svar och Poäng för en uppsättning frågor
* verifiera förväntat svar för uppsättning av frågor

### <a name="prerequisites"></a>Förutsättningar

* Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/cognitive-services/)
* [Skapa antingen en QNA Maker tjänst](../Quickstarts/create-publish-knowledge-base.md) eller Använd en befintlig tjänst som använder det engelska språket.
* Ladda ned [exempel `.docx` filen med flera turn](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/multi-turn.docx)
* Hämta [batch-testverktyget](https://aka.ms/qnamakerbatchtestingtool), extrahera den körbara filen från `.zip` filen.

### <a name="sign-into-qna-maker-portal"></a>Logga in på QnA Maker Portal

[Logga](https://www.qnamaker.ai/) in på QNA Maker Portal.

### <a name="create-a-new-knowledge-base-from-the-multi-turn-sampledocx-file"></a>Skapa en ny kunskaps bas från sample.docx-filen med flera turn

1. Välj **skapa en kunskaps bas** i verktygsfältet.
1. Hoppa över **steg 1** eftersom du redan bör ha en QNA Maker resurs, gå vidare till **steg 2** för att välja din befintliga resursinformation:
    * Azure Active Directory-ID
    * Namn på Azure-prenumeration
    * Namn på Azure QnA-tjänsten
    * Språk – det engelska språket
1. Ange namnet `Multi-turn batch test quickstart` som namn på din kunskaps bas.

1. I **steg 4** konfigurerar du inställningarna med följande tabell:

    |Inställning|Värde|
    |--|--|
    |**Aktivera extrahering av flera adresser från URL: er, PDF-eller docx-filer.**|Markerad|
    |**Standard svars text**| `Batch test - default answer not found.`|
    |**+ Lägg till fil**|Välj den nedladdade `.docx` fil listan i kraven.|
    |**Chit-chat**|Välj **Professional**|

1. I **steg 5** väljer **du skapa din KB**.

    När processen har slutförts visas den redigerbara kunskaps basen i portalen.

### <a name="save-train-and-publish-knowledge-base"></a>Spara, träna och publicera kunskaps bas

1. Spara kunskaps basen genom att välja **Spara och träna** från verktygsfältet.
1. Välj **publicera** från verktygsfältet och välj sedan **publicera** igen för att publicera kunskaps basen. Publicering gör kunskaps basen tillgänglig för frågor från en offentlig URL-slutpunkt. När publiceringen är klar sparar du värd-URL och information om slut punkts nyckel som visas på **publicerings** sidan.

    |Obligatoriska data| Exempel|
    |--|--|
    |Publicerad värd|`https://YOUR-RESOURCE-NAME.azurewebsites.net`|
    |Publicerad nyckel|`XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX` (32 tecken sträng visas efter `Endpoint` )|
    |App-ID|`xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` (36 tecken sträng visas som en del av `POST` ) |

### <a name="create-batch-test-file-with-question-ids"></a>Skapa batch-testfilen med fråga-ID

För att kunna använda batch-testverktyget skapar du en fil med namnet `batch-test-data-1.tsv` med en text redigerare. Filen ska vara i UTF-8-format och måste ha följande kolumner avgränsade med en flik.

|Fält för TSV-indatafiler|Kommentarer|Exempel|
|--|--|--|
|Kunskaps bas-ID|Ditt kunskaps bas-ID finns på publicerings sidan. Testa flera kunskaps banker i samma tjänst samtidigt i en enda fil med hjälp av olika kunskaps bas-ID: n i en enda fil.|`xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` (36 tecken sträng visas som en del av `POST` ) |
|Fråga|Den frågetext som en användare skulle ange. 1 000-Character max.|`How do I sign out?`|
|Metadatataggar|valfri|`topic:power` använder `key:value` formatet|
|Övre parameter|valfri|`25`|
|Förväntat svars-ID|valfri|`13`|

I den här kunskaps basen lägger du till tre rader av bara de två obligatoriska kolumnerna i filen. Den första kolumnen är ditt kunskaps bas-ID och den andra kolumnen bör vara följande lista över frågor:

|Kolumn 2 – frågor|
|--|
|`Use Windows Hello to sign in`|
|`Charge your Surface Pro 4`|
|`Get to know Windows 10`|

Dessa frågor är den exakta orda lydelsen från kunskaps basen och ska returnera 100 som konfidens resultat.

Lägg sedan till några frågor, liknande dessa frågor, men inte exakt samma på tre rader, med samma kunskaps bas-ID:

|Kolumn 2 – frågor|
|--|
|`What is Windows Hello?`|
|`How do I charge the laptop?`|
|`What features are in Windows 10?`|

> [!CAUTION]
> Se till att varje kolumn är avgränsad med en tabb-avgränsare. Inledande eller avslutande blank steg läggs till i kolumn data och gör att programmet returnerar undantag när typen eller storleken är felaktig.

Batch-testfilen när den öppnas i Excel ser ut som följande bild. Kunskaps bas-ID: t har ersatts av `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` säkerhet. Se till att kolumnen visar ditt kunskaps bas-ID för ditt eget batch-test.

> [!div class="mx-imgBorder"]
> ![Mata in den första versionen av. tsv-filen från batch-test](../media/batch-test/batch-test-1-input.png)

### <a name="test-the-batch-file"></a>Testa kommando filen

Kör batch testing-programmet med följande CLI-format på kommando raden.

Ersätt `YOUR-RESOURCE-NAME` och `ENDPOINT-KEY` med dina egna värden för tjänst namn och slut punkts nyckel. Dessa värden finns på sidan **Inställningar** på QNA Maker-portalen.

```console
batchtesting.exe batch-test-data-1.tsv https://YOUR-RESOURCE-NAME.azurewebsites.net ENDPOINT-KEY out.tsv
```
Testet slutförs och `out.tsv` filen skapas:

> [!div class="mx-imgBorder"]
> ![Mata ut den första versionen av. tsv-filen från batch-test](../media/batch-test/batch-test-1-output.png)

Kunskaps bas-ID: t har ersatts av `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` säkerhet. För ditt eget batch-test visar kolumnen ditt kunskaps bas-ID.

Test resultatet av konfidens poängen, i den fjärde kolumnen, visar de tre viktigaste frågorna returnerade en poäng på 100 som förväntat eftersom varje fråga är exakt samma som den visas i kunskaps basen. De tre senaste frågorna, med en ny formulering av frågan, returnerar inte 100 som säkerhets poäng. För att öka poängen både för testet och användarna måste du lägga till fler alternativa frågor i kunskaps basen.

### <a name="testing-with-the-optional-fields"></a>Testa med valfria fält

När du förstår formatet och processen kan du generera en testfil för att köra mot din kunskaps bas från en data källa, t. ex. från Chat-loggar.

Eftersom data källan och processen automatiseras, kan test filen köras flera gånger med olika inställningar för att fastställa korrekta värden.

Om du till exempel har en chatt-logg och vill bestämma vilken Chat-text som ska användas för vilka metadatafält, skapar du en test fil och anger metadata-fälten för varje rad. Kör testet och granska sedan de rader som matchar metadata. I allmänhet ska matchningarna vara positiva, men du bör granska resultaten för falska positiva identifieringar. Ett falskt positivt värde är en rad som matchar metadata, men som baseras på texten, den ska inte matcha.

### <a name="using-optional-fields-in-the-input-batch-test-file"></a>Använda valfria fält i test filen för indata-batch

Använd följande diagram för att förstå hur du hittar fältvärdena för valfria data.

|Kolumn nummer|Valfri kolumn|Dataplats|
|--|--|--|
|3|metadata|Exportera befintlig kunskaps bas för befintliga `key:value` par.|
|4|top|Standardvärdet `25` rekommenderas.|
|5|ID för fråga och svars uppsättning|Exportera befintlig kunskaps bas för ID-värden. Observera också att ID: n returnerades i utdatafilen.|

### <a name="add-metadata-to-the-knowledge-base"></a>Lägg till metadata i kunskaps basen

1. På sidan **redigera** i QNA-portalen lägger du till metadata för `topic:power` följande frågor:

    |Frågor|
    |--|
    |Debitera din yta Pro 4|
    |Kontrol lera batteri nivån|

    Två QnA-par har den inställda metadata.

    > [!TIP]
    > Exportera kunskaps basen om du vill se metadata och QnA-ID för varje uppsättning. Välj sidan **Inställningar** och välj sedan **Exportera** som en `.xls` fil. Hitta den här hämtade filen och öppna med Excel-granska för metadata och ID.

1. Välj **Spara och träna**, välj sedan **publicerings** sidan och välj sedan knappen **publicera** . De här åtgärderna gör ändringen tillgänglig för batch-testet. Hämta kunskaps basen från sidan **Inställningar** .

    Den hämtade filen har rätt format för metadata och korrekt fråge-och svars uppsättnings-ID. Använd de här fälten i nästa avsnitt

    > [!div class="mx-imgBorder"]
    > ![Exporterad kunskaps bas med metadata](../media/batch-test/exported-knowledge-base-with-metadata.png)

### <a name="create-a-second-batch-test"></a>Skapa ett andra batch-test

Det finns två huvud scenarier för batch-testning:
* **Bearbeta chatt-loggfiler** – Bestäm det främsta svaret för en tidigare osett-fråga – den vanligaste situationen är när du behöver bearbeta logg filen för frågor, till exempel en chatts användar frågor. Skapa ett test av en batch-fil med endast de kolumner som krävs. Testet returnerar det främsta svaret för varje fråga. Det innebär inte att det är det högsta svaret är rätt svar. När du har slutfört testet går du vidare till verifierings testet.
* **Verifierings test** – verifiera förväntat svar. Det här testet kräver att alla frågor och matchar förväntade svar i batch-testet har verifierats. Detta kan kräva viss manuell process.

Följande procedur förutsätter att scenariot är att bearbeta chatt loggar med

1. Skapa en ny batch-testfil för att inkludera valfria data `batch-test-data-2.tsv` . Lägg till de sex raderna från den ursprungliga batch-testfilen och Lägg sedan till ID för metadata, topp och QnA-par för varje rad.

    För att simulera den automatiserade processen med att kontrol lera ny text från Chat-loggar mot kunskaps basen ställer du in metadata för varje kolumn till samma värde: `topic:power` .

    > [!div class="mx-imgBorder"]
    > ![Mata in en annan version av. tsv-filen från batch-test](../media/batch-test/batch-test-2-input.png)

1. Kör testet igen och ändra namnen på indata-och utdatafiler för att ange att det är det andra testet.

    > [!div class="mx-imgBorder"]
    > ![Mata ut den andra versionen av. tsv-filen från batch-test](../media/batch-test/batch-test-2-output.png)

### <a name="test-results-and-an-automated-test-system"></a>Test resultat och ett automatiserat test system

Den här test utdatafilen kan parsas som en del av en automatiserad kontinuerlig test-pipeline.

Den här test utmatningen bör läsas som: varje rad filtreras med metadata och eftersom varje rad inte matchar metadata i kunskaps basen returnerades standard svaret för de icke-matchande raderna ("ingen lämplig matchning hittades i KB"). För de rader som var identiska returnerades QnA-ID och poäng.

Alla rader returnerade etiketten fel eftersom ingen rad matchade svars-ID: t förväntades.

Du bör kunna se med de här resultaten att du kan ta en chatt-logg och använda texten som fråga för varje rad. Om du inte vet något om data kan du få mer information om de data som du sedan kan använda för att flytta framåt:

* Meta-data
* QnA-ID
* poäng

Var det en bra idé att filtrera med meta-data för testet? Ja och nej. Test systemet bör skapa testfiler för varje Meta-Data-par samt ett test utan meta-datapar.

### <a name="clean-up-resources"></a>Rensa resurser

Om du inte kommer att fortsätta att testa kunskaps basen tar du bort batch-filverktyget och testfilerna.

Om du inte kommer att fortsätta att använda den här kunskaps basen tar du bort kunskaps basen med följande steg:

1. I QnA Maker-portalen väljer du **Mina kunskaps banker** på den översta menyn.
1. I listan över kunskaps baser väljer du ikonen **ta bort** på raden i kunskaps basen för den här snabb starten.

[Referens dokumentation om verktyget](../reference-tsv-format-batch-testing.md) inkluderar:

* kommando rads exemplet för verktyget
* formatet för TSV-och utdatafiler

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Publicera en kunskapsbas](../quickstarts/create-publish-knowledge-base.md)