---
title: Recept för Azure Container instance
titleSuffix: Azure Cognitive Services
description: Lär dig hur du distribuerar Cognitive Services behållare på Azure Container instance
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: aahi
ms.openlocfilehash: 003b4411ac791898f4a7467b9b03f29aadba2fc7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "97704851"
---
# <a name="deploy-and-run-container-on-azure-container-instance"></a>Distribuera och köra container på Azure Container-instans

Med följande steg kan du enkelt skala Azure Cognitive Services-program i molnet med Azure [container instances](../../container-instances/index.yml). Skapa behållare hjälper dig att fokusera på att skapa dina program i stället för att hantera infrastrukturen. Mer information om hur du använder behållare finns i [funktioner och förmåner](../cognitive-services-container-support.md#features-and-benefits).

## <a name="prerequisites"></a>Förutsättningar

Receptet fungerar med alla Cognitive Services behållare. Du måste skapa en kognitiv tjänst resurs innan du använder receptet. Varje kognitiv tjänst som stöder behållare har en "så här installerar du"-artikeln för att installera och konfigurera tjänsten för en behållare. Vissa tjänster kräver en fil eller en uppsättning filer som indata för behållaren, det är viktigt att du förstår och har använt behållaren innan du använder den här lösningen.

* En Azure-resurs för Azure-tjänsten som du använder.
* **Webbplats slut punkts-URL** – granska din tjänsts "hur du installerar" för behållaren för att ta reda på var slut punkts-URL: en är inifrån Azure Portal och hur ett korrekt exempel på URL: en ser ut. Det exakta formatet kan ändras från tjänst till tjänst.
* **Nyckel** för kognitiva tjänster – nycklarna finns på sidan **nycklar** för Azure-resursen. Du behöver bara en av de två nycklarna. Nyckeln är en sträng med 32 alfanumeriska tecken.

* En enda Cognitive Services-behållare på din lokala värd (datorn). Kontrol lera att du kan:
  * Dra ned avbildningen med ett `docker pull` kommando.
  * Kör den lokala behållaren korrekt med alla konfigurations inställningar som krävs med ett `docker run` kommando.
  * Anropa behållarens slut punkt, få svar på HTTP-2xx och ett JSON-svar tillbaka.

Alla variabler inom vinkelparenteser, `<>` måste ersättas med dina egna värden. Den här ersättningen innehåller vinkelparenteser.

> [!IMPORTANT]
> LUIS-containern kräver en `.gz` modell fil som hämtas i vid körning. Behållaren måste kunna komma åt den här modell filen via en volym som monteras från behållar instansen. Följ dessa steg om du vill ladda upp en modell fil:
> 1. [Skapa en Azure-filresurs](../../storage/files/storage-how-to-create-file-share.md). Anteckna Azure Storage konto namn, nyckel och fil resurs namn som du behöver dem senare.
> 2. [exportera din Luis-modell (paketerad app) från Luis-portalen](../LUIS/luis-container-howto.md#export-packaged-app-from-luis). 
> 3. I Azure Portal går du till sidan **Översikt** för lagrings konto resursen och väljer **fil resurser**. 
> 4. Välj fil resurs namnet som du nyss skapade och välj sedan **Ladda upp**. Ladda sedan upp din paketerade app. 

# <a name="azure-portal"></a>[Azure-portalen](#tab/portal)

[!INCLUDE [Portal instructions for creating an ACI instance](includes/create-container-instances-resource.md)]

# <a name="cli"></a>[CLI](#tab/cli)

[!INCLUDE [CLI instructions for creating an ACI instance](../containers/includes/create-container-instances-resource-from-azure-cli.md)]

---


## <a name="use-the-container-instance"></a>Använda behållar instansen

# <a name="azure-portal"></a>[Azure-portalen](#tab/portal)

1. Välj **Översikt** och kopiera IP-adressen. Det kommer att vara en numerisk IP-adress, till exempel `55.55.55.55` .
1. Öppna en ny flik i webbläsaren och Använd IP-adressen, till exempel, `http://<IP-address>:5000 (http://55.55.55.55:5000` . Behållarens start sida visas, så att du vet att behållaren körs.

    ![Behållarens start sida](../../../includes/media/cognitive-services-containers-api-documentation/container-webpage.png)

1. Välj **Service API-Beskrivning** för att Visa Swagger-sidan för behållaren.

1. Välj någon av **post** -API: erna och välj **prova**.  Parametrarna visas inklusive indatamängden. Fyll i parametrarna.

1. Välj **Kör** för att skicka begäran till behållar instansen.

    Du har skapat och använt Cognitive Services behållare i Azure Container instance.

# <a name="cli"></a>[CLI](#tab/cli)

[!INCLUDE [API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

> [!NOTE]
> Om du kör Textanalys för Health-container använder du följande URL för att skicka frågor: `http://localhost:5000/text/analytics/v3.2-preview.1/entities/health`

---
