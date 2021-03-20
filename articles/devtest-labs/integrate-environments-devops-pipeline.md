---
title: Integrera miljöer i Azure-pipeliner i Azure DevTest Labs
description: Lär dig hur du integrerar Azure DevTest Labs miljöer i din Azure DevOps-pipeline för kontinuerlig integrering (CI) och kontinuerlig leverans (CD).
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: be726b2a3f67fd3dada4fdc3cf794922a3c18d06
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "85483031"
---
# <a name="integrate-environments-into-your-azure-devops-cicd-pipelines"></a>Integrera miljöer i Azure DevOps CI/CD-pipeliner
Du kan använda tillägget Azure DevTest Labs uppgifter som är installerat i Azure DevOps Services (tidigare kallat Visual Studio Team Services) för att enkelt integrera en pipeline för kontinuerlig integrering (CI)/kontinuerlig leverans (CD) build-och-release med Azure DevTest Labs. De här tilläggen gör det enklare att snabbt distribuera en [miljö](devtest-lab-test-env.md) för en speciell test aktivitet och sedan ta bort den när testet är klart. 

Den här artikeln visar hur du skapar och distribuerar en miljö och sedan tar bort miljön, allt i en fullständig pipeline. Du utför vanligt vis var och en av dessa uppgifter individuellt i din egen anpassade version av build-test-Deploy. Tilläggen som används i den här artikeln är förutom de här [Skapa/ta bort DTL VM-aktiviteterna](devtest-lab-integrate-ci-cd.md):

- Skapa en miljö
- Ta bort en miljö

## <a name="before-you-begin"></a>Innan du börjar
Innan du kan integrera din CI/CD-pipeline med Azure DevTest Labs installerar du [Azure DevTest Labs tasks](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks) -tillägg från Visual Studio Marketplace. 

## <a name="create-and-configure-the-lab-for-environments"></a>Skapa och konfigurera labbet för miljöer
I det här avsnittet beskrivs hur du skapar och konfigurerar ett labb där Azure-miljön ska distribueras till.

1. [Skapa ett labb](devtest-lab-create-lab.md) om du inte redan har ett. 
2. Konfigurera labbet och skapa en miljö mall genom att följa anvisningarna i den här artikeln: [skapa miljöer med flera virtuella datorer och PaaS-resurser med Azure Resource Manager mallar](devtest-lab-create-environment-from-arm.md).
3. I det här exemplet använder du en befintlig Azure snabb starts mall [https://azure.microsoft.com/resources/templates/201-web-app-redis-cache-sql-database/](https://azure.microsoft.com/resources/templates/201-web-app-redis-cache-sql-database/) .
4. Kopiera mappen **201-Web-App-Redis-cache-SQL-Database** till mappen **ArmTemplate** i den databas som konfigurerades i steg 2.

## <a name="create-a-release-definition"></a>Skapa en versionsdefinition
Skapa versions definitionen genom att göra följande:

1.  På fliken **versioner** i **build & release Hub** väljer du knappen **plus tecken (+)** .
2.  I fönstret **skapa versions definition** väljer du den **tomma** mallen och väljer sedan **Nästa**.
3.  Välj **Välj senare** och välj sedan **skapa** för att skapa en ny versions definition med en standard miljö och inga länkade artefakter.
4.  Öppna snabb menyn genom att välja **ellipsen (...)** bredvid miljö namnet i den nya versions definitionen och välj sedan **Konfigurera variabler**.
5.  I fönstret **Konfigurera-miljö** anger du följande värden för de variabler som du använder i versions definitions uppgifterna:
1.  För **administratorLogin** anger du inloggnings namnet för SQL-administratören.
2.  För **administratorLoginPassword** anger du det lösen ord som ska användas av SQL-administratörens inloggning. Använd "hänglås"-ikonen för att dölja och skydda lösen ordet.
3.  För **databasename** anger du SQL Database namn.
4.  Dessa variabler är speciella för exempel miljöerna, olika miljöer kan ha olika variabler.

## <a name="create-an-environment"></a>Skapa en miljö
Nästa steg i distributionen är att skapa miljön som ska användas i utvecklings-eller testnings syfte.

1. I versions definitionen väljer du **Lägg till aktiviteter**.
2. Lägg till en Azure DevTest Labs Skapa miljö aktivitet på fliken **aktiviteter** . Konfigurera uppgiften enligt följande:
    1. För **Azure RM-prenumeration** väljer du en anslutning i listan **tillgängliga Azure Service-anslutningar** eller skapar en mer begränsad behörighets anslutning till din Azure-prenumeration. Mer information finns i [Azure Resource Manager tjänstens slut punkt](/azure/devops/pipelines/library/service-endpoints).
2. För **labb namn** väljer du namnet på den instans som du skapade tidigare *.
3. För **namn på databas** väljer du den lagrings plats där Resource Manager-mallen (201) har flyttats till *.
4. För **mallnamn** väljer du namnet på den miljö som du sparade i din käll kods lagrings plats *. 
5. **Labb namnet**, **databas namnet** och **mallnamnet** är de egna representationerna av Azures resurs-ID. Om du anger det egna namnet manuellt kan det orsaka fel, Använd List rutorna för att välja informationen.
6. För **miljö namn** anger du ett namn som identifierar miljö instansen unikt i labbet.  Det måste vara unikt inom labbet.
7. **Parameter filen** och **parametrarna** tillåter att anpassade parametrar skickas till miljön. Båda kan användas för att ange parameter värden. I det här exemplet används avsnittet parametrar. Använd namnen på de variabler som du har definierat i miljön, till exempel: `-administratorLogin "$(administratorLogin)" -administratorLoginPassword "$(administratorLoginPassword)" -databaseName "$(databaseName)" -cacheSKUCapacity 1`
8. Information i miljö mal len kan skickas genom i avsnittet utdata i mallen. Markera **skapa utdata-variabler baserat på miljö mal len utdata** så att andra aktiviteter kan använda dessa data. `$(Reference name.Output Name)` är mönstret som ska följas. Om referens namnet exempelvis var DTL och utdatafilen i mallen var variabeln `$(DTL.location)` .

## <a name="delete-the-environment"></a>Ta bort miljön
Det sista steget är att ta bort den miljö som du har distribuerat i Azure DevTest Labs-instansen. Du skulle vanligt vis ta bort miljön när du har kört dev-aktiviteterna eller kört de tester som du behöver på de distribuerade resurserna.

I versions definitionen väljer du **Lägg till aktiviteter** och lägger sedan till aktiviteten **Azure DevTest Labs ta bort miljö** på fliken **distribuera** . Konfigurera den på följande sätt:

1. Information om hur du tar bort den virtuella datorn finns [Azure DevTest Labs uppgifter](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks):
    1. För **Azure RM-prenumeration** väljer du en anslutning i listan **tillgängliga Azure Service-anslutningar** eller skapar en mer begränsad behörighets anslutning till din Azure-prenumeration. Mer information finns i [Azure Resource Manager tjänstens slut punkt](/azure/devops/pipelines/library/service-endpoints).
    2. För **labb namn** väljer du det labb där miljön finns.
    3. För **miljö namn** anger du namnet på den miljö som ska tas bort.
2. Ange ett namn för versions definitionen och spara det.

## <a name="next-steps"></a>Nästa steg
Se följande artiklar: 
- [Skapa miljöer med flera virtuella datorer med Resource Manager-mallar](devtest-lab-create-environment-from-arm.md).
- Snabb start Resource Manager-mallar för DevTest Labs automation från [DevTest Labs GitHub-lagringsplatsen](https://github.com/Azure/azure-quickstart-templates).
- [Sidan fel sökning av VSTS](/azure/devops/pipelines/troubleshooting)

