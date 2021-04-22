---
title: Övervaka och hantera Azure Stream Analytics jobb med PowerShell
description: Den här artikeln beskriver hur du använder Azure PowerShell och cmdlets för att övervaka och hantera Azure Stream Analytics jobb.
author: jseb225
ms.author: jeanb
ms.service: stream-analytics
ms.topic: how-to
ms.date: 03/28/2017
ms.openlocfilehash: 15b2e5ef5873ea48c6c3f2c790619392f622fb66
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107870147"
---
# <a name="monitor-and-manage-stream-analytics-jobs-with-azure-powershell-cmdlets"></a>Övervaka och hantera Stream Analytics jobb med Azure PowerShell-cmdlets
Lär dig hur du övervakar och hanterar Stream Analytics resurser med Azure PowerShell-cmdlets och PowerShell-skript som kör grundläggande Stream Analytics uppgifter.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites-for-running-azure-powershell-cmdlets-for-stream-analytics"></a>Krav för att köra Azure PowerShell-cmdlets för Stream Analytics
* Skapa en Azure-resursgrupp i din prenumeration. Följande är ett exempel på Azure PowerShell skript. Mer Azure PowerShell finns i [Installera och konfigurera Azure PowerShell](/powershell/azure/).  

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

Azure PowerShell 1.0:

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
> Stream Analytics jobb som skapas programmatiskt har inte övervakning aktiverat som standard.  Du kan aktivera övervakning manuellt i Azure Portal genom att gå till sidan Övervaka för jobbet och klicka på knappen Aktivera. Du kan också göra detta programmässigt genom att följa stegen i [Azure Stream Analytics – Övervaka Stream Analytics-jobb programmässigt.](stream-analytics-monitor-jobs.md)
> 
> 

## <a name="azure-powershell-cmdlets-for-stream-analytics"></a>Azure PowerShell cmdlets för Stream Analytics
Följande cmdlets Azure PowerShell kan användas för att övervaka och hantera Azure Stream Analytics jobb. Observera att Azure PowerShell har olika versioner. 
**I exemplen som anges är det första kommandot för Azure PowerShell 0.9.8, det andra kommandot är för Azure PowerShell 1.0.** Kommandona Azure PowerShell 1.0 har alltid "Az" i kommandot.

### <a name="get-azurestreamanalyticsjob--get-azstreamanalyticsjob"></a>Get-AzureStreamAnalyticsJob | Get-AzStreamAnalyticsJob
Visar alla Stream Analytics jobb som definierats i Azure-prenumerationen eller den angivna resursgruppen, eller hämtar jobbinformation om ett specifikt jobb i en resursgrupp.

**Exempel 1**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsJob
```

Azure PowerShell 1.0:  

```powershell
Get-AzStreamAnalyticsJob
```

Det här PowerShell-kommandot returnerar information om Stream Analytics jobb i Azure-prenumerationen.

**Exempel 2**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US 
```

Azure PowerShell 1.0:  

```powershell
Get-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US 
```

Det här PowerShell-kommandot returnerar information Stream Analytics alla jobb i resursgruppen StreamAnalytics-Default-Central-US.

**Exempel 3**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob
```

Azure PowerShell 1.0:  

```powershell
Get-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob
```

Det här PowerShell-kommandot returnerar information Stream Analytics jobbet StreamingJob i resursgruppen StreamAnalytics-Default-Central-US.

### <a name="get-azurestreamanalyticsinput--get-azstreamanalyticsinput"></a>Get-AzureStreamAnalyticsInput | Get-AzStreamAnalyticsInput
Listar alla indata som definieras i ett angivet Stream Analytics jobb eller hämtar information om en specifik indata.

**Exempel 1**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob
```

Azure PowerShell 1.0:  

```powershell
Get-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob
```

Det här PowerShell-kommandot returnerar information om alla indata som definierats i jobbet StreamingJob.

**Exempel 2**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name EntryStream
```

Azure PowerShell 1.0:  

```powershell
Get-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name EntryStream
```

Det här PowerShell-kommandot returnerar information om indata med namnet EntryStream som definierats i jobbet StreamingJob.

### <a name="get-azurestreamanalyticsoutput--get-azstreamanalyticsoutput"></a>Get-AzureStreamAnalyticsOutput | Get-AzStreamAnalyticsOutput
Visar alla utdata som definieras i ett angivet Stream Analytics jobb eller hämtar information om specifika utdata.

**Exempel 1**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob
```

Azure PowerShell 1.0:  

```powershell
Get-AzStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob
```

Det här PowerShell-kommandot returnerar information om de utdata som definierats i jobbet StreamingJob.

**Exempel 2**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name Output
```

Azure PowerShell 1.0:  

```powershell
Get-AzStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name Output
```

Det här PowerShell-kommandot returnerar information om utdata med namnet Output som definierats i jobbet StreamingJob.

### <a name="get-azurestreamanalyticsquota--get-azstreamanalyticsquota"></a>Get-AzureStreamAnalyticsQuota | Get-AzStreamAnalyticsQuota
Hämtar information om kvoten för strömningsenheter i en angiven region.

**Exempel 1**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsQuota -Location "Central US" 
```

Azure PowerShell 1.0:  

```powershell
Get-AzStreamAnalyticsQuota -Location "Central US" 
```

Det här PowerShell-kommandot returnerar information om kvoten och användningen av strömningsenheter i regionen USA, centrala.

### <a name="get-azurestreamanalyticstransformation--get-azstreamanalyticstransformation"></a>Get-AzureStreamAnalyticsTransformation | Get-AzStreamAnalyticsTransformation
Hämtar information om en specifik transformering som definierats i ett Stream Analytics jobb.

**Exempel 1**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name StreamingJob
```

Azure PowerShell 1.0:  

```powershell
Get-AzStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name StreamingJob
```

Det här PowerShell-kommandot returnerar information om omvandlingen med namnet StreamingJob i jobbet StreamingJob.

### <a name="new-azurestreamanalyticsinput--new-azstreamanalyticsinput"></a>New-AzureStreamAnalyticsInput | New-AzStreamAnalyticsInput
Skapar nya indata i ett Stream Analytics jobb eller uppdaterar en befintlig angiven indata.

Namnet på indata kan anges i .json-filen eller på kommandoraden. Om båda anges måste namnet på kommandoraden vara samma som det i filen.

Om du anger indata som redan finns och inte anger parametern -Force frågar cmdleten om du vill ersätta befintliga indata.

Om du anger parametern -Force och anger ett befintligt indatanamn ersätts indata utan bekräftelse.

Detaljerad information om JSON-filstrukturen och JSON-innehållet finns i avsnittet Skapa indata [(Azure Stream Analytics)][msdn-rest-api-create-stream-analytics-input] i Stream Analytics [Management REST API Reference Library][stream.analytics.rest.api.reference].

**Exempel 1**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -File "C:\Input.json" 
```

Azure PowerShell 1.0:  

```powershell
New-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -File "C:\Input.json" 
```

Det här PowerShell-kommandot skapar nya indata från filen Input.jspå. Om en befintlig indata med det namn som anges i indatadefinitionsfilen redan har definierats, frågar cmdleten om den ska ersättas eller inte.

**Exempel 2**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -File "C:\Input.json" -Name EntryStream
```

Azure PowerShell 1.0:  

```powershell
New-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -File "C:\Input.json" -Name EntryStream
```

Det här PowerShell-kommandot skapar nya indata i jobbet EntryStream. Om en befintlig indata med det här namnet redan har definierats, kommer cmdleten att fråga om den ska ersättas eller inte.

**Exempel 3**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -File "C:\Input.json" -Name EntryStream -Force
```

Azure PowerShell 1.0:  

```powershell
New-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -File "C:\Input.json" -Name EntryStream -Force
```

Det här PowerShell-kommandot ersätter definitionen av den befintliga indatakällan med namnet EntryStream med definitionen från filen .

### <a name="new-azurestreamanalyticsjob--new-azstreamanalyticsjob"></a>New-AzureStreamAnalyticsJob | New-AzStreamAnalyticsJob
Skapar ett Stream Analytics jobb i Microsoft Azure uppdaterar definitionen av ett befintligt angivet jobb.

Jobbets namn kan anges i .json-filen eller på kommandoraden. Om båda anges måste namnet på kommandoraden vara samma som det i filen.

Om du anger ett jobbnamn som redan finns och inte anger parametern -Force frågar cmdleten om det befintliga jobbet ska ersättas eller inte.

Om du anger parametern -Force och anger ett befintligt jobbnamn ersätts jobbdefinitionen utan bekräftelse.

Detaljerad information om JSON-filstrukturen och JSON-innehållet finns i avsnittet Create Stream Analytics Job (Skapa [Stream Analytics-jobb)][msdn-rest-api-create-stream-analytics-job] i Stream Analytics [Management REST API Reference Library][stream.analytics.rest.api.reference].

**Exempel 1**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\JobDefinition.json" 
```

Azure PowerShell 1.0:  

```powershell
New-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\JobDefinition.json" 
```

Det här PowerShell-kommandot skapar ett nytt jobb från definitionen i JobDefinition.jspå. Om ett befintligt jobb med det namn som anges i jobbdefinitionsfilen redan har definierats frågar cmdleten om den ska ersättas eller inte.

**Exempel 2**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\JobDefinition.json" -Name StreamingJob -Force
```

Azure PowerShell 1.0:  

```powershell
New-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\JobDefinition.json" -Name StreamingJob -Force
```

Det här PowerShell-kommandot ersätter jobbdefinitionen för StreamingJob.

### <a name="new-azurestreamanalyticsoutput--new-azstreamanalyticsoutput"></a>New-AzureStreamAnalyticsOutput | New-AzStreamAnalyticsOutput
Skapar nya utdata i ett Stream Analytics jobb eller uppdaterar befintliga utdata.  

Namnet på utdata kan anges i .json-filen eller på kommandoraden. Om båda anges måste namnet på kommandoraden vara samma som det i filen.

Om du anger utdata som redan finns och inte anger parametern -Force frågar cmdleten om du vill ersätta befintliga utdata.

Om du anger parametern -Force och anger ett befintligt utdatanamn ersätts utdata utan bekräftelse.

Detaljerad information om JSON-filstrukturen och -innehållet finns i avsnittet Skapa utdata [(Azure Stream Analytics)][msdn-rest-api-create-stream-analytics-output] i Stream Analytics [Management REST API Reference Library][stream.analytics.rest.api.reference].

**Exempel 1**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\Output.json" -JobName StreamingJob -Name output
```

Azure PowerShell 1.0:  

```powershell
New-AzStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\Output.json" -JobName StreamingJob -Name output
```

Det här PowerShell-kommandot skapar nya utdata med namnet "output" i jobbet StreamingJob. Om en befintlig utdata med det här namnet redan har definierats, frågar cmdleten om den ska ersättas eller inte.

**Exempel 2**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\Output.json" -JobName StreamingJob -Name output -Force
```

Azure PowerShell 1.0:  

```powershell
New-AzStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\Output.json" -JobName StreamingJob -Name output -Force
```

Det här PowerShell-kommandot ersätter definitionen för "utdata" i jobbet StreamingJob.

### <a name="new-azurestreamanalyticstransformation--new-azstreamanalyticstransformation"></a>New-AzureStreamAnalyticsTransformation | New-AzStreamAnalyticsTransformation
Skapar en ny transformering inom Stream Analytics jobb eller uppdaterar den befintliga transformeringen.

Namnet på transformeringen kan anges i .json-filen eller på kommandoraden. Om båda anges måste namnet på kommandoraden vara samma som det i filen.

Om du anger en transformering som redan finns och inte anger parametern -Force frågar cmdleten om den befintliga transformeringen ska ersättas eller inte.

Om du anger parametern -Force och anger ett befintligt transformeringsnamn ersätts transformeringen utan bekräftelse.

Detaljerad information om JSON-filstrukturen och JSON-innehållet finns i avsnittet Create [Transformation (Azure Stream Analytics) i][msdn-rest-api-create-stream-analytics-transformation] [Stream Analytics Management REST API Reference Library][stream.analytics.rest.api.reference].

**Exempel 1**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\Transformation.json" -JobName StreamingJob -Name StreamingJobTransform
```

Azure PowerShell 1.0:  

```powershell
New-AzStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\Transformation.json" -JobName StreamingJob -Name StreamingJobTransform
```

Det här PowerShell-kommandot skapar en ny transformering med namnet StreamingJobTransform i jobbet StreamingJob. Om en befintlig transformering redan har definierats med det här namnet frågar cmdleten om den ska ersättas eller inte.

**Exempel 2**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\Transformation.json" -JobName StreamingJob -Name StreamingJobTransform -Force
```

Azure PowerShell 1.0:  

```powershell
New-AzStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\Transformation.json" -JobName StreamingJob -Name StreamingJobTransform -Force
```

 Det här PowerShell-kommandot ersätter definitionen av StreamingJobTransform i jobbet StreamingJob.

### <a name="remove-azurestreamanalyticsinput--remove-azstreamanalyticsinput"></a>Remove-AzureStreamAnalyticsInput | Remove-AzStreamAnalyticsInput
Asynkront tar bort specifika indata från ett Stream Analytics jobb i Microsoft Azure.  
Om du anger parametern -Force tas indata bort utan bekräftelse.

**Exempel 1**

Azure PowerShell 0.9.8:  

```powershell
Remove-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name EventStream
```

Azure PowerShell 1.0:  

```powershell
Remove-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name EventStream
```

Det här PowerShell-kommandot tar bort indata för EventStream i jobbet StreamingJob.  

### <a name="remove-azurestreamanalyticsjob--remove-azstreamanalyticsjob"></a>Remove-AzureStreamAnalyticsJob | Remove-AzStreamAnalyticsJob
Tar asynkront bort ett Stream Analytics jobb i Microsoft Azure.  
Om du anger parametern -Force tas jobbet bort utan bekräftelse.

**Exempel 1**

Azure PowerShell 0.9.8:  

```powershell
Remove-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob 
```

Azure PowerShell 1.0:  

```powershell
Remove-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob 
```

Det här PowerShell-kommandot tar bort jobbet StreamingJob.  

### <a name="remove-azurestreamanalyticsoutput--remove-azstreamanalyticsoutput"></a>Remove-AzureStreamAnalyticsOutput | Remove-AzStreamAnalyticsOutput
Tar asynkront bort specifika utdata från ett Stream Analytics jobb i Microsoft Azure.  
Om du anger parametern -Force tas utdata bort utan bekräftelse.

**Exempel 1**

Azure PowerShell 0.9.8:  

```powershell
Remove-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name Output
```

Azure PowerShell 1.0:  

```powershell
Remove-AzStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name Output
```

Det här PowerShell-kommandot tar bort utdata i jobbet StreamingJob.  

### <a name="start-azurestreamanalyticsjob--start-azstreamanalyticsjob"></a>Start-AzureStreamAnalyticsJob | Start-AzStreamAnalyticsJob
Distribuerar asynkront och startar ett Stream Analytics jobb i Microsoft Azure.

**Exempel 1**

Azure PowerShell 0.9.8:  

```powershell
Start-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob -OutputStartMode CustomTime -OutputStartTime 2012-12-12T12:12:12Z
```

Azure PowerShell 1.0:  

```powershell
Start-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob -OutputStartMode CustomTime -OutputStartTime 2012-12-12T12:12:12Z
```

Det här PowerShell-kommandot startar jobbet StreamingJob med en anpassad starttid för utdata inställd på 12 december 2012, 12:12:12 UTC.

### <a name="stop-azurestreamanalyticsjob--stop-azstreamanalyticsjob"></a>Stop-AzureStreamAnalyticsJob | Stop-AzStreamAnalyticsJob
Asynkront stoppar ett Stream Analytics jobb från att köras i Microsoft Azure och de allokerar resurser som användes. Jobbdefinitionen och metadata förblir tillgängliga i din prenumeration via både Azure Portal api:er och hanterings-API:er, så att jobbet kan redigeras och startas om. Du debiteras inte för ett jobb i stoppat tillstånd.

**Exempel 1**

Azure PowerShell 0.9.8:  

```powershell
Stop-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob 
```

Azure PowerShell 1.0:  

```powershell
Stop-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob 
```

Det här PowerShell-kommandot stoppar jobbet StreamingJob.  

### <a name="test-azurestreamanalyticsinput--test-azstreamanalyticsinput"></a>Test-AzureStreamAnalyticsInput | Test-AzStreamAnalyticsInput
Testar möjligheten för Stream Analytics att ansluta till en angiven indata.

**Exempel 1**

Azure PowerShell 0.9.8:  

```powershell
Test-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name EntryStream
```

Azure PowerShell 1.0:  

```powershell
Test-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name EntryStream
```

Det här PowerShell-kommandot testar anslutningsstatusen för indata EntryStream i StreamingJob.  

### <a name="test-azurestreamanalyticsoutput--test-azstreamanalyticsoutput"></a>Test-AzureStreamAnalyticsOutput | Test-AzStreamAnalyticsOutput
Testar möjligheten för Stream Analytics att ansluta till angivna utdata.

**Exempel 1**

Azure PowerShell 0.9.8:  

```powershell
Test-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name Output
```

Azure PowerShell 1.0:  

```powershell
Test-AzStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name Output
```

Det här PowerShell-kommandot testar anslutningsstatusen för utdata i StreamingJob.  

## <a name="get-support"></a>Få support
Om du vill ha mer hjälp kan du prova [vår Microsoft Q&A-frågesida för att Azure Stream Analytics](/answers/topics/azure-stream-analytics.html). 

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
[msdn-rest-api-create-stream-analytics-transformation]: /cli/azure/stream-analytics/transformation

[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.query.language.reference]: /stream-analytics-query/stream-analytics-query-language-reference
[stream.analytics.rest.api.reference]: /rest/api/streamanalytics/