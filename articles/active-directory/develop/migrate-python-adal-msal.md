---
title: Guide för att migrera python-ADAL till MSAL | Azure
titleSuffix: Microsoft identity platform
description: Lär dig hur du migrerar din ADAL-app (Azure Active Directory Authentication Library) till Microsoft Authentication Library (MSAL) för python.
services: active-directory
author: rayluo
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.tgt_pltfrm: Python
ms.workload: identity
ms.date: 11/11/2019
ms.author: rayluo
ms.reviewer: marsma, rayluo, nacanuma
ms.custom: aaddev, devx-track-python
ms.openlocfilehash: 42ffc7ffba20868b23675fd8613fd3ef11b0924a
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/25/2021
ms.locfileid: "98755057"
---
# <a name="adal-to-msal-migration-guide-for-python"></a>ADAL till MSAL migration-guide för python

I den här artikeln beskrivs de ändringar du behöver göra för att migrera en app som använder ADAL (Azure Active Directory Authentication Library) för att använda Microsoft Authentication Library (MSAL).

## <a name="difference-highlights"></a>Skillnader i fokus

ADAL fungerar med Azure Active Directory (Azure AD) v 1.0-slutpunkten. Microsoft Authentication Library (MSAL) fungerar med Microsoft Identity Platform – tidigare kallat Azure Active Directory v 2.0-slutpunkten. Microsoft Identity Platform skiljer sig från Azure AD v 1.0 på så här:

Uppfyller
  - Arbets-och skol konton (etablerade Azure AD-konton)
  - Personliga konton (t. ex. Outlook.com eller Hotmail.com)
  - Dina kunder som tar sin egen e-post eller sociala identitet (till exempel LinkedIn, Facebook, Google) via det Azure AD B2C erbjudandet

- Är standarder som är kompatibla med:
  - OAuth v 2.0
  - OpenID Connect (OIDC)

Se [vad som är annorlunda om Microsoft Identity Platform?](../azuread-dev/azure-ad-endpoint-comparison.md) för mer information.

### <a name="scopes-not-resources"></a>Webbprogramsomfattande omfattningar

ADAL python hämtar token för resurser, men MSAL python hämtar token för omfattningar. API-ytan i MSAL python har inte längre någon resurs parameter. Du måste ange omfattningar som en lista med strängar som deklarerar önskade behörigheter och resurser som begärs. För att se några exempel på omfattningar, se [Microsoft graphs omfång](/graph/permissions-reference).

Du kan lägga till `/.default` scope-suffixet till resursen för att hjälpa till att migrera dina appar från v 1.0-slutpunkten (ADAL) till Microsoft Identity Platform (MSAL). För resurs värdet för är till exempel `https://graph.microsoft.com` värdet för motsvarande omfång `https://graph.microsoft.com/.default` .  Om resursen inte finns i URL-formuläret, men ett resurs-ID för formuläret `XXXXXXXX-XXXX-XXXX-XXXXXXXXXXXX` , kan du fortfarande använda omfångs värdet som `XXXXXXXX-XXXX-XXXX-XXXXXXXXXXXX/.default` .

För mer information om de olika typerna av omfattningar, se [behörigheter och medgivande i Microsoft Identity Platform](./v2-permissions-and-consent.md) och [scope för ett webb-API som accepterar v 1.0-token-](./msal-v1-app-scopes.md) artiklar.

### <a name="error-handling"></a>Felhantering

Azure Active Directory Authentication Library (ADAL) för python använder undantaget `AdalError` för att indikera att det har uppstått ett problem. MSAL for python använder vanligt vis felkoder i stället. Mer information finns i [MSAL för hantering av python-fel](msal-error-handling-python.md).

### <a name="api-changes"></a>API-ändringar

I följande tabell visas ett API i ADAL för python, och det som ska användas på dess ställe i MSAL för python:

| ADAL för python-API  | MSAL för python-API |
| ------------------- | ---------------------------------- |
| [AuthenticationContext](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext)  | [PublicClientApplication eller ConfidentialClientApplication](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.__init__)  |
| Ej tillämpligt  | [get_authorization_request_url ()](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.get_authorization_request_url)  |
| [acquire_token_with_authorization_code ()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_authorization_code) | [acquire_token_by_authorization_code ()](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.acquire_token_by_authorization_code) |
| [acquire_token ()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token) | [acquire_token_silent ()](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.acquire_token_silent) |
| [acquire_token_with_refresh_token ()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_refresh_token) | Ej tillämpligt |
| [acquire_user_code ()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_user_code) | [initiate_device_flow ()](https://msal-python.readthedocs.io/en/latest/#msal.PublicClientApplication.initiate_device_flow) |
| [acquire_token_with_device_code ()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_device_code) och [cancel_request_to_get_token_with_device_code ()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.cancel_request_to_get_token_with_device_code) | [acquire_token_by_device_flow ()](https://msal-python.readthedocs.io/en/latest/#msal.PublicClientApplication.acquire_token_by_device_flow) |
| [acquire_token_with_username_password ()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_username_password) | [acquire_token_by_username_password ()](https://msal-python.readthedocs.io/en/latest/#msal.PublicClientApplication.acquire_token_by_username_password) |
| [acquire_token_with_client_credentials ()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_client_credentials) och [acquire_token_with_client_certificate ()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_client_certificate) | [acquire_token_for_client ()](https://msal-python.readthedocs.io/en/latest/#msal.ConfidentialClientApplication.acquire_token_for_client) |
| Ej tillämpligt | [acquire_token_on_behalf_of ()](https://msal-python.readthedocs.io/en/latest/#msal.ConfidentialClientApplication.acquire_token_on_behalf_of) |
| [TokenCache ()](https://adal-python.readthedocs.io/en/latest/#adal.TokenCache) | [SerializableTokenCache()](https://msal-python.readthedocs.io/en/latest/#msal.SerializableTokenCache) |
| Ej tillämpligt | Cachelagra med persistence, tillgängligt från [MSAL-tillägg](https://github.com/marstr/original-microsoft-authentication-extensions-for-python) |

## <a name="migrate-existing-refresh-tokens-for-msal-python"></a>Migrera befintliga uppdateringstoken för MSAL python

Microsoft Authentication Library (MSAL) är ett sammandrag av begreppet uppdateringstoken. MSAL python tillhandahåller en token cache i minnet som standard, så att du inte behöver lagra, söka eller uppdatera uppdateringstoken. Användarna kommer också att se färre inloggnings meddelanden eftersom uppdateringstoken ofta kan uppdateras utan att användaren behöver göra något. Mer information om token cache finns i [anpassat token cache-serialisering i MSAL för python](msal-python-token-cache-serialization.md).

Följande kod hjälper dig att migrera dina uppdateringstoken som hanteras av ett annat OAuth2-bibliotek (inklusive men inte begränsat till ADAL python) som ska hanteras av MSAL för python. En anledning till att migrera dessa uppdateringstoken är att förhindra att befintliga användare behöver logga in igen när du migrerar din app till MSAL för python.

Metoden för att migrera en uppdateringstoken är att använda MSAL för python för att hämta en ny åtkomsttoken med hjälp av föregående uppdateringstoken. När den nya uppdateringstoken returneras kommer MSAL för python att lagra den i cacheminnet.
Eftersom MSAL python 1.3.0 tillhandahåller vi ett API i MSAL för detta ändamål.
Se följande kodfragment som citeras från [ett slutfört exempel på migrering av uppdateringstoken med MSAL python](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/1.3.0/sample/migrate_rt.py#L28-L67)

```python
import msal
def get_preexisting_rt_and_their_scopes_from_elsewhere():
    # Maybe you have an ADAL-powered app like this
    #   https://github.com/AzureAD/azure-activedirectory-library-for-python/blob/1.2.3/sample/device_code_sample.py#L72
    # which uses a resource rather than a scope,
    # you need to convert your v1 resource into v2 scopes
    # See https://docs.microsoft.com/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison#scopes-not-resources
    # You may be able to append "/.default" to your v1 resource to form a scope
    # See https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#the-default-scope

    # Or maybe you have an app already talking to the Microsoft identity platform,
    # powered by some 3rd-party auth library, and persist its tokens somehow.

    # Either way, you need to extract RTs from there, and return them like this.
    return [
        ("old_rt_1", ["scope1", "scope2"]),
        ("old_rt_2", ["scope3", "scope4"]),
        ]


# We will migrate all the old RTs into a new app powered by MSAL
app = msal.PublicClientApplication(
    "client_id", authority="...",
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache
    )

# We choose a migration strategy of migrating all RTs in one loop
for old_rt, scopes in get_preexisting_rt_and_their_scopes_from_elsewhere():
    result = app.acquire_token_by_refresh_token(old_rt, scopes)
    if "error" in result:
        print("Discarding unsuccessful RT. Error: ", json.dumps(result, indent=2))

print("Migration completed")
```


## <a name="next-steps"></a>Nästa steg

Mer information finns i [jämförelse mellan v 1.0 och v 2.0](../azuread-dev/azure-ad-endpoint-comparison.md).
