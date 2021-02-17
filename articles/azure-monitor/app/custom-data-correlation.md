---
title: Azure Application Insights | Microsoft Docs
description: Korrelera data från Application Insights till andra data uppsättningar, till exempel data anriknings-eller uppslags tabeller, icke-Application Insights data källor och anpassade data.
ms.topic: conceptual
author: eternovsky
ms.author: evternov
ms.date: 08/08/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 933280b5d3b81098f18f22a72bd2c7f942869e6a
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100578330"
---
# <a name="correlating-application-insights-data-with-custom-data-sources"></a>Korrelera Application Insights data med anpassade data källor

Application Insights samlar in flera olika data typer: undantag, spår, sid visningar och andra. Det här är ofta tillräckligt för att undersöka programmets prestanda, tillförlitlighet och användning, men det finns fall då det är användbart att korrelera data som lagras i Application Insights till andra helt anpassade data uppsättningar.

Några situationer där du kanske vill att anpassade data ska vara:

- Data anriknings-eller uppslags tabeller: du kan till exempel komplettera ett server namn med ägaren av servern och den labb plats där den finns 
- Korrelation med icke-Application Insights data Källor: du kan t. ex. korrelera data om ett köp i en webb butik med information från din köp tjänst för att fastställa hur korrekta uppskattningar för leverans tiden är 
- Fullständigt anpassade data: många av våra kunder älskar att älska frågespråket och prestanda för den Azure Monitor logg plattform som säkerhetskopierar Application Insights och vill använda den för att fråga efter data som inte är relaterade till Application Insights. Till exempel för att spåra sol skärms prestanda som en del av en Smart Start installation som beskrivs [här](https://www.catapultsystems.com/blogs/using-log-analytics-and-a-special-guest-to-forecast-electricity-generation/).

## <a name="how-to-correlate-custom-data-with-application-insights-data"></a>Korrelera anpassade data med Application Insights data 

Eftersom Application Insights backas upp av den kraftfulla Azure Monitor logg plattformen kan vi använda den fullt kraften i Azure Monitor för att mata in data. Sedan ska vi skriva frågor med operatorn "Join" som korrelerar dessa anpassade data med de data som är tillgängliga för oss i Azure Monitor loggar. 

## <a name="ingesting-data"></a>Mata in data

I det här avsnittet ska vi granska hur du hämtar dina data till Azure Monitor loggar.

Om du inte redan har ett kan du etablera en ny Log Analytics arbets yta genom att följa [dessa instruktioner](../vm/quick-collect-azurevm.md) genom att följa steget "skapa en arbets yta".

För att börja skicka loggdata till Azure Monitor. Det finns flera alternativ:

- För en synkron mekanism kan du antingen anropa [API: et för data insamlaren](../logs/data-collector-api.md) direkt eller använda vår Logic app Connector – du behöver bara leta efter "Azure Log Analytics" och välja alternativet "skicka data":

  ![Skärm bilds val och åtgärd](./media/custom-data-correlation/01-logic-app-connector.png)  

- För ett asynkront alternativ använder du API: et för data insamling för att bygga en bearbetnings pipeline. Mer information finns i [den här artikeln](../logs/create-pipeline-datacollector-api.md) .

## <a name="correlating-data"></a>Korrelera data

Application Insights baseras på Azure Monitor logg plattform. Vi kan därför använda [kors resurs kopplingar](../logs/cross-workspace-query.md) för att korrelera data som vi har matat in i Azure monitor med våra Application Insights data.

Vi kan till exempel mata in vår labb inventering och platser i en tabell med namnet "LabLocations_CL" i en Log Analytics arbets yta med namnet "myLA". Om vi sedan ville granska våra begär Anden som spårats i Application Insights app med namnet "myAI" och korrelera dator namnen som hanterade begär anden till platserna för de här datorerna som lagras i den tidigare nämnda anpassade tabellen, kan vi köra följande fråga från antingen Application Insights-eller Azure Monitor kontexten:

```
app('myAI').requests
| join kind= leftouter (
    workspace('myLA').LabLocations_CL
    | project Computer_S, Owner_S, Lab_S
) on $left.cloud_RoleInstance == $right.Computer
```

## <a name="next-steps"></a>Nästa steg

- Kolla in API-referens för [data insamling](../logs/data-collector-api.md) .
- För mer information om [anslutningar över flera resurser](../logs/cross-workspace-query.md).
