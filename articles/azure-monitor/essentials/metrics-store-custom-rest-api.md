---
title: Skicka mått till Azure Monitor Metric-databasen med hjälp av REST API
description: Skicka anpassade mått för en Azure-resurs till Azure Monitor mått lagret med hjälp av en REST API
author: anirudhcavale
services: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ancav
ms.openlocfilehash: 8acb3c31a41521eace46e065c06f04e69d97a2e4
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102037225"
---
# <a name="send-custom-metrics-for-an-azure-resource-to-the-azure-monitor-metric-store-by-using-a-rest-api"></a>Skicka anpassade mått för en Azure-resurs till Azure Monitor mått lagret med hjälp av en REST API

Den här artikeln visar hur du skickar anpassade mått för Azure-resurser till Azure Monitor Mät lagring via en REST API. När måtten har Azure Monitor kan du göra allt detta med dem som du gör med standard mått. Exempel är diagram, aviseringar och vidarebefordrar dem till andra externa verktyg.  

>[!NOTE]  
>REST API tillåter endast att du skickar anpassade mått för Azure-resurser. Om du vill skicka mått för resurser i olika miljöer eller lokalt kan du använda [Application Insights](../app/api-custom-events-metrics.md).    


## <a name="create-and-authorize-a-service-principal-to-emit-metrics"></a>Skapa och auktorisera ett huvud namn för tjänsten för att generera mått 

Skapa ett huvud namn för tjänsten i din Azure Active Directory klient genom att följa anvisningarna i [skapa ett huvud namn för tjänsten](../../active-directory/develop/howto-create-service-principal-portal.md). 

Tänk på följande när du går igenom den här processen: 

- Du kan ange valfri URL för inloggnings-URL: en.  
- Skapa en ny klient hemlighet för den här appen.  
- Spara nyckeln och klient-ID: t för användning i senare steg.  

Ge appen skapats som en del av steg 1, övervaka mått utgivare och behörigheter till den resurs som du vill skapa mått för. Om du planerar att använda appen för att generera anpassade mått för många resurser kan du bevilja dessa behörigheter på resurs gruppen eller prenumerations nivån. 

## <a name="get-an-authorization-token"></a>Hämta en autentiseringstoken
Öppna en kommando tolk och kör följande kommando:

```shell
curl -X POST https://login.microsoftonline.com/<yourtenantid>/oauth2/token -F "grant_type=client_credentials" -F "client_id=<insert clientId from earlier step>" -F "client_secret=<insert client secret from earlier step>" -F "resource=https://monitoring.azure.com/"
```
Spara åtkomsttoken från svaret.

![Åtkomsttoken](./media/metrics-store-custom-rest-api/accesstoken.png)

## <a name="emit-the-metric-via-the-rest-api"></a>Generera måttet via REST API 

1. Klistra in följande JSON i en fil och spara den som **custommetric.jspå** den lokala datorn. Uppdatera tids parametern i JSON-filen: 
    
    ```json
    { 
        "time": "2018-09-13T16:34:20", 
        "data": { 
            "baseData": { 
                "metric": "QueueDepth", 
                "namespace": "QueueProcessing", 
                "dimNames": [ 
                  "QueueName", 
                  "MessageType" 
                ], 
                "series": [ 
                  { 
                    "dimValues": [ 
                      "ImagesToProcess", 
                      "JPEG" 
                    ], 
                    "min": 3, 
                    "max": 20, 
                    "sum": 28, 
                    "count": 3 
                  } 
                ] 
            } 
        } 
    } 
    ``` 

1. I kommando tolkens fönster lägger du till mått data: 
   - **azureRegion**. Måste matcha distributions regionen för den resurs som du skickar mått för. 
   - **resourceID**.  Resurs-ID för den Azure-resurs som du ska spåra måttet mot.  
   - **AccessToken**. Klistra in den token som du har köpt tidigare.

     ```Shell 
     curl -X POST https://<azureRegion>.monitoring.azure.com/<resourceId>/metrics -H "Content-Type: application/json" -H "Authorization: Bearer <AccessToken>" -d @custommetric.json 
     ```
1. Ändra tidsstämpel och värden i JSON-filen. 
1. Upprepa föregående två steg några gånger, så att du har data i flera minuter.

## <a name="troubleshooting"></a>Felsökning 
Om du får ett fel meddelande med en del av processen bör du tänka på följande felsöknings information:

1. Du kan inte utfärda mått mot en prenumeration eller resurs grupp som Azure-resurs. 
1. Du kan inte placera ett mått i butiken som är över 20 minuter gammal. Mått lagret är optimerat för varningar och diagram i real tid. 
2. Antalet dimensions namn ska matcha värdena och vice versa. Kontrol lera värdena. 
2. Du kan överföra mått mot en region som inte stöder anpassade mått. Se [regioner som stöds](./metrics-custom-overview.md#supported-regions). 



## <a name="view-your-metrics"></a>Visa dina mått 

1. Logga in på Azure-portalen. 

1. På den vänstra menyn väljer du **övervaka**. 

1. Välj **mått** på sidan **övervaka** . 

   ![Välj mått](./media/metrics-store-custom-rest-api/metrics.png) 

1. Ändra agg regerings perioden till de **senaste 30 minuterna**.  

1. I den nedrullningsbara menyn **resurs** väljer du den resurs som du avsänt måttet mot.  

1. I den nedrullningsbara menyn **namn områden** väljer du **QueueProcessing**. 

1. I den nedrullningsbara menyn **mått** väljer du **QueueDepth**.  

 
## <a name="next-steps"></a>Nästa steg
- Lär dig mer om [anpassade mått](./metrics-custom-overview.md).

