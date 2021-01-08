---
title: Använd Azure Stream Analytics CI/CD NuGet-paketet
description: Den här artikeln beskriver hur du använder Azure Stream Analytics CI/CD NuGet-paket för att konfigurera en kontinuerlig integrering och distributions process.
author: su-jie
ms.author: sujie
ms.service: stream-analytics
ms.topic: how-to
ms.date: 05/15/2019
ms.openlocfilehash: 9fe1aaed58d6069cd583f8607f7bd6332f97674e
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2021
ms.locfileid: "98019898"
---
# <a name="use-the-azure-stream-analytics-cicd-nuget-package-for-integration-and-development"></a>Använd Azure Stream Analytics CI/CD NuGet-paketet för integrering och utveckling 
Den här artikeln beskriver hur du använder Azure Stream Analytics CI/CD NuGet-paketet för att skapa en kontinuerlig integrering och distributions process.

Använd version 2.3.0000.0 eller senare av [Stream Analytics verktyg för Visual Studio](./stream-analytics-quick-create-vs.md) för att få stöd för MSBuild.

Det finns ett NuGet-paket: [Microsoft. Azure. Stream Analytics. CICD](https://www.nuget.org/packages/Microsoft.Azure.StreamAnalytics.CICD/). Det tillhandahåller de MSBuild-, lokala körnings-och distributions verktyg som har stöd för kontinuerlig integrering och distributions processen för [Stream Analytics Visual Studio-projekt](stream-analytics-vs-tools.md). 
> [!NOTE]
> NuGet-paketet kan bara användas med 2.3.0000.0 eller över versionen av Stream Analytics Tools för Visual Studio. Om du har projekt som skapats i tidigare versioner av Visual Studio-verktyg öppnar du dem bara med 2.3.0000.0 eller över-versionen och sparar. De nya funktionerna är aktiverade. 

Mer information finns i [Stream Analytics Tools för Visual Studio](./stream-analytics-quick-create-vs.md).

## <a name="msbuild"></a>MSBuild
Precis som med standard Visual Studio MSBuild-upplevelsen kan du bygga ett projekt med två alternativ. Du kan högerklicka på projektet och välja **build**. Du kan också använda **MSBuild** i NuGet-paketet från kommando raden.
```
./build/msbuild /t:build [Your Project Full Path] /p:CompilerTaskAssemblyFile=Microsoft.WindowsAzure.StreamAnalytics.Common.CompileService.dll  /p:ASATargetsFilePath="[NuGet Package Local Path]\build\StreamAnalytics.targets"

```

När en Stream Analytics Visual Studio-projekt skapas, genererar det följande två Azure Resource Manager mallfiler under mappen **bin/[debug/återförsäljarversion]/Deploy** : 

* Resource Manager-mallfil

   `[ProjectName].JobTemplate.json`

* Parameter fil för Resource Manager
   
   `[ProjectName].JobTemplate.parameters.json`

Standard parametrarna i parameters.jsi filen är från inställningarna i Visual Studio-projektet. Om du vill distribuera till en annan miljö ersätter du parametrarna på motsvarande sätt.

> [!NOTE]
> Standardvärdena anges till null för alla autentiseringsuppgifter. Du **måste** ange värdena innan du distribuerar till molnet.

```json
"Input_EntryStream_sharedAccessPolicyKey": {
      "value": null
    },
```
Lär dig mer om hur du [distribuerar med en Resource Manager-mallfil och Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md). Lär dig mer om hur du [använder ett objekt som en parameter i en Resource Manager-mall](/azure/architecture/building-blocks/extending-templates/objects-as-parameters).

Om du vill använda hanterad identitet för Azure Data Lake Store gen1 som utgående mottagare måste du ge åtkomst till tjänstens huvud namn med hjälp av PowerShell innan du distribuerar till Azure. Lär dig mer om hur du [distribuerar ADLS gen1 med hanterad identitet med Resource Manager-mall](stream-analytics-managed-identities-adls.md#resource-manager-template-deployment).


## <a name="command-line-tool"></a>Kommando rads verktyg

### <a name="build-the-project"></a>Bygga projektet
NuGet-paketet har ett kommando rads verktyg som heter **SA.exe**. Den har stöd för projekt utveckling och lokal testning på en godtycklig dator, som du kan använda i den kontinuerliga integreringen och den kontinuerliga leverans processen. 

Distributions-filerna placeras under den aktuella katalogen som standard. Du kan ange en sökväg för utdata genom att använda följande-OutputPath-parameter:

```
./tools/SA.exe build -Project [Your Project Full Path] [-OutputPath <outputPath>] 
```

### <a name="test-the-script-locally"></a>Testa skriptet lokalt

Om ditt projekt har angett lokala indatafiler i Visual Studio kan du köra ett automatiserat skript test med hjälp av kommandot *localrun* . Resultatet av utdata placeras under den aktuella katalogen.
 
```
localrun -Project [ProjectFullPath]
```

### <a name="generate-a-job-definition-file-to-use-with-the-stream-analytics-powershell-api"></a>Generera en jobb definitions fil som ska användas med Stream Analytics PowerShell-API

*Arm* -kommandot tar den jobbmall och den mall för jobb mal len som genereras med hjälp av skapa som inmatade filer. Sedan kombineras de till en JSON-fil för jobb definition som kan användas med Stream Analytics PowerShell API.

```powershell
arm -JobTemplate <templateFilePath> -JobParameterFile <jobParameterFilePath> [-OutputFile <asaArmFilePath>]
```
Exempel:
```powershell
./tools/SA.exe arm -JobTemplate "ProjectA.JobTemplate.json" -JobParameterFile "ProjectA.JobTemplate.parameters.json" -OutputFile "JobDefinition.json" 
```



## <a name="next-steps"></a>Nästa steg

* [Snabb start: skapa ett Azure Stream Analytics moln jobb i Visual Studio](stream-analytics-quick-create-vs.md)
* [Testa Stream Analytics frågor lokalt med Visual Studio](stream-analytics-vs-tools-local-run.md)
* [Utforska Azure Stream Analytics-jobb med Visual Studio](stream-analytics-vs-tools.md)