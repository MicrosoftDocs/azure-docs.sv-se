---
title: Beskärningsvärden
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder modulen Clip Values i Azure Machine Learning för att identifiera avvikande värden och klipp eller ersätta deras värden.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/09/2019
ms.openlocfilehash: 99fb41542dff28997438881abad71da11e927a78
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "96014898"
---
# <a name="clip-values"></a>Beskärningsvärden

I den här artikeln beskrivs en modul i Azure Machine Learning designer.

Använd modulen Clip Values för att identifiera och ersätta data värden som är över eller under en angiven tröskel med ett medelvärde, en konstant eller något annat värde.  

Du ansluter modulen till en data uppsättning som har de siffror som du vill klippa ut, väljer de kolumner som du vill arbeta med och sedan anger du ett tröskelvärde eller ett intervall med värden och en ersättnings metod. Modulen kan antingen skriva ut enbart resultaten eller de ändrade värdena som läggs till i den ursprungliga data uppsättningen.

## <a name="how-to-configure-clip-values"></a>Så här konfigurerar du klipp värden

Innan du börjar identifierar du de kolumner som du vill klippa och vilken metod som ska användas. Vi rekommenderar att du testar alla urklipps metoder på en liten delmängd av data först.

Modulen använder samma villkor och ersättnings metod för **alla** kolumner som du inkluderar i urvalet. Se därför till att undanta kolumner som du inte vill ändra.

Om du behöver tillämpa urklipps metoder eller andra villkor för vissa kolumner måste du använda en ny instans av **klipp värden** för varje uppsättning liknande kolumner.

1.  Lägg till modulen **Clip Values** i din pipeline och Anslut den till den data uppsättning som du vill ändra. Du hittar den här modulen under **dataomvandling** i kategorin **skala och minska** . 
  
1.  Använd kolumn väljaren i kolumn **listan** för att välja de kolumner som **klipp värden** ska användas för.  
  
1.  Välj något av följande alternativ i list rutan för att **ange tröskelvärden**. De här alternativen avgör hur du ställer in de övre och nedre gränserna för acceptabla värden jämfört med värden som måste klippas.  
  
    - **ClipPeaks**: när du klipper värden efter toppar kan du bara ange en övre kant. Värden som är större än det här värdet ersätts.
  
    -  **ClipSubpeaks**: när du klipper värden efter toppar kan du bara ange en nedre gränser. Värden som är mindre än det aktuella värdet ersätts.  
  
    - **ClipPeaksAndSubpeaks**: när du klipper värden efter toppar och under toppar kan du ange både övre och nedre gränser. Värden som ligger utanför intervallet ersätts. Värden som matchar gränserna ändras inte.
  
1.  Beroende på ditt val i föregående steg kan du ange följande tröskelvärden: 

    + **Nedre tröskelvärde**: visas bara om du väljer **ClipSubPeaks**
    + **Övre tröskelvärde**: visas bara om du väljer **ClipPeaks**
    + **Tröskel**: visas bara om du väljer **ClipPeaksAndSubPeaks**

    Välj antingen **konstant** eller **percentil** för varje tröskel typ.

1. Om du väljer **konstant** anger du det högsta eller lägsta värdet i text rutan. Anta till exempel att du vet att värdet 999 har använts som plats hållarens värde. Du kan välja **konstant** för det övre tröskelvärdet och ange 999 i **konstant värde för övre tröskelvärde**.
  
1. Om du väljer **percentil** begränsar du kolumnens värden till ett percentils intervall. 

    Anta till exempel att du bara vill behålla värdena i intervallet 10-80 percentil och ersätta alla andra. Välj **percentil** och skriv sedan 10 för **percentilvärdet för nedre tröskelvärde** och skriv 80 för **percentilvärdet för övre tröskelvärdet**. 

    Se avsnittet om [percentiler](#examples-for-clipping-using-percentiles) för några exempel på hur du använder percentils intervall.  
  
1.  Definiera ett ersättnings värde.

    Siffror som exakt matchar de gränser som du har angett anses ligga innanför det tillåtna värde intervallet och ersätts därför inte. Alla tal som faller utanför det angivna intervallet ersätts med ersättnings värdet. 
  
    + **Ersättnings värde för toppar**: definierar värdet som ska ersättas för alla kolumn värden som är större än det angivna tröskelvärdet.  
    + **Ersättnings värde för under toppar**: definierar värdet som ska användas som ersättning för alla kolumn värden som är mindre än det angivna tröskelvärdet.  
    + Om du använder alternativet **ClipPeaksAndSubpeaks** kan du ange separata ersättnings värden för de övre och nedre urklippta värdena.  

    Följande ersättnings värden stöds:  
  
    -   **Tröskel**: ersätter urklippta värden med det angivna tröskelvärdet.  
  
    -   **Medelvärde**: ersätter urklippta värden med medelvärdet av kolumnvärdena. Medelvärdet beräknas innan värdena klipps av.  
  
    -   **Median**: ersätter urklippta värden med median värdet i kolumnvärdena. Median värdet beräknas innan värdena klipps av.   
  
    -   **Saknas**. Ersätter urklippta värden med värdet som saknas (tomt).  
  
1.  **Lägg till indikator kolumner**: Välj det här alternativet om du vill generera en ny kolumn som anger om den angivna urklipps åtgärden ska tillämpas på data i den raden. Det här alternativet är användbart när du testar en ny uppsättning urklipps-och ersättnings värden.  
  
1. **Skriv över flagga**: Ange hur du vill att de nya värdena ska genereras. Som standard skapar **urklipps värden** en ny kolumn med topp-värden som är avgränsade med det önskade tröskelvärdet. Nya värden skriver över den ursprungliga kolumnen.  
  
    Avmarkera det här alternativet om du vill behålla den ursprungliga kolumnen och lägga till en ny kolumn med de urklippta värdena.  
  
1.  Skicka pipelinen.  
  
    Högerklicka på modulen **Clip Values** och välj **visualisera** eller Välj modulen och växla till fliken **utdata** i den högra panelen, klicka på histogrammets ikon i **portens utdata** för att granska värdena och se till att urklipps åtgärden uppfyller dina förväntningar.  
 
### <a name="examples-for-clipping-using-percentiles"></a>Exempel på Urklipp med hjälp av percentiler

För att förstå hur Urklipp i percentiler fungerar, bör du överväga en data uppsättning med 10 rader, som har en instans var och en av värdena 1-10.  
  
- Om du använder percentil som övre tröskelvärde måste värdet för den 90: e percentilen 90 procent av alla värden i data uppsättningen vara mindre än det värdet.  
  
- Om du använder percentilen som nedre tröskel, måste 10 procent av alla värden i data uppsättningen vara lägre än värdet för den tionde percentilen.  
  
1.  För **uppsättning tröskelvärden** väljer du **ClipPeaksAndSubPeaks**.  
  
1.  För **övre tröskelvärdet** väljer du **percentil** och för **percentils nummer** skriver du 90.  
  
1.  För **övre ersättnings värde** väljer du **värde som saknas**.  
  
1.  För **lägre tröskelvärde** väljer du **percentil** och för **percentils nummer** skriver du 10.  
  
1.  Om du vill ha ett **lägre ersättnings värde** väljer du **värde som saknas**.  
  
1.  Avmarkera alternativet **Skriv över flagga** och välj alternativet **Lägg till indikator kolumn**.  
  
Prova nu samma pipeline med 60 som det övre percentilvärdet och 30 som nedre percentilvärdet och Använd tröskelvärdet som ersättnings värde. I följande tabell jämförs dessa två resultat:  
  
1.  Ersätt med saknas; Övre tröskelvärde = 90; Lägre tröskelvärde = 20  
  
1.  Ersätt med tröskelvärde; Övre percentil = 60; Nedre percentil = 40  
  
|Ursprungliga data|Ersätt med saknas|Ersätt med tröskelvärdet|  
|-------------------|--------------------------|----------------------------|  
|1<br /><br /> 2<br /><br /> 3<br /><br /> 4<br /><br /> 5<br /><br /> 6<br /><br /> 7<br /><br /> 8<br /><br /> 9<br /><br /> 10|TRUE<br /><br /> TRUE<br /><br /> 3, FALSKT<br /><br /> 4, FALSKT<br /><br /> 5, FALSKT<br /><br /> 6, FALSKT<br /><br /> 7, FALSKT<br /><br /> 8, FALSKT<br /><br /> 9, FALSKT<br /><br /> TRUE|4, SANT<br /><br /> 4, SANT<br /><br /> 4, SANT<br /><br /> 4, SANT<br /><br /> 5, FALSKT<br /><br /> 6, FALSKT<br /><br /> 7, SANT<br /><br /> 7, SANT<br /><br /> 7, SANT<br /><br /> 7, SANT| 
 
## <a name="next-steps"></a>Nästa steg

Se en [uppsättning moduler som är tillgängliga](module-reference.md) för Azure Machine Learning. 
