---
title: Träna och distribuera en Custom Speech modell med tal tjänster
titleSuffix: Azure Cognitive Services
description: Lär dig hur du tränar och distribuerar Custom Speech modeller. Träna en tal-till-text-modell kan förbättra igenkännings precisionen för Microsofts bas linje modell eller en för anpassad modell.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/12/2021
ms.author: trbye
ms.openlocfilehash: 4da93503c32e380adb82028e7c5e11dddb247d6f
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100373376"
---
# <a name="train-and-deploy-a-custom-speech-model"></a>Träna och distribuera en Custom Speech-modell

I den här artikeln får du lära dig hur du tränar och distribuerar Custom Speech modeller. Träna en tal-till-text-modell kan förbättra igenkännings precisionen för Microsofts bas linje modell. Du använder Human-märkta avskrifter och relaterad text för att träna en modell. Dessa data uppsättningar, tillsammans med tidigare överförda ljud data, används för att förfina och träna tal-till-text-modellen.

## <a name="use-training-to-resolve-accuracy-problems"></a>Använd utbildning för att lösa problem med precision

Om du stöter på igenkännings problem med en bas modell kan du använda medmärkta avskrifter och relaterade data för att träna en anpassad modell och hjälpa till att förbättra noggrannheten. Använd den här tabellen för att avgöra vilken data mängd som ska användas för att lösa dina problem:

| Användningsfall | Datatyp |
| -------- | --------- |
| Förbättra igenkännings precisionen för branschspecifika vokabulär och grammatik, som medicinsk terminologi eller IT-jargong | Relaterad text (meningar/yttranden) |
| Definiera den fonetiska och visade formen av ett ord eller en term som innehåller uttal som inte är standard, t. ex. produkt namn eller akronymer | Relaterad text (uttal) |
| Förbättra igenkännings precisionen för tal format, accenttecken eller vissa bakgrunds brus | Ljud + medmärkta avskrifter |

## <a name="train-and-evaluate-a-model"></a>Träna och utvärdera en modell

Det första steget för att träna en modell är att överföra tränings data. Se [förbereda och testa dina data](./how-to-custom-speech-test-and-train.md) för steg-för-steg-instruktioner för att förbereda medmärkta avskrifter och relaterad text (yttranden och uttal). När du har överfört tränings data följer du de här anvisningarna för att börja träna din modell:

1. Logga in på [Custom Speech Portal](https://speech.microsoft.com/customspeech). Om du planerar att träna en modell med ljud och data uppsättningar med Human-märkta avskrifter väljer du en tal prenumeration i en [region med dedikerad maskin vara](custom-speech-overview.md#set-up-your-azure-account) för utbildning.
2. Gå till **tal-till-text**  >  **Custom Speech**  >  **[Name of Project]**  >  **Training**.
3. Välj **träna modell**.
4. Ge din utbildning ett **namn** och en **Beskrivning**.
5. I listan **scenario och bas linje modell** väljer du det scenario som passar din domän bäst. Om du inte är säker på vilket scenario du väljer väljer du **Allmänt**. Bas linje modellen är start punkten för utbildning. Den senaste modellen är vanligt vis det bästa valet.
6. På sidan **Välj tränings data** väljer du en eller flera relaterade text data uppsättningar eller ljud och data uppsättningar med mänskligt avskrifter som du vill använda för utbildning.

> [!NOTE]
> När du tränar en ny modell börjar du med relaterad text. utbildning med ljud och mänsklig avskrift kan ta mycket längre tid **(upp till [flera dagar](how-to-custom-speech-evaluate-data.md#add-audio-with-human-labeled-transcripts)**).

> [!NOTE]
> Det är inte alla bas modeller som stöder utbildning med ljud. Om en bas modell inte stöder den, kommer tal tjänsten endast använda texten från avskrifterna och ignorera ljudet. Se [språk stöd](language-support.md#speech-to-text) för en lista över bas modeller som stöder utbildning med ljud data.

> [!NOTE]
> I fall när du ändrar bas modellen som används för utbildning och du har ljud i träning-datauppsättningen, kontrollerar du *alltid* om den nya valda bas modellen [stöder utbildning med ljuddata](language-support.md#speech-to-text). Om den tidigare använda bas modellen inte har stöd för utbildning med ljuddata, och hierarkin data uppsättning innehåller ljud, ökar inlärnings tiden med den nya bas modellen **drastiskt** , och det kan enkelt gå från flera timmar till flera dagar. Detta gäller särskilt om din röst tjänst prenumeration **inte** finns i en [region med den dedikerade maskin varan](custom-speech-overview.md#set-up-your-azure-account) för utbildning.
>
> Om du möter problemet som beskrivs i stycket ovan kan du snabbt minska inlärnings tiden genom att minska mängden ljud i data uppsättningen eller ta bort det helt och hållet texten kvar. Det sistnämnda alternativet är starkt rekommenderat om din prenumeration på röst tjänsten **inte** finns i en [region med den dedikerade maskin varan](custom-speech-overview.md#set-up-your-azure-account) för utbildning.

7. När träningen är klar kan du göra en precisions testning för den nytränade modellen. Det här är valfritt.
8. Välj **skapa** för att skapa din anpassade modell.

I **tränings** tabellen visas en ny post som motsvarar den nya modellen. Tabellen visar också status: **bearbetning**, **lyckades** eller **misslyckades**.

Se [hur du kan](how-to-custom-speech-evaluate-data.md) utvärdera och förbättra Custom Speech modellens precision. Om du väljer att testa noggrannhet är det viktigt att välja en akustisk data uppsättning som skiljer sig från den som du använde med din modell för att få en realistisk känsla för modellens prestanda.

> [!NOTE]
> Både bas modeller och anpassade modeller kan bara användas upp till ett visst datum (se [modellens livs cykel](custom-speech-overview.md#model-lifecycle)). Tal Studio visar det här datumet i kolumnen **förfallo** datum för varje modell och slut punkt. Efter denna datum förfrågan till en slut punkt eller till en batch-avskrift kan det gå sönder eller återgå till bas modellen.
>
> Träna modellen med hjälp av den senaste bas modellen för att dra nytta av precisions förbättringar och undvika att din modell upphör att gälla.

## <a name="deploy-a-custom-model"></a>Distribuera en anpassad modell

När du har laddat upp och inspekterat data, utvärderar precision och tränar en anpassad modell, kan du distribuera en anpassad slut punkt för användning med dina appar, verktyg och produkter. 

Logga in på [Custom Speech Portal](https://speech.microsoft.com/customspeech)om du vill skapa en anpassad slut punkt. Välj **distribution** på **Custom Speech** -menyn längst upp på sidan. Om det här är din första körning ser du att det inte finns några slut punkter som visas i tabellen. När du har skapat en slut punkt använder du den här sidan för att spåra varje distribuerad slut punkt.

Välj sedan **Lägg till slut punkt** och ange ett **namn** och en **Beskrivning** för din anpassade slut punkt. Välj den anpassade modell som du vill koppla till slut punkten.  Du kan också aktivera loggning från den här sidan. Med loggning kan du övervaka slut punkts trafik. Om loggning är inaktiverat lagras inte trafiken.

![Skärm bild som visar sidan ny slut punkt.](./media/custom-speech/custom-speech-deploy-model.png)

> [!NOTE]
> Glöm inte att acceptera villkoren för användning och pris information.

Välj sedan **skapa**. Den här åtgärden återgår till **distributions** sidan. Tabellen innehåller nu en post som motsvarar din anpassade slut punkt. Slut punktens status visar dess aktuella tillstånd. Det kan ta upp till 30 minuter att instansiera en ny slut punkt med hjälp av dina anpassade modeller. När distributions statusen ändras till **slutförd**, är slut punkten redo att användas.

När slut punkten har distribuerats visas slut punktens namn som en länk. Välj länken om du vill se information som är speciell för din slut punkt, till exempel slut punkts nyckel, slut punkts-URL och exempel kod. Anteckna förfallo datumet och uppdatera slut punktens modell före det datumet för att säkerställa en oavbruten tjänst.

## <a name="view-logging-data"></a>Visa loggnings data

Loggnings data är tillgängliga för export om du går till slut punkts sidan under **distributioner**.
> [!NOTE]
>Loggnings data är tillgängliga i 30 dagar på Microsoft-ägda lagrings enheter. Den kommer att tas bort efteråt. Om ett kundägda lagrings konto är länkat till Cognitive Services prenumerationen raderas inte loggnings data automatiskt.

## <a name="next-steps"></a>Nästa steg

* [Lär dig hur du använder din anpassade modell](how-to-specify-source-language.md)

## <a name="additional-resources"></a>Ytterligare resurser

- [Förbered och testa dina data](./how-to-custom-speech-test-and-train.md)
- [Inspektera dina data](how-to-custom-speech-inspect-data.md)
- [Utvärdera dina data](how-to-custom-speech-evaluate-data.md)
