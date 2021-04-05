---
title: Skapa en extern ASE
description: Lär dig hur du skapar en App Service-miljö med en app i den eller skapa en fristående (tom) ASE.
author: ccompy
ms.assetid: 94dd0222-b960-469c-85da-7fcb98654241
ms.topic: article
ms.date: 06/13/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: c953c31792b8d01199d409cbd91124138a6ebb15
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92927455"
---
# <a name="create-an-external-app-service-environment"></a>Skapa en extern App Service-miljö

Azure App Service Environment är en distribution av Azure App Service i ett undernät i ett virtuellt nätverk i Azure (VNet).

> [!NOTE]
> Varje App Service-miljön har en virtuell IP-adress (VIP) som kan användas för att kontakta App Service-miljön.

Det går att distribuera en App Service-miljö (ASE) på två sätt:

- Med en VIP på en extern IP-adress som ofta kallas för en extern ASE.
- Med VIP på en intern IP-adress, som ofta kallas för en ILB-ASE, eftersom den interna slut punkten är en intern Load Balancer (ILB).

Den här artikeln visar hur du skapar en extern ASE. En översikt över ASE finns i [en introduktion till App Service-miljön][Intro]. Information om hur du skapar en ILB-ASE finns i [skapa och använda en ILB-ASE][MakeILBASE].

## <a name="before-you-create-your-ase"></a>Innan du skapar din ASE

När du har skapat din ASE kan du inte ändra följande:

- Plats
- Prenumeration
- Resursgrupp
- VNet används
- Undernät som används
- Under näts storlek

> [!NOTE]
> När du väljer ett VNet och anger ett undernät måste du kontrol lera att det är tillräckligt stort för att kunna hantera framtida tillväxt-och skalnings behov. Vi rekommenderar en storlek på `/24` med 256 adresser.
>

## <a name="three-ways-to-create-an-ase"></a>Tre sätt att skapa en ASE

Det finns tre sätt att skapa en ASE:

- **När du skapar en app service plan**. Den här metoden skapar ASE och App Service plan i ett enda steg.
- **Som en fristående åtgärd**. Den här metoden skapar en fristående ASE, som är en ASE utan något. Den här metoden är en mer avancerad process för att skapa en ASE. Du använder den för att skapa en ASE med en ILB.
- **Från en Azure Resource Manager-mall**. Den här metoden är för avancerade användare. Mer information finns i [skapa en ASE från en mall][MakeASEfromTemplate].

En extern ASE har en offentlig VIP, vilket innebär att all HTTP/HTTPS-trafik till apparna i ASE träffar en IP-adress som kan nås via Internet. En ASE med en ILB har en IP-adress från det undernät som används av ASE. Appar som är värdar för en ILB-ASE exponeras inte direkt för Internet.

## <a name="create-an-ase-and-an-app-service-plan-together"></a>Skapa en ASE och en App Service plan tillsammans

App Service plan är en behållare för appar. När du skapar en app i App Service kan du välja eller skapa en App Service plan. App Service miljöer innehåller App Service-planer och App Service planer.

Så här skapar du en ASE när du skapar en App Service plan:

1. I [Azure Portal](https://portal.azure.com/)väljer du **skapa en resurs**  >  **webb och mobilt**  >  **-webbapp**.

    ![Skärm bild av Azure Portal som visar Webb och mobilt valt på Azure Marketplace och skärmen för att skapa en ny webbapp öppen till höger.][1]

2. Välj din prenumeration. Appen och ASE skapas i samma prenumerationer.

3. Välj eller skapa en Resursgrupp. Med resurs grupper kan du hantera relaterade Azure-resurser som en enhet. Resurs grupper är också användbara när du upprättar Role-Based Access Controls regler för dina appar. Mer information finns i [översikten över Azure Resource Manager][ARMOverview].

4. Välj ditt operativ system (Windows, Linux eller Docker). 

5. Välj App Service plan och välj sedan **Skapa ny**. Linux-webbappar och Windows-webbappar kan inte vara i samma App Service plan, men kan vara i samma App Service-miljön. 

    ![Skärm bild av Azure Portal som visar fönstret webbapp, fönstret App Service-plan och fönstret ny App Service plan öppnas.][2]

6. I list rutan **plats** väljer du den region där du vill skapa ASE. Om du väljer en befintlig ASE skapas inte en ny ASE. App Service plan skapas i den ASE som du har valt. 

7. Välj **pris nivå** och välj en av de **isolerade** pris-SKU: erna. Om du väljer ett **isolerat** SKU-kort och en plats som inte är en ASE, skapas en ny ASE på den platsen. Välj **Välj** för att starta processen för att skapa en ASE. Den **isolerade** SKU: n är bara tillgänglig tillsammans med en ASE. Du kan inte heller använda någon annan pris-SKU i en annan ASE än **isolerad**. 

    ![Val av pris nivå][3]

8. Ange namnet på din ASE. Det här namnet används i det adresser bara namnet för dina appar. Om namnet på ASE är _appsvcenvdemo_ är domän namnet *. appsvcenvdemo.p.azurewebsites.net*. Om du skapar en app med namnet *mytestapp* kan den adresseras på mytestapp.appsvcenvdemo.p.azurewebsites.net. Du kan inte använda blank steg i namnet. Om du använder versaler är domän namnet den totala gemen versionen av det namnet.

    ![Nytt App Service plan namn][4]

9. Ange information om det virtuella Azure-nätverket. Välj antingen **Skapa ny** eller **Välj befintlig**. Alternativet för att välja ett befintligt VNet är bara tillgängligt om du har ett VNet i den valda regionen. Om du väljer **Skapa ny** anger du ett namn för VNet. Ett nytt virtuellt Resource Manager VNet med det namnet skapas. Det använder adress utrymmet `192.168.250.0/23` i den valda regionen. Om du väljer **Välj befintlig** måste du:

    a. Välj VNet-adressblock om du har fler än ett.

    b. Ange ett nytt namn på under nätet.

    c. Välj storlek på under nätet. *Kom ihåg att välja en storlek som är tillräckligt stor för att kunna hantera framtida tillväxt av din ASE.* Vi rekommenderar `/24` , som har 256 adresser och kan hantera en maximal storlek ASE. Vi rekommenderar till `/28` exempel inte att bara 16 adresser är tillgängliga. Infrastrukturen använder minst sju adresser och Azure-nätverk använder en till 5. I ett `/28` undernät har du lämnat en maximal skalning på 4 App Service plan instanser för en extern ASE och endast tre App Service plan-instanser för en ILB-ASE.

    d. Välj IP-intervall för under nätet.

10. Välj **skapa** för att skapa ASE. Den här processen skapar även App Service plan och appen. ASE, App Service plan och app är alla under samma prenumeration och också i samma resurs grupp. Om din ASE behöver en separat resurs grupp eller om du behöver en ILB-ASE, följer du stegen för att skapa en ASE av sig själv.

## <a name="create-an-ase-and-a-linux-web-app-using-a-custom-docker-image-together"></a>Skapa en ASE och en Linux-webbapp med hjälp av en anpassad Docker-avbildning

1. [](https://portal.azure.com/) **Skapa en resurs**  >  **webb och mobilt**  >  **Web App for containers** i Azure Portal. 

    ![Skärm bild av Azure Portal som visar Webb och mobilt valdes på Azure Marketplace och fönstret Web App for Containers öppnas till höger.][7]

1. Välj din prenumeration. Appen och ASE skapas i samma prenumerationer.

1. Välj eller skapa en Resursgrupp. Med resurs grupper kan du hantera relaterade Azure-resurser som en enhet. Resurs grupper är också användbara när du upprättar Role-Based Access Controls regler för dina appar. Mer information finns i [översikten över Azure Resource Manager][ARMOverview].

1. Välj App Service plan och välj sedan **Skapa ny**. Linux-webbappar och Windows-webbappar kan inte vara i samma App Service plan, men kan vara i samma App Service-miljön. 

    ![Skärm bild av Azure Portal som visar fönstret Web App for Containers, fönstret App Service-plan och fönstret ny App Service plan öppen.][8]

1. I list rutan **plats** väljer du den region där du vill skapa ASE. Om du väljer en befintlig ASE skapas inte en ny ASE. App Service plan skapas i den ASE som du har valt. 

1. Välj **pris nivå** och välj en av de **isolerade** pris-SKU: erna. Om du väljer ett **isolerat** SKU-kort och en plats som inte är en ASE, skapas en ny ASE på den platsen. Välj **Välj** för att starta processen för att skapa en ASE. Den **isolerade** SKU: n är bara tillgänglig tillsammans med en ASE. Du kan inte heller använda någon annan pris-SKU i en annan ASE än **isolerad**. 

    ![Val av pris nivå][3]

1. Ange namnet på din ASE. Det här namnet används i det adresser bara namnet för dina appar. Om namnet på ASE är _appsvcenvdemo_ är domän namnet *. appsvcenvdemo.p.azurewebsites.net*. Om du skapar en app med namnet *mytestapp* kan den adresseras på mytestapp.appsvcenvdemo.p.azurewebsites.net. Du kan inte använda blank steg i namnet. Om du använder versaler är domän namnet den totala gemen versionen av det namnet.

    ![Nytt App Service plan namn][4]

1. Ange information om det virtuella Azure-nätverket. Välj antingen **Skapa ny** eller **Välj befintlig**. Alternativet för att välja ett befintligt VNet är bara tillgängligt om du har ett VNet i den valda regionen. Om du väljer **Skapa ny** anger du ett namn för VNet. Ett nytt virtuellt Resource Manager VNet med det namnet skapas. Det använder adress utrymmet `192.168.250.0/23` i den valda regionen. Om du väljer **Välj befintlig** måste du:

    a. Välj VNet-adressblock om du har fler än ett.

    b. Ange ett nytt namn på under nätet.

    c. Välj storlek på under nätet. *Kom ihåg att välja en storlek som är tillräckligt stor för att kunna hantera framtida tillväxt av din ASE.* Vi rekommenderar `/24` , som har 128 adresser och kan hantera en maximal storlek ASE. Vi rekommenderar till `/28` exempel inte att bara 16 adresser är tillgängliga. Infrastrukturen använder minst sju adresser och Azure-nätverk använder en till 5. I ett `/28` undernät har du lämnat en maximal skalning på 4 App Service plan instanser för en extern ASE och endast tre App Service plan-instanser för en ILB-ASE.

    d. Välj IP-intervall för under nätet.

1.  Välj "Konfigurera behållare".
    * Ange namnet på den anpassade avbildningen (du kan använda Azure Container Registry, Docker Hub och ditt eget privata register). Om du inte vill använda en egen anpassad behållare kan du bara ta med din kod och använda en inbyggd avbildning med App Service på Linux med hjälp av anvisningarna ovan. 

    ![Konfigurera behållare][9]

1. Välj **skapa** för att skapa ASE. Den här processen skapar även App Service plan och appen. ASE, App Service plan och app är alla under samma prenumeration och också i samma resurs grupp. Om din ASE behöver en separat resurs grupp eller om du behöver en ILB-ASE, följer du stegen för att skapa en ASE av sig själv.


## <a name="create-an-ase-by-itself"></a>Skapa en ASE själva

Om du skapar en fristående ASE, har den inget i sig. En tom ASE debiteras fortfarande för en månatlig avgift för infrastrukturen. Följ de här stegen för att skapa en ASE med en ILB eller för att skapa en ASE i en egen resurs grupp. När du har skapat din ASE kan du skapa appar i den med hjälp av normal processen. Välj den nya ASE som plats.

1. Sök på Azure Marketplace efter **App Service-miljön** eller Välj **skapa en resurs**  >  **webb mobil**  >  **App Service-miljön**. 

1. Ange namnet på din ASE. Det här namnet används för de appar som skapas i ASE. Om namnet är *mynewdemoase* är under domän namnet *. mynewdemoase.p.azurewebsites.net*. Om du skapar en app med namnet *mytestapp* kan den adresseras på mytestapp.mynewdemoase.p.azurewebsites.net. Du kan inte använda blank steg i namnet. Om du använder versaler är domän namnet den totala gemen versionen av namnet. Om du använder en ILB används inte ditt ASE-namn i under domänen, men anges i stället uttryckligen när ASE skapas.

    ![ASE namngivning][5]

1. Välj din prenumeration. Den här prenumerationen är också en som alla appar i ASE använder. Du kan inte ange din ASE i ett VNet som finns i en annan prenumeration.

1. Välj eller ange en ny resurs grupp. Resurs gruppen som används för din ASE måste vara samma som används för ditt VNet. Om du väljer ett befintligt virtuellt nätverk uppdateras resurs grupps valet för din ASE så att det återspeglar det virtuella nätverket. *Du kan skapa en ASE med en resurs grupp som skiljer sig från resurs gruppen VNet om du använder en Resource Manager-mall.* Information om hur du skapar en ASE från en mall finns i [skapa en app service-miljö från en mall][MakeASEfromTemplate].

    ![Val av resursgrupp][6]

1. Välj ditt VNet och din plats. Du kan skapa ett nytt VNet eller välja ett befintligt VNet: 

    * Om du väljer ett nytt virtuellt nätverk, kan du ange ett namn och en plats. 
    
    * Det nya virtuella nätverket har adress intervallet 192.168.250.0/23 och ett undernät med namnet default. Under nätet definieras som 192.168.250.0/24. Du kan bara välja ett virtuellt Resource Manager-nätverk. Valet av **VIP-typ** avgör om din ASE kan nås direkt från Internet (extern) eller om den använder en ILB. Mer information om de här alternativen finns i [skapa och använda en intern belastningsutjämnare med en app Services miljö][MakeILBASE]. 

      * Om du väljer **extern** för **VIP-typen** kan du välja hur många externa IP-adresser som systemet ska skapas med för IP-baserade SSL-syfte. 
    
      * Om du väljer **intern** för **VIP-typen** måste du ange den domän som din ASE använder. Du kan distribuera en ASE till ett VNet som använder offentliga eller privata adress intervall. Om du vill använda ett VNet med ett offentligt adress intervall måste du skapa VNet i förväg. 
    
    * Om du väljer ett befintligt virtuellt nätverk skapas ett nytt undernät när ASE skapas. *Du kan inte använda ett i förväg skapade undernät i portalen. Du kan skapa en ASE med ett befintligt undernät om du använder en Resource Manager-mall.* Information om hur du skapar en ASE från en mall finns i [skapa en app service-miljön från en mall][MakeASEfromTemplate].

## <a name="app-service-environment-v1"></a>App Service Environment v1

Du kan fortfarande skapa instanser av den första versionen av App Service-miljön (ASEv1). Starta processen genom att söka på Marketplace efter **App Service-miljön v1**. Du skapar ASE på samma sätt som du skapar den fristående ASE. När det är klart har din ASEv1 två klient delar och två arbetare. Med ASEv1 måste du hantera klient delar och arbetare. De läggs inte automatiskt till när du skapar dina App Service-planer. Klient delen fungerar som HTTP/HTTPS-slutpunkter och skickar trafik till arbets tagarna. Arbetarna är de roller som är värdar för dina appar. Du kan justera antalet klient delar och arbetare när du har skapat din ASE. 

Mer information om ASEv1 finns i [Introduktion till App Service-miljön v1][ASEv1Intro]. Mer information om skalning, hantering och övervakning av ASEv1 finns i [så här konfigurerar du en app service-miljön][ConfigureASEv1].

<!--Image references-->
[1]: ./media/how_to_create_an_external_app_service_environment/createexternalase-create.png
[2]: ./media/how_to_create_an_external_app_service_environment/createexternalase-aspcreate.png
[3]: ./media/how_to_create_an_external_app_service_environment/createexternalase-pricing.png
[4]: ./media/how_to_create_an_external_app_service_environment/createexternalase-embeddedcreate.png
[5]: ./media/how_to_create_an_external_app_service_environment/createexternalase-standalonecreate.png
[6]: ./media/how_to_create_an_external_app_service_environment/createexternalase-network.png
[7]: ./media/how_to_create_an_external_app_service_environment/createexternalase-createwafc.png
[8]: ./media/how_to_create_an_external_app_service_environment/createexternalase-aspcreatewafc.png
[9]: ./media/how_to_create_an_external_app_service_environment/createexternalase-configurecontainer.png



<!--Links-->
[Intro]: ./intro.md
[MakeExternalASE]: ./create-external-ase.md
[MakeASEfromTemplate]: ./create-from-template.md
[MakeILBASE]: ./create-ilb-ase.md
[ASENetwork]: ./network-info.md
[UsingASE]: ./using-an-ase.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/network-security-groups-overview.md
[ConfigureASEv1]: app-service-web-configure-an-app-service-environment.md
[ASEv1Intro]: app-service-app-service-environment-intro.md
[webapps]: ../overview.md
[mobileapps]: /previous-versions/azure/app-service-mobile/app-service-mobile-value-prop
[Functions]: ../../azure-functions/index.yml
[Pricing]: https://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/management/overview.md
