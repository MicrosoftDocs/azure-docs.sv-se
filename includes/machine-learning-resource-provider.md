---
author: blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 01/09/2020
ms.author: larryfr
ms.openlocfilehash: 8fd774f8a3a73ceaffa7902b35e1b1dff12ef5af
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "75893978"
---
När du skapar en Azure Machine Learning arbets yta eller en resurs som används av arbets ytan kan du få ett fel meddelande som liknar följande meddelanden:

* `No registered resource provider found for location {location}`
* `The subscription is not registered to use namespace {resource-provider-namespace}`

De flesta resurs leverantörer registreras automatiskt, men inte alla. Om du får det här meddelandet måste du registrera den angivna providern.

Information om hur du registrerar resurs leverantörer finns i [lösa fel för registrering av resurs leverantör](../articles/azure-resource-manager/templates/error-register-resource-provider.md).