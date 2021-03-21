---
title: Skriv om HTTP-begäran och svarshuvuden i Portal-Azure Application Gateway
description: Lär dig hur du använder Azure Portal för att konfigurera en Azure Application-Gateway för att skriva om HTTP-huvudena i de begär Anden och svar som passerar genom gatewayen
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/13/2019
ms.author: absha
ms.custom: mvc
ms.openlocfilehash: a77476086d6100cbaf49d54791972940cca0644f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101708942"
---
# <a name="rewrite-http-request-and-response-headers-with-azure-application-gateway---azure-portal"></a>Skriv om HTTP-begäran och svarshuvuden med Azure Application Gateway – Azure Portal

Den här artikeln beskriver hur du använder Azure Portal för att konfigurera en instans av [Application Gateway v2-SKU](./application-gateway-autoscaling-zone-redundant.md) för att skriva om HTTP-huvudena i begär Anden och svar.

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="before-you-begin"></a>Innan du börjar

Du måste ha en instans av Application Gateway v2 SKU för att kunna slutföra stegen i den här artikeln. Omskrivning av rubriker stöds inte i v1 SKU. Om du inte har installerat v2-SKU: n skapar du en instans av [Application Gateway v2-SKU](./tutorial-autoscale-ps.md) innan du börjar.

## <a name="create-required-objects"></a>Skapa nödvändiga objekt

Om du vill konfigurera omskrivning av HTTP-huvud måste du slutföra de här stegen.

1. Skapa de objekt som krävs för omskrivning av HTTP-huvud:

   - **Skriv om åtgärd**: används för att ange de rubrik fält för begäran och begär Ande som du tänker skriva om och det nya värdet för rubrikerna. Du kan associera ett eller flera omskrivnings villkor med en Skriv åtgärd.

   - **Skriv villkor**: en valfri konfiguration. Omskrivnings villkor utvärderar innehållet i HTTP (S)-begär Anden och svar. Återskrivning görs om HTTP (S)-begäran eller-svaret matchar omskrivnings villkoret.

     Om du associerar fler än ett villkor med en åtgärd sker åtgärden endast när alla villkor är uppfyllda. Med andra ord är åtgärden ett logiskt och en åtgärd.

   - **Rewrite-regel**: innehåller flera kombinationer av åtgärder för omskrivning/omskrivning.

   - **Regel ordning**: hjälper till att fastställa i vilken ordning reglerna för att skriva om ska köras. Den här konfigurationen är användbar när du har flera omskrivnings regler i en omskrivnings uppsättning. En omskrivnings regel som har ett lägre regel ordnings värde körs först. Om du tilldelar samma regel ordnings värde till två omskrivnings regler är körnings ordningen icke-deterministisk.

   - **Skriv över uppsättning**: innehåller flera omskrivnings regler som ska associeras med en regel för anslutningsbegäran.

2. Koppla den omskrivnings uppsättningen till en regel för routning. Den omskrivna konfigurationen är kopplad till käll lyssnaren via regeln för routning. När du använder en regel för grundläggande routning associeras konfigurationen för omskrivning av huvuden med en käll lyssnare och är en omskrivning av globala huvuden. När du använder en regel för Sök vägs-baserad routning definieras konfigurationen för att skriva över rubriker i sökvägen till URL-sökvägen. I så fall gäller det bara för det angivna Sök vägs området på en plats.

Du kan skapa flera Skriv-och skriv åtgärder för HTTP-huvudet och tillämpa varje skrivnings uppsättning på flera lyssnare. Men du kan endast använda en omskrivnings uppsättning för en speciell lyssnare.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på [Azure-portalen](https://portal.azure.com/) med ditt Azure-konto.

## <a name="configure-header-rewrite"></a>Konfigurera omskrivning av rubrik

I det här exemplet ska vi ändra en URL för omdirigering genom att skriva om plats rubriken i HTTP-svaret som skickas av ett Server dels program.

1. Välj **alla resurser** och välj sedan din Application Gateway.

2. Välj **omarbetningar** i det vänstra fönstret.

3. Välj **Skriv över uppsättning**:

   ![Lägg till Skriv över uppsättning](media/rewrite-http-headers-portal/add-rewrite-set.png)

4. Ange ett namn för den omskrivna uppsättningen och koppla den till en regel för Routning:

   - Ange namnet på den omarbetning som anges i rutan **namn** .
   - Välj en eller flera av reglerna som anges i listan **associerade regler för routning** . Du kan bara välja regler som inte har associerats med andra omskrivnings uppsättningar. Reglerna som redan är kopplade till andra omskrivnings uppsättningar är nedtonade.
   - Välj **Nästa**.
   
     ![Lägg till namn och Association](media/rewrite-http-headers-portal/name-and-association.png)

5. Skapa en omskrivnings regel:

   - Välj **Lägg till omskrivning av regel**.

     ![Lägg till omskrivnings regel](media/rewrite-http-headers-portal/add-rewrite-rule.png)

   - Ange ett namn för omarbetnings regeln i rutan **Skriv om regel namn** . Ange ett tal i rutan **regelmall** .

     ![Lägg till omskrivning av regel namn](media/rewrite-http-headers-portal/rule-name.png)

6. I det här exemplet skriver vi bara plats rubriken när den innehåller en referens till azurewebsites.net. Det gör du genom att lägga till ett villkor för att utvärdera om plats rubriken i svaret innehåller azurewebsites.net:

   - Välj **Lägg till villkor** och markera sedan rutan som innehåller **instruktioner för** att expandera den.

     ![Lägga till ett villkor](media/rewrite-http-headers-portal/add-condition.png)

   - Välj **http-huvud** i listan **typ av variabel att checka** in.

   - I listan **rubrik typ** väljer du **svar**.

   - Eftersom vi i det här exemplet utvärderar plats rubriken, som är en gemensam rubrik, väljer du  **gemensamt huvud** under **rubrik namn**.

   - I listan med **vanliga rubriker** väljer du **plats**.

   - Under **SKIFT** läges känslig väljer du **Nej**.

   - I listan **operator** väljer du **lika med (=)**.

   - Ange ett mönster för reguljära uttryck. I det här exemplet ska vi använda mönstret `(https?)://.*azurewebsites.net(.*)$` .

   - Välj **OK**.

     ![Konfigurera ett IF-villkor](media/rewrite-http-headers-portal/condition.png)

7. Lägg till en åtgärd för att skriva om plats rubriken:

   - I listan **Åtgärds typ** väljer du **Ange**.

   - I listan **rubrik typ** väljer du **svar**.

   - Under **rubrik namn** väljer du **gemensam rubrik**.

   - I listan med **vanliga rubriker** väljer du **plats**.

   - Ange huvudets värde. I det här exemplet ska vi använda `{http_resp_Location_1}://contoso.com{http_resp_Location_2}` som huvud värde. Det här värdet kommer att ersätta *azurewebsites.net* med *contoso.com* i plats rubriken.

   - Välj **OK**.

     ![Lägga till en åtgärd](media/rewrite-http-headers-portal/action.png)

8. Välj **skapa** för att skapa den omskrivnings uppsättning:

   ![Välj Skapa](media/rewrite-http-headers-portal/create.png)

9. Vyn för att skriva över uppsättningar öppnas. Kontrol lera att den omskrivnings uppsättning som du har skapat finns i listan över återskrivnings uppsättningar:

   ![Skriv över uppsättnings vy](media/rewrite-http-headers-portal/rewrite-set-list.png)

## <a name="next-steps"></a>Nästa steg

Mer information om hur du konfigurerar några vanliga användnings fall finns i [vanliga scenarier](./rewrite-http-headers.md)för att skriva om huvuden.