---
title: Terminologi – anpassad översättare
titleSuffix: Azure Cognitive Services
description: Lista över de termer som används i Custom Translator-artiklarna.
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 08/17/2020
ms.author: lajanuar
ms.topic: reference
ms.openlocfilehash: 4461f584e365a5d47e7ceee942e33bc8b101b2d2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104657832"
---
# <a name="custom-translator-terminology"></a>Terminologi för anpassad översättare

I följande tabell visas en lista över termer som du kan hitta när du arbetar med den [anpassade översättare](https://portal.customtranslator.azure.ai).

| Ord eller fras|Definition|
|------------------|-----------|
| Käll språk | Käll språket är det start språk som du vill konvertera till ett annat språk ("mål").|
| Mål språk| Mål språket är det språk som du vill att maskin översättningen ska tillhandahålla när käll språket har mottagits. |
| Monolingual-fil | En monolingual-fil har ett enda språk som inte är kopplat till en annan fil med ett annat språk. |
| Parallella filer | En parallell fil är en kombination av två filer med motsvarande text. En fil har käll språket. Den andra har mål språket.|
| Menings justering| Parallel dataset måste ha justerade meningar till meningar som representerar samma text i båda språken. I en parallell käll fil bör till exempel den första meningen i teorin mappa till den första meningen i den parallella mål filen.|
| Justerad text | En av de viktigaste stegen i fil verifieringen är att justera meningarna i de parallella dokumenten. Saker uttrycks på olika språk. Det finns även olika ord ordning för olika språk. Det här steget gör jobbet för att justera meningarna med samma innehåll så att de kan användas för utbildning. En justering för låg mening visar att det kan vara något fel med en eller båda av filerna. |
| Ord som bryts/bryts | Ord brytning är funktionen för att markera gränser mellan ord. Många skriv system använder ett utrymme för att ange avgränsningen mellan ord. Ord brytning syftar på borttagning av en synlig markör som kan ha infogats mellan ord i föregående steg. |
| Avgränsare   | Avgränsare är de sätt som en mening delas upp i segment eller begränsar marginalen mellan meningar. I engelska utrymmen kan du t. ex. avgränsa ord, kolon och semikolon avgränsade satser och punkter avgränsa meningar. |
| Träna filer | En utbildnings fil används för att lära dator översättnings systemet att mappa från ett språk (källan) till ett mål språk (målet). Den mer information som du anger, desto bättre kommer systemet att utföra. |
| Justera filer | De här filerna härleds ofta slumpmässigt från inlärnings uppsättningen (om du inte väljer någon justerings uppsättning). Meningarna markeras automatiskt och används för att finjustera systemet och se till att det fungerar korrekt. Om du vill skapa en generell översättnings modell och skapa egna finjusterade filer, se till att de är en slumpmässig uppsättning meningar över domäner |
| Testa filer| Filerna är ofta härledda filer som väljs slumpmässigt från inlärnings uppsättningen (om du inte väljer någon test uppsättning). Syftet med dessa meningar är att utvärdera översättnings modellens exakthet. Eftersom dessa meningar är de som du vill kontrol lera att systemet är korrekt översätts kanske du vill skapa en test uppsättning och ladda upp den till Translator. Genom att göra detta ser du till att dessa meningar används i systemets utvärdering (genereringen av en BLEU Poäng).   |
| Kombinations fil   | En typ av fil där källan och de översatta meningarna finns i samma fil. Fil format som stöds (. TMX,. xliff,. xlf,. ICI,. xlsx). |
| Arkivfil | En fil som innehåller andra filer. Fil format som stöds (zip, GZ, tgz).  |
| BLEU-poäng   | [Bleu](what-is-bleu-score.md) är bransch standard metoden för att utvärdera "precision" eller precisionen för översättnings modellen. Även om det finns andra utvärderings metoder använder Microsoft Translator BLEU-metoden för att rapportera precision till projekt ägare.
