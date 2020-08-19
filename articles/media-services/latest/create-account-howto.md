---
title: Skapa ett Azure Media Services-konto
description: Den här självstudien vägleder dig genom stegen för att skapa ett Azure Media Services-konto.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 03/15/2020
ms.author: inhenkel
ms.custom: devx-track-azurecli
ms.openlocfilehash: 23a26e01c8329fa384d29443a143b173c58a2c6e
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88539145"
---
# <a name="create-a-media-services-account"></a>Skapa ett Media Services-konto

För att börja kryptera, koda, analysera, hantera och strömma medieinnehåll i Azure behöver du skapa ett Media Services-konto. Media Services-kontot måste vara associerat med ett eller flera lagringskonton. I den här artikeln beskrivs hur du skapar ett nytt Azure Media Services-konto.

> [!NOTE]
> Media Services-kontot och alla associerade lagringskonton måste finnas i samma Azure-prenumeration. Vi rekommenderar starkt att du använder lagringskonton på samma plats som Media Services-kontot för att undvika extra kostnader för latens och utgående datatrafik.

 Du kan använda antingen Azure Portal eller CLI för att skapa ett Media Services-konto. Välj fliken för den metod som du vill använda.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="portal"></a>[Portal](#tab/portal/)

## <a name="use-the-azure-portal"></a>Använda Azure-portalen

<!-- Move this section. This section should be moved to conceptual.  It doesn't belong in task based -->
Azure Portal är ett sätt att snabbt skapa ett Azure Media Services-konto. Du kan använda ditt konto för att få åtkomst till Media Services för att lagra, kryptera, koda, hantera och strömma medieinnehåll i Azure.

För närvarande kan du använda [Azure Portal](https://portal.azure.com/) för att:

* Hantera Media Services v3 [Live-händelser](live-events-outputs-concept.md), 
* Visa (inte hantera) v3- [till gångar](assets-concept.md), 
* [Hämta information om att komma åt API: er](./access-api-howto.md). 

För alla andra hanterings uppgifter (t. ex. [transformationer och jobb](transforms-jobs-concept.md) och [innehålls skydd](content-protection-overview.md)) använder du [REST API](https://aka.ms/ams-v3-rest-ref), [CLI](https://aka.ms/ams-v3-cli-ref)eller någon av de [SDK](media-services-apis-overview.md#sdks): er som stöds.
<!-- Move this section. This section should be moved to conceptual.  It doesn't belong in task based -->

### <a name="use-the-azure-portal-to-create-a-media-services-account"></a>Använd Azure Portal för att skapa ett Media Services konto

1. Logga in på [Azure Portal](https://portal.azure.com/).
1. Klicka på **+ skapa en resurs**  >  **medie**  >  **Media Services**.
1. I avsnittet **skapa ett Media Services konto** anger du de värden som krävs.

    | Name | Beskrivning |
    | ---|---|
    |**Kontonamn**|Ange namnet på det nya Media Services kontot. Namnet på ett Media Services-konto består av gemena bokstäver eller siffror utan blanksteg och 3 till 24 tecken.|
    |**Prenumeration**|Om du har mer än en prenumeration väljer du en i listan med Azure-prenumerationer som du har åtkomst till.|
    |**Resursgrupp**|Välj en ny eller befintlig resurs. En resursgrupp är en samling resurser som delar livscykel, behörigheter och principer. Läs mer [här](../../azure-resource-manager/management/overview.md#resource-groups).|
    |**Plats**|Välj den geografiska region som ska användas för att lagra media-och metadata-poster för ditt Media Services-konto. Den här regionen används för att bearbeta och strömma dina media. Endast de tillgängliga Media Services-regionerna visas i listrutan. |
    |**Lagrings konto**|Välj ett lagrings konto för att tillhandahålla Blob Storage för medie innehållet från ditt Media Services-konto. Du kan välja ett befintligt lagringskonto i samma geografiska region som ditt Media Services-konto eller skapa ett nytt lagringskonto. Ett nytt lagringskonto skapas i samma region. Reglerna för namn på lagringskonton är desamma som för Media Services-konton.<br/><br/>Du måste ha ett **primärt** lagrings konto och du kan ha valfritt antal **sekundära** lagrings konton som är kopplade till ditt Media Services-konto. Du kan använda Azure Portal för att lägga till sekundära lagrings konton. Mer information finns i [Azure Storage konton med Azure Media Services-konton](storage-account-concept.md).<br/><br/>Media Services-kontot och alla associerade lagringskonton måste finnas i samma Azure-prenumeration. Vi rekommenderar starkt att du använder lagringskonton på samma plats som Media Services-kontot för att undvika extra kostnader för latens och utgående datatrafik.|

1. Välj **PIN-kod för instrumentpanelen** för att se förloppet för kontodistributionen.
1. Klicka på **Skapa** längst ned i formuläret.

    När ditt Media Services-konto skapas, läggs en **standard** slut punkt för direkt uppspelning till på ditt konto i **stoppat** tillstånd. Om du vill börja strömma ditt innehåll och dra nytta av [dynamisk paketering](dynamic-packaging-overview.md) och [dynamisk kryptering](content-protection-overview.md), måste den strömmande slut punkten från vilken du vill strömma innehåll vara i **Kör** tillstånd. 

## <a name="cli"></a>[CLI](#tab/cli/)

## <a name="use-the-azure-cli"></a>Använda Azure CLI

<!-- NOTE: The following are in the includes file and are reused in other How To articles. All task based content should be in the includes folder with the task- prefix prepended to the file name. -->

### <a name="set-the-azure-subscription"></a>Ange Azure-prenumeration

[!INCLUDE [Set the Azure subscription with CLI](./includes/task-set-azure-subscription-cli.md)]

### <a name="create-a-resource-group"></a>Skapa en resursgrupp

[!INCLUDE [Create a resource group with CLI](./includes/task-create-resource-group-cli.md)]

### <a name="create-a-storage-account"></a>Skapa ett lagringskonto

[!INCLUDE [Create a storage account with CLI](./includes/task-create-storage-account-cli.md)]

### <a name="create-a-media-services-account"></a>Skapa ett Media Services-konto

[!INCLUDE [Create a Media Services account with CLI](./includes/task-create-media-services-account-cli.md)]

### <a name="see-also"></a>Se även

* [Azure CLI](/cli/azure/ams?view=azure-cli-latest)
* [Koppla en sekundär lagring till ett Media Services konto](/cli/azure/ams/account/storage?view=azure-cli-latest#az-ams-account-storage-add)

---

## <a name="next-steps"></a>Nästa steg

[Strömma en fil](stream-files-dotnet-quickstart.md)
