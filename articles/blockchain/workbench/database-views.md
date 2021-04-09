---
title: Azure blockchain Workbench-databasvyer
description: Översikt över tillgängliga Azure blockchain Workbench Preview SQL DB-databasvyer.
ms.date: 09/05/2019
ms.topic: article
ms.reviewer: mmercuri
ms.openlocfilehash: 6c7fba590c9c8951e1862c28cb24dbde52c8da8d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105625542"
---
# <a name="azure-blockchain-workbench-database-views"></a>Azure blockchain Workbench-databasvyer

För hands versionen av Azure blockchain Workbench levererar data från distribuerade transaktioner till en SQL DB *-databas utanför kedjan* . Med hjälp av databasen från andra kedjan kan du använda SQL och befintliga verktyg, till exempel [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms), för att interagera med blockchain-data.

Azure blockchain Workbench tillhandahåller en uppsättning databasvyer som ger åtkomst till data som kommer att vara till hjälp när du utför dina frågor. Dessa vyer är mycket denormaliserade för att göra det enkelt att snabbt komma igång med att skapa rapporter, analysera och på annat sätt använda blockchain-data med befintliga verktyg och utan att behöva träna databas personal.

Det här avsnittet innehåller en översikt över databasobjekten och de data som de innehåller.

> [!NOTE]
> All direkt användning av databas tabeller som finns i databasen utanför dessa vyer, medan det är möjligt, stöds inte.
>

## <a name="vwapplication"></a>vwApplication

Den här vyn innehåller information om **program** som har överförts till Azure blockchain Workbench.

| Namn                             | Typ          | Kan vara null | Beskrivning                                                                                                                                                                                                                                                   |
|----------------------------------|---------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId                    | int           | Inga          | En unik identifierare för programmet |
| ApplicationName                  | nvarchar (50)  | Inga          | Namnet på programmet |
| ApplicationDescription           | nvarchar (255) | Ja         | En beskrivning av programmet |
| ApplicationDisplayName           | nvarchar (255) | Inga          | Namnet som ska visas i ett användar gränssnitt |
| ApplicationEnabled               | bit           | Inga          | Identifierar om programmet är aktiverat<br /> **Obs:** Även om ett program kan reflekteras som inaktiverat i databasen, finns associerade kontrakt kvar på blockchain och data om dessa avtal finns kvar i databasen. |
| UploadedDtTm                     | datetime2 (7)  | Inga          | Datum och tid då ett kontrakt överfördes |
| UploadedByUserId                 | int           | Inga          | ID för den användare som laddade upp programmet |
| UploadedByUserExternalId         | nvarchar (255) | Inga          | Den externa identifieraren för den användare som laddade upp programmet. Som standard är detta ID användaren från Azure Active Directory för konsortiet.                                                                                                |
| UploadedByUserProvisioningStatus | int           | Inga          | Identifierar den aktuella statusen för etablerings processen för användaren. Möjliga värden: <br />0 – användaren har skapats av API: et<br />1 – en nyckel har associerats med användaren i databasen<br />2 – användaren är helt etablerad                         |
| UploadedByUserFirstName          | nvarchar (50)  | Ja         | Förnamnet på den användare som laddade upp kontraktet |
| UploadedByUserLastName           | nvarchar (50)  | Ja         | Det sista namnet på den användare som laddade upp kontraktet |
| UploadedByUserEmailAddress       | nvarchar (255) | Ja         | E-postadressen till den användare som överförde kontraktet |

## <a name="vwapplicationrole"></a>vwApplicationRole

Den här vyn innehåller information om de roller som har definierats i Azure blockchain Workbench-program.

I ett program för *till gångs överföring* kan exempelvis roller som *köpare* och *säljar* roller definieras.

| Namn                   | Typ             | Kan vara null | Beskrivning                                       |
|------------------------|------------------|-------------|---------------------------------------------------|
| ApplicationId          | int              | Inga          | En unik identifierare för programmet           |
| ApplicationName        | nvarchar (50)     | Inga          | Namnet på programmet                       |
| ApplicationDescription | nvarchar (255)    | Ja         | En beskrivning av programmet                  |
| ApplicationDisplayName | nvarchar (255)    | Inga          | Namnet som ska visas i ett användar gränssnitt      |
| RoleId                 | int              | Inga          | En unik identifierare för en roll i programmet |
| RoleName               | nvarchar50)      | Inga          | Namnet på rollen                              |
| RoleDescription        | Beskrivning (255) | Ja         | En beskrivning av rollen                         |

## <a name="vwapplicationroleuser"></a>vwApplicationRoleUser

Den här vyn innehåller information om de roller som har definierats i Azure blockchain Workbench-program och användare som är kopplade till dem.

I en *till gångs överförings* applikation kan exempelvis *John Smith* vara associerat med *köpar* rollen.

| Namn                       | Typ          | Kan vara null | Beskrivning                                                                                                                                                                                                                           |
|----------------------------|---------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId              | int           | Inga          | En unik identifierare för programmet                                                                                                                                                                                               |
| ApplicationName            | nvarchar (50)  | Inga          | Namnet på programmet                                                                                                                                                                                                           |
| ApplicationDescription     | nvarchar (255) | Ja         | En beskrivning av programmet                                                                                                                                                                                                      |
| ApplicationDisplayName     | nvarchar (255) | Inga          | Namnet som ska visas i ett användar gränssnitt                                                                                                                                                                                          |
| ApplicationRoleId          | int           | Inga          | En unik identifierare för en roll i programmet                                                                                                                                                                                     |
| ApplicationRoleName        | nvarchar50)   | Inga          | Namnet på rollen                                                                                                                                                                                                                  |
| ApplicationRoleDescription | nvarchar (255) | Ja         | En beskrivning av rollen                                                                                                                                                                                                             |
| UserId                     | int           | Inga          | ID för den användare som är associerad med rollen |
| UserExternalId             | nvarchar (255) | Inga          | Den externa identifieraren för den användare som är associerad med rollen. Som standard är detta ID användaren från Azure Active Directory för konsortiet.                                                                     |
| UserProvisioningStatus     | int           | Inga          | Identifierar den aktuella statusen för etablerings processen för användaren. Möjliga värden: <br />0 – användaren har skapats av API: et<br />1 – en nyckel har associerats med användaren i databasen<br />2 – användaren är helt etablerad |
| UserFirstName              | nvarchar (50)  | Ja         | Förnamnet på den användare som är associerad med rollen |
| UserLastName               | nvarchar (255) | Ja         | Efter namnet på den användare som är associerad med rollen |
| UserEmailAddress           | nvarchar (255) | Ja         | E-postadressen för den användare som är associerad med rollen |

## <a name="vwconnectionuser"></a>vwConnectionUser

Den här vyn innehåller information om de anslutningar som definieras i Azure blockchain Workbench och de användare som är kopplade till dem. För varje anslutning innehåller den här vyn följande data:

-   Associerad redovisnings information
-   Associerad användar information

| Namn                     | Typ          | Kan vara null | Beskrivning                                                                                                                                                                                                                           |
|--------------------------|---------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ConnectionId             | int           | Inga          | Den unika identifieraren för en anslutning i Azure blockchain Workbench |
| ConnectionEndpointUrl    | nvarchar (50)  | Inga          | Slut punkts-URL för en anslutning |
| ConnectionFundingAccount | nvarchar (255) | Ja         | Det finansierings konto som är associerat med en anslutning, om tillämpligt |
| LedgerId                 | int           | Inga          | Den unika identifieraren för en redovisning |
| LedgerName               | nvarchar (50)  | Inga          | Namnet på redovisningen |
| LedgerDisplayName        | nvarchar (255) | Inga          | Namnet på redovisningen som ska visas i användar gränssnittet |
| UserId                   | int           | Inga          | ID för den användare som är associerad med anslutningen |
| UserExternalId           | nvarchar (255) | Inga          | Den externa identifieraren för den användare som är associerad med anslutningen. Som standard är detta ID användaren från Azure Active Directory för konsortiet. |
| UserProvisioningStatus   | int           | Inga          |Identifierar den aktuella statusen för etablerings processen för användaren. Möjliga värden: <br />0 – användaren har skapats av API: et<br />1 – en nyckel har associerats med användaren i databasen<br />2 – användaren är helt etablerad |
| UserFirstName            | nvarchar (50)  | Ja         | Förnamnet på den användare som är associerad med anslutningen |
| UserLastName             | nvarchar (255) | Ja         | Efter namnet på den användare som är associerad med anslutningen |
| UserEmailAddress         | nvarchar (255) | Ja         | E-postadressen för den användare som är associerad med anslutningen |

## <a name="vwcontract"></a>vwContract

Den här vyn innehåller information om distribuerade kontrakt. För varje kontrakt innehåller den här vyn följande data:

-   Associerad program definition
-   Associerad arbets flödes definition
-   Associerad redovisnings implementering för funktionen
-   Information för den användare som initierade åtgärden
-   Information relaterad till blockchain-blocket och transaktionen

| Namn                                     | Typ           | Kan vara null | Beskrivning                                                                                                                                                                                                                                                   |
|------------------------------------------|----------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ConnectionId                             | int            | Inga          | Den unika identifieraren för en anslutning i Azure blockchain Workbench.                                                                                                                                                                                         |
| ConnectionEndpointUrl                    | nvarchar (50)   | Inga          | Slut punkts-URL för en anslutning |
| ConnectionFundingAccount                 | nvarchar (255)  | Ja         | Det finansierings konto som är associerat med en anslutning, om tillämpligt |
| LedgerId                                 | int            | Inga          | Den unika identifieraren för en redovisning |
| LedgerName                               | nvarchar (50)   | Inga          | Namnet på redovisningen |
| LedgerDisplayName                        | nvarchar (255)  | Inga          | Namnet på redovisningen som ska visas i användar gränssnittet |
| ApplicationId                            | int            | Inga          | En unik identifierare för programmet |
| ApplicationName                          | nvarchar (50)  | Inga          | Namnet på programmet |
| ApplicationDisplayName                   | nvarchar (255) | Inga          | Namnet som ska visas i ett användar gränssnitt |
| ApplicationEnabled                       | bit            | Inga          | Identifierar om programmet är aktiverat för tillfället.<br /> **Obs:** Även om ett program kan reflekteras som inaktiverat i databasen, finns associerade kontrakt kvar på blockchain och data om dessa avtal finns kvar i databasen.  |
| WorkflowId                               | int            | Inga          | En unik identifierare för det arbets flöde som är associerat med ett kontrakt |
| WorkflowName                             | nvarchar (50)   | Inga          | Namnet på det arbets flöde som är associerat med ett kontrakt |
| WorkflowDisplayName                      | nvarchar (255)  | Inga          | Namnet på det arbets flöde som är associerat med kontraktet som visas i användar gränssnittet |
| WorkflowDescription                      | nvarchar (255)  | Ja         | Beskrivning av det arbets flöde som är associerat med ett kontrakt |
| ContractCodeId                           | int            | Inga          | En unik identifierare för kontrakt koden som är associerad med kontraktet |
| ContractFileName                         | int            | Inga          | Namnet på filen som innehåller den smarta kontrakt koden för det här arbets flödet. |
| ContractUploadedDtTm                     | int            | Inga          | Datum och tid då kontrakt koden överfördes |
| ContractId                               | int            | Inga          | Kontraktets unika identifierare |
| ContractProvisioningStatus               | int            | Inga          | Identifierar den aktuella statusen för etablerings processen för kontraktet. Möjliga värden: <br />0 – kontraktet har skapats av API: et i databasen<br />1 – kontraktet har skickats till redovisningen<br />2 – kontraktet har distribuerats till redovisningen<br />3 eller 4 – Det gick inte att distribuera kontraktet till redovisningen<br />5 – kontraktet har distribuerats till redovisningen <br /><br />Från och med version 1,5 stöds värdena 0 till 5. För bakåtkompatibilitet i den aktuella versionen är Visa **vwContractV0** tillgängligt som bara stöder värdena 0 till och med 2. |
| ContractLedgerIdentifier                 | nvarchar (255) |             | E-postadressen för den användare som distribuerade kontraktet |
| ContractDeployedByUserId                 | int            | Inga          | En extern identifierare för den användare som distribuerade kontraktet. Som standard är detta ID det GUID som representerar Azure Active Directory-ID: t för användaren.                                                                                                          |
| ContractDeployedByUserExternalId         | nvarchar (255)  | Inga          | En extern identifierare för den användare som distribuerade kontraktet. Som standard är detta ID det GUID som representerar Azure Active Directory-ID: t för användaren.                                                                                                         |
| ContractDeployedByUserProvisioningStatus | int            | Inga          | Identifierar den aktuella statusen för etablerings processen för användaren. Möjliga värden: <br />0 – användaren har skapats av API: et<br />1 – en nyckel har associerats med användaren i databasen <br />2 – användaren är helt etablerad                     |
| ContractDeployedByUserFirstName          | nvarchar (50)   | Ja         | Förnamnet på den användare som distribuerade kontraktet |
| ContractDeployedByUserLastName           | nvarchar (255)  | Ja         | Efter namnet på den användare som distribuerade kontraktet |
| ContractDeployedByUserEmailAddress       | nvarchar (255)  | Ja         | E-postadressen för den användare som distribuerade kontraktet |

## <a name="vwcontractaction"></a>vwContractAction

Den här vyn representerar merparten av information som rör åtgärder som vidtagits i kontrakt och är utformad för att enkelt under lätta vanliga rapporterings scenarier. För varje åtgärd som har vidtagits innehåller den här vyn följande data:

-   Associerad program definition
-   Associerad arbets flödes definition
-   Associerad Smart kontrakts funktion och parameter definition
-   Associerad redovisnings implementering för funktionen
-   Angivna instans värden har angetts för parametrar
-   Information för den användare som initierade åtgärden
-   Information relaterad till blockchain-blocket och transaktionen

| Namn                                     | Typ          | Kan vara null | Beskrivning                                                                                                                                                                                                                                                                                                    |
|------------------------------------------|---------------|-------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId                            | int           | Inga          | En unik identifierare för programmet |
| ApplicationName                          | nvarchar (50)  | Inga          | Namnet på programmet |
| ApplicationDisplayName                   | nvarchar (255) | Inga          | Namnet som ska visas i ett användar gränssnitt |
| ApplicationEnabled                       | bit           | Inga          | Det här fältet anger om programmet är aktiverat för tillfället. Obs! även om ett program kan reflekteras som inaktiverat i databasen, finns associerade kontrakt kvar på blockchain och data om dessa avtal finns kvar i-databasen.                                                  |
| WorkflowId                               | int           | Inga          | En unik identifierare för ett arbets flöde |
| WorkflowName                             | nvarchar (50)  | Inga          | Namnet på arbets flödet |
| WorkflowDisplayName                      | nvarchar (255) | Inga          | Namnet på arbets flödet som ska visas i ett användar gränssnitt |
| WorkflowDescription                      | nvarchar (255) | Ja         | Arbets flödets Beskrivning |
| ContractId                               | int           | Inga          | En unik identifierare för kontraktet |
| ContractProvisioningStatus               | int           | Inga          | Identifierar den aktuella statusen för etablerings processen för kontraktet. Möjliga värden: <br />0 – kontraktet har skapats av API: et i databasen<br />1 – kontraktet har skickats till redovisningen<br />2 – kontraktet har distribuerats till redovisningen<br />3 eller 4 – Det gick inte att distribuera kontraktet till redovisningen<br />5 – kontraktet har distribuerats till redovisningen <br /><br />Från och med version 1,5 stöds värdena 0 till 5. För bakåtkompatibilitet i den aktuella versionen är Visa **vwContractActionV0** tillgängligt som bara stöder värdena 0 till och med 2. |
| ContractCodeId                           | int           | Inga          | En unik identifierare för kod implementeringen av kontraktet |
| ContractLedgerIdentifier                 | nvarchar (255) | Ja         | En unik identifierare som är associerad med den distribuerade versionen av ett smart kontrakt för en enskild distribuerad redovisning. Till exempel Ethereum. |
| ContractDeployedByUserId                 | int           | Inga          | Unikt ID för den användare som distribuerade kontraktet |
| ContractDeployedByUserFirstName          | nvarchar (50)  | Ja         | Förnamn för den användare som distribuerade kontraktet |
| ContractDeployedByUserLastName           | nvarchar (255) | Ja         | Efter namnet på den användare som distribuerade kontraktet |
| ContractDeployedByUserExternalId         | nvarchar (255) | Inga          | Extern identifierare för den användare som distribuerade kontraktet. Som standard är det här ID: t det GUID som representerar identiteten i konsortiet Azure Active Directory.                                                                                                                                                |
| ContractDeployedByUserEmailAddress       | nvarchar (255) | Ja         | E-postadressen för den användare som distribuerade kontraktet |
| WorkflowFunctionId                       | int           | Inga          | En unik identifierare för en arbets flödes funktion |
| WorkflowFunctionName                     | nvarchar (50)  | Inga          | Namnet på funktionen |
| WorkflowFunctionDisplayName              | nvarchar (255) | Inga          | Namnet på en funktion som ska visas i användar gränssnittet |
| WorkflowFunctionDescription              | nvarchar (255) | Inga          | Beskrivning av funktionen |
| ContractActionId                         | int           | Inga          | Den unika identifieraren för en kontrakts åtgärd |
| ContractActionProvisioningStatus         | int           | Inga          | Identifierar den aktuella statusen för etablerings processen för kontrakt åtgärden. Möjliga värden: <br />0 – kontrakt åtgärden har skapats av API: et i databasen<br />1 – kontrakt åtgärden har skickats till redovisningen<br />2 – kontrakt åtgärden har distribuerats till redovisningen<br />3 eller 4 – Det gick inte att distribuera kontraktet till redovisningen<br />5 – kontraktet har distribuerats till redovisningen <br /><br />Från och med version 1,5 stöds värdena 0 till 5. För bakåtkompatibilitet i den aktuella versionen är Visa **vwContractActionV0** tillgängligt som bara stöder värdena 0 till och med 2. |
| ContractActionTimestamp                  | datetime (2, 7) | Inga          | Tidsstämpeln för kontrakt åtgärden |
| ContractActionExecutedByUserId           | int           | Inga          | Unikt ID för den användare som utförde kontrakt åtgärden |
| ContractActionExecutedByUserFirstName    | int           | Ja         | Förnamn för den användare som körde kontrakt åtgärden |
| ContractActionExecutedByUserLastName     | nvarchar (50)  | Ja         | Efter namnet på den användare som körde kontrakt åtgärden |
| ContractActionExecutedByUserExternalId   | nvarchar (255) | Ja         | Extern identifierare för den användare som körde kontrakt åtgärden. Som standard är det här ID: t det GUID som representerar identiteten i konsortiet Azure Active Directory. |
| ContractActionExecutedByUserEmailAddress | nvarchar (255) | Ja         | E-postadressen för den användare som körde kontrakt åtgärden |
| WorkflowFunctionParameterId              | int           | Inga          | En unik identifierare för en parameter i funktionen |
| WorkflowFunctionParameterName            | nvarchar (50)  | Inga          | Namnet på en parameter i funktionen |
| WorkflowFunctionParameterDisplayName     | nvarchar (255) | Inga          | Namnet på en funktions parameter som ska visas i användar gränssnittet |
| WorkflowFunctionParameterDataTypeId      | int           | Inga          | Den unika identifieraren för den datatyp som är associerad med en arbets flödes funktions parameter |
| WorkflowParameterDataTypeName            | nvarchar (50)  | Inga          | Namnet på en datatyp som är associerad med en arbets flödes funktions parameter |
| ContractActionParameterValue             | nvarchar (255) | Inga          | Värdet för den parameter som lagras i det smarta kontraktet |
| BlockHash                                | nvarchar (255) | Ja         | Hash för blocket |
| BlockNumber                              | int           | Ja         | Numret på blocket i redovisningen |
| BlockTimestamp                           | datetime (2, 7) | Ja         | Tidsstämpeln för blocket |
| TransactionId                            | int           | Inga          | En unik identifierare för transaktionen |
| TransactionFrom                          | nvarchar (255) | Ja         | Den part som har inhämtat transaktionen |
| TransactionTo                            | nvarchar (255) | Ja         | Den part som överfördes med |
| TransactionHash                          | nvarchar (255) | Ja         | Hash för en transaktion |
| TransactionIsWorkbenchTransaction        | bit           | Ja         | En bit som identifierar om transaktionen är en Azure blockchain Workbench-transaktion |
| TransactionProvisioningStatus            | int           | Ja         | Identifierar den aktuella statusen för etablerings processen för transaktionen. Möjliga värden: <br />0 – transaktionen har skapats av API: t i databasen<br />1 – transaktionen har skickats till redovisningen<br />2 – transaktionen har distribuerats till redovisningen                 |
| TransactionValue                         | decimal (32, 2) | Ja         | Transaktionens värde |

## <a name="vwcontractproperty"></a>vwContractProperty

Den här vyn representerar merparten av information som rör egenskaper som är associerade med ett kontrakt och som är utformade för att enkelt under lätta vanliga rapporterings scenarier. För varje egenskap som har tagits innehåller den här vyn följande data:

-   Associerad program definition
-   Associerad arbets flödes definition
-   Information för den användare som distribuerade arbets flödet
-   Associerad egenskaps definition för smart kontrakt
-   Vissa instans värden för egenskaper
-   Information om kontraktets tillstånds egenskap

| Namn                               | Typ          | Kan vara null | Beskrivning                                                                                                                                                                                                                                                                        |
|------------------------------------|---------------|-------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId                      | int           | Inga          | En unik identifierare för programmet |
| ApplicationName                    | nvarchar (50)  | Inga          | Namnet på programmet |
| ApplicationDisplayName             | nvarchar (255) | Inga          | Namnet som ska visas i ett användar gränssnitt |
| ApplicationEnabled                 | bit           | Inga          | Identifierar om programmet är aktiverat för tillfället.<br />**Obs:** Även om ett program kan reflekteras som inaktiverat i databasen, finns associerade kontrakt kvar på blockchain och data om dessa avtal finns kvar i databasen.                      |
| WorkflowId                         | int           | Inga          | Arbets flödets unika identifierare |
| WorkflowName                       | nvarchar (50)  | Inga          | Namnet på arbets flödet |
| WorkflowDisplayName                | nvarchar (255) | Inga          | Namnet på arbets flödet som visas i användar gränssnittet |
| WorkflowDescription                | nvarchar (255) | Ja         | Arbets flödets Beskrivning |
| ContractId                         | int           | Inga          | Kontraktets unika identifierare |
| ContractProvisioningStatus         | int           | Inga          | Identifierar den aktuella statusen för etablerings processen för kontraktet. Möjliga värden: <br />0 – kontraktet har skapats av API: et i databasen<br />1 – kontraktet har skickats till redovisningen<br />2 – kontraktet har distribuerats till redovisningen<br />3 eller 4 – Det gick inte att distribuera kontraktet till redovisningen<br />5 – kontraktet har distribuerats till redovisningen <br /><br />Från och med version 1,5 stöds värdena 0 till 5. För bakåtkompatibilitet i den aktuella versionen är Visa **vwContractPropertyV0** tillgängligt som bara stöder värdena 0 till och med 2. |
| ContractCodeId                     | int           | Inga          | En unik identifierare för kod implementeringen av kontraktet |
| ContractLedgerIdentifier           | nvarchar (255) | Ja         | En unik identifierare som är associerad med den distribuerade versionen av ett smart kontrakt för en enskild distribuerad redovisning. Till exempel Ethereum. |
| ContractDeployedByUserId           | int           | Inga          | Unikt ID för den användare som distribuerade kontraktet |
| ContractDeployedByUserFirstName    | nvarchar (50)  | Ja         | Förnamn för den användare som distribuerade kontraktet |
| ContractDeployedByUserLastName     | nvarchar (255) | Ja         | Efter namnet på den användare som distribuerade kontraktet |
| ContractDeployedByUserExternalId   | nvarchar (255) | Inga          | Extern identifierare för den användare som distribuerade kontraktet. Som standard är det här ID: t det GUID som representerar identiteten i konsortiet Azure Active Directory |
| ContractDeployedByUserEmailAddress | nvarchar (255) | Ja         | E-postadressen för den användare som distribuerade kontraktet |
| WorkflowPropertyId                 | int           |             | En unik identifierare för en egenskap för ett arbets flöde |
| WorkflowPropertyDataTypeId         | int           | Inga          | ID för egenskapens datatyp |
| WorkflowPropertyDataTypeName       | nvarchar (50)  | Inga          | Namnet på egenskapens datatyp |
| WorkflowPropertyName               | nvarchar (50)  | Inga          | Namnet på arbets flödes egenskapen |
| WorkflowPropertyDisplayName        | nvarchar (255) | Inga          | Visnings namnet för arbets flödes egenskapen |
| WorkflowPropertyDescription        | nvarchar (255) | Ja         | En beskrivning av egenskapen |
| ContractPropertyValue              | nvarchar (255) | Inga          | Värdet för en egenskap i kontraktet |
| StateName                          | nvarchar (50)  | Ja         | Om den här egenskapen innehåller status för kontraktet är det visnings namnet för status. Om den inte är associerad med statusen kommer värdet vara null. |
| StateDisplayName                   | nvarchar (255) | Inga          | Om den här egenskapen innehåller tillstånd är det visnings namnet för statusen. Om den inte är associerad med statusen kommer värdet vara null. |
| StateValue                         | nvarchar (255) | Ja         | Om den här egenskapen innehåller statusen är det tillstånd svärdet. Om den inte är associerad med statusen kommer värdet vara null. |

## <a name="vwcontractstate"></a>vwContractState

Den här vyn representerar merparten av information som rör ett särskilt avtals tillstånd och som är utformad för att enkelt under lätta vanliga rapporterings scenarier. Varje post i den här vyn innehåller följande data:

-   Associerad program definition
-   Associerad arbets flödes definition
-   Information för den användare som distribuerade arbets flödet
-   Associerad egenskaps definition för smart kontrakt
-   Information om kontraktets tillstånds egenskap

| Namn                               | Typ          | Kan vara null | Beskrivning                                                                                                                                                                                                                                                                        |
|------------------------------------|---------------|-------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId                      | int           | Inga          | En unik identifierare för programmet |
| ApplicationName                    | nvarchar (50)  | Inga          | Namnet på programmet |
| ApplicationDisplayName             | nvarchar (255) | Inga          | Namnet som ska visas i ett användar gränssnitt |
| ApplicationEnabled                 | bit           | Inga          | Identifierar om programmet är aktiverat för tillfället.<br />**Obs:** Även om ett program kan reflekteras som inaktiverat i databasen, finns associerade kontrakt kvar på blockchain och data om dessa avtal finns kvar i databasen. |
| WorkflowId                         | int           | Inga          | En unik identifierare för arbets flödet |
| WorkflowName                       | nvarchar (50)  | Inga          | Namnet på arbets flödet |
| WorkflowDisplayName                | nvarchar (255) | Inga          | Namnet som visas i användar gränssnittet |
| WorkflowDescription                | nvarchar (255) | Ja         | Arbets flödets Beskrivning |
| ContractLedgerImplementationId     | nvarchar (255) | Ja         | En unik identifierare som är associerad med den distribuerade versionen av ett smart kontrakt för en enskild distribuerad redovisning. Till exempel Ethereum. |
| ContractId                         | int           | Inga          | En unik identifierare för kontraktet |
| ContractProvisioningStatus         | int           | Inga          |Identifierar den aktuella statusen för etablerings processen för kontraktet. Möjliga värden: <br />0 – kontraktet har skapats av API: et i databasen<br />1 – kontraktet har skickats till redovisningen<br />2 – kontraktet har distribuerats till redovisningen<br />3 eller 4 – Det gick inte att distribuera kontraktet till redovisningen<br />5 – kontraktet har distribuerats till redovisningen <br /><br />Från och med version 1,5 stöds värdena 0 till 5. För bakåtkompatibilitet i den aktuella versionen är Visa **vwContractStateV0** tillgängligt som bara stöder värdena 0 till och med 2. |
| ConnectionId                       | int           | Inga          | En unik identifierare för den blockchain-instans som arbets flödet distribueras till |
| ContractCodeId                     | int           | Inga          | En unik identifierare för kod implementeringen av kontraktet |
| ContractDeployedByUserId           | int           | Inga          | Unikt ID för den användare som distribuerade kontraktet |
| ContractDeployedByUserExternalId   | nvarchar (255) | Inga          | Extern identifierare för den användare som distribuerade kontraktet. Som standard är det här ID: t det GUID som representerar identiteten i konsortiet Azure Active Directory. |
| ContractDeployedByUserFirstName    | nvarchar (50)  | Ja         | Förnamn för den användare som distribuerade kontraktet |
| ContractDeployedByUserLastName     | nvarchar (255) | Ja         | Efter namnet på den användare som distribuerade kontraktet |
| ContractDeployedByUserEmailAddress | nvarchar (255) | Ja         | E-postadressen för den användare som distribuerade kontraktet |
| WorkflowPropertyId                 | int           | Inga          | En unik identifierare för en arbets flödes egenskap |
| WorkflowPropertyDataTypeId         | int           | Inga          | ID för data typen för arbets flödes egenskapen |
| WorkflowPropertyDataTypeName       | nvarchar (50)  | Inga          | Namnet på data typen för arbets flödes egenskapen |
| WorkflowPropertyName               | nvarchar (50)  | Inga          | Namnet på arbets flödes egenskapen |
| WorkflowPropertyDisplayName        | nvarchar (255) | Inga          | Visnings namnet för den egenskap som ska visas i ett användar gränssnitt |
| WorkflowPropertyDescription        | nvarchar (255) | Ja         | Beskrivning av egenskapen |
| ContractPropertyValue              | nvarchar (255) | Inga          | Värdet för en egenskap som lagras i kontraktet |
| StateName                          | nvarchar (50)  | Ja         | Om den här egenskapen innehåller statusen visas visnings namnet för statusen. Om den inte är associerad med statusen kommer värdet vara null. |
| StateDisplayName                   | nvarchar (255) | Inga          | Om den här egenskapen innehåller tillstånd är det visnings namnet för statusen. Om den inte är associerad med statusen kommer värdet vara null. |
| StateValue                         | nvarchar (255) | Ja         | Om den här egenskapen innehåller statusen är det tillstånd svärdet. Om den inte är associerad med statusen kommer värdet vara null. |

## <a name="vwuser"></a>vwUser

Den här vyn innehåller information om de konsortier-medlemmar som är etablerade att använda Azure blockchain Workbench. Som standard fylls data i genom den första etableringen av användaren.

| Namn               | Typ          | Kan vara null | Description                                                                                                                                                                                                                               |
|--------------------|---------------|-------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ID                 | int           | Inga          | En unik identifierare för en användare |
| ExternalID         | nvarchar (255) | Inga          | En extern identifierare för en användare. Som standard är detta ID det GUID som representerar Azure Active Directory-ID: t för användaren. |
| ProvisioningStatus | int           | Inga          |Identifierar den aktuella statusen för etablerings processen för användaren. Möjliga värden: <br />0 – användaren har skapats av API: et<br />1 – en nyckel har associerats med användaren i databasen<br />2 – användaren är helt etablerad |
| FirstName          | nvarchar (50)  | Ja         | Användarens förnamn |
| LastName           | nvarchar (50)  | Ja         | Användarens efter namn |
| EmailAddress       | nvarchar (255) | Ja         | Användarens e-postadress |

## <a name="vwworkflow"></a>vwWorkflow

Den här vyn visar information om de grundläggande metadata för arbets flöden samt arbets flödets funktioner och parametrar. Den innehåller även metadata om det program som är kopplat till arbets flödet. Den här vyn innehåller data från flera underliggande tabeller för att under lätta rapportering av arbets flöden. För varje arbets flöde innehåller den här vyn följande data:

-   Associerad program definition
-   Associerad arbets flödes definition
-   Information om start tillstånd för associerad arbets flöde

| Namn                              | Typ          | Kan vara null | Beskrivning                                                                                                                                |
|-----------------------------------|---------------|-------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId                     | int           | Inga          | En unik identifierare för programmet |
| ApplicationName                   | nvarchar (50)  | Inga          | Namnet på programmet |
| ApplicationDisplayName            | nvarchar (255) | Inga          | Namnet som ska visas i ett användar gränssnitt |
| ApplicationEnabled                | bit           | Inga          | Identifierar om programmet är aktiverat |
| WorkflowId                        | int           | Ja         | En unik identifierare för ett arbets flöde |
| WorkflowName                      | nvarchar (50)  | Inga          | Namnet på arbets flödet |
| WorkflowDisplayName               | nvarchar (255) | Inga          | Namnet som visas i användar gränssnittet |
| WorkflowDescription               | nvarchar (255) | Ja         | Beskrivning av arbets flödet. |
| WorkflowConstructorFunctionId     | int           | Inga          | Identifierare för arbets flödes funktionen som fungerar som konstruktorn för arbets flödet |
| WorkflowStartStateId              | int           | Inga          | En unik identifierare för tillstånd |
| WorkflowStartStateName            | nvarchar (50)  | Inga          | Namnet på tillstånd |
| WorkflowStartStateDisplayName     | nvarchar (255) | Inga          | Namnet som ska visas i användar gränssnittet för status |
| WorkflowStartStateDescription     | nvarchar (255) | Ja         | En beskrivning av arbets flödets tillstånd |
| WorkflowStartStateStyle           | nvarchar (50)  | Ja         | Det här värdet anger procent andelen slutfört som arbets flödet är i det här läget |
| WorkflowStartStateValue           | int           | Inga          | Värdet för tillstånd |
| WorkflowStartStatePercentComplete | int           | Inga          | En text beskrivning som ger ett tips till klienter om hur det här läget återges i användar gränssnittet. Tillstånd som stöds är *lyckade* och *misslyckade* |

## <a name="vwworkflowfunction"></a>vwWorkflowFunction

Den här vyn visar information om de grundläggande metadata för arbets flöden samt arbets flödets funktioner och parametrar. Den innehåller även metadata om det program som är kopplat till arbets flödet. Den här vyn innehåller data från flera underliggande tabeller för att under lätta rapportering av arbets flöden. För varje arbets flödes funktion innehåller den här vyn följande data:

-   Associerad program definition
-   Associerad arbets flödes definition
-   Information om arbets flödes funktion

| Namn                                 | Typ          | Kan vara null | Beskrivning                                                                          |
|--------------------------------------|---------------|-------------|--------------------------------------------------------------------------------------|
| ApplicationId                        | int           | Inga          | En unik identifierare för programmet |
| ApplicationName                      | nvarchar (50)  | Inga          | Namnet på programmet |
| ApplicationDisplayName               | nvarchar (255) | Inga          | Namnet som ska visas i ett användar gränssnitt |
| ApplicationEnabled                   | bit           | Inga          | Identifierar om programmet är aktiverat |
| WorkflowId                           | int           | Inga          | En unik identifierare för ett arbets flöde |
| WorkflowName                         | nvarchar (50)  | Inga          | Namnet på arbets flödet |
| WorkflowDisplayName                  | nvarchar (255) | Inga          | Namnet på arbets flödet som visas i användar gränssnittet |
| WorkflowDescription                  | nvarchar (255) | Ja         | Arbets flödets Beskrivning |
| WorkflowFunctionId                   | int           | Inga          | En unik identifierare för en funktion |
| WorkflowFunctionName                 | nvarchar (50)  | Ja         | Namnet på funktionen |
| WorkflowFunctionDisplayName          | nvarchar (255) | Inga          | Namnet på en funktion som ska visas i användar gränssnittet |
| WorkflowFunctionDescription          | nvarchar (255) | Ja         | Beskrivning av arbets flödes funktionen |
| WorkflowFunctionIsConstructor        | bit           | Inga          | Identifierar om arbets flödes funktionen är konstruktorn för arbets flödet |
| WorkflowFunctionParameterId          | int           | Inga          | En unik identifierare för en parameter för en funktion |
| WorkflowFunctionParameterName        | nvarchar (50)  | Inga          | Namnet på en parameter i funktionen |
| WorkflowFunctionParameterDisplayName | nvarchar (255) | Inga          | Namnet på en funktions parameter som ska visas i användar gränssnittet |
| WorkflowFunctionParameterDataTypeId  | int           | Inga          | En unik identifierare för den datatyp som är associerad med en arbets flödes funktions parameter |
| WorkflowParameterDataTypeName        | nvarchar (50)  | Inga          | Namnet på en datatyp som är associerad med en arbets flödes funktions parameter |

## <a name="vwworkflowproperty"></a>vwWorkflowProperty

Den här vyn representerar de egenskaper som definierats för ett arbets flöde. För varje egenskap innehåller den här vyn följande data:

-   Associerad program definition
-   Associerad arbets flödes definition
-   Information om arbets flödes egenskaper

| Namn                         | Typ          | Kan vara null | Beskrivning                                                                                                                                                                                                                                                   |
|------------------------------|---------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId                | int           | Inga          | En unik identifierare för programmet |
| ApplicationName              | nvarchar (50)  | Inga          | Namnet på programmet |
| ApplicationDisplayName       | nvarchar (255) | Inga          | Namnet som ska visas i ett användar gränssnitt |
| ApplicationEnabled           | bit           | Inga          | Identifierar om programmet är aktiverat för tillfället.<br />**Obs:** Även om ett program kan reflekteras som inaktiverat i databasen, finns associerade kontrakt kvar på blockchain och data om dessa avtal finns kvar i databasen. |
| WorkflowId                   | int           | Inga          | En unik identifierare för arbets flödet |
| WorkflowName                 | nvarchar (50)  | Inga          | Namnet på arbets flödet |
| WorkflowDisplayName          | nvarchar (255) | Inga          | Namnet som ska visas för arbets flödet i ett användar gränssnitt |
| WorkflowDescription          | nvarchar (255) | Ja         | En beskrivning av arbets flödet |
| WorkflowPropertyID           | int           | Inga          | En unik identifierare för en egenskap för ett arbets flöde |
| WorkflowPropertyName         | nvarchar (50)  | Inga          | Egenskapens namn |
| WorkflowPropertyDescription  | nvarchar (255) | Ja         | En beskrivning av egenskapen |
| WorkflowPropertyDisplayName  | nvarchar (255) | Inga          | Namnet som ska visas i ett användar gränssnitt |
| WorkflowPropertyWorkflowId   | int           | Inga          | ID för det arbets flöde som egenskapen är kopplad till |
| WorkflowPropertyDataTypeId   | int           | Inga          | ID för den datatyp som definierats för egenskapen |
| WorkflowPropertyDataTypeName | nvarchar (50)  | Inga          | Namnet på den datatyp som definierats för egenskapen |
| WorkflowPropertyIsState      | bit           | Inga          | Det här fältet anger om den här arbets flödes egenskapen innehåller status för arbets flödet |

## <a name="vwworkflowstate"></a>vwWorkflowState

Den här vyn representerar egenskaperna som är kopplade till ett arbets flöde. För varje kontrakt innehåller den här vyn följande data:

-   Associerad program definition
-   Associerad arbets flödes definition
-   Information om arbets flödes tillstånd

| Namn                         | Typ          | Kan vara null | Beskrivning                                                                                                                                                                                                                                                   |
|------------------------------|---------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId                | int           | Inga          | En unik identifierare för programmet |
| ApplicationName              | nvarchar (50)  | Inga          | Namnet på programmet |
| ApplicationDisplayName       | nvarchar (255) | Inga          | En beskrivning av programmet |
| ApplicationEnabled           | bit           | Inga          | Identifierar om programmet är aktiverat för tillfället.<br />**Obs:** Även om ett program kan reflekteras som inaktiverat i databasen, finns associerade kontrakt kvar på blockchain och data om dessa avtal finns kvar i databasen. |
| WorkflowId                   | int           | Inga          | Arbets flödets unika identifierare |
| WorkflowName                 | nvarchar (50)  | Inga          | Namnet på arbets flödet |
| WorkflowDisplayName          | nvarchar (255) | Inga          | Namnet som visas i användar gränssnittet för arbets flödet |
| WorkflowDescription          | nvarchar (255) | Ja         | Arbets flödets Beskrivning |
| WorkflowStateID              | int           | Inga          | Den unika identifieraren för tillstånd |
| WorkflowStateName            | nvarchar (50)  | Inga          | Namnet på tillstånd |
| WorkflowStateDisplayName     | nvarchar (255) | Inga          | Namnet som ska visas i användar gränssnittet för status |
| WorkflowStateDescription     | nvarchar (255) | Ja         | En beskrivning av arbets flödets tillstånd |
| WorkflowStatePercentComplete | int           | Inga          | Det här värdet anger procent andelen slutfört som arbets flödet är i det här läget |
| WorkflowStateValue           | nvarchar (50)  | Inga          | Värde för tillstånd |
| WorkflowStateStyle           | nvarchar (50)  | Inga          | En text beskrivning som ger ett tips till klienter om hur det här läget återges i användar gränssnittet. Tillstånd som stöds är *lyckade* och *misslyckade* |
