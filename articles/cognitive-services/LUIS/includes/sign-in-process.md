---
title: ta med fil
description: ta med fil
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 10/30/2020
ms.topic: include
ms.openlocfilehash: e006f804b8ab6411f4949424147acf567dc2ed24
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "97371327"
---
## <a name="sign-in-to-luis-portal"></a>Logga in på LUIS-portalen

[!INCLUDE [Note about portal deprecation](luis-portal-note.md)]

En ny användare av LUIS måste följa den här proceduren:

1. Logga in på [Luis-portalen](https://www.luis.ai), Välj land/region och godkänn användnings villkoren. Om du ser **Mina appar** i stället finns det redan en Luis-resurs och du bör gå vidare till skapa en app. Du har två alternativ för att registrera dig:

    * Med hjälp av en Azure-resurs (rekommenderas) – kan du länka ditt LUIS-konto till en ny eller befintlig Azure Authoring-resurs. Detta motsvarar att registrera redan migrerat. Du behöver inte gå igenom [migreringsprocessen](../luis-migration-authoring.md#what-is-migration) senare.

    * Använda en utvärderings nyckel. På så sätt kan du logga in på LUIS med en utvärderings resurs som du inte behöver konfigurera. Om du väljer det här alternativet kommer du att behöva [migrera ditt konto](../luis-migration-authoring.md#migration-steps) och länka dina program till en redigerings resurs.

1. I fönstret **Välj ett redigerings** fönster som visas letar du reda på din Azure-prenumeration och Luis Authoring-resurs. Om du inte har någon resurs kan du skapa en ny.

    :::image type="content" source="../media/luis-how-to-azure-subscription/choose-authoring-resource.png" alt-text="Välj en typ av Language Understanding redigering av resurs.":::
    
    När du skapar en ny redigerings resurs anger du följande information:
    * **Klient** organisations namn – klienten som din Azure-prenumeration är associerad med.
    * **Namn på Azure-prenumeration** – den prenumeration som ska faktureras för resursen.
    * **Namn på Azure-resurs grupp** – ett namn på en anpassad resurs grupp som du väljer eller skapar. Med resurs grupper kan du gruppera Azure-resurser för åtkomst och hantering.
    * **Azure-resurs namn** – ett anpassat namn som du väljer, används som en del av URL: en för din redigerings-och förutsägelse slut punkts fråga.
    * **Pris nivå** – pris nivån avgör den högsta transaktionen per sekund och månad.


