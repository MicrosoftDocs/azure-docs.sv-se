---
title: 'Självstudie: lägga till en anpassad domän i din slut punkt'
titleSuffix: Azure Content Delivery Network
description: Använd den här självstudien för att lägga till en anpassad domän i en Azure Content Delivery Network-slutpunkt så att ditt domän namn visas i din URL.
services: cdn
author: asudbring
manager: KumudD
ms.service: azure-cdn
ms.topic: tutorial
ms.date: 02/04/2020
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: b0e8f2b14d506eb408660b939a7c925a33215cca
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "99537753"
---
# <a name="tutorial-add-a-custom-domain-to-your-endpoint"></a>Självstudie: lägga till en anpassad domän i din slut punkt

Den här självstudien visar hur du lägger till en anpassad domän till en slutpunkt i Azure Content Delivery Network (CDN). 

Slut punkts namnet i CDN-profilen är en under domän till azureedge.net. Som standard ingår CDN-profilens domän i URL: en när innehållet levereras.

Till exempel **https://contoso.azureedge.net/photo.png**.

Azure CDN ger möjlighet att associera en anpassad domän med en CDN-slutpunkt. Det här alternativet levererar innehåll med en anpassad domän i din URL i stället för standard domänen.

I den här guiden får du lära dig att:
> [!div class="checklist"]
> - Skapa en CNAME DNS-post
> - Associera den anpassade domänen med CDN-slutpunkten
> - Verifiera den anpassade domänen

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Förutsättningar

* Innan du kan slutföra stegen i den här självstudien skapar du en CDN-profil och minst en CDN-slutpunkt. 
    * Mer information finns i [Snabbstart: Skapa en Azure CDN-profil och CDN-slutpunkt](cdn-create-new-endpoint.md).

* Om du inte har en anpassad domän kan du köpa en med en domän leverantör. 
    * Mer information finns i [köpa ett anpassat domän namn](../app-service/manage-custom-dns-buy-domain.md).

* Om du använder Azure som värd för dina [DNS-domäner](../dns/dns-overview.md)kan du delegera din anpassade domän till Azure DNS. 
    * Mer information finns i [delegera en domän till Azure DNS](../dns/dns-delegate-domain-azure-dns.md).

* Om du använder en domän leverantör för att hantera DNS-domänen fortsätter du att [skapa en CNAME DNS-post](#create-a-cname-dns-record).


## <a name="create-a-cname-dns-record"></a>Skapa en CNAME DNS-post

Innan du kan använda en anpassad domän med en Azure CDN slut punkt måste du först skapa en post för kanoniskt namn (CNAME) med Azure DNS eller din DNS-Provider för att peka på CDN-slutpunkten. 

En CNAME-post är en DNS-post som mappar ett käll domän namn till ett mål domän namn. 

För Azure CDN är källdomännamnet namnet på din anpassade domän och måldomännamnet är CDN-slutpunktens värdnamn. 

Azure CDN dirigerar trafik som är adresserad till den anpassade käll domänen till målets CDN-slutpunktens värdnamn när den verifierar CNAME-posten.

En anpassad domän och dess under domän kan associeras med en enda slut punkt i taget. 

Använd flera CNAME-poster för olika under domäner från samma anpassade domän för olika Azure-tjänster.

Du kan mappa en anpassad domän med olika under domäner till samma CDN-slutpunkt.

> [!NOTE]
> I den här självstudien används post typen CNAME. Om du använder poster av typen eller AAAA följer du stegen nedan och ersätter CNAME med den posttyp du väljer.

---
# <a name="azure-dns"></a>[**Azure DNS**](#tab/azure-dns)

Azure DNS använder Ali Asets poster för Azure-resurser inom samma prenumeration.

Så här lägger du till en aliasresurspost för din Azure CDN-slutpunkt:

1. Logga in på [Azure-portalen](https://portal.azure.com).

2. Välj **alla resurser** i den vänstra menyn och sedan Azure DNS zon för din anpassade domän.

3. I DNS-zonen för din anpassade domän väljer du **+ post uppsättning**.

4. I **Lägg till post uppsättning** anger eller väljer du följande information:

    | Inställning | Värde |
    | ------- | ----- |
    | Namn  | Ange det alias du vill använda för CDN-slutpunkten. Till exempel **www**. |
    | Typ  | Välj **CNAME**. |
    | Aliaspostuppsättning | Välj **Ja**. |
    | Aliastyp | Välj **Azure-resurs**. |
    | Välj en prenumeration | Välj din prenumeration. |
    | Azure-resurs | Välj din Azure CDN slut punkt. |

5. Ändra **TTL** -värdet för posten till ditt värde.

6. Välj **OK**.

# <a name="dns-provider"></a>[**DNS-Provider**](#tab/dns-provider)

## <a name="map-the-temporary-cdnverify-subdomain"></a>Mappa den tillfälliga underdomänen cdnverify

När du mappar en befintlig domän som är i produktion finns några saker att tänka på. 

En kort period av drift stopp för domänen kan ske när du registrerar din anpassade domän i Azure Portal.

För att undvika avbrott av webb trafik mappar du din anpassade domän till CDN-slutpunktens värdnamn med Azure **cdnverify** -underdomänen. Den här processen skapar en tillfällig CNAME-mappning. 

Den här metoden gör att användare kan komma åt din domän utan avbrott under tiden DNS-mappningen utförs. 

Om det inte är något problem med att tänka på produktions avbrott kan du mappa din anpassade domän direkt till CDN-slutpunkten. Fortsätt att [mappa den permanenta anpassade domänen](#map-the-permanent-custom-domain).

Skapa en CNAME-post med underdomänen cdnverify:

1. Logga in på webbplatsen för DNS-providern för din anpassade domän.

2. Skapa en CNAME-post för din anpassade domän och fyll i fälten enligt tabellen nedan (fältnamnen kan variera):

    | Källa                    | Typ  | Mål                     |
    |---------------------------|-------|---------------------------------|
    | cdnverify.www.contoso.com | CNAME | cdnverify.contoso.azureedge.net |

    - Källa: Ange ditt anpassade domän namn, inklusive under domänen cdnverify i följande format: **cdnverify. \<custom-domain-name>**
        - Till exempel: **cdnverify.www.contoso.com**

    - Typ: Ange eller Välj **CNAME**.

    - Mål: Ange CDN-slutpunktens värdnamn, inklusive under domänen cdnverify i följande format: **cdnverify. \<endpoint-name> . azureedge.net**. 
        - Till exempel: **cdnverify.contoso.azureedge.net**

3. Spara ändringarna.

## <a name="map-the-permanent-custom-domain"></a>Mappa den permanenta anpassade domänen

I det här avsnittet mappar du den permanenta anpassade domänen till CDN-slutpunkten. 

Skapa en CNAME-post för den anpassade domänen:

1. Logga in på webbplatsen för domänleverantören för den anpassade domänen.

2. Skapa en CNAME-post för din anpassade domän och fyll i fälten enligt tabellen nedan (fältnamnen kan variera):

    | Källa          | Typ  | Mål           |
    |-----------------|-------|-----------------------|
    | www.contoso.com | CNAME | contoso.azureedge.net |

    - Källa: Ange ditt anpassade domän namn.
        - Till exempel: **www.contoso.com**

    - Typ: Ange eller Välj **CNAME**.

    - Mål: Ange CDN-slutpunktens värdnamn i följande format: **\<endpoint-name> . azureedge.net**. 
        - Till exempel: **contoso.azureedge.net**

3. Spara ändringarna.

4. Om du tidigare skapade en tillfällig CNAME-post för underdomänen cdnverify tar du bort den. 

5. Om du använder den här anpassade domänen i produktion för första gången följer du stegen för [att associera den anpassade domänen med CDN-slutpunkten](#associate-the-custom-domain-with-your-cdn-endpoint) och [verifiera den anpassade domänen](#verify-the-custom-domain).

---
## <a name="associate-the-custom-domain-with-your-cdn-endpoint"></a>Associera den anpassade domänen med CDN-slutpunkten

När du har registrerat din anpassade domän kan du lägga till den i din CDN-slutpunkt. 


---
# <a name="azure-portal"></a>[**Azure Portal**](#tab/azure-portal)

1. Logga in på [Azure Portal](https://portal.azure.com/) och navigera till den CDN-profil som innehåller den slutpunkt du vill mappa till en anpassad domän.
    
2. På sidan **CDN-profil** väljer du den CDN-slutpunkt som du vill associera med den anpassade domänen.

    :::image type="content" source="media/cdn-map-content-to-custom-domain/cdn-endpoint-selection.png" alt-text="Val av CDN-slutpunkt" border="true":::
    
3. Välj **+ anpassad domän**. 

   :::image type="content" source="media/cdn-map-content-to-custom-domain/cdn-custom-domain-button.png" alt-text="Knappen Lägg till anpassad domän" border="true":::

4. I **Lägg till en anpassad domän**, fylls **slut punktens värdnamn** i i förväg och härleds från CDN-slutpunktens URL: **\<endpoint-hostname>** . azureedge.net. Det kan inte ändras.

5. För **Anpassat värdnamn** anger du din anpassade domän, inklusive underdomänen, som ska användas som källdomän för din CNAME-post. 
    1. Till exempel **www.contoso.com** eller **CDN.contoso.com**. **Använd inte namnet på cdnverify-underdomänen**.

    :::image type="content" source="media/cdn-map-content-to-custom-domain/cdn-add-custom-domain.png" alt-text="Lägg till anpassad domän" border="true":::

6. Välj **Lägg till**.

   Azure verifierar att det finns en CNAME-post för det anpassade domännamnet som du har angett. Om CNAME är korrekt verifieras din anpassade domän. 

   Det kan ta lite tid innan inställningarna för den nya anpassade domänen sprids till alla CDN-gränsnoder: 
    - För **Azure CDN Standard från Microsoft**-profiler slutförs spridningen vanligtvis inom 10 minuter. 
    - För **Azure CDN Standard från Akamai**-profiler slutförs spridningen vanligtvis inom en minut. 
    - För **Azure CDN Standard från Verizon**- och **Azure CDN Premium från Verizon**-profiler slutförs spridningen vanligtvis inom 10 minuter.   

# <a name="powershell"></a>[**PowerShell**](#tab/azure-powershell)

1. Logga in på Azure PowerShell:

```azurepowershell-interactive
    Connect-AzAccount

```
2. Använd [New-AzCdnCustomDomain](/powershell/module/az.cdn/new-azcdncustomdomain) för att mappa den anpassade domänen till CDN-slutpunkten. 

    * Ersätt **myendpoint8675.azureedge.net** med slut punkts-URL: en.
    * Ersätt **myendpoint8675** med namnet på CDN-slutpunkten.
    * Ersätt **www.contoso.com** med ditt anpassade domän namn.
    * Ersätt **myCDN** med ditt CDN-profilnamn.
    * Ersätt **myResourceGroupCDN** med namnet på din resurs grupp.

```azurepowershell-interactive
    $parameters = @{
        Hostname = 'myendpoint8675.azureedge.net'
        EndPointName = 'myendpoint8675'
        CustomDomainName = 'www.contoso.com'
        ProfileName = 'myCDN'
        ResourceGroupName = 'myResourceGroupCDN'
    }
    New-AzCdnCustomDomain @parameters
```

Azure verifierar att det finns en CNAME-post för det anpassade domännamnet som du har angett. Om CNAME är korrekt verifieras din anpassade domän. 

   Det kan ta lite tid innan inställningarna för den nya anpassade domänen sprids till alla CDN-gränsnoder: 

- För **Azure CDN Standard från Microsoft**-profiler slutförs spridningen vanligtvis inom 10 minuter. 
- För **Azure CDN Standard från Akamai**-profiler slutförs spridningen vanligtvis inom en minut. 
- För **Azure CDN Standard från Verizon**- och **Azure CDN Premium från Verizon**-profiler slutförs spridningen vanligtvis inom 10 minuter.   


---
## <a name="verify-the-custom-domain"></a>Verifiera den anpassade domänen

När du har slutfört registreringen av din anpassade domän kontrollerar du att den anpassade domänen refererar till CDN-slutpunkten.
 
1. Se till att du har offentligt innehåll som cachelagras på slutpunkten. Om CDN-slutpunkten till exempel är associerad med ett lagringskonto cachelagrar Azure CDN innehållet i en offentlig container. Ange att din behållare ska tillåta offentlig åtkomst och att den innehåller minst en fil för att testa den anpassade domänen.

2. Navigera till filens adress genom att använda den anpassade domänen i webbläsaren. Om din anpassade domän t. ex. är `www.contoso.com` , bör URL: en till den cachelagrade filen likna följande URL: `http://www.contoso.com/my-public-container/my-file.jpg` . Kontrol lera att resultatet är detsamma som när du öppnar CDN-slutpunkten direkt på **\<endpoint-hostname>** . azureedge.net.

## <a name="clean-up-resources"></a>Rensa resurser

---
# <a name="azure-portal"></a>[**Azure Portal**](#tab/azure-portal-cleanup)

Om du inte längre vill koppla slut punkten till en anpassad domän tar du bort den anpassade domänen genom att utföra följande steg:
 
1. I din CDN-profil väljer du slutpunkten med den anpassade domän som du vill ta bort.

2. På sidan **Slutpunkt** högerklickar du på den anpassade domänen som du vill ta bort under Anpassade domäner. Välj sedan **Ta bort** på snabbmenyn. Välj **Ja**.

   Den anpassade domänen kopplas bort från slutpunkten.

# <a name="powershell"></a>[**PowerShell**](#tab/azure-powershell-cleanup)

Om du inte längre vill koppla slut punkten till en anpassad domän tar du bort den anpassade domänen genom att utföra följande steg:

1. Använd [Remove-AzCdnCustomDomain](/powershell/module/az.cdn/remove-azcdncustomdomain) om du vill ta bort den anpassade domänen från slut punkten:

    * Ersätt **myendpoint8675** med namnet på CDN-slutpunkten.
    * Ersätt **www.contoso.com** med ditt anpassade domän namn.
    * Ersätt **myCDN** med ditt CDN-profilnamn.
    * Ersätt **myResourceGroupCDN** med namnet på din resurs grupp.


```azurepowershell-interactive
    $parameters = @{
        CustomDomainName = 'www.contoso.com'
        EndPointName = 'myendpoint8675'
        ProfileName = 'myCDN'
        ResourceGroupName = 'myResourceGroupCDN'
    }
    Remove-AzCdnCustomDomain @parameters
```

---
## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> - Skapa en CNAME DNS-post
> - Associera den anpassade domänen med CDN-slutpunkten
> - Verifiera den anpassade domänen

Gå vidare till nästa kurs om du vill lära dig hur du konfigurerar HTTPS på en anpassad Azure CDN-domän.

> [!div class="nextstepaction"]
> [Självstudiekurs: Konfigurera HTTPS på en anpassad Azure CDN-domän](cdn-custom-ssl.md)