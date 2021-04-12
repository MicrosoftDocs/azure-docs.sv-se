---
title: ta med fil
description: ta med fil
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.date: 06/04/2020
ms.subservice: language-understanding
ms.topic: include
ms.custom: include file
ms.author: aahi
ms.reviewer: roy-har
ms.openlocfilehash: a021b7c5b26e1bca1e3f623ad762c2f3ee8d6020
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "97820611"
---
Skapa pizza-appen.

1. Välj [pizza-app-for-luis-v6.jspå](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/luis/apps/pizza-app-for-luis-v6.json) för att öppna filens GitHub-sida `pizza-app-for-luis.json` .
1. Högerklicka eller länge knacka på knappen **RAW** och välj **Spara länk som** för att spara på `pizza-app-for-luis.json` din dator.
1. Logga in på [Luis-portalen](https://www.luis.ai).
1. Välj [Mina appar](https://www.luis.ai/applications).
1. På sidan **Mina appar** väljer du **+ ny app för konversation**.
1. Välj **Importera som JSON**.
1. I dialog rutan **Importera ny app** väljer du knappen **Välj fil** .
1. Välj den `pizza-app-for-luis.json` fil som du laddade ned och välj sedan **Öppna**.
1. I fältet **Importera ny app** dialog **namn** anger du ett namn för din pizza-app och väljer sedan knappen **OK** .

Appen kommer att importeras.

Om du ser en dialog ruta **för att skapa en effektiv Luis-app** stänger du dialog rutan.

## <a name="train-and-publish-the-pizza-app"></a>Träna och publicera pizza-appen

Sidan **avsikter** bör visas med en lista över syftet i pizza-appen.

[!INCLUDE [How to train](howto-train.md)]

[!INCLUDE [How to publish](howto-publish.md)]

## <a name="add-an-authoring-resource-to-the-pizza-app"></a>Lägga till en redigerings resurs i pizza-appen

1. Välj **Hantera**.
1. Välj **Azure-resurser**.
1. Välj **redigerings resurs**.
1. Välj **ändra redigering av resurs**.

Om du har en redigerings resurs anger du **klient namnet**, **prenumerations namnet** och **Luis resurs namnet** för din redigerings resurs.

Om du inte har någon redigerings resurs:

1. Välj **Skapa ny resurs**.
1. Ange ett **klient namn**, **resurs namn**, **prenumerations namn** och **namn på Azure-resurs gruppen**.

Din pizza-app är nu redo att användas.

## <a name="record-the-access-values-for-your-pizza-app"></a>Registrera åtkomst värden för din pizza-app

Om du vill använda den nya pizza-appen behöver du appens ID, redigerings nyckel och redigerings slut punkt för din pizza-app. För att få förutsägelser behöver du en separat förutsägelse slut punkt och förutsägelse nyckel.

Så här hittar du dessa värden:

1. Välj **Hantera** på sidan **avsikter** .
1. På sidan **program inställningar** registrerar du **app-ID: t**.
1. Välj **Azure-resurser**.
1. Välj **redigerings resurs**.
1. Registrera **primär nyckeln** från flikarna **Redigera resurs** och **förutsägelse resurser** . Det här värdet är din redigerings nyckel.
1. Registrera **slut punkts-URL: en**. Det här värdet är din redigerings slut punkt.
