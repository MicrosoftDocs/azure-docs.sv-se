---
title: Parkoppling och justering av meningar – Custom Translator
titleSuffix: Azure Cognitive Services
description: Under träningskörningen paras eller justeras meningar som finns i parallella dokument. Custom Translator lär sig översättningar en mening i taget, genom att läsa en mening, översättningen av den här meningen. Sedan justeras ord och fraser i dessa två meningar till varandra.
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 04/19/2021
ms.author: lajanuar
ms.topic: conceptual
ms.openlocfilehash: 43268afccbe66a21d2ce78709ba372a8a6682444
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2021
ms.locfileid: "107727158"
---
# <a name="sentence-pairing-and-alignment-in-parallel-documents"></a>Parkoppling och justering av meningar i parallella dokument

När dokument har laddats upp paras eller justeras meningar som finns i parallella dokument. Custom Translator rapporterar antalet meningar som den kunde parkoppla som justerade meningar i var och en av datauppsättningarna.

## <a name="pairing-and-alignment-process"></a>Processen för parkoppling och justering

Custom Translator lär sig översättningar av meningar en mening i taget. Den läser en mening från källtexten och sedan översättningen av den här meningen från måltexten. Sedan justeras ord och fraser i dessa två meningar till varandra. Med den här processen kan den skapa en karta över orden och fraserna i en mening till motsvarande ord och fraser i översättningen av meningen. Justeringen försöker se till att systemet tränar på meningar som är översättningar av varandra.

## <a name="pre-aligned-documents"></a>Förjusterade dokument

Om du vet att du har parallella dokument kan du åsidosätta meningsjusteringen genom att ange förjusterade textfiler. Du kan extrahera alla meningar från båda dokumenten till en textfil, ordna en mening per rad och ladda upp med ett `.align` tillägg. Tillägget `.align` signalerar Custom Translator den ska hoppa över meningsjusteringen.

För bästa resultat bör du försöka se till att du har en mening per rad i dina filer. Ha inte tecken på nyrad i en mening eftersom detta leder till dåliga justeringar.

## <a name="suggested-minimum-number-of-sentences"></a>Föreslaget minsta antal meningar

För att en träning ska lyckas visar tabellen nedan det minsta antal meningar som krävs i varje dokumenttyp.Den här begränsningen är ett säkerhetsnät för att se till att dina parallella meningar innehåller tillräckligt med unika ordförråd för att kunna träna en översättningsmodell. De allmänna riktlinjerna är att ha mer parallella meningar i domänen med mänsklig översättningskvalitet som ska ge modeller av högre kvalitet.

| Dokumenttyp   | Föreslaget minsta antal meningar | Maximalt antal meningar |
|------------|--------------------------------------------|--------------------------------|
| Utbildning   | 10 000                                     | Ingen övre gräns                 |
| Finjustering     | 500                                      | 2 500       |
| Testning    | 500                                      | 2 500  |
| Ordlista | 0                                          | 250,000                 |

> [!NOTE]
>
> - Träningen startar inte och misslyckas om det minsta antalet meningar på 10 000 för Träning inte uppfylls.
> - Justering och testning är valfria. Om du inte anger dem tar systemet bort en lämplig procentandel från Träning som ska användas för validering och testning.
> - Du kan träna en modell med endast ordlistedata. Mer information finns [i Vad är ordlista?](./what-is-dictionary.md)
> - Om ordlistan innehåller fler än 250 000 meningar är **[Document Translator](https://docs.microsoft.com/azure/cognitive-services/translator/document-translation/overview)** förmodligen ett bättre val.

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du använder [en ordlista](what-is-dictionary.md) i Custom Translator.
