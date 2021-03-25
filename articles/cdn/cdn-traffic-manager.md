---
title: Redundansväxla över flera slut punkter med Traffic Manager
titleSuffix: Azure Content Delivery Network
description: Lär dig hur du konfigurerar redundans över flera Azure Content Delivery Network-slutpunkter med hjälp av Azure Traffic Manager.
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: how-to
ms.date: 10/08/2020
ms.author: allensu
ms.custom: ''
ms.openlocfilehash: a003becba0bc1e42d8fe0c0c5b199402a430a8e1
ms.sourcegitcommit: bb330af42e70e8419996d3cba4acff49d398b399
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2021
ms.locfileid: "105034766"
---
# <a name="failover-across-multiple-endpoints-with-azure-traffic-manager"></a>Redundans över flera slut punkter med Azure Traffic Manager

När du konfigurerar Azure Content Delivery Network (CDN) kan du välja den optimala providern och pris nivån för dina behov. 

Azure CDN, med globalt distribuerad infrastruktur, skapar som standard lokal och geografisk redundans och global belastnings utjämning för att förbättra tjänstens tillgänglighet och prestanda. 

Om en plats inte är tillgänglig för att hantera innehåll dirigeras begär Anden automatiskt till en annan plats. Den optimala närvaro punkten (POP) används för att betjäna varje klient förfrågan. Automatisk routning baseras på faktorer som begär ande plats och server belastning.
 
Om du har flera CDN-profiler kan du ytterligare förbättra tillgängligheten och prestandan med Azure Traffic Manager. 

Använd Azure Traffic Manager med Azure CDN för belastnings utjämning mellan flera CDN-slutpunkter för:
 
* Redundans
* Belastnings utjämning för geo 

I ett typiskt failover-scenario dirigeras alla klient begär anden till den primära CDN-profilen. 

Om profilen inte är tillgänglig dirigeras förfrågningar till den sekundära profilen.  Förfrågningarna återupptas till din primära profil när den kommer tillbaka online.

Genom att använda Azure Traffic Manager på det här sättet ser du till att ditt webb program alltid är tillgängligt. 

Den här artikeln innehåller vägledning och ett exempel på hur du konfigurerar redundans med profiler från: 

* **Azure CDN Standard från Verizon**
* **Azure CDN Standard från Akamai**

**Azure CDN från Microsoft** stöds också.

## <a name="create-azure-cdn-profiles"></a>Skapa Azure CDN profiler
Skapa två eller fler Azure CDN profiler och slut punkter med olika leverantörer.

1. Skapa två CDN-profiler:
    * **Azure CDN Standard från Verizon**
    * **Azure CDN Standard från Akamai** 

    Skapa profilerna genom att följa stegen i [skapa en ny CDN-profil](cdn-create-new-endpoint.md#create-a-new-cdn-profile).
 
   ![CDN flera profiler](./media/cdn-traffic-manager/cdn-multiple-profiles.png)

2. I var och en av de nya profilerna skapar du minst en slut punkt genom att följa stegen i [skapa en ny CDN-slutpunkt](cdn-create-new-endpoint.md#create-a-new-cdn-endpoint).

## <a name="create-traffic-manager-profile"></a>Skapa Traffic Manager-profil
Skapa en Azure Traffic Manager-profil och konfigurera belastnings utjämning för dina CDN-slutpunkter. 

1. Skapa en Azure Traffic Manager-profil genom att följa stegen i [skapa en Traffic Manager profil](../traffic-manager/quickstart-create-traffic-manager-profile.md). 

    * **Routningsmetod** väljer du **prioritet**.

2. Lägg till dina CDN-slutpunkter i din Traffic Manager profil genom att följa stegen i [Lägg till Traffic Manager slut punkter](../traffic-manager/quickstart-create-traffic-manager-profile.md#add-traffic-manager-endpoints)

    * **Typ**, Välj **externa slut punkter**.
    * **Prioritet** anger du ett tal.

    Skapa till exempel **cdndemo101akamai.azureedge.net** med prioritet **1** och **cdndemo101verizon.azureedge.net** med prioritet **2**.

   ![CDN Traffic Manager-slutpunkter](./media/cdn-traffic-manager/cdn-traffic-manager-endpoints.png)


## <a name="configure-custom-domain-on-azure-cdn-and-azure-traffic-manager"></a>Konfigurera anpassad domän på Azure CDN och Azure Traffic Manager
När du har konfigurerat CDN och Traffic Manager profiler följer du dessa steg för att lägga till DNS-mappning och registrera anpassad domän i CDN-slutpunkterna. I det här exemplet är det anpassade domän namnet **cdndemo101.dustydogpetcare.online**.

1. Gå till webbplatsen för domän leverantören för din anpassade domän, till exempel GoDaddy, och skapa två CNAME-poster för DNS. 

    a. För den första CNAME-posten mappar du din anpassade domän med under domänen cdnverify till din CDN-slutpunkt. Posten är ett obligatoriskt steg för att registrera den anpassade domänen till den CDN-slutpunkt som du lade till i Traffic Manager i steg 2.

      Exempel: 

      `cdnverify.cdndemo101.dustydogpetcare.online  CNAME  cdnverify.cdndemo101akamai.azureedge.net`  

    b. För den andra CNAME-posten mappar du din anpassade domän utan cdnverify-underdomänen till CDN-slutpunkten. Den här posten mappar den anpassade domänen till Traffic Manager. 

      Exempel: 
      
      `cdndemo101.dustydogpetcare.online  CNAME  cdndemo101.trafficmanager.net`   

    > [!NOTE]
    > Om din domän är Live och inte kan avbrytas, gör du det här steget sist. Kontrol lera att CDN-slutpunkterna och Traffic Manager-domänerna är aktiva innan du uppdaterar din anpassade domän-DNS till Traffic Manager.
    >
   
    > [!NOTE]
    > För att implemeting den här redundansväxla scenerio måste båda slut punkterna finnas i olika profiler och de olika profilerna bör vara av en annan CDN-Provider för att undvika domän namns konflikter.
    > 

2.  Välj den första CDN-slutpunkten (Akamai) från din Azure CDN profil. Välj **Lägg till anpassad domän** och **cdndemo101.dustydogpetcare.online** för indatamängd. Kontrol lera att kryss rutan kontrol lera att den anpassade domänen är grön. 

    Azure CDN använder under domänen **cdnverify** för att verifiera DNS-mappningen för att slutföra den här registrerings processen. Mer information finns i [skapa en CNAME DNS-post](cdn-map-content-to-custom-domain.md#create-a-cname-dns-record). Det här steget gör det möjligt Azure CDN att identifiera den anpassade domänen så att den kan svara på begär Anden.
    
    > [!NOTE]
    > Om du vill aktivera TLS i en **Azure CDN från Akamai** profiler måste du direkt CNAME den anpassade domänen till din slut punkt. cdnverify för att aktivera TLS stöds inte ännu. 
    >

3.  Gå tillbaka till webbplatsen för domän leverantören för din anpassade domän. Uppdatera den första DNS-mappning som du skapade. Mappa den anpassade domänen till din andra CDN-slutpunkt.
                             
    Exempel: 

    `cdnverify.cdndemo101.dustydogpetcare.online  CNAME  cdnverify.cdndemo101verizon.azureedge.net`  

4. Välj den andra CDN-slutpunkten (Verizon) från din Azure CDN profil och upprepa steg 2. Välj **Lägg till anpassad domän** och ange **cdndemo101.dustydogpetcare.online**.
 
När du har slutfört de här stegen konfigureras tjänsten multi-CDN med funktioner för redundans med Azure Traffic Manager. 

Du kan komma åt test-URL: er från din anpassade domän. 

Om du vill testa funktionen inaktiverar du den primära CDN-slutpunkten och kontrollerar att begäran har flyttats till den sekundära CDN-slutpunkten. 

## <a name="next-steps"></a>Nästa steg
Du kan konfigurera andra routningsmetoder, till exempel geografisk, för att utjämna belastningen mellan olika CDN-slutpunkter. 

Mer information finns i [Konfigurera metoden för att dirigera geografisk trafik med hjälp av Traffic Manager](../traffic-manager/traffic-manager-configure-geographic-routing-method.md).
