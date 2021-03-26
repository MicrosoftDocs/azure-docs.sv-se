---
title: Ljud-och indikator beteende för Azure percept
description: Lär dig mer om knappen och LYSDIODer för Azure percept-ljud
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: conceptual
ms.date: 03/25/2021
ms.openlocfilehash: fa919acb527084d19ab88b2e7895d4e6ab0b72d3
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2021
ms.locfileid: "105609079"
---
# <a name="azure-percept-audio-button-and-led-behavior"></a>Ljud-och indikator beteende för Azure percept

I följande vägledning hittar du information om knappens och LEDnas tillstånd för Azure percept-ljudenheten.

## <a name="button-behavior"></a>Knapp beteende

Använd knapparna för att styra enhetens funktions sätt.

|Knapp status|Beteende|
|------------|----------|
|Mikrofon|Tryck för att stänga av/stänga av MIC-matrisen. Knapp händelsen släpps när den trycks ned.|
|PTT/PT|Tryck på PTT för att kringgå nyckelordet upptäcka-tillstånd och aktivera kommandot lyssnings tillstånd. Tryck på igen för att stoppa agentens aktiva dialog och återgå till status för nyckelordet upptäcka. Knapp händelsen släpps när den trycks ned. PUNKTER fungerar bara när knappen trycks ned medan agenten talar, inte när agenten lyssnar eller tänker.|

## <a name="led-behavior"></a>LED beteende

Använd indikator indikatorerna för att förstå vilket tillstånd som enheten är i.

|SPOLNING|LEDande tillstånd|Öron som status|
|---|------------|----------------|
|L02|1x-vit, statisk på|Slå på |
|L02|1x-vit, 0,5 Hz blinkar|Autentisering pågår |
|L01 & L02 & L03|3x, blå, statisk på|Väntar på nyckelord|
|L01 & L02 & L03|Indikator för blinkande matris, 20fps |Lyssna eller tala|
|L01 & L02 & L03|LED mat ris racing, 20fps|Tänka|
|L01 & L02 & L03|3x röd, statisk på |Mikrofon|

## <a name="next-steps"></a>Nästa steg

Fel söknings tips för din Azure percept-ljudenhet finns i den här [guiden](./troubleshoot-audio-accessory-speech-module.md).