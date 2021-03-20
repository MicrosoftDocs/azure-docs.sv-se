---
author: cephalin
ms.service: app-service-web
ms.topic: include
ms.date: 11/03/2016
ms.author: cephalin
ms.openlocfilehash: 329db6b3fc0bd6d11e5fbac9472aa03899caec2a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "86050352"
---
Med Azure Resource Manager kan du definiera parametrar för värden som du vill ange när mallen distribueras. Mallen innehåller ett avsnitt med namnet parametrar som innehåller alla parameter värden.
Du bör definiera en parameter för de värden som varierar beroende på vilket projekt du distribuerar eller baserat på den miljö som du distribuerar till. Definiera inte parametrar för värden som alltid ska vara desamma. Varje parametervärde används i mallen för att definiera de resurser som distribueras. 

När du definierar parametrar använder du fältet **allowedValues** för att ange vilka värden som en användare kan ange under distributionen. Använd fältet **DefaultValue** för att tilldela ett värde till parametern, om inget värde anges under distributionen.

Vi kommer att beskriva varje parameter i mallen.

### <a name="sitename"></a>Namn
Namnet på den webbapp som du vill skapa.

```config
"siteName":{
  "type":"string"
}
```

### <a name="hostingplanname"></a>hostingPlanName
Namnet på App Service plan som ska användas för att vara värd för webbappen.

```config
"hostingPlanName":{
  "type":"string"
}
```

### <a name="sku"></a>sku
Pris nivån för värd planen.

```config
"sku": {
  "type": "string",
  "allowedValues": [
    "F1",
    "D1",
    "B1",
    "B2",
    "B3",
    "S1",
    "S2",
    "S3",
    "P1",
    "P2",
    "P3",
    "P4"
  ],
  "defaultValue": "S1",
  "metadata": {
    "description": "The pricing tier for the hosting plan."
  }
}
```

Mallen definierar de värden som tillåts för den här parametern och tilldelar standardvärdet (S1) om inget värde anges.

### <a name="workersize"></a>workerSize
Värd planens instans storlek (liten, medel eller stor).

```config
"workerSize":{
  "type":"string",
  "allowedValues":[
    "0",
    "1",
    "2"
  ],
  "defaultValue":"0"
}
```

Mallen definierar de värden som tillåts för den här parametern (0, 1 eller 2) och tilldelar standardvärdet (0) om inget värde anges. Värdena motsvarar små, medel stora och stora.

