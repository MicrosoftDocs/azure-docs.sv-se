---
title: Azure DevTest Labs vanliga frågor och svar | Microsoft Docs
description: Den här artikeln innehåller svar på några vanliga frågor och svar om Azure DevTest Labs.
ms.topic: article
ms.date: 07/17/2020
ms.openlocfilehash: 9fcdc160754822d5c6f22b7349d0e72f0cf22633
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "97590281"
---
# <a name="azure-devtest-labs-faq"></a>Azure DevTest Labs vanliga frågor och svar
Få svar på några av de vanligaste frågorna om Azure DevTest Labs.

## <a name="blog-post"></a>Blogg inlägg
Vår team blogg för DevTest Labs har dragits tillbaka från och med 20 mars 2019. 

### <a name="where-can-i-track-feature-updates-from-now-on"></a>Var kan jag spåra funktions uppdateringar från nu?
Nu kommer vi att publicera funktions uppdateringar och informativa blogg inlägg på Azure-bloggen och Azure-uppdateringar. Dessa blogg inlägg kommer också att länka till vår dokumentation där det behövs.

Prenumerera på [DevTest Labs Azure-bloggen](https://azure.microsoft.com/blog/tag/azure-devtest-labs/) och [DevTest Labs Azure-uppdateringar](https://azure.microsoft.com/updates/?product=devtest-lab) för att hålla dig informerad om nya funktioner i DevTest Labs.

### <a name="what-happens-to-the-existing-blog-posts"></a>Vad händer med befintliga blogg inlägg?
Vi arbetar för närvarande med att migrera befintliga blogg inlägg (exklusive avbrott-uppdateringar) till vår [DevTest Labs-dokumentation](devtest-lab-overview.md). När MSDN-bloggen är föråldrad kommer den att omdirigeras till dokumentations översikten för DevTest Labs. När den har omdirigerats kan du söka efter den artikel du letar efter i rubriken "Filtrera efter". Vi har inte migrerat alla inlägg ännu, men bör utföras vid slutet av den här månaden. 


### <a name="where-do-i-see-outage-updates"></a>Var kan jag se avbrott-uppdateringar?
Vi kommer att publicera avbrott-uppdateringar med vår Twitter-referens från och med nu. Följ oss på Twitter för att få de senaste uppdateringarna om avbrott och kända buggar.

### <a name="twitter"></a>Twitter
Vår Twitter-referens: [@azlabservices](https://twitter.com/azlabservices)

## <a name="general"></a>Allmänt
### <a name="what-if-my-question-isnt-answered-here"></a>Vad händer om min fråga inte besvaras här?
Om din fråga inte visas här kan vi berätta för oss, så vi kan hjälpa dig att hitta ett svar.

- Publicera en fråga i slutet av dessa vanliga frågor och svar.
- För att få en större mål grupp kan du publicera en fråga på [sidan Microsoft Q&en fråga för Azure DevTest Labs](/answers/topics/azure-devtestlabs.html). Engagera dig med Azure DevTest Labs-teamet och andra medlemmar i communityn.
- Skicka dina förfrågningar och idéer till [Azure DevTest Labs User Voice](https://feedback.azure.com/forums/320373-azure-devtest-labs)för funktions förfrågningar.

### <a name="what-is-a-microsoft-account"></a>Vad är en Microsoft-konto?
Ett Microsoft-konto är ett konto som du använder för nästan allt du gör med Microsoft-enheter och-tjänster. Det är en e-postadress och ett lösen ord som du använder för att logga in på Skype, Outlook.com, OneDrive, Windows Phone, Azure och Xbox Live. Ett enda konto innebär att dina filer, foton, kontakter och inställningar kan följa dig på vilken enhet som helst.

> [!NOTE]
> Ett Microsoft-konto som används för att anropa ett Windows Live ID.

### <a name="why-should-i-use-azure-devtest-labs"></a>Varför ska jag använda Azure DevTest Labs?
Azure DevTest Labs kan spara tid och pengar till din grupp. Utvecklare kan skapa sina egna miljöer genom att använda flera olika baser. De kan också använda artefakter för att snabbt distribuera och konfigurera program. Genom att använda anpassade avbildningar och formler kan du spara virtuella datorer som mallar och enkelt återge dem i hela teamet. DevTest Labs erbjuder också flera konfigurerbara principer som labb administratörer kan använda för att minska avfall och hantera en grupps miljöer. Dessa principer omfattar automatisk avstängning, kostnads tröskel, maximalt antal virtuella datorer per användare och maximal VM-storlek. En mer ingående förklaring av DevTest Labs finns i [översikten](devtest-lab-overview.md) eller den [inledande videon](https://channel9.msdn.com/Blogs/Azure/what-is-azure-devtest-labs).

### <a name="what-does-worry-free-self-service-mean"></a>Vad innebär "bekymra dig fritt självbetjäning"?
Kostnads fri självbetjäning innebär att utvecklare och testare skapar sina egna miljöer efter behov. Administratörer har trygghet att veta att DevTest Labs kan hjälpa dig att ange lämplig åtkomst, minimera avfalls-och kontroll kostnader. Administratörer kan ange vilka VM-storlekar som tillåts, det maximala antalet virtuella datorer och när virtuella datorer startas och stängs av. DevTest Labs gör det också enkelt att övervaka kostnader och ange aviseringar för att hjälpa dig att hålla koll på hur labb resurser används.

### <a name="how-can-i-use-devtest-labs"></a>Hur kan jag använda DevTest Labs?
DevTest Labs är praktiskt när du behöver utvecklings-eller test miljöer och vill återskapa dem snabbt, eller hantera dem genom att använda kostnads besparingar.
Här följer några scenarier som våra kunder använder DevTest Labs för:

- Hantera utvecklings-och test miljöer på ett och samma ställe. Använd principer för att minska kostnaderna och skapa anpassade avbildningar för att dela versioner i teamet.
- Utveckla ett program genom att använda anpassade avbildningar för att spara disk tillstånd under hela utvecklings stegen.
- Spåra kostnad i förhållande till förloppet.
- Skapa Mass test miljöer för kvalitets säkrings testning.
- Använd artefakter och formler för att enkelt konfigurera och återskapa ett program i olika miljöer.
- Distribuera virtuella datorer för Hackathon kickar (utveckling av utveckling eller testning) och avetablera dem sedan enkelt när händelsen slutar.

### <a name="how-am-i-billed-for-devtest-labs"></a>Hur faktureras jag för DevTest Labs?
DevTest Labs är en kostnads fri tjänst. Det är kostnads fritt att skapa labb och konfigurera principer, mallar och artefakter i DevTest Labs. Du betalar bara för de Azure-resurser som används i dina labb, till exempel virtuella datorer, lagrings konton och virtuella nätverk. Mer information om kostnaden för labb resurser finns [Azure DevTest Labs prissättning](https://azure.microsoft.com/pricing/details/devtest-lab/).

## <a name="security"></a>Säkerhet

### <a name="what-are-the-different-security-levels-in-devtest-labs"></a>Vilka är de olika säkerhets nivåerna i DevTest Labs?
Säkerhets åtkomst bestäms av Azure-rollbaserad åtkomst kontroll (Azure RBAC). För att lära dig hur Access fungerar, hjälper det till att lära sig skillnaderna mellan en behörighet, en roll och ett omfång som definieras av Azure RBAC.

- **Behörighet**: en behörighet är en definierad åtkomst till en viss åtgärd. Till exempel kan en behörighet ha Läs behörighet till alla virtuella datorer.
- **Roll**: en roll är en uppsättning behörigheter som kan grupperas och tilldelas till en användare. Till exempel har en användare med rollen prenumerations ägare åtkomst till alla resurser i en prenumeration.
- **Omfattning**: ett omfång är en nivå i hierarkin för en Azure-resurs. Ett omfång kan till exempel vara en resurs grupp, ett enda labb eller hela prenumerationen.

Inom ramen för DevTest Labs finns det två typer av roller som definierar användar behörigheter:

- **Labb ägare**: en labb ägare har åtkomst till alla resurser i labbet. En labb ägare kan ändra principer, läsa och skriva till alla virtuella datorer, ändra det virtuella nätverket och så vidare.
- **Labb användare**: en labb användare kan visa alla labb resurser, till exempel virtuella datorer, principer och virtuella nätverk. En labb användare kan dock inte ändra principer eller virtuella datorer som har skapats av andra användare.

Du kan också skapa anpassade roller i DevTest Labs. Information om hur du skapar anpassade roller i DevTest Labs finns i [Granting User Permissions to Specific Lab policies](devtest-lab-grant-user-permissions-to-specific-lab-policies.md).

Eftersom omfattningar är hierarkiska, beviljas användaren automatiskt de behörigheter som finns på alla lågnivå omfattningar i omfånget när en användare har behörighet för ett visst omfång. Om en användare till exempel har tilldelats rollen som prenumerations ägare, har användaren åtkomst till alla resurser i en prenumeration. Dessa resurser omfattar virtuella datorer, virtuella nätverk och labb. En prenumerations ägare ärver automatiskt rollen som labb ägare. Motsatt är dock inte sant. En labb ägare har åtkomst till ett labb, vilket är ett lägre omfång än prenumerations nivån. En labb ägare kan därför inte se virtuella datorer, virtuella nätverk eller andra resurser som ligger utanför labbet.

### <a name="how-do-i-define-azure-role-based-access-control-for-my-devtest-labs-environments-to-ensure-that-it-can-govern-while-developerstest-can-do-their-work"></a>Hur gör jag för att definierar du Azure rollbaserad åtkomst kontroll för mina DevTest Labs-miljöer för att säkerställa att den kan styra medan utvecklare/test kan utföra sitt arbete?
Det finns ett brett mönster, men detaljerna beror på din organisation.

Den centrala IT-avdelningen bör endast äga vad som är nödvändigt och göra det möjligt för projekt-och program team att ha den kontroll nivå som krävs. Det innebär vanligt vis att central IT äger prenumerationen och hanterar kärn IT-funktioner, till exempel nätverkskonfigurationer. Uppsättningen **ägare** för en prenumeration ska vara liten. Dessa ägare kan utse ytterligare ägare när det behövs eller tillämpa principer på prenumerations nivå, till exempel "ingen offentlig IP".

Det kan finnas en delmängd av användare som behöver åtkomst över en prenumeration, till exempel 1 eller support på nivå 2. I det här fallet rekommenderar vi att du ger användarna **deltagare** åtkomst så att de kan hantera resurserna, men inte ge användar åtkomst eller ändra principer.

DevTest Labs-resursen bör ägas av ägare som är nära projekt-/program gruppen. Det beror på att de förstår kraven för datorer och nödvändig program vara. I de flesta organisationer är ägaren av den här DevTest Labs-resursen ofta projekt-/utvecklings ledare. Den här ägaren kan hantera användare och principer i labb miljön och kan hantera alla virtuella datorer i DevTest Labs miljö.

Projekt-/program grupp medlemmar ska läggas till i **användar rollen DevTest Labs** . Dessa användare kan skapa virtuella datorer (i linje med principerna för labb och prenumerations nivå). De kan också hantera sina egna virtuella datorer. De kan inte hantera virtuella datorer som tillhör andra användare.

Mer information finns i [Azure Enterprise-Autogenerera – dokumentation om preskripts styrning av prenumerationer](/azure/architecture/cloud-adoption/appendix/azure-scaffold).


### <a name="how-do-i-create-a-role-to-allow-users-to-do-a-specific-task"></a>Hur gör jag för att skapa en roll för att tillåta användare att utföra en speciell uppgift?
En omfattande artikel om hur du skapar anpassade roller och tilldelar behörigheter till en roll finns i [bevilja användar behörigheter till vissa labb principer](devtest-lab-grant-user-permissions-to-specific-lab-policies.md). Här är ett exempel på ett skript som skapar rollen **DevTest Labs Advanced User**, som har behörighet att starta och stoppa alla virtuella datorer i labbet:


```powershell
$policyRoleDef = Get-AzRoleDefinition "DevTest Labs User"
$policyRoleDef.Actions.Remove('Microsoft.DevTestLab/Environments/*')
$policyRoleDef.Id = $null
$policyRoleDef.Name = "DevTest Labs Advanced User"
$policyRoleDef.IsCustom = $true
$policyRoleDef.AssignableScopes.Clear()
$policyRoleDef.AssignableScopes.Add("subscriptions/<subscription Id>")
$policyRoleDef.Actions.Add("Microsoft.DevTestLab/labs/virtualMachines/Start/action")
$policyRoleDef.Actions.Add("Microsoft.DevTestLab/labs/virtualMachines/Stop/action")
$policyRoleDef = New-AzRoleDefinition -Role $policyRoleDef  
```

### <a name="how-can-an-organization-ensure-corporate-security-policies-are-in-place"></a>Hur kan en organisation se till att företagets säkerhets principer är på plats?
En organisation kan uppnå det genom att utföra följande åtgärder:

- Utveckla och publicera en omfattande säkerhets princip. Principen förbrukar reglerna för acceptabel användning som är associerade med användning av program vara, moln till gångar. Den definierar också vad som tydligt strider mot principen.
- Utveckla en anpassad avbildning, anpassade artefakter och en distributions process som tillåter dirigering i säkerhets sfären som har definierats med Active Directory. Den här metoden tillämpar företags gränser och anger en gemensam uppsättning miljö kontroller. Dessa kontroller mot miljön som en utvecklare kan ta hänsyn till när de utvecklar och följer en säker utvecklings livs cykel som en del av den övergripande processen. Målet är också att tillhandahålla en miljö som inte är alltför begränsad och som kan förhindra utveckling, men en rimlig uppsättning kontroller. Grup principerna på organisationsenheten (OU) som innehåller virtuella labb datorer kan vara en delmängd av de totala grup principer som finns i produktionen. De kan också vara ytterligare en uppsättning för att korrekt minimera eventuella identifierade risker.

### <a name="how-can-an-organization-ensure-data-integrity-to-ensure-that-remoting-developers-cant-remove-code-or-introduce-malware-or-unapproved-software"></a>Hur kan en organisation säkerställa data integriteten för att säkerställa att fjärrutvecklare inte kan ta bort kod eller införa skadlig kod eller program vara som inte godkänts?
Det finns flera lager med kontroll för att minimera hotet från externa konsulter, entreprenörer eller anställda som är fjärran på att samar beta i DevTest Labs.

Som tidigare nämnts måste det första steget ha ett acceptable use policy som har skapats och definierats som tydligt beskriver konsekvenserna när någon strider mot principen.

Det första lagret med kontroller för fjärråtkomst är att tillämpa en fjärråtkomstprincip via en VPN-anslutning som inte är direkt ansluten till labbet.

Det andra lagret med kontroller är att använda en uppsättning grup princip objekt som förhindrar kopiering och inklistring via fjärr skrivbord. En nätverks princip kan implementeras för att inte tillåta utgående tjänster från miljön, till exempel FTP-och RDP-tjänster från miljön. Användardefinierad routning kan tvinga all Azure-nätverkstrafik tillbaka till lokalt, men routningen kunde inte ta hänsyn till alla URL: er som kan möjliggöra överföring av data om du inte styrs via en proxy för att genomsöka innehåll och sessioner. Offentliga IP-adresser kan begränsas inom det virtuella nätverk som stöder DevTest Labs för att inte tillåta bryggning av en extern nätverks resurs.

Slutligen måste samma typ av begränsningar tillämpas i hela organisationen, vilket skulle kunna ta hänsyn till alla möjliga metoder för flyttbara media eller externa URL: er som kan ta emot en post av innehållet. Kontakta din säkerhets personal för att granska och implementera en säkerhets princip. Fler rekommendationer finns i [Microsoft cyberhot Security](https://www.microsoft.com/security/default.aspx?&WT.srch=1&wt.mc_id=AID623240_SEM_sNYnsZDs).

## <a name="lab-configuration"></a>Labb konfiguration

### <a name="how-do-i-create-a-lab-from-a-resource-manager-template"></a>Hur gör jag för att skapar du ett labb från en Resource Manager-mall?
Vi erbjuder en [GitHub-lagringsplats med labb Azure Resource Manager mallar](https://azure.microsoft.com/resources/templates/101-dtl-create-lab) som du kan distribuera som-är eller ändra för att skapa anpassade mallar för dina labb. Varje mall innehåller en länk för att distribuera labbet som i din egen Azure-prenumeration. Du kan också anpassa mallen och [distribuera med hjälp av PowerShell eller Azure CLI](../azure-resource-manager/templates/deploy-powershell.md).


### <a name="can-i-have-all-virtual-machines-to-be-created-in-a-common-resource-group-instead-having-each-machine-in-its-own-resource-group"></a>Kan jag ha alla virtuella datorer som ska skapas i en gemensam resurs grupp i stället för varje dator i sin egen resurs grupp?
Ja, som labb ägare kan du antingen låta labbet hantera resurs grupp tilldelningen åt dig eller ha alla virtuella datorer som skapats i en gemensam resurs grupp som du anger.

Separata resurs grupps scenario:
-   DevTest Labs skapar en ny resurs grupp för varje offentlig/privat virtuell IP-dator som du ökar
-   DevTest Labs skapar en resurs grupp för delade IP-datorer som tillhör samma storlek.

Gemensamt resurs grupps scenario:
-   Alla virtuella datorer är i den gemensamma resurs grupp som du anger. Läs mer [om allokering av resurs grupper för labbet](./resource-group-control.md).

### <a name="how-do-i-maintain-a-naming-convention-across-my-devtest-labs-environment"></a>Hur gör jag för att underhålla en namngivnings konvention i min DevTest Labs-miljö?
Du kanske vill utöka de nuvarande konventionerna för företags namn till Azure-åtgärder och göra dem konsekventa i DevTest Labs-miljön. När du distribuerar DevTest Labs rekommenderar vi att du har vissa start principer. Du distribuerar dessa principer av ett centralt skript och JSON-mallar för att genomdriva konsekvens. Namngivnings principer kan implementeras via Azure-principer som tillämpas på prenumerations nivån. JSON-exempel för Azure Policy finns i [Azure policy exempel](../governance/policy/samples/index.md).

### <a name="how-many-labs-can-i-create-under-the-same-subscription"></a>Hur många labb kan jag skapa under samma prenumeration?
Det finns ingen viss gräns för antalet Labb som kan skapas per prenumeration. Den mängd resurser som används per prenumeration är dock begränsad. Du kan läsa om [gränser och kvoter för Azure-prenumerationer](../azure-resource-manager/management/azure-subscription-service-limits.md) och [hur du ökar dessa gränser](https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests).


### <a name="how-many-vms-can-i-create-per-lab"></a>Hur många virtuella datorer kan jag skapa per labb?
Det finns ingen viss gräns för antalet virtuella datorer som kan skapas per labb. Men de resurser (virtuella dator kärnor, offentliga IP-adresser och så vidare) som används är begränsade per prenumeration. Du kan läsa om [gränser och kvoter för Azure-prenumerationer](../azure-resource-manager/management/azure-subscription-service-limits.md) och [hur du ökar dessa gränser](https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests).

### <a name="how-do-i-determine-the-ratio-of-users-per-lab-and-the-overall-number-of-labs-that-are-needed-across-an-organization"></a>Hur gör jag för att fastställa förhållandet mellan användarna per labb och det totala antalet Labb som behövs i en organisation?
Vi rekommenderar att affär senheter och utvecklings grupper som är associerade med samma utvecklings projekt är associerade med samma labb. Den tillåter samma typer av principer, avbildningar och avslutnings principer som tillämpas på båda grupperna.

Du kan också behöva överväga geografiska gränser. Till exempel kan utvecklare i norra östra USA (US) använda ett labb som har etablerad i östra 2; USA. Och utvecklare i Borås, Texas och Denver, kan i USA hänvisas till att använda en resurs i södra centrala USA. Om det finns en samarbets ansträngning med en extern tredje part kan de tilldelas ett labb som inte används av interna utvecklare.

Du kan också använda ett labb för ett enskilt projekt i Azure DevOps Projects. Sedan tillämpar du säkerheten via en angiven Azure Active Directory grupp, som ger åtkomst till båda uppsättningarna av resurser. Det virtuella nätverk som tilldelats labbet kan vara en annan bindning för att konsolidera användare.

### <a name="how-can-we-prevent-the-deletion-of-resources-within-a-lab"></a>Hur kan vi förhindra att resurser tas bort inom ett labb?
Vi rekommenderar att du ställer in rätt behörigheter på labb nivån så att endast auktoriserade användare kan ta bort resurser eller ändra labb principer. Utvecklare bör placeras i gruppen **DevTest Labs-användare** . Lead-utvecklaren eller infrastrukturens lead bör vara **DevTest Labs-ägare**. Vi rekommenderar att du bara har två labb ägare. Den här principen sträcker sig över kod lagrings platsen för att undvika skador. Labb användare har behörighet att använda resurser, men kan inte uppdatera labb principer. Se följande artikel som visar de roller och rättigheter som varje inbyggd grupp har i ett labb: [Lägg till ägare och användare i Azure DevTest Labs](devtest-lab-add-devtest-user.md).

### <a name="how-do-i-share-a-direct-link-to-my-lab"></a>Hur gör jag för att dela en direkt länk till mitt labb?

1. I [Azure Portal](https://portal.azure.com)går du till labbet.
2. Kopiera **labb-URL: en** från webbläsaren och dela den sedan med dina labb användare.

> [!NOTE]
> Om en labb användare är en extern användare som har en Microsoft-konto, men som inte är medlem i din organisations Active Directory instans, kan användaren se ett fel meddelande när de försöker få åtkomst till den delade länken. Om en extern användare ser ett fel meddelande ber du användaren att först välja sitt namn i det övre högra hörnet i Azure Portal. I avsnittet katalog på menyn kan användaren välja den katalog där labbet finns.

## <a name="virtual-machines"></a>Virtuella datorer

### <a name="why-cant-i-see-vms-on-the-virtual-machines-page-that-i-see-in-devtest-labs"></a>Varför kan jag inte se virtuella datorer på sidan Virtual Machines som jag ser i DevTest Labs?
När du skapar en virtuell dator i DevTest Labs får du behörighet att komma åt den virtuella datorn. Du kan visa den virtuella datorn på sidan labb och på sidan **Virtual Machines** . Användare som har tilldelats rollen **DevTest Labs-ägare** kan se alla virtuella datorer som har skapats i labbet på Labbets **alla Virtual Machines** sida. Användare som har **användar rollen DevTest Labs** beviljas dock inte automatiskt Läs behörighet till VM-resurser som andra användare har skapat. Dessa virtuella datorer visas därför inte på sidan **Virtual Machines** .


### <a name="how-do-i-create-multiple-vms-from-the-same-template-at-once"></a>Hur gör jag för att skapa flera virtuella datorer från samma mall på samma gång?
Du har två alternativ för att samtidigt skapa flera virtuella datorer från samma mall:

- Du kan använda [Azure DevOps tasks-tillägget](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks).
- Du kan [skapa en Resource Manager-mall](devtest-lab-add-vm.md#save-azure-resource-manager-template) när du skapar en virtuell dator och [distribuera Resource Manager-mallen från Windows PowerShell](../azure-resource-manager/templates/deploy-powershell.md).
- Du kan också ange mer än en instans av en dator som ska skapas när du skapar en virtuell dator. Mer information om hur du skapar flera instanser av virtuella datorer finns i dokumentet om att [skapa en virtuell labb dator](devtest-lab-add-vm.md).

### <a name="how-do-i-move-my-existing-azure-vms-into-my-devtest-labs-lab"></a>Hur gör jag för att flytta mina befintliga virtuella Azure-datorer till mitt DevTest Labs labb?
Så här kopierar du befintliga virtuella datorer till DevTest Labs:

1.  Kopiera VHD-filen för din befintliga virtuella dator med hjälp av ett [Windows PowerShell-skript](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/Scripts/CopyVirtualMachines/CopyAzVHDFromVMToLab.ps1).
2.  Skapa den [anpassade avbildningen](devtest-lab-create-template.md) i DevTest Labs Lab.
3.  Skapa en virtuell dator i labbet från din anpassade avbildning.

### <a name="can-i-attach-multiple-disks-to-my-vms"></a>Kan jag koppla flera diskar till mina virtuella datorer?

Ja, du kan koppla flera diskar till dina virtuella datorer.

### <a name="are-gen-2-images-supported-by-devtest-labs"></a>Stöds generation 2-avbildningar av DevTest Labs?
Ja. Tjänsten DevTest Labs stöder [generation 2-avbildningar](../virtual-machines/generation-2.md). Men om både gen 1-och generation 2-versioner är tillgängliga för en avbildning, visar DevTest Labs bara generation 1-versionen av avbildningen när du skapar en virtuell dator. Du ser avbildningen om det bara finns en version av den tillgängliga generation 2. 

### <a name="if-i-want-to-use-a-windows-os-image-for-my-testing-do-i-have-to-purchase-an-msdn-subscription"></a>Behöver jag köpa en MSDN-prenumeration om jag vill använda en Windows OS-avbildning för min testning?
Gör något av följande om du vill använda Windows Client OS-avbildningar (Windows 7 eller en senare version) för utveckling eller testning i Azure:

- [Köp en MSDN-prenumeration](https://www.visualstudio.com/products/how-to-buy-vs).
- Om du har en Enterprise-avtal skapar du en Azure-prenumeration med det [Enterprise dev/test erbjudandet](https://azure.microsoft.com/offers/ms-azr-0148p).

Mer information om Azure-krediterna för varje MSDN-erbjudande finns i [månatlig Azure-kredit för Visual Studio-prenumeranter](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/).


### <a name="how-do-i-automate-the-process-of-deleting-all-the-vms-in-my-lab"></a>Hur gör jag för att automatisera processen med att ta bort alla virtuella datorer i mitt labb?
Som labb ägare kan du ta bort virtuella datorer från labbet i Azure Portal. Du kan också ta bort alla virtuella datorer i labbet med hjälp av ett PowerShell-skript. I följande exempel, under **värdena för att ändra** kommentaren, ändrar du parameter värden. Du kan hämta `subscriptionId` värdena, `labResourceGroup` och `labName` från labb fönstret i Azure Portal.

```powershell
# Delete all the VMs in a lab.

# Values to change:
$subscriptionId = "<Enter Azure subscription ID here>"
$labResourceGroup = "<Enter lab's resource group here>"
$labName = "<Enter lab name here>"

# Sign in to your Azure account.
Connect-AzAccount

# Select the Azure subscription that has the lab. This step is optional
# if you have only one subscription.
Select-AzSubscription -SubscriptionId $subscriptionId

# Get the lab that has the VMs that you want to delete.
$lab = Get-AzResource -ResourceId ('subscriptions/' + $subscriptionId + '/resourceGroups/' + $labResourceGroup + '/providers/Microsoft.DevTestLab/labs/' + $labName)

# Get the VMs from that lab.
$labVMs = Get-AzResource | Where-Object {
          $_.ResourceType -eq 'microsoft.devtestlab/labs/virtualmachines' -and
          $_.Name -like "$($lab.Name)/*"}

# Delete the VMs.
foreach($labVM in $labVMs)
{
    Remove-AzResource -ResourceId $labVM.ResourceId -Force
}
```

## <a name="environments"></a>Miljöer

### <a name="how-can-i-use-resource-manager-templates-in-my-devtest-labs-environment"></a>Hur kan jag använda Resource Manager-mallar i min DevTest Labs-miljö?
Du distribuerar dina Resource Manager-mallar till en DevTest labb miljö med hjälp av de steg som beskrivs i [miljö funktionen i DevTest Labs](devtest-lab-test-env.md) -artikeln. I princip kan du kontrol lera dina Resource Manager-mallar i en git-lagringsplats (antingen Azure databaser eller GitHub) och lägga till ett [privat lager för dina mallar](devtest-lab-test-env.md) i labbet. Det här scenariot kanske inte är användbart om du använder DevTest Labs för att hantera utvecklings datorer, men det kan vara användbart om du skapar en fristående miljö som är representativ för produktionen.

Det är också värt att Observera att alternativet antal virtuella datorer per labb eller per användare endast begränsar antalet datorer internt som skapats i själva labbet, och inte av några miljöer (Resource Manager-mallar).

## <a name="custom-images"></a>Anpassade avbildningar

### <a name="how-can-i-set-up-an-easily-repeatable-process-to-bring-my-custom-organizational-images-into-a-devtest-labs-environment"></a>Hur kan jag skapa en lätt upprepnings bar process för att överföra mina anpassade organisations bilder till en DevTest Labs-miljö?
Se det här [videoklippet om bild fabriks mönster](https://sec.ch9.ms/ch9/8e8a/9ea0b8d4-b803-4f23-bca4-4808d9368e8a/dtlimagefactory_mid.mp4). Det här scenariot är ett avancerat scenario och de angivna skripten är endast exempel skript. Om det krävs några ändringar måste du hantera och underhålla skripten som används i din miljö.

Detaljerad information om hur du skapar en avbildnings fabrik finns [i skapa en anpassad avbildnings fabrik i Azure DevTest Labs](image-factory-create.md).

### <a name="what-is-the-difference-between-a-custom-image-and-a-formula"></a>Vad är skillnaden mellan en anpassad bild och en formel?
En anpassad avbildning är en hanterad avbildning. En formel är en bild som du kan konfigurera med ytterligare inställningar och sedan Spara och återskapa. En anpassad avbildning kan vara bättre om du snabbt vill skapa flera miljöer med samma grundläggande och oföränderliga avbildning. En formel kan vara bättre om du vill återskapa konfigurationen av din virtuella dator med de senaste bitarna som en del av ett virtuellt nätverk eller undernät eller som en virtuell dator med en angiven storlek. En mer detaljerad förklaring finns i [jämföra anpassade bilder och formler i DevTest Labs](devtest-lab-comparing-vm-base-image-types.md).

### <a name="when-should-i-use-a-formula-vs-custom-image"></a>När ska jag använda en formel eller en anpassad avbildning?
Normalt är den avgörande faktorn i det här scenariot kostnad och åter användning. Om du har ett scenario där många användare/labb kräver en avbildning med mycket program vara ovanpå bas avbildningen kan du minska kostnaderna genom att skapa en anpassad avbildning. Det innebär att avbildningen skapas en gång. Det minskar installations tiden för den virtuella datorn och kostnaden som uppstått på grund av att den virtuella datorn körs när installationen sker.

En extra faktor att notera är dock frekvensen av ändringar i ditt program varu paket. Om du kör dagliga versioner och kräver att programmet finns på användarnas virtuella datorer, bör du överväga att använda en formel i stället för en anpassad avbildning.

En mer detaljerad förklaring finns i [jämföra anpassade bilder och formler](devtest-lab-comparing-vm-base-image-types.md) i DevTest Labs.

### <a name="how-do-i-automate-the-process-of-uploading-vhd-files-to-create-custom-images"></a>Hur gör jag för att automatisera processen med att ladda upp VHD-filer för att skapa anpassade avbildningar?

Om du vill automatisera uppladdningen av VHD-filer för att skapa anpassade avbildningar har du två alternativ:

- Använd [AzCopy](../storage/common/storage-use-azcopy-v10.md) för att kopiera eller ladda upp VHD-filer till det lagrings konto som är associerat med labbet.
- Använd [Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md). Storage Explorer är en fristående app som körs på Windows, OS X och Linux.

Så här hittar du det mål lagrings konto som är associerat med ditt labb:

1.  Logga in på [Azure-portalen](https://portal.azure.com).
2.  Välj **resurs grupper** på den vänstra menyn.
3.  Sök efter och välj den resurs grupp som är kopplad till ditt labb.
4.  Under **Översikt** väljer du ett av lagrings kontona.
5.  Välj **blobbar**.
6.  Sök efter uppladdningar i listan. Om ingen finns går du tillbaka till steg 4 och försöker med ett annat lagrings konto.
7.  Använd **webb adressen** som mål i AzCopy-kommandot.

När ska jag använda en Azure Marketplace-avbildning eller en egen anpassad organisations avbildning?

### <a name="when-should-i-use-an-azure-marketplace-image-vs-my-own-custom-organizational-image"></a>När ska jag använda en Azure Marketplace-avbildning eller en egen anpassad organisations avbildning?
Azure Marketplace bör användas som standard om du inte har särskilda problem eller organisations krav. Några vanliga exempel är:

- Komplex program vara som kräver att ett program ingår som en del av bas avbildningen.
- Det kan ta flera timmar att installera och konfigurera ett program, vilket inte är en effektiv användning av beräknings tiden som ska läggas till på en Azure Marketplace-avbildning.
- Utvecklare och testare behöver snabbt åtkomst till en virtuell dator och vill minimera installations tiden för en ny virtuell dator.
- Efterlevnads-eller reglerings villkor (till exempel säkerhets principer) som måste finnas på plats för alla datorer.
- Att använda anpassade bilder bör inte anses vara lätt. De introducerar extra komplexitet eftersom du nu måste hantera VHD-filer för de underliggande bas avbildningarna. Du måste också regelbundet korrigera de grundläggande avbildningarna med program uppdateringar. Dessa uppdateringar omfattar nya operativ Systems uppdateringar och uppdateringar eller konfigurations ändringar som krävs för själva programpaketet.

## <a name="artifacts"></a>Artifacts

### <a name="what-are-artifacts"></a>Vad är artefakter?
Artefakter är anpassningsbara element som du kan använda för att distribuera de senaste bitarna eller distribuera dina dev-verktyg till en virtuell dator. Bifoga artefakter till den virtuella datorn när du skapar den virtuella datorn. När den virtuella datorn har allokerats distribuerar artefakterna och konfigurerar den virtuella datorn. Olika befintliga artefakter är tillgängliga i vår [offentliga GitHub-lagringsplats](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts). Du kan också [Redigera dina egna artefakter](devtest-lab-artifact-author.md).

### <a name="my-artifact-failed-during-vm-creation-how-do-i-troubleshoot-it"></a>Min artefakt kunde inte skapas när den virtuella datorn skapades. Hur gör jag för att fel sökning?
Information om hur du hämtar loggar för den misslyckade artefakten finns i [så här diagnostiserar du artefakt fel i DevTest Labs](devtest-lab-troubleshoot-artifact-failure.md).

### <a name="when-should-an-organization-use-a-public-artifact-repository-vs-private-artifact-repository-in-devtest-labs"></a>När ska en organisation använda ett offentligt artefakt lager eller en privat artefakt lagrings plats i DevTest Labs?
Den [offentliga artefakt lagrings platsen](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts) innehåller en första uppsättning program varu paket som används oftast. Den hjälper till med snabb distribution utan att behöva investera tid för att återskapa vanliga utvecklingsverktyg och tillägg. Du kan välja att distribuera egna privata lagrings platser. Du kan använda en offentlig och en privat databas parallellt. Du kan också välja att inaktivera den offentliga lagrings platsen. Villkoren för att distribuera ett privat lager bör drivas av följande frågor och överväganden:

- Har organisationen ett krav på att ha en licensierad program vara som en del av deras DevTest Labs-erbjudande? Om svaret är ja, ska du skapa ett privat lager.
- Utvecklar organisationen anpassad program vara som tillhandahåller en speciell åtgärd, vilket krävs som en del av den övergripande etablerings processen? Om svaret är ja, ska en privat lagrings plats distribueras.
- Om organisationens styrnings princip kräver isolering, och externa databaser inte är under direkt konfigurations hantering av organisationen, bör ett privat artefakt lager distribueras. Som en del av den här processen kan en ursprunglig kopia av det offentliga lagrings platsen kopieras och integreras med den privata lagrings platsen. Sedan kan den offentliga lagrings platsen inaktive ras så att ingen i organisationen kan komma åt den längre. Den här metoden tvingar alla användare i organisationen att bara ha en enda lagrings plats som godkänts av organisationen och minimerar konfigurations avvikelser.


### <a name="should-an-organization-plan-for-a-single-repository-or-allow-multiple-repositories"></a>Bör en organisations plan för en enda lagrings plats eller tillåta flera databaser?
Som en del av din organisations övergripande styrnings-och konfigurations hanterings strategi rekommenderar vi att du använder en central lagrings plats. När du använder flera databaser kan de bli silor av ohanterad program vara under tiden. Med en central lagrings plats kan flera team använda artefakter från den här lagrings platsen för sina projekt. Den tillämpar standardisering, säkerhet, enkel hantering och eliminerar dubbleringen av insatser. Som en del av centralisering är följande åtgärder rekommenderade metoder för långsiktig hantering och hållbarhet:

- Associera Azure-databaser med samma Azure Active Directory klient som Azure-prenumerationen använder för autentisering och auktorisering.
- Skapa en grupp med namnet `All DevTest Labs Developers` i Azure Active Directory som hanteras centralt. Alla utvecklare som bidrar till artefakt utveckling bör placeras i den här gruppen.
- Samma Azure Active Directorys grupp kan användas för att ge åtkomst till Azure databaser-lagringsplatsen och till labbet.
- I Azure databaser bör branchning eller förgreningar användas för att separera ett utvecklings lager från den primära produktions lagrings platsen. Innehåll läggs bara till i huvud grenen med en pull-begäran efter en korrekt kod granskning. När kod granskaren godkänner ändringen, en lead-utvecklare, som ansvarar för underhåll av huvud grenen, sammanfogar den uppdaterade koden.

## <a name="cicd-integration"></a>CI/CD-integrering

### <a name="does-devtest-labs-integrate-with-my-cicd-toolchain"></a>Integrerar DevTest Labs med min CI/CD-verktygskedjan?
Om du använder Azure-DevOps kan du använda ett [tillägg för DevTest Labs-aktiviteter](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks) för att automatisera din versions pipeline i DevTest Labs. Några av de uppgifter som du kan göra med tillägget är:

- Skapa och distribuera en virtuell dator automatiskt. Du kan också konfigurera den virtuella datorn med den senaste versionen med hjälp av Azure File Copy eller PowerShell Azure DevOps Services-uppgifter.
- Ta automatiskt reda på status för en virtuell dator efter testning för att återskapa ett fel på samma virtuella dator för ytterligare undersökning.
- Ta bort den virtuella datorn i slutet av versions pipelinen när den inte längre behövs.

Följande blogg inlägg ger vägledning och information om hur du använder Azure DevOps Services-tillägget:

- [DevTest Labs och Azure DevOps-tillägget](integrate-environments-devops-pipeline.md)
- [Distribuera en ny virtuell dator i ett befintligt DevTest Labs labb från Azure DevOps Services](https://www.visualstudiogeeks.com/blog/DevOps/Deploy-New-VM-To-Existing-AzureDevTestLab-From-VSTS)
- [Använda versions hantering i Azure DevOps Services för kontinuerlig distribution till DevTest-labb](https://www.visualstudiogeeks.com/blog/DevOps/Use-VSTS-ReleaseManagement-to-Deploy-and-Test-in-AzureDevTestLabs)

För andra verktygs kedjor (CI)/Continuous Delivery (CD) kan du uppnå samma scenarier genom att distribuera [Azure Resource Manager mallar](https://azure.microsoft.com/resources/templates/) genom att använda [Azure PowerShell-cmdlets](../azure-resource-manager/templates/deploy-powershell.md) och [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.DevTestLabs/): er. Du kan också använda [REST-API: er för DevTest Labs](https://aka.ms/dtlrestapis) för att integrera med din verktygskedjan.

## <a name="networking"></a>Nätverk

### <a name="when-should-i-create-a-new-virtual-network-for-my-devtest-labs-environment-vs-using-an-existing-virtual-network"></a>När ska jag skapa ett nytt virtuellt nätverk för min DevTest Labs-miljö jämfört med att använda ett befintligt virtuellt nätverk?
Om dina virtuella datorer behöver interagera med en befintlig infrastruktur kan du överväga att använda ett befintligt virtuellt nätverk i din DevTest Labs-miljö. Om du använder ExpressRoute kanske du vill minimera antalet virtuella nätverk/undernät så att du inte fragmenterar ditt IP-adressutrymme som tilldelas för användning i prenumerationerna.

Överväg att använda det virtuella nätverkets peering-mönster här ([Hub-eker modell](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke)). Den här metoden aktiverar VNet/Subnet-kommunikation mellan prenumerationer. I annat fall kan varje DevTest Labs-miljö ha sitt eget virtuella nätverk.

Det finns [gränser](../azure-resource-manager/management/azure-subscription-service-limits.md) för antalet virtuella nätverk per prenumeration. Standardvärdet är 50, men den här gränsen kan höjas till 100.

### <a name="when-should-i-use-a-shared-ip-vs-public-ip-vs-private-ip"></a>När ska jag använda en delad IP-adress eller offentlig IP-adress jämfört med privat IP?

Om du använder en plats-till-plats-VPN eller Express Route bör du överväga att använda privata IP-adresser så att datorerna kan nås via det interna nätverket och inte kan nås via offentliga Internet.

> [!NOTE]
> Labb ägare kan ändra den här under näts principen så att ingen av misstag skapar offentliga IP-adresser för sina virtuella datorer. Prenumerationens ägare bör skapa en prenumerations princip som förhindrar att offentliga IP-adresser skapas.

När du använder delade offentliga IP-adresser delar de virtuella datorerna i ett labb en offentlig IP-adress. Den här metoden kan vara till hjälp när du behöver undvika att lösa begränsningar för offentliga IP-adresser för en specifik prenumeration.

### <a name="how-do-i-ensure-that-development-and-test-virtual-machines-are-unable-to-reach-the-public-internet-are-there-any-recommended-patterns-to-set-up-network-configuration"></a>Hur gör jag för att se till att det inte går att komma åt de virtuella datorerna i utvecklings-och test miljön? Finns det några rekommenderade mönster för att konfigurera nätverks konfigurationen?

Ja. Det finns två aspekter att överväga – inkommande och utgående trafik.

- **Inkommande trafik** – om den virtuella datorn inte har en offentlig IP-adress kan den inte nås via Internet. En vanlig metod är att se till att en princip för en prenumerations nivå har angetts, så att ingen användare kan skapa en offentlig IP-adress.
- **Utgående trafik** – om du vill förhindra att virtuella datorer får åtkomst till offentligt Internet direkt och tvingar trafik genom en företags brand vägg, kan du dirigera trafik lokalt via Express Route eller VPN genom att använda Tvingad routning.

> [!NOTE]
> Om du har en proxyserver som blockerar trafik utan proxyinställningar, ska du inte glömma att lägga till undantag till Labbets artefakt lagrings konto.

Du kan också använda nätverks säkerhets grupper för virtuella datorer eller undernät. Det här steget lägger till ett extra skydds lager för att tillåta/blockera trafik.

## <a name="troubleshooting"></a>Felsökning

### <a name="why-isnt-my-existing-virtual-network-saving-properly"></a>Varför sparar inte mitt befintliga virtuella nätverk korrekt?
En möjlighet är att det virtuella nätverks namnet innehåller punkter. I så fall kan du försöka ta bort perioderna eller ersätta dem med bindestreck. Försök sedan igen för att spara det virtuella nätverket.

### <a name="why-do-i-get-a-parent-resource-not-found-error-when-i-provision-a-vm-from-powershell"></a>Varför visas fel meddelandet "Det gick inte att hitta den överordnade resursen" när jag etablerar en virtuell dator från PowerShell?
När en resurs är överordnad till en annan resurs måste den överordnade resursen finnas innan du skapar den underordnade resursen. Om den överordnade resursen inte finns visas ett **ParentResourceNotFound** -meddelande. Om du inte anger ett beroende på den överordnade resursen kan den underordnade resursen distribueras före den överordnade resursen.

Virtuella datorer är underordnade resurser under ett labb i en resurs grupp. När du använder Resource Manager-mallar för att distribuera virtuella datorer med hjälp av PowerShell bör resurs grupps namnet som anges i PowerShell-skriptet vara namnet på resurs gruppen i labbet. Mer information finns i [Felsöka vanliga](../azure-resource-manager/templates/common-deployment-errors.md)problem med Azure-distribution.

### <a name="where-can-i-find-more-error-information-if-a-vm-deployment-fails"></a>Var kan jag hitta mer fel information om det inte går att distribuera en virtuell dator?
Distributions fel för virtuella datorer samlas in i aktivitets loggarna. Du kan hitta aktivitets loggar för labb virtuella datorer under **gransknings loggar** eller **diagnostik för virtuella datorer** på resurs menyn på sidan för labbets virtuella dator (sidan visas när du har valt den virtuella datorn från listan Mina virtuella datorer).

Ibland uppstår distributions felet innan distributionen av virtuella datorer påbörjas. Ett exempel är när prenumerations gränsen för en resurs som har skapats med den virtuella datorn har överskridits. I det här fallet registreras fel informationen i aktivitets loggarna på labb nivå. Aktivitets loggar finns längst ned i **konfigurations-och princip** inställningarna. Mer information om hur du använder aktivitets loggar i Azure finns i [Visa aktivitets loggar för att granska åtgärder på resurser](../azure-resource-manager/management/view-activity-logs.md).

### <a name="why-do-i-get-location-is-not-available-for-resource-type-error-when-trying-to-create-a-lab"></a>Varför visas fel meddelandet "platsen är inte tillgänglig för resurs typen" när jag försöker skapa ett labb?
Du kan se ett fel meddelande som liknar följande när du försöker skapa ett labb:

```
The provided location 'australiacentral' is not available for resource type 'Microsoft.KeyVault/vaults'. List of available regions for the resource type is 'northcentralus,eastus,northeurope,westeurope,eastasia,southeastasia,eastus2,centralus,southcentralus,westus,japaneast,japanwest,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia,canadacentral,canadaeast,uksouth,ukwest,westcentralus,westus2,koreacentral,koreasouth,francecentral,southafricanorth
```

Du kan lösa det här felet genom att göra något av följande:

#### <a name="option-1"></a>Alternativ 1
Kontrol lera tillgängligheten för resurs typen i Azure-regioner på sidan [produkter som är tillgängliga efter region](https://azure.microsoft.com/global-infrastructure/services/) . Om resurs typen inte är tillgänglig i en viss region har DevTest Labs inte stöd för att skapa ett labb i den regionen. Välj en annan region när du skapar labbet.

#### <a name="option-2"></a>Alternativ 2
Om resurs typen är tillgänglig i din region kontrollerar du om den är registrerad i din prenumeration. Du kan göra det på prenumerationens ägar nivå enligt [den här artikeln](../azure-resource-manager/management/resource-providers-and-types.md).