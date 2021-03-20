---
title: Använd PaaS-tjänster (Platform-as-a-Service) i Azure DevTest Labs
description: Lär dig hur du använder tjänster som plattform som en tjänst (pass) i Azure DevTest Labs.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: eec37527386098174906dc2737d7b763241da3f2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "85478747"
---
# <a name="use-platform-as-a-service-paas-services-in-azure-devtest-labs"></a>Använd PaaS-tjänster (Platform-as-a-Service) i Azure DevTest Labs
PaaS stöds i DevTest Labs via miljö funktionen. Miljöer i DevTest Labs stöds av förkonfigurerade Azure Resource Manager mallar i en git-lagringsplats. Miljöer kan innehålla både PaaS-och IaaS-resurser. De gör att du kan skapa komplexa system som kan omfatta Azure-resurser, till exempel virtuella datorer, databaser, virtuella nätverk och webbappar, som är anpassade för att fungera tillsammans. Dessa mallar ger konsekvent distribution och förbättrad hantering av miljöer med käll kods kontroll. 

Ett korrekt konfigurerat system tillåter följande scenarier: 

- Utvecklare har oberoende och flera miljöer
- Testning av olika konfigurationer asynkront
- Integrering av pipeline för mellanlagring och produktion utan några ändringar i mallen
- Att ha både utvecklings datorer och miljöer inom samma labb förbättrar enkel hantering och kostnads rapportering.  

DevTest Labs-providern skapar resurser för labb användarens räkning, så inga extra behörigheter måste ges till labb användaren för att aktivera användningen av PaaS-resurserna. Följande bild visar ett Service Fabric-kluster som en miljö i labbet.

![Service Fabric kluster som en miljö](./media/create-environment-service-fabric-cluster/cluster-created.png)

Mer information om hur du konfigurerar miljöer finns i [skapa miljöer med flera virtuella datorer och PaaS-resurser med Azure Resource Manager mallar](devtest-lab-create-environment-from-arm.md). DevTest Labs har ett offentligt lager med Azure Resource Manager mallar som du kan använda för att skapa miljöer utan att behöva ansluta till en extern GitHub-källa. Mer information om offentliga miljöer finns [i Konfigurera och använda offentliga miljöer i Azure DevTest Labs](devtest-lab-configure-use-public-environments.md).

I stora organisationer tillhandahåller utvecklings grupper vanligt vis miljöer som anpassade/isolerade test miljöer. Det kan finnas miljöer som ska användas av alla team inom en affär senhet eller en avdelning. IT-gruppen kanske vill ge sina miljöer som kan användas av alla team i organisationen.  

## <a name="customizations"></a>Anpassningar

#### <a name="sandbox"></a>Begränsat läge 
Labb ägaren kan anpassa labb miljöer för att ändra användarens roll från **läsare** till **deltagare** i resurs gruppen. Den här funktionen finns på sidan **Lab-inställningar** under Labbets **konfiguration och principer** . Den här roll ändringen gör att användaren kan lägga till eller ta bort resurser i den miljön. Använd Azure-principer om du vill begränsa åtkomsten ytterligare. Med den här funktionen kan du anpassa resurser eller konfiguration utan åtkomst på prenumerations nivå.

#### <a name="custom-tokens"></a>Anpassade token
Det finns viss anpassad labb information som ligger utanför resurs gruppen och är till för miljöer som mallen har åtkomst till. Här är några av dem: 

- Nätverks identifiering för labb
- Location
- Lagrings konto där filerna i Resource Manager-mallarna lagras. 
 
#### <a name="lab-virtual-network"></a>Virtuellt labb nätverk
I de [anslutande miljöerna till Labbets Virtual Network](connect-environment-lab-virtual-network.md) -artikel beskrivs hur du ändrar Resource Manager-mallen för att använda `$(LabSubnetId)` token. När en miljö skapas, `$(LabSubnetId)` ersätts token av det första under nätet där alternativet **Använd vid skapande av virtuell dator** är inställt på **Sant**. Det gör att vår miljö kan använda tidigare skapade nätverk. Om du vill använda samma Resource Manager-mallar i miljöer i test som mellanlagring och produktion, använder du `$(LabSubnetId)` som standardvärde i en Resource Manager-mallparameter. 

#### <a name="environment-storage-account"></a>Miljö lagrings konto
DevTest Labs stöder användning av [kapslade Resource Manager-mallar](../azure-resource-manager/templates/linked-templates.md). I artikeln [[distribuera kapslade Azure Resource Manager mallar för testnings miljöer](deploy-nested-template-environments.md) förklaras hur du använder  `_artifactsLocation` och tokens för att `_artifactsLocationSasToken` skapa en URI till en Resource Manager-mall i samma mapp som eller i en kapslad mapp i huvud mal len. Mer information om dessa två tokens finns i avsnittet **distributions artefakter** i [Azure Resource Manager – metod Tips Guide](https://github.com/Azure/azure-quickstart-templates/blob/master/1-CONTRIBUTION-GUIDE/best-practices.md).

## <a name="user-experience"></a>Användarupplevelse

## <a name="developer"></a>Utvecklare
Utvecklare använder samma arbets flöde för att skapa en virtuell dator för att skapa en speciell miljö. De väljer miljön eller dator avbildningen och anger nödvändig information som krävs för mallen. Varje utvecklare som har en miljö möjliggör distribution av ändringar och förbättrad fel sökning av inre loopar. Miljön kan skapas när som helst med den senaste mallen.  Den här funktionen gör att miljöer kan förstöras och återskapas för att minska stillestånds tiden från att behöva skapa systemet manuellt eller återställning från fel testning.  

### <a name="testing"></a>Testning
DevTest Labs-miljöer tillåter oberoende testning av särskild kod och konfigurationer asynkront. Den gemensamma övningen är att konfigurera miljön med koden från den enskilda pull-begäran och starta eventuella automatiserade tester. När den automatiserade testningen har slutförts kan alla manuella tester köras mot den aktuella miljön. Den här processen utförs vanligt vis som en del av CI/CD-pipeline. 

## <a name="management-experience"></a>Hanterings upplevelse

### <a name="cost-tracking"></a>Kostnads uppföljning
Funktionen kostnads uppföljning inkluderar Azure-resurser i olika miljöer som en del av den övergripande kostnads trenden. Kostnaden per resurs delar inte ut de olika resurserna i miljön, men visar miljön som en enda kostnad.

### <a name="security"></a>Säkerhet
En korrekt konfigurerad Azure-prenumeration med DevTest Labs kan [begränsa åtkomsten till Azure-resurser via labbet](devtest-lab-add-devtest-user.md). Med miljöer kan en labb ägare ge användare åtkomst till PaaS-resurser med godkända konfigurationer utan att tillåta åtkomst till andra Azure-resurser. I scenariot där labb användare anpassar miljöer, kan labb ägaren tillåta deltagar åtkomst. Deltagar åtkomsten ger labb användaren bara möjlighet att lägga till eller ta bort Azure-resurser i den hanterade resurs gruppen. Det möjliggör enklare spårning och hantering jämfört med att ge användaren deltagare åtkomst till prenumerationen.

### <a name="automation"></a>Automation
Automation är en viktig komponent för ett stort skalbart eko system. Automation krävs för att hantera hantering eller spårning av flera miljöer över prenumerationer och labb.

### <a name="cicd-pipeline"></a>CI/CD-pipeline
PaaS Services i DevTest Labs kan hjälpa till att förbättra CI/CD-pipelinen genom att ha fokuserade distributioner där åtkomsten styrs av labbet.

## <a name="next-steps"></a>Nästa steg
I följande artiklar finns information om miljöer: 

- 
- [Skapa miljöer med flera virtuella datorer och PaaS-resurser med Azure Resource Manager-mallar](devtest-lab-create-environment-from-arm.md)
- [Konfigurera och använda offentliga miljöer i Azure DevTest Labs](devtest-lab-configure-use-public-environments.md)
- [Skapa en miljö med fristående Service Fabric kluster i Azure DevTest Labs](create-environment-service-fabric-cluster.md)
- [Anslut en miljö till ditt labbs virtuella nätverk i Azure DevTest Labs](connect-environment-lab-virtual-network.md)
- [Integrera miljöer i Azure DevOps CI/CD-pipeliner](integrate-environments-devops-pipeline.md)
 





