---
title: Reserverat resurs namn fel
description: Beskriver hur du löser fel när du tillhandahåller ett resurs namn som innehåller ett reserverat ord.
ms.topic: troubleshooting
ms.date: 11/08/2017
ms.openlocfilehash: e76f4bf9bfee7de6e7523d69acf1388d2dd80e93
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "75477634"
---
# <a name="resolve-reserved-resource-name-errors"></a>Lös fel med reserverat resurs namn

I den här artikeln beskrivs ett fel som uppstår när du distribuerar en resurs som innehåller ett reserverat ord i sitt namn.

## <a name="symptom"></a>Symptom

När du distribuerar en resurs som är tillgänglig via en offentlig slut punkt kan du få följande fel meddelande:

```
Code=ReservedResourceName;
Message=The resource name <resource-name> or a part of the name is a trademarked or reserved word.
```

## <a name="cause"></a>Orsak

Resurser som har en offentlig slut punkt kan inte använda reserverade ord eller varumärken i namnet.

Följande ord är reserverade:

* ÅTKOMST
* AZURE
* Bestäm
* BIZSPARK
* INKÖPSOFFERTFÖRFRÅGAN
* CORTANA
* DIRECTX
* DOTNET
* DYNAMICS
* EXCEL
* UTVÄXLA
* FOREFRONT
* GROOVE
* HOLOLENS
* HYPERV
* KINECT
* LYNC
* MSDN
* O365
* OFFICE
* OFFICE365
* ONEDRIVE
* ANTECKNINGAR
* OUTLOOK
* LERA
* SHAREPOINT
* VIA
* VISION
* VISUALSTUDIO

Följande ord kan inte användas som antingen ett helt ord eller en under sträng i namnet:

* GÄST
* UTFORSKAREN
* WINDOWS
* XBOX

## <a name="solution"></a>Lösning

Ange ett namn som inte använder något av de reserverade orden.