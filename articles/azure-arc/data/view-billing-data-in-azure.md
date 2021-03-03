---
title: Ladda upp fakturerings data till Azure och visa dem i Azure Portal
description: Ladda upp fakturerings data till Azure och visa dem i Azure Portal
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 03/02/2021
ms.topic: how-to
ms.openlocfilehash: 16546432c8c0a23d5c9dc471fe8c62ced5eca993
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101687540"
---
# <a name="upload-billing-data-to-azure-and-view-it-in-the-azure-portal"></a>Ladda upp fakturerings data till Azure och visa dem i Azure Portal

> [!IMPORTANT] 
>  Det kostar inget att använda Azure Arc-aktiverade data tjänster under för hands versions perioden. Även om fakturerings systemet fungerar som slutdatum är fakturerings mätaren inställd på $0.  Om du följer det här scenariot visas poster i din fakturering för en tjänst som för närvarande heter **hybrid data tjänster** och för resurser av en typ som kallas **Microsoft. AzureData/ `<resource type>`**. Du kommer att kunna se en post för varje data tjänst – Azure-båge som du skapar, men varje post debiteras för $0.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="connectivity-modes---implications-for-billing-data"></a>Anslutnings lägen – konsekvenser för fakturerings data

I framtiden finns det två lägen där du kan köra dina Azure Arc-aktiverade data tjänster:

- **Indirekt ansluten** – det finns ingen direkt anslutning till Azure. Data skickas endast till Azure via en export-/överförings process. Alla Azure Arc Data Services-distributioner fungerar i det här läget idag i för hands versionen.
- **Direkt ansluten** – i det här läget är det ett beroende på den Azure Arc-aktiverade Kubernetes-tjänsten för att tillhandahålla en direkt anslutning mellan Azure och det Kubernetes-kluster som Azure Arc-aktiverade data tjänster körs på. Detta möjliggör fler funktioner och du kan också använda Azure Portal och Azure CLI för att hantera dina Azure Arc-aktiverade data tjänster precis som du hanterar dina data tjänster i Azure PaaS.  Detta anslutnings läge är inte tillgängligt ännu i förhands granskning, men kommer snart.

Du kan läsa mer om skillnaden mellan [anslutnings lägena](./connectivity.md).

I läget för indirekt anslutning exporteras fakturerings data regelbundet från data styrenheten för Azure-bågen till en säker fil och överförs sedan till Azure och bearbetas.  I det kommande anslutna läget skickas fakturerings data automatiskt till Azure ungefär 1/timme för att ge en nästan real tids vy över kostnaderna för dina tjänster. Processen för att exportera och ladda upp data i det indirekt anslutna läget kan också automatiseras med hjälp av skript eller så kan vi skapa en tjänst som gör det åt dig.

## <a name="upload-billing-data-to-azure"></a>Ladda upp fakturerings data till Azure

Om du vill överföra fakturerings data till Azure måste följande inträffa först:

1. Skapa en Azure Arc-aktiverad data tjänst om du inte redan har en. Skapa till exempel något av följande:
   - [Skapa en Azure SQL-hanterad instans på Azure-bågen](create-sql-managed-instance.md)
   - [Skapa en Azure-Arc-aktiverad PostgreSQL Hyperskala-servergrupp](create-postgresql-hyperscale-server-group.md)
1. [Överför resurs inventering, användnings data, statistik och loggar till Azure Monitor](upload-metrics-and-logs-to-azure-monitor.md) om du inte redan gjort det.
1. Vänta minst 2 timmar sedan skapandet av data tjänsten så att insamlings processen för betalningstelemetri kan samla in vissa fakturerings data.

Kör följande kommando för att exportera fakturerings data:

```console
azdata arc dc export -t usage -p usage.json
```

För tillfället är filen inte krypterad så att du kan se innehållet. Du kan öppna i en text redigerare och se hur innehållet ser ut.

Du ser att det finns två uppsättningar med data: `resources` och `data` . `resources`Är datakontrollanten, postgresql för storskaliga Server grupper och SQL-hanterade instanser. `resources`Posterna i data insamlingen av de viktiga händelserna i historiken för en resurs – när den skapades, när den uppdaterades och när den togs bort. `data`Posterna fångar hur många kärnor som skulle vara tillgängliga för användning av en specifik instans i varje timme.

Exempel på en `resource` post:

```console
    {
        "customObjectName": "<resource type>-2020-29-5-23-13-17-164711",
        "uid": "4bc3dc6b-9148-4c7a-b7dc-01afc1ef5373",
        "instanceName": "sqlInstance001",
        "instanceNamespace": "arc",
        "instanceType": "<resource>",
        "location": "eastus",
        "resourceGroupName": "production-resources",
        "subscriptionId": "482c901a-129a-4f5d-86e3-cc6b294590b2",
        "isDeleted": false,
        "externalEndpoint": "32.191.39.83:1433",
        "vCores": "2",
        "createTimestamp": "05/29/2020 23:13:17",
        "updateTimestamp": "05/29/2020 23:13:17"
    }
```

Exempel på en `data` post:

```console
        {
          "requestType": "usageUpload",
          "clusterId": "4b0917dd-e003-480e-ae74-1a8bb5e36b5d",
          "name": "DataControllerTestName",
          "subscriptionId": "482c901a-129a-4f5d-86e3-cc6b294590b2",
          "resourceGroup": "production-resources",
          "location": "eastus",
          "uploadRequest": {
            "exportType": "usages",
            "dataTimestamp": "2020-06-17T22:32:24Z",
            "data": "[{\"name\":\"sqlInstance001\",
                       \"namespace\":\"arc\",
                       \"type\":\"<resource type>\",
                       \"eventSequence\":1, 
                       \"eventId\":\"50DF90E8-FC2C-4BBF-B245-CB20DC97FF24\",
                       \"startTime\":\"2020-06-17T19:11:47.7533333\",
                       \"endTime\":\"2020-06-17T19:59:00\",
                       \"quantity\":1,
                       \"id\":\"4BC3DC6B-9148-4C7A-B7DC-01AFC1EF5373\"}]",
           "signature":"MIIE7gYJKoZIhvcNAQ...2xXqkK"
          }
        }
```

Kör följande kommando för att överföra usage.jspå filen till Azure:

```console
azdata arc dc upload -p usage.json
```

## <a name="view-billing-data-in-azure-portal"></a>Visa fakturerings data i Azure Portal

Följ dessa steg om du vill visa fakturerings data i Azure Portal:

1. Öppna [Azure-portalen](https://portal.azure.com).
1. I rutan Sök högst upp på skärmen skriver du **Cost Management** och klickar på tjänsten Cost Management.
1. Klicka på fliken **Cost Management** under **Cost Management översikt**.
1. Klicka på fliken **kostnads analys** till vänster.
1. Klicka på knappen **kostnad per resurs** överst i vyn.
1. Se till att ditt omfång är inställt på den prenumeration som dina data tjänst resurser skapades i.
1. Välj **kostnad per resurs** i list rutan Visa bredvid omfattnings väljaren längst upp i vyn.
1. Kontrol lera att datum filtret är inställt på **den här månaden** eller något annat tidsintervall som passar för när du skapade dina data tjänst resurser.
1. Klicka på **Lägg till filter** för att lägga till ett filter efter **resurs typ**  =  `microsoft.azuredata/<data service type>` om du bara vill filtrera ned till en typ av Azure Arc-aktiverad data tjänst.
1. Nu visas en lista över alla resurser som har skapats och laddats upp till Azure. Eftersom fakturerings mätaren är $0 kommer du att se att kostnaden alltid är $0.

## <a name="download-billing-data"></a>Hämta fakturerings data

Du kan hämta sammanfattnings data för fakturering direkt från Azure Portal.

1. I samma **kostnads analys – > visning av resurs typs** vyn som du har uppnått genom att följa anvisningarna ovan, klickar du på nedladdnings knappen längst upp.
1. Välj hämtnings fil typ-Excel eller CSV – och klicka på knappen **Hämta data** .
1. Öppna filen i ett lämpligt redigerings program för den valda filtypen.

## <a name="export-billing-data"></a>Exportera fakturerings data

Du kan också regelbundet exportera **detaljerad** användnings-och fakturerings information till en Azure Storage behållare genom att skapa ett fakturerings export jobb. Detta är användbart om du vill se information om din fakturering, till exempel hur många timmar en viss instans fakturerades för under fakturerings perioden.

Följ de här stegen för att ställa in ett fakturerings export jobb:

1. Klicka på **Exportera** till vänster.
1. Klicka på **Lägg till**.
1. Ange ett namn och en export frekvens och klicka på Nästa.
1. Välj att antingen skapa ett nytt lagrings konto eller använda ett befintligt konto och fyll i formuläret för att ange lagrings kontot, behållaren och sökvägen för att exportera datafilerna till och klicka på Nästa.
1. Klicka på **Skapa**.

Dina exportfiler för fakturerings data kommer att vara tillgängliga på ungefär fyra timmar och kommer att exporteras enligt det schema du angav när du skapade export jobbet för fakturering.

Följ dessa steg om du vill visa de fakturerings data filer som exporteras:

Du kan verifiera fakturerings data filen i Azure Portal. 

> [!IMPORTANT]
> När du har skapat fakturerings export jobbet väntar du i 4 timmar innan du fortsätter med följande steg.

1. I rutan Sök högst upp i portalen skriver du in **lagrings konton** och klickar på **lagrings konton**.
3. Klicka på det lagrings konto som du angav när du skapade export jobbet för fakturering ovan.
4. Klicka på behållare till vänster.
5. Klicka på den behållare som du angav när du skapade export jobbet för fakturering ovan.
6. Klicka på den mapp som du angav när du skapade export jobbet för fakturering ovan.
7. Öka detalj nivån i de genererade mapparna och filerna och klicka på en av de genererade CSV-filerna.
8. Klicka på knappen **Ladda ned** för att spara filen i mappen med lokala nedladdningar.
9. Öppna filen med ett. CSV-filformat som Excel.
10. Filtrera resultaten så att endast de rader som har **resurs typen** visas  =  `Microsoft.AzureData/<data service resource type` .
11. Du kommer att se antalet timmar som instansen användes under den aktuella 24-timmarsperiod i kolumnen UsageQuantity.
