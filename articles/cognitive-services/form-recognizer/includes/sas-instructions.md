---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 12/11/2020
ms.author: pafarley
ms.openlocfilehash: 8ad682ccd7feb9c7f089e9cf0f066cb1be8c756c
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/12/2020
ms.locfileid: "97505332"
---
Om du vill hämta SAS-URL: en för din anpassade modell inlärnings data går du till lagrings resursen i Azure Portal och väljer fliken **Storage Explorer** . Navigera till din behållare, högerklicka och välj **Hämta signatur för delad åtkomst**. Det är viktigt att hämta SAS för din behållare, inte för själva lagrings kontot. Kontrol lera att **Läs** -och **list** behörigheterna är markerade och klicka på **skapa**. Kopiera sedan värdet i **URL** -avsnittet till en tillfällig plats. Det bör ha formatet: `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`.
   > [!div class="mx-imgBorder"]
   > ![alt-text](../media/quickstarts/get-sas-url.png)