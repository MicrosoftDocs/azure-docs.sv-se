---
title: ta med fil
description: ta med fil
services: azure-communication-services
author: tomaschladek
manager: nmurav
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 08/20/2020
ms.topic: include
ms.custom: include file
ms.author: tchladek
ms.openlocfilehash: 472129be5baa865365b49894b705d84c23e9cd04
ms.sourcegitcommit: 2ba6303e1ac24287762caea9cd1603848331dd7a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/15/2020
ms.locfileid: "97506311"
---
## <a name="prerequisites"></a>Förutsättningar

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Python](https://www.python.org/downloads/) 2,7, 3,5 eller senare.
- En aktiv kommunikations tjänst resurs och anslutnings sträng. [Skapa en kommunikations tjänst resurs](../create-communication-resource.md).

## <a name="setting-up"></a>Konfigurera

### <a name="create-a-new-python-application"></a>Skapa ett nytt Python-program

1. Öppna terminalen eller kommando fönstret Skapa en ny katalog för din app och navigera till den.

   ```console
   mkdir access-tokens-quickstart && cd access-tokens-quickstart
   ```

1. Använd en text redigerare för att skapa en fil med namnet **Issue-Access-tokens.py** i projektets rot Katalog och lägga till strukturen för programmet, inklusive grundläggande undantags hantering. Du kommer att lägga till alla käll koder för den här snabb starten till den här filen i följande avsnitt.

   ```python
   import os
   from azure.communication.administration import CommunicationIdentityClient

   try:
      print('Azure Communication Services - Access Tokens Quickstart')
      # Quickstart code goes here
   except Exception as ex:
      print('Exception:')
      print(ex)
   ```

### <a name="install-the-package"></a>Installera paketet

När du fortfarande är i program katalogen installerar du Azure Communication Services administrations klient bibliotek för python-paket med hjälp av `pip install` kommandot.

```console
pip install azure-communication-administration
```

## <a name="authenticate-the-client"></a>Autentisera klienten

Instansiera en `CommunicationIdentityClient` med anslutnings strängen. Koden nedan hämtar anslutnings strängen för resursen från en miljö variabel med namnet `COMMUNICATION_SERVICES_CONNECTION_STRING` . Lär dig hur [du hanterar anslutnings strängen](../create-communication-resource.md#store-your-connection-string)för din resurs.

Lägg till den här koden inuti `try` blocket:

```python
# This code demonstrates how to fetch your connection string
# from an environment variable.
connection_string = os.environ['COMMUNICATION_SERVICES_CONNECTION_STRING']

# Instantiate the identity client
client = CommunicationIdentityClient.from_connection_string(connection_string)
```

## <a name="create-an-identity"></a>Skapa en identitet

Azure Communication Services upprätthåller en Lightweight Identity-katalog. Använd `create_user` metoden för att skapa en ny post i katalogen med en unik `Id` . Lagra mottagen identitet med mappning till programmets användare. Till exempel genom att lagra dem i program serverns databas. Identiteten krävs senare för att utfärda åtkomsttoken.

```python
identity = client.create_user()
print("\nCreated an identity with ID: " + identity.identifier + ":")
```

## <a name="issue-access-tokens"></a>Utfärda åtkomsttoken

Använd `issue_token` metoden för att utfärda en åtkomsttoken för redan befintlig kommunikations tjänst identitet. Parameter `scopes` definierar uppsättning primitiver som auktoriserar denna åtkomsttoken. Se [listan över åtgärder som stöds](../../concepts/authentication.md). En ny instans av parametern `communicationUser` kan konstrueras baserat på en sträng representation av Azure Communication Service-identiteten.

```python
# Issue an access token with the "voip" scope for an identity
token_result = client.issue_token(identity, ["voip"])
expires_on = token_result.expires_on.strftime('%d/%m/%y %I:%M %S %p')
print("\nIssued an access token with 'voip' scope that expires at " + expires_on + ":")
print(token_result.token)
```

Åtkomsttoken är korta autentiseringsuppgifter som måste återutfärdas. Om du inte gör det kan det orsaka störningar i programmets användar upplevelse. `expires_on`Egenskapen svar anger livs längden för åtkomsttoken.

## <a name="refresh-access-tokens"></a>Uppdatera åtkomsttoken

Om du vill uppdatera en åtkomsttoken använder du `CommunicationUser` objektet för att utfärda följande:

```python  
# Value existingIdentity represents identity of Azure Communication Services stored during identity creation
identity = CommunicationUser(existingIdentity)
token_result = client.issue_token( identity, ["voip"])
```

## <a name="revoke-access-tokens"></a>Återkalla åtkomsttoken

I vissa fall kan du uttryckligen återkalla åtkomsttoken. Till exempel när ett programs användare ändrar lösen ordet som de använder för att autentisera till din tjänst. Metoden `revoke_tokens` ogiltig förklarade alla aktiva åtkomsttoken som utfärdats till identiteten.

```python  
client.revoke_tokens(identity)
print("\nSuccessfully revoked all access tokens for identity with ID: " + identity.identifier)
```

## <a name="delete-an-identity"></a>Ta bort en identitet

Om du tar bort en identitet återkallar du alla aktiva åtkomsttoken och förhindrar att du utfärdar åtkomsttoken för identiteten. Det tar också bort allt beständigt innehåll som är associerat med identiteten.

```python
client.delete_user(identity)
print("\nDeleted the identity with ID: " + identity.identifier)
```

## <a name="run-the-code"></a>Kör koden

Från en konsol tolk navigerar du till katalogen som innehåller filen *Issue-Access-token.py* och kör sedan följande `python` kommando för att köra appen.

```console
python ./issue-access-token.py
```
