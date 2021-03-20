---
title: Azure CDN slut punkt multi-Origin (för hands version)
description: Kom igång med Azure CDN slut punkt flera ursprung.
services: cdn
author: asudbring
manager: KumudD
ms.service: azure-cdn
ms.topic: how-to
ms.date: 9/06/2020
ms.author: allensu
ms.openlocfilehash: 6e433950c04c4494201b090063b17a10e54a4822
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98685779"
---
# <a name="azure-cdn-endpoint-multi-origin"></a>Azure CDN slut punkt multi-Origin

Stöd för flera ursprung eliminerar nedtid och upprättar global redundans. 

Genom att välja flera ursprung inom en Azure CDN slut punkt, förklaras risken genom att avsökningen av hälso tillståndet för varje ursprung och redundansväxling sker vid behov.

Konfigurera en eller flera ursprungs grupper och välj en standard ursprungs grupp. Varje ursprungs grupp är en samling av ett eller flera ursprung som kan ta liknande arbets belastningar.

> [!NOTE]
> Den här funktionen är för närvarande endast tillgänglig från Azure CDN från Microsoft. 

## <a name="create-the-origin-group"></a>Skapa ursprungs gruppen

1. Logga in på [Azure-portalen](https://portal.azure.com)

2. Välj din Azure CDN profil och välj sedan den slut punkt som ska konfigureras för flera ursprung.

3. Välj **ursprung** under **Inställningar** i slut punkts konfigurationen:

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-1.png" alt-text="CDN-slutpunkt" border="true":::

4. Om du vill aktivera flera ursprung behöver du minst en ursprungs grupp. Välj **skapa ursprungs grupp**:

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-2.png" alt-text="Ursprungs inställningar" border="true":::

5. I **Lägg till konfiguration av ursprungs grupp** anger eller väljer du följande information:

   | Inställning           | Värde                                                                 |
   |-------------------|-----------------------------------------------------------------------|
   | Namn på ursprungs grupp | Ange ett namn för din ursprungs grupp.                                   |
   | Avsöknings status      | Välj **Aktiverad**. </br> Azure CDN kommer att köra hälso avsökningar från olika platser i hela världen för att fastställa hälso tillståndet. Aktivera inte om den aktuella ursprungs gruppen inte är aktiv för att undvika ytterligare kostnader.
   | Sökväg för avsökning        | Sökvägen i ursprunget som används för att fastställa hälso tillståndet. |
   | Avsöknings intervall    | Välj ett avsöknings intervall på 1, 2 eller 4 minuter.                        |
   | Avsöknings protokoll    | Välj **http** eller **https**.                                         |
   | Avsöknings metod      | Välj **huvud** eller **Hämta**.                                           |
   | Standard ursprungs grupp | Markera rutan om du vill ange som standard start grupp.
    
   :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-3.png" alt-text="Lägg till ursprungs grupp" border="true":::

6. Välj **Lägg till**.

## <a name="add-multiple-origins"></a>Lägg till flera ursprung

1. I ursprungs inställningarna för din slut punkt väljer du **+ skapa ursprung**:

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-5.png" alt-text="Skapa ursprung" border="true":::

2. Ange eller Välj följande information i **Lägg till ursprung** -konfiguration:

   | Inställning           | Värde                                                                 |
   |-------------------|-----------------------------------------------------------------------|
   | Namn        | Ange ett namn för ursprunget.        |
   | Typ av ursprung | Välj **lagring**, **moln tjänst**, **webbapp** eller **anpassat ursprung**.                                   |
   | Ursprungets värdnamn        | Välj eller ange ditt ursprungs namn.  I list rutan visas alla tillgängliga ursprung av den typ som du angav i föregående inställning. Om du valde **anpassat ursprung** som typ av ursprung anger du domänen för din kund ursprungs Server. |
   | Ursprungsvärdadress    | Ange värd huvudet som du vill Azure CDN skicka med varje begäran, eller lämna standardvärdet.                        |
   | HTTP-port   | Ange HTTP-porten.                                         |
   | HTTPS-port     | Ange HTTPS-porten.                                           |
   | Prioritet    | Ange ett tal mellan 1 och 5.       |
   | Vikt      | Ange ett tal mellan 1 och 1000.   |

    > [!NOTE]
    > När ett ursprung skapas i en ursprungs grupp måste det tilldelas en prioritet och vikt. Om en ursprungs grupp endast har ett ursprung anges standard prioritet och vikt som 1. Trafiken dirigeras till de högsta prioritets ursprungen om ursprunget är felfritt. Om ett ursprung bedöms vara ohälsosamt, överförs anslutningarna till ett annat ursprung i prioritetsordning. Om två ursprung har samma prioritet, distribueras trafiken per vikt som anges för ursprunget 

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-6.png" alt-text="Lägg till ytterligare ursprung" border="true":::

3. Välj **Lägg till**.

4. Välj **Konfigurera ursprung** för att ange ursprungs Sök väg för alla ursprung:

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-7.png" alt-text="Konfigurera ursprungs Sök väg" border="true":::

5. Välj **OK**.

## <a name="configure-origins-and-origin-group-settings"></a>Konfigurera inställningar för ursprung och ursprungs grupp

När du har flera ursprung och en ursprungs grupp kan du lägga till eller ta bort ursprungen i olika grupper. Ursprung inom samma grupp bör fungera som liknande arbets belastningar. Trafiken fördelas i dessa ursprung utifrån deras friska status, prioritet och vikt värde. 

1. I ursprungs inställningarna för Azure CDN slut punkten väljer du namnet på den ursprungs grupp som du vill konfigurera:

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-8.png" alt-text="Konfigurera inställningar för ursprung och ursprungs grupp" border="true":::

2. I **gruppen uppdatera ursprungs grupp** väljer du **+ Välj ursprung**:

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-9.png" alt-text="Uppdatera ursprungs grupp" border="true":::

4. Välj det ursprung som ska läggas till i gruppen i den nedrullningsbara List rutan och välj **OK**.

5. Kontrol lera att ursprunget har lagts till i gruppen och välj sedan **Spara**:

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-10.png" alt-text="Kontrol lera att ytterligare ursprung har lagts till i gruppen" border="true":::

## <a name="remove-origin-from-origin-group"></a>Ta bort ursprung från gruppen ursprung

1. I ursprungs inställningarna för Azure CDN slut punkten väljer du namnet på ursprungs gruppen:

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-8.png" alt-text="Ta bort ursprung från grupp" border="true":::

2. Om du vill ta bort ett ursprung från gruppen original, väljer du pappers korgs ikonen bredvid ursprunget och väljer **Spara**:

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-11.png" alt-text="Uppdatera ursprungs grupp ta bort ursprung" border="true":::

## <a name="override-origin-group-with-rules-engine"></a>Åsidosätt ursprungs grupp med regel motor

Anpassa hur trafiken distribueras till olika ursprungs grupper med hjälp av standard regel motorn.

Distribuera trafik till en annan grupp baserat på URL: en för begäran.

1. I CDN-slutpunkten väljer du **regel motor** under **Inställningar**:

:::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-12.png" alt-text="Regel motor" border="true":::

2. Välj **+ Lägg till regel**.

3. Ange ett namn för regeln i **namn**.

4. Välj **+ villkor** och välj sedan **URL-sökväg**.

5. I **operatorns** listruta väljer du **innehåller**.

6. I **värde** anger du **/images**.

7. Välj **+ Lägg till åtgärd** och välj sedan **åsidosättande av ursprungs grupp**.

8. I **gruppen original** väljer du gruppen ursprung i den nedrullningsbara rutan.

:::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-13.png" alt-text="Regel motor villkor" border="true":::

För alla inkommande begär Anden om URL-sökvägen innehåller **/images**, kommer begäran att tilldelas ursprungs gruppen i åtgärds avsnittet **(myorigingroup)**. 

## <a name="next-steps"></a>Nästa steg
I den här artikeln har du aktiverat Azure CDN slut punkt multi-Origin.

Mer information om Azure CDN och de andra Azure-tjänsterna som nämns i den här artikeln finns i:

* [Azure CDN](./cdn-overview.md)
* [Jämför Azure CDN produkt funktion](./cdn-features.md)
