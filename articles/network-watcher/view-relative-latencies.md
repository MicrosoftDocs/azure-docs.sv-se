---
title: Visa relativ fördröjning till Azure-regioner från vissa platser
description: Lär dig hur du visar relativa fördröjningar i Internet leverantörer till Azure-regioner från vissa platser.
services: network-watcher
documentationcenter: ''
author: damendo
ms.service: virtual-network
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/14/2017
ms.author: damendo
ms.custom: ''
ms.openlocfilehash: 938315dac80ae51984851083753a9cb8f77662b2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "84737911"
---
# <a name="view-relative-latency-to-azure-regions-from-specific-locations"></a>Visa relativ svarstid till Azure-regioner från specifika platser

> [!WARNING]
> Den här funktionen är för närvarande en för hands version och testas fortfarande för stabilitet.

I den här självstudien får du lära dig hur du använder Azure [Network Watcher](network-watcher-monitoring-overview.md) -tjänsten för att avgöra vilken Azure-region som du vill distribuera ditt program eller din tjänst i, baserat på din användar demografiska information. Dessutom kan du använda den för att utvärdera tjänst leverantörers anslutningar till Azure.  
        

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="create-a-network-watcher"></a>Skapa en nätverks övervakare

Om du redan har en nätverks övervakare i minst en Azure- [region](https://azure.microsoft.com/regions)kan du hoppa över aktiviteterna i det här avsnittet. Skapa en resurs grupp för Network Watcher. I det här exemplet skapas resurs gruppen i regionen USA, östra, men du kan skapa resurs gruppen i valfri Azure-region.

```powershell
New-AzResourceGroup -Name NetworkWatcherRG -Location eastus
```

Skapa en Network Watcher. Du måste ha en nätverks Övervakare som skapats i minst en Azure-region. I det här exemplet skapas en nätverks övervakare i Azure-regionen USA, östra.

```powershell
New-AzNetworkWatcher -Name NetworkWatcher_eastus -ResourceGroupName NetworkWatcherRG -Location eastus
```

## <a name="compare-relative-network-latencies-to-a-single-azure-region-from-a-specific-location"></a>Jämför relativ nätverks fördröjning till en enda Azure-region från en viss plats

Utvärdera tjänst leverantörer eller Felsök en användare som rapporterar ett problem som "webbplatsen var långsam" från en bestämd plats till den Azure-region där en tjänst distribueras. Följande kommando returnerar till exempel den genomsnittliga relativa Internet leverantörens fördröjning mellan delstaten Washington i USA och USA, västra 2 Azure-regionen mellan 13-15 december, 2017:

```powershell
Get-AzNetworkWatcherReachabilityReport `
  -NetworkWatcherName NetworkWatcher_eastus `
  -ResourceGroupName NetworkWatcherRG `
  -Location "West US 2" `
  -Country "United States" `
  -State "washington" `
  -StartTime "2017-12-13" `
  -EndTime "2017-12-15"
```

> [!NOTE]
> Den region som du anger i föregående kommando behöver inte vara samma som den region som du angav när du hämtade nätverks övervakaren. Föregående kommando kräver bara att du anger en befintlig nätverks övervakare. Nätverks bevakaren kan finnas i vilken region som helst. Om du anger värden för `-Country` och `-State` måste de vara giltiga. Värdena är Skift läges känsliga. Data är tillgängliga för ett begränsat antal länder/regioner, stater och städer. Kör kommandona i [Visa tillgängliga länder/regioner, tillstånd, städer och providers](#view-available) om du vill visa en lista över tillgängliga länder/regioner, städer och tillstånd som ska användas med föregående kommando. 

> [!WARNING]
> Du måste ange ett datum under de senaste 30 dagarna för `-StartTime` och `-EndTime` . Att ange ett tidigare datum innebär att inga data returneras.

Utdata från föregående kommando ser ut så här:

```powershell
AggregationLevel   : State
ProviderLocation   : {
                       "Country": "United States",
                       "State": "washington"
                     }
ReachabilityReport : [
                       {
                         "Provider": "Qwest Communications Company, LLC - ASN 209",
                         "AzureLocation": "West US 2",
                         "Latencies": [
                           {
                             "TimeStamp": "2017-12-14T00:00:00Z",
                             "Score": 92
                           },
                           {
                             "TimeStamp": "2017-12-13T00:00:00Z",
                             "Score": 92
                           }
                         ]
                       },
                       {
                         "Provider": "Comcast Cable Communications, LLC - ASN 7922",
                         "AzureLocation": "West US 2",
                         "Latencies": [
                           {
                             "TimeStamp": "2017-12-14T00:00:00Z",
                             "Score": 96
                           },
                           {
                             "TimeStamp": "2017-12-13T00:00:00Z",
                             "Score": 96
                           }
                         ]
                       }
                     ]
```

I returnerade utdata är värdet för **score** den relativa svars tiden i regioner och leverantörer. Poängen på 1 är den värsta (högsta) fördröjningen, medan 100 är den lägsta svars tiden. De relativa fördröjningarna beräknas för dagen. I föregående exempel är det klart att fördröjningen var samma båda dagar och att det finns en liten skillnad mellan de två providerns svars tider, men det är också uppenbart att fördröjningarna för båda providern är låga på 1-100-skalan. Det här förväntas, eftersom delstaten Washington i USA är fysiskt nära regionen Västra USA 2 Azure, men resultatet blir inte som förväntat. Ju större datum intervall du anger, desto mer kan du genomsnittlig svars tid över tid.

## <a name="compare-relative-network-latencies-across-azure-regions-from-a-specific-location"></a>Jämför relativa nätverks fördröjningar i Azure-regioner från en angiven plats

Om du i stället för att ange den relativa svars tids fördröjningen mellan en bestämd plats och en särskild Azure-region med `-Location` , ville du fastställa den relativa svars tid för alla Azure-regioner från en särskild fysisk plats, kan du också göra det. Följande kommando hjälper dig till exempel att utvärdera vilken Azure-region som ska användas för att distribuera en tjänst i om dina primära användare är Comcast användare i Washington-tillstånd:

```powershell
Get-AzNetworkWatcherReachabilityReport `
  -NetworkWatcherName NetworkWatcher_eastus `
  -ResourceGroupName NetworkWatcherRG `
  -Provider "Comcast Cable Communications, LLC - ASN 7922" `
  -Country "United States" `
  -State "washington" `
  -StartTime "2017-12-13" `
  -EndTime "2017-12-15"
```

> [!NOTE]
> Till skillnad från när du anger en enda plats, om du inte anger en plats eller anger flera platser, t. ex. "västra 2; USA", "västra USA", måste du ange en Internet tjänst leverantör när du kör kommandot. 

## <a name="view-available-countriesregions-states-cities-and-providers"></a><a name="view-available"></a>Visa tillgängliga länder/regioner, tillstånd, städer och leverantörer

Data är tillgängliga för vissa Internet tjänst leverantörer, länder/regioner, stater och städer. Om du vill visa en lista över alla tillgängliga Internet leverantörer, länder/regioner, stater och städer som du kan visa data för, anger du följande kommando:

```powershell
Get-AzNetworkWatcherReachabilityProvidersList -NetworkWatcherName NetworkWatcher_eastus -ResourceGroupName NetworkWatcherRG
```

Data är bara tillgängliga för de länder/regioner, stater och städer som returnerades av föregående kommando. Det tidigare kommandot kräver att du anger en befintlig nätverks övervakare. Exemplet angav *NetworkWatcher_eastus* Network watcher i en resurs grupp med namnet *NetworkWatcherRG*, men du kan ange en befintlig nätverks övervakare. Om du inte har en befintlig nätverks övervakare kan du skapa en genom att slutföra uppgifterna i [skapa en Network Watcher](#create-a-network-watcher). 

När du har kört föregående kommando kan du filtrera utdata som returneras genom att ange giltiga värden för **land**, **delstat** och **stad**, om så önskas.  Om du till exempel vill visa en lista över Internet leverantörer som är tillgängliga i Seattle, Washington, i USA, anger du följande kommando:

```powershell
Get-AzNetworkWatcherReachabilityProvidersList `
  -NetworkWatcherName NetworkWatcher_eastus `
  -ResourceGroupName NetworkWatcherRG `
  -City Seattle `
  -Country "United States" `
  -State washington
```

> [!WARNING]
> Värdet som anges för **land** måste vara versaler och gemener. Värdena som anges för **State** och **City** måste vara gemener. Värdena måste anges i de utdata som returneras när kommandot har körts utan värden för **land**, **delstat** och **ort**. Om du anger fel skift läge eller anger ett värde för **land**, **delstat** eller **stad** som inte finns i de utdata som returneras när du kör kommandot utan värden för dessa egenskaper, är den returnerade informationen tom.
