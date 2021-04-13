---
title: Självstudie – Lägg till anpassad domän i din Azure-konfiguration för front dörren
description: I den här självstudien får du lära dig hur du implementerar en anpassad domän i Azure Front Door.
services: frontdoor
documentationcenter: ''
author: jessie-jyy
editor: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/12/2021
ms.author: yuajia
ms.openlocfilehash: 7e2f05a7d911ce2b311a423994d2b459de0fa269
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107308871"
---
# <a name="tutorial-add-a-custom-domain-to-your-front-door"></a>Självstudie: Lägga till en anpassad domän i din Front Door

I den här självstudien får du lära dig hur du lägger till en anpassad domän i Front Door. När du använder Azure-frontend för program leverans krävs en anpassad domän om du vill att ditt eget domän namn ska vara synligt i slutanvändarens begäran. Att ha ett synligt domännamn kan vara praktiskt för dina kunder och användbart i profileringssyfte.

När du har skapat en front dörr ingår standard klient dels värden, som är en under domän till `azurefd.net` , i URL: en för att leverera innehåll från hård dörren från Server delen som standard (till exempel https: \/ /contoso-frontend.azurefd.net/activeusers.htm). Av bekvämlighetsskäl tillhandahåller Azure Front Door möjligheten att associera en anpassad domän med standardvärden. På så sätt kan du leverera ditt innehåll med en anpassad domän i din URL i stället för ett Front Door-ägt domännamn (till exempel https:\//www.contoso.com/photo.png). 

I den här guiden får du lära dig att:
> [!div class="checklist"]
> - Skapa en CNAME DNS-post
> - Associera den anpassade domänen med din Front Door.
> - Verifiera den anpassade domänen

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

> [!NOTE]
> Front dörren stöder **inte** anpassade domäner med [punycode](https://en.wikipedia.org/wiki/Punycode) tecken. 

## <a name="prerequisites"></a>Förutsättningar

* Innan du kan slutföra stegen i den här kursen behöver du skapa en Front Door. Mer information finns i [Snabbstart: Skapa en Front Door](quickstart-create-front-door.md).

* Om du inte redan har en anpassad domän måste du först köpa en med en domän leverantör. Se exempelvis [Köpa ett anpassat domännamn](../app-service/manage-custom-dns-buy-domain.md).

* Om du använder Azure som värd för dina [DNS-domäner](../dns/dns-overview.md)måste du delegera DOMÄNens DNS-domännamn (Domain Name System) till en Azure DNS. Mer information finns i [delegera en domän till Azure DNS](../dns/dns-delegate-domain-azure-dns.md). Annars fortsätter du att [skapa en CNAME DNS-post](#create-a-cname-dns-record)om du använder en domän leverantör för att hantera DNS-domänen.


## <a name="create-a-cname-dns-record"></a>Skapa en CNAME DNS-post

Innan du kan använda en anpassad domän med din Front Door behöver du skapa en CNAME-post (kanoniskt namn) med din domänleverantör som pekar till din Front Doors standardklientdelsvärd (till exempel contoso.azurefd.net). En CNAME-post är en typ av DNS-post som mappar ett källdomännamn till ett måldomännamn. För Azure-frontend är käll domän namnet ditt anpassade domän namn och mål domän namnet är standard värd namnet för din dörr. När front dörren verifierar den CNAME-post som du skapar dirigeras trafik som är adresserad till den anpassade käll domänen (till exempel www \. -contoso.com) till den angivna mål klient datorns standard klient dels värd (till exempel contoso-frontend.azurefd.net). 

En anpassad domän och dess under domän kan bara associeras med en enda frontend-dörr i taget. Du kan dock använda olika under domäner från samma anpassade domän för olika front dörrar genom att använda flera CNAME-poster. Du kan också mappa en anpassad domän med olika under domäner till samma frontend-dörr.


## <a name="map-the-temporary-afdverify-subdomain"></a>Mappa den temporära afdverify-underdomänen

När du mappar en befintlig domän som är i produktion finns några saker att tänka på. När du registrerar din anpassade domän i Azure Portal kan en kort period av drift stopp för domänen ske. För att undvika att webb trafik avbryts, ska du först mappa din anpassade domän till din frontend-standardklients värd med Azure afdverify-underdomänen för att skapa en tillfällig CNAME-mappning. Den här metoden gör att användare kan komma åt din domän utan avbrott under tiden DNS-mappningen utförs.

Annars, om du använder din anpassade domän för första gången och ingen produktions trafik körs på den, kan du mappa din anpassade domän direkt till din front dörr. Fortsätt att [mappa den permanenta anpassade domänen](#map-the-permanent-custom-domain).

Så här skapar du en CNAME-post med underdomänen afdverify:

1. Logga in på webbplatsen för domänleverantören för den anpassade domänen.

2. Leta reda på sidan för att hantera DNS-poster med hjälp av leverantörens dokumentation eller genom att söka på webbplatsen efter platser som **domännamn**, **DNS** eller **namnserverhantering**. 

3. Skapa en CNAME-post för din anpassade domän och fyll i fälten enligt tabellen nedan (fältnamnen kan variera):

    | Källa                    | Typ  | Mål                     |
    |---------------------------|-------|---------------------------------|
    | afdverify.www.contoso.com | CNAME | afdverify.contoso-frontend.azurefd.net |

    - Källa: Ange ditt anpassade domän namn, inklusive under domänen afdverify i följande format: afdverify. _&lt; eget domän namn &gt;_. Till exempel: afdverify.www.contoso.com. Om du mappar en domän med jokertecken, t \* . ex. contoso.com, är source-värdet detsamma som det skulle vara utan jokertecknet: afdverify.contoso.com.

    - Typ: Ange *CNAME*.

    - Mål: Ange din standard klients frontend-värd, inklusive under domänen afdverify i följande format: afdverify. _&lt; slut punkts namn &gt;_. azurefd.net. Till exempel afdverify.contoso-frontend.azurefd.net.

4. Spara ändringarna.

Till exempel är förfarandet för GoDaddy-domänregistratorn följande:

1. Logga in och välj den anpassade domänen som du vill använda.

2. Välj **Manage All** (Hantera alla) i avsnittet för domäner och välj sedan **DNS** | **Manage Zones** (Hantera zoner).

3. Ange din anpassade domän i **Domain Name** (Domännamn) och välj sedan **Search** (Sök).

4. På sidan **DNS Management** (DNS-hantering) väljer du **Add** (Lägg till ) och sedan **CNAME** i listan **Type** (Typ).

5. Fyll i följande fält för CNAME-posten:

    - Type (Typ): Använd det förvalda värdet *CNAME*.

    - Värd: Ange underdomänen för den anpassade domän som du vill använda, inklusive underdomännamnet afdverify. Till exempel: afdverify.www.

    - Pekar till: Ange värdnamnet för standardklientdelsvärden för Front Door, inklusive underdomänsnamnet afdverify. Till exempel afdverify.contoso-frontend.azurefd.net. 

    - TTL: lämna *en timme* vald.

6. Välj **Spara**.
 
    CNAME-posten läggs till i tabellen med DNS-poster.


## <a name="associate-the-custom-domain-with-your-front-door"></a>Associera den anpassade domänen med din Front Door

När du har registrerat din anpassade domän kan du lägga till den i din Front Door.

1. Logga in på [Azure Portal](https://portal.azure.com/) och navigera till den Front Door som innehåller klientdelsvärden som du vill mappa till en anpassad domän.
    
2. På sidan **front dörr designer** väljer du "+" för att lägga till en anpassad domän.
    
3. Ange **Anpassad domän**. 

4. För **Klientdelsvärd** är klientdelsvärden som ska användas som måldomän för din CNAME-post redan ifylld och den härleds från din Front Door: *&lt;standardvärdnamn&gt;*.azurefd.NET. Det kan inte ändras.

5. För **Anpassat värdnamn** anger du din anpassade domän, inklusive underdomänen, som ska användas som källdomän för din CNAME-post. Till exempel www \. contoso.com eller CDN.contoso.com. Använd inte namnet på afdverify-underdomänen.

6. Välj **Lägg till**.

   Azure verifierar att det finns en CNAME-post för det anpassade domännamnet som du har angett. Om CNAME är korrekt verifieras din anpassade domän.

>[!WARNING]
> Du **måste** se till att alla klientdelsvärdar (inklusive anpassade domäner) i din Front Door har en routningsregel med en associerad standardsökväg ('/\*'). Det betyder att för alla dina hanteringsregler måste det finnas minst en hanteringsregel för alla dina klientdelsvärdar som har definierats på standardsökvägen ('/\*'). Om du inte gör det kan det resultera i att slutanvändartrafiken inte dirigeras korrekt.

## <a name="verify-the-custom-domain"></a>Verifiera den anpassade domänen

När du har slutfört registreringen av din anpassade domän kontrollerar du att den anpassade domänen refererar till din standard klient dels värd för klient delen.
 
Navigera till filens adress genom att använda den anpassade domänen i webbläsaren. Om din anpassade domän till exempel är robotics.contoso.com ska URL:en till den cachelagrade filen se ut ungefär som följande URL: http:\//robotics.contoso.com/my-public-container/my-file.jpg. Kontrol lera att resultatet är samma som när du ansluter till frontend-dörren direkt på *&lt; värddatorn &gt; för den främre dörren*. azurefd.net.


## <a name="map-the-permanent-custom-domain"></a>Mappa den permanenta anpassade domänen

Om du har verifierat att afdverify-underdomänen har mappats till din front dörr (eller om du använder en ny anpassad domän som inte är i produktion) kan du mappa den anpassade domänen direkt till din standard klient för klient dels klienten.

Skapa en CNAME-post för den anpassade domänen:

1. Logga in på webbplatsen för domänleverantören för den anpassade domänen.

2. Hitta sidan för att hantera DNS-poster genom att konsultera leverantörens dokumentation eller söka efter områden på webbplatsen som är märkta med **domän namn**, **DNS** eller **namn server hantering**. 

3. Skapa en CNAME-post för din anpassade domän och fyll i fälten enligt tabellen nedan (fältnamnen kan variera):

    | Källa          | Typ  | Mål           |
    |-----------------|-------|-----------------------|
    | <www.contoso.com> | CNAME | contoso-frontend.azurefd.net |

   - Källa: Ange ditt anpassade domän namn (t. ex. www \. -contoso.com).

   - Typ: Ange *CNAME*.

   - Mål: Ange standardklientdelsvärden för din Front Door. Det måste ha följande format:_&lt; hostname &gt;_. azurefd.net. Till exempel contoso-frontend.azurefd.net.

4. Spara ändringarna.

5. Om du tidigare skapat en tillfällig CNAME-post för underdomänen afdverify tar du bort den. 

6. Om du använder den här anpassade domänen i produktion för första gången följer du stegen för att [associera den anpassade domänen med din front dörr](#associate-the-custom-domain-with-your-front-door) och [verifiera den anpassade domänen](#verify-the-custom-domain).

Till exempel är förfarandet för GoDaddy-domänregistratorn följande:

1. Logga in och välj den anpassade domänen som du vill använda.

2. Välj **Manage All** (Hantera alla) i avsnittet för domäner och välj sedan **DNS** | **Manage Zones** (Hantera zoner).

3. Ange din anpassade domän i **Domain Name** (Domännamn) och välj sedan **Search** (Sök).

4. På sidan **DNS Management** (DNS-hantering) väljer du **Add** (Lägg till ) och sedan **CNAME** i listan **Type** (Typ).

5. Fyll i fälten för CNAME-posten:

    - Type (Typ): Använd det förvalda värdet *CNAME*.

    - Host (Värd): Ange underdomänen för den anpassade domänen. Till exempel: www eller profil.

    - Pekar på: Ange standardvärdnamnet för din Front Door. Till exempel: contoso.azurefd.net. 

    - TTL: lämna *en timme* vald.

6. Välj **Spara**.
 
    CNAME-posten läggs till i tabellen med DNS-poster.

7. Om du har en CNAME-post för afdverify väljer du pennikonen bredvid den och sedan papperskorgsikonen.

8. Välj **Ta bort** för att ta bort CNAME-posten.

## <a name="clean-up-resources"></a>Rensa resurser

I föregående steg lade du till en anpassad domän i en Front Door. Om du inte längre vill koppla din frontend-dörr till en anpassad domän kan du ta bort den anpassade domänen genom att utföra följande steg:
 
1. Gå till din DNS-Provider, ta bort CNAME-posten för den anpassade domänen eller uppdatera CNAME-posten för den anpassade domänen till en slut punkt för en icke-front dörr.

    > [!Important]
    > För att förhindra att Dangling DNS-poster och de säkerhets risker som de skapar, från och med nionde 2021, måste Azures front dörr ta bort CNAME-posterna till front dörrs slut punkter innan resurserna kan tas bort. Resurser inkluderar anpassade domäner i front dörren, slut punkter för front dörren eller Azure-resurs grupper som har aktiverade anpassade domän platser.

2. I din Front Door-designer väljer du den anpassade domänen som du vill ta bort.

3. Välj **ta bort** på snabb menyn för den anpassade domänen. Den anpassade domänen är nu avkopplad från slut punkten.

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

* Skapa en CNAME DNS-post
* Associera den anpassade domänen med din Front Door.
* Verifiera den anpassade domänen

Fortsätt till nästa självstudie om du vill lära dig hur du aktiverar HTTPS för din anpassade domän.

> [!div class="nextstepaction"]
> [Aktivera HTTPS för en anpassad domän](front-door-custom-domain-https.md)
