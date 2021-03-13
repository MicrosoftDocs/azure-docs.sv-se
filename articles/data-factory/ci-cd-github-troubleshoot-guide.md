---
title: Felsök problem med CI-CD, Azure DevOps och GitHub i ADF
description: Använd olika metoder för att felsöka problem med CI-CD i ADF.
author: ssabat
ms.author: susabat
ms.reviewer: susabat
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 03/12/2021
ms.openlocfilehash: 4be015b1a8ba4b6fc6ea3acc74318f9a8b298e8e
ms.sourcegitcommit: df1930c9fa3d8f6592f812c42ec611043e817b3b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/13/2021
ms.locfileid: "103418104"
---
# <a name="troubleshoot-ci-cd-azure-devops-and-github-issues-in-adf"></a>Felsök problem med CI-CD, Azure DevOps och GitHub i ADF 

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Den här artikeln visar vanliga fel söknings metoder för kontinuerlig Integration-Continuous distribution (CI-CD), Azure-DevOps och GitHub-problem i Azure Data Factory.

Om du har frågor eller problem med att använda käll kontroll eller DevOps-tekniker finns det några artiklar som kan vara användbara:

- Se [käll kontroll i ADF](source-control.md) för att lära dig hur käll kontroll utförs i ADF. 
- Se  [CI-CD i ADF](continuous-integration-deployment.md) för att lära dig mer om hur DevOps CI-CD är förbrukad i ADF.
 
## <a name="common-errors-and-messages"></a>Vanliga fel och meddelanden

### <a name="connect-to-git-repository-failed-due-to-different-tenant"></a>Det gick inte att ansluta till git-lagringsplatsen på grund av en annan klient

#### <a name="issue"></a>Problem
    
Ibland uppstår autentiserings problem som HTTP-status 401. I synnerhet när du har flera klienter med gäst kontot kan saker bli mer komplicerade.

#### <a name="cause"></a>Orsak

Det vi har observerat är att token hämtades från den ursprungliga klienten, men ADF är i gäst klienten och försöker använda token för att besöka DevOps i gäst klienten. Detta är inte det förväntade beteendet.

#### <a name="recommendation"></a>Rekommendation

Du bör använda token som utfärdats från gäst klienten i stället. Till exempel måste du tilldela samma Azure Active Directory som gäst-och DevOps, så att den kan ange token-beteendet korrekt och använda rätt klient organisation.

### <a name="template-parameters-in-the-parameters-file-are-not-valid"></a>Mallparametrar i parameter filen är inte giltiga

#### <a name="issue"></a>Problem

Om vi tar bort en utlösare i dev Branch, som redan är tillgänglig i test-eller produktions grenen med **samma** konfiguration (t. ex. frekvens och intervall), så slutförs distributionen av pipeline och motsvarande utlösare tas bort i respektive miljö. Men om du har en **annan** konfiguration (t. ex. frekvens och intervall) för utlösare i test-/produktions miljöer och om du tar bort samma utlösare i dev, Miss lyckas distributionen med ett fel.

#### <a name="cause"></a>Orsak

CI/CD-pipelinen Miss lyckas med följande fel:

`
2020-07-20T11:19:02.1276769Z ##[error]Deployment template validation failed: 'The template parameters 'Trigger_Salesforce_properties_typeProperties_recurrence_frequency, Trigger_Salesforce_properties_typeProperties_recurrence_interval, Trigger_Salesforce_properties_typeProperties_recurrence_startTime, Trigger_Salesforce_properties_typeProperties_recurrence_timeZone' in the parameters file are not valid; they are not present in the original template and can therefore not be provided at deployment time. The only supported parameters for this template are 'factoryName, PlanonDWH_connectionString, PlanonKeyVault_properties_typeProperties_baseUrl
`

#### <a name="recommendation"></a>Rekommendation

Felet beror på att vi ofta tar bort en utlösare, som är parameterstyrda, och därför är parametrarna inte tillgängliga i ARM-mallen (eftersom utlösaren inte finns längre). Eftersom parametern inte finns i ARM-mallen längre, måste vi uppdatera de åsidosatta parametrarna i DevOps-pipeline. Annars måste alla parametrar i ARM-mallen ändras, så att de uppdaterar åsidosatta parametrar i DevOps-pipeline (i distributions aktiviteten).

### <a name="updating-property-type-is-not-supported"></a>Uppdatering av egenskaps typ stöds inte

#### <a name="issue"></a>Problem

CI/CD versions pipelinen fungerar inte med följande fel:

`
2020-07-06T09:50:50.8716614Z There were errors in your deployment. Error code: DeploymentFailed.
2020-07-06T09:50:50.8760242Z ##[error]At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/DeployOperations for usage details.
2020-07-06T09:50:50.8771655Z ##[error]Details:
2020-07-06T09:50:50.8772837Z ##[error]DataFactoryPropertyUpdateNotSupported: Updating property type is not supported.
2020-07-06T09:50:50.8774148Z ##[error]DataFactoryPropertyUpdateNotSupported: Updating property type is not supported.
2020-07-06T09:50:50.8775530Z ##[error]Check out the troubleshooting guide to see if your issue is addressed: https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-resource-group-deployment#troubleshooting
2020-07-06T09:50:50.8776801Z ##[error]Task failed while creating or updating the template deployment.
`

#### <a name="cause"></a>Orsak

Detta beror på en integration runtime med samma namn i mål fabriken men med en annan typ. Integration Runtime måste vara av samma typ vid distribution.

#### <a name="recommendation"></a>Rekommendation

- Läs följande metod tips för CI/CD nedan:

    https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment#best-practices-for-cicd 
- Integrerings körningar ändras inte ofta och liknar varandra i alla steg i CI/CD, så Data Factory förväntar dig att du har samma namn och typ av integration runtime i alla stadier av CI/CD. Om namn och typer & egenskaper skiljer sig åt, måste du matcha konfigurationen för källans och målets konfigurations körning och sedan distribuera pipelinen för version.
- Om du vill dela integrerings körningar i alla faser bör du överväga att använda en ternär fabrik som bara innehåller de delade integrerings körningarna. Du kan använda den här delade fabriken i alla dina miljöer som en länkad integration runtime-typ.

### <a name="document-creation-or-update-failed-because-of-invalid-reference"></a>Det gick inte att skapa eller uppdatera dokument på grund av Ogiltig referens

#### <a name="issue"></a>Problem

När du försöker publicera ändringar till en Data Factory visas följande fel meddelande:

`
"error": {
        "code": "BadRequest",
        "message": "The document creation or update failed because of invalid reference '<entity>'.",
        "target": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/<rgname>/providers/Microsoft.DataFactory/factories/<datafactory>/pipelines/<pipeline>",
        "details": null
    }
`

#### <a name="symptom"></a>Symptom

Du har frånkopplat git-konfigurationen och konfigurerat den igen med flaggan "Importera resurser" markerad, vilket anger Data Factory som "synkroniserat". Det innebär att inga ändringar har publicerats.

#### <a name="resolution"></a>Lösning

Koppla från git-konfigurationen och konfigurera den igen, och se till att du inte markerar kryss rutan importera befintliga resurser.

### <a name="data-factory-move-failing-from-one-resource-group-to-another"></a>Det går inte att flytta Data Factory från en resurs grupp till en annan

#### <a name="issue"></a>Problem

Du kan inte flytta Data Factory från en resurs grupp till en annan, som inte har följande fel:

`
{
    "code": "ResourceMoveProviderValidationFailed",
    "message": "Resource move validation failed. Please see details. Diagnostic information: timestamp 'xxxxxxxxxxxxZ', subscription id 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx', tracking id 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx', request correlation id 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'.",
    "details": [
        {
            "code": "BadRequest",
            "target": "Microsoft.DataFactory/factories",
            "message": "One of the resources contain integration runtimes that are either SSIS-IRs in starting/started/stopping state, or Self-Hosted IRs which are shared with other resources. Resource move is not supported for those resources."
        }
    ]
}
`

#### <a name="resolution"></a>Lösning

Du måste ta bort SSIS-IR och Shared IRs för att tillåta flytt åtgärden. Om du inte vill ta bort integrerings körningarna, är det bästa sättet att följa dokumentet kopiera och klona för att kopiera och när det är färdigt, ta bort den gamla Data Factory.

###  <a name="unable-to-export-and-import-arm-template"></a>Det går inte att exportera och importera ARM-mall

#### <a name="issue"></a>Problem

Det går inte att exportera och importera ARM-mall. Inget fel inträffade på portalen, men i webb läsar spårningen kan du se följande fel:

`Failed to load resource: the server responded with a status code of 401 (Unauthorized)`

#### <a name="cause"></a>Orsak

Du har skapat en kund roll som användare och den har inte den behörighet som krävs. När fabriken läses in i användar gränssnittet kontrol leras en serie exponerings kontroll värden för fabriken. I det här fallet har användarens åtkomst roll inte behörighet att komma åt *queryFeaturesValue* -API: et. Funktionen globala parametrar är inaktive rad för att få åtkomst till detta API. ARM-exportens kod Sök väg förlitar sig delvis på funktionen globala parametrar.

#### <a name="resolution"></a>Lösning

För att lösa problemet måste du lägga till följande behörighet i rollen: *Microsoft. DataFactory/factors/queryFeaturesValue/Action*. Den här behörigheten ska inkluderas som standard i rollen Data Factory deltagare.

###  <a name="automatic-publishing-for-cicd-without-clicking-publish-button"></a>Automatisk publicering för CI/CD utan att klicka på knappen publicera  

#### <a name="issue"></a>Problem

Manuell publicering med knapp klickning i ADF-portalen aktiverar inte automatisk CI/CD-åtgärd.

#### <a name="cause"></a>Orsak

Tills det nyligen har du klickat på knappen för att publicera ADF-pipeline för distributioner. Nu kan du göra processen till automatisk. 

#### <a name="resolution"></a>Lösning

CI/CD-processen har förbättrats. Funktionen **automatiserad publicering** tar, validerar och exporterar alla mallar för Azure Resource Manager (arm) från ADF-UX. Det gör det möjligt att använda logiken via ett offentligt tillgängligt NPM-paket [@microsoft/azure-data-factory-utilities](https://www.npmjs.com/package/@microsoft/azure-data-factory-utilities) . På så sätt kan du programmatiskt utlösa dessa åtgärder i stället för att behöva gå till ADF-ANVÄNDARGRÄNSSNITTET och göra en knapp klickning. Detta ger din CI/CD-pipeline en **verklig** kontinuerlig integrerings upplevelse. Följ [förbättringarna i ADF CI/CD-publicering](./continuous-integration-deployment-improvements.md) för mer information. 

###  <a name="cannot-publish-because-of-4mb-arm-template-limit"></a>Det går inte att publicera på grund av 4 MB ARM-mall  

#### <a name="issue"></a>Problem

Det går inte att distribuera eftersom du träffar Azure Resource Manager gränsen på 4 MB Total storlek. Du behöver en lösning för att distribuera när gränsen har passerats. 

#### <a name="cause"></a>Orsak

Azure Resource Manager begränsar storleken på mallen till 4 MB. Begränsa storleken på din mall till 4 MB och varje parameter fil till 64 KB. Gränsen på 4 MB gäller för mallens slutliga tillstånd när den har utökats med iterativa resurs definitioner och värden för variabler och parametrar. Men du har överskridit gränsen. 

#### <a name="resolution"></a>Lösning

För små till medelstora lösningar är en enskild mall enklare att förstå och underhålla. Du kan se alla resurser och värden i en enda fil. I avancerade scenarier kan du använda länkade mallar till att dela upp lösningen i riktade komponenter. Följ bästa praxis vid [användning av länkade och kapslade mallar](../azure-resource-manager/templates/linked-templates.md?tabs=azure-powershell).

### <a name="cannot-connect-to-git-enterprise-cloud"></a>Det går inte att ansluta till GIT Enterprise Cloud 

##### <a name="issue"></a>Problem

Du kan inte ansluta till GIT Enterprise-molnet på grund av behörighets problem. Du kan se felet som **422-en entitet som inte kan bearbetas.**

#### <a name="cause"></a>Orsak

* Du använder git Enterprise på lokal-servern. 
* Du har inte konfigurerat OAuth för ADF. 
* URL: en är felkonfigurerad.

##### <a name="resolution"></a>Lösning

Du beviljar OAuth-åtkomst till ADF först. Sedan måste du använda rätt URL för att ansluta till GIT Enterprise. Konfigurationen måste anges till kundens organisation (er). ADF kommer till exempel att försöka *https://hostname/api/v3/search/repositories?q=user%3 <customer credential> ....* vid första och misslyckad. Sedan kommer det att försöka *https://hostname/api/v3/orgs/ <org> / <repo> ...* och lyckas. 
 
### <a name="recover-from-a-deleted-data-factory"></a>Återställa från en borttagen data fabrik

#### <a name="issue"></a>Problem
Kunden tog bort data fabriken eller resurs gruppen som innehåller Data Factory. Han vill veta hur du återställer en borttagen data fabrik.

#### <a name="cause"></a>Orsak

Det går bara att återställa Data Factory om kunden har en käll kontroll som kon figurer ATS (DevOps eller git). Detta tar all den senaste publicerade resursen och **kommer inte** att återställa den opublicerade pipelinen, data uppsättningen och den länkade tjänsten.

Om det inte finns någon käll kontroll är det inte möjligt att återställa en borttagen Data Factory från Server delen eftersom när tjänsten tar emot borttagna kommando, raderas instansen och ingen säkerhets kopia har lagrats.

#### <a name="resolution"></a>Lösning

För att återställa den borttagna Data Factory som har käll kontroll, se stegen nedan:

 * Skapa en ny Azure Data Factory.

 * Konfigurera om git med samma inställningar, men se till att importera befintliga Data Factory resurser till den valda lagrings platsen och välj ny gren.

 * Skapa en pull-begäran för att sammanfoga ändringarna i samarbets grenen och publicera.

 * Om kunden hade en egen värd Integration Runtime i en borttagen ADF, måste de skapa en ny instans i ny ADF, även avinstallera och ominstallera instansen på sin lokal dator/VM med den nya nyckeln som hämtats. När installationen av IR har slutförts måste kunden ändra den länkade tjänsten så att den pekar på ny IR och testa anslutningen, annars Miss kan den fel **referens.**



## <a name="next-steps"></a>Nästa steg

Om du behöver hjälp med fel sökning kan du prova följande resurser:

*  [Data Factory blogg](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory funktions begär Anden](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure-videor](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Stack Overflow-forum för Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Twitter-information om Data Factory](https://twitter.com/hashtag/DataFactory)
