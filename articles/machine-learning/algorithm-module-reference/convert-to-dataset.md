---
title: 'Konvertera till data uppsättning: modulreferens'
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder modulen konvertera till data uppsättning i Azure Machine Learning designer för att konvertera data indata till det interna data uppsättnings formatet.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2019
ms.openlocfilehash: 3ba69a6ddf38083e5665e20081f5f909b7100024
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/09/2021
ms.locfileid: "102508494"
---
# <a name="convert-to-dataset"></a>Konvertera till datamängd

Den här artikeln beskriver hur du använder modulen konvertera till data uppsättning i Azure Machine Learning designer för att konvertera data för en pipeline till designerns interna format.
  
Konvertering krävs inte i de flesta fall. Azure Machine Learning implicit konverterar data till det ursprungliga data uppsättnings formatet när en åtgärd utförs på data. 

Vi rekommenderar att du sparar data till data uppsättnings formatet om du har utfört någon typ av normalisering eller rensning på en uppsättning data, och du vill se till att ändringarna används i andra pipeliner.  
  
> [!NOTE]
> Konvertera till data uppsättning ändrar endast data formatet. En ny kopia av data sparas inte i arbets ytan. Om du vill spara data uppsättningen dubbelklickar du på utdataporten, väljer **Spara som data uppsättning** och anger ett nytt namn.  
  
## <a name="how-to-use-convert-to-dataset"></a>Använda konvertera till data uppsättning  

Vi rekommenderar att du använder modulen [Redigera metadata](edit-metadata.md) för att förbereda data uppsättningen innan du använder konvertera till data uppsättning. Du kan lägga till eller ändra kolumn namn, justera data typer och göra andra ändringar om det behövs.

1.  Lägg till modulen konvertera till data uppsättning i din pipeline. Du hittar den här modulen i kategorin **data omvandling** i designern. 

2. Anslut den till en modul som matar ut en data uppsättning.   

    Så länge data är [tabell](/python/api/azureml-core/azureml.data.tabulardataset), kan du konvertera dem till en data uppsättning. Detta inkluderar data som läses in genom att [Importera data](import-data.md), data som skapats genom att [ange data manuellt](enter-data-manually.md)eller data uppsättningar omvandlas via [tillämpa omvandling](apply-transformation.md).

3.  I list rutan **åtgärd** anger du om du vill göra några rensningar av data innan du sparar data uppsättningen:  
  
    - **Ingen**: Använd datan som de är.  
  
    - **SetMissingValue**: Ange ett värde för ett värde som saknas i data uppsättningen. Standard plats hållaren är frågetecknet (?), men du kan använda alternativet  **anpassat värde som saknas** för att ange ett annat värde. Om du till exempel anger **taxi** för **anpassat saknat värde**, kommer alla instanser av **taxin** i data uppsättningen att ändras till det saknade värdet.
  
    - **ReplaceValues**: Använd det här alternativet för att ange ett enda exakt värde som ska ersättas med andra exakta värden. Du kan ersätta saknade värden eller anpassade värden genom att ange **ersättnings** metoden:

      - **Saknas**: Välj det här alternativet om du vill ersätta saknade värden i indata-datauppsättningen. För **nytt värde** anger du det värde som du vill ersätta de saknade värdena med.
      - **Anpassad**: Välj det här alternativet om du vill ersätta anpassade värden i indata-datauppsättningen. För **anpassat värde** anger du det värde som du vill söka efter. Om dina data t. ex. innehåller den sträng `obs` som används som plats hållare för värden som saknas, anger du `obs` . För **nytt värde** anger du det nya värdet som ersätter den ursprungliga strängen med.
  
    Observera att **ReplaceValues** -åtgärden endast gäller för exakta matchningar. Dessa strängar påverkas till exempel inte: `obs.` , `obsolete` .  
 
  
5.  Skicka pipelinen.  

## <a name="results"></a>Resultat

+  Om du vill spara den resulterande data uppsättningen med ett nytt namn väljer du på ikonen **registrera data uppsättning** under fliken **utdata** i den högra panelen i modulen.  
  
## <a name="technical-notes"></a>Tekniska anteckningar  

-   Alla moduler som tar en data uppsättning som indata kan också ta data i CSV-filen eller TSV-filen. Innan all kod körs förbehandlas indata. För bearbetning är detsamma som att köra modulen konvertera till data uppsättning i indata.  
  
-   Du kan inte konvertera från SVMLight-formatet till en data uppsättning.  
  
-   När du anger en anpassad ersättnings åtgärd, gäller åtgärden Sök och Ersätt för fullständiga värden. Partiella matchningar är inte tillåtna. Du kan till exempel ersätta en 3 med en-1 eller med 33, men du kan inte ersätta en 3 med tvåsiffriga tal som 35.  
  
-   För anpassade ersättnings åtgärder, kommer ersättningen att Miss varnas tyst om du använder som ersättning för alla bokstäver som inte överensstämmer med den aktuella data typen för kolumnen.  

  
## <a name="next-steps"></a>Nästa steg

Se en [uppsättning moduler som är tillgängliga](module-reference.md) för Azure Machine Learning.