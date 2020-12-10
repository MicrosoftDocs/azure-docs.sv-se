---
title: Azure cache för Redis med Azure Private Link (för hands version)
description: Den privata Azure-slutpunkten är ett nätverks gränssnitt som ansluter privat och säkert till Azure cache för Redis som drivs av en privat Azure-länk. I den här artikeln får du lära dig hur du skapar en Azure-cache, en Azure-Virtual Network och en privat slut punkt med hjälp av Azure Portal.
author: curib
ms.author: cauribeg
ms.service: cache
ms.topic: conceptual
ms.date: 10/14/2020
ms.openlocfilehash: 22bdf93e7236ae5220a6bb7c6ead898628bb51a1
ms.sourcegitcommit: 273c04022b0145aeab68eb6695b99944ac923465
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97007593"
---
# <a name="azure-cache-for-redis-with-azure-private-link-public-preview"></a>Azure cache för Redis med Azures privata länk (offentlig för hands version)
I den här artikeln får du lära dig hur du skapar ett virtuellt nätverk och en Azure-cache för Redis-instans med en privat slut punkt med hjälp av Azure Portal. Du får också lära dig hur du lägger till en privat slut punkt i en befintlig Azure-cache för Redis-instansen.

Den privata Azure-slutpunkten är ett nätverks gränssnitt som ansluter privat och säkert till Azure cache för Redis som drivs av en privat Azure-länk. 

## <a name="prerequisites"></a>Förutsättningar
* Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/)

> [!IMPORTANT]
> Om du vill använda privata slut punkter måste Azure-cachen för Redis-instansen ha skapats efter 28 juli 2020.
> För närvarande finns det inte stöd för geo-replikering, brand Väggs regler, stöd för Portal konsol, flera slut punkter per klustrad cache, persistence till brand väggen och VNet-inmatnings cache. 
>
>

## <a name="create-a-private-endpoint-with-a-new-azure-cache-for-redis-instance"></a>Skapa en privat slut punkt med en ny Azure-cache för Redis-instans 

I det här avsnittet ska du skapa en ny Azure-cache för Redis-instans med en privat slut punkt.

### <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk 

1. Logga in på [Azure Portal](https://portal.azure.com) och välj **Skapa en resurs**.

    :::image type="content" source="media/cache-private-link/1-create-resource.png" alt-text="Välj Skapa en resurs.":::

2. Välj **nätverk** på sidan **nytt** och välj sedan **virtuellt nätverk**.

3. Välj **Lägg till** för att skapa ett virtuellt nätverk.

4. I **Skapa virtuellt nätverk** anger eller väljer du den här informationen på fliken **grundläggande** :

   | Inställning      | Föreslaget värde  | Beskrivning |
   | ------------ |  ------- | -------------------------------------------------- |
   | **Prenumeration** | List rutan och välj din prenumeration. | Den prenumeration som du vill skapa det här virtuella nätverket under. | 
   | **Resursgrupp** | List rutan och välj en resurs grupp, eller Välj **Skapa ny** och ange ett nytt resurs grupp namn. | Namnet på den resurs grupp där du vill skapa ditt virtuella nätverk och andra resurser. Genom att lägga till alla dina app-resurser i en resurs grupp kan du enkelt hantera eller ta bort dem tillsammans. | 
   | **Namn** | Ange ett virtuellt nätverks namn. | Namnet måste börja med en bokstav eller en siffra, sluta med en bokstav, en siffra eller ett under streck och får bara innehålla bokstäver, siffror, under streck, punkter eller bindestreck. | 
   | **Region** | List rutan och välj en region. | Välj en [region](https://azure.microsoft.com/regions/) nära andra tjänster som ska använda ditt virtuella nätverk. |

5. Välj fliken **IP-adresser** eller klicka på knappen **Nästa: IP-adresser** längst ned på sidan.

6. På fliken **IP-adresser** anger du **IPv4-adress utrymmet** som ett eller flera adressprefix i CIDR-format (t. ex. 192.168.1.0/24).

7. Under **undernäts namn** klickar du på **standard** för att redigera under nätets egenskaper.

8. I rutan **Redigera undernät** anger du ett **namn för under nätet** samt **adress intervallet för under nätet**. Under nätets adress intervall ska vara i CIDR-notation (t. ex. 192.168.1.0/24). Det måste finnas i det virtuella nätverkets adress utrymme.

9. Välj **Spara**.

10. Välj fliken **Granska + skapa** eller klicka på knappen **Granska + skapa** .

11. Kontrol lera att all information är korrekt och klicka på **skapa** för att etablera det virtuella nätverket.

### <a name="create-an-azure-cache-for-redis-instance-with-a-private-endpoint"></a>Skapa en Azure-cache för Redis-instans med en privat slut punkt
Följ dessa steg om du vill skapa en cache-instans.

1. Gå tillbaka till Azure Portal start sidan eller öppna menyn på panelen och välj sedan **skapa en resurs**. 
   
1. Välj **databaser** på sidan **nytt** och välj sedan **Azure cache för Redis**.

    :::image type="content" source="media/cache-private-link/2-select-cache.png" alt-text="Välj Azure-cache för Redis.":::
   
1. På sidan **ny Redis cache** konfigurerar du inställningarna för din nya cache.
   
   | Inställning      | Föreslaget värde  | Beskrivning |
   | ------------ |  ------- | -------------------------------------------------- |
   | **DNS-namn** | Ange ett globalt unikt namn. | Cache-namnet måste vara en sträng mellan 1 och 63 tecken som bara innehåller siffror, bokstäver eller bindestreck. Namnet måste börja och sluta med en siffra eller en bokstav och får inte innehålla flera bindestreck i rad. Din cacheposts *värdnamn* är *\<DNS name> . Redis.cache.Windows.net*. | 
   | **Prenumeration** | List rutan och välj din prenumeration. | Den prenumeration som du vill skapa den här nya Azure-cache för Redis-instansen för. | 
   | **Resursgrupp** | List rutan och välj en resurs grupp, eller Välj **Skapa ny** och ange ett nytt resurs grupp namn. | Namnet på resurs gruppen där du vill skapa cachen och andra resurser. Genom att lägga till alla dina app-resurser i en resurs grupp kan du enkelt hantera eller ta bort dem tillsammans. | 
   | **Plats** | List rutan och välj en plats. | Välj en [region](https://azure.microsoft.com/regions/) nära andra tjänster som ska använda din cache. |
   | **Prisnivå** | List rutan och välj en [pris nivå](https://azure.microsoft.com/pricing/details/cache/). |  Prisnivån avgör storlek, prestanda och funktioner som är tillgängliga för cacheminnet. Mer information finns i [Översikt över Azure Cache for Redis](cache-overview.md). |

1. Välj fliken **nätverk** eller klicka på knappen **nätverk** längst ned på sidan.

1. På fliken **nätverk** väljer du **privat slut punkt** för anslutnings metoden.

1. Klicka på knappen **Lägg till** för att skapa din privata slut punkt.

    :::image type="content" source="media/cache-private-link/3-add-private-endpoint.png" alt-text="I nätverk lägger du till en privat slut punkt.":::

1. På sidan **skapa en privat slut punkt** konfigurerar du inställningarna för din privata slut punkt med det virtuella nätverk och undernät som du skapade i det sista avsnittet och väljer **OK**. 

1. Välj **Nästa: fliken Avancerat** eller klicka på **Nästa: Avancerat** längst ned på sidan.

1. På fliken **Avancerat** för en Basic-eller standard-cachepost väljer du aktivera växling om du vill aktivera en icke-TLS-port.

1. På fliken **Avancerat** för Premium-cache-instans konfigurerar du inställningarna för icke-TLS-port, klustring och data beständighet.

1. Välj fliken **Nästa: Taggar** eller klicka på knappen **Nästa: Taggar** längst ned på sidan.

1. Alternativt går du till fliken **taggar** och anger namn och värde om du vill kategorisera resursen. 

1. Välj **Granska + skapa**. Du kommer till fliken Granska + skapa där Azure verifierar konfigurationen.

1. När meddelandet grön verifiering har skickats visas väljer du **skapa**.

Det tar en stund innan cacheminnet skulle skapas. Du kan övervaka förloppet på **översikts** sidan för Azure-cache för Redis. När **statusen** är **igång** är cacheminnet redo att användas. 
    
> [!IMPORTANT]
> 
> Det finns en `publicNetworkAccess` flagga som är som `Disabled` standard. 
> Den här flaggan är avsedd att tillåta att både offentlig och privat slut punkt får åtkomst till cachen om den är inställd på `Enabled` . Om detta är inställt på `Disabled` , tillåter det bara åtkomst till privat slut punkt. Du kan ställa in värdet på `Disabled` eller `Enabled` med följande patch-begäran. Redigera värdet för att avspegla vilken flagga som du vill använda för cacheminnet.
> ```http
> PATCH  https://management.azure.com/subscriptions/{subscription}/resourceGroups/{resourcegroup}/providers/Microsoft.Cache/Redis/{cache}?api-version=2020-06-01
> {    "properties": {
>        "publicNetworkAccess":"Disabled"
>    }
> }
> ```
>

> [!IMPORTANT]
> 
> För att ansluta till en klustrad cache `publicNetworkAccess` måste anges till `Disabled` och det får bara finnas en privat slut punkts anslutning. 
>

## <a name="create-a-private-endpoint-with-an-existing-azure-cache-for-redis-instance"></a>Skapa en privat slut punkt med en befintlig Azure-cache för Redis-instans 

I det här avsnittet ska du lägga till en privat slut punkt till en befintlig Azure-cache för Redis-instansen. 

### <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk 
Följ dessa steg om du vill skapa ett virtuellt nätverk.

1. Logga in på [Azure Portal](https://portal.azure.com) och välj **Skapa en resurs**.

2. Välj **nätverk** på sidan **nytt** och välj sedan **virtuellt nätverk**.

3. Välj **Lägg till** för att skapa ett virtuellt nätverk.

4. I **Skapa virtuellt nätverk** anger eller väljer du den här informationen på fliken **grundläggande** :

   | Inställning      | Föreslaget värde  | Beskrivning |
   | ------------ |  ------- | -------------------------------------------------- |
   | **Prenumeration** | List rutan och välj din prenumeration. | Den prenumeration som du vill skapa det här virtuella nätverket under. | 
   | **Resursgrupp** | List rutan och välj en resurs grupp, eller Välj **Skapa ny** och ange ett nytt resurs grupp namn. | Namnet på den resurs grupp där du vill skapa ditt virtuella nätverk och andra resurser. Genom att lägga till alla dina app-resurser i en resurs grupp kan du enkelt hantera eller ta bort dem tillsammans. | 
   | **Namn** | Ange ett virtuellt nätverks namn. | Namnet måste börja med en bokstav eller en siffra, sluta med en bokstav, en siffra eller ett under streck och får bara innehålla bokstäver, siffror, under streck, punkter eller bindestreck. | 
   | **Region** | List rutan och välj en region. | Välj en [region](https://azure.microsoft.com/regions/) nära andra tjänster som ska använda ditt virtuella nätverk. |

5. Välj fliken **IP-adresser** eller klicka på knappen **Nästa: IP-adresser** längst ned på sidan.

6. På fliken **IP-adresser** anger du **IPv4-adress utrymmet** som ett eller flera adressprefix i CIDR-format (t. ex. 192.168.1.0/24).

7. Under **undernäts namn** klickar du på **standard** för att redigera under nätets egenskaper.

8. I rutan **Redigera undernät** anger du ett **namn för under nätet** samt **adress intervallet för under nätet**. Under nätets adress intervall ska vara i CIDR-notation (t. ex. 192.168.1.0/24). Det måste finnas i det virtuella nätverkets adress utrymme.

9. Välj **Spara**.

10. Välj fliken **Granska + skapa** eller klicka på knappen **Granska + skapa** .

11. Kontrol lera att all information är korrekt och klicka på **skapa** för att etablera det virtuella nätverket.

### <a name="create-a-private-endpoint"></a>Skapa en privat slutpunkt 

Följ dessa steg om du vill skapa en privat slut punkt.

1. I Azure Portal kan du söka efter **Azure cache för Redis** och trycka på RETUR eller välja den från Sök förslagen.

    :::image type="content" source="media/cache-private-link/4-search-for-cache.png" alt-text="Sök efter Azure-cache för Redis.":::

2. Välj den cache-instans som du vill lägga till en privat slut punkt till.

3. På vänster sida av skärmen väljer du (för **hands version) privat slut punkt**.

4. Klicka på knappen **privat slut punkt** för att skapa din privata slut punkt.

    :::image type="content" source="media/cache-private-link/5-add-private-endpoint.png" alt-text="Lägg till privat slut punkt.":::

5. På **sidan Skapa en privat slut punkt** konfigurerar du inställningarna för din privata slut punkt.

   | Inställning      | Föreslaget värde  | Beskrivning |
   | ------------ |  ------- | -------------------------------------------------- |
   | **Prenumeration** | List rutan och välj din prenumeration. | Prenumerationen som den här privata slut punkten ska skapas under. | 
   | **Resursgrupp** | List rutan och välj en resurs grupp, eller Välj **Skapa ny** och ange ett nytt resurs grupp namn. | Namnet på resurs gruppen där du vill skapa din privata slut punkt och andra resurser. Genom att lägga till alla dina app-resurser i en resurs grupp kan du enkelt hantera eller ta bort dem tillsammans. | 
   | **Namn** | Ange ett namn på en privat slut punkt. | Namnet måste börja med en bokstav eller en siffra, sluta med en bokstav, en siffra eller ett under streck och får bara innehålla bokstäver, siffror, under streck, punkter eller bindestreck. | 
   | **Region** | List rutan och välj en region. | Välj en [region](https://azure.microsoft.com/regions/) nära andra tjänster som kommer att använda din privata slut punkt. |

6. Klicka på **Nästa: resurs** knappen längst ned på sidan.

7. På fliken **resurs** väljer du din prenumeration, väljer resurs typ som `Microsoft.Cache/Redis` och väljer sedan den cache som du vill ansluta den privata slut punkten till.

8. Klicka på **Nästa: konfigurations** knappen längst ned på sidan.

9. På fliken **konfiguration** väljer du det virtuella nätverk och undernät som du skapade i föregående avsnitt.

10. Klicka på **Nästa: taggs** -knappen längst ned på sidan.

11. Alternativt går du till fliken **taggar** och anger namn och värde om du vill kategorisera resursen.

12. Välj **Granska + skapa**. Du kommer till fliken **Granska + skapa** där Azure verifierar konfigurationen.

13. När meddelandet grön **verifiering har skickats** visas väljer du **skapa**.

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR

### <a name="why-cant-i-connect-to-a-private-endpoint"></a>Varför kan jag inte ansluta till en privat slut punkt?
Om cacheminnet redan är ett VNet-inmatat cacheminne kan inte privata slut punkter användas med din cache-instans. Om din cache-instans använder en funktion som inte stöds (visas nedan) kan du inte ansluta till din privata slut punkts instans. Dessutom måste cache-instanser skapas efter 27 juli för att använda privata slut punkter.

### <a name="what-features-are-not-supported-with-private-endpoints"></a>Vilka funktioner stöds inte med privata slut punkter?
Geo-replikering, brand Väggs regler, stöd för Portal konsol, flera slut punkter per klustrad cache, beständighet till brand Väggs regler och zon redundans. 

### <a name="how-can-i-change-my-private-endpoint-to-be-disabled-or-enabled-from-public-network-access"></a>Hur kan jag ändra min privata slut punkt så att den inaktive ras eller aktive ras från offentlig nätverks åtkomst?
Det finns en `publicNetworkAccess` flagga som är som `Disabled` standard. Den här flaggan är avsedd att tillåta att både offentlig och privat slut punkt får åtkomst till cachen om den är inställd på `Enabled` . Om detta är inställt på `Disabled` , tillåter det bara åtkomst till privat slut punkt. Du kan ställa in värdet på `Disabled` eller `Enabled` med följande patch-begäran. Redigera värdet för att avspegla vilken flagga som du vill använda för cacheminnet.

```http
PATCH  https://management.azure.com/subscriptions/{subscription}/resourceGroups/{resourcegroup}/providers/Microsoft.Cache/Redis/{cache}?api-version=2020-06-01
{    "properties": {
       "publicNetworkAccess":"Disabled"
   }
}
```

### <a name="are-network-security-groups-nsg-enabled-for-private-endpoints"></a>Är nätverks säkerhets grupper (NSG) aktiverade för privata slut punkter?
Nej, de är inaktiverade för privata slut punkter. Medan undernät som innehåller den privata slut punkten kan ha NSG kopplade till sig, gäller inte reglerna för trafik som bearbetas av den privata slut punkten. Du måste ha [aktiverat tvingande nätverks principer](../private-link/disable-private-endpoint-network-policy.md) för att distribuera privata slut punkter i ett undernät. NSG tillämpas fortfarande på andra arbets belastningar som finns i samma undernät. Vägar i alla klient under nät kommer att använda ett/32-prefix, men om du ändrar standardvärdet för routning krävs ett liknande UDR. 

Styr trafiken genom att använda NSG regler för utgående trafik på käll klienter. Distribuera enskilda vägar med/32-prefix för att åsidosätta privata slut punkts vägar. NSG flödes loggar och övervaknings information för utgående anslutningar stöds fortfarande och kan användas

### <a name="can-i-use-firewall-rules-with-private-endpoints"></a>Kan jag använda brand Väggs regler med privata slut punkter?
Nej, det här är en aktuell begränsning för privata slut punkter. Den privata slut punkten kommer inte att fungera korrekt om brand Väggs regler har kon figurer ATS i cachen.

### <a name="how-can-i-connect-to-a-clustered-cache"></a>Hur kan jag ansluta till en klustrad cache?
`publicNetworkAccess` måste anges till `Disabled` och det får bara finnas en privat slut punkts anslutning.

### <a name="since-my-private-endpoint-instance-is-not-in-my-vnet-how-is-it-associated-with-my-vnet"></a>Eftersom min privata slut punkts instans inte finns i mitt VNet, hur associeras den med mitt VNet?
Den är bara länkad till ditt VNet. Eftersom den inte finns i ditt VNet behöver NSG-regler inte ändras för beroende slut punkter.

### <a name="how-can-i-migrate-my-vnet-injected-cache-to-a-private-endpoint-cache"></a>Hur kan jag migrera mitt VNet-inmatade cacheminne till en privat slut punkt för slut punkter?
Du måste ta bort ditt VNet-inmatade cache och skapa en ny cache-instans med en privat slut punkt.

## <a name="next-steps"></a>Nästa steg

* Mer information om en privat Azure-länk finns i [dokumentationen till Azures privata länkar](../private-link/private-link-overview.md).
* Information om hur du jämför olika alternativ för nätverks isolering för din cache-instans finns i [Azure cache för Redis-dokumentation om nätverks isolerings alternativ](cache-network-isolation.md).