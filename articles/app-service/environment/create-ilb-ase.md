---
title: Skapa en ILB-ASE med ARM
description: Lär dig hur du skapar en App Service-miljö med en intern belastningsutjämnare (ILB ASE) med Azure Resource Manager-mallar. Isolera dina appar helt från Internet.
author: ccompy
ms.assetid: 0f4c1fa4-e344-46e7-8d24-a25e247ae138
ms.topic: quickstart
ms.date: 09/16/2020
ms.author: ccompy
ms.custom: mvc, seodec18
ms.openlocfilehash: 27c9198558a730d0af49077d6f5baa6db4789416
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96009559"
---
# <a name="create-and-use-an-internal-load-balancer-app-service-environment"></a>Skapa och Använd en intern Load Balancer App Service-miljön 

Azure App Service Environment är en distribution av Azure App Service till ett undernät i ett virtuellt Azure-nätverk (VNet). Det går att distribuera en App Service-miljö (ASE) på två sätt: 

- Med en VIP på en extern IP-adress som ofta kallas för en extern ASE.
- Med en VIP på en intern IP-adress, som ofta kallas ILB ASE eftersom den interna slutpunkten är en intern lastbalanserare (ILB). 

Den här artikeln visar hur du kan skapa en intern belastningsutjämnare i apptjänstmiljö. En översikt över ASE finns i [Introduktion till App Service miljöer][Intro]. Om du vill veta hur du skapar en extern ASE läser du [Create an External ASE][MakeExternalASE] (Skapa en extern ASE).

## <a name="overview"></a>Översikt 

Du kan distribuera en ASE med en internet-tillgänglig slutpunkt eller med en IP-adress i ditt VNet. Om du ska konfigurera IP-adressen till en VNet-adress måste ASE vara distribuerad med en ILB. När du distribuerar din ASE med en ILB måste du ange namnet på din ASE. Namnet på din ASE används i domänsuffix för apparna i din ASE.  Domänsuffixet för din ILB-ASE är &lt; ASE name &gt; . appserviceenvironment.net. Appar som görs i en ILB-ASE placeras inte i den offentliga DNS-tjänsten. 

Tidigare versioner av ILB-ASE krävde att du anger ett domänsuffix och ett standard certifikat för HTTPS-anslutningar. Domänsuffixet samlas inte in längre vid skapande av ILB-ASE och ett standard certifikat samlas inte längre in. När du skapar en ILB-ASE nu tillhandahålls standard certifikatet av Microsoft och är betrott av webbläsaren. Du kan fortfarande ange anpassade domän namn på appar i din ASE och ange certifikat för dessa anpassade domän namn. 

Med en ILB-ASE kan du göra följande:

-   Vara värd för intranät program på ett säkert sätt i molnet, som du kommer åt via en plats-till-plats-eller ExpressRoute.
-   Skydda appar med en WAF-enhet
-   Vara värd för appar i molnet som inte listas i offentliga DNS-servrar.
-   Skapa internet-isolerade appar för serverdelar, som dina appar för klientdelar säkert kan integrera med.

### <a name="disabled-functionality"></a>Inaktiverad funktion ###

Det finns några saker som du inte kan göra när du använder en ILB ASE:

-   Använd IP-baserad SSL.
-   Tilldela IP-adresser till specifika appar.
-   Köp och använd ett certifikat med en app via Azure-portalen. Du kan hämta certifikat direkt från en certifikatutfärdare och använda dem med dina appar. Du kan inte hämta dem via Azure-portalen.

## <a name="create-an-ilb-ase"></a>Skapa en intern belastningsutjämnare i apptjänstmiljö ##

Så här skapar du en intern belastningsutjämnare i apptjänstmiljö:

1. I Azure Portal väljer du **skapa en resurs**  >  **webb**  >  **App Service-miljön**.

2. Välj din prenumeration.

3. Välj eller skapa en Resursgrupp.

4. Ange namnet på App Service-miljön.

5. Välj virtuell IP-typ för intern.

    ![ASE-generering](media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase.png)

> [!NOTE]
> App Service-miljön namnet får innehålla högst 37 tecken.

6. Välj nätverk

7. Välj eller skapa en Virtual Network. Om du skapar ett nytt VNet här så definieras det med ett adress intervall på 192.168.250.0/23. Om du vill skapa ett VNet med ett annat adress intervall eller i en annan resurs grupp än ASE använder du portalen för att skapa Azure Virtual Network. 

8. Välj eller skapa ett tomt undernät. Om du vill välja ett undernät måste det vara tomt och inte delegerat. Under näts storleken kan inte ändras efter att ASE har skapats. Vi rekommenderar en storlek på `/24`, som har 256 adresser och kan hantera en ASE med maximal storlek och olika skalningsbehov. 

    ![ASE nätverk][1]

7. Välj **Granska och skapa och** Välj sedan **skapa**.


## <a name="create-an-app-in-an-ilb-ase"></a>Skapa en app i en ILB ASE ##

Du skapar en app i en ILB ASE på samma sätt som du skapar en app i en ASE vanligtvis.

1. I Azure Portal väljer du **skapa en resurs**  >  **webb**  >  **webbapp**.

1. Ange appens namn.

1. Välj prenumerationen.

1. Välj eller skapa en Resursgrupp.

1. Välj din publicering, körnings stack och operativ system.

1. Välj en plats där platsen är en befintlig ILB-ASE.  Du kan också skapa en ny ASE när du skapar appar genom att välja ett isolerat App Service plan. Om du vill skapa en ny ASE väljer du den region som du vill att ASE ska skapas i.

1. Välj eller skapa en App Service plan. 

1. Välj **Granska och skapa och** Välj sedan **skapa** när du är klar.

### <a name="web-jobs-functions-and-the-ilb-ase"></a>Webbjobb, Functions och ILB ASE 

Både Functions och webbjobb går att använda på en ILB ASE, men för att portalen ska fungera med dem måste du ha nätverksåtkomst till SCM-webbplatsen.  Det innebär att din webbläsare antingen måste vara på en värd som är i eller anslutet till det virtuella nätverket. Om din ILB-ASE har ett domän namn som inte slutar med *appserviceenvironment.net* måste du be webbläsaren att lita på https-certifikatet som används av din SCM-webbplats.

## <a name="dns-configuration"></a>DNS-konfiguration 

När du använder en extern ASE registreras appar i dina ASE med Azure DNS. Det finns inga ytterligare steg i en extern ASE för att dina appar ska vara offentligt tillgängliga. Med en ILB-ASE måste du hantera din egen DNS. Du kan göra detta på din egen DNS-server eller med Azure DNS privata zoner.

Så här konfigurerar du DNS på din egen DNS-server med din ILB-ASE:

1. skapa en zon för &lt; ASE name &gt; . appserviceenvironment.net
2. skapa en A-post i den zonen som pekar på ILB IP-adress
3. skapa en A-post i den zonen som pekar @ på ILB IP-adress
4. skapa en zon i &lt; ASE name &gt; . appserviceenvironment.net med namnet SCM
5. skapa en A-post i SCM-zonen som pekar på ILB IP-adress

Så här konfigurerar du DNS i Azure DNS privata zoner:

1. skapa en Azure DNS privat zon med namnet &lt; ASE name &gt; . appserviceenvironment.net
2. skapa en A-post i den zonen som pekar på ILB IP-adress
3. skapa en A-post i den zonen som pekar @ på ILB IP-adress
4. skapa en A-post i den zonen som pekar *. scm till ILB-IP-adressen

DNS-inställningarna för ditt ASE standard-domänsuffix begränsar inte dina appar till att endast vara tillgängliga för dessa namn. Du kan ange ett anpassat domän namn utan att verifiera dina appar i en ILB-ASE. Om du sedan vill skapa en zon med namnet contoso.net kan du göra det och peka den mot ILB IP-adressen. Det anpassade domän namnet fungerar för app-begäranden, men inte för SCM-platsen. SCM-webbplatsen är bara tillgänglig på &lt; APPNAME &gt; . scm. &lt; asename &gt; . appserviceenvironment.net.

Zonen med namnet. &lt; asename &gt; . appserviceenvironment.net är globalt unikt. Innan maj 2019 kunde kunderna ange domänsuffix för ILB-ASE. Om du vill använda. contoso.com för domänsuffix kan du göra det och inkludera SCM-webbplatsen. Det fanns utmaningar med denna modell, inklusive; hantera SSL-standardcertifikatet, avsaknad av enkel inloggning med SCM-platsen och kravet på att använda ett jokertecken. ILB ASE-processen för standard certifikats uppgradering avbröts också och det gjorde att programmet startades om. För att lösa dessa problem ändrades beteendet för ILB ASE till att använda ett domänsuffix baserat på namnet på ASE och med ett Microsoft-ägda suffix. Ändringen av ILB ASE-beteendet påverkar bara ILB ASE som gjorts efter maj 2019. Befintliga ILB-ASE måste fortfarande hantera standard certifikatet för ASE och deras DNS-konfiguration.

## <a name="publish-with-an-ilb-ase"></a>Publicera med en ILB ASE

För varje app som skapas finns det två slutpunkter. I en ILB-ASE har du ett *&lt; namn på appen &gt; . &lt; ILB ASE- &gt; domän* och *&lt; app-namn &gt; . scm. &lt; ILB ASE- &gt; domän*. 

SCM-webbplatsens namn tar dig till Kudu-konsolen som heter **Avancerad portal** inom Azure-portalen. Med Kudu-konsolen kan du visa miljövariabler, utforska disken, använda en konsol och mycket mer. Mer information finns i [Kudu console for Azure App Service][Kudu] (Kudu-konsol för Azure App Service). 

Internetbaserade CI-system, t.ex GitHub och Azure DevOps, fungerar fortfarande med en ILB ASE om Build Agent är tillgänglig via Internet och på samma nätverk som ILB ASE. För Azure DevOps gäller att om Build Agent har skapats på samma virtuella nätverk som ILB ASE (olika undernät går bra) kan den hämta koden från Azure DevOps-git och distribuera till ILB ASE. Om du inte vill skapa en egen Build Agent måste du använda ett CI-system som använder en pull-modell, till exempel Dropbox.

Publiceringsslutpunkterna för appar i en ILB ASE använder domänen som ILB ASE skapades med. Den här domänen visas i appens publicerings profil och i appens Portal blad (**Översikt**  >  **Essentials** och även **Egenskaper**). Om du har en ILB-ASE med domänsuffix *&lt; ASE name &gt; . appserviceenvironment.net* och en app som heter *test* använder du test av *test. &lt; ASE name &gt; . appserviceenvironment.net* för FTP och *mytest.scm.contoso.net* för webb distribution.

## <a name="configure-an-ilb-ase-with-a-waf-device"></a>Konfigurera en ILB-ASE med en WAF-enhet ##

Du kan kombinera en brand vägg för webbaserade program (WAF) med ILB-ASE för att bara visa de appar som du vill använda på Internet och hålla resten tillgänglig enbart från i VNet. På så sätt kan du bygga säkra program på flera nivåer bland andra saker.

Mer information om hur du konfigurerar ILB-ASE med en WAF-enhet finns i [Konfigurera en brand vägg för webbaserade program med din app service-miljö][ASEWAF]. Den här artikeln visar hur du använder en virtuell Barracuda-installation med din apptjänstmiljö. Ett annat alternativ är att använda Azure Application Gateway. Application Gateway använder OWASP-kärnregler för att göra programmen säkra. Mer information om Application Gateway finns i [Introduction to the Azure web application firewall][AppGW] (Introduktion till Azures brandvägg för webbaserade program).

## <a name="ilb-ases-made-before-may-2019"></a>ILB ASE som gjorts före maj 2019

ILB-ASE som gjordes före maj 2019 krävde att du ställer in domänsuffix under ASE skapas. Du måste också ladda upp ett standard certifikat som baseras på det domänsuffix. Med en äldre ILB-ASE kan du också inte utföra enkel inloggning till kudu-konsolen med appar i som ILB ASE. När du konfigurerar DNS för en äldre ILB-ASE måste du ange jokertecken för en post i en zon som matchar ditt domänsuffix. 

## <a name="get-started"></a>Kom igång ##

* Information om hur du kommer igång med ASE finns i [Introduktion till App Service-miljöer][Intro]. 

<!--Image references-->
[1]: ./media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase-network.png
[2]: ./media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase-webapp.png
[5]: ./media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase-ipaddresses.png

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
[ConfigureSSL]: ../configure-ssl-certificate.md
[Kudu]: https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../web-application-firewall/ag/ag-overview.md
[customdomain]: ../app-service-web-tutorial-custom-domain.md
[linuxapp]: ../overview.md#app-service-on-linux
