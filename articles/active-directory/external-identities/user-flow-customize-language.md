---
title: Språk anpassning i Azure AD-användar flöden
description: Lär dig mer om att anpassa språk upplevelsen i dina användar flöden i Azure Active Directory.
services: active-directory
author: msmimart
manager: celestedg
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 03/02/2021
ms.author: mimart
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: a199c207e8ea35f1471df9bfd0c4134551b9995f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101653498"
---
# <a name="language-customization-in-azure-active-directory"></a>Språk anpassning i Azure Active Directory

Med språk anpassning i Azure Active Directory (Azure AD) kan ditt användar flöde hantera olika språk som passar dina användares behov. Microsoft tillhandahåller översättningarna för [36-språk](#supported-languages). Även om din upplevelse bara är avsedd för ett enda språk, kan du anpassa attributnamnet på sidan attribut samling.

## <a name="how-language-customization-works"></a>Så här fungerar språk anpassning

Som standard är språk anpassning aktive rad för användare som registrerar sig för att säkerställa en konsekvent registrerings upplevelse. Du kan använda språk för att ändra de strängar som visas för användare som en del av processen för insamling av attribut under registreringen.

> [!NOTE]
> Om du använder anpassade användarattribut måste du ange dina egna översättningar. Mer information finns i [Anpassa dina strängar](#customize-your-strings).

## <a name="customize-your-strings"></a>Anpassa dina strängar

Med språk anpassning kan du anpassa en sträng i ditt användar flöde.

1. Logga in till [Azure-portalen](https://portal.azure.com) som Azure AD-administratör.
2. Under **Azure-tjänster** väljer du **Azure Active Directory**.
3. På den vänstra menyn väljer du **externa identiteter**.
4. Välj **användar flöden**.
3. Välj det användar flöde som du vill aktivera för översättningar.
4. Välj **språk**.
5. På sidan **språk** för användar flödet väljer du det språk som du vill anpassa.
6. **Sidan expandera attribut samling**.
7. Välj **Hämta standardvärden** (eller **Hämta åsidosättningar** om du tidigare har redigerat det här språket).

De här stegen ger dig en JSON-fil som du kan använda för att börja redigera dina strängar.

### <a name="change-any-string-on-the-page"></a>Ändra valfri sträng på sidan

1. Öppna JSON-filen som hämtats från tidigare instruktioner i en JSON-redigerare.
1. Hitta det element som du vill ändra. Du kan söka efter `StringId` den sträng som du söker eller leta efter det `Value` attribut som du vill ändra.
1. Uppdatera `Value` attributet med det du vill visa.
1. Ändra till för varje sträng som du vill ändra `Override` `true` .
1. Spara filen och överför ändringarna. (Du kan hitta överförings kontrollen på samma plats som du laddade ned JSON-filen.)

> [!IMPORTANT]
> Om du behöver åsidosätta en sträng, se till att ange `Override` värdet till `true` . Om värdet inte ändras ignoreras posten.

### <a name="change-extension-attributes"></a>Ändra attribut för tillägg

Om du vill ändra strängen för ett anpassat användarattribut, eller om du vill lägga till ett i JSON, är det i följande format:

```JSON
{
  "LocalizedStrings": [
    {
      "ElementType": "ClaimType",
      "ElementId": "extension_<ExtensionAttribute>",
      "StringId": "DisplayName",
      "Override": true,
      "Value": "<ExtensionAttributeValue>"
    }
    [...]
}
```

Ersätt `<ExtensionAttribute>` med namnet på ditt anpassade användar-attribut.

Ersätt `<ExtensionAttributeValue>` med den nya strängen som ska visas.

### <a name="provide-a-list-of-values-by-using-localizedcollections"></a>Ange en lista med värden med hjälp av LocalizedCollections

Om du vill ange en uppsättning värden för svar måste du skapa ett- `LocalizedCollections` attribut. `LocalizedCollections` är en matris med `Name` och `Value` par. Ordningen för objekten visas i den ordning som de visas. Använd följande format för att lägga till `LocalizedCollections` :

```JSON
{
  "LocalizedStrings": [...],
  "LocalizedCollections": [{
      "ElementType":"ClaimType",
      "ElementId":"<UserAttribute>",
      "TargetCollection":"Restriction",
      "Override": true,
      "Items":[
           {
                "Name":"<Response1>",
                "Value":"<Value1>"
           },
           {
                "Name":"<Response2>",
                "Value":"<Value2>"
           }
     ]
  }]
}
```

* `ElementId` är det användar-attribut som det här `LocalizedCollections` attributet är ett svar på.
* `Name` är värdet som visas för användaren.
* `Value` är vad som returneras i anspråket när det här alternativet är markerat.

### <a name="upload-your-changes"></a>Överför dina ändringar

1. När du har slutfört ändringarna i JSON-filen går du tillbaka till klienten.
1. Välj **användar flöden** och klicka på det användar flöde som du vill aktivera för översättningar.
1. Välj **språk**.
1. Välj det språk som du vill översätta till.
1. **Sidan Välj attribut samling**.
1. Välj mappikonen och välj den JSON-fil som ska överföras.

Ändringarna sparas automatiskt i ditt användar flöde.

## <a name="additional-information"></a>Ytterligare information

### <a name="page-ui-customization-labels-as-overrides"></a>Sid gränssnitt anpassnings etiketter som åsidosättningar

När du aktiverar språk anpassning behålls dina tidigare redigeringar av etiketter med anpassning av sid gränssnitt i en JSON-fil för engelska (en). Du kan fortsätta att ändra dina etiketter och andra strängar genom att överföra språk resurser i språk anpassning.

### <a name="up-to-date-translations"></a>Aktuella översättningar

Microsoft strävar efter att tillhandahålla de senaste översättningarna för din användning. Microsoft förbättrar kontinuerligt översättningarna och ser till att de är kompatibla. Microsoft kommer att identifiera buggar och ändringar i global terminologi och göra uppdateringar som fungerar sömlöst i ditt användar flöde.

### <a name="support-for-right-to-left-languages"></a>Stöd för höger-till-vänster-språk

Microsoft tillhandahåller för närvarande inte stöd för språk som skrivs från höger till vänster. Du kan göra detta med hjälp av anpassade språk och använda CSS för att ändra hur strängarna visas. Om du behöver den här funktionen kan du rösta på [Azure feedback](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/19393000-provide-language-support-for-right-to-left-languag).

### <a name="social-identity-provider-translations"></a>Översättningar av sociala identitets leverantörer

Microsoft tillhandahåller `ui_locales` OIDC-parametern för sociala inloggningar. Men vissa sociala identitets leverantörer, inklusive Facebook och Google, följer inte dem.

### <a name="browser-behavior"></a>Webb läsar beteende

Chrome och Firefox båda förfrågningarna om deras inställda språk. Om det är ett språk som stöds visas det före standardvärdet. Microsoft Edge begär för närvarande inte ett språk och går direkt till standard språket.

## <a name="supported-languages"></a>Språk som stöds

Azure AD innehåller stöd för följande språk. Användar flödes språk tillhandahålls av Azure AD. Meddelande språken för Multi-Factor Authentication (MFA) tillhandahålls av [Azure AD MFA](../authentication/concept-mfa-howitworks.md).

| Språk              | Språkkod | Användarflöden         | MFA-meddelanden  |
|-----------------------| :-----------: | :----------------: | :----------------: |
| Arabiska                | bakre            | ![X som anger Nej.](./media/user-flow-customize-language/no.png) | ![Grön bock markering.](./media/user-flow-customize-language/yes.png) |
| Bulgariska             | Bulgarien            | ![X som anger Nej.](./media/user-flow-customize-language/no.png) | ![Grön bock markering.](./media/user-flow-customize-language/yes.png) |
| Bangla                | bn            | ![Grön bock markering.](./media/user-flow-customize-language/yes.png) | ![X som anger Nej.](./media/user-flow-customize-language/no.png) |
| Katalanska               | onlinecertifikatutfärdare            | ![X som anger Nej.](./media/user-flow-customize-language/no.png) | ![Grön bock markering.](./media/user-flow-customize-language/yes.png) |
| Tjeckiska                 | östasiatisk            | ![Grön bock markering.](./media/user-flow-customize-language/yes.png) | ![Grön bock markering.](./media/user-flow-customize-language/yes.png) |
| Danska                | da            | ![Grön bock markering.](./media/user-flow-customize-language/yes.png) | ![Grön bock markering.](./media/user-flow-customize-language/yes.png) |
| Tyska                | de            | ![Grön bock markering.](./media/user-flow-customize-language/yes.png) | ![Grön bock markering.](./media/user-flow-customize-language/yes.png) |
| Grekiska                 | El            | ![Grön bock markering.](./media/user-flow-customize-language/yes.png) | ![Grön bock markering.](./media/user-flow-customize-language/yes.png) |
| Engelska               | en            | ![Grön bock markering.](./media/user-flow-customize-language/yes.png) | ![Grön bock markering.](./media/user-flow-customize-language/yes.png) |
| Spanska               | es            | ![Grön bock markering.](./media/user-flow-customize-language/yes.png) | ![Grön bock markering.](./media/user-flow-customize-language/yes.png) |
| Estniska              | ge            | ![X som anger Nej.](./media/user-flow-customize-language/no.png) | ![Grön bock markering.](./media/user-flow-customize-language/yes.png) |
| Baskiska                | gemenskaps            | ![X som anger Nej.](./media/user-flow-customize-language/no.png) | ![Grön bock markering.](./media/user-flow-customize-language/yes.png) |
| Finska               | fi            | ![Grön bock markering.](./media/user-flow-customize-language/yes.png) | ![Grön bock markering.](./media/user-flow-customize-language/yes.png) |
| Franska                | fr            | ![Grön bock markering.](./media/user-flow-customize-language/yes.png) | ![Grön bock markering.](./media/user-flow-customize-language/yes.png) |
| Galiciska              | huvud            | ![X som anger Nej.](./media/user-flow-customize-language/no.png) | ![Grön bock markering.](./media/user-flow-customize-language/yes.png) |
| Gujarati              | gu            | ![Grön bock markering.](./media/user-flow-customize-language/yes.png) | ![X som anger Nej.](./media/user-flow-customize-language/no.png) |
| Hebreiska                | producenten            | ![X som anger Nej.](./media/user-flow-customize-language/no.png) | ![Grön bock markering.](./media/user-flow-customize-language/yes.png) |
| Hindi                 | Hej            | ![Grön bock markering.](./media/user-flow-customize-language/yes.png) | ![Grön bock markering.](./media/user-flow-customize-language/yes.png) |
| Kroatiska              | tim            | ![Grön bock markering.](./media/user-flow-customize-language/yes.png) | ![Grön bock markering.](./media/user-flow-customize-language/yes.png) |
| Ungerska             | HU            | ![Grön bock markering.](./media/user-flow-customize-language/yes.png) | ![Grön bock markering.](./media/user-flow-customize-language/yes.png) |
| Indonesiska            | id            | ![X som anger Nej.](./media/user-flow-customize-language/no.png) | ![Grön bock markering.](./media/user-flow-customize-language/yes.png) |
| Italienska               | it            | ![Grön bock markering.](./media/user-flow-customize-language/yes.png) | ![Grön bock markering.](./media/user-flow-customize-language/yes.png) |
| Japanska              | ja            | ![Grön bock markering.](./media/user-flow-customize-language/yes.png) | ![Grön bock markering.](./media/user-flow-customize-language/yes.png) |
| Kazakiska                | kk            | ![X som anger Nej.](./media/user-flow-customize-language/no.png) | ![Grön bock markering.](./media/user-flow-customize-language/yes.png) |
| Kannada               | 5,0            | ![Grön bock markering.](./media/user-flow-customize-language/yes.png) | ![X som anger Nej.](./media/user-flow-customize-language/no.png) |
| Koreanska                | Ko            | ![Grön bock markering.](./media/user-flow-customize-language/yes.png) | ![Grön bock markering.](./media/user-flow-customize-language/yes.png) |
| Litauiska            | lt            | ![X som anger Nej.](./media/user-flow-customize-language/no.png) | ![Grön bock markering.](./media/user-flow-customize-language/yes.png) |
| Lettiska               | LV            | ![X som anger Nej.](./media/user-flow-customize-language/no.png) | ![Grön bock markering.](./media/user-flow-customize-language/yes.png) |
| Malayalam             | ml            | ![Grön bock markering.](./media/user-flow-customize-language/yes.png) | ![X som anger Nej.](./media/user-flow-customize-language/no.png) |
| Marathi               | Mr            | ![Grön bock markering.](./media/user-flow-customize-language/yes.png) | ![X som anger Nej.](./media/user-flow-customize-language/no.png) |
| Malajiska                 | millisekund            | ![Grön bock markering.](./media/user-flow-customize-language/yes.png) | ![Grön bock markering.](./media/user-flow-customize-language/yes.png) |
| Norska bokmål      | Anm            | ![Grön bock markering.](./media/user-flow-customize-language/yes.png) | ![X som anger Nej.](./media/user-flow-customize-language/no.png) |
| Nederländska                 | nl            | ![Grön bock markering.](./media/user-flow-customize-language/yes.png) | ![Grön bock markering.](./media/user-flow-customize-language/yes.png) |
| Norska             | nej            | ![X som anger Nej.](./media/user-flow-customize-language/no.png) | ![Grön bock markering.](./media/user-flow-customize-language/yes.png) |
| Punjabi               | PA            | ![Grön bock markering.](./media/user-flow-customize-language/yes.png) | ![X som anger Nej.](./media/user-flow-customize-language/no.png) |
| Polska                | pl            | ![Grön bock markering.](./media/user-flow-customize-language/yes.png) | ![Grön bock markering.](./media/user-flow-customize-language/yes.png) |
| Portugisiska – Brasilien   | pt-br         | ![Grön bock markering.](./media/user-flow-customize-language/yes.png) | ![Grön bock markering.](./media/user-flow-customize-language/yes.png) |
| Portugisiska – Portugal | pt-pt         | ![Grön bock markering.](./media/user-flow-customize-language/yes.png) | ![Grön bock markering.](./media/user-flow-customize-language/yes.png) |
| Rumänska              | ro            | ![Grön bock markering.](./media/user-flow-customize-language/yes.png) | ![Grön bock markering.](./media/user-flow-customize-language/yes.png) |
| Ryska               | ru            | ![Grön bock markering.](./media/user-flow-customize-language/yes.png) | ![Grön bock markering.](./media/user-flow-customize-language/yes.png) |
| Slovakiska                | sk            | ![Grön bock markering.](./media/user-flow-customize-language/yes.png) | ![Grön bock markering.](./media/user-flow-customize-language/yes.png) |
| Slovenska             | SL            | ![X som anger Nej.](./media/user-flow-customize-language/no.png) | ![Grön bock markering.](./media/user-flow-customize-language/yes.png) |
| Serbiska (kyrillisk)    | SR-cryl-CS    | ![X som anger Nej.](./media/user-flow-customize-language/no.png) | ![Grön bock markering.](./media/user-flow-customize-language/yes.png) |
| Serbiska (latinsk)       | SR-latn-CS    | ![X som anger Nej.](./media/user-flow-customize-language/no.png) | ![Grön bock markering.](./media/user-flow-customize-language/yes.png) |
| Svenska               | sv            | ![Grön bock markering.](./media/user-flow-customize-language/yes.png) | ![Grön bock markering.](./media/user-flow-customize-language/yes.png) |
| Tamilska                 | layout            | ![Grön bock markering.](./media/user-flow-customize-language/yes.png) | ![X som anger Nej.](./media/user-flow-customize-language/no.png) |
| Telugu                | &            | ![Grön bock markering.](./media/user-flow-customize-language/yes.png) | ![X som anger Nej.](./media/user-flow-customize-language/no.png) |
| Thailändska                  | i:te            | ![Grön bock markering.](./media/user-flow-customize-language/yes.png) | ![Grön bock markering.](./media/user-flow-customize-language/yes.png) |
| Turkiska               | TR            | ![Grön bock markering.](./media/user-flow-customize-language/yes.png) | ![Grön bock markering.](./media/user-flow-customize-language/yes.png) |
| Ukrainska             | Storbritannien            | ![X som anger Nej.](./media/user-flow-customize-language/no.png) | ![Grön bock markering.](./media/user-flow-customize-language/yes.png) |
| Vietnamesiska            | Vi            | ![X som anger Nej.](./media/user-flow-customize-language/no.png) | ![Grön bock markering.](./media/user-flow-customize-language/yes.png) |
| Kinesiska (förenklad)  | zh-hans       | ![Grön bock markering.](./media/user-flow-customize-language/yes.png) | ![Grön bock markering.](./media/user-flow-customize-language/yes.png) |
| Kinesiska (traditionell) | zh-Hant       | ![Grön bock markering.](./media/user-flow-customize-language/yes.png) | ![Grön bock markering.](./media/user-flow-customize-language/yes.png) |