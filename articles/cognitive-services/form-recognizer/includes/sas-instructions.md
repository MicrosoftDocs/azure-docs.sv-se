---
author: laujan
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 12/11/2020
ms.author: lajanuar
ms.openlocfilehash: 8bf78d4c2f703ee10b26b1936620f7cf23ed7c14
ms.sourcegitcommit: 3ea12ce4f6c142c5a1a2f04d6e329e3456d2bda5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/15/2021
ms.locfileid: "103467212"
---
Om du vill hämta SAS-URL: en för din anpassade modell inlärnings data går du till lagrings resursen i Azure Portal och väljer fliken **Storage Explorer** . Navigera till din behållare, högerklicka och välj **Hämta signatur för delad åtkomst**. Det är viktigt att hämta SAS för din behållare, inte för själva lagrings kontot. Kontrol lera att behörigheterna **läsa**, **skriva**, **ta bort** och **lista** är markerade och klicka på **skapa**. Kopiera sedan värdet i **URL** -avsnittet till en tillfällig plats. Det bör ha formatet: `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`.
