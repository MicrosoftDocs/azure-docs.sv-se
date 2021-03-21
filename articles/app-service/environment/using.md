---
title: Använd App Service Environment
description: Lär dig hur du använder App Service-miljön för att vara värd för isolerade program.
author: ccompy
ms.assetid: 377fce0b-7dea-474a-b64b-7fbe78380554
ms.topic: article
ms.date: 11/16/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: d4cd673b5029d8379a699becd7339a265c787390
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100586407"
---
# <a name="using-an-app-service-environment"></a>Använda en App Service-miljön

App Service-miljön (ASE) är en enda klient distribution av Azure App Service som infogar direkt i ett Azure-Virtual Network (VNet) som du väljer. Det är ett system som bara används av en kund. Appar som distribueras till ASE omfattas av de nätverksfunktioner som tillämpas på ASE-undernätet. Det finns inga ytterligare funktioner som behöver aktive ras i dina appar för att omfattas av dessa nätverksfunktioner. 

## <a name="create-an-app-in-an-ase"></a>Skapa en app i en ASE

Om du vill skapa en app i en ASE använder du samma process som när du normalt skapar en app, men med några små skillnader. När du skapar en ny App Service plan:

- I stället för att välja en geografisk plats där du vill distribuera din app väljer du en ASE som plats.
- Alla App Services planer som skapats i en ASE kan bara finnas i en isolerad v2-pris nivå.

Om du inte har en ASE kan du skapa en genom att följa anvisningarna i [skapa en app service-miljön][MakeASE].

Så här skapar du en app i en ASE:

1. Välj **skapa en resurs**  >  **webb och mobilt**  >  **-webbapp**.

1. Välj en prenumeration.

1. Ange ett namn för en ny resurs grupp eller Välj **Använd befintlig** och välj en i list rutan.

1. Ange ett namn för appen. Om du redan har valt en App Service plan i en ASE, motsvarar domän namnet för appen domän namnet för ASE:

    ![skapa en app i en ASE][1]

1. Välj publicerings typ, stack och operativ system.

1.  Välj region. Här måste du välja en befintlig App Service-miljön v3.  Det går inte att skapa en ASEv3 när appen skapas 

1. Välj en befintlig App Service plan i ASE eller skapa en ny. Om du skapar en ny app väljer du den storlek som du vill använda för App Service plan. Den enda SKU som du kan välja för din app är en isolerad v2-pris-SKU.

    ![Isolerade v2-pris nivåer][2]

    > [!NOTE]
    > Linux-appar och Windows-appar kan inte vara i samma App Service plan, men de kan vara i samma App Service-miljön.
    >

1. Välj **Nästa: övervakning**  om du vill aktivera App Insights med din app kan du göra det här när du skapar flödet. 

1.  Välj **Nästa: Taggar** Lägg till alla Taggar som du vill använda i appen  

1. Välj **Granska + skapa**, kontrol lera att informationen är korrekt och välj sedan **skapa**.

## <a name="how-scale-works"></a>Så här fungerar skala

Varje App Service App körs i en App Service plan. App Service miljöer innehåller App Service-planer och App Service planer. När du skalar en app skalar du också App Service plan och alla appar i samma plan.

När du skalar ett App Service plan läggs den infrastruktur som behövs till automatiskt. Det finns en tids fördröjning för skalning av åtgärder medan infrastrukturen läggs till. När du skalar en App Service plan väntar andra skalnings åtgärder som begärs av samma operativ system och storlek tills den första är klar. När den blockerade skalnings åtgärden har slutförts bearbetas alla begär anden i kö på samma tidpunkt. En skalnings åtgärd på en storlek och ett operativ system blockerar inte skalning av de andra kombinationerna av storlek och operativ system. Om du till exempel har skalat en Windows-I2v2 App Service plan kommer alla andra begär Anden att skala Windows-I2v2 i den ASE att placeras tills den är klar.   

I App Service flera innehavare är skalningen omedelbart eftersom det är enkelt att använda en pool med resurser för att stödja den. I en ASE finns det ingen sådan buffert, och resurserna allokeras utifrån behov.

## <a name="app-access"></a>Appåtkomst

I en ASE är det domänsuffix som används för att skapa appar *. &lt; asename &gt; . appserviceenvironment.net*. Om din ASE har namnet _My-ASE_ och du är värd för en app som heter _contoso_ i den ASE, så når du den på följande URL: er:

- contoso.my-ase.appserviceenvironment.net
- contoso.scm.my-ase.appserviceenvironment.net

Information om hur du skapar en ASE finns i [skapa en app service-miljön][MakeASE].

SCM-URL: en används för att få åtkomst till kudu-konsolen eller för att publicera din app med hjälp av webb distribution. Information om kudu-konsolen finns i [kudu-konsolen för Azure App Service][Kudu]. Kudu-konsolen ger dig ett webb gränssnitt för fel sökning, överföring av filer, redigering av filer och mycket mer.

### <a name="dns-configuration"></a>DNS-konfiguration 

ASE använder privata slut punkter för inkommande trafik. Den konfigureras inte automatiskt med Azure DNS privata zoner. Om du vill använda en egen DNS-server måste du lägga till följande poster:

1. skapa en zon för &lt; ASE name &gt; . appserviceenvironment.net
1. skapa en A-post i den zonen som pekar * till den inkommande IP-adressen som används av din privata ASE-slutpunkt
1. skapa en A-post i den zonen som pekar på den inkommande IP-adressen som används av din privata ASE-slutpunkt
1. skapa en zon i &lt; ASE name &gt; . appserviceenvironment.net med namnet SCM
1. skapa en A-post i SCM-zonen som pekar * till den IP-adress som används av din privata ASE-slutpunkt

Så här konfigurerar du DNS i Azure DNS privata zoner:

1. skapa en Azure DNS privat zon med namnet <ASE name> . appserviceenvironment.net
1. skapa en A-post i den zonen som pekar på ILB IP-adress
1. skapa en A-post i den zonen som pekar @ på ILB IP-adress
1. skapa en A-post i den zonen som pekar *. scm till ILB-IP-adressen

DNS-inställningarna för ditt ASE-standarddomänsuffix begränsar inte dina appar till att endast vara tillgängliga för dessa namn. Du kan ange ett anpassat domän namn utan att verifiera dina appar i en ASE. Om du sedan vill skapa en zon med namnet *contoso.net* kan du göra det och peka den mot den inkommande IP-adressen. Det anpassade domän namnet fungerar för app-begäranden, men inte för SCM-platsen. SCM-webbplatsen är bara tillgänglig på *&lt; APPNAME &gt; . scm. &lt; asename &gt; . appserviceenvironment.net*. 

## <a name="publishing"></a>Publicera

I en ASE, som med App Service med flera innehavare, kan du publicera på följande sätt:

- Webb distribution
- Kontinuerlig integrering (CI)
- Dra och släpp i kudu-konsolen
- En IDE, till exempel Visual Studio, Sol förmörkelse eller IntelliJ-idé

Med en ASE är publicerings slut punkterna bara tillgängliga via den inkommande adressen som används av den privata slut punkten. Om du inte har nätverks åtkomst till den privata slut punkts adressen kan du inte publicera några appar på den ASE.  Din IDE: er måste också ha nätverks åtkomst till ILB för att publicera direkt till den.

Utan ytterligare ändringar fungerar inte Internet-baserade CI-system som GitHub och Azure DevOps med en ILB-ASE eftersom publicerings slut punkten inte är tillgänglig för Internet. Du kan aktivera publicering till en ILB-ASE från Azure DevOps genom att installera en lokal versions agent i det virtuella nätverket som innehåller ILB ASE. 

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

Om du integrerar med Log Analytics kan du se loggarna genom att välja **loggar** från ASE-portalen och skapa en fråga mot **AppServiceEnvironmentPlatformLogs**. Loggar genereras bara när ASE har en händelse som ska utlösa den. Om din ASE inte har en sådan händelse finns det inga loggar. Om du snabbt vill se ett exempel på loggar i arbets ytan Log Analytics kan du utföra en skalnings åtgärd med något av App Services planerna i din ASE. Du kan sedan köra en fråga mot **AppServiceEnvironmentPlatformLogs** för att se dessa loggar. 

**Skapa en avisering**

Om du vill skapa en avisering mot loggarna följer du anvisningarna i [skapa, Visa och hantera logg aviseringar med hjälp av Azure Monitor](../../azure-monitor/alerts/alerts-log.md). I korthet:

* Öppna sidan aviseringar i ASE-portalen
* Välj **ny aviserings regel**
* Välj din resurs som Log Analytics arbets yta
* Ange villkoret med en anpassad loggs ökning för att använda en fråga som "AppServiceEnvironmentPlatformLogs | där ResultDescription innehåller "har startat skalning" eller vad du vill. Ange tröskelvärdet efter behov. 
* Lägg till eller skapa en åtgärds grupp som du vill. I åtgärds gruppen definierar du svaret på aviseringen, till exempel att skicka ett e-postmeddelande eller ett SMS-meddelande
* Ge aviseringen ett namn och spara den.

## <a name="internal-encryption"></a>Intern kryptering

App Service-miljön fungerar som ett svart Box-system där du inte kan se interna komponenter eller kommunikationen i systemet. Om du vill aktivera högre data flöde är kryptering inte aktiverat som standard mellan interna komponenter. Systemet är säkert eftersom trafiken är helt otillgänglig för övervakning eller åtkomst. Om du har ett krav på efterlevnad, men som kräver fullständig kryptering av data Sök vägen från slut punkt till slut punkt, kan du aktivera detta i **konfigurations** gränssnittet för ASE.

![Aktivera intern kryptering][5]

Detta krypterar intern nätverks trafik i din ASE mellan klient delar och arbetare, krypterar växlings filen och krypterar även arbets diskarna. När InternalEncryption-clusterSetting har Aktiver ATS kan det påverka systemets prestanda. När du gör ändringen för att aktivera InternalEncryption, kommer ASE att vara i ett instabilt tillstånd tills ändringen har spridits helt. Det kan ta några timmar att slutföra spridningen av ändringen, beroende på hur många instanser du har i din ASE. Vi rekommenderar starkt att du inte aktiverar detta på en ASE medan den används. Om du behöver aktivera detta på en aktivt Använd ASE rekommenderar vi starkt att du avinstallerar trafik till en säkerhets kopierings miljö tills åtgärden har slutförts.

## <a name="upgrade-preference"></a>Uppgraderings inställning

Om du har flera ASE kanske du vill att vissa ASE ska uppgraderas före andra. I ASE **HostingEnvironment Resource Manager** -objektet kan du ange ett värde för **upgradePreference**. Inställningen **upgradePreference** kan konfigureras med hjälp av en mall, ARMClient eller https://resources.azure.com . De tre möjliga värdena är:

- **Ingen**: Azure kommer att uppgradera din ASE utan någon särskild batch. Detta värde är standard.
- **Tidigt**: din ASE kommer att uppgraderas i den första hälften av App Service uppgraderingar.
- **Sent**: din ASE kommer att uppgraderas i den andra halvan av App Service uppgraderingar.

Om du vill konfigurera uppgraderings inställningen går du till **konfigurations** gränssnittet för ASE. 

Funktionen **upgradePreferences** är den mest begripliga när du har flera ASE eftersom din "tidiga" ASE kommer att uppgraderas före "sent" ASE. När du har flera ASE bör du ställa in din utvecklings-och test-ASE så att den blir "tidig" och produktions ASE ska vara "sent".

## <a name="delete-an-ase"></a>Ta bort en ASE

Så här tar du bort en ASE:

1. Välj **ta bort** överst i **App Service-miljöns** fönstret.

1. Ange namnet på din ASE för att bekräfta att du vill ta bort den. När du tar bort en ASE tar du även bort allt innehåll i den.

    ![Borttagning av ASE][3]

1. Välj **OK**.

<!--Image references-->
[1]: ./media/using/using-appcreate.png
[2]: ./media/using/using-appcreate-skus.png
[3]: ./media/using/using-delete.png
[4]: ./media/using/using-logsetup.png
[4]: ./media/using/using-logs.png
[5]: ./media/using/using-configuration.png

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
