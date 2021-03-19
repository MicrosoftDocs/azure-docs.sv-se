---
title: Metadata-referens för konfiguration av Azure blockchain Workbench
description: Översikt över metadata för för hands versionen av Azure blockchain Workbench för program konfiguration.
ms.date: 12/09/2019
ms.topic: article
ms.reviewer: brendal
ms.openlocfilehash: f0ba19bf1d7fdf05014ac199fae9392b5c3249d1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "87073072"
---
# <a name="azure-blockchain-workbench-configuration-reference"></a>Konfigurations referens för Azure blockchain Workbench

Azure blockchain Workbench-program är arbets flöden för flera parter som definieras av konfigurations-metadata och smart kontrakt kod. Konfigurationsmetadata definierar avancerade arbetsflöden och interaktionsmodellen för blockkedjeprogrammet. Smarta kontrakt definierar affärs logiken för blockchain-programmet. Workbench använder konfiguration och smart kontrakt kod för att generera användar upplevelser för blockchain-program.

Metadata för konfiguration anger följande information för varje blockchain-program:

* Namn på och beskrivning av blockchain-programmet
* Unika roller för användare som kan agera eller delta i blockchain-programmet
* Ett eller flera arbets flöden. Varje arbets flöde fungerar som en tillstånds dator för att kontrol lera flödet för affärs logiken. Arbets flöden kan vara oberoende eller interagera med varandra.

I varje definierat arbets flöde anges följande:

* Namn och beskrivning av arbets flödet
* Tillstånd för arbets flödet.  Varje tillstånd är ett stadium i affärs logikens kontroll flöde. 
* Åtgärder för att övergå till nästa tillstånd
* Användar roller som tillåts att initiera varje åtgärd
* Smarta kontrakt som representerar affärs logik i kod filer

## <a name="application"></a>Program

Ett blockchain-program innehåller konfigurations-metadata, arbets flöden och användar roller som kan agera eller delta i programmet.

| Fält | Beskrivning | Krävs |
|-------|-------------|:--------:|
| ApplicationName | Unikt program namn. Motsvarande Smart kontrakt måste använda samma **ApplicationName** för den aktuella kontrakts klassen.  | Ja |
| DisplayName | Eget visnings namn för programmet. | Ja |
| Beskrivning | Beskrivning av programmet. | Inga |
| ApplicationRoles | Samling av [ApplicationRoles](#application-roles). Användar roller som kan agera eller delta i programmet.  | Ja |
| Arbetsflöden | Samling av  [arbets flöden](#workflows). Varje arbets flöde fungerar som en tillstånds dator för att kontrol lera flödet för affärs logiken. | Ja |

Ett exempel finns i [konfigurations filen exempel](#configuration-file-example).

## <a name="workflows"></a>Arbetsflöden

Ett programs affärs logik kan modelleras som en tillstånds dator där en åtgärd gör att affärs logikens flöde flyttas från ett tillstånd till ett annat. Ett arbets flöde är en samling av sådana tillstånd och åtgärder. Varje arbets flöde består av ett eller flera smarta kontrakt, som representerar affärs logiken i kod filer. Ett körbart kontrakt är en instans av ett arbets flöde.

| Fält | Beskrivning | Krävs | Högsta längd |
|-------|-------------|:--------:|-----------:|
| Name | Unikt arbets flödes namn. Motsvarande smarta kontrakt måste använda samma **namn** för den aktuella kontrakts klassen. | Ja | 50 |
| DisplayName | Eget visnings namn för arbets flödet. | Ja | 255 |
| Beskrivning | Beskrivning av arbets flödet. | Inga | 255 |
| Initierare | Samling av [ApplicationRoles](#application-roles). Roller som tilldelas till användare som har behörighet att skapa kontrakt i arbets flödet. | Ja | |
| StartState | Namnet på arbets flödets ursprungliga tillstånd. | Ja | |
| Egenskaper | Samling [identifierare](#identifiers). Representerar data som kan läsas av eller visualiseras i ett användar upplevelse verktyg. | Ja | |
| Konstruktor | Definierar indataparametrar för att skapa en instans av arbets flödet. | Ja | |
| Functions | En samling [funktioner](#functions) som kan köras i arbets flödet. | Ja | |
| Tillstånd | En samling med arbets flödes [tillstånd](#states). | Ja | |

Ett exempel finns i [konfigurations filen exempel](#configuration-file-example).

## <a name="type"></a>Typ

Data typer som stöds.

| Typ | Beskrivning |
|-------|-------------|
| adress  | Blockchain-adress typ, till exempel *kontrakt* eller *användare*. |
| matris    | Matris med en nivå av typen heltal, bool, Money eller Time. Matriser kan vara statiska eller dynamiska. Använd **ElementType** för att ange data typen för elementen i matrisen. Se [exempel på konfiguration](#example-configuration-of-type-array). |
| boolesk     | Boolesk datatyp. |
| tillverknings | Adress till typ kontrakt. |
| räkning     | Uppräknad uppsättning med namngivna värden. När du använder Enum-typen anger du även en lista över EnumValues. Varje värde är begränsat till 255 tecken. Giltiga värde tecken innehåller gemener och versaler (A-Z, A-z) och siffror (0-9). Se [exempel på konfiguration och användning i fast](#example-configuration-of-type-enum)form. |
| int      | Data typen Integer. |
| money    | Data typen Money. |
| state    | Arbets flödes tillstånd. |
| sträng  | Strängdatatyp. 4000-maximalt antal bokstäver. Se [exempel på konfiguration](#example-configuration-of-type-string). |
| användare     | Adress till typ användare. |
| time     | Tids data typ. |
|`[ Application Role Name ]`| Ett namn som anges i program rollen. Begränsar användare till en roll typ. |

### <a name="example-configuration-of-type-array"></a>Exempel på konfiguration av typen matris

```json
{
  "Name": "Quotes",
  "Description": "Market quotes",
  "DisplayName": "Quotes",
  "Type": {
    "Name": "array",
    "ElementType": {
        "Name": "int"
    }
  }
}
```

#### <a name="using-a-property-of-type-array"></a>Använda en egenskap av typen matris

Om du definierar en egenskap som typ mat ris i konfigurationen, måste du ta med en explicit get-funktion för att returnera den offentliga egenskapen för mat ris typen i solider. Exempel:

```
function GetQuotes() public constant returns (int[]) {
     return Quotes;
}
```

### <a name="example-configuration-of-type-string"></a>Exempel på konfiguration av typ sträng

``` json
{
  "Name": "description",
  "Description": "Descriptive text",
  "DisplayName": "Description",
  "Type": {
    "Name": "string"
  }
}
```

### <a name="example-configuration-of-type-enum"></a>Exempel på konfiguration av typen Enum

``` json
{
  "Name": "PropertyType",
  "DisplayName": "Property Type",
  "Description": "The type of the property",
  "Type": {
    "Name": "enum",
    "EnumValues": ["House", "Townhouse", "Condo", "Land"]
  }
}
```

#### <a name="using-enumeration-type-in-solidity"></a>Använda uppräknings typ i fast form

När en uppräkning har definierats i konfigurationen kan du använda uppräknings typer i solide. Du kan till exempel definiera en uppräkning som kallas PropertyTypeEnum.

```
enum PropertyTypeEnum {House, Townhouse, Condo, Land} PropertyTypeEnum public PropertyType; 
```

Listan över strängar måste matcha mellan konfigurationen och det smarta kontraktet för att vara giltiga och konsekventa deklarationer i blockchain Workbench.

Tilldelnings exempel:

```
PropertyType = PropertyTypeEnum.Townhouse;
```

Exempel på funktions parameter: 

``` 
function AssetTransfer(string description, uint256 price, PropertyTypeEnum propertyType) public
{
    InstanceOwner = msg.sender;
    AskingPrice = price;
    Description = description;
    PropertyType = propertyType;
    State = StateType.Active;
    ContractCreated();
}

```

## <a name="constructor"></a>Konstruktor

Definierar indataparametrar för en instans av ett arbets flöde.

| Fält | Beskrivning | Krävs |
|-------|-------------|:--------:|
| Parametrar | Samling [identifierare](#identifiers) som krävs för att initiera ett smart kontrakt. | Ja |

### <a name="constructor-example"></a>Konstruktor-exempel

``` json
{
  "Parameters": [
    {
      "Name": "description",
      "Description": "The description of this asset",
      "DisplayName": "Description",
      "Type": {
        "Name": "string"
      }
    },
    {
      "Name": "price",
      "Description": "The price of this asset",
      "DisplayName": "Price",
      "Type": {
        "Name": "money"
      }
    }
  ]
}
```

## <a name="functions"></a>Functions

Definierar funktioner som kan köras i arbets flödet.

| Fält | Beskrivning | Krävs | Högsta längd |
|-------|-------------|:--------:|-----------:|
| Name | Funktionens unika namn. Motsvarande smarta kontrakt måste använda samma **namn** för den aktuella funktionen. | Ja | 50 |
| DisplayName | Användarvänligt visnings namn för funktionen. | Ja | 255 |
| Beskrivning | Beskrivning av funktionen | Inga | 255 |
| Parametrar | Samling [identifierare](#identifiers) som motsvarar parametrarna i funktionen. | Ja | |

### <a name="functions-example"></a>Functions-exempel

``` json
"Functions": [
  {
    "Name": "Modify",
    "DisplayName": "Modify",
    "Description": "Modify the description/price attributes of this asset transfer instance",
    "Parameters": [
      {
        "Name": "description",
        "Description": "The new description of the asset",
        "DisplayName": "Description",
        "Type": {
          "Name": "string"
        }
      },
      {
        "Name": "price",
        "Description": "The new price of the asset",
        "DisplayName": "Price",
        "Type": {
          "Name": "money"
        }
      }
    ]
  },
  {
    "Name": "Terminate",
    "DisplayName": "Terminate",
    "Description": "Used to cancel this particular instance of asset transfer",
    "Parameters": []
  }
]

```

## <a name="states"></a>Tillstånd

En samling av unika tillstånd i ett arbets flöde. Varje tillstånd fångar ett steg i affärs logikens kontroll flöde. 

| Fält | Beskrivning | Krävs | Högsta längd |
|-------|-------------|:--------:|-----------:|
| Name | Unikt namn för tillstånd. Motsvarande Smart kontrakt måste använda samma **namn** för det aktuella läget. | Ja | 50 |
| DisplayName | Eget visnings namn för tillstånd. | Ja | 255 |
| Beskrivning | Beskrivning av tillstånd. | Inga | 255 |
| Procent | Ett heltals värde som visas i användar gränssnittet för blockchain Workbench för att visa förloppet i affärs logikens kontroll flöde. | Ja | |
| Format | Visuell ledtråd som anger om tillstånd representerar ett lyckat eller misslyckat tillstånd. Det finns två giltiga värden: `Success` eller `Failure` . | Ja | |
| Övergångar | Samling tillgängliga [över gångar](#transitions) från det aktuella tillståndet till nästa uppsättning tillstånd. | Inga | |

### <a name="states-example"></a>Exempel på tillstånd

``` json
"States": [
    {
      "Name": "Active",
      "DisplayName": "Active",
      "Description": "The initial state of the asset transfer workflow",
      "PercentComplete": 20,
      "Style": "Success",
      "Transitions": [
        {
          "AllowedRoles": [],
          "AllowedInstanceRoles": [ "InstanceOwner" ],
          "Description": "Cancels this instance of asset transfer",
          "Function": "Terminate",
          "NextStates": [ "Terminated" ],
          "DisplayName": "Terminate Offer"
        },
        {
          "AllowedRoles": [ "Buyer" ],
          "AllowedInstanceRoles": [],
          "Description": "Make an offer for this asset",
          "Function": "MakeOffer",
          "NextStates": [ "OfferPlaced" ],
          "DisplayName": "Make Offer"
        },
        {
          "AllowedRoles": [],
          "AllowedInstanceRoles": [ "InstanceOwner" ],
          "Description": "Modify attributes of this asset transfer instance",
          "Function": "Modify",
          "NextStates": [ "Active" ],
          "DisplayName": "Modify"
        }
      ]
    },
    {
      "Name": "Accepted",
      "DisplayName": "Accepted",
      "Description": "Asset transfer process is complete",
      "PercentComplete": 100,
      "Style": "Success",
      "Transitions": []
    },
    {
      "Name": "Terminated",
      "DisplayName": "Terminated",
      "Description": "Asset transfer has been canceled",
      "PercentComplete": 100,
      "Style": "Failure",
      "Transitions": []
    }
  ]
```

## <a name="transitions"></a>Övergångar

Tillgängliga åtgärder till nästa tillstånd. En eller flera användar roller kan utföra en åtgärd i varje tillstånd, där en åtgärd kan övergå till ett tillstånd till ett annat tillstånd i arbets flödet. 

| Fält | Beskrivning | Krävs |
|-------|-------------|:--------:|
| AllowedRoles | Lista över program roller som tillåts att initiera över gången. Alla användare av den angivna rollen kan kunna utföra åtgärden. | Inga |
| AllowedInstanceRoles | Lista med användar roller som ingår eller anges i det smarta kontrakt som tillåts initiera över gången. Instans roller definieras i **Egenskaper** i arbets flöden. AllowedInstanceRoles representerar en användare som deltar i en instans av ett smart kontrakt. AllowedInstanceRoles ger dig möjlighet att begränsa åtgärden med en användar roll i en avtals instans.  Till exempel kanske du bara vill tillåta att användaren som skapade kontraktet (InstanceOwner) kan avsluta i stället för alla användare i roll typ (ägare) om du har angett rollen i AllowedRoles. | Inga |
| DisplayName | Eget visnings namn för över gången. | Ja |
| Beskrivning | Beskrivning av över gången. | Inga |
| Funktion | Namnet på funktionen som ska användas för att initiera över gången. | Ja |
| NextStates | En samling med potentiella nästa tillstånd efter en lyckad över gång. | Ja |

### <a name="transitions-example"></a>Exempel på över gångar

``` json
"Transitions": [
  {
    "AllowedRoles": [],
    "AllowedInstanceRoles": [ "InstanceOwner" ],
    "Description": "Cancels this instance of asset transfer",
    "Function": "Terminate",
    "NextStates": [ "Terminated" ],
    "DisplayName": "Terminate Offer"
  },
  {
    "AllowedRoles": [ "Buyer" ],
    "AllowedInstanceRoles": [],
    "Description": "Make an offer for this asset",
    "Function": "MakeOffer",
    "NextStates": [ "OfferPlaced" ],
    "DisplayName": "Make Offer"
  },
  {
    "AllowedRoles": [],
    "AllowedInstanceRoles": [ "InstanceOwner" ],
    "Description": "Modify attributes of this asset transfer instance",
    "Function": "Modify",
    "NextStates": [ "Active" ],
    "DisplayName": "Modify"
  }
]

```

## <a name="application-roles"></a>Programroller

Program roller definierar en uppsättning roller som kan tilldelas till användare som vill agera eller delta i programmet. Program roller kan användas för att begränsa åtgärder och deltagande i blockchain-programmet och motsvarande arbets flöden. 

| Fält | Beskrivning | Krävs | Högsta längd |
|-------|-------------|:--------:|-----------:|
| Name | Program rollens unika namn. Motsvarande Smart kontrakt måste använda samma **namn** för den aktuella rollen. Namnet på bastypen är reserverat. Det går inte att namnge en program roll med samma namn som [typen](#type)| Ja | 50 |
| Beskrivning | Beskrivning av program rollen. | Inga | 255 |

### <a name="application-roles-example"></a>Exempel på program roller

``` json
"ApplicationRoles": [
  {
    "Name": "Appraiser",
    "Description": "User that signs off on the asset price"
  },
  {
    "Name": "Buyer",
    "Description": "User that places an offer on an asset"
  }
]
```
## <a name="identifiers"></a>Identifierare

Identifierare representerar en samling information som används för att beskriva arbets flödes egenskaper, konstruktorer och funktions parametrar. 

| Fält | Beskrivning | Krävs | Högsta längd |
|-------|-------------|:--------:|-----------:|
| Name | Egenskapens eller parameterns unika namn. Motsvarande smarta kontrakt måste använda samma **namn** för den aktuella egenskapen eller parametern. | Ja | 50 |
| DisplayName | Eget visnings namn för egenskapen eller parametern. | Ja | 255 |
| Beskrivning | Beskrivning av egenskapen eller parametern. | Inga | 255 |
| Typ | Egenskaps [data typ](#type). | Ja |

### <a name="identifiers-example"></a>Exempel på identifierare

``` json
"Properties": [
  {
    "Name": "State",
    "DisplayName": "State",
    "Description": "Holds the state of the contract",
    "Type": {
      "Name": "state"
    }
  },
  {
    "Name": "Description",
    "DisplayName": "Description",
    "Description": "Describes the asset being sold",
    "Type": {
      "Name": "string"
    }
  }
]
```

## <a name="configuration-file-example"></a>Exempel på konfigurations fil

Till gångs överföring är ett smart kontrakt scenario för att köpa och sälja höga värde till gångar, vilket kräver en inspektör och en bedömning. Säljare kan lista sina till gångar genom att instansiera ett smart kontrakt för till gångs överföring. Köparna kan erbjuda erbjudanden genom att vidta åtgärder i det smarta avtalet och andra parter kan vidta åtgärder för att inspektera eller bedöma till gången. När till gången har marker ATS som både inspekterad och uppfyllt, kommer köparen och säljaren att bekräfta försäljningen igen innan kontraktet har angetts vara slutfört. Vid varje punkt i processen har alla deltagare insyn i kontraktets status när de uppdateras. 

Mer information, inklusive kod filerna, finns i [exempel på till gångs överföring för Azure blockchain Workbench](https://github.com/Azure-Samples/blockchain/tree/master/blockchain-workbench/application-and-smart-contract-samples/asset-transfer)

Följande konfigurations fil är för exempel på till gångs överföring:

``` json
{
  "ApplicationName": "AssetTransfer",
  "DisplayName": "Asset Transfer",
  "Description": "Allows transfer of assets between a buyer and a seller, with appraisal/inspection functionality",
  "ApplicationRoles": [
    {
      "Name": "Appraiser",
      "Description": "User that signs off on the asset price"
    },
    {
      "Name": "Buyer",
      "Description": "User that places an offer on an asset"
    },
    {
      "Name": "Inspector",
      "Description": "User that inspects the asset and signs off on inspection"
    },
    {
      "Name": "Owner",
      "Description": "User that signs off on the asset price"
    }
  ],
  "Workflows": [
    {
      "Name": "AssetTransfer",
      "DisplayName": "Asset Transfer",
      "Description": "Handles the business logic for the asset transfer scenario",
      "Initiators": [ "Owner" ],
      "StartState":  "Active",
      "Properties": [
        {
          "Name": "State",
          "DisplayName": "State",
          "Description": "Holds the state of the contract",
          "Type": {
            "Name": "state"
          }
        },
        {
          "Name": "Description",
          "DisplayName": "Description",
          "Description": "Describes the asset being sold",
          "Type": {
            "Name": "string"
          }
        },
        {
          "Name": "AskingPrice",
          "DisplayName": "Asking Price",
          "Description": "The asking price for the asset",
          "Type": {
            "Name": "money"
          }
        },
        {
          "Name": "OfferPrice",
          "DisplayName": "Offer Price",
          "Description": "The price being offered for the asset",
          "Type": {
            "Name": "money"
          }
        },
        {
          "Name": "InstanceAppraiser",
          "DisplayName": "Instance Appraiser",
          "Description": "The user that appraises the asset",
          "Type": {
            "Name": "Appraiser"
          }
        },
        {
          "Name": "InstanceBuyer",
          "DisplayName": "Instance Buyer",
          "Description": "The user that places an offer for this asset",
          "Type": {
            "Name": "Buyer"
          }
        },
        {
          "Name": "InstanceInspector",
          "DisplayName": "Instance Inspector",
          "Description": "The user that inspects this asset",
          "Type": {
            "Name": "Inspector"
          }
        },
        {
          "Name": "InstanceOwner",
          "DisplayName": "Instance Owner",
          "Description": "The seller of this particular asset",
          "Type": {
            "Name": "Owner"
          }
        }
      ],
      "Constructor": {
        "Parameters": [
          {
            "Name": "description",
            "Description": "The description of this asset",
            "DisplayName": "Description",
            "Type": {
              "Name": "string"
            }
          },
          {
            "Name": "price",
            "Description": "The price of this asset",
            "DisplayName": "Price",
            "Type": {
              "Name": "money"
            }
          }
        ]
      },
      "Functions": [
        {
          "Name": "Modify",
          "DisplayName": "Modify",
          "Description": "Modify the description/price attributes of this asset transfer instance",
          "Parameters": [
            {
              "Name": "description",
              "Description": "The new description of the asset",
              "DisplayName": "Description",
              "Type": {
                "Name": "string"
              }
            },
            {
              "Name": "price",
              "Description": "The new price of the asset",
              "DisplayName": "Price",
              "Type": {
                "Name": "money"
              }
            }
          ]
        },
        {
          "Name": "Terminate",
          "DisplayName": "Terminate",
          "Description": "Used to cancel this particular instance of asset transfer",
          "Parameters": []
        },
        {
          "Name": "MakeOffer",
          "DisplayName": "Make Offer",
          "Description": "Place an offer for this asset",
          "Parameters": [
            {
              "Name": "inspector",
              "Description": "Specify a user to inspect this asset",
              "DisplayName": "Inspector",
              "Type": {
                "Name": "Inspector"
              }
            },
            {
              "Name": "appraiser",
              "Description": "Specify a user to appraise this asset",
              "DisplayName": "Appraiser",
              "Type": {
                "Name": "Appraiser"
              }
            },
            {
              "Name": "offerPrice",
              "Description": "Specify your offer price for this asset",
              "DisplayName": "Offer Price",
              "Type": {
                "Name": "money"
              }
            }
          ]
        },
        {
          "Name": "Reject",
          "DisplayName": "Reject",
          "Description": "Reject the user's offer",
          "Parameters": []
        },
        {
          "Name": "AcceptOffer",
          "DisplayName": "Accept Offer",
          "Description": "Accept the user's offer",
          "Parameters": []
        },
        {
          "Name": "RescindOffer",
          "DisplayName": "Rescind Offer",
          "Description": "Rescind your placed offer",
          "Parameters": []
        },
        {
          "Name": "ModifyOffer",
          "DisplayName": "Modify Offer",
          "Description": "Modify the price of your placed offer",
          "Parameters": [
            {
              "Name": "offerPrice",
              "DisplayName": "Price",
              "Type": {
                "Name": "money"
              }
            }
          ]
        },
        {
          "Name": "Accept",
          "DisplayName": "Accept",
          "Description": "Accept the inspection/appraisal results",
          "Parameters": []
        },
        {
          "Name": "MarkInspected",
          "DisplayName": "Mark Inspected",
          "Description": "Mark the asset as inspected",
          "Parameters": []
        },
        {
          "Name": "MarkAppraised",
          "DisplayName": "Mark Appraised",
          "Description": "Mark the asset as appraised",
          "Parameters": []
        }
      ],
      "States": [
        {
          "Name": "Active",
          "DisplayName": "Active",
          "Description": "The initial state of the asset transfer workflow",
          "PercentComplete": 20,
          "Style": "Success",
          "Transitions": [
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Cancels this instance of asset transfer",
              "Function": "Terminate",
              "NextStates": [ "Terminated" ],
              "DisplayName": "Terminate Offer"
            },
            {
              "AllowedRoles": [ "Buyer" ],
              "AllowedInstanceRoles": [],
              "Description": "Make an offer for this asset",
              "Function": "MakeOffer",
              "NextStates": [ "OfferPlaced" ],
              "DisplayName": "Make Offer"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Modify attributes of this asset transfer instance",
              "Function": "Modify",
              "NextStates": [ "Active" ],
              "DisplayName": "Modify"
            }
          ]
        },
        {
          "Name": "OfferPlaced",
          "DisplayName": "Offer Placed",
          "Description": "Offer has been placed for the asset",
          "PercentComplete": 30,
          "Style": "Success",
          "Transitions": [
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Accept the proposed offer for the asset",
              "Function": "AcceptOffer",
              "NextStates": [ "PendingInspection" ],
              "DisplayName": "Accept Offer"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Reject the proposed offer for the asset",
              "Function": "Reject",
              "NextStates": [ "Active" ],
              "DisplayName": "Reject"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Cancel this instance of asset transfer",
              "Function": "Terminate",
              "NextStates": [ "Terminated" ],
              "DisplayName": "Terminate"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceBuyer" ],
              "Description": "Rescind the offer you previously placed for this asset",
              "Function": "RescindOffer",
              "NextStates": [ "Active" ],
              "DisplayName": "Rescind Offer"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceBuyer" ],
              "Description": "Modify the price that you specified for your offer",
              "Function": "ModifyOffer",
              "NextStates": [ "OfferPlaced" ],
              "DisplayName": "Modify Offer"
            }
          ]
        },
        {
          "Name": "PendingInspection",
          "DisplayName": "Pending Inspection",
          "Description": "Asset is pending inspection",
          "PercentComplete": 40,
          "Style": "Success",
          "Transitions": [
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Reject the offer",
              "Function": "Reject",
              "NextStates": [ "Active" ],
              "DisplayName": "Reject"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Cancel the offer",
              "Function": "Terminate",
              "NextStates": [ "Terminated" ],
              "DisplayName": "Terminate"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceBuyer" ],
              "Description": "Rescind the offer you placed for this asset",
              "Function": "RescindOffer",
              "NextStates": [ "Active" ],
              "DisplayName": "Rescind Offer"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceInspector" ],
              "Description": "Mark this asset as inspected",
              "Function": "MarkInspected",
              "NextStates": [ "Inspected" ],
              "DisplayName": "Mark Inspected"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceAppraiser" ],
              "Description": "Mark this asset as appraised",
              "Function": "MarkAppraised",
              "NextStates": [ "Appraised" ],
              "DisplayName": "Mark Appraised"
            }
          ]
        },
        {
          "Name": "Inspected",
          "DisplayName": "Inspected",
          "PercentComplete": 45,
          "Style": "Success",
          "Transitions": [
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Reject the offer",
              "Function": "Reject",
              "NextStates": [ "Active" ],
              "DisplayName": "Reject"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Cancel the offer",
              "Function": "Terminate",
              "NextStates": [ "Terminated" ],
              "DisplayName": "Terminate"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceBuyer" ],
              "Description": "Rescind the offer you placed for this asset",
              "Function": "RescindOffer",
              "NextStates": [ "Active" ],
              "DisplayName": "Rescind Offer"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceAppraiser" ],
              "Description": "Mark this asset as appraised",
              "Function": "MarkAppraised",
              "NextStates": [ "NotionalAcceptance" ],
              "DisplayName": "Mark Appraised"
            }
          ]
        },
        {
          "Name": "Appraised",
          "DisplayName": "Appraised",
          "Description": "Asset has been appraised, now awaiting inspection",
          "PercentComplete": 45,
          "Style": "Success",
          "Transitions": [
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Reject the offer",
              "Function": "Reject",
              "NextStates": [ "Active" ],
              "DisplayName": "Reject"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Cancel the offer",
              "Function": "Terminate",
              "NextStates": [ "Terminated" ],
              "DisplayName": "Terminate"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceBuyer" ],
              "Description": "Rescind the offer you placed for this asset",
              "Function": "RescindOffer",
              "NextStates": [ "Active" ],
              "DisplayName": "Rescind Offer"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceInspector" ],
              "Description": "Mark the asset as inspected",
              "Function": "MarkInspected",
              "NextStates": [ "NotionalAcceptance" ],
              "DisplayName": "Mark Inspected"
            }
          ]
        },
        {
          "Name": "NotionalAcceptance",
          "DisplayName": "Notional Acceptance",
          "Description": "Asset has been inspected and appraised, awaiting final sign-off from buyer and seller",
          "PercentComplete": 50,
          "Style": "Success",
          "Transitions": [
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Sign-off on inspection and appraisal",
              "Function": "Accept",
              "NextStates": [ "SellerAccepted" ],
              "DisplayName": "SellerAccept"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Reject the proposed offer for the asset",
              "Function": "Reject",
              "NextStates": [ "Active" ],
              "DisplayName": "Reject"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Cancel this instance of asset transfer",
              "Function": "Terminate",
              "NextStates": [ "Terminated" ],
              "DisplayName": "Terminate"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceBuyer" ],
              "Description": "Sign-off on inspection and appraisal",
              "Function": "Accept",
              "NextStates": [ "BuyerAccepted" ],
              "DisplayName": "BuyerAccept"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceBuyer" ],
              "Description": "Rescind the offer you placed for this asset",
              "Function": "RescindOffer",
              "NextStates": [ "Active" ],
              "DisplayName": "Rescind Offer"
            }
          ]
        },
        {
          "Name": "BuyerAccepted",
          "DisplayName": "Buyer Accepted",
          "Description": "Buyer has signed-off on inspection and appraisal",
          "PercentComplete": 75,
          "Style": "Success",
          "Transitions": [
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Sign-off on inspection and appraisal",
              "Function": "Accept",
              "NextStates": [ "SellerAccepted" ],
              "DisplayName": "Accept"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Reject the proposed offer for the asset",
              "Function": "Reject",
              "NextStates": [ "Active" ],
              "DisplayName": "Reject"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Cancel this instance of asset transfer",
              "Function": "Terminate",
              "NextStates": [ "Terminated" ],
              "DisplayName": "Terminate"
            }
          ]
        },
        {
          "Name": "SellerAccepted",
          "DisplayName": "Seller Accepted",
          "Description": "Seller has signed-off on inspection and appraisal",
          "PercentComplete": 75,
          "Style": "Success",
          "Transitions": [
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceBuyer" ],
              "Description": "Sign-off on inspection and appraisal",
              "Function": "Accept",
              "NextStates": [ "Accepted" ],
              "DisplayName": "Accept"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceBuyer" ],
              "Description": "Rescind the offer you placed for this asset",
              "Function": "RescindOffer",
              "NextStates": [ "Active" ],
              "DisplayName": "Rescind Offer"
            }
          ]
        },
        {
          "Name": "Accepted",
          "DisplayName": "Accepted",
          "Description": "Asset transfer process is complete",
          "PercentComplete": 100,
          "Style": "Success",
          "Transitions": []
        },
        {
          "Name": "Terminated",
          "DisplayName": "Terminated",
          "Description": "Asset transfer has been canceled",
          "PercentComplete": 100,
          "Style": "Failure",
          "Transitions": []
        }
      ]
    }
  ]
}
```
## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [REST API-referens för Azure Blockchain Workbench](/rest/api/azure-blockchain-workbench)
