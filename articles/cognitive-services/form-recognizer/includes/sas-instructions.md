---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 12/11/2020
ms.author: pafarley
ms.openlocfilehash: 45ac0e74733ade9801a6f3624d6200c594eff698
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/10/2021
ms.locfileid: "102623226"
---
Om du vill hämta SAS-URL: en för din anpassade modell inlärnings data går du till lagrings resursen i Azure Portal och väljer fliken **Storage Explorer** . Navigera till din behållare, högerklicka och välj **Hämta signatur för delad åtkomst**. Det är viktigt att hämta SAS för din behållare, inte för själva lagrings kontot. Kontrol lera att behörigheterna **läsa**, **skriva**, **ta bort** och **lista** är markerade och klicka på **skapa**. Kopiera sedan värdet i **URL** -avsnittet till en tillfällig plats. Det bör ha formatet: `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`.
