---
title: Jobb storleken överskred felet
description: Beskriver hur du felsöker fel när jobb storleken eller mallen är för stor.
ms.topic: troubleshooting
ms.date: 03/23/2021
ms.openlocfilehash: b39a0bba15e73bab1a85cbd9e36efebf82d6cf42
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104889373"
---
# <a name="resolve-errors-for-job-size-exceeded"></a>Fel för jobb storleken har överskridits

Den här artikeln beskriver hur du löser **JobSizeExceededException** -och **DeploymentJobSizeExceededException** -felen.

## <a name="symptom"></a>Symptom

När du distribuerar en mall får du ett fel meddelande om att distributionen har överskridit gränsen.

## <a name="cause"></a>Orsak

Du får det här felet när distributionen överskrider en av de tillåtna gränserna. Normalt visas det här felet när din mall eller det jobb som kör distributionen är för stort.

Distributions jobbet får inte överstiga 1 MB. Jobbet innehåller metadata om förfrågan. För stora mallar kan metadata tillsammans med mallen överskrida den tillåtna storleksgränsen för jobb.

Mallen får inte överstiga 4 MB. Gränsen på 4 MB gäller för mallens slutliga tillstånd när den har expanderats för resurs definitioner som använder [copy](copy-resources.md) för att skapa många instanser. Sluttillståndet omfattar även matchade värden för variabler och parametrar.

Andra gränser för mallen är:

* 256 parametrar
* 256 variabler
* 800 resurser (inklusive antal exemplar)
* 64-utmatnings värden
* 24 576 tecken i ett mall uttryck

När du använder kopierings slingor för att distribuera resurser ska du inte använda loop-namnet som ett beroende:

```json
dependsOn: [ "nicLoop" ]
```

Använd i stället den instans av resursen från den loop som du behöver vara beroende av. Exempel:

```json
dependsOn: [
    "[resourceId('Microsoft.Network/networkInterfaces', concat('nic-', copyIndex()))]"
]
```

## <a name="solution-1---simplify-template"></a>Lösning 1 – förenkla mall

Ditt första alternativ är att förenkla mallen. Det här alternativet fungerar när mallen distribuerar många olika resurs typer. Överväg att dela upp mallen i [länkade mallar](linked-templates.md). Dela dina resurs typer i logiska grupper och Lägg till en länkad mall för varje grupp. Om du till exempel behöver distribuera många nätverks resurser kan du flytta dessa resurser till en länkad mall.

Du kan ange andra resurser som beroende av den länkade mallen och [Hämta värden från den länkade mallens utdata](linked-templates.md#get-values-from-linked-template).

## <a name="solution-2---reduce-name-size"></a>Lösning 2 – minska namn storlek

Försök att förkorta längden på de namn du använder för [parametrar](template-parameters.md), [variabler](template-variables.md)och [utdata](template-outputs.md). När dessa värden upprepas genom kopierings slingor multipliceras ett stort namn flera gånger.