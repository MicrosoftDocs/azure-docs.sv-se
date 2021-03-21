---
title: Skapa en översättare-resurs
titleSuffix: Azure Cognitive Services
description: I den här artikeln visas hur du skapar en Azure Cognitive Services Translator-resurs och hämtar en prenumerations nyckel och en slut punkts-URL.
services: cognitive-services
author: laujan
ms.author: lajanuar
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: how-to
ms.date: 02/16/2021
ms.openlocfilehash: a0d8532d19aff41bc5e7defb3b58462e81018749
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101712937"
---
# <a name="create-a-translator-resource"></a>Skapa en översättare-resurs

I den här artikeln får du lära dig hur du skapar en Translator-resurs i Azure Portal. [Azure Translator](translator-info-overview.md) är en molnbaserad maskin översättnings tjänst som ingår i [Azure Cognitive Services](../what-are-cognitive-services.md) -familjen av REST-API: er. Azure-resurser är instanser av tjänster som du skapar. Alla API-förfrågningar till Azure-tjänster kräver en **slut punkts** -URL och en skrivskyddad **prenumerations nyckel** för att autentisera åtkomst.

## <a name="prerequisites"></a>Förutsättningar

Du behöver ett aktivt [**Azure-konto**](https://azure.microsoft.com/free/cognitive-services/)för att komma igång.  Om du inte har någon kan du [**skapa en kostnads fri 12-månaders prenumeration**](https://azure.microsoft.com/free/).

## <a name="translator-resource-types"></a>Översättare resurs typer

Tjänsten Translator kan nås via två olika resurs typer:

* Resurs typer med **enskild tjänst** ger åtkomst till en enda tjänst-API-nyckel och slut punkt.  

* Resurs typer **med flera tjänster** ger åtkomst till flera Cognitive Services med hjälp av en enda API-nyckel och slut punkt. Den Cognitive Services resursen är för närvarande tillgänglig för följande tjänster:
  * Språk ([Translator](../translator/translator-info-overview.md), [language Understanding (LUIS)](../luis/what-is-luis.md), [textanalys](../text-analytics/overview.md))  
  * Vision ([visuellt innehåll](../computer-vision/overview.md)), ([ansikte](../face/overview.md))  
  * Beslut ([Content moderator](../content-moderator/overview.md))  

## <a name="create-your-resource"></a>Skapa din resurs

* Gå direkt till sidan [**skapa översättare**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation) i Azure Portal för att slutföra projekt informationen.

* Gå direkt till sidan [**skapa Cognitive Services**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne) i Azure Portal för att slutföra projekt informationen.

>[!TIP]
>Om du vill kan du börja **skapa** processen på Start sidan för Azure-portalen på följande sätt:
>
> 1. Gå till start sidan för [**Azure-portalen**](https://ms.portal.azure.com/#home) .
> 1. Välj ➕**skapa en resurs**  från menyn Azure-tjänster.
>1. I Sök rutan **Sök i sökrutan för Marketplace** anger och väljer du **Translator** (resurs för enskild tjänst) eller **Cognitive Services** (resurs för flera tjänster).  *Se* [välja resurs typ](#create-your-resource)ovan.
> 1. Välj **skapa** så kommer du till sidan projekt information.
><br/><br/>

## <a name="complete-your-project-and-instance-details"></a>Slutför projekt-och instans informationen

1. **Prenumeration**. Välj en av dina tillgängliga Azure-prenumerationer.

1. **Resursgrupp**. Den Azure-resurs grupp som du väljer fungerar som en virtuell behållare för den nya resursen. Du kan skapa en ny resurs grupp eller lägga till din resurs i en befintlig resurs grupp som delar samma livs cykel, behörigheter och principer.

1. **Resurs region**. Välj **Global** om inte ditt företag eller program kräver en specifik region. Translator är en icke-regional tjänst – det finns inget beroende av en speciell Azure-region. *Se* [regioner och Tillgänglighetszoner i Azure](../../availability-zones/az-overview.md).

1. **Namn**. Ange det namn som du har valt för din resurs. Det namn du väljer måste vara unikt i Azure.

> [!NOTE]
> Om du använder en Translator-funktion som kräver en anpassad domän slut punkt blir det värde som du anger i fältet namn den anpassade domän namns parametern för slut punkten.

5. **Prisnivå**. Välj en [pris nivå](https://azure.microsoft.com/pricing/details/cognitive-services/translator) som uppfyller dina behov:

   * Varje prenumeration har en kostnads fri nivå.
   * Den kostnads fria nivån har samma funktioner och funktioner som betalda planer och upphör inte.
   * Det får bara finnas en kostnads fri prenumeration per konto.</li></ul>

1. Om du har skapat en resurs för flera tjänster måste du bekräfta ytterligare användnings information via kryss rutorna.

1. Välj **Granska + skapa**.

1. Granska tjänst villkoren och välj **skapa** för att distribuera din resurs.

1. När din resurs har distribuerats väljer **du gå till resurs**.

### <a name="authentication-keys-and-endpoint-url"></a>Autentiseringsinställningar och slut punkts-URL

Alla Cognitive Services API-begäranden kräver en slut punkts-URL och en skrivskyddad nyckel för autentisering.

* **Nycklar för autentisering**. Din nyckel är en unik sträng som skickas på varje begäran till översättnings tjänsten. Du kan skicka nyckeln via en frågesträngparametern eller genom att ange den i rubriken HTTP-begäran.

* **Slut punkts-URL**. Använd den globala slut punkten i din API-begäran om du inte behöver en speciell Azure-region. *Se* [bas-URL: er](reference/v3-0-reference.md#base-urls). URL: en för den globala slut punkten är `api.cognitive.microsofttranslator.com` .

## <a name="get-your-authentication-keys-and-endpoint"></a>Hämta nycklar och slut punkt för autentisering

1. När du har distribuerat dina nya resurser väljer **du gå till resurs** eller navigera direkt till resurs sidan.
1. I vänster järnväg, under *resurs hantering*, väljer du **nycklar och slut punkt**.
1. Kopiera och klistra in prenumerations nycklar och slut punkts-URL på en lämplig plats, till exempel *Microsoft Notepad*.

:::image type="content" source="../media/cognitive-services-apis-create-account/get-cog-serv-keys.png" alt-text="Hämta nyckel och slut punkt.":::

## <a name="how-to-delete-a--resource-or-resource-group"></a>Så här tar du bort en resurs eller resurs grupp

> [!Warning]
> Om du tar bort en resurs grupp raderas även alla resurser som ingår i gruppen.

Om du vill ta bort en Cognitive Services-eller Translator-resurs kan du **ta bort resursen** eller **ta bort resurs gruppen**.

Ta bort resursen:

1. Navigera till din resurs grupp i Azure Portal.
1. Välj de resurser som ska tas bort genom att markera kryss rutan bredvid.
1. Välj **ta bort** på den övre menyn nära den högra kanten.
1. Skriv *Ja* i dialog rutan **borttagna resurser** .
1. Välj **Ta bort**.

Så här tar du bort resursgruppen:

1. Navigera till din resurs grupp i Azure Portal.
1. Välj **ta bort resurs grupp** på den översta meny raden nära den vänstra kanten.
1. Bekräfta borttagnings förfrågan genom att ange resurs gruppens namn och välja **ta bort**.

## <a name="how-to-get-started-with-translator"></a>Så här kommer du igång med Translator

I vår snabb start lär du dig hur du använder tjänsten Translator med REST API: er.

> [!div class="nextstepaction"]
> [Kom igång med Translator](quickstart-translator.md)

## <a name="more-resources"></a>Fler resurser

* [Kod exempel för Microsoft Translator](https://github.com/MicrosoftTranslator).  Kod exempel för flera språk översättare finns på GitHub.
* [Support forum för Microsoft Translator](https://www.aka.ms/TranslatorForum)
* [Kom igång med Azure (3-minuters video)](https://azure.microsoft.com/get-started/?b=16.24)