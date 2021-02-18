---
title: Runbook-körning i Azure Automation
description: Den här artikeln innehåller en översikt över bearbetningen av Runbooks i Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 10/06/2020
ms.topic: conceptual
ms.openlocfilehash: ca28d5829689dca46bbf3a94ce7c1591c20cf7b0
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100586048"
---
# <a name="runbook-execution-in-azure-automation"></a>Runbook-körning i Azure Automation

Med process automatisering i Azure Automation kan du skapa och hantera PowerShell, PowerShell-arbetsflöde och grafiska runbooks. Mer information finns i [Azure Automation runbooks](automation-runbook-types.md). 

Automation kör dina runbooks baserat på logiken som definierats i dem. Om en Runbook avbryts startas den om i början. Med det här beteendet måste du skriva Runbooks som stöder omstart om det uppstår tillfälliga problem.

Om du startar en Runbook i Azure Automation skapas ett jobb, som är en enskild körnings instans av runbooken. Varje jobb får åtkomst till Azure-resurser genom att ansluta till din Azure-prenumeration. Jobbet kan bara komma åt resurser i ditt data Center om resurserna är tillgängliga från det offentliga molnet.

Azure Automation tilldelar en arbets rutin att köra varje jobb under Runbook-körningen. Medan arbets tagarna delas av många Azure-konton, är jobb från olika Automation-konton isolerade från varandra. Du kan inte styra vilka Worker Services ditt jobb begär.

När du visar listan över Runbooks i Azure Portal visas statusen för varje jobb som har startats för varje Runbook. Azure Automation lagrar jobb loggar i högst 30 dagar.

Följande diagram visar livs cykeln för ett Runbook-jobb för [PowerShell-Runbooks](automation-runbook-types.md#powershell-runbooks), [PowerShell Workflow-Runbooks](automation-runbook-types.md#powershell-workflow-runbooks)och [grafiska runbooks](automation-runbook-types.md#graphical-runbooks).

![Jobb status – PowerShell-arbetsflöde](./media/automation-runbook-execution/job-statuses.png)

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="runbook-execution-environment"></a>Körnings miljö för Runbook

Runbooks i Azure Automation kan köras antingen i ett Azure-sandbox eller en [hybrid Runbook Worker](automation-hybrid-runbook-worker.md). 

När Runbooks är utformade för att autentisera och köra mot resurser i Azure körs de i en Azure-sandbox, som är en delad miljö som flera jobb kan använda. Jobb som använder samma Sandbox är begränsade till resurs begränsningarna i sand boxen. Azure sandbox-miljön stöder inte interaktiva åtgärder. Den förhindrar åtkomst till alla out-of-process COM-servrar. Det kräver också att lokala MOF-filer används för Runbooks som gör Win32-anrop.

Du kan också använda en [hybrid Runbook Worker](automation-hybrid-runbook-worker.md) för att köra Runbooks direkt på den dator som är värd för rollen och mot lokala resurser i miljön. Azure Automation lagrar och hanterar Runbooks och levererar dem sedan till en eller flera tilldelade datorer.

>[!NOTE]
>Om du vill köra på en Linux-Hybrid Runbook Worker måste skripten vara signerade och arbetaren har kon figurer ATS enligt detta. Du måste också stänga [av verifieringen av signaturen](automation-linux-hrw-install.md#turn-off-signature-validation).

I följande tabell visas några aktiviteter för Runbook-körningar med den rekommenderade körnings miljön som visas för var och en.

|Uppgift|Rekommendation|Kommentarer|
|---|---|---|
|Integrera med Azure-resurser|Sand box för Azure|Azure är värd för Azure och det är enklare att autentisera. Om du använder en Hybrid Runbook Worker på en virtuell Azure-dator kan du [använda Runbook-autentisering med hanterade identiteter](automation-hrw-run-runbooks.md#runbook-auth-managed-identities).|
|Få optimala prestanda för att hantera Azure-resurser|Sand box för Azure|Skriptet körs i samma miljö, vilket har mindre latens.|
|Minimera drifts kostnader|Sand box för Azure|Det finns ingen beräknings kapacitet och inget behov av en virtuell dator.|
|Kör tids krävande skript|Hybrid Runbook Worker|Azure-sandboxs har [resurs gränser](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits).|
|Interagera med lokala tjänster|Hybrid Runbook Worker|Få direkt åtkomst till värddatorn eller resurser i andra moln miljöer eller den lokala miljön. |
|Kräv program vara från tredje part och körbara filer|Hybrid Runbook Worker|Du hanterar operativ systemet och kan installera program vara.|
|Övervaka en fil eller mapp med en Runbook|Hybrid Runbook Worker|Använd en [bevakare-aktivitet](automation-watchers-tutorial.md) på en hybrid Runbook Worker.|
|Köra ett resurs intensivt skript|Hybrid Runbook Worker| Azure-sandboxs har [resurs gränser](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits).|
|Använda moduler med särskilda krav| Hybrid Runbook Worker|Några exempel är:</br> WinSCP – beroende av winscp.exe </br> IIS-administration – beroende av att aktivera eller hantera IIS|
|Installera en modul med ett installations program|Hybrid Runbook Worker|Moduler för sandbox måste ha stöd för kopiering.|
|Använd Runbooks eller moduler som kräver .NET Framework version som skiljer sig från 4.7.2|Hybrid Runbook Worker|Azure-sandbox stöder .NET Framework 4.7.2 och uppgradering till en annan version stöds inte.|
|Kör skript som kräver höjning|Hybrid Runbook Worker|Sand boxar tillåter inte utökade privilegier. Med en Hybrid Runbook Worker kan du inaktivera UAC och använda [Invoke-Command](/powershell/module/microsoft.powershell.core/invoke-command) när du kör kommandot som kräver utökade privilegier.|
|Kör skript som kräver åtkomst till Windows Management Instrumentation (WMI)|Hybrid Runbook Worker|Jobb som körs i sand lådor i molnet kan inte komma åt WMI-providern. |

## <a name="temporary-storage-in-a-sandbox"></a>Tillfällig lagring i ett begränsat läge

Om du behöver skapa temporära filer som en del av din Runbook-logik kan du använda Temp-mappen (det vill säga `$env:TEMP` ) i Azure sandbox för Runbooks som körs i Azure. Den enda begränsningen är att du inte kan använda mer än 1 GB disk utrymme, vilket är kvoten för varje begränsat läge. När du arbetar med PowerShell-arbetsflöden kan det här scenariot orsaka ett problem eftersom PowerShell-arbetsflöden använder kontroll punkter och skriptet kan provas i ett annat läge.

Med hybrid sand boxen kan du använda `C:\temp` baserat på tillgängligheten för lagring på en hybrid Runbook Worker. Enligt Azures rekommendationer för virtuella datorer bör du dock inte använda den [temporära disken](../virtual-machines/managed-disks-overview.md#temporary-disk) på Windows eller Linux för data som måste vara bestående.

## <a name="resources"></a>Resurser

Dina Runbooks måste innehålla logik för att hantera [resurser](/rest/api/resources/resources), till exempel virtuella datorer, nätverk och resurser i nätverket. Resurserna är kopplade till en Azure-prenumeration och Runbooks kräver lämpliga autentiseringsuppgifter för att få åtkomst till alla resurser. Ett exempel på hur du hanterar resurser i en Runbook finns i [Hantera resurser](manage-runbooks.md#handle-resources).

## <a name="security"></a>Säkerhet

Azure Automation använder [Azure Security Center (ASC)](../security-center/security-center-introduction.md) för att tillhandahålla säkerhet för dina resurser och identifiera kompromisser i Linux-system. Säkerhet tillhandahålls för dina arbets belastningar, oavsett om resurserna är i Azure eller inte. Se [Introduktion till autentisering i Azure Automation](automation-security-overview.md).

ASC begränsar begränsningar för användare som kan köra skript, antingen signerade eller osignerade, på en virtuell dator. Om du är en användare med rot åtkomst till en virtuell dator måste du uttryckligen konfigurera datorn med en digital signatur eller inaktivera den. Annars kan du bara köra ett skript för att tillämpa operativ system uppdateringar när du har skapat ett Automation-konto och aktiverat lämplig funktion.

## <a name="subscriptions"></a>Prenumerationer

En Azure- [prenumeration](/office365/enterprise/subscriptions-licenses-accounts-and-tenants-for-microsoft-cloud-offerings) är ett avtal med Microsoft för att använda en eller flera molnbaserade tjänster som du debiteras för. För Azure Automation är varje prenumeration länkad till ett Azure Automation konto och du kan [skapa flera prenumerationer](manage-runbooks.md#work-with-multiple-subscriptions) i kontot.

## <a name="credentials"></a>Autentiseringsuppgifter

En Runbook kräver rätt [autentiseringsuppgifter](shared-resources/credentials.md) för att få åtkomst till alla resurser, oavsett om det gäller Azure eller tredje part system. Autentiseringsuppgifterna lagras i Azure Automation, Key Vault osv.  

## <a name="azure-monitor"></a>Azure Monitor

Azure Automation använder [Azure Monitor](../azure-monitor/overview.md) för att övervaka dator åtgärder. Åtgärderna kräver en Log Analytics-arbetsyta och en [Log Analytics-agent](../azure-monitor/agents/log-analytics-agent.md).

### <a name="log-analytics-agent-for-windows"></a>Log Analytics agent för Windows

[Log Analytics agenten för Windows](../azure-monitor/agents/agent-windows.md) fungerar med Azure Monitor för att hantera virtuella Windows-datorer och fysiska datorer. Datorerna kan köras antingen i Azure eller i en miljö som inte är en Azure-miljö, till exempel ett lokalt Data Center.

>[!NOTE]
>Log Analytics agent för Windows kallades tidigare för Microsoft Monitoring Agent (MMA).

### <a name="log-analytics-agent-for-linux"></a>Log Analytics agent för Linux

[Log Analytics-agenten för Linux](../azure-monitor/agents/agent-linux.md) fungerar på samma sätt som agenten för Windows, men ansluter Linux-datorer till Azure Monitor. Agenten installeras med ett **nxautomation** -användarkonto som tillåter körning av kommandon som kräver rot behörigheter, till exempel på en hybrid Runbook Worker. **Nxautomation** -kontot är ett system konto som inte kräver ett lösen ord.

**Nxautomation** -kontot med motsvarande sudo-behörigheter måste finnas under [installationen av en Linux hybrid Runbook Worker](automation-linux-hrw-install.md). Om du försöker installera Worker och kontot inte finns eller inte har rätt behörighet, Miss lyckas installationen.

Du bör inte ändra behörigheterna för `sudoers.d` mappen eller dess ägarskap. Sudo-behörighet krävs för **nxautomation** -kontot och behörigheterna bör inte tas bort. Att begränsa detta till vissa mappar eller kommandon kan resultera i en ändring.

De loggar som är tillgängliga för Log Analytics-agenten och **nxautomation** -kontot är:

* /var/opt/Microsoft/omsagent/log/omsagent.log-Log Analytics agent logg
* /var/opt/Microsoft/omsagent/Run/automationworker/Worker.log – automatisering Worker-logg

>[!NOTE]
>**Nxautomation** -användaren som är aktive rad som en del av uppdateringshantering kör bara signerade Runbooks.

## <a name="runbook-permissions"></a>Runbook-behörigheter

En Runbook behöver behörighet för autentisering till Azure via autentiseringsuppgifter. Se [Översikt över Azure Automation autentisering](automation-security-overview.md).

## <a name="modules"></a>Moduler

Azure Automation stöder ett antal standardmoduler, inklusive vissa AzureRM-moduler (AzureRM. Automation) och en modul som innehåller flera interna cmdletar. De kan också användas för att installera moduler, inklusive AZ-moduler (AZ. Automation) som för närvarande används i stället för AzureRM-moduler. Mer information om de moduler som är tillgängliga för dina runbooks och DSC-konfigurationer finns i [Hantera moduler i Azure Automation](shared-resources/modules.md).

## <a name="certificates"></a>Certifikat

Azure Automation använder [certifikat](shared-resources/certificates.md) för autentisering till Azure eller lägger till dem i Azure eller från resurser från tredje part. Certifikaten lagras på ett säkert sätt för åtkomst av Runbooks och DSC-konfigurationer.

Dina runbooks kan använda självsignerade certifikat som inte är signerade av en certifikat utfärdare (CA). Se [skapa ett nytt certifikat](shared-resources/certificates.md#create-a-new-certificate).

## <a name="jobs"></a>Jobb

Azure Automation stöder en miljö för att köra jobb från samma Automation-konto. En enda Runbook kan ha många jobb på samma gång. Fler jobb som du kör samtidigt, desto oftare kan de skickas till samma sandbox. 

Jobb som körs i samma sand Box process kan påverka varandra. Ett exempel är att köra cmdleten [disconnect-AzAccount](/powershell/module/az.accounts/disconnect-azaccount) . Körning av denna cmdlet kopplar från varje Runbook-jobb i den delade sand Box processen. Ett exempel på hur du arbetar med det här scenariot finns i [förhindra samtidiga jobb](manage-runbooks.md#prevent-concurrent-jobs).

>[!NOTE]
>PowerShell-jobb som startas från en Runbook som körs i en Azure-sandbox kanske inte körs i det fullständiga [PowerShell-språkläget](/powershell/module/microsoft.powershell.core/about/about_language_modes).

### <a name="job-statuses"></a>Jobb status

I följande tabell beskrivs de status värden som är möjliga för ett jobb. Du kan visa en status sammanfattning för alla Runbook-jobb eller öka detalj nivån för ett särskilt Runbook-jobb i Azure Portal. Du kan också konfigurera integrering med din Log Analytics arbets yta för att vidarebefordra Runbook-jobbets status och jobb strömmar. Mer information om hur du integrerar med Azure Monitor loggar finns i [vidarebefordra jobb status och jobb strömmar från Automation till Azure Monitor loggar](automation-manage-send-joblogs-log-analytics.md). Se även [jobb status](manage-runbooks.md#obtain-job-statuses) för att få ett exempel på hur du arbetar med status i en Runbook.

| Status | Beskrivning |
|:--- |:--- |
| Ring |Jobbet aktive ras. |
| Slutförd |Jobbet har slutförts. |
| Misslyckad |Det gick inte att kompilera en grafisk eller PowerShell-arbetsflöde Runbook. Det gick inte att starta PowerShell-runbooken eller så innehöll jobbet ett undantag. Se [Azure Automation Runbook-typer](automation-runbook-types.md).|
| Misslyckades, väntar på resurser |Jobbet misslyckades eftersom det nådde den [verkliga delnings](#fair-share) gränsen tre gånger och startade från samma kontroll punkt eller från början av runbooken varje gång. |
| I kö |Jobbet väntar på att resurser i en automatiserings arbets uppgift ska bli tillgängliga så att de kan startas. |
| Återupptar |Systemet återupptar jobbet när det har pausats. |
| Körs |Jobbet körs |
| Körs, väntar på resurser |Jobbet har inaktiverats på grund av att gränsen nåddes för den verkliga delningen. Den kommer snart att återupptas från den senaste kontroll punkten. |
| Startar |Jobbet har tilldelats en anställd och systemet startas. |
| Stoppad |Jobbet stoppades av användaren innan det slutfördes. |
| Stoppas |Jobbet stoppas av systemet. |
| Inaktiverad |Gäller endast för [grafiska och PowerShell Workflow-Runbooks](automation-runbook-types.md) . Jobbet pausades av användaren, av systemet, eller av ett kommando i runbook. Om en Runbook inte har en kontroll punkt börjar den från början. Om den har en kontroll punkt kan den startas igen och återupptas från den senaste kontroll punkten. Systemet pausar bara runbooken när ett undantag inträffar. Som standard `ErrorActionPreference` är variabeln inställd på Fortsätt, vilket anger att jobbet fortsätter att köras vid ett fel. Om variabeln ställs in på Avbryt pausas jobbet vid ett fel.  |
| Pausar |Gäller endast för [grafiska och PowerShell Workflow-Runbooks](automation-runbook-types.md) . Systemet försöker pausa jobbet på användarens begäran. Runbooken måste nå nästa kontrollpunkt innan den kan pausas. Om den redan har passerat den senaste kontroll punkten slutförs den innan den kan pausas. |

## <a name="activity-logging"></a>Aktivitetsloggning

Körning av Runbooks i Azure Automation skriver information i en aktivitets logg för Automation-kontot. Information om hur du använder loggen finns i [Hämta information från aktivitets loggen](manage-runbooks.md#retrieve-details-from-activity-log).

## <a name="exceptions"></a>Undantag

I det här avsnittet beskrivs några sätt att hantera undantag eller tillfälliga problem i dina runbooks. Ett exempel är ett WebSocket-undantag. Med rätt undantags hantering förhindras tillfälliga nätverks fel från att dina runbooks Miss lyckas.

### <a name="erroractionpreference"></a>ErrorActionPreference

[Erroractionpreference satt](/powershell/module/microsoft.powershell.core/about/about_preference_variables#erroractionpreference) -variabeln avgör hur PowerShell svarar på ett icke-avslutande fel. Avslutande fel avslutas alltid och påverkas inte av `ErrorActionPreference` .

När runbooken används `ErrorActionPreference` stoppar ett normalt icke-avslutande fel som `PathNotFound` från [Get-ChildItem](/powershell/module/microsoft.powershell.management/get-childitem) -cmdleten Runbook från att slutföras. I följande exempel visas användningen av `ErrorActionPreference` . Det slutliga kommandot [Write-output](/powershell/module/microsoft.powershell.utility/write-output) körs aldrig, eftersom skriptet slutar.

```powershell-interactive
$ErrorActionPreference = 'Stop'
Get-ChildItem -path nofile.txt
Write-Output "This message will not show"
```

### <a name="try-catch-finally"></a>Prova att fånga finally

[Prova Catch finally](/powershell/module/microsoft.powershell.core/about/about_try_catch_finally) används i PowerShell-skript för att hantera avslutande fel. Skriptet kan använda den här metoden för att fånga upp vissa undantag eller allmänna undantag. `catch`Instruktionen ska användas för att spåra eller försöka hantera fel. I följande exempel försöker hämta en fil som inte finns. Det fångar `System.Net.WebException` undantag och returnerar det sista värdet för alla andra undantag.

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

### <a name="throw"></a>Genereras

[Throw](/powershell/module/microsoft.powershell.core/about/about_throw) kan användas för att generera ett avslutande fel. Den här mekanismen kan vara användbar när du definierar din egen logik i en Runbook. Om skriptet uppfyller ett villkor som ska stoppas kan det använda `throw` instruktionen för att stoppa. I följande exempel används den här instruktionen för att visa en obligatorisk funktions parameter.

```powershell-interactive
function Get-ContosoFiles
{
  param ($path = $(throw "The Path parameter is required."))
  Get-ChildItem -Path $path\*.txt -recurse
}
```

## <a name="errors"></a>Fel

Dina Runbooks måste hantera fel. Azure Automation stöder två typer av PowerShell-fel, avslutande och icke-avslutande. 

Om du avslutar fel stoppas Runbook-körningen när de inträffar. Runbooken slutar med jobb statusen misslyckades.

Icke-avslutande fel tillåter att ett skript fortsätter även efter att det har inträffat. Ett exempel på ett icke-avslutande fel är en som inträffar när en Runbook använder `Get-ChildItem` cmdleten med en sökväg som inte finns. PowerShell ser att sökvägen inte finns, genererar ett fel och fortsätter till nästa mapp. Felet i det här fallet anger inte status för Runbook-jobbet till misslyckades och jobbet kan till och med slutföras. Om du vill tvinga en Runbook att stoppa vid ett icke-avslutande fel, kan du använda `ErrorAction Stop` på-cmdleten.

## <a name="calling-processes"></a>Anropande processer

Runbooks som körs i Azure-sandbox stöder inte anrops processer, till exempel körbara filer (**. exe** -filer) eller under processer. Orsaken till detta är att en Azure-Sandbox är en delad process som körs i en behållare som kanske inte kan komma åt alla underliggande API: er. För scenarier som kräver tredjepartsprogram eller anrop till subprocesser bör du köra en Runbook på en [hybrid Runbook Worker](automation-hybrid-runbook-worker.md).

## <a name="device-and-application-characteristics"></a>Egenskaper för enhet och program

Runbook-jobb i Azure-sandbox har inte åtkomst till några enhets-eller program egenskaper. Det vanligaste API: et som används för att fråga prestanda värden i Windows är WMI, med några vanliga mått som minnes-och processor användning. Men det spelar ingen roll vilket API som används, eftersom jobb som körs i molnet inte kan komma åt Microsoft-implementeringen av Web-Based Enterprise Management (WBEM). Den här plattformen bygger på Common Information Model (CIM) och tillhandahåller bransch standarder för att definiera egenskaper för enheter och program.

## <a name="webhooks"></a>Webhooks

Externa tjänster, till exempel Azure DevOps Services och GitHub, kan starta en Runbook i Azure Automation. För att utföra den här typen av start använder tjänsten en [webhook](automation-webhooks.md) via en enda http-begäran. Med en webhook kan Runbooks startas utan att en fullständig Azure Automation-funktion implementeras.

## <a name="shared-resources"></a><a name="fair-share"></a>Delade resurser

För att dela resurser mellan alla Runbooks i molnet, använder Azure ett koncept som kallas rättvist delning. Med en rättvis resurs laddar Azure tillfälligt bort eller stoppar jobb som har körts i mer än tre timmar. Jobb för [PowerShell-Runbooks](automation-runbook-types.md#powershell-runbooks) och [python-Runbooks](automation-runbook-types.md#python-runbooks) stoppas och startas inte om, och jobb statusen stoppas.

För tids krävande Azure Automation uppgifter rekommenderar vi att du använder en Hybrid Runbook Worker. Hybrid Runbook Worker begränsas inte av en rättvis resurs och har ingen begränsning för hur länge en Runbook kan köras. De andra jobb [gränserna](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits) gäller för både Azure-Sandbox och hybrid Runbook Worker. Även om hybrid Runbook Worker inte begränsas av den tre gränsen för gränsen för en timme bör du utveckla Runbooks som ska köras på de arbetare som har stöd för omstarter från oväntade problem med lokal infrastruktur.

Ett annat alternativ är att optimera en Runbook genom att använda underordnade Runbooks. Din Runbook kan till exempel loopa genom samma funktion på flera resurser, till exempel med en databas åtgärd på flera databaser. Du kan flytta den här funktionen till en [underordnad Runbook](automation-child-runbooks.md) och låta din Runbook anropa den med hjälp av [Start-AzAutomationRunbook](/powershell/module/az.automation/start-azautomationrunbook). Underordnade Runbooks körs parallellt i separata processer.

Om underordnade Runbooks används minskar den totala tiden som den överordnade runbooken slutförs. Din Runbook kan använda cmdleten [Get-AzAutomationJob](/powershell/module/az.automation/get-azautomationjob) för att kontrol lera jobb status för en underordnad Runbook om den fortfarande har fler åtgärder när den underordnade har slutförts.

## <a name="next-steps"></a>Nästa steg

* Information om hur du kommer igång med en PowerShell-Runbook finns i [Självstudier: skapa en PowerShell-Runbook](learn/automation-tutorial-runbook-textual-powershell.md).
* Information om hur du arbetar med Runbooks finns [i hantera Runbooks i Azure Automation](manage-runbooks.md).
* Mer information om PowerShell finns i [PowerShell-dokument](/powershell/scripting/overview).
* En PowerShell-cmdlet-referens finns i [AZ. Automation](/powershell/module/az.automation#automation).
