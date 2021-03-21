---
title: Använda och hantera en App Service-miljön
description: Lär dig hur du skapar, publicerar och skalar appar i en App Service-miljön. Hitta alla vanliga uppgifter i den här artikeln.
author: ccompy
ms.assetid: a22450c4-9b8b-41d4-9568-c4646f4cf66b
ms.topic: article
ms.date: 9/22/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: a7fa9ece3728214fad31f0bae769e1e50206df7e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100594054"
---
# <a name="use-an-app-service-environment"></a>Använd App Service Environment

En App Service-miljön (ASE) är en distribution av Azure App Service till ett undernät i en kunds Azure Virtual Network-instans. En ASE består av:

- **Klient** delar: där http eller https slutar i en app service-miljön
- **Arbetare**: de resurser som är värdar för dina appar
- **Databas**: innehåller information som definierar miljön
- **Lagring**: används som värd för kundens publicerade appar

Du kan distribuera en ASE med en extern eller intern virtuell IP-adress (VIP) för åtkomst till appen. En distribution med en extern VIP kallas ofta för en *extern ASE*. En distribution med en intern VIP kallas för en *ILB-ASE* eftersom den använder en intern BELASTNINGSUTJÄMNARE (ILB). Mer information om ILB-ASE finns i [skapa och använda en ILB-ASE][MakeILBASE].

## <a name="create-an-app-in-an-ase"></a>Skapa en app i en ASE

Om du vill skapa en app i en ASE använder du samma process som när du normalt skapar en app, men med några små skillnader. När du skapar en ny App Service plan:

- I stället för att välja en geografisk plats där du vill distribuera din app väljer du en ASE som plats.
- Alla App Services planer som har skapats i en ASE kan bara finnas på en isolerad pris nivå.

Om du inte har en ASE kan du skapa en genom att följa anvisningarna i [skapa en app service-miljön][MakeExternalASE].

Så här skapar du en app i en ASE:

1. Välj **skapa en resurs**  >  **webb och mobilt**  >  **-webbapp**.

1. Ange ett namn för appen. Om du redan har valt en App Service plan i en ASE, motsvarar domän namnet för appen domän namnet för ASE:

    ![Val av app-namn][1]

1. Välj en prenumeration.

1. Ange ett namn för en ny resurs grupp eller Välj **Använd befintlig** och välj en i list rutan.

1. Välj ditt operativsystem.

1. Välj en befintlig App Service plan i ASE eller skapa en ny genom att följa dessa steg:

    a. Från Azure Portal vänstra menyn väljer du **skapa en resurs > webbapp**.

    b. Välj prenumerationen.

    c. Välj eller skapa resurs gruppen.

    d. Ange namnet på din webbapp.

    e. Välj **kod** eller **DockerContainer**.

    f. Välj en körnings stack.

    ex. Välj **Linux** eller **Windows**. 

    h. Välj din ASE i list rutan **region** . 

    i. Välj eller skapa en ny App Service plan. Om du skapar en ny App Service plan väljer du lämplig **isolerad** SKU-storlek.

    ![Isolerade pris nivåer][2]

    > [!NOTE]
    > Linux-appar och Windows-appar kan inte vara i samma App Service plan, men de kan vara i samma App Service-miljön.
    >

1. Välj **Granska + skapa**, kontrol lera att informationen är korrekt och välj sedan **skapa**.

## <a name="how-scale-works"></a>Så här fungerar skala

Varje App Service App körs i en App Service plan. App Service miljöer innehåller App Service-planer och App Service planer. När du skalar en app skalar du också App Service plan och alla appar i samma plan.

När du skalar ett App Service plan läggs den infrastruktur som behövs till automatiskt. Det finns en tids fördröjning för skalning av åtgärder medan infrastrukturen läggs till. Om du utför flera skalnings åtgärder i sekvensen, behandlas den första begäran om infrastruktur skalning och de andra placeras i kö. När den första skalnings åtgärden slutförs, begär den andra infrastrukturen alla att fungera tillsammans. När infrastrukturen läggs till, tilldelas App Service planer efter behov. Att skapa en ny App Service plan är i sig en skalnings åtgärd eftersom den begär ytterligare maskin vara.

I App Service flera innehavare är skalningen omedelbart eftersom det är enkelt att använda en pool med resurser för att stödja den. I en ASE finns det ingen sådan buffert, och resurserna allokeras utifrån behov.

I en ASE kan du skala en App Service plan upp till 100 instanser. En ASE kan ha upp till 201 sammanlagt antal instanser för alla App Service planer i den ASE.

## <a name="ip-addresses"></a>IP-adresser

App Service kan allokera en särskild IP-adress till en app. Den här funktionen är tillgänglig när du har konfigurerat IP-baserad SSL, enligt beskrivningen i [BIND ett befintligt anpassat TLS/SSL-certifikat till Azure App Service][ConfigureSSL]. I en ILB-ASE kan du inte lägga till fler IP-adresser som ska användas för IP-baserad SSL.

Med en extern ASE kan du konfigurera IP-baserad SSL för din app på samma sätt som i App Service flera innehavare. Det finns alltid en extra adress i ASE, upp till 30 IP-adresser. Varje gång du använder ett, läggs en annan till så att det alltid är enkelt att komma åt en adress. En tids fördröjning krävs för att allokera en annan IP-adress. Denna fördröjning förhindrar att IP-adresser läggs till i snabb följd.

## <a name="front-end-scaling"></a>Skalning på klient Sidan

När du skalar dina App Service-planer läggs arbetare automatiskt till för att stödja dem. Varje ASE skapas med två frontend-sidor. Klient delens slut skalar automatiskt ut till en hastighet av en klient del för varje uppsättning på 15 App Service plan instanser. Om du till exempel har tre App Service planer med fem instanser, har du totalt 15 instanser och tre klient delar. Om du skalar till totalt 30 instanser har du fyra frontend-sidor. Det här mönstret fortsätter när du skalar ut.

Antalet klient delar som tilldelas som standard är lämpligt för en måttlig belastning. Du kan sänka förhållandet till så lite som en klient del för var femte instans. Du kan också ändra storleken på klient delens ändar. De är som standard en enda kärna. I Azure Portal kan du ändra storlek till två eller fyra kärnor i stället.

Det är en avgift för att ändra förhållandet eller frontend-storlekarna. Mer information finns i [Azure App Service prissättning][Pricing]. Om du vill förbättra belastnings kapaciteten för din ASE får du bättre förbättringar genom att först skala ut till två kärnor innan du justerar skalnings förhållandet. Om du ändrar huvud storleken för dina klient delar kommer din ASE att uppgraderas och bör utföras utanför ordinarie arbets tid.

Klient dels resurser är HTTP/HTTPS-slutpunkten för ASE. Med standard konfigurationen för klient delen, är minnes användningen konsekvent runt 60 procent. Den främsta orsaken till att skala dina klient delar är CPU-användning, som främst drivs av HTTPS-trafik.

## <a name="app-access"></a>Appåtkomst

I en extern ASE är det domänsuffix som används för att skapa appar *. &lt; asename &gt; . p.azurewebsites.net*. Om din ASE har namnet _external-ASE_ och du är värd för en app som heter _contoso_ i den ASE, så når du den på följande URL: er:

- contoso.external-ase.p.azurewebsites.net
- contoso.scm.external-ase.p.azurewebsites.net

Information om hur du skapar en extern ASE finns i [skapa en app service-miljön][MakeExternalASE].

I en ILB-ASE är det domänsuffix som används för att skapa appar *. &lt; asename &gt; . appserviceenvironment.net*. Om din ASE heter _ILB-ASE_ och du är värd för en app som heter _contoso_ i den ASE, så når du den på följande URL: er:

- contoso.ilb-ase.appserviceenvironment.net
- contoso.scm.ilb-ase.appserviceenvironment.net

Information om hur du skapar en ILB-ASE finns i [skapa och använda en ILB-ASE][MakeILBASE].

SCM-URL: en används för att få åtkomst till kudu-konsolen eller för att publicera din app med hjälp av webb distribution. Information om kudu-konsolen finns i [kudu-konsolen för Azure App Service][Kudu]. Kudu-konsolen ger dig ett webb gränssnitt för fel sökning, överföring av filer, redigering av filer och mycket mer.

### <a name="dns-configuration"></a>DNS-konfiguration 

När du använder en extern ASE registreras appar i dina ASE med Azure DNS. Det finns inga ytterligare steg i en extern ASE för att dina appar ska vara offentligt tillgängliga. Med en ILB-ASE måste du hantera din egen DNS. Du kan göra detta på din egen DNS-server eller med Azure DNS privata zoner.

Så här konfigurerar du DNS på din egen DNS-server med din ILB-ASE:

1. skapa en zon för &lt; ASE name &gt; . appserviceenvironment.net
1. skapa en A-post i den zonen som pekar på ILB IP-adress
1. skapa en A-post i den zonen som pekar @ på ILB IP-adress
1. skapa en zon i &lt; ASE name &gt; . appserviceenvironment.net med namnet SCM
1. skapa en A-post i SCM-zonen som pekar på ILB IP-adress

Så här konfigurerar du DNS i Azure DNS privata zoner:

1. skapa en Azure DNS privat zon med namnet &lt; ASE name &gt; . appserviceenvironment.net
1. skapa en A-post i den zonen som pekar på ILB IP-adress
1. skapa en A-post i den zonen som pekar @ på ILB IP-adress
1. skapa en A-post i den zonen som pekar *. scm till ILB-IP-adressen

DNS-inställningarna för ditt ASE standard-domänsuffix begränsar inte dina appar till att endast vara tillgängliga för dessa namn. Du kan ange ett anpassat domän namn utan att verifiera dina appar i en ILB-ASE. Om du sedan vill skapa en zon med namnet *contoso.net* kan du göra det och peka den mot ILB IP-adressen. Det anpassade domän namnet fungerar för app-begäranden, men inte för SCM-platsen. SCM-webbplatsen är bara tillgänglig på *&lt; APPNAME &gt; . scm. &lt; asename &gt; . appserviceenvironment.net*. 

Zonen med namnet *. &lt; asename &gt; . appserviceenvironment.net* är globalt unikt. Innan maj 2019 kunde kunderna ange domänsuffix för ILB-ASE. Om du vill använda *. contoso.com* för domänsuffix kan du göra det och inkludera SCM-webbplatsen. Det fanns utmaningar med denna modell, inklusive; hantera SSL-standardcertifikatet, avsaknad av enkel inloggning med SCM-platsen och kravet på att använda ett jokertecken. ILB ASE-processen för standard certifikats uppgradering avbröts också och det gjorde att programmet startades om. För att lösa dessa problem ändrades beteendet för ILB ASE till att använda ett domänsuffix baserat på namnet på ASE och med ett Microsoft-ägda suffix. Ändringen av ILB ASE-beteendet påverkar bara ILB ASE som gjorts efter maj 2019. Befintliga ILB-ASE måste fortfarande hantera standard certifikatet för ASE och deras DNS-konfiguration.

## <a name="publishing"></a>Publicera

I en ASE, som med App Service med flera innehavare, kan du publicera på följande sätt:

- Webb distribution
- FTP
- Kontinuerlig integrering (CI)
- Dra och släpp i kudu-konsolen
- En IDE, till exempel Visual Studio, Sol förmörkelse eller IntelliJ-idé

Med en extern ASE fungerar dessa publicerings alternativ på samma sätt. Mer information finns i [distribution i Azure App Service][AppDeploy].

Med en ILB-ASE är publicerings slut punkterna bara tillgängliga via ILB. ILB finns på en privat IP-adress i ASE-undernätet i det virtuella nätverket. Om du inte har nätverks åtkomst till ILB kan du inte publicera några appar på den ASE. Som anges i [skapa och Använd en ILB-ASE][MakeILBASE]måste du konfigurera DNS för apparna i systemet. Detta krav omfattar SCM-slutpunkten. Om slut punkterna inte är korrekt definierade kan du inte publicera. Din IDE: er måste också ha nätverks åtkomst till ILB för att publicera direkt till den.

Utan ytterligare ändringar fungerar inte Internet-baserade CI-system som GitHub och Azure DevOps med en ILB-ASE eftersom publicerings slut punkten inte är tillgänglig för Internet. Du kan aktivera publicering till en ILB-ASE från Azure DevOps genom att installera en lokal versions agent i det virtuella nätverket som innehåller ILB ASE. Du kan också använda ett CI-system som använder en pull-modell, till exempel Dropbox.

Publiceringsslutpunkterna för appar i en ILB ASE använder domänen som ILB ASE skapades med. Du kan se den i appens publicerings profil och i appens Portal fönster (i **översikts** information  >   och i **Egenskaper**).

## <a name="storage"></a>Storage

En ASE har 1 TB lagrings utrymme för alla appar i ASE. En App Service plan i den isolerade pris-SKU: n har en gräns på 250 GB. I en ASE läggs 250 GB lagrings utrymme per App Service plan upp till 1 TB-gränsen. Du kan ha fler App Services planer än bara fyra, men det finns ingen mer lagrings utrymme utöver gränsen på 1 TB.

## <a name="logging"></a>Loggning

Du kan integrera din ASE med Azure Monitor för att skicka loggar om ASE till Azure Storage, Azure Event Hubs eller Log Analytics. Dessa objekt loggas idag:

| Tillståndet | Meddelande |
|---------|----------|
| ASE är inte felfri | Den angivna ASE är inte felfri på grund av en ogiltig konfiguration av virtuellt nätverk. ASE inaktive ras om ohälsosamt tillstånd fortsätter. Se till att de rikt linjer som anges här följs: https://docs.microsoft.com/azure/app-service/environment/network-info . |
| ASE-undernätet är nästan slut på utrymme | Den angivna ASE finns i ett undernät som är nästan slut på utrymme. Det finns {0} återstående adresser. När de här adresserna är uttömda kan ASE inte skalas.  |
| ASE närmar sig den totala instans gränsen | Den angivna ASE närmar sig den totala instans gränsen för ASE. Den innehåller för närvarande {0} App Service plans instanser av högst 201 instanser. |
| ASE kan inte komma åt ett beroende | Det angivna ASE kan inte uppnås {0} .  Se till att de rikt linjer som anges här följs: https://docs.microsoft.com/azure/app-service/environment/network-info . |
| ASE är inaktive rad | Den angivna ASE har pausats. ASE-inskjutningen kan bero på en konto brister eller en ogiltig konfiguration av virtuellt nätverk. Lös rotor saken och återuppta ASE för att fortsätta betjäna trafiken. |
| ASE-uppgraderingen har startat | En plattforms uppgradering till den angivna ASE har påbörjats. Förväntar sig fördröjningar vid skalnings åtgärder. |
| ASE-uppgraderingen har slutförts | En plattforms uppgradering till den angivna ASE har avslut ATS. |
| Skalnings åtgärder har startat | En App Service plan ( {0} ) har börjat skala. Önskat tillstånd: {1} I {2} arbetare.
| Skalnings åtgärder har slutförts | Skalningen av en App Service plan ( {0} ) har avslut ATS. Nuvarande tillstånd: {1} I {2} arbetare. |
| Skalnings åtgärder har misslyckats | {0}Det gick inte att skala en app service plan (). Nuvarande tillstånd: {1} I {2} arbetare. |

Så här aktiverar du loggning på din ASE:

1. I portalen går du till **diagnostikinställningar**.
1. Välj **Lägg till diagnostikinställning**.
1. Ange ett namn för logg integreringen.
1. Välj och konfigurera de logg destinationer som du vill använda.
1. Välj **AppServiceEnvironmentPlatformLogs**.

![Logg inställningar för ASE-diagnostik][4]

Om du integrerar med Log Analytics kan du se loggarna genom att välja **loggar** från ASE-portalen och skapa en fråga mot **AppServiceEnvironmentPlatformLogs**. Loggar genereras bara när ASE har en händelse som ska utlösa den. Om din ASE inte har en sådan händelse kommer det inte att finnas några loggar. Om du snabbt vill se ett exempel på loggar i arbets ytan Log Analytics kan du utföra en skalnings åtgärd med något av App Services planerna i din ASE. Du kan sedan köra en fråga mot **AppServiceEnvironmentPlatformLogs** för att se dessa loggar. 

**Skapa en avisering**

Om du vill skapa en avisering mot loggarna följer du anvisningarna i [skapa, Visa och hantera logg aviseringar med hjälp av Azure Monitor](../../azure-monitor/alerts/alerts-log.md). I korthet:

* Öppna sidan aviseringar i ASE-portalen
* Välj **ny aviserings regel**
* Välj din resurs som Log Analytics arbets yta
* Ange villkoret med en anpassad loggs ökning för att använda en fråga som "AppServiceEnvironmentPlatformLogs | där ResultDescription innehåller "har startat skalning" eller vad du vill. Ange tröskelvärdet efter behov. 
* Lägg till eller skapa en åtgärds grupp som du vill. I åtgärds gruppen definierar du svaret på aviseringen, till exempel att skicka ett e-postmeddelande eller ett SMS-meddelande
* Ge aviseringen ett namn och spara den.

## <a name="upgrade-preference"></a>Uppgraderings inställning

Om du har flera ASE kanske du vill att vissa ASE ska uppgraderas före andra. I ASE **HostingEnvironment Resource Manager** -objektet kan du ange ett värde för **upgradePreference**. Inställningen **upgradePreference** kan konfigureras med hjälp av en mall, ARMClient eller https://resources.azure.com . De tre möjliga värdena är:

- **Ingen**: Azure kommer att uppgradera din ASE utan någon särskild batch. Detta värde är standard.
- **Tidigt**: din ASE kommer att uppgraderas i den första hälften av App Service uppgraderingar.
- **Sent**: din ASE kommer att uppgraderas i den andra halvan av App Service uppgraderingar.

Om du använder https://resources.azure.com följer du dessa steg för att ange värdet **upgradePreferences** :

1. Gå till resources.azure.com och logga in med ditt Azure-konto.
1. Gå igenom resurser till prenumerationer \/ \[ prenumerations namn \] \/ resourceGroups \/ \[ resurs grupp namn \] \/ leverantörer \/ Microsoft. Web \/ hostingEnvironments \/ \[ ASE name \] .
1. Välj **Läs/skriv** överst.
1. Välj **Redigera**.
1. Ange **upgradePreference** till det som är ett av de tre värdena som du vill ha.
1. Välj **korrigering**.

![resurser Azure com-visning][5]

Funktionen **upgradePreferences** är den mest begripliga när du har flera ASE eftersom din "tidiga" ASE kommer att uppgraderas före "sent" ASE. När du har flera ASE bör du ställa in din utvecklings-och test-ASE så att den blir "tidig" och produktions ASE ska vara "sent".

## <a name="pricing"></a>Priser

Pris-SKU: n som kallas *isolerad* är endast för användning med ASE. Alla App Service planer som finns i ASE finns i den isolerade pris-SKU: n. De isolerade priserna för App Service planer kan variera beroende på region.

Utöver priset för dina App Service-planer finns det en fast pris nivå för själva ASE. Den fasta hastigheten ändras inte med storleken på din ASE. Den betalar för ASE-infrastrukturen på en standard skalnings takt för varje 15 App Service plan instans.

Om standard skalnings takten för en klient del för varje 15 App Service plan-instans inte är tillräckligt snabb, kan du justera förhållandet mellan front-och utgångs ändar. När du justerar förhållandet eller storleken betalar du för de front-end-kärnor som inte ska läggas till som standard.

Om du till exempel ändrar skalnings förhållandet till 10 läggs en klient del till för varje 10-instans i dina App Service planer. Den fasta avgiften omfattar en skalnings takt för en klient del för var 15: a instans. Med en skalnings kvot på 10 betalar du en avgift för den tredje klient delen som har lagts till för de 10 App Service plan instanserna. Du behöver inte betala för det när du når 15 instanser, eftersom den lades till automatiskt.

Om du justerar storleken på front ändarna till två kärnor, men inte justerar förhållandet, betalar du för de extra kärnorna. En ASE skapas med två klient delar, så till och med under tröskelvärdet för automatisk skalning betalar du för två extra kärnor om du har ökat storleken till två kärnor.

Mer information finns i [Azure App Service prissättning][Pricing].

## <a name="delete-an-ase"></a>Ta bort en ASE

Så här tar du bort en ASE:

1. Välj **ta bort** överst i **App Service-miljöns** fönstret.

1. Ange namnet på din ASE för att bekräfta att du vill ta bort den. När du tar bort en ASE tar du även bort allt innehåll i den.

    ![Borttagning av ASE][3]

1. Välj **OK**.

## <a name="ase-cli"></a>ASE CLI

Det finns kommando rads funktioner för att administrera till en ASE.  AZ CLI-kommandona anges nedan.

```azurecli
C:\>az appservice ase --help

Group
    az appservice ase : Manage App Service Environments v2.
        This command group is in preview. It may be changed/removed in a future release.
Commands:
    create         : Create app service environment.
    delete         : Delete app service environment.
    list           : List app service environments.
    list-addresses : List VIPs associated with an app service environment.
    list-plans     : List app service plans associated with an app service environment.
    show           : Show details of an app service environment.
    update         : Update app service environment.

For more specific examples, use: az find "az appservice ase"
```



<!--Image references-->
[1]: ./media/using_an_app_service_environment/usingase-appcreate.png
[2]: ./media/using_an_app_service_environment/usingase-pricingtiers.png
[3]: ./media/using_an_app_service_environment/usingase-delete.png
[4]: ./media/using_an_app_service_environment/usingase-logsetup.png
[4]: ./media/using_an_app_service_environment/usingase-logs.png
[5]: ./media/using_an_app_service_environment/usingase-upgradepref.png

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
[Functions]: ../../azure-functions/index.yml
[Pricing]: https://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/management/overview.md
[ConfigureSSL]: ../configure-ssl-certificate.md
[Kudu]: https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[AppDeploy]: ../deploy-local-git.md
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../web-application-firewall/ag/ag-overview.md
[logalerts]: ../../azure-monitor/alerts/alerts-log.md
