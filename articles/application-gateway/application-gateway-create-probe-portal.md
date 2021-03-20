---
title: Skapa en anpassad avsökning med hjälp av portalen
titleSuffix: Azure Application Gateway
description: Lär dig hur du skapar en anpassad avsökning för Application Gateway med hjälp av portalen
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 07/09/2020
ms.author: victorh
ms.openlocfilehash: 5d2760415e4f4ef3b181f2fb69802659fec3ef66
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "95975963"
---
# <a name="create-a-custom-probe-for-application-gateway-by-using-the-portal"></a>Skapa en anpassad avsökning för Application Gateway med hjälp av portalen

> [!div class="op_single_selector"]
> * [Azure-portalen](application-gateway-create-probe-portal.md)
> * [PowerShell och Azure Resource Manager](application-gateway-create-probe-ps.md)
> * [PowerShell och den klassiska Azure-portalen](application-gateway-create-probe-classic-ps.md)

I den här artikeln lägger du till en anpassad hälso avsökning till en befintlig Application Gateway via Azure Portal. Med hjälp av hälso avsökningarna övervakar Azure Application Gateway hälsan för resurserna i backend-poolen.

## <a name="before-you-begin"></a>Innan du börjar

Om du inte redan har en Application Gateway kan du gå till [skapa en Application Gateway](./quick-create-portal.md) för att skapa en Programgateway att arbeta med.

## <a name="create-probe-for-application-gateway-v2-sku"></a>Skapa avsökning för Application Gateway v2 SKU

Avsökningar konfigureras i en två stegs process via portalen. Det första steget är att ange de värden som krävs för avsöknings konfigurationen. I det andra steget testar du Server dels hälsan med den här avsöknings konfigurationen och sparar avsökningen. 

### <a name="enter-probe-properties"></a><a name="createprobe"></a>Ange egenskaper för avsökning

1. Logga in på [Azure-portalen](https://portal.azure.com). Om du inte redan har ett konto kan du registrera dig för en [kostnads fri utvärderings period på en månad](https://azure.microsoft.com/free)

2. Klicka på Alla resurser i rutan Favoriter i Azure Portal. Klicka på Application Gateway på bladet alla resurser. Om den prenumeration du valde redan har flera resurser kan du ange partners.contoso.net i rutan Filtrera efter namn... för att enkelt få åtkomst till Application Gateway.

3. Välj **hälso avsökningar** och välj sedan **Lägg** till för att lägga till en ny hälso avsökning.

   ![Lägg till ny avsökning][4]

4. På sidan **Lägg till hälso avsökning** fyller du i den information som krävs för avsökningen och när du är klar väljer du **OK**.

   |**Inställning** | **Värde** | **Information**|
   |---|---|---|
   |**Namn**|customProbe|Det här värdet är ett eget namn som anges för den avsökning som är tillgänglig i portalen.|
   |**Protokoll**|HTTP eller HTTPS | Det protokoll som används av hälso avsökningen. |
   |**Värd**|säga contoso.com|Det här värdet är namnet på den virtuella värden (skiljer sig från värd namnet för den virtuella datorn) som körs på program servern. Avsökningen skickas till \<protocol\> :// \<host name\> :\<port\>/\<urlPath\>|
   |**Välj värd namnet från Server delens HTTP-inställningar**|Ja eller nej|Anger *värd* rubriken i avsökningen till värd namnet från de http-inställningar som denna avsökning är kopplad till. Särskilt krävs i händelse av Server delar för flera klient organisationer, till exempel Azure App Service. [Läs mer](./configuration-http-settings.md#pick-host-name-from-back-end-address)|
   |**Välj port från Server delens HTTP-inställningar**| Ja eller nej|Anger *porten* för hälso avsökningen till porten från http-inställningar som denna avsökning är kopplad till. Om du väljer Nej kan du ange en anpassad målport som ska användas |
   |**Port**| 1-65535 | Anpassad port som ska användas för hälso avsökningar | 
   |**Sökväg**|/eller en giltig sökväg|Resten av den fullständiga URL: en för den anpassade avsökningen. En giltig sökväg börjar med "/". För standard Sök vägen för http: \/ /contoso.com Använd bara "/" |
   |**Intervall (SEK)**|30|Hur ofta avsökningen körs för att kontrol lera hälso tillståndet. Vi rekommenderar inte att du anger de lägre än 30 sekunderna.|
   |**Tids gräns (SEK)**|30|Den tid som avsökningen väntar innan tids gränsen uppnåddes. Om ett giltigt svar inte tas emot inom den här tids perioden markeras avsökningen som misslyckad. Timeout-intervallet måste vara tillräckligt högt så att ett HTTP-anrop kan göras för att se till att sidan för Server dels hälsa är tillgänglig. Observera att timeout-värdet inte ska vara mer än värdet för ' Interval ' som används i den här avsöknings inställningen eller värdet för timeout för begäran i HTTP-inställningen som ska associeras med den här avsökningen.|
   |**Tröskelvärde för ej felfri**|3|Antalet misslyckade på varandra följande försök anses vara ohälsosamt. Tröskelvärdet kan anges till 1 eller mer.|
   |**Använd villkor för avsöknings matchning**|Ja eller nej|Som standard betraktas ett HTTP (S)-svar med status kod mellan 200 och 399 som felfri. Du kan ändra det godkända intervallet för Server dels svars kod eller Server dels svars text. [Läs mer](./application-gateway-probe-overview.md#probe-matching)|
   |**HTTP-inställningar**|markering från listruta|Avsökningen associeras med de HTTP-inställningar som valts här och övervakar därför hälso tillståndet för den backend-poolen som är associerad med den valda HTTP-inställningen. Den kommer att använda samma port för avsöknings förfrågan som den som används i den valda HTTP-inställningen. Du kan bara välja de HTTP-inställningar som inte är associerade med andra anpassade avsökningar. <br>Observera att endast de HTTP-inställningar som är tillgängliga för associationer som har samma protokoll som det protokoll som valts i denna avsöknings konfiguration och har samma tillstånd för *inställningen Välj värd namn från Server delens HTTP-* växel.|
   
   > [!IMPORTANT]
   > Avsökningen övervakar endast hälso tillståndet för Server delen när den är kopplad till en eller flera HTTP-inställningar. Den övervakar backend-resurser för de Server dels pooler som är associerade med de HTTP-inställningar som denna avsökning är associerad med. Avsöknings förfrågan kommer att skickas som \<protocol\> :// \<hostName\> : \<port\> / \<urlPath\> .

### <a name="test-backend-health-with-the-probe"></a>Testa Server dels hälsa med avsökningen

När du har angett egenskaper för avsökning kan du testa hälso tillståndet för backend-resurserna för att kontrol lera att avsöknings konfigurationen är korrekt och att Server dels resurserna fungerar som förväntat.

1. Välj **test** och anteckna resultatet av avsökningen. Programgatewayen testar hälsan för alla Server dels resurser i de backend-pooler som är associerade med de HTTP-inställningar som används för den här avsökningen. 

   ![Testa Server dels hälsa][5]

2. Om det finns några resurser som inte är felfria, kontrollerar du kolumnen **information** för att förstå orsaken till resursens hälso tillstånd. Om resursen har marker ATS som ohälsosamt på grund av en felaktig avsöknings konfiguration väljer du länken **gå tillbaka till avsöknings** länken och redigerar avsöknings konfigurationen. Annars, om resursen har marker ATS som ohälsosam på grund av ett problem med Server delen, löser du problemen med Server dels resursen och testar sedan Server delen igen genom att välja länken **gå tillbaka till avsökning** och välj **testa**.

   > [!NOTE]
   > Du kan välja att spara avsökningen även med felaktiga Server dels resurser, men det rekommenderas inte. Detta beror på att Application Gateway inte vidarebefordrar begär anden till backend-servrarna från backend-poolen som har avvisats vara ohälsosam av avsökningen. Om det inte finns några felfria resurser i en backend-pool kan du inte komma åt ditt program och får ett HTTP 502-fel.

   ![Visa avsöknings resultat][6]

3. Välj **Lägg till** för att spara avsökningen. 

## <a name="create-probe-for-application-gateway-v1-sku"></a>Skapa avsökning för Application Gateway v1 SKU

Avsökningar konfigureras i en två stegs process via portalen. Det första steget är att skapa avsökningen. I det andra steget lägger du till avsökningen i http-inställningarna för Server delen i Application Gateway.

### <a name="create-the-probe"></a><a name="createprobe"></a>Skapa avsökningen

1. Logga in på [Azure-portalen](https://portal.azure.com). Om du inte redan har ett konto kan du registrera dig för en [kostnads fri utvärderings period på en månad](https://azure.microsoft.com/free)

2. Klicka på **Alla resurser** i rutan Favoriter i Azure Portal. Välj Application Gateway på sidan **alla resurser** . Om den prenumeration du valde redan har flera resurser kan du ange partners.contoso.net i rutan Filtrera efter namn... för att enkelt få åtkomst till Application Gateway.

3. Välj **avsökningar** och välj sedan **Lägg** till för att lägga till en avsökning.

   ![Lägg till avsöknings blad med information ifylld][1]

4. På bladet **Lägg till hälso avsökning** fyller du i den information som krävs för avsökningen och när du är klar väljer du **OK**.

   |**Inställning** | **Värde** | **Information**|
   |---|---|---|
   |**Namn**|customProbe|Det här värdet är ett eget namn som anges för den avsökning som är tillgänglig i portalen.|
   |**Protokoll**|HTTP eller HTTPS | Det protokoll som används av hälso avsökningen. |
   |**Värd**|säga contoso.com|Det här värdet är namnet på den virtuella värden (skiljer sig från värd namnet för den virtuella datorn) som körs på program servern. Avsökningen skickas till (protokoll)://(värd namn):(port från httpsetting)/urlPath.  Detta gäller när flera platser har kon figurer ATS på Application Gateway. Om Application Gateway har kon figurer ATS för en enda plats anger du 127.0.0.1.|
   |**Välj värd namnet från Server delens HTTP-inställningar**|Ja eller nej|Anger *värd* rubriken i avsökningen till värd namnet för backend-resursen i backend-poolen som är kopplad till den http-inställning som den här avsökningen är kopplad till. Särskilt krävs i händelse av Server delar för flera klient organisationer, till exempel Azure App Service. [Läs mer](./configuration-http-settings.md#pick-host-name-from-back-end-address)|
   |**Sökväg**|/eller en giltig sökväg|Resten av den fullständiga URL: en för den anpassade avsökningen. En giltig sökväg börjar med "/". För standard Sök vägen för http: \/ /contoso.com Använd bara "/" |
   |**Intervall (SEK)**|30|Hur ofta avsökningen körs för att kontrol lera hälso tillståndet. Vi rekommenderar inte att du anger de lägre än 30 sekunderna.|
   |**Tids gräns (SEK)**|30|Den tid som avsökningen väntar innan tids gränsen uppnåddes. Om ett giltigt svar inte tas emot inom den här tids perioden markeras avsökningen som misslyckad. Timeout-intervallet måste vara tillräckligt högt så att ett HTTP-anrop kan göras för att se till att sidan för Server dels hälsa är tillgänglig. Observera att timeout-värdet inte ska vara mer än värdet för ' Interval ' som används i den här avsöknings inställningen eller värdet för timeout för begäran i HTTP-inställningen som ska associeras med den här avsökningen.|
   |**Tröskelvärde för ej felfri**|3|Antalet misslyckade på varandra följande försök anses vara ohälsosamt. Tröskelvärdet kan anges till 1 eller mer.|
   |**Använd villkor för avsöknings matchning**|Ja eller nej|Som standard betraktas ett HTTP (S)-svar med status kod mellan 200 och 399 som felfri. Du kan ändra det godkända intervallet för Server dels svars kod eller Server dels svars text. [Läs mer](./application-gateway-probe-overview.md#probe-matching)|

   > [!IMPORTANT]
   > Värd namnet är inte detsamma som server namn. Det här värdet är namnet på den virtuella värd som körs på program servern. Avsökningen skickas till \<protocol\> :// \<hostName\> :\<port from http settings\>/\<urlPath\>

### <a name="add-probe-to-the-gateway"></a>Lägg till avsökning i gatewayen

Nu när avsökningen har skapats är det dags att lägga till den i gatewayen. Avsöknings inställningar anges i Server delens http-inställningar för programgatewayen.

1. Klicka på **http-inställningar** på programgatewayen för att öppna konfigurations bladet och klicka på de aktuella http-inställningarna för Server delen som anges i fönstret.

   ![fönstret https-inställningar][2]

2. På sidan Inställningar för **appGatewayBackEndHttpSettings** markerar du kryss rutan **Använd anpassad avsökning** och väljer den avsökning som skapades i avsnittet [skapa avsökning](#createprobe) i list rutan **anpassad avsökning** .
   När du är klar klickar du på **Spara** och inställningarna tillämpas.

## <a name="next-steps"></a>Nästa steg

Visa hälso tillståndet för Server dels resurserna som fastställs av avsökningen med hjälp av [Server dels hälso visningen](./application-gateway-diagnostics.md#back-end-health).

[1]: ./media/application-gateway-create-probe-portal/figure1.png
[2]: ./media/application-gateway-create-probe-portal/figure2.png
[4]: ./media/application-gateway-create-probe-portal/figure4.png
[5]: ./media/application-gateway-create-probe-portal/figure5.png
[6]: ./media/application-gateway-create-probe-portal/figure6.png
