---
title: Ansluta Operations Manager till Azure Monitor | Microsoft Docs
description: För att underhålla din befintliga investering i System Center Operations Manager och använda utökade funktioner med Log Analytics, kan du integrera Operations Manager med ditt arbetsområde.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/24/2020
ms.openlocfilehash: 9c20994c27680aaccb68db2ff78deb6b6bf9f8cf
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101718139"
---
# <a name="connect-operations-manager-to-azure-monitor"></a>Anslut Operations Manager till Azure Monitor

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

Om du vill behålla din befintliga investering i [System Center Operations Manager](/system-center/scom/key-concepts) och använda utökade funktioner med Azure Monitor kan du integrera Operations Manager med din Log Analytics-arbetsyta. På så sätt kan du utnyttja affärs möjligheterna för loggar i Azure Monitor samtidigt som du fortsätter att använda Operations Manager för att:

* Övervaka hälsotillståndet för dina IT-tjänster med Operations Manager
* Underhålla integrationen med dina ITSM-lösningar med stöd för hantering av incidenter och problem
* Hantera livscykeln för agenter som distribuerats lokalt och till virtuella IaaS-datorer i det offentliga molnet som du övervakar med Operations Manager

Genom att integrera med System Center Operations Manager lägger du till värde i din strategi för service åtgärder med hjälp av hastigheten och effektiviteten i Azure Monitor när du samlar in, lagrar och analyserar loggdata från Operations Manager. Azure Monitor logg frågor hjälper dig att korrelera och arbeta med att identifiera fel och Visa upprepningar som stöd för din befintliga problem hanterings process. Den flexibilitet som används i frågemotor för att undersöka prestanda, händelse-och aviserings data, med omfattande instrument paneler och rapporterings funktioner för att visa dessa data på ett meningsfullt sätt, vilket visar styrkan Azure Monitor ger Operations Manager.

Agenter som rapporterar till Operations Manager hanterings gruppen samlar in data från dina servrar baserat på [Log Analytics data källor](../agents/agent-data-sources.md) och lösningar som du har aktiverat i din arbets yta. Beroende på vilka lösningar som har Aktiver ATS skickas deras data antingen direkt från en Operations Manager hanterings server till tjänsten, eller på grund av data mängden som samlas in på det agentbaserade systemet, skickas direkt från agenten till en Log Analytics arbets yta. Hanteringsservern vidarebefordrar data direkt till tjänsten, de skrivs aldrig till den operativa databasen eller informationslagerdatabasen. När en hanterings server förlorar anslutningen till Azure Monitor cachelagrar den data lokalt tills kommunikationen återupprättas. Om-hanterings servern är offline på grund av planerat underhåll eller oplanerat avbrott, återupptar en annan hanterings server i hanterings gruppen anslutningen till Azure Monitor.  

Följande diagram visar anslutningen mellan hanterings servrar och agenter i en System Center Operations Manager hanterings grupp och Azure Monitor, inklusive riktning och portar.

![oms-operations-manager-integration-diagram](./media/om-agents/oms-operations-manager-connection.png)

Om dina IT-säkerhetsprinciper inte tillåter datorer i nätverket att ansluta till Internet, kan hanterings servrar konfigureras för att ansluta till Log Analytics Gateway för att ta emot konfigurations information och skicka insamlade data beroende på vilka lösningar som är aktiverade. Mer information och anvisningar om hur du konfigurerar din Operations Manager hanterings grupp för att kommunicera via en Log Analytics Gateway till Azure Monitor finns i [ansluta datorer till Azure monitor med hjälp av Log Analytics Gateway](./gateway.md).  

## <a name="prerequisites"></a>Förutsättningar

Läs igenom följande krav innan du börjar.

* Azure Monitor stöder endast System Center Operations Manager 2016 eller senare, Operations Manager 2012 SP1 UR6 eller senare och Operations Manager 2012 R2 UR2 eller senare. Stöd för proxy har lagts till i Operations Manager 2012 SP1 UR7 och Operations Manager 2012 R2 UR3.
* Att integrera System Center Operations Manager 2016 med det amerikanska myndighets molnet kräver ett uppdaterat Advisor-hanterings paket som ingår i Samlad uppdatering 2 eller senare. System Center Operations Manager 2012 R2 kräver ett uppdaterat Advisor Management Pack som ingår i Samlad uppdatering 3 eller senare.
* Alla Operations Manager-agenter måste uppfylla minimikraven. Kontrol lera att agenterna har den lägsta uppdateringen, annars kan Windows agent-kommunikation Miss lyckas och generera fel i händelse loggen för Operations Manager.
* En Log Analytics-arbetsyta. Mer information hittar du i [Översikt över Log Analytics-arbetsyta](../logs/design-logs-deployment.md).
* Du autentiserar till Azure med ett konto som är medlem i [rollen Log Analytics Contributor](../logs/manage-access.md#manage-access-using-azure-permissions).

* Regioner som stöds – endast följande Azure-regioner stöds av System Center Operations Manager för att ansluta till en Log Analytics arbets yta:
    - USA, västra centrala
    - Sydöstra Australien
    - Europa, västra
    - East US
    - Sydostasien
    - Japan, östra
    - Storbritannien, södra
    - Indien, centrala
    - Kanada, centrala
    - USA, västra 2

>[!NOTE]
>De senaste ändringarna i Azure API: er hindrar kunder från att kunna konfigurera integration mellan hanterings gruppen och Azure Monitor för första gången. För kunder som redan har integrerat sin hanterings grupp med tjänsten påverkas du inte om du inte behöver konfigurera om den befintliga anslutningen.  
>Ett nytt hanterings paket har släppts för följande versioner av Operations Manager:
> - För System Center Operations Manager 2019 ingår detta hanterings paket med käll mediet och installeras under installationen av en ny hanterings grupp eller under en uppgradering.
>- Operations Manager 1801-hanterings paketet gäller också för Operations Manager 1807.
>- För System Center Operations Manager 1801 hämtar du hanterings paketet härifrån [.](https://www.microsoft.com/download/details.aspx?id=57173)
>- För System Center 2016 – Operations Manager laddar du ned hanterings paketet [härifrån.](https://www.microsoft.com/download/details.aspx?id=57172)  
>- Hämta hanterings [paketet härifrån för](https://www.microsoft.com/download/details.aspx?id=57171)System Center Operations Manager 2012 R2.  


### <a name="network"></a>Nätverk

Informationen nedan visar den konfigurations information för proxy och brand vägg som krävs för att Operations Manager agent, hanterings servrar och drift konsol för att kommunicera med Azure Monitor. Trafik från varje komponent är utgående från nätverket till Azure Monitor.

|Resurs | Portnummer| Kringgå HTTPS-kontroll|  
|---------|------|-----------------------|  
|**Gent**|||  
|\*.ods.opinsights.azure.com| 443 |Ja|  
|\*.oms.opinsights.azure.com| 443|Ja|  
|\*.blob.core.windows.net| 443|Ja|  
|\*.azure-automation.net| 443|Ja|  
|**Hanteringsserver**|||  
|\*.service.opinsights.azure.com| 443||  
|\*.blob.core.windows.net| 443| Ja|  
|\*.ods.opinsights.azure.com| 443| Ja|  
|*.azure-automation.net | 443| Ja|  
|**Operations Manager-konsolen för att Azure Monitor**|||  
|service.systemcenteradvisor.com| 443||  
|\*.service.opinsights.azure.com| 443||  
|\*.live.com| 80 och 443||  
|\*.microsoft.com| 80 och 443||  
|\*.microsoftonline.com| 80 och 443||  
|\*.mms.microsoft.com| 80 och 443||  
|login.windows.net| 80 och 443||  
|portal.loganalytics.io| 80 och 443||
|api.loganalytics.io| 80 och 443||
|docs.loganalytics.io| 80 och 443||  

### <a name="tls-12-protocol"></a>TLS 1,2-protokoll

För att säkerställa säkerheten för data som överförs till Azure Monitor rekommenderar vi starkt att du konfigurerar agenten och hanterings gruppen så att de använder minst Transport Layer Security (TLS) 1,2. Äldre versioner av TLS/Secure Sockets Layer (SSL) har befunnits vara sårbara och även om de fortfarande arbetar för att tillåta bakåtkompatibilitet, rekommenderas de **inte**. Mer information finns i [skicka data på ett säkert sätt med TLS 1,2](../logs/data-security.md#sending-data-securely-using-tls-12).

## <a name="connecting-operations-manager-to-azure-monitor"></a>Ansluta Operations Manager till Azure Monitor

Utför följande steg för att konfigurera hanteringsgruppen för Operations Manager för att ansluta till en av dina Log Analytics-arbetsytor.

Under den inledande registreringen av din Operations Manager hanterings grupp med en Log Analytics arbets yta är alternativet för att ange proxykonfigurationen för hanterings gruppen inte tillgängligt i drift konsolen.  Hanteringsgruppen måste registrerats med tjänsten innan det här alternativet är tillgängligt.  För att undvika detta måste du uppdatera systemproxy-konfigurationen med Netsh på systemet som kör drift konsolen från för att konfigurera integrering och alla hanterings servrar i hanterings gruppen.  

1. Öppna en upphöjd kommandotolk.
   a. Gå till **Start** och skriv **cmd**.
   b. Högerklicka på **kommando tolken** och välj Kör som administratör * *.
1. Ange följande kommando och tryck på **Enter**:

    `netsh winhttp set proxy <proxy>:<port>`

När du har slutfört följande steg för att integrera med Azure Monitor kan du ta bort konfigurationen genom `netsh winhttp reset proxy` att köra och sedan använda alternativet **Konfigurera proxyserver** i drift konsolen för att ange proxyservern eller Log Analytics Gateway-server.

1. Välj arbetsytan **Administration** i Operations Manager-konsolen.
1. Expandera noden Operations Management Suite och klicka på **Anslutning**.
1. Klicka på länken för att **registrera till Operations Management Suite**.
1. På sidan **Guiden Operations Management Suite Onboarding: Autentisering** anger du e-postadress eller telefonnummer och lösenordet för administratörskontot som är kopplat till din OMS-prenumeration och klickar på **Logga in**.

   >[!NOTE]
   >Operations Management Suite-namnet har dragits tillbaka.

1. När du har autentiserats går du till sidan **Operations Management Suite onboarding: Välj arbets yta** och du uppmanas att välja din Azure-klient, prenumeration och Log Analytics-arbetsyta. Om du har mer än en arbetsyta väljer du arbetsytan som du vill registrera med Operations Manager-hanteringsgruppen från listrutan och klickar på **Nästa**.

   > [!NOTE]
   > Operations Manager stöder bara en Log Analytics-arbetsyta i taget. Anslutningen och datorerna som har registrerats för Azure Monitor med den föregående arbets ytan tas bort från Azure Monitor.
   >
   >
1. På sidan **Guiden Operations Management Suite Onboarding: Sammanfattning** bekräftar du dina inställningar och om de är korrekta klickar du på **Skapa**.
1. På sidan **Guiden Operations Management Suite Onboarding: Slutför** klickar du på **Stäng**.

### <a name="add-agent-managed-computers"></a>Lägg till datorer som hanteras med agent

När du har konfigurerat integrationen med din Log Analytics-arbetsyta upprättar den bara en anslutning till tjänsten, inga data samlas in från agenter som rapporterar till hanterings gruppen. Detta sker inte förrän du har konfigurerat vilka vissa datorer som hanteras av agenten samla in loggdata för Azure Monitor. Du kan antingen välja datorobjekt individuellt eller välja en grupp som innehåller Windows-datorobjekt. Du kan inte välja en grupp som innehåller instanser av en annan klass, till exempel logiska diskar eller SQL-databaser.

1. Öppna Operations Manager-konsolen och välj arbetsytan **Administration**.
1. Expandera noden Operations Management Suite och klicka på **Anslutning**.
1. Klicka på länken **Lägg till en dator/grupp** under rubriken Åtgärder på höger sida av fönstret.
1. I dialogrutan **Datorsökning** kan du söka efter datorer eller grupper som övervakas av Operations Manager. Välj datorer eller grupper, inklusive den Operations Manager hanterings server som ska publiceras i Azure Monitor, klicka på **Lägg till** och sedan på **OK**.

Du kan visa datorer och grupper som konfigurerats för att samla in data från noden för hanterade datorer under Operations Management Suite i arbetsytan **Administration** i Operations-konsolen. Härifrån kan du lägga till eller ta bort datorer och grupper efter behov.

### <a name="configure-proxy-settings-in-the-operations-console"></a>Konfigurera proxyinställningar i Operations-konsolen

Utför följande steg om en intern proxyserver är mellan hanterings gruppen och Azure Monitor. De här inställningarna hanteras centralt från hanterings gruppen och distribueras till agenter som hanteras av agenter som ingår i omfattningen för insamling av loggdata för Azure Monitor.  Detta är fördelaktigt när vissa lösningar förbigår hanteringsservern och skickar data direkt till tjänsten.

1. Öppna Operations Manager-konsolen och välj arbetsytan **Administration**.
1. Expandera Operations Management Suite och klicka sedan på **Anslutningar**.
1. I vyn OMS-anslutning klickar du på **Konfigurera proxyserver**.
1. På sidan med **guiden Operations Management Suite: Proxyserver** väljer du alternativet för att **använda en proxyserver för att få åtkomst till Operational Management Suite** och anger sedan webbadressen med portnumret, till exempel http://corpproxy:80 och klickar på **Slutför**.

Om proxyservern kräver autentisering utför du följande steg för att konfigurera autentiseringsuppgifter och inställningar som måste spridas till hanterade datorer som rapporterar till Azure Monitor i hanterings gruppen.

1. Öppna Operations Manager-konsolen och välj arbetsytan **Administration**.
1. Under **Kör som-konfiguration** väljer du **Profiler**.
1. Öppna profilen **Kör som-profilproxy för System Center Advisor**.
1. I guiden Kör som-profil klickar du på Lägg till för att använda ett Kör som-konto. Du kan skapa ett [Kör som-konto](/previous-versions/system-center/system-center-2012-R2/hh321655(v=sc.12)) eller använda ett befintligt konto. Det här kontot måste ha tillräcklig behörighet för att kunna passera genom proxyservern.
1. Ange vilket konto som ska hanteras genom att välja **En vald klass, grupp eller objekt**, klicka på **Välj...** och sedan klicka på **Grupp...** för att öppna **Gruppsökning**.
1. Sök efter och välj **Övervakning av servergrupp i Microsoft System Center Advisor**. Klicka på **OK** när du har valt grupp för att stänga rutan **Gruppsökning**.
1. Klicka på **OK** för att stänga rutan **Lägg till ett Kör som-konto** .
1. Klicka på **Spara** för att slutföra guiden och spara dina ändringar.

När anslutningen har skapats och du konfigurerar vilka agenter som ska samla in och rapportera logg data till Azure Monitor, tillämpas följande konfiguration i hanterings gruppen, inte nödvändigt vis i ordning:

* Kör som-kontot **Microsoft.SystemCenter.Advisor.RunAsAccount.Certificate** skapas. Det är associerat med Kör som-profilen **Microsoft System Center Advisor Run As Profile Blob** (Microsoft System Center Advisor Kör som-profilblob) och har två klasser som mål – **insamlingsserver** och **Operations Manager-hanteringsgrupp**.
* Två anslutningsprogram skapas.  Det första heter **Microsoft.SystemCenter. Advisor. DataConnector** och konfigureras automatiskt med en prenumeration som vidarebefordrar alla aviseringar som genereras från instanser av alla klasser i hanterings gruppen till Azure Monitor. Den andra kopplingen är **Advisor-koppling**, som ansvarar för att kommunicera med Azure Monitor och dela data.
* Agenter och grupper som du har valt för att samla in data i hanteringsgruppen läggs till i **Övervakning av servergrupp i Microsoft System Center Advisor**.

## <a name="management-pack-updates"></a>Uppdateringar av hanteringspaket

När konfigurationen är klar upprättar Operations Manager hanterings gruppen en anslutning med Azure Monitor. Hanteringsservern synkroniserar med webbtjänsten och tar emot uppdaterad konfigurationsinformation i form av hanteringspaket för de lösningar du har aktiverat som integreras med Operations Manager. Operations Manager söker efter uppdateringar av dessa hanterings paket och hämtar och importerar dem automatiskt när de är tillgängliga. Det är särskilt två regler som styr detta:

* **Microsoft.SystemCenter. Advisor. MPUpdate** – uppdaterar bas Azure Monitor hanterings paketen. Kör som standard var 12:e timme.
* **Microsoft.SystemCenter.Advisor.Core.GetIntelligencePacksRule** – uppdaterar hanteringspaketen för lösningen som aktiverats i din arbetsyta. Körs som standard var 5:e minut.

Du kan åsidosätta de här två reglerna för att antingen förhindra automatisk nedladdning genom att inaktivera dem eller ändra frekvensen för hur ofta hanterings servern ska synkroniseras med Azure Monitor för att avgöra om ett nytt hanterings paket är tillgängligt och ska laddas ned. Följ [de här stegen för att åsidosätta en regel eller övervakare](/previous-versions/system-center/system-center-2012-R2/hh212869(v=sc.12)) för att ändra parametern **Frekvens** (med ett värde i sekunder) för att justera synkroniseringsschemat eller ändra parametern **Aktiverad** för att avaktivera reglerna. Rikta åsidosättningarna mot alla objekt i klassen Operations Manager Management Group.

Om du vill fortsätta att följa den befintliga processen för ändrings kontroll för att kontrol lera hanterings paket versioner i produktions hanterings gruppen kan du inaktivera reglerna och aktivera dem vid vissa tidpunkter när uppdateringar är tillåtna. Om du har en utvecklings- eller kvalitetskontrollshanteringsgrupp i din miljö och den är ansluten till Internet, kan du konfigurera hanteringsgruppen med Log Analytics-arbetsytan för att stödja det här scenariot. På så sätt kan du granska och utvärdera de iterativa versionerna av de Azure Monitor hanterings paketen innan du släpper dem i produktions hanterings gruppen.

## <a name="switch-an-operations-manager-group-to-a-new-log-analytics-workspace"></a>Växla en Operations Manager-grupp till en ny Log Analytics-arbetsyta

1. Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com).
1. I Azure Portal klickar du på knappen **Fler tjänster** längst upp till vänster. I listan över resurser skriver du **Log Analytics**. När du börjar skriva filtreras listan baserat på det du skriver. Välj **Log Analytics** och skapa en arbetsyta.  
1. Öppna Operations Manager-konsolen med ett konto med rollen Operations Manager-administratör och välj arbetsytan **Administration**.
1. Expandera Log Analytics och välj **anslutningar**.
1. Välj länken för att **konfigurera om Operation Management Suite** i mitten av fönstret.
1. Följ **guiden Log Analytics onboarding** och ange e-postadressen eller telefonnumret och lösen ordet för det administratörs konto som är associerat med den nya Log Analytics-arbetsytan.

   > [!NOTE]
   > På sidan **Guiden Operations Management Suite Onboarding: Välj arbetsyta** visas den befintliga arbetsytan som används.
   >
   >

## <a name="validate-operations-manager-integration-with-azure-monitor"></a>Verifiera Operations Manager-integrering med Azure Monitor

Använd följande fråga för att hämta de anslutna instanserna av Operations Manager:

```azurepowershell
union *
| where isnotempty(MG)
| where not(ObjectName == 'Advisor Metrics' or ObjectName == 'ManagedSpace')
| summarize LastData = max(TimeGenerated) by lowerCasedComputerName=tolower(Computer), MG, ManagementGroupName
| sort by lowerCasedComputerName asc
```

## <a name="remove-integration-with-azure-monitor"></a>Ta bort integrering med Azure Monitor

Flera steg måste utföras för att ta bort anslutningen och konfigurationen korrekt i hanteringsgruppen när du inte längre behöver integreringen mellan din Operations Manager-hanteringsgrupp och Log Analytics-arbetsytan. Med följande procedur kan du uppdatera Log Analytics-arbetsytan genom att ta bort referensen för hanterings gruppen, ta bort Azure Monitor-anslutningarna och sedan ta bort hanterings paket som stöder integrering med tjänsten.  

Hanterings paket för de lösningar som du har aktiverat och som integreras med Operations Manager och de hanterings paket som krävs för att stödja integration med Azure Monitor kan inte enkelt tas bort från hanterings gruppen. Detta beror på att vissa av de Azure Monitor hanterings paketen är beroende av andra relaterade hanterings paket. För att ta bort hanteringspaket med beroenden i andra hanteringspaket laddar du ned skriptet [ för att ta bort hanteringspaket med ett beroende](https://gallery.technet.microsoft.com/scriptcenter/Script-to-remove-a-84f6873e) från TechNet Script Center.  

1. Öppna Operations Manager kommandotolken med ett konto som är medlem i rollen Operations Manager-administratörer.

    > [!WARNING]
    > Kontrollera att du inte har några anpassade hanteringspaket som innehåller ordet Advisor eller IntelligencePack i namnet innan du fortsätter. I annat fall kan du ta bort dem från hanteringsgruppen.
    >

1. Från kommandotolken skriver du: `Get-SCOMManagementPack -name "*Advisor*" | Remove-SCOMManagementPack -ErrorAction SilentlyContinue`
1. Skriv sedan `Get-SCOMManagementPack -name "*IntelligencePack*" | Remove-SCOMManagementPack -ErrorAction SilentlyContinue`
1. För att ta bort återstående hanteringspaket med beroenden till andra System Center Advisor-hanteringspaket använder du skriptet *RecursiveRemove.ps1* som du laddade ned innan från TechNet Script Center.  

    > [!NOTE]
    > Steget för att ta bort Advisor-hanterings paketen med PowerShell tar inte automatiskt bort Microsoft System Center Advisor eller Microsoft System Center Advisor Internal Management Packs.  Försök inte att ta bort dem.  
    >  

1. Öppna Operations-konsolen i Operations Manager med ett konto som har rollen Operations Manager-administratör.
1. Under **Administration** väljer du noden **Hanteringspaket** och i rutan för att **söka** skriver du **Advisor** och kontrollerar att följande hanteringspaket fortfarande importeras i hanteringsgruppen:

   * Microsoft System Center Advisor
   * Microsoft System Center Advisor Internal

1. Klicka på panelen **Inställningar** i Azure Portal.
1. Välj **anslutna källor**.
1. I tabellen under avsnittet System Center Operations Manager bör du se namnet på den hanterings grupp som du vill ta bort från arbets ytan. Under kolumnen **Senaste data** klickar du på **Ta bort**.  

    > [!NOTE]
    > Länken **Ta bort** blir inte tillgänglig förrän efter att den anslutna hanteringsgruppen känner av 14 dagar utan aktivitet.  
    >

1. Ett fönster visas där du uppmanas att bekräfta att du vill ta bort volymen.  Klicka på **Ja** för att gå vidare.

För att radera de två anslutningsprogrammen – Microsoft.SystemCenter.Advisor.DataConnector och Advisor Connector – sparar du PowerShell-skriptet nedan på datorn och kör det med följande exempel:

```
    .\OM2012_DeleteConnectors.ps1 "Advisor Connector" <ManagementServerName>
    .\OM2012_DeleteConnectors.ps1 "Microsoft.SystemCenter.Advisor.DataConnector" <ManagementServerName>
```

> [!NOTE]
> Den dator som du kör skriptet från (om det inte är en hanteringsserver) bör ha Operations Manager-kommandogränssnittet installerat, beroende på version av hanteringsgruppen.
>
>

```powershell
    param(
    [String] $connectorName,
    [String] $msName="localhost"
    )
    $mg = new-object Microsoft.EnterpriseManagement.ManagementGroup $msName
    $admin = $mg.GetConnectorFrameworkAdministration()
    ##########################################################################################
    # Configures a connector with the specified name.
    ##########################################################################################
    function New-Connector([String] $name)
    {
         $connectorForTest = $null;
         foreach($connector in $admin.GetMonitoringConnectors())
    {
    if($connectorName.Name -eq ${name})
    {
         $connectorForTest = Get-SCOMConnector -id $connector.id
    }
    }
    if ($connectorForTest -eq $null)
    {
         $testConnector = New-Object Microsoft.EnterpriseManagement.ConnectorFramework.ConnectorInfo
         $testConnector.Name = $name
         $testConnector.Description = "${name} Description"
         $testConnector.DiscoveryDataIsManaged = $false
         $connectorForTest = $admin.Setup($testConnector)
         $connectorForTest.Initialize();
    }
    return $connectorForTest
    }
    ##########################################################################################
    # Removes a connector with the specified name.
    ##########################################################################################
    function Remove-Connector([String] $name)
    {
        $testConnector = $null
        foreach($connector in $admin.GetMonitoringConnectors())
       {
        if($connector.Name -eq ${name})
       {
         $testConnector = Get-SCOMConnector -id $connector.id
       }
      }
     if ($testConnector -ne $null)
     {
        if($testConnector.Initialized)
     {
     foreach($alert in $testConnector.GetMonitoringAlerts())
     {
       $alert.ConnectorId = $null;
       $alert.Update("Delete Connector");
     }
     $testConnector.Uninitialize()
     }
     $connectorIdForTest = $admin.Cleanup($testConnector)
     }
    }
    ##########################################################################################
    # Delete a connector's Subscription
    ##########################################################################################
    function Delete-Subscription([String] $name)
    {
      foreach($testconnector in $admin.GetMonitoringConnectors())
      {
      if($testconnector.Name -eq $name)
      {
        $connector = Get-SCOMConnector -id $testconnector.id
      }
    }
    $subs = $admin.GetConnectorSubscriptions()
    foreach($sub in $subs)
    {
      if($sub.MonitoringConnectorId -eq $connector.id)
      {
        $admin.DeleteConnectorSubscription($admin.GetConnectorSubscription($sub.Id))
      }
     }
    }
    #New-Connector $connectorName
    write-host "Delete-Subscription"
    Delete-Subscription $connectorName
    write-host "Remove-Connector"
    Remove-Connector $connectorName
```

I framtiden om du planerar att återansluta din hanterings grupp till en Log Analytics arbets yta måste du importera `Microsoft.SystemCenter.Advisor.Resources.\<Language>\.mpb` hanterings paket filen igen. Beroende på vilken version av System Center Operations Manager som har distribuerats i din miljö kan du hitta filen här:

* På källmediet under mappen `\ManagementPacks` för System Center 2016 – Operations Manager och högre.
* Från den senaste uppdateringen som tillämpats på din hanteringsgrupp. För Operations Manager 2012 är källmappen `%ProgramFiles%\Microsoft System Center 2012\Operations Manager\Server\Management Packs for Update Rollups` och för 2012 R2 finns den i `System Center 2012 R2\Operations Manager\Server\Management Packs for Update Rollups` .

## <a name="next-steps"></a>Nästa steg

Information om hur du lägger till funktioner och samlar in data finns i [Lägg till Azure Monitor lösningar från Lösningsgalleriet](../insights/solutions.md).