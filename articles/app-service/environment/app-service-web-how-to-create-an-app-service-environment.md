---
title: Skapa en ASE v1
description: Beskrivning av skapande flöde för en app service Environment v1. Detta dokument tillhandahålls endast för kunder som använder den äldre v1-ASE.
author: ccompy
ms.assetid: 81bd32cf-7ae5-454b-a0d2-23b57b51af47
ms.topic: article
ms.date: 07/11/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 543050bc899c257c4ad5e0d0c399a1de6f0f58f2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96005221"
---
# <a name="how-to-create-an-app-service-environment-v1"></a>Så här skapar du en App Service-miljön v1 

> [!NOTE]
> Den här artikeln gäller App Service-miljön v1. Det finns en nyare version av App Service-miljön som är enklare att använda och som körs på en kraftfullare infrastruktur. Om du vill veta mer om den nya versionen börjar [du med introduktionen till App Service-miljön](intro.md).
> 

### <a name="overview"></a>Översikt
App Service-miljön (ASE) är ett premium tjänst alternativ för Azure App Service som ger en förbättrad konfigurations funktion som inte är tillgänglig i flera klientens stämplar. Funktionen ASE distribuerar i princip Azure App Service till kundens virtuella nätverk. För att få en bättre förståelse för de funktioner som erbjuds av App Services miljöer läser du dokumentationen [Vad är en app service-miljön][WhatisASE] .

### <a name="before-you-create-your-ase"></a>Innan du skapar din ASE
Det är viktigt att vara medveten om de saker som du inte kan ändra. De aspekter som du inte kan ändra om din ASE när den har skapats är:

* Plats
* Prenumeration
* Resursgrupp
* VNet används
* Undernät som används 
* Under näts storlek

När du väljer ett VNet och anger ett undernät kontrollerar du att det är tillräckligt stort för att kunna hantera framtida tillväxt. 

### <a name="creating-an-app-service-environment-v1"></a>Skapa en App Service-miljön v1
Om du vill skapa en app service-miljön v1 kan du söka i Azure Marketplace efter ***App Service-miljön v1** _ eller gå till _ *skapa en resurs**-> **webb och mobilt**  ->  **App Service-miljön**. Så här skapar du en ASEv1:

1. Ange namnet på din ASE. Det namn som du anger för ASE kommer att användas för de appar som skapas i ASE. Om namnet på ASE är appsvcenvdemo skulle under domän namnet vara: *appsvcenvdemo.p.azurewebsites.net*. Om du skapar en app med namnet *mytestapp* skulle den vara adresserbar vid *mytestapp.appsvcenvdemo.p.azurewebsites.net*. Du kan inte använda blank steg i namnet på din ASE. Om du använder versaler i namnet blir domän namnet den totala gemena versionen av det namnet. Om du använder en ILB används inte ditt ASE-namn i under domänen, men anges i stället uttryckligen när ASE skapas.
   
    ![Skärm bild som visar hur du skapar en App Service-miljön (ASE).][1]
2. Välj din prenumeration. Prenumerationen som du använder för din ASE gäller även för alla appar som du skapar i den ASE. Du kan inte placera din ASE i ett VNet som finns i en annan prenumeration.
3. Välj eller ange en ny resurs grupp. Resurs gruppen som används för din ASE måste vara samma som används för ditt VNet. Om du väljer ett befintligt virtuellt nätverk kommer resurs grupps valet för din ASE att uppdateras för att avspegla detta för ditt VNet.
   
    ![Skärm bild som visar hur du kan välja eller ändra en ny resurs grupp.][2]
4. Gör dina val för Virtual Network och plats. Du kan välja att skapa ett nytt VNet eller välja ett befintligt VNet. Om du väljer ett nytt virtuellt nätverk kan du ange ett namn och en plats. Det nya virtuella nätverket kommer att ha adress intervallet 192.168.250.0/23 och ett undernät med namnet **default** som definieras som 192.168.250.0/24. Du kan också välja ett redan befintligt klassiskt eller Resource Manager VNet. Valet av VIP-typ avgör om din ASE kan nås direkt från Internet (extern) eller om den använder en intern Load Balancer (ILB). Om du vill veta mer om hur de läser [använder du ett internt Load Balancer med ett App Service-miljön][ILBASE]. Om du väljer en VIP-typ som är extern kan du välja hur många externa IP-adresser som systemet ska skapas med i IP SSL syfte. Om du väljer intern måste du ange den under domän som din ASE ska använda. ASE kan distribueras i virtuella nätverk som använder *antingen* offentliga adress intervall *eller* RFC1918 adress utrymmen (dvs. privata adresser). För att du ska kunna använda ett virtuellt nätverk med ett offentligt adress intervall måste du skapa VNet i förväg. När du väljer ett befintligt virtuellt nätverk måste du skapa ett nytt undernät när ASE skapas. **Du kan inte använda ett i förväg skapade undernät i portalen. Du kan skapa en ASE med ett redan befintligt undernät om du skapar din ASE med hjälp av en Resource Manager-mall.** Om du vill skapa en ASE från en mall använder du informationen här, [skapar en app service-miljön från mallen][ILBAseTemplate] och [skapar en ILB-App Service-miljön från mallen][ASEfromTemplate].

### <a name="details"></a>Information
En ASE skapas med 2 klient delar och två arbetare. Klient delar fungerar som HTTP/HTTPS-slutpunkter och skickar trafik till de arbetare som är de roller som är värdar för dina appar. Du kan justera antalet när ASE har skapats och även ange regler för autoskalning på dessa resurspooler. Mer information kring manuell skalning, hantering och övervakning av en App Service-miljön finns här: [så här konfigurerar du en app service-miljön][ASEConfig] 

Endast en ASE kan finnas i under nätet som används av ASE. Det går inte att använda under nätet för något annat än ASE

### <a name="after-app-service-environment-v1-creation"></a>När App Service-miljön v1 skapas
När ASE har skapats kan du justera:

* Antal klient delar (minst: 2)
* Antal arbetare (minimum: 2)
* Antal IP-adresser som är tillgängliga för IP SSL
* Beräknings resurs storlekar som används av klient delar eller arbetare (lägsta lägsta storlek för klient delen är P2)

Det finns mer information kring manuell skalning, hantering och övervakning av App Service miljöer här: [så här konfigurerar du en app service-miljön][ASEConfig] 

För information om automatisk skalning finns en guide här: [så här konfigurerar du automatisk skalning för en app service-miljön][ASEAutoscale]

Det finns ytterligare beroenden som inte är tillgängliga för anpassning, till exempel databasen och lagringen. De hanteras av Azure och levereras med systemet. System lagringen har stöd för upp till 500 GB för hela App Service-miljön och databasen justeras av Azure efter behov av systemets skala.

## <a name="getting-started"></a>Komma igång
För att komma igång med App Service-miljön v1, se [Introduktion till App Service-miljön v1][WhatisASE]

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!--Image references-->
[1]: ./media/app-service-web-how-to-create-an-app-service-environment/asecreate-basecreateblade.png
[2]: ./media/app-service-web-how-to-create-an-app-service-environment/asecreate-vnetcreation.png

<!--Links-->
[WhatisASE]: app-service-app-service-environment-intro.md
[ASEConfig]: app-service-web-configure-an-app-service-environment.md
[AppServicePricing]: https://azure.microsoft.com/pricing/details/app-service/ 
[ASEAutoscale]: app-service-environment-auto-scale.md
[ILBASE]: app-service-environment-with-internal-load-balancer.md
[ILBAseTemplate]: https://azure.microsoft.com/documentation/templates/201-web-app-ase-create/
[ASEfromTemplate]: app-service-app-service-environment-create-ilb-ase-resourcemanager.md
