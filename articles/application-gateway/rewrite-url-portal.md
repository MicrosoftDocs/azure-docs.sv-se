---
title: Skriv om URL och frågesträng med Azure Application Gateway – Azure Portal
description: 'Lär dig hur du använder Azure Portal för att konfigurera en Azure Application-Gateway för att skriva om URL: en och frågesträng'
services: application-gateway
author: azhar2005
ms.service: application-gateway
ms.topic: how-to
ms.date: 4/05/2021
ms.author: azhussai
ms.openlocfilehash: b8ddc5e57b9ce56d6bce7e220bc840ba0fa43ae2
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2021
ms.locfileid: "106384765"
---
# <a name="rewrite-url-with-azure-application-gateway---azure-portal"></a>Skriv om URL med Azure Application Gateway – Azure Portal

Den här artikeln beskriver hur du använder Azure Portal för att konfigurera en instans av [Application Gateway v2-SKU](application-gateway-autoscaling-zone-redundant.md) för att skriva om URL: en.

>[!NOTE]
> Funktionen för URL-omskrivning är endast tillgänglig för Standard_v2 och WAF_v2 SKU för Application Gateway. När URL-omskrivning har kon figurer ATS på en WAF-aktiverad gateway kommer WAF-utvärderingen att äga rum på de begärda rubrikerna och URL: en för begäran. [Läs mer](rewrite-http-headers-url.md#using-url-rewrite-or-host-header-rewrite-with-web-application-firewall-waf_v2-sku).

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="before-you-begin"></a>Innan du börjar

Du måste ha en instans av Application Gateway v2 SKU för att kunna slutföra stegen i den här artikeln. Omskrivning av URL stöds inte i v1 SKU. Om du inte har installerat v2-SKU: n skapar du en instans av [Application Gateway v2-SKU](tutorial-autoscale-ps.md) innan du börjar.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på [Azure-portalen](https://portal.azure.com/) med ditt Azure-konto.

## <a name="configure-url-rewrite"></a>Konfigurera URL-omskrivning

I exemplet nedan när URL: en för begäran innehåller */article*, skrivs URL-sökvägen och URL-frågesträngen om

`contoso.com/article/123/fabrikam` -> `contoso.com/article.aspx?id=123&title=fabrikam`

1. Välj **alla resurser** och välj sedan din Application Gateway.

2. Välj **omarbetningar** i det vänstra fönstret.

3. Välj **Skriv över uppsättning**:

    :::image type="content" source="./media/rewrite-url-portal/rewrite-url-portal-1.png" alt-text="Lägg till Skriv över uppsättning":::

4. Ange ett namn för den omskrivna uppsättningen och koppla den till en regel för Routning:

    a. Ange namnet på den omarbetning som anges i rutan **namn** .
    
    b. Välj en eller flera av reglerna som anges i listan **associerade regler för routning** . Detta används för att koppla den omskrivna konfigurationen till käll lyssnaren via regeln för routning. Du kan bara välja de routningsregler som inte är kopplade till andra omskrivnings uppsättningar. Reglerna som redan är kopplade till andra omskrivnings uppsättningar är grå.
    
    c. Välj **Nästa**.
    
    :::image type="content" source="./media/rewrite-url-portal/rewrite-url-portal-2.png" alt-text="Associera till en regel":::

5. Skapa en omskrivnings regel:

    a. Välj **Lägg till omskrivning av regel**.
    
    :::image type="content" source="./media/rewrite-url-portal/rewrite-url-portal-3.png" alt-text="Skärm bild som visar lägga till en omskriven regel.":::
    
    b. Ange ett namn för omarbetnings regeln i rutan **Skriv om regel namn** . Ange ett tal i rutan **regelmall** .

6. I det här exemplet ska vi skriva om URL-sökvägen och URL-frågesträngen endast när sökvägen innehåller */article*. Det gör du genom att lägga till ett villkor för att utvärdera om URL-sökvägen innehåller */article*

    a. Välj **Lägg till villkor** och markera sedan rutan som innehåller **instruktioner för** att expandera den.
    
    b. Eftersom vi i det här exemplet vill kontrol lera mönstret */article* i URL-sökvägen väljer du **Server variabel** i listan **typ av variabel att checka** in.
    
    c. I listan **Server variabel** väljer du uri_path
    
    d. Under **SKIFT** läges känslig väljer du **Nej**.
    
    e. I listan **operator** väljer du **lika med (=)**.
    
    f. Ange ett mönster för reguljära uttryck. I det här exemplet ska vi använda mönstret `.*article/(.*)/(.*)`
    
      () används för att avbilda del strängen för senare användning i att skriva uttrycket för att skriva om URL-sökvägen. Mer information finns [här](rewrite-http-headers-url.md#capturing).

    ex. Välj **OK**.

    :::image type="content" source="./media/rewrite-url-portal/rewrite-url-portal-4.png" alt-text="Condition":::

 

7. Lägg till en åtgärd för att skriva om URL: en och URL-sökvägen

   a. Välj **URL** i listan **typ av omskrivning** .

   b. I listan **Åtgärds typ** väljer du **Ange**.

   c. Under **komponenter** väljer du **både URL-sökväg och URL-frågesträng**

   d. Ange det nya värdet för sökvägen i **värdet för URL-sökvägen**. I det här exemplet ska vi använda **/article.aspx** 

   e. I **URL-värdet för URL-fråga** anger du det nya värdet för URL-frågesträngen. I det här exemplet ska vi använda **ID = {var_uri_path_1} &title = {var_uri_path_2}**
    
    `{var_uri_path_1}` och `{var_uri_path_1}` används för att hämta de del strängar som fångats när villkoret utvärderas i det här uttrycket `.*article/(.*)/(.*)`
    
   f. Välj **OK**.

    :::image type="content" source="./media/rewrite-url-portal/rewrite-url-portal-5.png" alt-text="Åtgärd":::

8. Klicka på **skapa** för att skapa en omskrivnings uppsättning.

9. Kontrol lera att den nya omarbetnings uppsättningen visas i listan över återskrivna uppsättningar

    :::image type="content" source="./media/rewrite-url-portal/rewrite-url-portal-6.png" alt-text="Lägg till omskrivnings regel":::

## <a name="verify-url-rewrite-through-access-logs"></a>Verifiera URL-omskrivning via åtkomst loggar

Observera nedanstående fält i åtkomst loggarna för att kontrol lera om URL: en omskrivning har skett enligt förväntat.

* **originalRequestUriWithArgs**: det här fältet innehåller den ursprungliga URL: en för begäran
* **requestUri**: det här fältet innehåller URL: en efter Skriv åtgärden på Application Gateway

Mer information om alla fält i åtkomst loggarna finns [här](application-gateway-diagnostics.md#for-application-gateway-and-waf-v2-sku).

##  <a name="next-steps"></a>Nästa steg

Mer information om hur du ställer in omskrivningar för några vanliga användnings fall finns i [vanliga omskrivnings scenarier](rewrite-http-headers.md).
