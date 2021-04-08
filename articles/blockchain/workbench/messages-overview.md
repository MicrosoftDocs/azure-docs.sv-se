---
title: Använda meddelanden för att integrera med Azure blockchain Workbench
description: Översikt över hur du använder meddelanden för att integrera Azure blockchain Workbench Preview med andra system.
ms.date: 09/05/2019
ms.topic: article
ms.reviewer: brendal
ms.openlocfilehash: 14bd0f84bc9490d95d3dbe0b9f122882f0d2059d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "74324511"
---
# <a name="azure-blockchain-workbench-messaging-integration"></a>Integrering av Azure blockchain Workbench-meddelanden

Förutom att tillhandahålla en REST API tillhandahåller Azure blockchain Workbench även meddelandebaserade integreringar. Workbench publicerar insamlade händelser via Azure Event Grid, så att efterföljande användare kan mata in data eller vidta åtgärder baserat på dessa händelser. För de klienter som kräver Reliable Messaging levererar Azure blockchain Workbench meddelanden till en Azure Service Bus-slutpunkt också.

## <a name="input-apis"></a>API: er för indataport

Om du vill initiera transaktioner från externa system för att skapa användare, skapa kontrakt och uppdatera kontrakt, kan du använda API: er för meddelande ingång för att utföra transaktioner i en redovisning. Se [exempel på integration av meddelande hantering](https://aka.ms/blockchain-workbench-integration-sample) för ett exempel som visar API: er för indataport.

Följande är de API: er som är tillgängliga för närvarande.

### <a name="create-user"></a>Skapa användare

Skapar en ny användare.

Begäran kräver följande fält:

| **Namn**             | **Beskrivning**                                      |
|----------------------|------------------------------------------------------|
| requestId            | GUID för klienten har angetts                                |
| firstName            | Användarens förnamn                              |
| lastName             | Användarens efter namn                               |
| emailAddress         | Användarens e-postadress                           |
| externalId           | Användarens Azure AD-objekt-ID                      |
| connectionId         | Unikt ID för blockchain-anslutningen |
| messageSchemaVersion | Meddelande schema version                            |
| messageName          | **CreateUserRequest**                               |

Exempel:

``` json
{
    "requestId": "e2264523-6147-41fc-bbbb-edba8e44562d",
    "firstName": "Ali",
    "lastName": "Alio",
    "emailAddress": "aa@contoso.com",
    "externalId": "6a9b7f65-ffff-442f-b3b8-58a35abd1bcd",
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateUserRequest"
}
```

Blockchain Workbench returnerar ett svar med följande fält:

| **Namn**              | **Beskrivning**                                                                                                             |
|-----------------------|-----------------------------------------------------------------------------------------------------------------------------|
| requestId             | GUID för klienten har angetts |
| userId                | ID för den användare som skapades |
| userChainIdentifier   | Adress till den användare som skapades i blockchain-nätverket. I Ethereum är adressen användarens **on-Chain** -adress. |
| connectionId          | Unikt ID för blockchain-anslutningen|
| messageSchemaVersion  | Meddelande schema version |
| messageName           | **CreateUserUpdate** |
| status                | Status för begäran om att skapa användare.  Om det lyckas är värdet **klart**. Vid haveriering, **Miss lyckas** värdet.     |
| additionalInformation | Ytterligare information som tillhandahålls utifrån status |

Exemplet lyckades **skapa användarens** svar från blockchain Workbench:

``` json
{ 
    "requestId": "e2264523-6147-41fc-bb59-edba8e44562d", 
    "userId": 15, 
    "userChainIdentifier": "0x9a8DDaCa9B7488683A4d62d0817E965E8f248398", 
    "connectionId": 1, 
    "messageSchemaVersion": "1.0.0", 
    "messageName": "CreateUserUpdate", 
    "status": "Success", 
    "additionalInformation": { } 
} 
```

Om begäran Miss lyckas ingår information om felen i ytterligare information.

``` json
{
    "requestId": "e2264523-6147-41fc-bb59-edba8e44562d", 
    "userId": 15, 
    "userChainIdentifier": null, 
    "connectionId": 1, 
    "messageSchemaVersion": "1.0.0", 
    "messageName": "CreateUserUpdate", 
    "status": "Failure", 
    "additionalInformation": { 
        "errorCode": 4000, 
        "errorMessage": "User cannot be provisioned on connection." 
    }
}
```

### <a name="create-contract"></a>Skapa kontrakt

Skapar ett nytt kontrakt.

Begäran kräver följande fält:

| **Namn**             | **Beskrivning**                                                                                                           |
|----------------------|---------------------------------------------------------------------------------------------------------------------------|
| requestId            | GUID för klienten har angetts |
| userChainIdentifier  | Adress till den användare som skapades i blockchain-nätverket. I Ethereum är den här adressen användarens **on Chain** -adress. |
| applicationName      | Programmets namn |
| version              | Programmets version. Krävs om du har flera versioner av programmet aktiverat. Annars är versionen valfri. Mer information om program versioner finns i [Azure blockchain Workbench Application Versioning](version-app.md). |
| workflowName         | Namn på arbets flödet |
| parametrar           | Parametrar för skapande av kontrakt |
| connectionId         | Unikt ID för blockchain-anslutningen |
| messageSchemaVersion | Meddelande schema version |
| messageName          | **CreateContractRequest** |

Exempel:

``` json
{ 
    "requestId": "ce3c429b-a091-4baa-b29b-5b576162b211", 
    "userChainIdentifier": "0x9a8DDaCa9B7488683A4d62d0817E965E8f248398", 
    "applicationName": "AssetTransfer",
    "version": "1.0",
    "workflowName": "AssetTransfer", 
    "parameters": [ 
        { 
            "name": "description", 
            "value": "a 1969 dodge charger" 
        }, 
        { 
            "name": "price", 
            "value": "12345" 
        } 
    ], 
    "connectionId": 1, 
    "messageSchemaVersion": "1.0.0", 
    "messageName": "CreateContractRequest" 
}
```

Blockchain Workbench returnerar ett svar med följande fält:

| **Namn**                 | **Beskrivning**                                                                   |
|--------------------------|-----------------------------------------------------------------------------------|
| requestId                | GUID för klienten har angetts                                                             |
| contractId               | Unikt ID för kontraktet i Azure blockchain Workbench |
| contractLedgerIdentifier | Adress till kontraktet i redovisningen                                            |
| connectionId             | Unikt ID för blockchain-anslutningen                               |
| messageSchemaVersion     | Meddelande schema version                                                         |
| messageName              | **CreateContractUpdate**                                                      |
| status                   | Status för begäran om att skapa kontrakt.  Möjliga värden: **skickade**, **bekräftade**, **felaktiga**.  |
| additionalInformation    | Ytterligare information som tillhandahålls utifrån status                              |

Exempel på ett skickat **kontrakts** svar från blockchain Workbench:

``` json
{
    "requestId": "ce3c429b-a091-4baa-b29b-5b576162b211",
    "contractId": 55,
    "contractLedgerIdentifier": "0xde0B295669a9FD93d5F28D9Ec85E40f4cb697BAe",
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractUpdate",
    "status": "Submitted",
    "additionalInformation": { }
}
```

Exempel på ett dedikerat **skapa kontrakts** svar från blockchain Workbench:

``` json
{
    "requestId": "ce3c429b-a091-4baa-b29b-5b576162b211",
    "contractId": 55,
    "contractLedgerIdentifier": "0xde0B295669a9FD93d5F28D9Ec85E40f4cb697BAe",
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractUpdate",
    "status": "Committed",
    "additionalInformation": { }
}
```

Om begäran Miss lyckas ingår information om felen i ytterligare information.

``` json
{
    "requestId": "ce3c429b-a091-4baa-b29b-5b576162b211",
    "contractId": 55,
    "contractLedgerIdentifier": null,
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractUpdate",
    "status": "Failure",
    "additionalInformation": {
        "errorCode": 4000,
        "errorMessage": "Contract cannot be provisioned on connection."
    }
}
```

### <a name="create-contract-action"></a>Skapa kontrakts åtgärd

Skapar en ny kontrakts åtgärd.

Begäran kräver följande fält:

| **Namn**                 | **Beskrivning**                                                                                                           |
|--------------------------|---------------------------------------------------------------------------------------------------------------------------|
| requestId                | GUID för klienten har angetts |
| userChainIdentifier      | Adress till den användare som skapades i blockchain-nätverket. I Ethereum är den här adressen användarens **on Chain** -adress. |
| contractLedgerIdentifier | Adress till kontraktet i redovisningen |
| version                  | Programmets version. Krävs om du har flera versioner av programmet aktiverat. Annars är versionen valfri. Mer information om program versioner finns i [Azure blockchain Workbench Application Versioning](version-app.md). |
| workflowFunctionName     | Namn på arbets flödes funktionen |
| parametrar               | Parametrar för skapande av kontrakt |
| connectionId             | Unikt ID för blockchain-anslutningen |
| messageSchemaVersion     | Meddelande schema version |
| messageName              | **CreateContractActionRequest** |

Exempel:

``` json
{
    "requestId": "a5530932-9d6b-4eed-8623-441a647741d3",
    "userChainIdentifier": "0x9a8DDaCa9B7488683A4d62d0817E965E8f248398",
    "contractLedgerIdentifier": "0xde0B295669a9FD93d5F28D9Ec85E40f4cb697BAe",
    "version": "1.0",
    "workflowFunctionName": "modify",
    "parameters": [
        {
            "name": "description",
            "value": "a 1969 dodge charger"
        },
        {
            "name": "price",
            "value": "12345"
        }
    ],
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractActionRequest"
}
```

Blockchain Workbench returnerar ett svar med följande fält:

| **Namn**              | **Beskrivning**                                                                   |
|-----------------------|-----------------------------------------------------------------------------------|
| requestId             | GUID för klienten har angetts|
| contractId            | Unikt ID för kontraktet i Azure blockchain Workbench |
| connectionId          | Unikt ID för blockchain-anslutningen |
| messageSchemaVersion  | Meddelande schema version |
| messageName           | **CreateContractActionUpdate** |
| status                | Status för kontrakts åtgärds förfrågan. Möjliga värden: **skickade**, **bekräftade**, **felaktiga**.                         |
| additionalInformation | Ytterligare information som tillhandahålls utifrån status |

Exempel på en skickad åtgärds svar för att **skapa kontrakt** från blockchain Workbench:

``` json
{
    "requestId": "a5530932-9d6b-4eed-8623-441a647741d3",
    "contractId": 105,
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractActionUpdate",
    "status": "Submitted",
    "additionalInformation": { }
}
```

Exempel på ett dedikerat **skapa kontrakt åtgärds** svar från blockchain Workbench:

``` json
{
    "requestId": "a5530932-9d6b-4eed-8623-441a647741d3",
    "contractId": 105,
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractActionUpdate",
    "status": "Committed",
    "additionalInformation": { }
}
```

Om begäran Miss lyckas ingår information om felen i ytterligare information.

``` json
{
    "requestId": "a5530932-9d6b-4eed-8623-441a647741d3",
    "contractId": 105,
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractActionUpdate",
    "status": "Failure",
    "additionalInformation": {
        "errorCode": 4000,
        "errorMessage": "Contract action cannot be provisioned on connection."
    }
}
```

### <a name="input-api-error-codes-and-messages"></a>Felkoder och meddelanden för API för indataport

**Felkod 4000: fel vid felaktig begäran**
- Ogiltig connectionId
- CreateUserRequest-deserialiseringen misslyckades
- CreateContractRequest-deserialiseringen misslyckades
- CreateContractActionRequest-deserialiseringen misslyckades
- Programmet {som identifieras med program namnet} finns inte
- Programmet {som identifieras med program namnet} saknar arbets flöde
- UserChainIdentifier finns inte
- Kontraktet {identifieras av Ledger-ID} finns inte
- Kontraktet {som identifieras av redovisnings-ID: n} saknar funktion {arbets flödes funktions namn}
- UserChainIdentifier finns inte

**Felkod 4090: konflikt fel**
- Användaren finns redan
- Kontraktet finns redan
- Kontrakt åtgärden finns redan

**Felkod 5000: internt Server fel**
- Undantags meddelanden

## <a name="event-notifications"></a>Händelseaviseringar

Händelse aviseringar kan användas för att meddela användare och underordnade system om händelser som sker i blockchain Workbench och det blockchain nätverk som det är anslutet till. Händelse aviseringar kan förbrukas direkt i kod eller användas med verktyg som Logic Apps och Flow för att utlösa data flöde till underordnade system.

Se [referens för meddelande](#notification-message-reference) meddelanden för information om olika meddelanden som kan tas emot.

### <a name="consuming-event-grid-events-with-azure-functions"></a>Konsumera Event Grid händelser med Azure Functions

Om en användare vill använda Event Grid för att få meddelanden om händelser som inträffar i blockchain Workbench, kan du använda händelser från Event Grid med Azure Functions.

1. Skapa ett **Azure-Funktionsapp** i Azure Portal.
2. Skapa en ny funktion.
3. Leta upp mallen för Event Grid. Basic-mallkod för att läsa meddelandet visas. Ändra koden efter behov.
4. Spara funktionen. 
5. Välj Event Grid från blockchain Workbenchs resurs grupp.

### <a name="consuming-event-grid-events-with-logic-apps"></a>Konsumera Event Grid händelser med Logic Apps

1. Skapa en ny **Azure Logic-app** i Azure Portal.
2. När du öppnar Azure Logic-appen i portalen uppmanas du att välja en utlösare. Välj **Azure Event Grid – när en resurs händelse inträffar**.
3. När arbetsflödesdesignern visas uppmanas du att logga in.
4. Välj prenumerationen. Resurs som **Microsoft. EventGrid. ämnen**. Välj **resurs namnet** från namnet på resursen från resurs gruppen Azure blockchain Workbench.
5. Välj Event Grid från blockchain Workbenchs resurs grupp.

## <a name="using-service-bus-topics-for-notifications"></a>Använda Service Bus ämnen för meddelanden

Service Bus ämnen kan användas för att meddela användare om händelser som inträffar i blockchain Workbench. 

1. Bläddra till Service Bus i arbets stationens resurs grupp.
2. Välj **Ämnen**.
3. Välj **utgående – ämne**.
4. Skapa en ny prenumeration på det här ämnet. Hämta en nyckel för den.
5. Skapa ett program som prenumererar på händelser från den här prenumerationen.

### <a name="consuming-service-bus-messages-with-logic-apps"></a>Använda Service Bus meddelanden med Logic Apps

1. Skapa en ny **Azure Logic-app** i Azure Portal.
2. När du öppnar Azure Logic-appen i portalen uppmanas du att välja en utlösare. Skriv **Service Bus** i sökrutan och välj den utlösare som är lämplig för den typ av interaktion som du vill ha med Service Bus. Till exempel **Service Bus – när ett meddelande tas emot i en ämnes prenumeration (komplettera automatiskt)**.
3. När arbetsflödesdesignern visas anger du anslutnings informationen för Service Bus.
4. Välj din prenumeration och ange ämnet i **Workbench-external**.
5. Utveckla logiken för ditt program som använder meddelandet från den här utlösaren.

## <a name="notification-message-reference"></a>Referens för meddelande meddelande

Beroende på **messageName** har aviseringarna en av följande typer av meddelanden.

### <a name="block-message"></a>Blockera meddelande

Innehåller information om enskilda block. *BlockMessage* innehåller ett avsnitt med information om block nivå och ett avsnitt med transaktionsinformation.

| Name | Beskrivning |
|------|-------------|
| blockera | Innehåller [block information](#block-information) |
| transaktioner | Innehåller en samlings [transaktions information](#transaction-information) för blocket |
| connectionId | Unikt ID för anslutningen |
| messageSchemaVersion | Meddelande schema version |
| messageName | **BlockMessage** |
| additionalInformation | Ytterligare information har angetts |

#### <a name="block-information"></a>Blockera information

| Name              | Beskrivning |
|-------------------|-------------|
| Block           | Unikt ID för blocket i Azure blockchain Workbench |
| blockNumber       | Unikt ID för ett block i redovisningen |
| blockHash         | Hash för blocket |
| previousBlockHash | Hash för det tidigare blocket |
| blockTimestamp    | Tidsstämpeln för blocket |

#### <a name="transaction-information"></a>Transaktionsinformation

| Name               | Beskrivning |
|--------------------|-------------|
| transactionId      | Unikt ID för transaktionen i Azure blockchain Workbench |
| transactionHash    | Hash för transaktionen i redovisningen |
| Från               | Unikt ID i redovisningen för transaktions ursprunget |
| på                 | Unikt ID i redovisningen för transaktions målet |
| provisioningStatus | Identifierar den aktuella statusen för etablerings processen för transaktionen. Möjliga värden: </br>0 – transaktionen har skapats av API: t i databasen</br>1 – transaktionen har skickats till redovisningen</br>2 – transaktionen har allokerats till redovisningen</br>3 eller 4-transaktionen kunde inte allokeras till redovisningen</br>5 – transaktionen har allokerats till redovisningen |

Exempel på en *BlockMessage* från blockchain Workbench:

``` json
{
    "block": {
        "blockId": 123,
        "blockNumber": 1738312,
        "blockHash": "0x03a39411e25e25b47d0ec6433b73b488554a4a5f6b1a253e0ac8a200d13fffff",
        "previousBlockHash": null,
        "blockTimestamp": "2018-10-09T23:35:58Z",
    },
    "transactions": [
        {
            "transactionId": 234,
            "transactionHash": "0xa4d9c95b581f299e41b8cc193dd742ef5a1d3a4ddf97bd11b80d123fec27ffff",
            "from": "0xd85e7262dd96f3b8a48a8aaf3dcdda90f60dffff",
            "to": null,
            "provisioningStatus": 1
        },
        {
            "transactionId": 235,
            "transactionHash": "0x5c1fddea83bf19d719e52a935ec8620437a0a6bdaa00ecb7c3d852cf92e1ffff",
            "from": "0xadd97e1e595916e29ea94fda894941574000ffff",
            "to": "0x9a8DDaCa9B7488683A4d62d0817E965E8f24ffff",
            "provisioningStatus": 2
        }
    ],
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "BlockMessage",
    "additionalInformation": {}
}
```

### <a name="contract-message"></a>Kontrakts meddelande

Innehåller information om ett kontrakt. Meddelandet innehåller ett avsnitt med kontrakt egenskaper och ett avsnitt med transaktionsinformation. Alla transaktioner som har ändrat kontraktet för det specifika blocket ingår i transaktions avsnittet.

| Name | Beskrivning |
|------|-------------|
| Block | Unikt ID för blocket i Azure blockchain Workbench |
| blockHash | Hash för blocket |
| modifyingTransactions | [Transaktioner som ändrade](#modifying-transaction-information) kontraktet |
| contractId | Unikt ID för kontraktet i Azure blockchain Workbench |
| contractLedgerIdentifier | Unik identifierare för kontraktet i redovisningen |
| contractProperties | [Kontraktets egenskaper](#contract-properties) |
| isNewContract | Anger om det här kontraktet har skapats nyligen. Möjliga värden är: sant: det här kontraktet var ett nytt kontrakt skapat. falskt: det här kontraktet är ett kontrakts uppdatering. |
| connectionId | Unikt ID för anslutningen |
| messageSchemaVersion | Meddelande schema version |
| messageName | **ContractMessage** |
| additionalInformation | Ytterligare information har angetts |

#### <a name="modifying-transaction-information"></a>Ändra transaktionsinformation

| Name               | Beskrivning |
|--------------------|-------------|
| transactionId | Unikt ID för transaktionen i Azure blockchain Workbench |
| transactionHash | Hash för transaktionen i redovisningen |
| Från | Unikt ID i redovisningen för transaktions ursprunget |
| på | Unikt ID i redovisningen för transaktions målet |

#### <a name="contract-properties"></a>Kontrakts egenskaper

| Name               | Beskrivning |
|--------------------|-------------|
| workflowPropertyId | Unikt ID för arbets flödes egenskapen i Azure blockchain Workbench |
| name | Namn på arbets flödes egenskapen |
| värde | Värdet för arbets flödes egenskapen |

Exempel på en *ContractMessage* från blockchain Workbench:

``` json
{
    "blockId": 123,
    "blockhash": "0x03a39411e25e25b47d0ec6433b73b488554a4a5f6b1a253e0ac8a200d13fffff",
    "modifyingTransactions": [
        {
            "transactionId": 234,
            "transactionHash": "0x5c1fddea83bf19d719e52a935ec8620437a0a6bdaa00ecb7c3d852cf92e1ffff",
            "from": "0xd85e7262dd96f3b8a48a8aaf3dcdda90f60dffff",
            "to": "0xf8559473b3c7197d59212b401f5a9f07ffff"
        },
        {
            "transactionId": 235,
            "transactionHash": "0xa4d9c95b581f299e41b8cc193dd742ef5a1d3a4ddf97bd11b80d123fec27ffff",
            "from": "0xd85e7262dd96f3b8a48a8aaf3dcdda90f60dffff",
            "to": "0xf8559473b3c7197d59212b401f5a9f07b429ffff"
        }
    ],
    "contractId": 111,
    "contractLedgerIdentifier": "0xf8559473b3c7197d59212b401f5a9f07b429ffff",
    "contractProperties": [
        {
            "workflowPropertyId": 1,
            "name": "State",
            "value": "0"
        },
        {
            "workflowPropertyId": 2,
            "name": "Description",
            "value": "1969 Dodge Charger"
        },
        {
            "workflowPropertyId": 3,
            "name": "AskingPrice",
            "value": "30000"
        },
        {
            "workflowPropertyId": 4,
            "name": "OfferPrice",
            "value": "0"
        },
        {
            "workflowPropertyId": 5,
            "name": "InstanceAppraiser",
            "value": "0x0000000000000000000000000000000000000000"
        },
        {
            "workflowPropertyId": 6,
            "name": "InstanceBuyer",
            "value": "0x0000000000000000000000000000000000000000"
        },
        {
            "workflowPropertyId": 7,
            "name": "InstanceInspector",
            "value": "0x0000000000000000000000000000000000000000"
        },
        {
            "workflowPropertyId": 8,
            "name": "InstanceOwner",
            "value": "0x9a8DDaCa9B7488683A4d62d0817E965E8f24ffff"
        },
        {
            "workflowPropertyId": 9,
            "name": "ClosingDayOptions",
            "value": "[21,48,69]"
        }
    ],
    "isNewContract": false,
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "ContractMessage",
    "additionalInformation": {}
}
```

### <a name="event-message-contract-function-invocation"></a>Händelse meddelande: kontrakts funktion anrop

Innehåller information när en kontrakts funktion anropas, t. ex. funktions namn, parameter indata och anroparen för funktionen.

| Name | Beskrivning |
|------|-------------|
| eventName                   | **ContractFunctionInvocation** |
| anroparen                      | [Information om anropare](#caller-information) |
| contractId                  | Unikt ID för kontraktet i Azure blockchain Workbench |
| contractLedgerIdentifier    | Unik identifierare för kontraktet i redovisningen |
| functionName                | Namnet på funktionen |
| parametrar                  | [Parameterinformation](#parameter-information) |
| transaktionen                 | Transaktionsinformation |
| inTransactionSequenceNumber | Ordnings numret för transaktionen i blocket |
| connectionId                | Unikt ID för anslutningen |
| messageSchemaVersion        | Meddelande schema version |
| messageName                 | **EventMessage** |
| additionalInformation       | Ytterligare information har angetts |

#### <a name="caller-information"></a>Information om anropare

| Name | Beskrivning |
|------|-------------|
| typ | Typ av anropare, t. ex. en användare eller ett kontrakt |
| id | Unikt ID för anroparen i Azure blockchain Workbench |
| ledgerIdentifier | Unikt ID för anroparen i redovisningen |

#### <a name="parameter-information"></a>Parameterinformation

| Name | Beskrivning |
|------|-------------|
| name | Parameternamn |
| värde | Parametervärde |

#### <a name="event-message-transaction-information"></a>Transaktions information för händelse meddelande

| Name               | Beskrivning |
|--------------------|-------------|
| transactionId      | Unikt ID för transaktionen i Azure blockchain Workbench |
| transactionHash    | Hash för transaktionen i redovisningen |
| Från               | Unikt ID i redovisningen för transaktions ursprunget |
| på                 | Unikt ID i redovisningen för transaktions målet |

Exempel på en *EventMessage-ContractFunctionInvocation* från blockchain Workbench:

``` json
{
    "eventName": "ContractFunctionInvocation",
    "caller": {
        "type": "User",
        "id": 21,
        "ledgerIdentifier": "0xd85e7262dd96f3b8a48a8aaf3dcdda90f60ffff"
    },
    "contractId": 34,
    "contractLedgerIdentifier": "0xf8559473b3c7197d59212b401f5a9f07b429ffff",
    "functionName": "Modify",
    "parameters": [
        {
            "name": "description",
            "value": "a new description"
        },
        {
            "name": "price",
            "value": "4567"
        }
    ],
    "transaction": {
        "transactionId": 234,
        "transactionHash": "0x5c1fddea83bf19d719e52a935ec8620437a0a6bdaa00ecb7c3d852cf92e1ffff",
        "from": "0xd85e7262dd96f3b8a48a8aaf3dcdda90f60dffff",
        "to": "0xf8559473b3c7197d59212b401f5a9f07b429ffff"
    },
    "inTransactionSequenceNumber": 1,
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "EventMessage",
    "additionalInformation": { }
}
```

### <a name="event-message-application-ingestion"></a>Händelse meddelande: program inmatning

Innehåller information när ett program laddas upp till Workbench, till exempel namn och version för det program som laddas upp.

| Name | Beskrivning |
|------|-------------|
| eventName | **ApplicationIngestion** |
| applicationId | Unikt ID för programmet i Azure blockchain Workbench |
| applicationName | Programnamn |
| applicationDisplayName | Programmets visnings namn |
| applicationVersion | Program version |
| applicationDefinitionLocation | URL där programmets konfigurations fil finns |
| contractCodes | Samling av [kontrakt koder](#contract-code-information) för programmet |
| applicationRoles | Samling av [program roller](#application-role-information) för programmet |
| applicationWorkflows | Samling av [program arbets flöden](#application-workflow-information) för programmet |
| connectionId | Unikt ID för anslutningen |
| messageSchemaVersion | Meddelande schema version |
| messageName | **EventMessage** |
| additionalInformation | Ytterligare information som anges här inkluderar programmets arbets flödes tillstånd och över gångs information. |

#### <a name="contract-code-information"></a>Information om kontrakt kod

| Name | Beskrivning |
|------|-------------|
| id | Unik identifierare för kontrakts kod filen i Azure blockchain Workbench |
| ledgerId | Unikt ID för redovisningen i Azure blockchain Workbench |
| location | URL där kontrakt kod filen finns |

#### <a name="application-role-information"></a>Information om program roll

| Name | Beskrivning |
|------|-------------|
| id | Unikt ID för program rollen i Azure blockchain Workbench |
| name | Namn på program rollen |

#### <a name="application-workflow-information"></a>Information om program arbets flöde

| Name | Beskrivning |
|------|-------------|
| id | Unikt ID för program arbets flödet i Azure blockchain Workbench |
| name | Namn på program arbets flöde |
| displayName | Visnings namn för program arbets flöde |
| funktionen | Insamling av [funktioner för program arbets flödet](#workflow-function-information)|
| USA | Insamling av [tillstånd för program arbets flödet](#workflow-state-information) |
| properties | Information om program [arbets flödets egenskaper](#workflow-property-information) |

##### <a name="workflow-function-information"></a>Information om arbets flödes funktion

| Name | Beskrivning |
|------|-------------|
| id | Unik identifierare för program arbets flödes funktionen i Azure blockchain Workbench |
| name | Funktionsnamn |
| parametrar | Parametrar för funktionen |

##### <a name="workflow-state-information"></a>Information om arbets flödes tillstånd

| Name | Beskrivning |
|------|-------------|
| name | Tillstånds namn |
| displayName | Visnings namn för tillstånd |
| stil | Tillstånds format (lyckades eller misslyckades) |

##### <a name="workflow-property-information"></a>Information om arbets flödes egenskaper

| Name | Beskrivning |
|------|-------------|
| id | Unikt ID för egenskapen för program arbets flödet i Azure blockchain Workbench |
| name | Egenskapsnamn |
| typ | Egenskaps typ |

Exempel på en *EventMessage-ApplicationIngestion* från blockchain Workbench:

``` json
{
    "eventName": "ApplicationIngestion",
    "applicationId": 31,
    "applicationName": "AssetTransfer",
    "applicationDisplayName": "Asset Transfer",
    "applicationVersion": "1.0",
    "applicationDefinitionLocation": "http://url",
    "contractCodes": [
        {
            "id": 23,
            "ledgerId": 1,
            "location": "http://url"
        }
    ],
    "applicationRoles": [
            {
                "id": 134,
                "name": "Buyer"
            },
            {
                "id": 135,
                "name": "Seller"
            }
       ],
    "applicationWorkflows": [
        {
            "id": 89,
            "name": "AssetTransfer",
            "displayName": "Asset Transfer",
            "functions": [
                {
                    "id": 912,
                    "name": "",
                    "parameters": [
                        {
                            "name": "description",
                            "type": {
                                "name": "string"
                             }
                        },
                        {
                            "name": "price",
                            "type": {
                                "name": "int"
                            }
                        }
                    ]
                },
                {
                    "id": 913,
                    "name": "modify",
                    "parameters": [
                        {
                            "name": "description",
                            "type": {
                                "name": "string"
                             }
                        },
                        {
                            "name": "price",
                            "type": {
                                "name": "int"
                            }
                        }
                    ]
                }
            ],
            "states": [ 
                 {
                      "name": "Created",
                      "displayName": "Created",
                      "style" : "Success"
                 },
                 {
                      "name": "Terminated",
                      "displayName": "Terminated",
                      "style" : "Failure"
                 }
            ],
            "properties": [
                {
                    "id": 879,
                    "name": "Description",
                    "type": {
                        "name": "string"
                     }
                },
                {
                    "id": 880,
                    "name": "Price",
                    "type": {
                        "name": "int"
                     }
                }
            ]
        }
    ],
    "connectionId": [ ],
    "messageSchemaVersion": "1.0.0",
    "messageName": "EventMessage",
    "additionalInformation":
        {
            "states" :
            [
                {
                    "Name": "BuyerAccepted",
                    "Transitions": [
                        {
                            "DisplayName": "Accept",
                            "AllowedRoles": [ ],
                            "AllowedInstanceRoles": [ "InstanceOwner" ],
                            "Function": "Accept",
                            "NextStates": [ "SellerAccepted" ]
                        }
                    ]
                }
            ]
        }
}
```

### <a name="event-message-role-assignment"></a>Händelse meddelande: roll tilldelning

Innehåller information när en användare tilldelas en roll i Workbench, till exempel vem som utförde roll tilldelningen och namnet på rollen och motsvarande program.

| Name | Beskrivning |
|------|-------------|
| eventName | **RoleAssignment** |
| applicationId | Unikt ID för programmet i Azure blockchain Workbench |
| applicationName | Programnamn |
| applicationDisplayName | Programmets visnings namn |
| applicationVersion | Program version |
| applicationRole        | Information om [program rollen](#roleassignment-application-role) |
| tilldelare               | Information om [tilldelningen](#roleassignment-assigner) |
| tilldelad person               | Information om [tilldelningen](#roleassignment-assignee) |
| connectionId           | Unikt ID för anslutningen |
| messageSchemaVersion   | Meddelande schema version |
| messageName            | **EventMessage** |
| additionalInformation  | Ytterligare information har angetts |

#### <a name="roleassignment-application-role"></a>RoleAssignment program roll

| Name | Beskrivning |
|------|-------------|
| id | Unikt ID för program rollen i Azure blockchain Workbench |
| name | Namn på program rollen |

#### <a name="roleassignment-assigner"></a>RoleAssignment tilldelas

| Name | Beskrivning |
|------|-------------|
| id | Unikt ID för användaren i Azure blockchain Workbench |
| typ | Typ av tilldelare |
| chainIdentifier | Unikt ID för användaren i redovisningen |

#### <a name="roleassignment-assignee"></a>RoleAssignment tilldelas

| Name | Beskrivning |
|------|-------------|
| id | Unikt ID för användaren i Azure blockchain Workbench |
| typ | Typ av tilldelad person |
| chainIdentifier | Unikt ID för användaren i redovisningen |

Exempel på en *EventMessage-RoleAssignment* från blockchain Workbench:

``` json
{
    "eventName": "RoleAssignment",
    "applicationId": 31,
    "applicationName": "AssetTransfer",
    "applicationDisplayName": "Asset Transfer",
    "applicationVersion": "1.0",
    "applicationRole": {
        "id": 134,
        "name": "Buyer"
    },
    "assigner": {
        "id": 1,
        "type": null,
        "chainIdentifier": "0xeFFC7766d38aC862d79706c3C5CEEf089564ffff"
    },
    "assignee": {
        "id": 3,
        "type": null,
        "chainIdentifier": "0x9a8DDaCa9B7488683A4d62d0817E965E8f24ffff"
    },
    "connectionId": [ ],
    "messageSchemaVersion": "1.0.0",
    "messageName": "EventMessage",
    "additionalInformation": { }
}
```

## <a name="next-steps"></a>Nästa steg

- [Integreringsmönster för smart kontrakt](integration-patterns.md)
