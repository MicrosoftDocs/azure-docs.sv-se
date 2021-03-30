---
title: Begrepp, terminologi och enheter
description: Läs om begrepp, terminologi och entitetshierarki inklusive jobb och jobbsamlingar i Azure Scheduler
services: scheduler
ms.service: scheduler
ms.suite: infrastructure-services
author: derek1ee
ms.author: deli
ms.reviewer: klam, estfan
ms.topic: conceptual
ms.date: 08/18/2016
ms.openlocfilehash: 899c64e818896cde18e955d6abd82594734c4b57
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92368170"
---
# <a name="concepts-terminology-and-entities-in-azure-scheduler"></a>Begrepp, terminologi och entiteter i Azure Scheduler

> [!IMPORTANT]
> [Azure Logic Apps](../logic-apps/logic-apps-overview.md) ersätter Azure Scheduler, som dras [tillbaka](../scheduler/migrate-from-scheduler-to-logic-apps.md#retire-date). Om du vill fortsätta arbeta med de jobb som du konfigurerar i Scheduler, [migrera till Azure Logic Apps](../scheduler/migrate-from-scheduler-to-logic-apps.md) så snart som möjligt. 
>
> Scheduler är inte längre tillgänglig i Azure Portal, men [PowerShell-cmdletarna](scheduler-powershell-reference.md) [REST API](/rest/api/scheduler) och Azure Scheduler är tillgängliga just nu så att du kan hantera jobb och jobb samlingar.

## <a name="entity-hierarchy"></a>Entitetshierarki

Azure Scheduler REST API:et visar och använder de här huvudsakliga entiteterna eller resurserna:

| Entitet | Beskrivning |
|--------|-------------|
| **Jobb** | Definierar en enskild återkommande åtgärd med enkla eller komplexa strategier för körning. Exempel på åtgärder kan vara HTTP, Storage-kö, Service Bus-kö eller Service Bus-ämnesbegäranden. | 
| **Jobbsamling** | Innehåller en grupp med jobb och hanterar inställningar, kvoter och begränsningar som delas av jobb i samlingen. Som en Azure-prenumerationsägare kan du skapa jobbsamlingar och gruppera jobb tillsammans baserat på deras användning eller programgränser. En jobbsamling har dessa attribut: <p>– begränsad till en region. <br>– låter dig framtvinga kvoter så att du kan begränsa användningen för alla jobb i en samling. <br>– kvoter inkluderar MaxJobs och MaxRecurrence. | 
| **Jobbhistorik** | Innehåller information om en jobbkörning, till exempel status och eventuell svarsinformation. |
||| 

## <a name="entity-management"></a>Entitetshantering

Vid en hög nivå exponerar Scheduler REST API de här åtgärderna för hantering av entiteter.

### <a name="job-management"></a>Jobbhantering

Stöder åtgärder för att skapa och redigera jobb. Alla jobb måste tillhöra en befintlig jobbsamling. Ingen implicit generering utförs. Mer information finns i [Scheduler REST API – jobb](/rest/api/scheduler/jobs). Här är URI-adressen för de här åtgärderna:

```
https://management.azure.com/subscriptions/{subscriptionID}/resourceGroups/{resourceGroupName}/providers/Microsoft.Scheduler/jobCollections/{jobCollectionName}/jobs/{jobName}
```

### <a name="job-collection-management"></a>Hantering av jobbsamlingar

Stöder åtgärder för att skapa och redigera jobb och jobbsamlingar, som mappar till kvoter och delade inställningar. Till exempel kvoter som anger maximalt antal jobb och minsta upprepningsintervall. Mer information finns i [Scheduler REST API – jobbsamlingar](/rest/api/scheduler/jobcollections). Här är URI-adressen för de här åtgärderna:

```
https://management.azure.com/subscriptions/{subscriptionID}/resourceGroups/{resourceGroupName}/providers/Microsoft.Scheduler/jobCollections/{jobCollectionName}
```

### <a name="job-history-management"></a>Hantering av jobbhistorik

Stöder GET-åtgärden för att hämta 60 dagars jobbkörningshistorik, till exempel förfluten tid och jobbkörningsresultat. Inkluderar parameterstöd för frågesträngar för filtrering baserat på tillstånd och status. Mer information finns i [Scheduler REST API – Jobb – Lista jobbhistorik](/rest/api/scheduler/jobs/listjobhistory). Här är URI-adressen för den här åtgärden:

```
https://management.azure.com/subscriptions/{subscriptionID}/resourceGroups/{resourceGroupName}/providers/Microsoft.Scheduler/jobCollections/{jobCollectionName}/jobs/{jobName}/history
```

## <a name="job-types"></a>Jobbtyper

Azure Scheduler stöder flera jobbtyper: 

* HTTP-jobb, inklusive HTTPS-jobb som stöder TLS, för när du har slut punkten för en befintlig tjänst eller arbets belastning
* Storage-köjobb för arbetsbelastningar som använder Storage-köer, som att publicera meddelanden till Storage-köer
* Service Bus-köjobb för arbetsbelastningar som använder Service Bus-köer
* Service Bus-ämnesjobb för arbetsbelastningar som använder Service Bus-ämnen

## <a name="job-definition"></a>Jobbdefinition

På hög nivå har ett Scheduler-jobb de här grundläggande delarna:

* Åtgärden som körs när jobbets timer utlöses
* Valfritt: tiden då jobbet ska köras
* Valfritt: när och hur ofta jobbet ska upprepas
* Valfritt: En felåtgärd som körs om den primära åtgärden misslyckas

Jobbet innehåller även systemangivna data, till exempel jobbets nästa schemalagda körning. Jobbets koddefinition är ett objekt i JavaScript Object Notation (JSON)-format, vilket har dessa element:

| Element | Krävs | Beskrivning | 
|---------|----------|-------------| 
| [**/St**](#start-time) | Inga | Starttid för jobbet med en tidszonsförskjutning i [ISO 8601-format](https://en.wikipedia.org/wiki/ISO_8601) | 
| [**tgärd**](#action) | Ja | Information om den primära åtgärden, vilket kan inkludera ett **errorAction**-objekt | 
| [**errorAction**](#error-action) | Inga | Information om den sekundära åtgärd som körs om den primära åtgärden misslyckas |
| [**mönster**](#recurrence) | Inga | Information som frekvens och intervall för ett återkommande jobb | 
| [**retryPolicy**](#retry-policy) | Inga | Information om hur ofta en åtgärd ska göras om | 
| [**låst**](#state) | Ja | Information om jobbets aktuella tillstånd |
| [**statusfältet**](#status) | Ja | Information om jobbets aktuella status som kontrolleras av tjänsten |
||||

Här är ett exempel som visar en heltäckande jobbdefinition för en HTTP-åtgärd med mer fullständig elementinformation som beskrivs i senare avsnitt: 

```json
"properties": {
   "startTime": "2012-08-04T00:00Z",
   "action": {
      "type": "Http",
      "request": {
         "uri": "http://contoso.com/some-method", 
         "method": "PUT",          
         "body": "Posting from a timer",
         "headers": {
            "Content-Type": "application/json"
         },
         "retryPolicy": { 
             "retryType": "None" 
         },
      },
      "errorAction": {
         "type": "Http",
         "request": {
            "uri": "http://contoso.com/notifyError",
            "method": "POST"
         }
      }
   },
   "recurrence": {
      "frequency": "Week",
      "interval": 1,
      "schedule": {
         "weekDays": ["Monday", "Wednesday", "Friday"],
         "hours": [10, 22]
      },
      "count": 10,
      "endTime": "2012-11-04"
   },
   "state": "Disabled",
   "status": {
      "lastExecutionTime": "2007-03-01T13:00:00Z",
      "nextExecutionTime": "2007-03-01T14:00:00Z ",
      "executionCount": 3,
      "failureCount": 0,
      "faultedCount": 0
   }
}
```

<a name="start-time"></a>

## <a name="starttime"></a>startTime

I objektet **startTime** kan du ange starttid och en tidszonsförskjutning i [ISO 8601-format](https://en.wikipedia.org/wiki/ISO_8601).

<a name="action"></a>

## <a name="action"></a>åtgärd

Ditt Scheduler-jobb kör en primär **åtgärd** baserat på det angivna schemat. Scheduler stöder HTTP, Storage-kö, Service Bus-kö och Service Bus-ämnesåtgärder. Om den primära **åtgärden** misslyckas, kan Scheduler köra en sekundär [**errorAction**](#erroraction) som hanterar felet. Objektet **åtgärd** beskriver dessa element:

* Åtgärdens tjänsttyp
* Åtgärdens information
* Ett alternativ **errorAction**

Föregående exempel beskriver en HTTP-åtgärd. Här är ett exempel på en Storage-köåtgärd:

```json
"action": {
   "type": "storageQueue",
   "queueMessage": {
      "storageAccount": "myStorageAccount",  
      "queueName": "myqueue",                
      "sasToken": "TOKEN",                   
      "message": "My message body"
    }
}
```

Här är ett exempel på en Service Bus-köåtgärd:

```json
"action": {
   "type": "serviceBusQueue",
   "serviceBusQueueMessage": {
      "queueName": "q1",  
      "namespace": "mySBNamespace",
      "transportType": "netMessaging", // Either netMessaging or AMQP
      "authentication": {  
         "sasKeyName": "QPolicy",
         "type": "sharedAccessKey"
      },
      "message": "Some message",  
      "brokeredMessageProperties": {},
      "customMessageProperties": {
         "appname": "FromScheduler"
      }
   }
},
```

Här är ett exempel på en Service Bus-ämnesåtgärd:

```json
"action": {
   "type": "serviceBusTopic",
   "serviceBusTopicMessage": {
      "topicPath": "t1",  
      "namespace": "mySBNamespace",
      "transportType": "netMessaging", // Either netMessaging or AMQP
      "authentication": {
         "sasKeyName": "QPolicy",
         "type": "sharedAccessKey"
      },
      "message": "Some message",
      "brokeredMessageProperties": {},
      "customMessageProperties": {
         "appname": "FromScheduler"
      }
   }
},
```

Mer information om SAS-tokens (signaturer för delad åtkomst) finns i [auktorisera med signaturer för delad åtkomst](../storage/common/storage-sas-overview.md).

<a name="error-action"></a>

## <a name="erroraction"></a>errorAction

Om ditt jobbs primära **åtgärd** misslyckas, kan Scheduler köra en **errorAction** som hanterar felet. I den primära **åtgärden** kan du ange ett **errorAction** objekt så att Scheduler kan anropa en slutpunkt för felhantering eller skicka ett användarmeddelande. 

Om en katastrof till exempel inträffar på den primära slutpunkten så kan du använda **errorAction** för att anropa en sekundär slutpunkt eller för att meddela en slutpunkt för felhantering. 

Precis som den primärra **åtgärden** så kan du låta felåtgärden använda enkel eller sammansatt logik baserat på andra åtgärder. 

<a name="recurrence"></a>

## <a name="recurrence"></a>recurrence

Ett jobb återkommer om jobbets JSON-definition innehåller objektet **upprepning**, till exempel:

```json
"recurrence": {
   "frequency": "Week",
   "interval": 1,
   "schedule": {
      "hours": [10, 22],
      "minutes": [0, 30],
      "weekDays": ["Monday", "Wednesday", "Friday"]
   },
   "count": 10,
   "endTime": "2012-11-04"
},
```

| Egenskap | Krävs | Värde | Beskrivning | 
|----------|----------|-------|-------------| 
| **frequency** | Ja, när **upprepning** används | Minut, timme, dag, vecka, månad, år | Tidsenheten mellan förekomster | 
| **intervall** | Inga | 1 till och med 1 000 | Ett positivt heltal som anger antalet tidsenheter mellan varje förekomst utifrån **frekvens** | 
| **Ange** | Inga | Det varierar | Information för mer komplicerade och avancerade scheman. Se **timmar**, **minuter**, **weekDays**, **månader** och **monthDays** | 
| **timmarna** | Inga | 1 till 24 | En matris med timmesmarkeringarrna för när jobbet ska köras | 
| **fördröjning** | Inga | 0 till 59 | En matris med minutmarkeringarrna för när jobbet ska köras | 
| **months** | Inga | 1 till 12 | En matris med månaderna då jobbet ska köras | 
| **monthDays** | Inga | Det varierar | En matris med dagarna i månaden då jobbet ska köras | 
| **weekDays** | Inga | Måndag, tisdag, onsdag, torsdag, fredag, lördag och söndag | En matris med veckodagarna när jobbet ska köras | 
| **count** | Inga | <*alternativet*> | Antal upprepningar. Standardvärdet är oändlig upprepning. Du kan inte använda både **antal** och **endTime** men regeln som slutar först gäller. | 
| **Slut** | Inga | <*alternativet*> | Datum och tid när du vill stoppa upprepningen. Standardvärdet är oändlig upprepning. Du kan inte använda både **antal** och **endTime** men regeln som slutar först gäller. | 
||||

Mer information om dessa element finns i [Skapa komplexa scheman och avancerad upprepningar](../scheduler/scheduler-advanced-complexity.md).

<a name="retry-policy"></a>

## <a name="retrypolicy"></a>retryPolicy

För fallet när ett Scheduler-jobb misslyckas, kan du ställa in en återförsöksprincip som avgör om och hur Scheduler försöker åtgärden igen. Som standard försöker Scheduler köra om jobbet fyra gånger med 30 sekunders mellanrum. Du kan göra den här principen mer eller mindre aggressiv, till exempel försöker den här principen en åtgärd två gånger per dag:

```json
"retryPolicy": { 
   "retryType": "Fixed",
   "retryInterval": "PT1D",
   "retryCount": 2
},
```

| Egenskap | Krävs | Värde | Beskrivning | 
|----------|----------|-------|-------------| 
| **retryType** | Ja | **Fast**, **Ingen** | Avgör om du anger en återförsöksprincip (**fast**) eller inte (**ingen**). | 
| **retryInterval** | Inga | PT30S | Anger intervall och frekvens mellan omförsök i [ISO 8601-format](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations). Minimivärdet är 15 sekunder medan det högsta värdet är 18 månader. | 
| **retryCount** | Inga | 4 | Anger antalet återförsök. Maxvärdet är 20. | 
||||

Mer information finns i [Hög tillgänglighet och tillförlitlighet](../scheduler/scheduler-high-availability-reliability.md).

<a name="status"></a>

## <a name="state"></a>state

Ett jobbs tillstånd är antingen **aktiverat**, **inaktiverat**, **slutfört** eller **fel**, till exempel: 

`"state": "Disabled"`

Ändra jobb till **aktiverat** eller **inaktiverat** tillstånd genom att använda PUT- eller PATCH-åtgärden på de jobben.
Men om ett jobb har tillståndet **slutfört** eller **fel** så kan du inte uppdatera tillståndet, även om du kan utföra DELETE-åtgärden på jobbet. Scheduler tar bort slutförda och feljobb efter 60 dagar. 

<a name="status"></a>

## <a name="status"></a>status

När ett jobb startar så returnerar Scheduler information om jobbets status med hjälp av **status**-objektet, som bara Scheduler styr. Du kan dock hitta **status**-objektet i **jobb** objektet. Här är den information som ett jobbs status innehåller:

* Tid för den föregående körningen, om någon
* Tid för nästa schemalagda körning för pågående jobb
* Antalet jobbkörningar
* Antal misslyckanden om några
* Antal fel om några

Exempel:

```json
"status": {
   "lastExecutionTime": "2007-03-01T13:00:00Z",
   "nextExecutionTime": "2007-03-01T14:00:00Z ",
   "executionCount": 3,
   "failureCount": 0,
   "faultedCount": 0
}
```

## <a name="next-steps"></a>Nästa steg

* [Skapa komplexa scheman och avancerad upprepning](scheduler-advanced-complexity.md)
* [Referens för REST-API:et för Azure Scheduler](/rest/api/scheduler)
* [Referens för PowerShell-cmdlets för Azure Scheduler](scheduler-powershell-reference.md)
* [Gränser, kvoter, standardvärden och felkoder](scheduler-limits-defaults-errors.md)