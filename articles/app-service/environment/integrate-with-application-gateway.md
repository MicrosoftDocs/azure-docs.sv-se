---
title: Integrera med Application Gateway
description: Lär dig hur du integrerar en app i din ILB-App Service-miljön med ett Application Gateway i den här slut punkt till slut punkt.
author: ccompy
ms.assetid: a6a74f17-bb57-40dd-8113-a20b50ba3050
ms.topic: article
ms.date: 03/03/2018
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 3b73d528802a8aa33c6122eaf5edfa9d046b6753
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "88962085"
---
# <a name="integrate-your-ilb-app-service-environment-with-the-azure-application-gateway"></a>Integrera en ILB App Service-miljö med Azure Application Gateway #

[App Service-miljön](./intro.md) är en distribution av Azure App Service i under nätet för kundens virtuella Azure-nätverk. Den kan distribueras med en offentlig eller privat slut punkt för åtkomst till appen. Distributionen av App Service-miljön med en privat slut punkt (dvs. en intern belastningsutjämnare) kallas för en ILB App Service-miljön.  

Brand väggar för webb program hjälper till att skydda dina webb program genom att kontrol lera inkommande webb trafik för att blockera SQL-injektering, skript körning över flera webbplatser, överföring av skadlig kod & program DDoS och andra attacker. Det kontrollerar också svaren från backend-webbservrar för data förlust skydd (DLP). Du kan få en WAF-enhet från Azure Marketplace eller så kan du använda [Azure Application Gateway][appgw].

Azure Application Gateway är en virtuell installation som tillhandahåller belastnings utjämning i Layer 7, TLS/SSL-avlastning och skydd för brand vägg för webbaserade program (WAF). Den kan lyssna på en offentlig IP-adress och dirigera trafik till program slut punkten. Följande information beskriver hur du integrerar en WAF-konfigurerad Application Gateway med en app i en ILB-App Service-miljön.  

Integreringen av programgatewayen med ILB App Service-miljön finns på en app-nivå. När du konfigurerar programgatewayen med ILB App Service-miljön du göra det för specifika appar i din ILB-App Service-miljön. Den här tekniken möjliggör värdbaserade säkra program för flera klient organisationer i en enda ILB App Service-miljön.  

![Application Gateway som pekar på en app på en ILB App Service-miljön][1]

I den här genomgången kommer du att:

* Skapa en Azure Application Gateway.
* Konfigurera Application Gateway så att den pekar på en app i din ILB App Service-miljön.
* Konfigurera din app för att respektera det anpassade domän namnet.
* Redigera det offentliga DNS-värdnamnet som pekar på din Application Gateway.

## <a name="prerequisites"></a>Förutsättningar

Om du vill integrera din Application Gateway med din ILB-App Service-miljön behöver du:

* Ett ILB-App Service-miljön.
* En app som körs i ILB-App Service-miljön.
* Ett Internet-dirigerbart domän namn som ska användas med din app i ILB-App Service-miljön.
* ILB-adressen som ILB-App Service-miljön använder. Den här informationen finns i App Service-miljön-portalen under **Inställningar**  >  **IP-adresser**:

    ![Exempel lista med IP-adresser som används av ILB-App Service-miljön][9]
    
* Ett offentligt DNS-namn som används senare för att peka på din Application Gateway. 

Mer information om hur du skapar en ILB App Service-miljön finns i [skapa och använda en ILB App Service-miljön][ilbase].

Den här artikeln förutsätter att du vill ha en Application Gateway i samma virtuella Azure-nätverk där App Service-miljön distribueras. Innan du börjar skapa Application Gateway väljer du eller skapar ett undernät som ska användas som värd för gatewayen. 

Du bör använda ett undernät som inte är det som heter GatewaySubnet. Om du Application Gateway i GatewaySubnet kan du inte skapa en virtuell nätverksgateway senare. 

Du kan inte heller ange den gateway i under nätet som ILB-App Service-miljön använder. App Service-miljön är den enda sak som kan finnas i det här under nätet.

## <a name="configuration-steps"></a>Konfigurationssteg ##

1. I Azure Portal går du till **nytt**  >  **nätverks**  >  **Application Gateway**.

2. I avsnittet **grundläggande** :

   a. I **namn** anger du namnet på Application Gateway.

   b. För **nivå** väljer du **WAF**.

   c. För **prenumeration** väljer du den prenumeration som App Service-miljön virtuellt nätverk använder.

   d. För **resurs grupp** skapar eller väljer du resurs gruppen.

   e. För **plats** väljer du platsen för det App Service-miljön virtuella nätverket.

   ![Grunderna för att skapa nya Application Gateway][2]

3. I **inställnings** avsnittet:

   a. För **virtuellt nätverk** väljer du det virtuella nätverket App Service-miljön.

   b. För **undernät** väljer du det undernät där Application Gateway måste distribueras. Använd inte GatewaySubnet, eftersom det gör att VPN-gatewayer inte kan skapas.

   c. I **typ av IP-adress** väljer du **offentlig**.

   d. För **offentlig IP-adress** väljer du en offentlig IP-adress. Om du inte har ett kan du skapa en nu.

   e. För **protokoll** väljer du **http** eller **https**. Om du konfigurerar för HTTPS måste du ange ett PFX-certifikat.

   f. För **brand vägg för webbaserade program** kan du aktivera brand väggen och även ställa in den för **identifiering** eller **skydd** när du ser anpassa.

   ![Nya inställningar för att skapa Application Gateway][3]
    
4. I **sammanfattnings** avsnittet granskar du inställningarna och väljer **OK**. Det kan ta lite mer än 30 minuter att slutföra installationen av Application Gateway.  

5. När Application Gateway har slutfört installationen går du till Application Gateway Portal. Välj **backend-pool**. Lägg till ILB-adressen för din ILB-App Service-miljön.

   ![Konfigurera backend-pool][4]

6. När du har konfigurerat backend-poolen slutförd väljer du **hälso avsökningar**. Skapa en hälso avsökning för det domän namn som du vill använda för din app. 

   ![Konfigurera hälsotillståndsavsökningar][5]
    
7. När du har konfigurerat hälso avsökningarna har slutförts väljer du **http-inställningar**. Redigera befintliga inställningar, Välj **Använd anpassad avsökning** och välj den avsökning som du har konfigurerat.

   ![Konfigurera HTTP-inställningar][6]
    
8. Gå till avsnittet **Översikt över** Application Gateway och kopiera den offentliga IP-adress som din Application Gateway använder. Ange IP-adressen som en post för ditt program domän namn eller Använd DNS-namnet för adressen i en CNAME-post. Det är enklare att välja den offentliga IP-adressen och kopiera den från den offentliga IP-adressens användar gränssnitt i stället för att kopiera den från länken i avsnittet **Översikt över** Application Gateway. 

   ![Application Gateway Portal][7]

9. Ange det anpassade domän namnet för din app i din ILB-App Service-miljön. Gå till din app i portalen och välj **anpassade domäner** under **Inställningar**.

   ![Ange anpassat domän namn i appen][8]

Det finns information om hur du anger anpassade domän namn för dina webb program i artikeln [Ange anpassade domän namn för din webbapp][custom-domain]. Men för en app i en ILB App Service-miljön finns det ingen verifiering på domän namnet. Eftersom du äger den DNS som hanterar appens slut punkter kan du placera vad du vill i där. Det anpassade domän namnet som du lägger till i det här fallet behöver inte finnas i din DNS, men det måste fortfarande konfigureras med din app. 

När installationen är klar och du har tillåtit en kort tid för dina DNS-ändringar att sprida, kan du komma åt din app med hjälp av det anpassade domän namnet som du har skapat. 


<!--IMAGES-->
[1]: ./media/integrate-with-application-gateway/appgw-highlevel.png
[2]: ./media/integrate-with-application-gateway/appgw-createbasics.png
[3]: ./media/integrate-with-application-gateway/appgw-createsettings.png
[4]: ./media/integrate-with-application-gateway/appgw-backendpool.png
[5]: ./media/integrate-with-application-gateway/appgw-healthprobe.png
[6]: ./media/integrate-with-application-gateway/appgw-httpsettings.png
[7]: ./media/integrate-with-application-gateway/appgw-publicip.png
[8]: ./media/integrate-with-application-gateway/appgw-customdomainname.png
[9]: ./media/integrate-with-application-gateway/appgw-iplist.png

<!--LINKS-->
[appgw]: ../../application-gateway/overview.md
[custom-domain]: ../app-service-web-tutorial-custom-domain.md
[ilbase]: ./create-ilb-ase.md