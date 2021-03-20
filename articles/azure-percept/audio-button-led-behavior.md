---
title: Ljud-och indikator beteende för Azure percept
description: Lär dig mer om knappen och LYSDIODer för Azure percept-ljud
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: conceptual
ms.date: 02/18/2021
ms.custom: template-concept
ms.openlocfilehash: 1d956e33a84b5509c16400c8f5f8e813d116411a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102095756"
---
# <a name="azure-percept-audio-button-and-led-behavior"></a>Ljud-och indikator beteende för Azure percept

I följande vägledning finns information om knappens och LED ande tillstånd för Azure percept-ljudet.

## <a name="button-behavior"></a>Knapp beteende

Du kan styra enhetens funktions sätt med hjälp av knapparna.

|Knapp status|  Beteende|
|------------|----------|
|Mikrofon|  Tryck för att stänga av/stänga av mic-arrayen. Knapp händelsen släpps när den trycks ned.|
|PTT/PT|   Tryck på PTT för att kringgå nyckelordet upptäcka-tillstånd och aktivera kommandot lyssnings tillstånd. Tryck på igen för att stoppa agentens aktiva dialog och återgå till Keyword upptäcka-tillstånd. Knapp händelsen släpps när den trycks ned. PUNKTER fungerar bara när knappen trycks ned medan agenten talar, inte när agenten lyssnar eller tänker.|

## <a name="led-behavior"></a>LED beteende

Du kan använda indikator indikatorer för att förstå vilket tillstånd som enheten är i.

|SPOLNING|   LEDande tillstånd|  Öron som status|
|---|------------|----------------| 
|L02|   1x-vit, statisk på |Slå på |
|L02|   1x-vit, 0,5 Hz blinkar|  Autentisering pågår |
|L01 & L02 & L03|   3x, blå, statisk på|     Väntar på nyckelord|
|L01 & L02 & L03|   Indikator för blinkande matris, 20fps | Lyssna eller tala|
|L01 & L02 & L03|   LED mat ris racing, 20fps|    Tänka|
|L01 & L02 & L03|   3x röd, statisk på | Mikrofon|

## <a name="next-steps"></a>Nästa steg

Fel söknings tips för din Azure percept-ljudenhet finns i den här [guiden](./troubleshoot-audio-accessory-speech-module.md).