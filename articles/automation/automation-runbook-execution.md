---
title: Runbook-körning i Azure Automation
description: Den här artikeln innehåller en översikt över bearbetningen av runbooks i Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 03/23/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 0807b11adfc46b9c32a8f7bd36a2f7d4db519975
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107830528"
---
# <a name="runbook-execution-in-azure-automation"></a>Runbook-körning i Azure Automation

Med processautomatisering Azure Automation kan du skapa och hantera PowerShell, PowerShell Workflow och grafiska runbooks. Mer information finns i [Azure Automation runbooks](automation-runbook-types.md). 

Automation kör dina runbooks baserat på den logik som definierats i dem. Om en runbook avbryts startas den om i början. Det här beteendet kräver att du skriver runbooks som stöder omstart om tillfälliga problem uppstår.

När du startar en runbook Azure Automation skapas ett jobb, vilket är en instans av en enskild körning av runbooken. Varje jobb får åtkomst till Azure-resurser genom att upprätta en anslutning till din Azure-prenumeration. Jobbet kan bara komma åt resurser i ditt datacenter om resurserna är tillgängliga från det offentliga molnet.

Azure Automation tilldelar en arbetsroll att köra varje jobb under runbook-körningen. Arbetare delas av många Azure-konton, men jobb från olika Automation-konton isoleras från varandra. Du kan inte styra vilka arbetsroller dina jobbbegäranden ska ha.

När du visar listan över runbooks i Azure Portal visas status för varje jobb som har startats för varje Runbook. Azure Automation lagrar jobbloggar i högst 30 dagar.

Följande diagram visar livscykeln för ett Runbook-jobb för [PowerShell-runbooks,](automation-runbook-types.md#powershell-runbooks) [PowerShell Workflow-runbooks](automation-runbook-types.md#powershell-workflow-runbooks)och [grafiska runbooks.](automation-runbook-types.md#graphical-runbooks)

![Jobbstatusar – PowerShell-arbetsflöde](./media/automation-runbook-execution/job-statuses.png)

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="runbook-execution-environment"></a>Runbook-körningsmiljö

Runbooks i Azure Automation kan köras i antingen en Azure-sandbox-miljö eller [en Hybrid Runbook Worker](automation-hybrid-runbook-worker.md). 

När runbooks är utformade för att autentisera och köras mot resurser i Azure körs de i en Sandbox-miljö i Azure, som är en delad miljö som flera jobb kan använda. Jobb som använder samma sandbox-miljö är bundna av resursbegränsningarna i sandbox-miljön. Sandbox-miljön i Azure stöder inte interaktiva åtgärder. Den förhindrar åtkomst till alla com-servrar som inte är processer och stöder inte [WMI-anrop](/windows/win32/wmisdk/wmi-architecture) till Win32-providern i din runbook.  De här scenarierna stöds endast genom att köra runbooken på en Windows-Hybrid Runbook Worker.


Du kan också använda en [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md) för att köra runbooks direkt på den dator som är värd för rollen och mot lokala resurser i miljön. Azure Automation lagrar och hanterar runbooks och levererar dem sedan till en eller flera tilldelade datorer.

>[!NOTE]
>Om du vill köra Hybrid Runbook Worker Linux-dator måste skripten signeras och arbetsrollen konfigureras därefter. Alternativt måste [signaturverifiering vara inaktiverat.](automation-linux-hrw-install.md#turn-off-signature-validation)

I följande tabell visas några runbook-körningsuppgifter med den rekommenderade körningsmiljön listad för var och en.

|Uppgift|Rekommendation|Kommentarer|
|---|---|---|
|Integrera med Azure-resurser|Sandbox-miljö i Azure|Autentisering är enklare i Azure. Om du använder en virtuell Hybrid Runbook Worker azure-dator kan du använda [runbook-autentisering med hanterade identiteter](automation-hrw-run-runbooks.md#runbook-auth-managed-identities).|
|Få optimala prestanda för att hantera Azure-resurser|Sandbox-miljö i Azure|Skriptet körs i samma miljö, som har kortare svarstid.|
|Minimera driftskostnader|Sandbox-miljö i Azure|Det finns inget beräkningskostnader och inget behov av en virtuell dator.|
|Köra långvariga skript|Hybrid Runbook Worker|Sandbox-miljöerna i Azure [har resursbegränsningar.](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits)|
|Interagera med lokala tjänster|Hybrid Runbook Worker|Få direkt åtkomst till värddatorn eller resurser i andra molnmiljöer eller i den lokala miljön. |
|Kräv programvara från tredje part och körbara filer|Hybrid Runbook Worker|Du hanterar operativsystemet och kan installera programvara.|
|Övervaka en fil eller mapp med en runbook|Hybrid Runbook Worker|Använd en [Bevakaraktivitet](automation-watchers-tutorial.md) på en Hybrid Runbook Worker.|
|Köra ett resursintensivt skript|Hybrid Runbook Worker| Sandbox-miljöerna i Azure har [resursbegränsningar.](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits)|
|Använda moduler med specifika krav| Hybrid Runbook Worker|Några exempel är:</br> WinSCP – beroende av winscp.exe </br> IIS-administration – beroende av aktivering eller hantering av IIS|
|Installera en modul med ett installationsprogram|Hybrid Runbook Worker|Moduler för sandbox-miljö måste ha stöd för kopiering.|
|Använd runbooks eller moduler som kräver en annan .NET Framework version än 4.7.2|Hybrid Runbook Worker|Sandbox-miljöerna i Azure .NET Framework 4.7.2 och uppgradering till en annan version stöds inte.|
|Köra skript som kräver utökade privilegier|Hybrid Runbook Worker|Sandbox-miljöerna tillåter inte utökade privilegier. Med en Hybrid Runbook Worker kan du inaktivera UAC och använda [Invoke-Command när du](/powershell/module/microsoft.powershell.core/invoke-command) kör kommandot som kräver utökade privilegier.|
|Köra skript som kräver åtkomst till Windows Management Instrumentation (WMI)|Hybrid Runbook Worker|Jobb som körs i sandbox-miljön i molnet kan inte komma åt WMI-providern. |

## <a name="temporary-storage-in-a-sandbox"></a>Tillfällig lagring i en sandbox-miljö

Om du behöver skapa temporära filer som en del av din Runbook-logik kan du använda Temp-mappen (det vill säga ) i Sandbox-miljön i Azure för `$env:TEMP` runbooks som körs i Azure. Den enda begränsningen är att du inte kan använda mer än 1 GB diskutrymme, vilket är kvoten för varje sandbox-miljö. När du arbetar med PowerShell-arbetsflöden kan det här scenariot orsaka ett problem eftersom PowerShell-arbetsflöden använder kontrollpunkter och skriptet kan göras om i en annan sandbox-miljö.

Med sandbox-miljön för hybrider kan `C:\temp` du använda baserat på tillgängligheten för lagring på en Hybrid Runbook Worker. Enligt rekommendationerna för virtuella Azure-datorer bör du dock inte använda den tillfälliga [disken](../virtual-machines/managed-disks-overview.md#temporary-disk) i Windows eller Linux för data som behöver bevaras.

## <a name="resources"></a>Resurser

Dina runbooks måste innehålla logik för att [hantera](/rest/api/resources/resources)resurser, till exempel virtuella datorer, nätverket och resurser i nätverket. Resurser är knutna till en Azure-prenumeration och runbooks kräver lämpliga autentiseringsuppgifter för att få åtkomst till alla resurser. Ett exempel på hur du hanterar resurser i en runbook finns i [Hantera resurser](manage-runbooks.md#handle-resources).

## <a name="security"></a>Säkerhet

Azure Automation använder Azure Security Center [(ASC) för](../security-center/security-center-introduction.md) att tillhandahålla säkerhet för dina resurser och identifiera komprometter i Linux-system. Säkerhet tillhandahålls i dina arbetsbelastningar, oavsett om resurserna finns i Azure eller inte. Se [Introduktion till autentisering i Azure Automation](automation-security-overview.md).

ASC lägger till begränsningar för användare som kan köra alla skript, antingen signerade eller osignerade, på en virtuell dator. Om du är en användare med rotåtkomst till en virtuell dator måste du uttryckligen konfigurera datorn med en digital signatur eller inaktivera den. Annars kan du bara köra ett skript för att tillämpa uppdateringar av operativsystemet när du har skapat ett Automation-konto och aktiverar lämplig funktion.

## <a name="subscriptions"></a>Prenumerationer

En [Azure-prenumeration](/office365/enterprise/subscriptions-licenses-accounts-and-tenants-for-microsoft-cloud-offerings) är ett avtal med Microsoft om att använda en eller flera molnbaserade tjänster som du debiteras för. För Azure Automation är varje prenumeration länkad till ett Azure Automation-konto och du [kan skapa flera](manage-runbooks.md#work-with-multiple-subscriptions) prenumerationer i kontot.

## <a name="credentials"></a>Autentiseringsuppgifter

En runbook kräver lämpliga [autentiseringsuppgifter](shared-resources/credentials.md) för att få åtkomst till alla resurser, oavsett om det gäller Azure eller system från tredje part. Dessa autentiseringsuppgifter lagras i Azure Automation, Key Vault osv.  

## <a name="azure-monitor"></a>Azure Monitor

Azure Automation använder sig av [Azure Monitor](../azure-monitor/overview.md) övervaka sina datoråtgärder. Åtgärderna kräver en Log Analytics-arbetsyta och en [Log Analytics-agent](../azure-monitor/agents/log-analytics-agent.md).

### <a name="log-analytics-agent-for-windows"></a>Log Analytics-agent för Windows

[Log Analytics-agenten för Windows](../azure-monitor/agents/agent-windows.md) fungerar med Azure Monitor för att hantera virtuella Windows-datorer och fysiska datorer. Datorerna kan köras antingen i Azure eller i en icke-Azure-miljö, till exempel ett lokalt datacenter.

>[!NOTE]
>Log Analytics-agenten för Windows kallades tidigare Microsoft Monitoring Agent (MMA).

### <a name="log-analytics-agent-for-linux"></a>Log Analytics-agent för Linux

[Log Analytics-agenten för Linux](../azure-monitor/agents/agent-linux.md) fungerar på samma sätt som agenten för Windows, men ansluter Linux-datorer till Azure Monitor. Agenten installeras med ett **nxautomation-användarkonto** som tillåter körning av kommandon som kräver rotbehörigheter, till exempel på en Hybrid Runbook Worker. **Nxautomation-kontot** är ett systemkonto som inte kräver något lösenord.

**Nxautomation-kontot** med motsvarande sudo-behörigheter måste finnas under [installationen av en Linux Hybrid Runbook Worker.](automation-linux-hrw-install.md) Om du försöker installera arbetsrollen och kontot inte finns eller inte har rätt behörigheter misslyckas installationen.

Du bör inte ändra behörigheterna för `sudoers.d` mappen eller dess ägarskap. Sudo-behörighet krävs för **nxautomation-kontot** och behörigheterna bör inte tas bort. Om du begränsar detta till vissa mappar eller kommandon kan det resultera i en stor ändring.

Loggarna som är tillgängliga för Log Analytics-agenten **och nxautomation-kontot** är:

* /var/opt/microsoft/omsagent/log/omsagent.log – Log Analytics-agentlogg
* /var/opt/microsoft/omsagent/run/automationworker/worker.log – Automation Worker-logg

>[!NOTE]
>**Nxautomation-användaren** som är aktiverad som en del Uppdateringshantering kör endast signerade runbooks.

## <a name="runbook-permissions"></a>Runbook-behörigheter

En runbook behöver behörighet för autentisering till Azure via autentiseringsuppgifter. Se [Azure Automation översikt över autentisering.](automation-security-overview.md)

## <a name="modules"></a>Moduler

Azure Automation stöder ett antal standardmoduler, inklusive vissa AzureRM-moduler (AzureRM.Automation) och en modul som innehåller flera interna cmdlets. Moduler som kan installeras, inklusive Az-modulerna (Az.Automation), som för närvarande används i stället för AzureRM-moduler, stöds också. Mer information om de moduler som är tillgängliga för dina runbooks och DSC-konfigurationer finns i [Hantera moduler i Azure Automation](shared-resources/modules.md).

## <a name="certificates"></a>Certifikat

Azure Automation använder [certifikat](shared-resources/certificates.md) för autentisering till Azure eller lägger till dem i Azure eller resurser från tredje part. Certifikaten lagras på ett säkert sätt för åtkomst av runbooks och DSC-konfigurationer.

Dina runbooks kan använda själv signerade certifikat som inte har signerats av en certifikatutfärdare (CA). Se [Skapa ett nytt certifikat.](shared-resources/certificates.md#create-a-new-certificate)

## <a name="jobs"></a>Jobb

Azure Automation har stöd för en miljö för att köra jobb från samma Automation-konto. En enda runbook kan ha många jobb som körs samtidigt. Ju fler jobb du kör samtidigt, desto oftare kan de skickas till samma sandbox-miljö. 

Jobb som körs i samma sandbox-process kan påverka varandra. Ett exempel är att köra [cmdleten Disconnect-AzAccount.](/powershell/module/az.accounts/disconnect-azaccount) Körningen av denna cmdlet kopplar från varje Runbook-jobb i den delade sandbox-processen. Ett exempel på hur du arbetar med det här scenariot finns i [Förhindra samtidiga jobb.](manage-runbooks.md#prevent-concurrent-jobs)

>[!NOTE]
>PowerShell-jobb som startats från en runbook som körs i en Azure-sandbox-miljö kanske inte körs i fullständigt [PowerShell-språkläge.](/powershell/module/microsoft.powershell.core/about/about_language_modes)

### <a name="job-statuses"></a>Jobbstatusar

I följande tabell beskrivs de statusar som är möjliga för ett jobb. Du kan visa en statussammanfattning för alla Runbook-jobb eller visa detaljerad information om ett specifikt runbook-jobb i Azure Portal. Du kan också konfigurera integrering med Log Analytics-arbetsytan för att vidarebefordra status för runbook-jobb och jobbströmmar. Mer information om hur du integrerar med Azure Monitor loggar finns i Vidarebefordra jobbstatus och [jobbströmmar](automation-manage-send-joblogs-log-analytics.md)från Automation till Azure Monitor loggar . Se även [Hämta jobbstatus för](manage-runbooks.md#obtain-job-statuses) ett exempel på att arbeta med statusar i en runbook.

| Status | Beskrivning |
|:--- |:--- |
| Aktivera |Jobbet aktiveras. |
| Slutförd |Jobbet har slutförts. |
| Misslyckad |En grafisk runbook eller PowerShell Workflow-runbook kunde inte kompileras. Det gick inte att starta en PowerShell-runbook eller så hade jobbet ett undantag. Se [Azure Automation runbook-typer.](automation-runbook-types.md)|
| Misslyckades, väntar på resurser |Jobbet misslyckades eftersom [](#fair-share) det nådde tidsgränsen tre gånger och startade från samma kontrollpunkt eller från början av runbooken varje gång. |
| I kö |Jobbet väntar på att resurser på ett Automation-arbete ska bli tillgängligt så att det kan startas. |
| Återupptar |Systemet återupptar jobbet efter att det pausas. |
| Körs |Jobbet körs |
| Körs, väntar på resurser |Jobbet har tagits bort eftersom det har nått gränsen för tidsbegränsade delningen. Den återupptas strax från den senaste kontrollpunkten. |
| Startar |Jobbet har tilldelats till en arbetsroll och systemet startar det. |
| Stoppad |Jobbet stoppades av användaren innan det slutfördes. |
| Stoppas |Systemet stoppar jobbet. |
| Inaktiverad |Gäller endast [för grafiska runbooks och PowerShell Workflow-runbooks.](automation-runbook-types.md) Jobbet pausades av användaren, av systemet, eller av ett kommando i runbook. Om en runbook inte har någon kontrollpunkt startar den från början. Om den har en kontrollpunkt kan den starta igen och återuppta från den senaste kontrollpunkten. Systemet pausar endast runbooken när ett undantag inträffar. Som standard är `ErrorActionPreference` variabeln inställd på Fortsätt, vilket anger att jobbet fortsätter att köras på ett fel. Om inställningsvariabeln är inställd på Stoppa pausas jobbet vid ett fel.  |
| Pausar |Gäller endast [för grafiska runbooks och PowerShell Workflow-runbooks.](automation-runbook-types.md) Systemet försöker pausa jobbet på begäran av användaren. Runbooken måste nå nästa kontrollpunkt innan den kan pausas. Om den redan har passerat sin sista kontrollpunkt slutförs den innan den kan pausas. |

## <a name="activity-logging"></a>Aktivitetsloggning

Körning av runbooks i Azure Automation skriver information i en aktivitetslogg för Automation-kontot. Mer information om hur du använder loggen finns [i Hämta information från aktivitetsloggen](manage-runbooks.md#retrieve-details-from-activity-log).

## <a name="exceptions"></a>Undantag

I det här avsnittet beskrivs några sätt att hantera undantag eller tillfälliga problem i dina runbooks. Ett exempel är ett WebSocket-undantag. Rätt undantagshantering förhindrar att tillfälliga nätverksfel orsakar att dina runbooks misslyckas.

### <a name="erroractionpreference"></a>ErrorActionPreference

Variabeln [ErrorActionPreference](/powershell/module/microsoft.powershell.core/about/about_preference_variables#erroractionpreference) avgör hur PowerShell svarar på ett icke-avslutande fel. Avslutande fel avslutas alltid och påverkas inte av `ErrorActionPreference` .

När runbooken använder stoppar ett normalt icke-avslutande fel, till exempel från `ErrorActionPreference` `PathNotFound` cmdleten [Get-ChildItem,](/powershell/module/microsoft.powershell.management/get-childitem) runbooken från att slutföras. I följande exempel visas användningen av `ErrorActionPreference` . Det slutliga [Write-Output-kommandot](/powershell/module/microsoft.powershell.utility/write-output) körs aldrig, eftersom skriptet stoppas.

```powershell-interactive
$ErrorActionPreference = 'Stop'
Get-ChildItem -path nofile.txt
Write-Output "This message will not show"
```

### <a name="try-catch-finally"></a>Prova Catch Finally

[Try Catch Finally](/powershell/module/microsoft.powershell.core/about/about_try_catch_finally) används i PowerShell-skript för att hantera avslutande fel. Skriptet kan använda den här mekanismen för att fånga upp specifika undantag eller allmänna undantag. `catch`-instruktionen ska användas för att spåra eller försöka hantera fel. I följande exempel försöker ladda ned en fil som inte finns. Den fångar upp `System.Net.WebException` undantaget och returnerar det sista värdet för alla andra undantag.

```powershell-interactive
try
{
   $wc = new-object System.Net.WebClient
   $wc.DownloadFile("http://www.contoso.com/MyDoc.doc")
}
catch [System.Net.WebException]
{
    "Unable to download MyDoc.doc from http://www.contoso.com."
}
catch
{
    "An error occurred that could not be resolved."
}
```

### <a name="throw"></a>Kasta

[Throw](/powershell/module/microsoft.powershell.core/about/about_throw) kan användas för att generera ett avslutande fel. Den här mekanismen kan vara användbar när du definierar din egen logik i en runbook. Om skriptet uppfyller ett villkor som ska stoppa det kan det använda `throw` -instruktionen för att stoppa. I följande exempel används den här instruktionen för att visa en obligatorisk funktionsparameter.

```powershell-interactive
function Get-ContosoFiles
{
  param ($path = $(throw "The Path parameter is required."))
  Get-ChildItem -Path $path\*.txt -recurse
}
```

## <a name="errors"></a>Fel

Dina runbooks måste hantera fel. Azure Automation två typer av PowerShell-fel, avslutande och icke-avslutande. 

Om du avslutar fel stoppas runbook-körningen när de inträffar. Runbooken stoppas med jobbstatusen Misslyckades.

Icke-avslutande fel gör att ett skript kan fortsätta även efter att de har inträffat. Ett exempel på ett icke-avslutande fel är ett som inträffar när en Runbook använder cmdleten med en sökväg `Get-ChildItem` som inte finns. PowerShell ser att sökvägen inte finns, kastar ett fel och fortsätter till nästa mapp. Felet i det här fallet anger inte statusen för runbook-jobbet till Misslyckades och jobbet kan till och med slutföras. Om du vill tvinga en runbook att stoppa vid ett icke-avslutande fel kan du `ErrorAction Stop` använda på cmdleten .

## <a name="calling-processes"></a>Anropa processer

Runbooks som körs i Azures sandbox-miljö stöder inte anropsprocesser, till exempel körbara filer **(EXE-filer)** eller delprocesser. Orsaken till detta är att en Sandbox-miljö i Azure är en delad process som körs i en container som kanske inte kan komma åt alla underliggande API:er. För scenarier som kräver programvara från tredje part eller anrop till underprocesser bör du köra en runbook på en [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md).

## <a name="device-and-application-characteristics"></a>Egenskaper för enhet och program

Runbook-jobb i Azure-sandbox-miljöerna har inte åtkomst till några enhets- eller programegenskaper. Det vanligaste API:et som används för att köra frågor mot prestandamått i Windows är WMI, där några av de vanligaste måtten är minnes- och CPU-användning. Det spelar dock ingen roll vilket API som används, eftersom jobb som körs i molnet inte kan komma åt Microsofts implementering av Web-Based Enterprise Management (WBEM). Den här plattformen bygger på Common Information Model (CIM), som tillhandahåller branschstandarder för att definiera egenskaper för enheter och program.

## <a name="webhooks"></a>Webhooks

Externa tjänster, till exempel Azure DevOps Services och GitHub, kan starta en runbook i Azure Automation. För att göra den här typen av start använder tjänsten en [webhook](automation-webhooks.md) via en enda HTTP-begäran. Användning av en webhook gör att runbooks kan startas utan implementering av en fullständig Azure Automation funktion.

## <a name="shared-resources"></a><a name="fair-share"></a>Delade resurser

För att dela resurser mellan alla runbooks i molnet använder Azure ett begrepp som kallas tidsfördelningsfördelning. Med hjälp av tids delning tar Azure tillfälligt bort eller stoppar jobb som har körts i mer än tre timmar. Jobb för [PowerShell-runbooks](automation-runbook-types.md#powershell-runbooks) [och Python-runbooks stoppas](automation-runbook-types.md#python-runbooks) och startas inte om, och jobbstatusen blir Stoppad.

För långvariga Azure Automation rekommenderar vi att du använder en Hybrid Runbook Worker. Hybrid Runbook Workers begränsas inte av tidsbegränsade resurser och har ingen begränsning för hur länge en runbook kan köras. De andra [jobbbegränsningarna](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits) gäller för både Azure-sandbox-miljö och Hybrid Runbook Workers. Även om Hybrid Runbook Workers inte begränsas av den tre timmar långa gränsen för tidsbegränsade resurser bör du utveckla runbooks som körs på de arbetare som stöder omstarter från oväntade lokala infrastrukturproblem.

Ett annat alternativ är att optimera en runbook med hjälp av underordnade runbooks. Din runbook kan till exempel loopa genom samma funktion på flera resurser, till exempel med en databasåtgärd på flera databaser. Du kan flytta den här funktionen till [en underordnad runbook och](automation-child-runbooks.md) få din runbook att anropa den med hjälp av [Start-AzAutomationRunbook](/powershell/module/az.automation/start-azautomationrunbook). Underordnade runbooks körs parallellt i separata processer.

Om du använder underordnade runbooks minskar den totala tiden som den överordnade runbooken kan slutföras. Din runbook kan använda cmdleten [Get-AzAutomationJob](/powershell/module/az.automation/get-azautomationjob) för att kontrollera jobbstatusen för en underordnad runbook om den fortfarande har fler åtgärder när det underordnade har slutförts.

## <a name="next-steps"></a>Nästa steg

* Om du vill komma igång med en PowerShell-runbook kan du [gå till Självstudie: Skapa en PowerShell-runbook.](learn/automation-tutorial-runbook-textual-powershell.md)
* Information om hur du arbetar med runbooks finns [i Hantera runbooks i Azure Automation](manage-runbooks.md).
* Mer information om PowerShell finns i [PowerShell Docs](/powershell/scripting/overview).
* En PowerShell-cmdlet-referens finns i [Az.Automation](/powershell/module/az.automation#automation).
