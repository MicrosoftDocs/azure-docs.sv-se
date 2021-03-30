---
title: Skapa ett blockchain-program – Azure blockchain Workbench
description: Självstudie om hur du skapar ett blockchain-program för för hands versionen av Azure blockchain Workbench.
ms.date: 08/24/2020
ms.topic: tutorial
ms.reviewer: ravastra
ms.openlocfilehash: add790a069d2e0ea66d84bbd632825cf9331fd38
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "88784036"
---
# <a name="tutorial-create-a-blockchain-application-for-azure-blockchain-workbench"></a>Självstudie: skapa ett blockchain-program för Azure blockchain Workbench

Du kan använda Azure Blockchain Workbench för att skapa blockkedjeprogram som representerar arbetsflöden med flera parter som definieras av konfiguration och smart kontaktkod.

Du lär dig följande:

> [!div class="checklist"]
> * Konfigurera ett blockkedjeprogram
> * Skapa en fil med smart kontraktkod
> * Lägga till ett blockkedjeprogram i Blockchain Workbench
> * Lägga till medlemmar i blockkedjeprogrammet

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Förutsättningar

* En Blockchain Workbench-distribution. Mer information om distributioner finns i avsnittet om [Azure Blockchain Workbench-distributioner](deploy.md).
* Azure Active Directory-användare i klientorganisationen som är associerade med Blockchain Workbench. Mer information finns i avsnittet om att [lägga till Azure AD-användare i Azure Blockchain Workbench](manage-users.md#add-azure-ad-users).
* Ett Blockchain Workbench-administratörskonto. Mer information finns i avsnittet om att [lägga till Blockchain Workbench-administratörer i Azure Blockchain Workbench](manage-users.md#manage-blockchain-workbench-administrators).

## <a name="hello-blockchain"></a>Hej blockkedjan!

Nu skapar vi ett grundläggande program där en begärare skickar en begäran och en svarare skickar ett svar på begäran.
En ansökan kan till exempel vara ”Hej! Hur är det?”, och svaret kan vara ”Det är toppen!”. Både begäran och svaret registreras på den underliggande blockkedjan.

Följ stegen för att skapa programfilerna, eller så kan du [ladda ned exemplet från GitHub](https://github.com/Azure-Samples/blockchain/tree/master/blockchain-workbench/application-and-smart-contract-samples/hello-blockchain).

## <a name="configuration-file"></a>Konfigurationsfil

Konfigurationsmetadata definierar avancerade arbetsflöden och interaktionsmodellen för blockkedjeprogrammet. Konfigurationsmetadata definierar arbetsflödesstegen och interaktionsmodellen för blockkedjeprogrammet. Mer information om innehållet i konfigurationsfiler finns i [referensen för konfiguration av Azure-blockkedjearbetsflöden](configuration.md).

1. Använd det redigeringsprogram du föredrar och skapa en fil med namnet `HelloBlockchain.json`.
2. Lägg till följande JSON för att definiera konfigurationen av blockkedjeprogrammet.

    ``` json
    {
      "ApplicationName": "HelloBlockchain",
      "DisplayName": "Hello, Blockchain!",
      "Description": "A simple application to send request and get response",
      "ApplicationRoles": [
        {
          "Name": "Requestor",
          "Description": "A person sending a request."
        },
        {
          "Name": "Responder",
          "Description": "A person responding to a request"
        }
      ],
      "Workflows": [
        {
          "Name": "HelloBlockchain",
          "DisplayName": "Request Response",
          "Description": "A simple workflow to send a request and receive a response.",
          "Initiators": [ "Requestor" ],
          "StartState": "Request",
          "Properties": [
            {
              "Name": "State",
              "DisplayName": "State",
              "Description": "Holds the state of the contract.",
              "Type": {
                "Name": "state"
              }
            },
            {
              "Name": "Requestor",
              "DisplayName": "Requestor",
              "Description": "A person sending a request.",
              "Type": {
                "Name": "Requestor"
              }
            },
            {
              "Name": "Responder",
              "DisplayName": "Responder",
              "Description": "A person sending a response.",
              "Type": {
                "Name": "Responder"
              }
            },
            {
              "Name": "RequestMessage",
              "DisplayName": "Request Message",
              "Description": "A request message.",
              "Type": {
                "Name": "string"
              }
            },
            {
              "Name": "ResponseMessage",
              "DisplayName": "Response Message",
              "Description": "A response message.",
              "Type": {
                "Name": "string"
              }
            }
          ],
          "Constructor": {
            "Parameters": [
              {
                "Name": "message",
                "Description": "...",
                "DisplayName": "Request Message",
                "Type": {
                  "Name": "string"
                }
              }
            ]
          },
          "Functions": [
            {
              "Name": "SendRequest",
              "DisplayName": "Request",
              "Description": "...",
              "Parameters": [
                {
                  "Name": "requestMessage",
                  "Description": "...",
                  "DisplayName": "Request Message",
                  "Type": {
                    "Name": "string"
                  }
                }
              ]
            },
            {
              "Name": "SendResponse",
              "DisplayName": "Response",
              "Description": "...",
              "Parameters": [
                {
                  "Name": "responseMessage",
                  "Description": "...",
                  "DisplayName": "Response Message",
                  "Type": {
                    "Name": "string"
                  }
                }
              ]
            }
          ],
          "States": [
            {
              "Name": "Request",
              "DisplayName": "Request",
              "Description": "...",
              "PercentComplete": 50,
              "Value": 0,
              "Style": "Success",
              "Transitions": [
                {
                  "AllowedRoles": ["Responder"],
                  "AllowedInstanceRoles": [],
                  "Description": "...",
                  "Function": "SendResponse",
                  "NextStates": [ "Respond" ],
                  "DisplayName": "Send Response"
                }
              ]
            },
            {
              "Name": "Respond",
              "DisplayName": "Respond",
              "Description": "...",
              "PercentComplete": 90,
              "Value": 1,
              "Style": "Success",
              "Transitions": [
                {
                  "AllowedRoles": [],
                  "AllowedInstanceRoles": ["Requestor"],
                  "Description": "...",
                  "Function": "SendRequest",
                  "NextStates": [ "Request" ],
                  "DisplayName": "Send Request"
                }
              ]
            }
          ]
        }
      ]
    }
    ```

3. Spara filen `HelloBlockchain.json`.

Konfigurationsfilen har flera avsnitt. Information om varje avsnitt är följande:

### <a name="application-metadata"></a>Programmetadata

Början av konfigurationsfilen innehåller information om programmet, däribland programnamn och beskrivning.

### <a name="application-roles"></a>Programroller

Avsnittet med programroller definierar de användarroller som kan agera eller delta i blockkedjeprogrammet. Du definierar en uppsättning med olika roller som baseras på funktionalitet. I scenariot med begäran–svar finns det en distinktion mellan funktionaliteten hos entiteten begärare, som skapar begäranden, och entiteten svarare, som skapar svar.

### <a name="workflows"></a>Arbetsflöden

Arbetsflöden definierar en eller flera steg och åtgärder i kontraktet. I scenariot med begäran–svar är det första steget (tillståndet) i arbetsflödet att en begärare (roll) vidtar en åtgärd (övergång) för att skicka en begäran (funktion). Nästa steg (tillstånd) är att en svarare (roll) vidtar en åtgärd (övergång) för att skicka ett svar (funktion). Arbetsflödet för ett program kan omfatta egenskaper, funktioner och tillstånd som krävs för att beskriva flödet i ett kontrakt.

## <a name="smart-contract-code-file"></a>Fil med smart kontraktkod

Smarta kontrakt representerar affärslogiken i blockkedjeprogrammet. För närvarande har Blockchain Workbench stöd för Ethereum för blockkedjeregistret. Ethereum använder [Solidity](https://solidity.readthedocs.io) som programmeringsspråk för att skriva självtillämpande affärslogik för smarta kontrakt.

Smarta kontrakt i Solidity liknar klasser i objektorienterade språk. Varje kontrakt innehåller tillstånd och funktioner för att implementera steg och åtgärder i det smarta kontraktet.

Använd det redigeringsprogram du föredrar och skapa en fil som heter `HelloBlockchain.sol`.

### <a name="version-pragma"></a>Pragmaversion

Som bästa praxis anger du den version av Solidity som du har som mål. Genom att ange versionen minskar du risken för inkompatibiliteter med framtida Solidity-versioner.

Lägg till följande pragmaversion längst upp i filen `HelloBlockchain.sol` med smart kontraktkod.

``` solidity
pragma solidity >=0.4.25 <0.6.0;
```

### <a name="configuration-and-smart-contract-code-relationship"></a>Konfiguration och kodrelation i smart kontrakt

Blockchain Workbench använder konfigurationsfilen och filen med smart kontraktkod för att skapa ett blockkedjeprogram. Det finns en relation mellan det som definieras i konfigurationen och koden i det smarta kontraktet. Kontraktsinformation, funktioner, parametrar och typer krävs för matchning för att skapa programmet. Blockchain Workbench verifierar filerna innan programmet skapas.

### <a name="contract"></a>Kontrakt

Lägg till huvudet **kontrakt** i filen `HelloBlockchain.sol` med smart kontraktkod.

``` solidity
contract HelloBlockchain {
```

### <a name="state-variables"></a>Tillståndsvariabler

Tillståndsvariabler lagrar värden för tillståndet för varje kontraktsinstans. Tillståndsvariablerna i ditt kontrakt måste matcha de arbetsflödesegenskaper som definieras i konfigurationsfilen.

Lägg till tillståndsvariablerna i ditt kontrakt i filen `HelloBlockchain.sol` med smart kontraktkod.

``` solidity
    //Set of States
    enum StateType { Request, Respond}

    //List of properties
    StateType public  State;
    address public  Requestor;
    address public  Responder;

    string public RequestMessage;
    string public ResponseMessage;
```

### <a name="constructor"></a>Konstruktor

Konstruktorn definierar indataparametrar för en ny instans med smart kontrakt för ett arbetsflöde. Obligatoriska parametrar för konstruktorn definieras som konstruktorparametrar i konfigurationsfilen. Antalet, ordningen och typen av parametrar måste stämma överens i båda filerna.

I konstruktorfunktionen skriver du eventuell affärslogik som du vill utföra innan du skapar kontraktet. Till exempel initierar du tillståndsvariablerna med startvärden.

Lägg till konstruktorfunktionen i ditt kontrakt i filen `HelloBlockchain.sol` med smart kontraktkod.

``` solidity
    // constructor function
    constructor(string memory message) public
    {
        Requestor = msg.sender;
        RequestMessage = message;
        State = StateType.Request;
    }
```

### <a name="functions"></a>Functions

Funktioner är körbara enheter med affärslogik i ett kontrakt. Obligatoriska parametrar för funktionen definieras som funktionsparametrar i konfigurationsfilen. Antalet, ordningen och typen av parametrar måste stämma överens i båda filerna. Funktioner är associerade med övergångar i ett Blockchain Workbench-arbetsflöde i konfigurationsfilen. En övergång är en åtgärd som utförs för en flytt till nästa steg i ett programs arbetsflöde enligt vad som bestäms av kontraktet.

Skriva eventuell affärslogik som du vill utföra i funktionen. Till exempel kan du ändra värdet för en tillståndsvariabel.

1. Lägg till följande funktioner i ditt kontrakt i filen `HelloBlockchain.sol` med smart kontraktkod.

    ``` solidity
        // call this function to send a request
        function SendRequest(string memory requestMessage) public
        {
            if (Requestor != msg.sender)
            {
                revert();
            }
    
            RequestMessage = requestMessage;
            State = StateType.Request;
        }
    
        // call this function to send a response
        function SendResponse(string memory responseMessage) public
        {
            Responder = msg.sender;
    
            ResponseMessage = responseMessage;
            State = StateType.Respond;
        }
    }
    ```

2. Spara filen `HelloBlockchain.sol` med smart kontraktkod.

## <a name="add-blockchain-application-to-blockchain-workbench"></a>Lägga till ett blockkedjeprogram i Blockchain Workbench

Om du vill lägga till ett blockkedjeprogram i Blockchain Workbench laddar du upp konfigurationen och filer för smarta kontrakt för att definiera programmet.

1. I en webbläsare går du till webbadressen för Blockchain Workbench. Till exempel `https://{workbench URL}.azurewebsites.net/` skapas webbprogrammet när du distribuerar Blockchain Workbench. Information om hur du hittar webbadressen för Blockchain Workbench finns i [Webbadress för Blockchain Workbench](deploy.md#blockchain-workbench-web-url)
2. Logga in som [Blockchain Workbench-administratör](manage-users.md#manage-blockchain-workbench-administrators).
3. Välj **program**  >  **ny**. Fönsterrutan **Nytt program** visas.
4. Välj **överför kontrakts konfigurationen**  >  **Bläddra** för att hitta **HelloBlockchain.jsi** konfigurations filen som du har skapat. Konfigurationsfilen verifieras automatiskt. Välj länken **Visa** för att visa verifieringsfel. Åtgärda verifieringsfel innan du distribuerar programmet.
5. Välj **överför kontrakt koden**  >  **Bläddra** för att leta reda på filen **HelloBlockchain. sol** Smart kontrakt kod. Kodfilen verifieras automatiskt. Välj länken **Visa** för att visa verifieringsfel. Åtgärda verifieringsfel innan du distribuerar programmet.
6. Välj **Distribuera** för att skapa blockkedjeprogrammet baserat på konfigurationen och filerna för smart kontrakt.

Distributionen av blockkedjeprogrammet tar några minuter. När distributionen är klar visas det nya programmet i **Program**. 

> [!NOTE]
> Du kan även skapa blockkedjeprogram med hjälp av [Azure Blockchain Workbench REST API](/rest/api/azure-blockchain-workbench).

## <a name="add-blockchain-application-members"></a>Lägga till blockkedjeprogrammedlemmar

Lägga till programmedlemmar i programmet för att initiera och utföra åtgärder på kontrakt. För att kunna lägga till programmedlemmar måste du vara [Blockchain Workbench-administratör](manage-users.md#manage-blockchain-workbench-administrators).

1. Välj **program**  >  **Hej, blockchain!**.
2. Det antal medlemmar som är associerade med programmet visas i det övre högra hörnet på sidan. För ett nytt program är antalet medlemmar noll.
3. Välj länken för **medlemmar** i det övre högra hörnet på sidan. En aktuell lista över medlemmar för programmet visas.
4. I medlemskapslistan väljer du **Lägg till medlemmar**.
5. Välj eller ange namnet på den medlem som du vill lägga till. Endast Azure AD-användare som finns i Blockchain Workbench-klientorganisationen visas. Om användaren inte hittas behöver du [lägga till Azure AD-användare](manage-users.md#add-azure-ad-users).
6. Välj **Roll** för medlemmen. För den första medlemmen väljer du **Begärare** som roll.
7. Välj **Lägg till** för att lägga till medlemmen med den tillhörande rollen i programmet.
8. Lägg till en till medlem i programmet med rollen **Svarare**.

Mer information om hur du hanterar användare i Blockchain Workbench finns i avsnittet om att [hantera användare i Azure Blockchain Workbench](manage-users.md)

## <a name="next-steps"></a>Nästa steg

I den här instruktionsartikeln har du skapat ett grundläggande program med begäran och svar. Om du vill lära dig hur du använder programmet fortsätter till nästa instruktionsartikel.

> [!div class="nextstepaction"]
> [Använda ett blockkedjeprogram](use.md)
