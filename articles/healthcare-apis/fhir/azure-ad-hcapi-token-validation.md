---
title: Verifiering av Azure API för FHIR-åtkomsttoken
description: Vägleder dig genom verifiering av token och ger tips om hur du felsöker åtkomst problem
services: healthcare-apis
author: caitlinv39
ms.reviewer: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: cavoeg
ms.openlocfilehash: 426ec0c2d6cc274aa0b6829eb4a30fd29b9ba8e2
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/11/2021
ms.locfileid: "103020278"
---
# <a name="azure-api-for-fhir-access-token-validation"></a>Verifiering av Azure API för FHIR-åtkomsttoken

Hur Azure API för FHIR verifierar att åtkomsttoken beror på implementering och konfiguration. I den här artikeln går vi igenom validerings stegen, som kan vara till hjälp vid fel sökning av åtkomst problem.

## <a name="validate-token-has-no-issues-with-identity-provider"></a>Verifiera token har inga problem med identitets leverantören

Det första steget i verifieringen av token är att verifiera att token har utfärdats av rätt identitetsprovider och att den inte har ändrats. FHIR-servern kommer att konfigureras för att använda en viss identitets leverantör som kallas utfärdare `Authority` . FHIR-servern kommer att hämta information om identitets leverantören från `/.well-known/openid-configuration` slut punkten. När du använder Azure AD skulle den fullständiga URL: en vara:

```
GET https://login.microsoftonline.com/<TENANT-ID>/.well-known/openid-configuration
```

där `<TENANT-ID>` är den särskilda Azure AD-klienten (antingen ett klient-ID eller ett domän namn).

Azure AD kommer att returnera ett dokument som det nedan till FHIR-servern.

```json
{
    "authorization_endpoint": "https://login.microsoftonline.com/<TENANT-ID>/oauth2/authorize",
    "token_endpoint": "https://login.microsoftonline.com/<TENANT-ID>/oauth2/token",
    "token_endpoint_auth_methods_supported": [
        "client_secret_post",
        "private_key_jwt",
        "client_secret_basic"
    ],
    "jwks_uri": "https://login.microsoftonline.com/common/discovery/keys",
    "response_modes_supported": [
        "query",
        "fragment",
        "form_post"
    ],
    "subject_types_supported": [
        "pairwise"
    ],
    "id_token_signing_alg_values_supported": [
        "RS256"
    ],
    "http_logout_supported": true,
    "frontchannel_logout_supported": true,
    "end_session_endpoint": "https://login.microsoftonline.com/<TENANT-ID>/oauth2/logout",
    "response_types_supported": [
        "code",
        "id_token",
        "code id_token",
        "token id_token",
        "token"
    ],
    "scopes_supported": [
        "openid"
    ],
    "issuer": "https://sts.windows.net/<TENANT-ID>/",
    "claims_supported": [
        "sub",
        "iss",
        "cloud_instance_name",
        "cloud_instance_host_name",
        "cloud_graph_host_name",
        "msgraph_host",
        "aud",
        "exp",
        "iat",
        "auth_time",
        "acr",
        "amr",
        "nonce",
        "email",
        "given_name",
        "family_name",
        "nickname"
    ],
    "microsoft_multi_refresh_token": true,
    "check_session_iframe": "https://login.microsoftonline.com/<TENANT-ID>/oauth2/checksession",
    "userinfo_endpoint": "https://login.microsoftonline.com/<TENANT-ID>/openid/userinfo",
    "tenant_region_scope": "WW",
    "cloud_instance_name": "microsoftonline.com",
    "cloud_graph_host_name": "graph.windows.net",
    "msgraph_host": "graph.microsoft.com",
    "rbac_url": "https://pas.windows.net"
}
``` 
De viktiga egenskaperna för FHIR-servern är `jwks_uri` , som talar om för servern var de krypterings nycklar som behövs för att validera token-signaturen ska hämtas och `issuer` vilka som talar om för servern vad som ska finnas i utfärdarens anspråk ( `iss` ) för token som utfärdats av den här servern. FHIR-servern kan använda detta för att kontrol lera att den tar emot en äkta token.

## <a name="validate-claims-of-the-token"></a>Verifiera anspråk för token

När servern har verifierat äktheten för token kommer FHIR-servern att fortsätta att verifiera att klienten har de nödvändiga anspråk för att få åtkomst till token.

När du använder Azure API för FHIR verifierar-servern:

1. Token har rätt `Audience` ( `aud` anspråk).
1. Användaren eller huvudobjektet som token utfärdades för får åtkomst till FHIR-serverns data plan. `oid`Anspråket för token innehåller ett Identity-objekt-ID som unikt identifierar användaren eller huvud kontot.

Vi rekommenderar att FHIR-tjänsten [konfigureras för att använda Azure RBAC](configure-azure-rbac.md) för att hantera roll tilldelningar för data planet. Men du kan också [Konfigurera lokal RBAC](configure-local-rbac.md) om din FHIR-tjänst använder en extern eller sekundär Azure Active Directory klient. 

När du använder OSS Microsoft FHIR-servern för Azure kommer servern att verifiera:

1. Token har rätt `Audience` ( `aud` anspråk).
1. Token har en roll i `roles` anspråket som har åtkomst till FHIR-servern.

Se information om hur du [definierar roller på FHIR-servern](https://github.com/microsoft/fhir-server/blob/master/docs/Roles.md).

En FHIR-Server kan också verifiera att en åtkomsttoken har omfattningarna (i token `scp` -anspråk) för att komma åt den del av FHIR-API: et som en klient försöker komma åt. Azure API för FHIR och FHIR-servern för Azure validerar för närvarande inte token-scope.

## <a name="next-steps"></a>Nästa steg
Nu när du vet hur du går igenom valideringen av token kan du slutföra självstudien för att skapa ett JavaScript-program och läsa FHIR-data.

>[!div class="nextstepaction"]
>[Självstudie för webb program](tutorial-web-app-fhir-server.md)