---
title: Språk distributioner som inte stöds – anpassad översättare
titleSuffix: Azure Cognitive Services
description: Den här artikeln visar hur du distribuerar språk par som inte stöds i Azure Cognitive Services anpassade översättare.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 04/24/2019
ms.author: lajanuar
ms.openlocfilehash: fb31388647fc6022a2e6670baf7b3e73f345c36a
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/27/2021
ms.locfileid: "98898078"
---
# <a name="unsupported-language-deployments"></a>Språkdistributioner som inte stöds

<!--Custom Translator provides the highest-quality translations possible using the latest techniques in neural machine learning. While Microsoft intends to make neural training available in all languages, there are some limitations that prevent us from being able to offer neural machine translation in all language pairs.-->  

Med den kommande indragningen av Microsoft Translator-hubben kommer Microsoft att ta bort alla modeller som för närvarande har distribuerats via hubben. Många av dina modeller har distribuerats i hubben vars språk par inte stöds i en anpassad översättare.  Vi vill inte att användare i den här situationen inte ska ha någon möjlighet att översätta sitt innehåll.

Nu har vi en process som gör att du kan distribuera dina modeller som inte stöds via den anpassade översättare.  Med den här processen kan du fortsätta att översätta innehåll med hjälp av det senaste v3-API: et.  Dessa modeller är värdar tills du väljer att avdistribuera dem, eller så blir språk paret tillgängligt i en anpassad översättare.  I den här artikeln beskrivs processen för att distribuera modeller med språk par som inte stöds.

## <a name="prerequisites"></a>Förutsättningar

För att dina modeller ska vara kandidater för distribution måste de uppfylla följande kriterier:
* Projektet som innehåller modellen måste ha migrerats från hubben till den anpassade översättare med hjälp av Migreringsverktyget.  Du hittar den [här](how-to-migrate.md)processen för att migrera projekt och arbets ytor.
* Modellen måste vara i distribuerat tillstånd när migreringen sker.  
* Språk paret för modellen måste vara ett språk par som inte stöds i en anpassad översättare.  Språk par där ett språk stöds för eller från engelska, men själva paret inte innehåller engelska, är kandidater för språk distributioner som inte stöds.  En nav modell för ett franskt till tyskt språk par betraktas till exempel som ett språk par som inte stöds, även om franska till engelska och engelska till tyska är språk par som stöds.

## <a name="process"></a>Process
När du har migrerat modeller från hubben som är kandidater för distribution kan du hitta dem genom att gå till **inställnings** sidan för din arbets yta och bläddra till slutet av sidan där du kommer att se avsnittet **utbildningar som inte stöds av Translator Hub** .  Det här avsnittet visas bara om du har projekt som uppfyller de krav som anges ovan.

![Skärm bild som visar avsnittet inlärnings verktyg som inte stöds.](media/unsupported-language-deployments/unsupported-translator-hub-trainings.jpg)

På sidan Val av **översättnings nav som inte stöds** , innehåller fliken inte **begärda utbildningar** modeller som är tillgängliga för distribution.  Välj de modeller som du vill distribuera och skicka en begäran.   Före den 30 april-distributions tids gränsen kan du välja så många modeller som du vill för distributionen.
 
![Skärm bild som visar fliken utbildningar som inte har begärts.](media/unsupported-language-deployments/unsupported-translator-hub-trainings-list.jpg)

När den har skickats är den inte längre tillgänglig på fliken **utbildningar** som inte har begärts och visas i stället på fliken **begärda utbildningar** .  Du kan när som helst visa dina begärda utbildningar.

![Migrera från hubben](media/unsupported-language-deployments/request-unsupported-trainings.jpg) 

## <a name="whats-next"></a>Nästa steg

De modeller som du har valt för distribution sparas när navet har inaktiverats och alla modeller är avdistribuerade.  Du har till och med 24 maj att skicka förfrågningar om distribution av modeller som inte stöds.  Vi kommer att distribuera dessa modeller den 15 juni vid vilken punkt de kommer att vara tillgängliga via Translator v3 API.  Dessutom kommer de att vara tillgängliga via v2-API: et till 1 juli.  

Mer information om viktiga datum i utfasningen av hubben finns [här](https://www.microsoft.com/translator/business/hub/).
När det har distribuerats kommer de normala värd avgifterna att gälla.  Se [pris](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/) information.  

Till skillnad från standard anpassade översättare-modeller är hubb modeller bara tillgängliga i en enda region, så värd avgifter för flera regioner kommer inte att gälla.  När du har distribuerat din Hubbs modell kan du när som helst ta en titt på det migrerade anpassade översättare-projektet.

## <a name="next-steps"></a>Nästa steg

- [Träna en modell](how-to-train-model.md).
- Börja använda din distribuerade anpassade översättnings modell via [Microsoft Translator text API v3](../reference/v3-0-translate.md?tabs=curl).