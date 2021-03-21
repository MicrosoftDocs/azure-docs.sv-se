---
title: 'Konvertera till CSV: modulreferens'
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder modulen konvertera till CSV i Azure Machine Learning designer för att konvertera en data uppsättning till en CSV-fil som kan återanvändas senare.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: cc58689e30e9b03e490c0871f3decd50372371fa
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93421931"
---
# <a name="convert-to-csv-module"></a>Konvertera till CSV-modul

I den här artikeln beskrivs en modul i Azure Machine Learning designer.

Använd den här modulen för att konvertera en data uppsättning till ett CSV-format som kan laddas ned, exporteras eller delas med R-eller Python-skript moduler.

### <a name="more-about-the-csv-format"></a>Mer om CSV-formatet 

CSV-formatet, som står för "kommaavgränsade värden", är ett fil format som används av många externa Machine Learning-verktyg. CSV är ett vanligt utbytes format när du arbetar med språk med öppen källkod, till exempel R eller python.

Även om du utför det mesta av ditt arbete i Azure Machine Learning, finns det tillfällen då du kan hitta det praktiskt att konvertera data uppsättningen till CSV och använda den i externa verktyg. Exempel:

+ Hämta CSV-filen för att öppna den med Excel, eller importera den till en Relations databas.  
+ Spara CSV-filen i moln lagringen och Anslut till den från Power BI för att skapa visualiseringar.  
+ Använd CSV-formatet för att förbereda data för användning i R och python. 

När du konverterar en data uppsättning till CSV sparas CSV-filen i din Azure ML-arbetsyta. Du kan använda ett Azure Storage-verktyg för att öppna och använda filen direkt. Du kan också få åtkomst till CSV i designern genom att välja kommandot **konvertera till CSV** och sedan välja ikonen histogram under fliken **utdata** i den högra panelen för att visa utdata. Du kan ladda ned CSV-filen från mappen Results till en lokal katalog.  

## <a name="how-to-configure-convert-to-csv"></a>Så här konfigurerar du Convert to CSV


1.  Lägg till modulen konvertera till CSV i din pipeline. Du hittar den här modulen i **data omvandlings** gruppen i designern. 

2. Anslut den till en modul som matar ut en data uppsättning.   
  
3.  Skicka pipelinen.

### <a name="results"></a>Resultat
  

Välj fliken **utdata** i den högra panelen i **konvertera till CSV** och välj en av dessa ikoner under **portens utdata**.  

+ **Registrera data uppsättning**: Välj ikonen och spara CSV-filen till Azure ml-arbetsytan som en separat data uppsättning. Du kan hitta data uppsättningen som en modul i modul trädet under avsnittet **mina data uppsättningar** .

 + **Visa utdata**: Välj ögon ikonen och följ anvisningarna för att bläddra i mappen **Results_dataset** och ladda ned data.csv-filen.

## <a name="next-steps"></a>Nästa steg

Se en [uppsättning moduler som är tillgängliga](module-reference.md) för Azure Machine Learning. 