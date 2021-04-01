---
title: Fel sökning – QnA Maker
description: Den granskade listan med de vanligaste frågorna om QnA Maker tjänsten hjälper dig att komma igång med tjänsten snabbare och med bättre resultat.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: troubleshooting
ms.date: 11/09/2020
ms.openlocfilehash: 7b7ac20672ee653cbf6d2b82b7a9454c1d742b2c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "102612702"
---
# <a name="troubleshooting-for-qna-maker"></a>Fel sökning för QnA Maker

Den granskade listan med de vanligaste frågorna om QnA Maker tjänsten hjälper dig att komma igång med tjänsten snabbare och med bättre resultat.

<a name="how-to-get-the-qnamaker-service-hostname"></a>

## <a name="manage-predictions"></a>Hantera förutsägelser

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil utgåva)](#tab/v1)

<details>
<summary><b>Hur kan jag förbättra data flödes prestandan för fråga förutsägelser?</b></summary>

**Svar**: problem med data flödes prestanda indikerar att du behöver skala upp för både app service och kognitiv sökning. Överväg att lägga till en replik i Kognitiv sökning för att förbättra prestandan.

Läs mer om [pris nivåer](Concepts/azure-resources.md).
</details>

<details>
<summary><b>Så här hämtar du QnAMaker-tjänstens slut punkt</b></summary>

**Svar**: QnAMaker-tjänstens slut punkt är användbart för fel sökning när du kontaktar QnAMaker-support eller UserVoice. Slut punkten är en URL i det här formuläret: `https://your-resource-name.azurewebsites.net` .

1. Gå till din QnAMaker-tjänst (resurs grupp) i [Azure Portal](https://portal.azure.com)

    ![QnAMaker Azure-resurs grupp i Azure Portal](./media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

1. Välj den App Service som är kopplad till QnA Maker resursen. Normalt är namnen desamma.

     ![Välj QnAMaker App Service](./media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)

1. Slut punkts-URL: en är tillgänglig i översikts avsnittet

    ![QnAMaker-slutpunkt](./media/qnamaker-how-to-troubleshoot/qnamaker-azure-gethostname.png)

</details>

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker hanterad (för hands version)](#tab/v2)

<details>
<summary><b>Hur kan jag förbättra data flödes prestandan för fråga förutsägelser?</b></summary>

**Svar**: problem med data flödes prestanda indikerar att du behöver skala upp kognitiv sökning. Överväg att lägga till en replik i Kognitiv sökning för att förbättra prestandan.

Läs mer om [pris nivåer](Concepts/azure-resources.md).
</details>

---

## <a name="manage-the-knowledge-base"></a>Hantera kunskaps basen

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil utgåva)](#tab/v1)

<details>
<summary><b>Jag har råkat ta bort en del av mitt QnA Maker, vad ska jag göra?</b></summary>

**Svar**: ta inte bort någon av de Azure-tjänster som skapats tillsammans med QNA Maker-resursen, till exempel search eller Web App. Detta är nödvändigt för att QnA Maker ska fungera om du tar bort ett QnA Maker slutar fungera korrekt.

Alla borttagningar är permanenta, inklusive frågor och svars par, filer, URL: er, anpassade frågor och svar, kunskaps banker eller Azure-resurser. Se till att exportera din kunskaps bas från sidan **Inställningar** innan du tar bort någon del av kunskaps basen.

</details>

<details>
<summary><b>Varför extraherar mina URL: er inte svars par för frågor?</b></summary>

**Svar**: det är möjligt att QNA Maker inte kan automatiskt extrahera en del frågor och svar (QNA) från giltiga URL: er för vanliga frågor och svar. I sådana fall kan du klistra in QnA-innehållet i en txt-fil och se om verktyget kan mata in det. Alternativt kan du lägga till innehåll i kunskaps basen på [QNA Maker portalen](https://qnamaker.ai).

</details>

<details>
<summary><b>Hur stor en kunskaps bas kan jag skapa?</b></summary>

**Svar**: kunskaps bas storleken beror på vilken SKU av Azure Search du väljer när du skapar tjänsten QNA Maker. Läs [här](./concepts/azure-resources.md) om du vill ha mer information.

</details>

<details>
<summary><b>Varför kan jag inte se något i list rutan när jag försöker skapa en ny kunskaps bas?</b></summary>

**Svar**: du har inte skapat några QNA Maker tjänster i Azure än. Läs [här](./How-To/set-up-qnamaker-service-azure.md) för att lära dig hur du gör det.

</details>

<details>
<summary><b>Hur gör jag för att dela en kunskaps bas med andra?</b></summary>

**Svar**: delningen fungerar på samma nivå som en QNA Maker tjänst, det vill säga att alla kunskaps banker i tjänsten delas. Läs mer [här](./index.yml) om hur du samarbetar i en kunskaps bas.

</details>

<details>
<summary><b>Kan du dela en kunskaps bas med en deltagare som inte tillhör samma AAD-klient, för att ändra en kunskaps bas?</b></summary>

**Svar**: delning baseras på rollbaserad åtkomst kontroll i Azure (Azure RBAC). Om du kan dela _en_ resurs i Azure med en annan användare kan du också dela QNA Maker.

</details>

<details>
<summary><b>Om du har en App Service-plan med 5 QnAMaker kunskaps Bases. Kan du tilldela läs-och Skriv behörighet till 5 olika användare så att var och en av dem bara har åtkomst till 1 QnAMaker kunskaps bas?</b></summary>

**Svar**: du kan dela en hel QnAMaker-tjänst, inte enskilda kunskaps baser.

</details>

<details>
<summary><b>Hur kan jag ändra standard meddelandet när ingen lämplig matchning hittas?</b></summary>

**Svar**: standard meddelandet är en del av inställningarna i App Service.
- Gå till App Service-resursen i Azure Portal

![qnamaker AppService](./media/qnamaker-faq/qnamaker-resource-list-appservice.png)
- Klicka på **inställnings** alternativet

![qnamaker AppService-inställningar](./media/qnamaker-faq/qnamaker-appservice-settings.png)
- Ändra värdet för **DefaultAnswer** -inställningen
- Starta om App Service

![qnamaker AppService-omstart](./media/qnamaker-faq/qnamaker-appservice-restart.png)


</details>

<details>
<summary><b>Varför hämtas inte min SharePoint-länk?</b></summary>

**Svar**: se [platser för data källor](./concepts/data-sources-and-content.md#data-source-locations) för mer information.

</details>

<details>
<summary><b>Uppdateringarna som jag gjorde i min kunskaps bas avspeglas inte vid publicering. Varför inte?</b></summary>

**Svar**: varje redigerings åtgärd, om i en tabell uppdatering, testning eller inställning, måste sparas innan den kan publiceras. Se till att klicka på knappen **Spara och träna** efter varje redigerings åtgärd.

</details>

<details>
<summary><b>Stöder kunskaps basen omfattande data eller Multimedia?</b></summary>

**Svar**:

#### <a name="multimedia-auto-extraction-for-files-and-urls"></a>Automatisk extrahering av multimedia för filer och URL: er

* URL-begränsad HTML-till-markdown konverterings funktion.
* Filer – stöds inte

#### <a name="answer-text-in-markdown"></a>Svars text i markdown
När QnA-par finns i kunskaps basen kan du redigera svarets markdown-text för att inkludera länkar till mediet som är tillgängliga från offentliga URL: er.


</details>

<details>
<summary><b>Stöder QnA Maker andra språk än engelska?</b></summary>

**Svar**: se mer information om [vilka språk som stöds](./overview/language-support.md).

Om du har innehåll från flera språk, måste du skapa en separat tjänst för varje språk.

</details>

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker hanterad (för hands version)](#tab/v2)

<details>
<summary><b>Varför extraherar mina URL: er inte svars par för frågor?</b></summary>

**Svar**: det är möjligt att QNA Maker inte kan automatiskt extrahera en del frågor och svar (QNA) från giltiga URL: er för vanliga frågor och svar. I sådana fall kan du klistra in QnA-innehållet i en txt-fil och se om verktyget kan mata in det. Alternativt kan du lägga till innehåll i kunskaps basen på [QNA Maker portalen](https://qnamaker.ai).

</details>

<details>
<summary><b>Hur stor en kunskaps bas kan jag skapa?</b></summary>

**Svar**: kunskaps bas storleken beror på vilken SKU av Azure Search du väljer när du skapar tjänsten QNA Maker. Läs [här](./concepts/azure-resources.md) om du vill ha mer information.

</details>

<details>
<summary><b>Varför kan jag inte se något i list rutan när jag försöker skapa en ny kunskaps bas?</b></summary>

**Svar**: du har inte skapat några QNA Maker tjänster i Azure än. Läs [här](./How-To/set-up-qnamaker-service-azure.md) för att lära dig hur du gör det.

</details>

<details>
<summary><b>Hur gör jag för att dela en kunskaps bas med andra?</b></summary>

**Svar**: delningen fungerar på samma nivå som en QNA Maker tjänst, det vill säga att alla kunskaps banker i tjänsten delas. Läs mer [här](./index.yml) om hur du samarbetar i en kunskaps bas.

</details>

<details>
<summary><b>Kan du dela en kunskaps bas med en deltagare som inte finns i samma Azure Active Directory klient, för att ändra en kunskaps bas?</b></summary>

**Svar**: delning baseras på rollbaserad åtkomst kontroll i Azure (Azure RBAC). Om du kan dela _en_ resurs i Azure med en annan användare kan du också dela QNA Maker.

</details>

<details>
<summary><b>Kan du tilldela läs-och Skriv behörighet till 5 olika användare så att var och en av dem bara har åtkomst till 1 QnAMaker kunskaps bas?</b></summary>

**Svar**: du kan dela en hel QnAMaker-tjänst, inte enskilda kunskaps baser.

</details>

<details>
<summary><b>Varför hämtas inte min SharePoint-länk?</b></summary>

**Svar**: se [platser för data källor](./concepts/data-sources-and-content.md#data-source-locations) för mer information.

</details>

<details>
<summary><b>Uppdateringarna som jag gjorde i min kunskaps bas avspeglas inte vid publicering. Varför inte?</b></summary>

**Svar**: varje redigerings åtgärd, om i en tabell uppdatering, testning eller inställning, måste sparas innan den kan publiceras. Se till att klicka på knappen **Spara och träna** efter varje redigerings åtgärd.

</details>

<details>
<summary><b>Stöder kunskaps basen omfattande data eller Multimedia?</b></summary>

**Svar**:

#### <a name="multimedia-auto-extraction-for-files-and-urls"></a>Automatisk extrahering av multimedia för filer och URL: er

* URL-begränsad HTML-till-markdown konverterings funktion.
* Filer – stöds inte

#### <a name="answer-text-in-markdown"></a>Svars text i markdown
När QnA-par finns i kunskaps basen kan du redigera svarets markdown-text för att inkludera länkar till mediet som är tillgängliga från offentliga URL: er.


</details>

<details>
<summary><b>Stöder QnA Maker andra språk än engelska?</b></summary>

**Svar**: se mer information om [vilka språk som stöds](./overview/language-support.md).

Om du har innehåll från flera språk, måste du skapa en separat tjänst för varje språk.

</details>

---

## <a name="manage-service"></a>Hantera tjänst

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil utgåva)](#tab/v1)

<details>
<summary><b>När ska jag starta om min app service?</b></summary>

**Svar**: uppdatera App Service när varnings ikonen är bredvid version svärdet för kunskaps basen i tabellen **slut punkts nycklar** på [sidan](https://www.qnamaker.ai/UserSettings) **användar inställningar** .

</details>

<details>
<summary><b>Jag har tagit bort min befintliga search-tjänst. Hur kan jag åtgärda detta?</b></summary>

**Svar**: om du tar bort ett Azure kognitiv sökning-index är åtgärden slutgiltig och indexet kan inte återställas.

</details>

<details>
<summary><b>Jag har tagit bort mitt `testkb` index i min search-tjänst. Hur kan jag åtgärda detta?</b></summary>

**Svar**: dina gamla data kan inte återställas. Skapa en ny QnA Maker resurs och skapa din kunskaps bas igen.

</details>

<details>
<summary><b>När ska jag uppdatera mina slut punkts nycklar?</b></summary>

**Svar**: uppdatera dina slut punkts nycklar om du misstänker att de har komprometterats.

</details>

<details>
<summary><b>Kan jag använda samma Azure Kognitiv sökning-resurs för kunskaps banker med flera språk?</b></summary>

**Svar**: användaren måste skapa en QNA Maker resurs för varje språk för att kunna använda flera språk och flera kunskaps baser. Då skapas en separat Azure Search-tjänst per språk. Att blanda olika språk kunskaps baser i en enda Azure Search-tjänst leder till försämrade resultat.

</details>

<details>
<summary><b>Hur kan jag ändra namnet på den Azure Kognitiv sökning-resurs som används av QnA Maker?</b></summary>

**Svar**: namnet på Azure kognitiv sökning-resursen är QNA Maker resurs namnet med vissa slumpmässiga bokstäver sist i slutet. Detta gör det svårt att skilja mellan flera Sök resurser för QnA Maker. Skapa en separat Sök tjänst (namnge den på det sätt som du vill) och Anslut den till din QnA-tjänst. Stegen liknar de steg du behöver utföra för att [uppgradera en Azure-sökning](How-To/set-up-qnamaker-service-azure.md#upgrade-the-azure-cognitive-search-service).

</details>

<details>
<summary><b>När QnA Maker returnerar `Runtime core is not initialized,` jag det?</b></summary>

**Svar**: disk utrymmet för App Service kan vara fullt. Steg för att åtgärda disk utrymmet:

1. I [Azure Portal](https://portal.azure.com)väljer du QNA Maker app service och stoppar sedan tjänsten.
1. När du fortfarande är i App Service väljer du **utvecklingsverktyg**, sedan **Avancerade verktyg** och sedan **gå**. Då öppnas ett nytt fönster i webbläsaren.
1. Välj **fel söknings konsolen** och sedan **cmd** för att öppna ett kommando rads verktyg.
1. Navigera till _platsen/wwwroot/data/QnAMaker/_ Directory.
1. Ta bort alla mappar vars namn börjar med `rd` .

    **Ta inte bort** följande:

    * KbIdToRankerMappings.txt fil
    * EndpointSettings.jspå fil
    * EndpointKeys-mapp

1. Starta App Service.
1. Kom åt din kunskaps bas för att kontrol lera att den fungerar nu.

</details>
<details>
<summary><b>Varför fungerar inte mitt Application Insights?</b></summary>

**Svar**: kontrol lera och uppdatera stegen nedan för att åtgärda problemet:

1. I App Service-> inställnings grupp-> konfigurations avsnittet-> program inställningar-> namn "UserAppInsightsKey"-parametrarna har kon figurer ATS korrekt och angetts till respektive Application Insights-översikt ("Instrumentation Key") GUID. 

1. I App Service > Settings Group-> "Application Insights"-> se till att App Insights är aktiverat och är anslutet till respektive Application Insights-resurs.

</details>

<details>
<summary><b>Mitt Application Insights är aktiverat men varför fungerar det inte korrekt?</b></summary>

**Svar**: Följ anvisningarna nedan: 

1.  Kopiera värdet för "" APPINSIGHTS_INSTRUMENTATIONKEY "namn" till "UserAppInsightsKey" genom att åsidosätta om det finns något värde som redan finns där. 

1.  Om nyckeln "UserAppInsightsKey" inte finns i appinställningar lägger du till en ny nyckel med det namnet och kopierar värdet.

1.  Spara det och starta om App Service automatiskt. Detta bör lösa problemet. 

</details>

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker hanterad (för hands version)](#tab/v2)


<details>
<summary><b>Jag har tagit bort min befintliga search-tjänst. Hur kan jag åtgärda detta?</b></summary>

**Svar**: om du tar bort ett Azure kognitiv sökning-index är åtgärden slutgiltig och indexet kan inte återställas.

</details>

<details>
<summary><b>Jag har tagit bort mitt `testkb` index i min search-tjänst. Hur kan jag åtgärda detta?</b></summary>

**Svar**: dina gamla data kan inte återställas. Skapa en ny QnA Maker resurs och skapa din kunskaps bas igen.

</details>

<details>
<summary><b>Kan jag använda samma Azure Kognitiv sökning-resurs för kunskaps banker med flera språk?</b></summary>

**Svar**: användaren måste skapa en QNA Maker resurs för varje språk för att kunna använda flera språk och flera kunskaps baser. Då skapas en separat Azure Search-tjänst per språk. Att blanda olika språk kunskaps baser i en enda Azure Search-tjänst leder till försämrade resultat.

</details>

<details>
<summary><b>Hur kan jag ändra namnet på den Azure Kognitiv sökning-resurs som används av QnA Maker?</b></summary>

**Svar**: namnet på Azure kognitiv sökning-resursen är QNA Maker resurs namnet med vissa slumpmässiga bokstäver sist i slutet. Detta gör det svårt att skilja mellan flera Sök resurser för QnA Maker. Skapa en separat Sök tjänst (namnge den på det sätt som du vill) och Anslut den till din QnA-tjänst. Stegen liknar de steg du behöver utföra för att [uppgradera en Azure-sökning](How-To/set-up-qnamaker-service-azure.md#upgrade-the-azure-cognitive-search-service).

</details>

---

## <a name="integrate-with-other-services-including-bots"></a>Integrera med andra tjänster, inklusive robotar

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil utgåva)](#tab/v1)

<details>
<summary><b>Behöver jag använda bot Framework för att kunna använda QnA Maker?</b></summary>

**Svar**: Nej, du behöver inte använda [Bot Framework](https://github.com/Microsoft/botbuilder-dotnet) med QNA Maker. QnA Maker erbjuds dock som en av flera mallar i [Azure bot service](/azure/bot-service/). Bot service möjliggör snabb intelligent bot-utveckling via Microsoft bot Framework och körs i en server som är mindre miljö.

</details>

<details>
<summary><b>Hur gör jag för att skapa en ny robot med QnA Maker?</b></summary>

**Svar**: Följ anvisningarna i [den här](./Quickstarts/create-publish-knowledge-base.md) dokumentationen för att skapa din robot med Azure bot service.

</details>

<details>
<summary><b>Hur gör jag för att använda en annan kunskaps bas med en befintlig Azure bot-tjänst?</b></summary>

**Svar**: du måste ha följande information om din kunskaps bas:

* Kunskaps bas-ID.
* Det anpassade under domän namnet för den publicerade slut punkten i kunskaps basen, känt som `host` , hittades på sidan **Inställningar** när du har publicerat.
* Kunskaps basens publicerade slut punkts nyckel finns på **inställnings** sidan när du har publicerat.

Med den här informationen går du till appens robots App Service i Azure Portal. Under **Inställningar-> konfiguration – inställningar för > program**, ändra dessa värden.

Kunskaps basens slut punkts nyckel är märkt `QnAAuthkey` i ABS-tjänsten.

</details>

<details>
<summary><b>Kan två eller flera klient program dela en kunskaps bas?</b></summary>

**Svar**: Ja, kunskaps basen kan frågas från valfritt antal klienter. Om svaret från kunskaps basen verkar vara långsamt eller tids gräns, bör du överväga att uppgradera tjänst nivån för App Service som är associerad med kunskaps basen.

</details>

<details>
<summary><b>Hur gör jag för att du bädda in QnA Makers tjänsten på min webbplats?</b></summary>

**Svar**: Följ de här stegen för att bädda in QNA Maker tjänsten som en webb chatt kontroll på din webbplats:

1. Skapa din vanliga bot-robot genom att följa anvisningarna [här](./Quickstarts/create-publish-knowledge-base.md).
2. Aktivera webbchatten genom att följa stegen [här](/azure/bot-service/bot-service-channel-connect-webchat)

</details>

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker hanterad (för hands version)](#tab/v2)


<details>
<summary><b>Behöver jag använda bot Framework för att kunna använda QnA Maker?</b></summary>

**Svar**: Nej, du behöver inte använda [Bot Framework](https://github.com/Microsoft/botbuilder-dotnet) med QNA Maker. QnA Maker erbjuds dock som en av flera mallar i [Azure bot service](/azure/bot-service/). Bot service möjliggör snabb intelligent bot-utveckling via Microsoft bot Framework och körs i en server som är mindre miljö.

</details>

<details>
<summary><b>Hur gör jag för att skapa en ny robot med QnA Maker?</b></summary>

**Svar**: Följ anvisningarna i [den här](./Quickstarts/create-publish-knowledge-base.md) dokumentationen för att skapa din robot med Azure bot service.

</details>

<details>
<summary><b>Hur gör jag för att använda en annan kunskaps bas med en befintlig Azure bot-tjänst?</b></summary>

**Svar**: du måste ha följande information om din kunskaps bas:

* Kunskaps bas-ID.
* Det anpassade under domän namnet för den publicerade slut punkten i kunskaps basen, känt som `host` , hittades på sidan **Inställningar** när du har publicerat.
* Kunskaps basens publicerade slut punkts nyckel finns på **inställnings** sidan när du har publicerat.

Med den här informationen går du till appens robots App Service i Azure Portal. Under **Inställningar-> konfiguration – inställningar för > program**, ändra dessa värden.

Kunskaps basens slut punkts nyckel är märkt `QnAAuthkey` i ABS-tjänsten.

</details>

<details>
<summary><b>Kan två eller flera klient program dela en kunskaps bas?</b></summary>

**Svar**: Ja, kunskaps basen kan frågas från valfritt antal klienter. Om svaret från kunskaps basen verkar vara långsamt eller tids gräns, bör du överväga att uppgradera tjänst nivån för App Service som är associerad med kunskaps basen.

</details>

<details>
<summary><b>Hur gör jag för att du bädda in QnA Makers tjänsten på min webbplats?</b></summary>

**Svar**: Följ de här stegen för att bädda in QNA Maker tjänsten som en webb chatt kontroll på din webbplats:

1. Skapa din vanliga bot-robot genom att följa anvisningarna [här](./Quickstarts/create-publish-knowledge-base.md).
2. Aktivera webbchatten genom att följa stegen [här](/azure/bot-service/bot-service-channel-connect-webchat)

---

## <a name="data-storage"></a>Datalagring

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil utgåva)](#tab/v1)

<details>
<summary><b>Vilka data lagras och var lagras de?</b></summary>

**Svar**:

När du skapar din QnA Maker-tjänst valde du en Azure-region. Dina kunskaps baser och loggfiler lagras i den här regionen.

</details>

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker hanterad (för hands version)](#tab/v2)

<details>
<summary><b>Vilka data lagras och var lagras de?</b></summary>

**Svar**:

När du skapar din QnA Maker-tjänst valde du en Azure-region. Dina kunskaps baser och loggfiler lagras i den här regionen.

</details>

---
