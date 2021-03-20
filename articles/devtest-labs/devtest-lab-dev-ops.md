---
title: Integrering av Azure DevTest Labs och DevOps | Microsoft Docs
description: Lär dig hur du använder labb Azure DevTest Labs inom en pipeline för kontinuerlig integrering (CI)/kontinuerlig leverans (CD) i en företags miljö.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 8a5d35a541e079b7d39cae2ec43da608274533f5
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "85481076"
---
# <a name="integration-of-azure-devtest-labs-and-azure-devops"></a>Integrering av Azure DevTest Labs och Azure-DevOps
DevOps är en metod för program varu utveckling som integrerar program varu utveckling (dev) med åtgärder (OPS) för ett system. Det här systemet kan leverera nya funktioner, uppdateringar och korrigeringar i anpassnings syfte med affärs mål. Den här metoden omfattar allt från att utforma nya funktioner baserat på mål, användnings mönster och feedback från kunder. att åtgärda, återvinna och härdning av systemet när problem uppstår. En lätt identifierbar komponent av den här metoden är pipeline för kontinuerlig integrering (CI)/kontinuerlig leverans (CD). En CI/CD-pipeline tar information, kod och resurser från ett genomförande genom en serie steg som omfattar utveckling, testning och distribution, för att skapa systemet. Den här artikeln fokuserar på olika sätt att effektivt använda labb i en pipeline i en företags miljö. 

## <a name="benefits-of-using-labs-in-devops-workflow"></a>Fördelar med att använda labb i DevOps-arbetsflöde 

### <a name="focused-access"></a>Fokuserad åtkomst 
Genom att använda ett labb som en komponent kan ett visst eko system associeras med en begränsad grupp personer. Ett team eller en grupp som arbetar i ett gemensamt områden eller en speciell funktion har vanligt vis tilldelats ett labb.   

### <a name="infrastructure-replication-in-the-cloud"></a>Infrastruktur replikering i molnet 
En utvecklare kan snabbt skapa ett utvecklings eko system som innehåller en dev box med källkod och verktyg. Utvecklaren kan också skapa en miljö som är nästan identisk med produktions konfigurationen. Det hjälper till med en snabbare inre loop-utveckling. 

### <a name="pre-production"></a>Förproduktion 
Om du har ett labb i CI/CD-pipeline blir det enklare att ha flera olika för produktions miljöer eller datorer som körs samtidigt för asynkron testning. Olika stöd infrastrukturer som build-agenter kan distribueras och hanteras i ett labb. 

## <a name="devops-with-devtest-labs"></a>DevOps med DevTest Labs 

### <a name="development--operation"></a>Utveckling/åtgärd 
Ett labb bör fokuseras på ett team som arbetar i ett funktions områden. Detta vanliga fokus gör det möjligt att dela sitespecifika resurser, t. ex. verktyg, skript eller Resource Manager-mallar. Den tillåter snabbare ändringar och begränsar de negativa effekterna till en mindre grupp. Dessa delade resurser gör det möjligt för utvecklare att skapa virtuella utvecklings datorer med all nödvändig kod, alla verktyg och all konfiguration. De kan antingen skapas dynamiskt eller ha ett system som skapar bas avbildningar med anpassningarna. Det går inte bara att skapa virtuella datorer i utvecklaren, utan även de kan skapa DevTest Labs-miljöer baserat på de mallar som krävs för att skapa lämpliga Azure-resurser i labbet. Ändringar eller destruktivt arbete kan göras mot labb miljön utan att någon annan påverkas. Tänk dig ett scenario där produkten är ett fristående system som är installerat på kundens dator. I det här scenariot har DevTest Labs förbättrat skapande av virtuella datorer som innehåller installation av ytterligare program vara med hjälp av artefakter och för att skapa kundkonfigurationer för snabbare inre loop-testning av koden. 
  
## <a name="cicd-pipeline"></a>CI/CD-pipeline 
CI/CD-pipelinen är en av de viktigaste komponenterna i DevOps som flyttar kod från en pull-begäran från utvecklaren, integrerar den med den befintliga koden och distribuerar den till produktions eko systemet. Alla resurser behöver inte vara inom ett labb. Till exempel kan en Jenkins-värd konfigureras utanför labbet som en mer permanent resurs. Här följer några exempel på hur du integrerar labb i pipelinen. 

### <a name="build"></a>Skapa 
Den bygga pipelinen fokuserar på att skapa ett paket med komponenter som testas tillsammans för att skickas vidare till versions pipelinen. Labb kan vara en del av build-pipeline som platsen för att bygga agenter och andra support resurser. Att kunna bygga infrastrukturen dynamiskt ger bättre kontroll. Med möjligheten att ha flera miljöer i ett labb kan varje version köras asynkront samtidigt som du använder build-ID som en del av miljö informationen för att unikt identifiera resurserna för den specifika versionen.   

För build-agenter ökar Labbets möjlighet att begränsa åtkomsten säkerheten och minskar risken för oavsiktliga skador.  

### <a name="test"></a>Testa 
Med DevTest Labs kan en CI/CD-pipeline automatisera skapandet av Azure Resource (VM, miljöer) som kan användas för automatisk och manuell testning. De virtuella datorerna skapas med artefakter eller formler som använder information från skapande processen för att skapa de olika anpassade konfigurationer som krävs för testning.   

### <a name="release"></a>Frisläpp 
DevTest Labs används ofta för verifiering i avsnittet version innan koden distribueras. Det liknar testning i avsnittet build. Produktions resurser bör inte distribueras inom DevTest Labs. 

### <a name="customization"></a>Anpassning 
I Azure DevOps finns det befintliga uppgifter som tillåter manipulering av virtuella datorer och miljöer inom vissa labb. Även om Azure DevOps Services är ett sätt att hantera CI/CD-pipeline kan du integrera labbet i alla system som stöder möjligheten att anropa REST-API: er, köra PowerShell-skript eller använda Azure CLI. 

Vissa CI/CD-ledare har befintliga plugin-program med öppen källkod som kan hantera resurser i Azure och DevTest Labs. Du kan behöva använda vissa anpassade skript för att passa de speciella behoven i pipelinen.  Med detta i åtanke, när en aktivitet körs, används ett huvud namn för tjänsten med lämplig roll för att få åtkomst till labbet. Tjänstens huvud namn behöver vanligt vis deltagar rollen åtkomst till labbet. Mer information finns i [integrera Azure DevTests Labs i din Azure DevOps-kontinuerlig integrering och leverans pipeline](devtest-lab-integrate-ci-cd.md). 
 