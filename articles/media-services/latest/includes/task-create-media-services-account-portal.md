---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 11/04/2020
ms.author: inhenkel
ms.custom: portal
ms.openlocfilehash: 72857564a6b195353e7bf2a27c8369004d52d3f2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "95971350"
---
<!-- Use the portal to create a media services account. -->

## <a name="create-a-media-services-account"></a>Skapa ett Media Services-konto

1. Logga in på [Azure Portal](https://portal.azure.com/).
1. Klicka på **+ skapa en resurs**  >  **medie**  >  **Media Services**.
1. I avsnittet **skapa ett Media Services konto** anger du de värden som krävs.

    | Name | Beskrivning |
    | ---|---|
    |**Kontonamn**|Ange namnet på det nya Media Services kontot. Namnet på ett Media Services-konto består av gemena bokstäver eller siffror utan blanksteg och 3 till 24 tecken.|
    |**Prenumeration**|Om du har mer än en prenumeration väljer du en i listan med Azure-prenumerationer som du har åtkomst till.|
    |**Resursgrupp**|Välj en ny eller befintlig resurs. En resursgrupp är en samling resurser som delar livscykel, behörigheter och principer. Läs mer [här](../../../azure-resource-manager/management/overview.md#resource-groups).|
    |**Plats**|Välj den geografiska region som ska användas för att lagra media-och metadata-poster för ditt Media Services-konto. Den här regionen används för att bearbeta och strömma dina media. Endast de tillgängliga Media Services-regionerna visas i listrutan. |
    |**Lagrings konto**|Välj ett lagrings konto för att tillhandahålla Blob Storage för medie innehållet från ditt Media Services-konto. Du kan välja ett befintligt lagringskonto i samma geografiska region som ditt Media Services-konto eller skapa ett nytt lagringskonto. Ett nytt lagringskonto skapas i samma region. Reglerna för namn på lagringskonton är desamma som för Media Services-konton.<br/><br/>Du måste ha ett **primärt** lagrings konto och du kan ha valfritt antal **sekundära** lagrings konton som är kopplade till ditt Media Services-konto. Du kan använda Azure Portal för att lägga till sekundära lagrings konton. Mer information finns i [Azure Storage konton med Azure Media Services-konton](../storage-account-concept.md).<br/><br/>Media Services-kontot och alla associerade lagringskonton måste finnas i samma Azure-prenumeration. Vi rekommenderar starkt att du använder lagringskonton på samma plats som Media Services-kontot för att undvika extra kostnader för latens och utgående datatrafik.|
    |**Avancerade inställningar**| Du kan skapa ett konto med hjälp av en Systemhanterad identitet genom att välja alternativ knappen **system hantering** .  Genom att göra det här valet kan du använda Kundhanterade nycklar eller ta med din egen nyckel (BYOK) och Media Services för att aktivera betrodd lagring.  Mer information om kundens hanterade nycklar finns i [ta med din egen nyckel (Kundhanterade nycklar) med Media Services](../concept-use-customer-managed-keys-byok.md). Dessutom aktive ras även [hanterade identiteter](../concept-managed-identities.md) .

1. Välj **PIN-kod för instrumentpanelen** för att se förloppet för kontodistributionen.
1. Klicka på **Skapa** längst ned i formuläret.
