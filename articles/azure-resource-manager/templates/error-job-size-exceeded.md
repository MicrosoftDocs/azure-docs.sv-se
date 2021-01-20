---
title: Jobb storleken överskred felet
description: Beskriver hur du felsöker fel när jobb storleken eller mallen är för stor.
ms.topic: troubleshooting
ms.date: 01/19/2021
ms.openlocfilehash: 1fde4918aff6e3bf494876f83c5b4313b3c5f3d2
ms.sourcegitcommit: 8a74ab1beba4522367aef8cb39c92c1147d5ec13
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/20/2021
ms.locfileid: "98610411"
---
# <a name="resolve-errors-for-job-size-exceeded"></a>Fel för jobb storleken har överskridits

Den här artikeln beskriver hur du löser **JobSizeExceededException** -och **DeploymentJobSizeExceededException** -felen.

## <a name="symptom"></a>Symptom

När du distribuerar en mall får du ett fel meddelande om att distributionen har överskridit gränsen.

## <a name="cause"></a>Orsak

Du får det här felet när distributionen överskrider en av de tillåtna gränserna. Normalt visas det här felet när din mall eller det jobb som kör distributionen är för stort.

Distributions jobbet får inte överstiga 1 MB. Jobbet innehåller metadata om begäran. För stora mallar kan metadata som kombineras med mallen överskrida den tillåtna storleken för ett jobb.


Mallen får inte överstiga 4 MB. Gränsen på 4 MB gäller för mallens slutliga tillstånd när den har expanderats för resurs definitioner som använder [copy](copy-resources.md) för att skapa många instanser. Det slutliga läget innehåller också de matchade värdena för variabler och parametrar.

Andra gränser för mallen är:

* 256 parametrar
* 256 variabler
* 800 resurser (inklusive antal exemplar)
* 64-utmatnings värden
* 24 576 tecken i ett mall uttryck

## <a name="solution-1---simplify-template"></a>Lösning 1 – förenkla mall

Ditt första alternativ är att förenkla mallen. Det här alternativet fungerar när mallen distribuerar många olika resurs typer. Överväg att dela upp mallen i [länkade mallar](linked-templates.md). Dela dina resurs typer i logiska grupper och Lägg till en länkad mall för varje grupp. Om du till exempel behöver distribuera många nätverks resurser kan du flytta dessa resurser till en länkad mall.

Du kan ange andra resurser som beroende av den länkade mallen och [Hämta värden från den länkade mallens utdata](linked-templates.md#get-values-from-linked-template).

## <a name="solution-2---reduce-name-size"></a>Lösning 2 – minska namn storlek

Försök att förkorta längden på de namn du använder för [parametrar](template-parameters.md), [variabler](template-variables.md)och [utdata](template-outputs.md). När dessa värden upprepas genom kopierings slingor multipliceras ett stort namn flera gånger.

## <a name="solution-3---use-serial-copy"></a>Lösning 3 – Använd serie kopia

Överväg att ändra din kopierings slinga från [parallell till seriell bearbetning](copy-resources.md#serial-or-parallel). Använd bara det här alternativet när du misstänker att felet kommer från att distribuera ett stort antal resurser genom kopiering. Den här ändringen kan öka distributions tiden avsevärt eftersom resurserna inte distribueras parallellt.
