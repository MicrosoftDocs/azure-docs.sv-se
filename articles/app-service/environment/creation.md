---
title: Skapa en App Service-miljön
description: Lär dig hur du skapar en App Service-miljön.
author: ccompy
ms.assetid: 7690d846-8da3-4692-8647-0bf5adfd862a
ms.topic: article
ms.date: 11/16/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 021c85037ea105ff7d8ff642e3a9f28ed3ebe991
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100594102"
---
# <a name="create-an-app-service-environment"></a>Skapa en App Service-miljön

> [!NOTE]
> Den här artikeln gäller App Service-miljön v3 (för hands version)
> 

[App Service-miljön (ASE)][Intro] är en enda klient distribution av app service som matas in i Azure-Virtual Network (VNet).  ASEv3 stöder endast exponerade appar på en privat adress i ditt VNet. När en ASEv3 skapas under för hands versionen läggs resurserna till i din prenumeration.

- App Service Environment
- Privat slutpunkt

En distribution av en ASE kräver att två undernät används.  Ett undernät kommer att innehålla den privata slut punkten.  Det här under nätet kan användas för andra saker, till exempel virtuella datorer.  Det andra under nätet används för utgående anrop som görs från ASE.  Det går inte att använda det här under nätet för något annat än ASE. 

## <a name="before-you-create-your-ase"></a>Innan du skapar din ASE

När du har skapat din ASE kan du inte ändra:

- Plats
- Prenumeration
- Resursgrupp
- Azure Virtual Network (VNet) används
- Undernät som används
- Under näts storlek
- Namnet på din ASE

Det utgående under nätet måste vara tillräckligt stort för att rymma den maximala storlek som du skalar din ASE. Välj ett tillräckligt stort undernät för att stödja dina maximala skalnings behov eftersom det inte kan ändras efter att det har skapats. Den rekommenderade storleken är a/24 med 256 adresser.

När ASE har skapats kan du lägga till appar till den. Om din ASEv3 inte har några App Service-planer i det, kostar det att du har en instans av en I1v2 App Service plan i din ASE.  

ASEv3 erbjuds endast i SELECT-regioner. Fler regioner kommer att läggas till när för hands versionen flyttas tillsammans med GA. 

## <a name="creating-an-ase-in-the-portal"></a>Skapa en ASE i portalen

1. Om du vill skapa en ASEv3 söker du på Marketplace efter **App Service-miljön (för hands version)**.  
2. Grunder: Välj prenumerationen, Välj eller skapa resurs gruppen och ange namnet på din ASE.  ASE namn används också för domänsuffix för din ASE.  Om ditt ASE namn är *contoso* kommer domänsuffixet att vara *contoso.appserviceenvironment.net*.  Det här namnet anges automatiskt i Azure DNS privata zon som används av det virtuella nätverk som ASE distribueras till. 

    ![App Service-miljön skapa fliken grunder](./media/creation/creation-basics.png)

3. Värd: Välj OS-preferens, värd grupp distribution. OS-inställningen anger vilket operativ system du använder för att börja använda för dina appar i den här ASE. Du kan lägga till appar från det andra operativ systemet när ASE har skapats. Distribution av värd grupper används för att välja dedikerad maskin vara. Med ASEv3 kan du välja aktive rad och sedan land på dedikerad maskin vara. Du debiteras för hela den dedikerade värden när ASE skapas och sedan ett reducerat pris för dina App Service plan-instanser. 

    ![App Service-miljön värd val](./media/creation/creation-hosting.png)

4. Nätverk: Välj eller skapa din Virtual Network, Välj eller skapa ditt inkommande undernät, Välj eller skapa ditt utgående undernät. Alla undernät som används för utgående måste vara tomma och delegerade till Microsoft. Web/hostingEnvironments. Om du skapar ditt undernät via portalen, kommer under nätet automatiskt att delegeras åt dig.

    ![App Service-miljön nätverks val](./media/creation/creation-networking.png)

5. Granska och skapa: kontrol lera att konfigurationen är korrekt och välj Skapa. ASE tar ungefär en timme att skapa. 

    ![App Service-miljön granska och skapa](./media/creation/creation-review.png)

När din ASE har skapats kan du välja den som en plats när du skapar dina appar. Mer information om hur du skapar appar i dina nya ASE finns i [använda en app service-miljön][UsingASE]

## <a name="os-preference"></a>OS-preferens
I en ASE kan du ha Windows-appar, Linux-appar eller båda. I ASEv2 lägger den första inställningen som valdes vid skapandet till infrastrukturen för hög tillgänglighet för det operativ systemet när ASE skapas. Om du vill lägga till appar från det andra operativ systemet gör du bara apparna som vanligt och väljer önskat operativ system. I ASEv3 kommer detta inte att påverka Server delens beteende appreciatively.  

## <a name="dedicated-hosts"></a>Dedikerade värdar
ASE distribueras vanligt vis på virtuella datorer som är etablerade på en hypervisor med flera innehavare. Om du behöver distribuera på dedikerade system, inklusive maskin vara, kan du etablera dina ASE på dedikerade värdar. I den inledande ASEv3-förhands granskningen kommer dedikerade värdar att komma i ett par. Varje dedikerad värd finns i en separat tillgänglighets zon, om regionen tillåter det. Dedikerade värdbaserade ASE-distributioner kostar annorlunda än normalt. Det finns en avgift för den dedikerade värden och en annan kostnad för varje App Service plan instans.  

<!--Links-->
[Intro]: ./overview.md
[MakeASE]: ./creation.md
[ASENetwork]: ./networking.md
[UsingASE]: ./using.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/network-security-groups-overview.md
[Pricing]: https://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/management/overview.md
[ConfigureSSL]: ../configure-ssl-certificate.md
[Kudu]: https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[AppDeploy]: ../deploy-local-git.md
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../web-application-firewall/ag/ag-overview.md
[logalerts]: ../../azure-monitor/alerts/alerts-log.md
