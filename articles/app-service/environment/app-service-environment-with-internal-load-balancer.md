---
title: Skapa en ILB-ASE v1
description: Skapa och använda en ASE med en ILB. Detta dokument tillhandahålls endast för kunder som använder den äldre v1-ASE.
author: ccompy
ms.assetid: ad9a1e00-d5e5-413e-be47-e21e5b285dbf
ms.topic: article
ms.date: 07/11/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: c4e5dedf2075a2e13cc91c5eed2c0f03ba498b97
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96021528"
---
# <a name="using-an-internal-load-balancer-with-an-app-service-environment"></a>Använda en intern Load Balancer med ett App Service-miljön

> [!NOTE] 
> Den här artikeln gäller App Service-miljön v1. Det finns en nyare version av App Service-miljön som är enklare att använda och som körs på en kraftfullare infrastruktur. Om du vill veta mer om den nya versionen börjar [du med introduktionen till App Service-miljön](intro.md).
>

Funktionen App Service-miljön (ASE) är ett premium tjänst alternativ för Azure App Service som ger en förbättrad konfigurations funktion som inte är tillgänglig i flera klientens stämplar. Funktionen ASE distribuerar i princip Azure App Service i Azure-Virtual Network (VNet). För att få en bättre förståelse för de funktioner som erbjuds av App Services miljöer läser du dokumentationen [Vad är en app service-miljön][WhatisASE] . Om du inte känner till fördelarna med att arbeta i ett virtuellt nätverk läser du [vanliga frågor och svar om Azure Virtual Network][virtualnetwork]. 

## <a name="overview"></a>Översikt
En ASE kan distribueras med en tillgänglig slut punkt för Internet eller med en IP-adress i ditt VNet. För att ange IP-adressen till en VNet-adress måste du distribuera din ASE med ett internt Load Balancer (ILB). När din ASE har kon figurer ATS med en ILB, kan du ange:

* din egen domän eller under domän. För att göra det enkelt förutsätter det här dokumentet under domänen, men du kan konfigurera det på något av sätt. 
* certifikatet som används för HTTPS
* DNS-hantering för under domänen. 

I utbyte kan du göra saker om att:

* värdbaserade intranät program, t. ex. branschspecifika program, säkert i molnet som du kommer åt via en plats till en plats eller ExpressRoute VPN
* värdbaserade appar i molnet som inte listas i offentliga DNS-servrar
* Skapa webb isolerade Server dels appar med vilka dina frontend-appar kan integrera på ett säkert sätt

#### <a name="disabled-functionality"></a>Inaktiverad funktion
Det finns några saker som du inte kan göra när du använder en ILB-ASE. Dessa är bland annat:

* använda IPSSL
* tilldela IP-adresser till vissa appar
* köpa och använda ett certifikat med en app via portalen. Du kan naturligtvis Hämta certifikat direkt med en certifikat utfärdare och använda dem med dina appar, men inte via Azure Portal.

## <a name="creating-an-ilb-ase"></a>Skapa en ILB-ASE
Att skapa en ILB-ASE är inte mycket annorlunda än att skapa en ASE normalt. En djupare diskussion om hur du skapar en ASE finns i [så här skapar du en app service-miljön][HowtoCreateASE]. Processen för att skapa en ILB-ASE är densamma som för att skapa ett VNet under ASE eller för att välja ett befintligt VNet. Så här skapar du en intern belastningsutjämnare i apptjänstmiljö: 

1. I Azure Portal väljer du **skapa en resurs-> webb och mobilt-> App Service-miljön**.
2. Välj din prenumeration.
3. Välj eller skapa en Resursgrupp.
4. Välj eller skapa ett virtuellt nätverk (VNet).
5. Skapa ett undernät om du väljer ett VNet.
6. Välj **Virtual Network/plats-> VNet-konfiguration** och ange intern VIP-typ.
7. Ange ett under domän namn (det här namnet är den under domän som används för appar som skapats i den här ASE).
8. Välj **OK** och sedan **skapa**.

![Visar de skärmar som används för att skapa en ILB-ASE.][1]

I rutan Virtual Network finns det ett konfigurations alternativ för virtuellt nätverk som låter dig välja mellan en extern VIP-eller intern VIP. Standardinställningen är Extern. Om du har angett till extern använder din ASE en tillgänglig VIP för Internet. Om du väljer Intern är din ASE konfigurerad med en ILB på en IP-adress inom ditt VNet. 

När du har valt intern tas möjligheten att lägga till fler IP-adresser till din ASE bort, och i stället måste du ange under domänen för ASE. I en ASE med en extern VIP används namnet på ASE i under domänen för appar som har skapats i den ASE. Om din ASE har namnet ***contosotest** _ och din app i denna ASE heter _*_test_*_, är under domänen formatet _*_contosotest.p.azurewebsites.net_*_ och URL: en för appen är _* _mytest.contosotest.p.azurewebsites.net_* *. Om du ställer in VIP-typen till Internal, används inte ditt ASE-namn i under domänen för ASE. Du anger en under domän explicit. Om din under domän är ***contoso.Corp.net**_ och du har gjort en app i ASE med namnet _*_timereporting_*_, är URL: en för den appen _ *_timereporting.contoso.Corp.net_* *.

## <a name="apps-in-an-ilb-ase"></a>Appar i en ILB-ASE
Att skapa en app i en ILB-ASE är detsamma som att skapa en app i en ASE på vanligt sätt. 

1. I Azure Portal väljer du **skapa en resurs-> webb och mobilt-> webb-** eller **mobil** -eller **API-app**.
2. Ange appens namn.
3. Välj din prenumeration.
4. Välj eller skapa en Resursgrupp.
5. Välj eller skapa en App Service plan (ASP). Om du skapar en ny ASP väljer du ASE som plats och väljer den arbets grupp som du vill att din ASP ska skapas i. När du skapar ASP väljer du ASE som plats och arbets grupp. När du anger namnet på appen kommer du att se att under domänen under ditt program namn ersätts av under domänen för din ASE. 
6. Välj **Skapa**. Se till att markera kryss rutan **Fäst på instrument panelen** om du vill att appen ska visas på instrument panelen. 

![Visar hur du skapar en app i en ILB-ASE i Azure Portal.][2]

Under namnet på appen uppdateras under domän namnet så att det återspeglar under domänen för din ASE. 

## <a name="post-ilb-ase-creation-validation"></a>ILB ASE-skapande validering
En ILB ASE skiljer sig något från en icke-ILB ASE. Som redan antecknas måste du hantera din egen DNS och du måste också ange ditt eget certifikat för HTTPS-anslutningar. 

När du har skapat din ASE ser du att under domänen visar under domänen som du har angett och att det finns ett nytt objekt på **inställnings** menyn med namnet **ILB-certifikat**. ASE skapas med ett självsignerat certifikat som gör det enklare att testa HTTPS. Portalen visar att du måste ange ditt eget certifikat för HTTPS, men det är för att hjälpa dig att be dig att ha ett certifikat som går med under domänen. 

![Visar den under domän som du angav när du skapade din ASE.][3]

Om du bara testar saker och inte vet hur du skapar ett certifikat kan du använda IIS MMC-konsolen för att skapa ett självsignerat certifikat. När den har skapats kan du exportera den som en. pfx-fil och sedan ladda upp den i ILB-certifikatets användar gränssnitt. När du ansluter till en plats som skyddas med ett självsignerat certifikat ger webbläsaren dig en varning om att den plats som du använder inte är säker på grund av att det inte går att validera certifikatet. Om du vill undvika den varningen behöver du ett signerat certifikat som matchar din under domän och har en förtroende kedja som identifieras av din webbläsare.

![Visar hur du använder IIS MMC-konsolen för att skapa ett självsignerat certifikat.][6]

Om du vill testa flödet med dina egna certifikat och testa både HTTP-och HTTPS-åtkomst till din ASE:

1. Gå till ASE-ANVÄNDARGRÄNSSNITTET när ASE har skapats **ASE-> Settings-> ILB-certifikat**.
2. Ange ILB-certifikat genom att välja certifikatets PFX-fil och ange lösen ord. Det här steget tar lite tid att bearbeta och meddelandet om att en skalnings åtgärd pågår visas.
3. Hämta ILB-adressen för dina ASE (**ASE->-egenskaper-> virtuella IP-adress**).
4. Skapa en webbapp i ASE när du har skapat den. 
5. Skapa en virtuell dator om du inte har en i detta VNET (inte i samma undernät som ASE eller sakernas rast).
6. Ange DNS för under domänen. Du kan använda ett jokertecken med under domänen i din DNS eller om du vill göra några enkla tester, redigera hosts-filen på den virtuella datorn för att ange webb program namn till VIP-IP-adress. Om din ASE hade under domän namnet. ilbase.com och du har skapat webbappen mytestapp så att den kan åtgärdas på mytestapp.ilbase.com, ange det i värd filen. (I Windows är Hosts-filen på C:\Windows\System32\drivers\etc\)
7. Använd en webbläsare på den virtuella datorn och gå till `https://mytestapp.ilbase.com` (eller något annat namn på din webbapp med din under domän).
8. Använd en webbläsare på den virtuella datorn och gå till `https://mytestapp.ilbase.com`. Du måste acceptera bristen på säkerhet om du använder ett självsignerat certifikat. 

IP-adressen för din ILB visas i egenskaperna som den virtuella IP-adressen.

![Visar att IP-adressen för din ILB visas i egenskaperna som den virtuella IP-adressen.][4]

## <a name="using-an-ilb-ase"></a>Använda en ILB-ASE
#### <a name="network-security-groups"></a>Nätverkssäkerhetsgrupper
En ILB-ASE möjliggör nätverks isolering för dina appar. Apparna är inte tillgängliga eller även kända av Internet. Den här metoden är utmärkt för att vara värd för intranät platser, till exempel branschspecifika program. När du behöver begränsa åtkomst ytterligare kan du fortfarande använda nätverks säkerhets grupper (NSG: er) för att kontrol lera åtkomst på nätverks nivå. 

Om du vill använda NSG: er för att ytterligare begränsa åtkomsten, måste du se till att du inte bryter den kommunikation som ASE behöver för att kunna fungera. Även om HTTP/HTTPS-åtkomst endast är via ILB som används av ASE, är ASE fortfarande beroende av resurser utanför det virtuella nätverket. För att se vilken nätverks åtkomst som fortfarande krävs, se [kontrol lera inkommande trafik till en app service-miljön][ControlInbound] -och  [nätverks konfigurations information för App Service miljöer med ExpressRoute][ExpressRoute]. 

Om du vill konfigurera din NSG: er måste du känna till den IP-adress som används av Azure för att hantera din ASE. IP-adressen är också den utgående IP-adressen från din ASE om den gör Internet-begäranden. Den utgående IP-adressen för din ASE är fortfarande statisk under ASE livs längd. Om du tar bort och återskapar din ASE, får du en ny IP-adress. Du hittar IP-adressen genom att gå till **Inställningar-> egenskaper** och hitta den **utgående IP-adressen**. 

![Visar var du kan hitta den utgående IP-adressen för din ASE.][5]

#### <a name="general-ilb-ase-management"></a>Allmän hantering av ILB-ASE
Att hantera en ILB-ASE är i stort sett detsamma som att hantera en ASE på vanligt sätt. Du måste skala upp dina Worker-pooler så att de är värdar för fler ASP-instanser och skala upp dina klient dels servrar för att hantera ökade mängder HTTP/HTTPS-trafik. Allmän information om hur du hanterar konfigurationen av en ASE finns i [Konfigurera en app service-miljön][ASEConfig]. 

De ytterligare hanterings objekten är certifikat hantering och DNS-hantering. Du måste hämta och ladda upp certifikatet som används för HTTPS när ILB ASE har skapats och bytt ut det innan det går ut. Eftersom Azure äger bas domänen kan den tillhandahålla certifikat för ASE med en extern VIP. Eftersom under domänen som används av en ILB-ASE kan vara något måste du ange ditt eget certifikat för HTTPS. 

#### <a name="dns-configuration"></a>DNS-konfiguration
När du använder en extern VIP hanteras DNS av Azure. Appar som skapas i din ASE läggs till automatiskt till Azure DNS, som är en offentlig DNS. I en ILB ASE måste du hantera din egen DNS. För en viss under domän, till exempel contoso.corp.net, måste du skapa DNS A-poster som pekar på din ILB-adress för:

- \*
- *. scm
- ftp
- publish

## <a name="getting-started"></a>Komma igång
Information om hur du kommer igång med App Service miljöer finns i [Introduktion till App Service miljöer][WhatisASE]

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!--Image references-->
[1]: ./media/app-service-environment-with-internal-load-balancer/ilbase-createilbase.png
[2]: ./media/app-service-environment-with-internal-load-balancer/ilbase-createapp.png
[3]: ./media/app-service-environment-with-internal-load-balancer/ilbase-newase.png
[4]: ./media/app-service-environment-with-internal-load-balancer/ilbase-vip.png
[5]: ./media/app-service-environment-with-internal-load-balancer/ilbase-externalvip.png
[6]: ./media/app-service-environment-with-internal-load-balancer/ilbase-ilbcertificate.png

<!--Links-->
[WhatisASE]: app-service-app-service-environment-intro.md
[HowtoCreateASE]: app-service-web-how-to-create-an-app-service-environment.md
[ControlInbound]: app-service-app-service-environment-control-inbound-traffic.md
[virtualnetwork]: ../../virtual-network/virtual-networks-faq.md
[AppServicePricing]: https://azure.microsoft.com/pricing/details/app-service/
[ASEAutoscale]: app-service-environment-auto-scale.md
[ExpressRoute]: app-service-app-service-environment-network-configuration-expressroute.md
[vnetnsgs]: ../../virtual-network/virtual-network-vnet-plan-design-arm.md
[ASEConfig]: app-service-web-configure-an-app-service-environment.md