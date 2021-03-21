---
author: normesta
ms.service: storage
ms.topic: include
ms.date: 09/28/2020
ms.author: normesta
ms.openlocfilehash: ca8963ed8928745a6d5918c86021199432339c83
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104612794"
---
| Egenskap | Beskrivning |
|:--- |:---|
|**identitet/typ** | Den typ av autentisering som användes för att utföra begäran. Till exempel: `OAuth` ,,, `Kerberos` `SAS Key` `Account Key` eller `Anonymous` |
|**identitets-tokenHash**|SHA-256-hashen för autentiseringstoken som används i begäran. <br>När autentiseringstypen är är `Account Key` formatet "KEY1 \| KEY2 (SHA256-hash för nyckeln)". Exempel: `key1(5RTE343A6FEB12342672AFD40072B70D4A91BGH5CDF797EC56BF82B2C3635CE)`. <br>När autentiseringstypen är är `SAS Key` formatet "KEY1 \| KEY2 (SHA 256 hash av nyckeln), SASSIGNATURE (SHA 256 hash för SAS-token)". Exempel: `key1(0A0XE8AADA354H19722ED12342443F0DC8FAF3E6GF8C8AD805DE6D563E0E5F8A),SasSignature(04D64C2B3A704145C9F1664F201123467A74D72DA72751A9137DDAA732FA03CF)`. När autentiseringstypen är är `OAuth` formatet "SHA 256 hash av OAuth-token". Exempelvis: `B3CC9D5C64B3351573D806751312317FE4E910877E7CBAFA9D95E0BE923DW25C`<br> Det finns inget tokenHash-fält för andra typer av autentisering. |
|**auktorisering/åtgärd** | Den åtgärd som har tilldelats begäran. |
|**auktoriserings-roleAssignmentId** | Roll tilldelnings-ID: t. Exempel: `4e2521b7-13be-4363-aeda-111111111111`.|
|**auktoriserings-roleDefinitionId** | Roll Definitions-ID: t. Exempel: `ba92f5b4-2d11-453d-a403-111111111111"`.|
|**huvud namn/ID** | Säkerhets objektets ID. Exempel: `a4711f3a-254f-4cfb-8a2d-111111111111`.|
|**objekt/typ** | Typ av säkerhets objekt. Exempel: `ServicePrincipal`. |
|**beställare/appID** | Det Open Authorization (OAuth) program-ID som används som beställare. <br> Exempel: `d3f7d5fe-e64a-4e4e-871d-333333333333`.|
|**beställare/mål grupp** | OAuth-mål för begäran. Exempel: `https://storage.azure.com`. |
|**beställare/objectId** | OAuth-objekt-ID för beställaren. I händelse av Kerberos-autentisering representerar objekt identifieraren för Kerberos-autentiserad användare. Exempel: `0e0bf547-55e5-465c-91b7-2873712b249c`. |
|**beställare/tenantId** | Identitet för OAuth-klient-ID. Exempel: `72f988bf-86f1-41af-91ab-222222222222`.|
|**beställare/tokenIssuer** | Utfärdaren av OAuth-token. Exempel: `https://sts.windows.net/72f988bf-86f1-41af-91ab-222222222222/`.|
|**beställare/UPN** | Användarens huvud namn (UPN) för begär Ande. Exempel: `someone@contoso.com`. |
|**beställare/användar namn** | Det här fältet är reserverat för internt bruk.|
