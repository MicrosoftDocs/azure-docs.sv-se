---
title: Ljud innehålls skapande – tal tjänst
titleSuffix: Azure Cognitive Services
description: Att skapa ljud innehåll är ett online verktyg som gör att du kan anpassa och finjustera Microsofts text till tal-utdata för dina appar och produkter.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/31/2020
ms.author: trbye
ms.openlocfilehash: 7e43c472c939049e5dfd4ec4df909a3178ef8e2d
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/07/2021
ms.locfileid: "106553268"
---
# <a name="improve-synthesis-with-the-audio-content-creation-tool"></a>Förbättra syntesen med verktyget för att skapa ljud innehåll

Att [skapa ljud innehåll](https://aka.ms/audiocontentcreation) är ett lättanvänt och kraftfullt verktyg som du kan använda för att skapa mycket naturligt ljud innehåll för en mängd olika scenarier, t. ex. Audiobooks, nyhets sändningar, video berättarröstinspelningar och chat-robotar. När du skapar ljud innehåll kan du finjustera text till tal-röster och utforma anpassade ljud upplevelser på ett effektivt och billigt sätt.

Verktyget baseras på [SSML (Speech syntes Markup Language)](speech-synthesis-markup.md). Det gör att du kan justera attribut för text till tal i real tid eller batch-syntes, till exempel röst tecken, röst format, tal hastighet, uttal och prosody.

Du kan ha enkel åtkomst till fler än 150 färdiga röster i nära 50 olika språk, inklusive den avancerade neurala TTS-rösterna och din anpassade röst om du har skapat en sådan. 

Se [video guiden](https://www.youtube.com/watch?v=O1wIJ7mts_w) för att skapa ljud innehåll.

## <a name="how-to-get-started"></a>Hur kommer jag igång?

Att skapa ljud innehåll är ett kostnads fritt verktyg, men du betalar för Azure Speech service som du använder. För att kunna arbeta med verktyget måste du logga in med ett Azure-konto och skapa en tal resurs. För varje Azure-konto har du månatliga kostnads fria tal kvoter som innehåller 500 000 tecken för neurala TTS-röster (per månad), 5 000 000 tecken för standard-och anpassade röster (per månad) och en anpassad värd tjänst för röst slut punkt (per månad). Den månatliga tilldelade mängden är oftast tillräckligt stor för ett litet innehålls lag runt 3-5 personer. Här följer stegen för att skapa ett Azure-konto och få en tal resurs. 

### <a name="step-1---create-an-azure-account"></a>Steg 1 – Skapa ett Azure-konto

Du måste ha ett [Microsoft-konto](https://account.microsoft.com/account) och ett [Azure-konto](https://azure.microsoft.com/free/ai/)för att kunna arbeta med att skapa ljud innehåll. Följ dessa instruktioner för att [Konfigurera kontot](./overview.md#try-the-speech-service-for-free). 

[Azure Portal](https://portal.azure.com/) är den centraliserade plats där du kan hantera ditt Azure-konto. Du kan skapa tal resursen, hantera produkt åtkomsten och övervaka allt från enkla webbappar till komplexa moln distributioner. 

### <a name="step-2---create-a-speech-resource"></a>Steg 2 – Skapa en tal resurs

När du har registrerat dig för Azure-kontot måste du skapa en tal resurs under ditt Azure-konto för att få åtkomst till tal tjänsterna. Visa instruktioner för [hur du skapar en tal resurs](./overview.md#create-the-azure-resource). 

Det tar en stund att distribuera den nya tal resursen. När distributionen är klar kan du starta resan för att skapa ljud innehåll. 

 >[!NOTE]
   > Om du planerar att använda neurala-röster ser du till att du skapar din resurs i [en region som stöder neurala-röster](regions.md#neural-and-standard-voices).
 
### <a name="step-3---log-into-the-audio-content-creation-with-your-azure-account-and-speech-resource"></a>Steg 3 – Logga in på skapandet av ljud innehåll med ditt Azure-konto och din tal resurs

1. När du har skaffat Azure-kontot och tal resursen kan du logga in på att [skapa ljud innehåll](https://aka.ms/audiocontentcreation) genom att klicka på **Kom igång**.
2. Sidan **tal resurs** visas för dig. Välj den tal resurs som du vill arbeta med. Klicka på **gå till Studio** för att starta din ljud skapande. Du kan också skapa en ny tal resurs här genom att klicka på **Skapa ny**. När du loggar in på verktyget för att skapa ljud innehåll för nästa gång, kommer vi att länka dig direkt till ljud arbets filerna under den aktuella tal resursen. 
3. Du kan ändra din tal resurs när som helst med alternativet **Inställningar** , som finns i det övre navigerings fältet.

## <a name="how-to-use-the-tool"></a>Hur använder du verktyget?

Det här diagrammet visar de steg som krävs för att finjustera text till tal-utdata. Använd länkarna nedan för att lära dig mer om varje steg.

:::image source="media/audio-content-creation/audio-content-creation-diagram.jpg" alt-text="Ett diagram över de steg som krävs för att finjustera text till tal-utdata":::


1. Välj den tal resurs som du vill arbeta med.
2. [Skapa en fil för ljud justering](#create-an-audio-tuning-file) med hjälp av oformaterad text eller SSML-skript. Skriv eller ladda upp innehållet i för att skapa ljud innehåll.
3. Välj rösten och språket för ditt skript innehåll. Framställning av ljud innehåll innehåller alla [Microsofts text till tal-röster](language-support.md#text-to-speech). Du kan använda standard, neurala eller din egen anpassade röst.
   >[!NOTE]
   > Gated Access är tillgängligt för anpassade neurala-röster, vilket gör att du kan skapa hög definitions röster som liknar naturliga ljud. Mer information finns i [Hantera process](./text-to-speech.md).

4. Klicka på ikonen **spela upp** (en triangel) om du vill förhandsgranska standard syntes resultatet. Förbättra sedan utdata genom att justera uttal, Break, färgdjup, Rate, intonation, röst stil med mera. En fullständig lista över alternativ finns i [tal syntes märknings språk](speech-synthesis-markup.md). Här är en [video](https://www.youtube.com/watch?v=O1wIJ7mts_w) som visar hur du finjusterar tal resultatet med att skapa ljud innehåll. 
5. Spara och [exportera det anpassade ljudet](#export-tuned-audio). När du sparar justerings spåret i systemet kan du fortsätta att arbeta och iterera på utdata. När du är nöjd med utdata kan du skapa en ljud skapande aktivitet med funktionen Exportera. Du kan se statusen för export uppgiften och hämta utdata för användning med dina appar och produkter.

## <a name="create-an-audio-tuning-file"></a>Skapa en fil för ljud justering

Det finns två sätt att hämta innehåll till verktyget för att skapa ljud innehåll.

**Alternativ 1:**

1. Klicka på ikonen **ny fil** längst upp till höger för att skapa en ny fil för ljud justering.
2. Skriv eller klistra in innehållet i redigerings fönstret. Tecknen för varje fil är upp till 20 000. Om skriptet innehåller fler än 20 000 tecken kan du använda alternativ 2 för att automatiskt dela upp innehållet i flera filer. 
3. Glöm inte att spara.

**Alternativ 2:**

1. Klicka på **överför** för att importera en eller flera textfiler. Både oformaterad text och SSML stöds. Om skript filen är mer än 20 000 tecken kan du dela filen med stycken, med tecken eller med reguljära uttryck. 
3. När du laddar upp dina textfiler bör du kontrol lera att filen uppfyller dessa krav.

   | Egenskap | Värde/anteckningar |
   |----------|---------------|
   | Filformat | Oformaterad text (. txt)<br/> SSML text (. txt)<br/> Zip-filer stöds inte |
   | Kodnings format | UTF-8 |
   | Filnamn | Varje fil måste ha ett unikt namn. Dubbletter stöds inte. |
   | Text längd | Text filens tecken begränsning är 20 000. Om filerna överskrider begränsningen kan du dela filerna med instruktionerna i verktyget. |
   | SSML-begränsningar | Varje SSML-fil får bara innehålla en enda del av SSML. |

**Exempel på oformaterad text**

```txt
Welcome to use Audio Content Creation to customize audio output for your products.
```
**SSML text exempel**

```xml
<speak xmlns="http://www.w3.org/2001/10/synthesis" xmlns:mstts="http://www.w3.org/2001/mstts" version="1.0" xml:lang="en-US">
    <voice name="Microsoft Server Speech Text to Speech Voice (en-US, AriaNeural)">
    Welcome to use Audio Content Creation <break time="10ms" />to customize audio output for your products.
    </voice>
</speak>
```

## <a name="export-tuned-audio"></a>Exportera anpassat ljud

När du har granskat din ljud uppspelning och är nöjd med justeringen och justeringen kan du exportera ljudet.

1. Klicka på **Exportera** för att skapa en ljud skapande aktivitet. **Export till ljud bibliotek** rekommenderas eftersom det stöder långa ljud utdata och den fullständiga ljud uppspelnings upplevelsen. Du kan också ladda ned ljudet till den lokala disken direkt, men bara de första 10 minuterna är tillgängliga.
2. Välj utdataformat för det justerade ljudet. En lista över format och samplings frekvenser som stöds finns nedan.
3. Du kan visa status för uppgiften på fliken **Exportera aktivitet** . Om aktiviteten Miss lyckas, se sidan detaljerad information för en fullständig rapport.
4. När uppgiften har slutförts är ljudet tillgängligt för hämtning på fliken **ljud bibliotek** .
5. Klicka på **Hämta**. Nu är du redo att använda ditt anpassade ljud i dina appar eller produkter.

**Ljud format som stöds**

| Format | 16 kHz samplings frekvens | 24 kHz samplings frekvens |
|--------|--------------------|--------------------|
| våg | riff-16khz-bitarsläge-mono-PCM | riff-24khz-bitarsläge-mono-PCM |
| MP3 | Audio-16khz-128kbitrate-mono-MP3 | Audio-24khz-160kbitrate-mono-MP3 |

## <a name="how-to-addremove-audio-content-creation-users"></a>Hur du lägger till/tar bort ljud innehålls användare?

Om fler än en användare vill använda framställning av ljud innehåll kan du bevilja användar åtkomst till Azure-prenumerationen och tal resursen. Om du lägger till en användare i en Azure-prenumeration har användaren åtkomst till alla resurser i Azure-prenumerationen. Men om du bara lägger till en användare till en tal resurs får användaren bara åtkomst till tal resursen och kan inte komma åt andra resurser i Azure-prenumerationen. En användare med åtkomst till tal resursen kan använda ljud innehåll.

### <a name="add-users-to-a-speech-resource"></a>Lägga till användare till en tal resurs

Följ dessa steg om du vill lägga till en användare till en tal resurs så att de kan använda ljud innehåll.

1. Sök efter **kognitiva tjänster** i [Azure Portal](https://portal.azure.com/)väljer du den tal resurs som du vill lägga till användare i.
2. Klicka på **Åtkomstkontroll (IAM)** . Klicka på fliken **Rolltilldelningar** så att du ser alla rolltilldelningar för prenumerationen.
    :::image source="media/audio-content-creation/access-control-roles.png" alt-text="Fliken roll tilldelning":::
1. Klicka på **Lägg till** > **Lägg till rolltilldelning** för att öppna fönstret Lägg till rolltilldelning. Välj **användar rollen kognitiv tjänst** i list rutan roll. Om du vill ge användaren ägarskap för den här tal resursen kan du välja **ägar** rollen.
1. Välj en användare i listan. Om du inte ser användaren i listan kan du skriva i rutan Välj för att söka i katalogen efter visnings namn och e-postadresser. Om användaren inte finns i den här katalogen kan du mata in användarens [Microsoft-konto](https://account.microsoft.com/account) (som är betrodd av Azure Active Directory).
1. Klicka på **Spara** för att tilldela rollen. Efter en liten stund tilldelas användaren kognitiv tjänst användar rollen i tal resurs omfånget.

    :::image source="media/audio-content-creation/add-role-first.png" alt-text="Dialog rutan Lägg till roll":::

1. Användare som du lägger till får ett e-postmeddelande om inbjudan. När de klickar på **acceptera inbjudan**  >  **acceptera för att ansluta till Azure** kan de använda [framställning av ljud innehåll](https://aka.ms/audiocontentcreation).

Användare som är i samma tal resurs ser var och en av de andra arbetar med att skapa ljud innehåll Studio. Om du vill att varje enskild användare ska ha en unik och privat arbets plats i innehålls skapande, [skapar du en ny tal resurs](#step-2---create-a-speech-resource) för varje användare och ger varje användare unik åtkomst till tal resursen. 

### <a name="remove-users-from-a-speech-resource"></a>Ta bort användare från en tal resurs
1. Sök efter **kognitiva tjänster** i Azure Portal väljer du den tal resurs som du vill ta bort användare från.
2. Klicka på **Åtkomstkontroll (IAM)**. Klicka på fliken **roll tilldelningar** för att visa alla roll tilldelningar för den här tal resursen.
3. Välj de användare som du vill ta bort och klicka på **ta bort**  >  **OK**.
    :::image source="media/audio-content-creation/remove-user.png" alt-text="Knappen Ta bort":::

### <a name="enable-users-to-grant-access"></a>Gör det möjligt för användare att bevilja åtkomst
Om du vill att en av användarna ska ge åtkomst till andra användare måste du ge användaren rollen som ägare till tal resursen och ange användaren som Azure Directory Reader. 
1. Lägg till användaren som ägare till tal resursen. Se [hur du lägger till användare till en tal resurs](#add-users-to-a-speech-resource).
    :::image source="media/audio-content-creation/add-role.png" alt-text="Roll ägar fältet":::
1. Välj menyn komprimerad i det övre vänstra hörnet. Klicka på **Azure Active Directory** och sedan på **användare**.
1. Sök i användarens Microsoft-konto och gå till användarens informations sida. Klicka på **tilldelade roller**.
1. Klicka på **Lägg till uppdrag**  ->  **katalog läsare**.

## <a name="see-also"></a>Se även

* [Långt ljud-API](./long-audio-api.md)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Speech Studio](https://speech.microsoft.com)
