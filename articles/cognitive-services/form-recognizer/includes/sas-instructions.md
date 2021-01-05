---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 12/11/2020
ms.author: pafarley
ms.openlocfilehash: 3f4272f5445c8530e97726bd4fdca6bf662719c2
ms.sourcegitcommit: 02ed9acd4390b86c8432cad29075e2204f6b1bc3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/29/2020
ms.locfileid: "97807815"
---
Om du vill hämta SAS-URL: en för din anpassade modell inlärnings data går du till lagrings resursen i Azure Portal och väljer fliken **Storage Explorer** . Navigera till din behållare, högerklicka och välj **Hämta signatur för delad åtkomst**. Det är viktigt att hämta SAS för din behållare, inte för själva lagrings kontot. Kontrol lera att **Läs** -och **list** behörigheterna är markerade och klicka på **skapa**. Kopiera sedan värdet i **URL** -avsnittet till en tillfällig plats. Det bör ha formatet: `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`.