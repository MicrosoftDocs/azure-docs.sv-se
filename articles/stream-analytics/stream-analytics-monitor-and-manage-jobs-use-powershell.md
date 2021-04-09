---
title: Övervaka och hantera Azure Stream Analytics jobb med PowerShell
description: Den här artikeln beskriver hur du använder Azure PowerShell och cmdlets för att övervaka och hantera Azure Stream Analytics-jobb.
author: jseb225
ms.author: jeanb
ms.service: stream-analytics
ms.topic: how-to
ms.date: 03/28/2017
ms.openlocfilehash: 6b404516c513dea0888974ffb4fa3d8d43db6c44
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98015206"
---
# <a name="monitor-and-manage-stream-analytics-jobs-with-azure-powershell-cmdlets"></a>Övervaka och hantera Stream Analytics jobb med Azure PowerShell-cmdletar
Lär dig hur du övervakar och hanterar Stream Analytics-resurser med Azure PowerShell-cmdletar och PowerShell-skript som kör grundläggande Stream Analytics uppgifter.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites-for-running-azure-powershell-cmdlets-for-stream-analytics"></a>Krav för att köra Azure PowerShell-cmdlet: ar för Stream Analytics
* Skapa en Azure-resurs grupp i din prenumeration. Följande är ett exempel på ett Azure PowerShell-skript. Azure PowerShell information finns i [Installera och konfigurera Azure PowerShell](/powershell/azure/);  

Azure PowerShell 0.9.8:  

```powershell
# Log in to your Azure account
Add-AzureAccount
# Select the Azure subscription you want to use to create the resource group if you have more han one subscription on your account.
Select-AzureSubscription -SubscriptionName <subscription name>
# If Stream Analytics has not been registered to the subscription, remove remark symbol below (#)to run the Register-AzureProvider cmdlet to register the provider namespace.
#Register-AzureProvider -Force -ProviderNamespace 'Microsoft.StreamAnalytics'
# Create an Azure resource group
New-AzureResourceGroup -Name <YOUR RESOURCE GROUP NAME> -Location <LOCATION>
```

Azure PowerShell 1,0:

```powershell
# Log in to your Azure account
Connect-AzAccount
# Select the Azure subscription you want to use to create the resource group.
Get-AzSubscription -SubscriptionName "your sub" | Select-AzSubscription
# If Stream Analytics has not been registered to the subscription, remove remark symbol below (#)to run the Register-AzureProvider cmdlet to register the provider namespace.
#Register-AzResourceProvider -Force -ProviderNamespace 'Microsoft.StreamAnalytics'
# Create an Azure resource group
New-AzResourceGroup -Name <YOUR RESOURCE GROUP NAME> -Location <LOCATION>
```


> [!NOTE]
> Stream Analytics jobb som skapats program mässigt har inte övervakning aktiverat som standard.  Du kan aktivera övervakning manuellt i Azure Portal genom att gå till jobbets övervaknings sida och klicka på knappen Aktivera eller så kan du göra detta program mässigt genom att följa stegen i [Azure Stream Analytics-Monitor Stream Analytics jobb program mässigt](stream-analytics-monitor-jobs.md).
> 
> 

## <a name="azure-powershell-cmdlets-for-stream-analytics"></a>Azure PowerShell-cmdletar för Stream Analytics
Följande Azure PowerShell-cmdletar kan användas för att övervaka och hantera Azure Stream Analytics-jobb. Observera att Azure PowerShell har olika versioner. 
**I exemplen som visas i det första kommandot är Azure PowerShell 0.9.8, det andra kommandot är för Azure PowerShell 1,0.** Azure PowerShell 1,0-kommandon kommer alltid att ha "AZ" i kommandot.

### <a name="get-azurestreamanalyticsjob--get-azstreamanalyticsjob"></a>Get-AzureStreamAnalyticsJob | Get-AzStreamAnalyticsJob
Visar en lista över alla Stream Analytics jobb som definierats i Azure-prenumerationen eller en angiven resurs grupp, eller hämtar jobb information om ett visst jobb inom en resurs grupp.

**Exempel 1**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsJob
```

Azure PowerShell 1,0:  

```powershell
Get-AzStreamAnalyticsJob
```

Det här PowerShell-kommandot returnerar information om alla Stream Analytics jobb i Azure-prenumerationen.

**Exempel 2**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US 
```

Azure PowerShell 1,0:  

```powershell
Get-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US 
```

Det här PowerShell-kommandot returnerar information om alla Stream Analytics jobb i resurs gruppen StreamAnalytics-default-Central-US.

**Exempel 3**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob
```

Azure PowerShell 1,0:  

```powershell
Get-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob
```

Det här PowerShell-kommandot returnerar information om Stream Analytics Job StreamingJob i resurs gruppen StreamAnalytics-default-Central-US.

### <a name="get-azurestreamanalyticsinput--get-azstreamanalyticsinput"></a>Get-AzureStreamAnalyticsInput | Get-AzStreamAnalyticsInput
Visar alla indata som har definierats i ett angivet Stream Analytics jobb eller hämtar information om en specifik inmatning.

**Exempel 1**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob
```

Azure PowerShell 1,0:  

```powershell
Get-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob
```

Det här PowerShell-kommandot returnerar information om alla indata som definierats i jobbets StreamingJob.

**Exempel 2**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name EntryStream
```

Azure PowerShell 1,0:  

```powershell
Get-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name EntryStream
```

Det här PowerShell-kommandot returnerar information om den indata med namnet EntryStream som definierats i jobbets StreamingJob.

### <a name="get-azurestreamanalyticsoutput--get-azstreamanalyticsoutput"></a>Get-AzureStreamAnalyticsOutput | Get-AzStreamAnalyticsOutput
Visar en lista över alla utdata som har definierats i ett angivet Stream Analytics jobb eller hämtar information om en specifik utmatning.

**Exempel 1**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob
```

Azure PowerShell 1,0:  

```powershell
Get-AzStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob
```

Det här PowerShell-kommandot returnerar information om de utdata som definierats i jobbets StreamingJob.

**Exempel 2**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name Output
```

Azure PowerShell 1,0:  

```powershell
Get-AzStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name Output
```

Det här PowerShell-kommandot returnerar information om utdata med namnet utdata som definierats i jobbets StreamingJob.

### <a name="get-azurestreamanalyticsquota--get-azstreamanalyticsquota"></a>Get-AzureStreamAnalyticsQuota | Get-AzStreamAnalyticsQuota
Hämtar information om kvoten för enheter för strömning i en angiven region.

**Exempel 1**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsQuota -Location "Central US" 
```

Azure PowerShell 1,0:  

```powershell
Get-AzStreamAnalyticsQuota -Location "Central US" 
```

Det här PowerShell-kommandot returnerar information om kvoten och användningen av enheter för strömning i regionen USA, centrala.

### <a name="get-azurestreamanalyticstransformation--get-azstreamanalyticstransformation"></a>Get-AzureStreamAnalyticsTransformation | Get-AzStreamAnalyticsTransformation
Hämtar information om en viss omvandling som definierats i ett Stream Analytics jobb.

**Exempel 1**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name StreamingJob
```

Azure PowerShell 1,0:  

```powershell
Get-AzStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name StreamingJob
```

Det här PowerShell-kommandot returnerar information om omvandlingen som heter StreamingJob i jobb-StreamingJob.

### <a name="new-azurestreamanalyticsinput--new-azstreamanalyticsinput"></a>New-AzureStreamAnalyticsInput | New-AzStreamAnalyticsInput
Skapar ett nytt inflöde i ett Stream Analytics jobb eller uppdaterar en befintlig angiven Indatatyp.

Namnet på indatan kan anges i. JSON-filen eller på kommando raden. Om båda anges måste namnet på kommando raden vara samma som det i filen.

Om du anger en inmatare som redan finns och inte anger parametern-Force frågar cmdleten om du vill ersätta den befintliga indatamängden.

Om du anger parametern-Force och anger ett befintligt indatamängds namn ersätts indatamängden utan bekräftelse.

Detaljerad information om JSON-filens struktur och innehåll finns i avsnittet [skapa indata (Azure Stream Analytics)][msdn-rest-api-create-stream-analytics-input] i [referens biblioteket för Stream Analytics hanterings REST API][stream.analytics.rest.api.reference].

**Exempel 1**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -File "C:\Input.json" 
```

Azure PowerShell 1,0:  

```powershell
New-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -File "C:\Input.json" 
```

Det här PowerShell-kommandot skapar en ny indata från filen Input.jspå. Om det redan finns en befintlig indata med det namn som angetts i definitions filen för indata, kommer cmdleten att be om att ersätta den.

**Exempel 2**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -File "C:\Input.json" -Name EntryStream
```

Azure PowerShell 1,0:  

```powershell
New-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -File "C:\Input.json" -Name EntryStream
```

Detta PowerShell-kommando skapar en ny inmatare i jobbet med namnet EntryStream. Om en befintlig indatamängd med det här namnet redan har definierats, kommer cmdleten att be om att ersätta den.

**Exempel 3**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -File "C:\Input.json" -Name EntryStream -Force
```

Azure PowerShell 1,0:  

```powershell
New-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -File "C:\Input.json" -Name EntryStream -Force
```

Detta PowerShell-kommando ersätter definitionen av den befintliga Indatakällan med namnet EntryStream med definitionen från filen.

### <a name="new-azurestreamanalyticsjob--new-azstreamanalyticsjob"></a>New-AzureStreamAnalyticsJob | New-AzStreamAnalyticsJob
Skapar ett nytt Stream Analytics jobb i Microsoft Azure eller uppdaterar definitionen för ett befintligt angivet jobb.

Namnet på jobbet kan anges i. JSON-filen eller på kommando raden. Om båda anges måste namnet på kommando raden vara samma som det i filen.

Om du anger ett jobbnamn som redan finns och inte anger parametern-Force frågar cmdleten om det befintliga jobbet ska ersättas eller inte.

Om du anger parametern-Force och anger ett befintligt jobb namn ersätts jobb definitionen utan bekräftelse.

Detaljerad information om JSON-filens struktur och innehåll finns i avsnittet [skapa Stream Analytics jobb][msdn-rest-api-create-stream-analytics-job] i [referens biblioteket för Stream Analytics hanterings REST API][stream.analytics.rest.api.reference].

**Exempel 1**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\JobDefinition.json" 
```

Azure PowerShell 1,0:  

```powershell
New-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\JobDefinition.json" 
```

Det här PowerShell-kommandot skapar ett nytt jobb från definitionen i JobDefinition.jspå. Om ett befintligt jobb med det namn som anges i jobb definitions filen redan har definierats, kommer cmdleten att be om att ersätta den.

**Exempel 2**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\JobDefinition.json" -Name StreamingJob -Force
```

Azure PowerShell 1,0:  

```powershell
New-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\JobDefinition.json" -Name StreamingJob -Force
```

Det här PowerShell-kommandot ersätter jobb definitionen för StreamingJob.

### <a name="new-azurestreamanalyticsoutput--new-azstreamanalyticsoutput"></a>New-AzureStreamAnalyticsOutput | New-AzStreamAnalyticsOutput
Skapar ett nytt utdata i ett Stream Analytics jobb eller uppdaterar en befintlig utmatning.  

Namnet på utdata kan anges i. JSON-filen eller på kommando raden. Om båda anges måste namnet på kommando raden vara samma som det i filen.

Om du anger en utmatning som redan finns och inte anger parametern-Force frågar cmdleten om den befintliga utdatan ska ersättas eller inte.

Om du anger parametern-Force och anger ett befintligt utmatnings namn ersätts utdata utan bekräftelse.

Detaljerad information om JSON-filens struktur och innehåll finns i avsnittet [skapa utdata (Azure Stream Analytics)][msdn-rest-api-create-stream-analytics-output] i [referens biblioteket för Stream Analytics hanterings REST API][stream.analytics.rest.api.reference].

**Exempel 1**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\Output.json" -JobName StreamingJob -Name output
```

Azure PowerShell 1,0:  

```powershell
New-AzStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\Output.json" -JobName StreamingJob -Name output
```

Detta PowerShell-kommando skapar nya utdata som kallas "utdata" i jobbets StreamingJob. Om en befintlig utmatning med det här namnet redan har definierats, kommer cmdleten att fråga om den ska ersättas eller inte.

**Exempel 2**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\Output.json" -JobName StreamingJob -Name output -Force
```

Azure PowerShell 1,0:  

```powershell
New-AzStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\Output.json" -JobName StreamingJob -Name output -Force
```

Det här PowerShell-kommandot ersätter definitionen för "output" i jobbets StreamingJob.

### <a name="new-azurestreamanalyticstransformation--new-azstreamanalyticstransformation"></a>New-AzureStreamAnalyticsTransformation | New-AzStreamAnalyticsTransformation
Skapar en ny omvandling i ett Stream Analytics jobb eller uppdaterar den befintliga omvandlingen.

Namnet på omvandlingen kan anges i. JSON-filen eller på kommando raden. Om båda anges måste namnet på kommando raden vara samma som det i filen.

Om du anger en omvandling som redan finns och inte anger parametern-Force frågar cmdleten om den befintliga omvandlingen ska ersättas eller inte.

Om du anger parametern-Force och anger ett befintligt omvandlings namn ersätts omvandlingen utan bekräftelse.

Detaljerad information om JSON-filens struktur och innehåll finns i avsnittet om att [skapa transformering (Azure Stream Analytics)][msdn-rest-api-create-stream-analytics-transformation] i [referens biblioteket för Stream Analytics hanterings REST API][stream.analytics.rest.api.reference].

**Exempel 1**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\Transformation.json" -JobName StreamingJob -Name StreamingJobTransform
```

Azure PowerShell 1,0:  

```powershell
New-AzStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\Transformation.json" -JobName StreamingJob -Name StreamingJobTransform
```

Detta PowerShell-kommando skapar en ny omvandling med namnet StreamingJobTransform i jobbets StreamingJob. Om en befintlig omvandling redan har definierats med det här namnet kommer cmdleten att fråga om den ska ersättas eller inte.

**Exempel 2**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\Transformation.json" -JobName StreamingJob -Name StreamingJobTransform -Force
```

Azure PowerShell 1,0:  

```powershell
New-AzStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\Transformation.json" -JobName StreamingJob -Name StreamingJobTransform -Force
```

 Detta PowerShell-kommando ersätter definitionen av StreamingJobTransform i jobb-StreamingJob.

### <a name="remove-azurestreamanalyticsinput--remove-azstreamanalyticsinput"></a>Remove-AzureStreamAnalyticsInput | Remove-AzStreamAnalyticsInput
Tar bort en angiven Indatatyp asynkront från ett Stream Analytics jobb i Microsoft Azure.  
Om du anger parametern-Force tas inmatarna bort utan bekräftelse.

**Exempel 1**

Azure PowerShell 0.9.8:  

```powershell
Remove-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name EventStream
```

Azure PowerShell 1,0:  

```powershell
Remove-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name EventStream
```

Detta PowerShell-kommando tar bort EventStream i jobbet StreamingJob.  

### <a name="remove-azurestreamanalyticsjob--remove-azstreamanalyticsjob"></a>Remove-AzureStreamAnalyticsJob | Remove-AzStreamAnalyticsJob
Tar bort ett särskilt Stream Analytics jobb asynkront i Microsoft Azure.  
Om du anger parametern-Force tas jobbet bort utan bekräftelse.

**Exempel 1**

Azure PowerShell 0.9.8:  

```powershell
Remove-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob 
```

Azure PowerShell 1,0:  

```powershell
Remove-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob 
```

Detta PowerShell-kommando tar bort jobbets StreamingJob.  

### <a name="remove-azurestreamanalyticsoutput--remove-azstreamanalyticsoutput"></a>Remove-AzureStreamAnalyticsOutput | Remove-AzStreamAnalyticsOutput
Tar bort ett särskilt utdata asynkront från ett Stream Analytics jobb i Microsoft Azure.  
Om du anger parametern-Force tas utdata bort utan bekräftelse.

**Exempel 1**

Azure PowerShell 0.9.8:  

```powershell
Remove-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name Output
```

Azure PowerShell 1,0:  

```powershell
Remove-AzStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name Output
```

Detta PowerShell-kommando tar bort utdata från jobbets StreamingJob.  

### <a name="start-azurestreamanalyticsjob--start-azstreamanalyticsjob"></a>Start-AzureStreamAnalyticsJob | Start-AzStreamAnalyticsJob
Distribuerar och startar ett Stream Analytics jobb asynkront i Microsoft Azure.

**Exempel 1**

Azure PowerShell 0.9.8:  

```powershell
Start-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob -OutputStartMode CustomTime -OutputStartTime 2012-12-12T12:12:12Z
```

Azure PowerShell 1,0:  

```powershell
Start-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob -OutputStartMode CustomTime -OutputStartTime 2012-12-12T12:12:12Z
```

Det här PowerShell-kommandot startar jobbet StreamingJob med en anpassad start tid för utdata angivet till 12 december 2012 12:12:12 UTC.

### <a name="stop-azurestreamanalyticsjob--stop-azstreamanalyticsjob"></a>Stop-AzureStreamAnalyticsJob | Stop-AzStreamAnalyticsJob
Stoppar asynkront ett Stream Analytics jobb från att köras i Microsoft Azure och allokerar de resurser som användes. Jobb definitionen och metadata är fortfarande tillgängliga i din prenumeration via både API: erna för Azure Portal och hantering, så att jobbet kan redige ras och startas om. Du debiteras inte för ett jobb i stoppat tillstånd.

**Exempel 1**

Azure PowerShell 0.9.8:  

```powershell
Stop-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob 
```

Azure PowerShell 1,0:  

```powershell
Stop-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob 
```

Detta PowerShell-kommando stoppar jobbets StreamingJob.  

### <a name="test-azurestreamanalyticsinput--test-azstreamanalyticsinput"></a>Test-AzureStreamAnalyticsInput | Test-AzStreamAnalyticsInput
Testar möjligheten för Stream Analytics att ansluta till en angiven indatamängd.

**Exempel 1**

Azure PowerShell 0.9.8:  

```powershell
Test-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name EntryStream
```

Azure PowerShell 1,0:  

```powershell
Test-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name EntryStream
```

Det här PowerShell-kommandot testar anslutnings status för EntryStream för indatamängden i StreamingJob.  

### <a name="test-azurestreamanalyticsoutput--test-azstreamanalyticsoutput"></a>Test-AzureStreamAnalyticsOutput | Test-AzStreamAnalyticsOutput
Testar möjligheten för Stream Analytics att ansluta till en angiven utmatning.

**Exempel 1**

Azure PowerShell 0.9.8:  

```powershell
Test-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name Output
```

Azure PowerShell 1,0:  

```powershell
Test-AzStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name Output
```

Det här PowerShell-kommandot testar anslutnings status för utdata i StreamingJob.  

## <a name="get-support"></a>Få support
Om du behöver ytterligare hjälp kan du prova vår [Microsoft Q&en fråge sida för Azure Stream Analytics](/answers/topics/azure-stream-analytics.html). 

## <a name="next-steps"></a>Nästa steg
* [Introduktion till Azure Stream Analytics](stream-analytics-introduction.md)
* [Komma igång med Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Skala Azure Stream Analytics-jobb](stream-analytics-scale-jobs.md)
* [Referens för Azure Stream Analytics-frågespråket](/stream-analytics-query/stream-analytics-query-language-reference)
* [Referens för Azure Stream Analytics Management REST-API:et](/rest/api/streamanalytics/)

[msdn-switch-azuremode]: /previous-versions/azure/dn722470(v=azure.100)
[powershell-install]: /powershell/azure/
[msdn-rest-api-create-stream-analytics-job]: ./stream-analytics-quick-create-portal.md
[msdn-rest-api-create-stream-analytics-input]: ./stream-analytics-define-inputs.md
[msdn-rest-api-create-stream-analytics-output]: ./stream-analytics-define-outputs.md
[msdn-rest-api-create-stream-analytics-transformation]: /cli/azure/ext/stream-analytics/stream-analytics/transformation

[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.query.language.reference]: /stream-analytics-query/stream-analytics-query-language-reference
[stream.analytics.rest.api.reference]: /rest/api/streamanalytics/