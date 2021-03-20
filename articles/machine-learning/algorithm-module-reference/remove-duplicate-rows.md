---
title: 'Ta bort dubblettrader: modulreferens'
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder modulen ta bort dubbla rader i Azure Machine Learning för att ta bort potentiella dubbletter från en data uppsättning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: bf35d08128aa8a3e8f545ed7184866694219f2cb
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "90905217"
---
# <a name="remove-duplicate-rows-module"></a>Ta bort modul för dubbla rader

I den här artikeln beskrivs en modul i Azure Machine Learning designer.

Använd den här modulen för att ta bort potentiella dubbletter från en data uppsättning.

Anta till exempel att dina data ser ut som följande och representerar flera poster för patienter. 

| PatientID | Initialer| Kön|Ålder|Emot|
|----|----|----|----|----|
|1|F.M.| M| 53| Jan|
|2| F.A.M.| M| 53| Jan|
|3| F.A.M.| M| 24| Jan|
|3| F.M.| M| 24| Feb|
|4| F.M.| M| 23| Feb|
| | F.M.| M| 23| |
|5| F.A.M.| M| 53| |
|6| F.A.M.| M| NaN| |
|7| F.A.M.| M| NaN| |

Detta exempel har flera kolumner med data som kan dupliceras. Oavsett om de egentligen är dubbletter beror på dina kunskaper om dina data. 

+ Du kanske till exempel vet att många patienter har samma namn. Du kan inte eliminera dubbletter med hjälp av namn kolumner, endast kolumnen **ID** . På så sätt filtreras bara raderna med duplicerade ID-värden ut, oavsett om patienterna har samma namn eller inte.

+ Alternativt kan du välja att tillåta dubbletter i fältet ID och använda någon annan kombination av filer för att hitta unika poster, till exempel förnamn, efter namn, ålder och kön.  

Om du vill ange villkor för om en rad är en dubblett eller inte, anger du en enskild kolumn eller en uppsättning kolumner som ska användas som **nycklar**. Två rader betraktas som dubbletter endast när värdena i **alla** nyckel kolumner är lika. Om en rad saknar värde för **nycklar** betraktas de inte som dubbletter av rader. Om t. ex. kön och ålder har angetts som nycklar i tabellen ovan, är rad 6 och 7 inte duplicerade rader, förutsatt att de saknar värde i ålder.

När du kör modulen skapar den en Candidate-datauppsättning och returnerar en uppsättning rader som inte har några dubbletter i den kolumn uppsättning som du har angett.

> [!IMPORTANT]
> Käll data uppsättningen har inte ändrats. den här modulen skapar en ny data uppsättning som filtreras för att undanta dubbletter, baserat på de kriterier som du anger.

## <a name="how-to-use-remove-duplicate-rows"></a>Så här använder du ta bort dubblettrader

1. Lägg till modulen i din pipeline. Du hittar modulen **ta bort dubbla rader** under **datatransformering**, **manipulering**.  

2. Anslut den data uppsättning som du vill söka efter dubbletter av rader.

3. I fönstret **Egenskaper** , under **nyckel kolumn urval filter uttryck**, klickar du på **Starta kolumn väljaren** för att välja kolumner som ska användas vid identifiering av dubbletter.

    I det här sammanhanget betyder inte **nyckeln** en unik identifierare. Alla kolumner som du väljer med kolumn väljaren anges som **nyckel kolumner**. Alla omarkerade kolumner betraktas som icke-nyckel kolumner. Kombinationen av kolumner som du väljer som nycklar bestämmer hur poster ska vara unika. (Tänk på det som ett SQL-uttryck som använder flera likhets kopplingar.)

    Exempel:

    + "Jag vill se till att ID: n är unika": Välj endast kolumnen ID.
    + "Jag vill se till att kombinationen av förnamn, efter namn och ID är unik": Markera alla tre kolumnerna.

4. Använd kryss rutan **Behåll den första duplicerade raden** för att ange vilken rad som ska returneras när dubbletter hittas:

    + Om det här alternativet väljs returneras den första raden och andra tas bort. 
    + Om du avmarkerar det här alternativet behålls den sista dubblettraderen i resultatet och andra raderas. 

5. Skicka pipelinen.

6. Om du vill granska resultaten högerklickar du på modulen och väljer **visualisera**. 

> [!TIP]
> Om resultatet är svårt att förstå, eller om du vill undanta vissa kolumner från att beaktas, kan du ta bort kolumner med hjälp av modulen [Välj kolumner i data uppsättning](./select-columns-in-dataset.md) .

## <a name="next-steps"></a>Nästa steg

Se en [uppsättning moduler som är tillgängliga](module-reference.md) för Azure Machine Learning. 