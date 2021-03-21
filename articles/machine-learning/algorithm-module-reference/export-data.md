---
title: 'Exportera data: modulreferens'
titleSuffix: Azure Machine Learning
description: Använd modulen exportera data i Azure Machine Learning designer för att spara resultat och mellanliggande data utanför Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 03/19/2021
ms.openlocfilehash: 90755aef66fa51084d83d036722187b61449a6fc
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104656914"
---
# <a name="export-data-module"></a>Exportera datamodul

I den här artikeln beskrivs en modul i Azure Machine Learning designer.

Använd den här modulen för att spara resultat, mellanliggande data och arbeta data från dina pipelines till moln lagrings mål. 

Den här modulen stöder export av data till följande moln data tjänster:

- Azure Blob-behållare
- Azure-filresurs
- Azure Data Lake Storage Gen1
- Azure Data Lake Storage Gen2
- Azure SQL-databas

Innan du exporterar dina data måste du först registrera ett data lager på din Azure Machine Learning-arbetsyta. Mer information finns i [få åtkomst till data i Azure Storage-tjänster](../how-to-access-data.md).

## <a name="how-to-configure-export-data"></a>Så här konfigurerar du export data

1. Lägg till modulen **Exportera data** till din pipeline i designern. Du hittar den här modulen i kategorin för **indata och utdata** .

1. Anslut **Exportera data** till modulen som innehåller de data som du vill exportera.

1. Välj **Exportera data** för att öppna fönstret **Egenskaper** .

1. För **data lager** väljer du ett befintligt data lager i list rutan. Du kan också skapa ett nytt data lager. Se hur du [använder åtkomst data i Azure Storage-tjänster](../how-to-access-data.md).

    > [!NOTE]
    > Export av data för en viss datatyp till en SQL Database-kolumn som har angetts som en annan datatyp stöds inte. Mål tabellen behöver inte existera först.

1. Kryss rutan **skapa utdata** igen, bestämmer om modulen ska köras för att återskapa utdata vid körning. 

    Den är som standard omarkerad, vilket innebär att om modulen har körts med samma parametrar tidigare kommer systemet att återanvända utdata från senaste körning för att minska körnings tiden. 

    Om den är markerad kommer systemet att köra modulen igen för att återskapa utdata.

1. Definiera sökvägen i data lagret där datan finns. Sökvägen är en relativ sökväg. Ta `data/testoutput` ett exempel, vilket innebär att indata för **export data** exporteras till `data/testoutput` i det data lager som du angett i **utmatnings inställningarna** för modulen.

    > [!NOTE]
    > Tomma sökvägar eller **URL-sökvägar** är inte tillåtna.


1. I **fil format** väljer du det format som data ska lagras i.
 
1. Skicka pipelinen.

## <a name="next-steps"></a>Nästa steg

Se en [uppsättning moduler som är tillgängliga](module-reference.md) för Azure Machine Learning. 
