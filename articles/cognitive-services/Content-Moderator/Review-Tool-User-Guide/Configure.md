---
title: Konfigurera inställningar för gransknings verktyget – Content Moderator
titleSuffix: Azure Cognitive Services
description: Använd gransknings verktyget för att konfigurera eller hämta team, taggar, kopplingar, arbets flöden och autentiseringsuppgifter för Content Moderator.
services: cognitive-services
author: PatrickFarley
manager: mikemcca
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/15/2019
ms.author: pafarley
ms.openlocfilehash: 8812fd1e6c1efb2aa44c77573bc4b8f1c099834d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "92912029"
---
# <a name="configure-the-review-tool"></a>Konfigurera granskningsverktyget

[Gransknings verktyget](https://contentmoderator.cognitive.microsoft.com) har flera viktiga funktioner som du kan komma åt via menyn **Inställningar** på instrument panelen.

![Menyn Content Moderator granska för inställningar](images/settings-1.png)

## <a name="manage-team-and-subteams"></a>Hantera team och under grupper

På fliken **team** kan du hantera ditt team och under &mdash; grupper av användare som kan få ett meddelande när vissa [mänsklig granskningar](../review-api.md#reviews) startas. Du kan bara ha ett team (som du skapar när du registrerar dig med gransknings verktyget), men du kan skapa flera under grupper. Team administratören kan bjuda in medlemmar, ange sina behörigheter och tilldela dem till olika under grupper.

![Granska team inställningar för verktyget](images/settings-2-team.png)

Under grupper är användbara för att skapa eskalerade team eller team som är avsedda att granska särskilda innehålls kategorier. Du kan till exempel skicka vuxen innehåll till ett separat team för ytterligare granskning.

I det här avsnittet beskrivs hur du skapar under grupper och snabbt tilldelar recensioner i farten. Du kan dock använda [arbets flöden](workflows.md) för att tilldela granskningar baserat på vissa kriterier.

### <a name="create-a-subteam"></a>Skapa ett under team

Gå till avsnittet under **grupper** och klicka på **Lägg till under team**. Ange ditt under grupp namn i dialog rutan och klicka på **Spara**.

![Namn på under grupp](images/1-Teams-2.PNG)

#### <a name="invite-teammates"></a>Bjud in grupp medlemmar

Du kan inte tilldela någon till ett under team om de inte redan är medlemmar i standard teamet, så du måste lägga till granskare till standard teamet först. Klicka på **Bjud in** på fliken **team** .

![Bjuda in användare](images/invite-users.png)

#### <a name="assign-teammates-to-subteam"></a>Tilldela medarbetare till under grupper

Klicka på knappen **Lägg till medlem** för att tilldela medlemmar från standard teamet till en eller flera under grupper. Du kan bara lägga till befintliga användare i ett under team. Om du vill lägga till nya användare som inte finns i gransknings verktyget bjuder du in dem genom att använda knappen Bjud in på sidan team inställningar.

![Tilldela medlemmar i under grupp](images/1-Teams-3.PNG)

### <a name="assign-reviews-to-subteams"></a>Tilldela granskningar till under grupper

När du har skapat dina under grupper och tilldelade medlemmar kan du börja tilldela innehålls [granskningar](../review-api.md#reviews) till dessa under grupper. Detta görs från fliken **Granska** på webbplatsen.
Om du vill tilldela innehåll till ett under team klickar du på ellipsen i det övre högra hörnet, väljer **Flytta till** och väljer ett under team.

![Tilldela bild granskning till under grupp](images/3-review-image-subteam-1.png)

### <a name="switch-between-subteams"></a>Växla mellan under grupper

Om du är medlem i fler än ett under team kan du växla mellan dessa under grupper för att ändra vilka innehålls granskningar som visas för dig. På fliken **Granska** väljer du den nedrullningsbara menyn med etiketten **standard** och väljer **Välj under team**. Du kan visa innehålls granskningarna för olika under grupper, men bara de som är medlemmar.

![Växla mellan under grupper](images/3-review-image-subteam-2.png)

## <a name="tags"></a>Taggar

På fliken **taggar** kan du definiera anpassade moderatorer utöver de två standard redigerings taggarna &mdash; **isadult** (**a**) och **isracy** (**r**). När du skapar en anpassad tagg blir den tillgänglig i granskningar tillsammans med Standardtaggarna. Du kan ändra vilka taggar som ska visas i granskningarna genom att växla deras Synlighets inställningar.

![Vyn taggar, inklusive kryss rutorna "är synliga"](images/tags-4-disable.png)

### <a name="create-custom-tags"></a>Skapa anpassade taggar

Om du vill skapa en ny tagg måste du ange en kort kod, ett namn och en beskrivning i respektive fält.

- **Kort kod**: Ange en kod med två bokstäver för taggen. Exempel: **CB**
- **Namn**: Ange ett kort och beskrivande taggnamn i gemener utan blank steg. Exempel: **isbullying**.
- **Beskrivning**: (valfritt) ange en beskrivning av den typ av innehåll som taggen riktas mot. Exempel: **skildringar eller instanser av cyberhot bullying**.

Klicka på **Lägg** till för att lägga till en tagg och klicka på **Spara** när du är klar med att skapa taggar.

![Dialog rutan för att granska verktyg skapa ny tagg](images/settings-3-tags.png)

### <a name="delete-tags"></a>Ta bort taggar

Du kan ta bort anpassade taggar genom att välja pappers korgs ikonen bredvid posterna i listan Taggar, men du kan inte ta bort Standardtaggarna.

## <a name="connectors"></a>Anslutningar

På fliken **anslutningar** kan du hantera dina anslutningar, som är tjänstespecific plugin-program som kan bearbeta innehåll på olika sätt som en del av innehålls [arbets flöden](../review-api.md#workflows).

Standard anslutnings programmet när du skapar ett arbets flöde är Content Moderator koppling, som kan markera innehåll som **vuxen** eller **vågat**, hitta svordomar och så vidare. Du kan dock använda andra anslutningar som visas här, så länge du har autentiseringsuppgifter för sina respektive tjänster (för att använda ansikts kopplingen, till exempel måste du hämta en [ansikts](../../face/overview.md) prenumerations nyckel).

[Gransknings verktyget](./human-in-the-loop.md) innehåller följande kopplingar:

- Känsla
- Ansikte
- PhotoDNA moln tjänst
- Textanalys

### <a name="add-a-connector"></a>Lägg till en anslutning

Om du vill lägga till en anslutning (och göra den tillgänglig för användning i innehålls [arbets flöden](../review-api.md#workflows)) väljer du lämplig **Connect** -knapp. I nästa dialog ruta anger du din prenumerations nyckel för den tjänsten. När du är färdig ska den nya anslutningen visas överst på sidan.

![Inställningar för Content Moderator anslutningar](images/settings-4-connectors.png)

## <a name="workflows"></a>Arbetsflöden

På fliken **arbets flöden** kan du hantera dina [arbets flöden](../review-api.md#workflows). Arbets flöden är molnbaserade filter för innehåll och de fungerar med kopplingar för att sortera innehåll på olika sätt och vidta lämpliga åtgärder. Här kan du definiera, redigera och testa dina arbets flöden. Se [definiera och använda arbets flöden](Workflows.md) för att få vägledning om hur du gör detta.

![Content Moderator arbets flödes inställningar](images/settings-5-workflows.png)

## <a name="credentials"></a>Autentiseringsuppgifter

Fliken **autentiseringsuppgifter** ger snabb åtkomst till din Content moderator prenumerations nyckel, som du behöver för att få åtkomst till alla redigerings tjänster från ett rest-anrop eller klient-SDK.

![Content Moderator autentiseringsuppgifter](images/settings-6-credentials.png)

### <a name="use-external-credentials-for-workflows"></a>Använd externa autentiseringsuppgifter för arbets flöden

[Gransknings verktyget](https://contentmoderator.cognitive.microsoft.com) genererar en kostnads fri utvärderings nyckel för Azure Content moderator Services när du registrerar dig, men du kan också konfigurera den så att den använder en befintlig nyckel från ditt Azure-konto. Detta rekommenderas för storskaliga scenarier, eftersom kostnads fria utvärderings nycklar har strikta användnings gränser ([priser och gränser](https://azure.microsoft.com/pricing/details/cognitive-services/content-moderator/)).

Om du har skapat en [Content moderator-resurs](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator) i Azure navigerar du till den i Azure Portal och väljer bladet **nycklar** . Kopiera en av dina nycklar.

![Content Moderator nycklar i Azure Portal](images/credentials-azure-portal-keys.PNG)

På fliken **autentiseringsuppgifter** för [gransknings verktyget](https://contentmoderator.cognitive.microsoft.com)går du till fönstret **arbets flödes inställningar** , väljer **Redigera** och klistrar in nyckeln i fältet **OCP-APIM-Subscription-Key** . Nu använder arbets flöden som anropar redigerings-API: er dina Azure-autentiseringsuppgifter.

> [!NOTE]
> De andra två fälten i fönstret **arbets flödes inställningar** är för anpassade term-och bild listor. Mer information om dessa finns i guiderna [anpassade termer](../try-terms-list-api.md) eller [anpassade bilder](../try-image-list-api.md) .

### <a name="use-your-azure-account-with-the-review-apis"></a>Använd ditt Azure-konto med gransknings-API: erna

Om du vill använda din Azure-nyckel med gransknings-API: erna måste du hämta ditt resurs-ID. Gå till din Content Moderator-resurs i Azure Portal och välj bladet **Egenskaper** . Kopiera resurs-ID-värdet och klistra in det i fältet **vit listas resurs-ID (n)** på fliken **autentiseringsuppgifter** för gransknings verktyget.

![Content Moderator resurs-ID i Azure Portal](images/credentials-azure-portal-resourceid.PNG)

Om du har angett din prenumerations nyckel på båda platserna, används inte den utvärderings nyckel som medföljer kontot granska verktyg, men den är fortfarande tillgänglig.

## <a name="next-steps"></a>Nästa steg

Följ snabb starten för [gransknings verktyget](../quick-start.md) för att börja använda gransknings verktyget i scenarier för innehålls redigering.