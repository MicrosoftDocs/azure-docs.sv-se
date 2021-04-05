---
title: Felsöka problem med åtkomst principer för Azure Key Vault
description: Felsöka problem med åtkomst principer för Azure Key Vault
author: sebansal
ms.author: sebansal
ms.date: 08/10/2020
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.openlocfilehash: c5fab8b856ff9c82a0de887dc9c322dbf541348b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98791415"
---
# <a name="troubleshooting-azure-key-vault-access-policy-issues"></a>Felsöka problem med åtkomst principer för Azure Key Vault

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

### <a name="i-am-not-able-to-list-or-get-secretskeyscertificate-i-am-seeing-something-went-wrong-error"></a>Jag kan inte Visa eller hämta hemligheter/nycklar/certifikat. Jag ser "något gick fel." Fels.
Om du har problem med att lista/Hämta/skapa eller komma åt hemlighet måste du kontrol lera att du har definierat åtkomst princip för att utföra åtgärden: [Key Vault åtkomst principer](./assign-access-policy-cli.md)

### <a name="how-can-i-identify-how-and-when-key-vaults-are-accessed"></a>Hur gör jag för att identifiera hur och när nyckel valv används?

När du har skapat ett eller flera nyckel valv vill du förmodligen övervaka hur och när nyckel valven nås, och av vem. Du kan övervaka genom att aktivera loggning för Azure Key Vault, för steg-för-steg-guide för att aktivera loggning, [Läs mer](./logging.md).

### <a name="how-can-i-monitor-vault-availability-service-latency-periods-or-other-performance-metrics-for-key-vault"></a>Hur kan jag övervaka valv tillgänglighet, tjänst svars perioder eller andra prestanda mått för Key Vault?

När du börjar skala tjänsten kommer antalet förfrågningar som skickats till ditt nyckel valv att öka. Sådan efter frågan har möjlighet att öka svars tiden för dina begär Anden och i extrema fall, orsaka att dina förfrågningar begränsas, vilket påverkar tjänstens prestanda. Du kan övervaka prestanda mått för nyckel valv och få aviseringar för vissa tröskelvärden, för steg-för-steg-guide för att konfigurera övervakning, [läsa mer](./alert.md).

### <a name="i-am-not-able-to-modify-access-policy-how-can-it-be-enabled"></a>Jag kan inte ändra åtkomst principen, hur kan den aktive ras?
Användaren måste ha tillräcklig AAD-behörighet för att ändra åtkomst principen. I det här fallet måste användaren ha högre deltagar roll.

### <a name="i-am-seeing-unknown-policy-error-what-does-that-mean"></a>Jag ser "okänd princip"-fel. Vad innebär det?
Det finns två olika möjligheter att se åtkomst principen i okänt avsnitt:
* Det kan finnas en tidigare användare som hade åtkomst och av någon anledning att användaren inte finns.
* Om åtkomst principen läggs till via PowerShell och åtkomst principen läggs till för programmet ObjectID i stället för tjänstens huvud namn.

### <a name="how-can-i-assign-access-control-per-key-vault-object"></a>Hur tilldelar jag åtkomst kontroll per Key Vault-objekt? 

Tillgänglighet för användar kontroll per hemlighet/nyckel/certifikat kommer att meddelas här, [läsa mer](https://feedback.azure.com/forums/906355-azure-key-vault/suggestions/32213176-per-secret-key-certificate-access-control)

### <a name="how-can-i-provide-key-vault-authenticate-using-access-control-policy"></a>Hur kan jag tillhandahålla Key Vault-autentisering med hjälp av principer för åtkomst kontroll?

Det enklaste sättet att autentisera ett molnbaserad program till Key Vault är med en hanterad identitet. Mer information finns i avsnittet [autentisera till Azure Key Vault](authentication.md) .
Om du skapar ett lokal program, gör en lokal utveckling eller inte kan använda en hanterad identitet, kan du istället registrera ett huvud namn för tjänsten manuellt och ge åtkomst till ditt nyckel valv med hjälp av en princip för åtkomst kontroll. Se [tilldela en princip för åtkomst kontroll](assign-access-policy-portal.md).

### <a name="how-can-i-give-the-ad-group-access-to-the-key-vault"></a>Hur kan jag ge AD-gruppen åtkomst till nyckel valvet?

Ge AD-gruppen behörighet till ditt nyckel valv med hjälp av Azure CLI `az keyvault set-policy` -kommandot eller Azure PowerShell Set-AzKeyVaultAccessPolicy-cmdleten. Se [tilldela en åtkomst princip – CLI](assign-access-policy-cli.md) och [tilldela en åtkomst princip – PowerShell](assign-access-policy-powershell.md).

Programmet behöver också minst en roll för identitets-och åtkomst hantering (IAM) som har tilldelats nyckel valvet. Annars kommer den inte att kunna logga in och kommer att Miss lyckas med otillräcklig behörighet för att komma åt prenumerationen. Azure AD-grupper med hanterade identiteter kan kräva upp till åtta timmar för att uppdatera tokens och bli gällande.

### <a name="how-can-i-redeploy-key-vault-with-arm-template-without-deleting-existing-access-policies"></a>Hur kan jag distribuera om Key Vault med ARM-mall utan att ta bort befintliga åtkomst principer?

För närvarande Key Vault omdistribution tar bort alla åtkomst principer i Key Vault och ersätter dem med åtkomst principen i ARM-mallen. Det finns inget stegvist alternativ för Key Vault åtkomst principer. Om du vill bevara åtkomst principer i Key Vault måste du läsa befintliga åtkomst principer i Key Vault och fylla i ARM-mallen med dessa principer för att undvika eventuella åtkomst avbrott.

Ett annat alternativ som kan hjälpa till med det här scenariot är att använda Azure RBAC och roller som ett alternativ till att få åtkomst till principer. Med Azure RBAC kan du distribuera nyckel valvet igen utan att ange principen igen. Du kan läsa mer om den här lösningen [här](./rbac-guide.md).

### <a name="recommended-troubleshooting-steps-for-following-error-types"></a>Rekommenderade fel söknings steg för följande feltyper

* HTTP 401: Oautentiserad begäran – [felsökningssteg](rest-error-codes.md#http-401-unauthenticated-request)
* HTTP 403: Otillräcklig behörighet – [felsökningssteg](rest-error-codes.md#http-403-insufficient-permissions)
* HTTP 429: För många begäranden – [felsökningssteg](rest-error-codes.md#http-429-too-many-requests)
* Kontrol lera om du har behörighet att ta bort åtkomst till Key Vault: se [tilldela en åtkomst princip – CLI](assign-access-policy-cli.md), [tilldela en åtkomst princip – PowerShell](assign-access-policy-powershell.md)eller [tilldela en åtkomst princip – Portal](assign-access-policy-portal.md).
* Om du har problem med att autentisera till nyckelvalv i kod använder du [SDK för autentisering](https://azure.github.io/azure-sdk/posts/2020-02-25/defaultazurecredentials.html)

### <a name="what-are-the-best-practices-i-should-implement-when-key-vault-is-getting-throttled"></a>Vilka är de bästa metoderna jag bör implementera när Key Vault får en begränsning?
Följ de bästa metoderna, dokumenterade [här](overview-throttling.md#how-to-throttle-your-app-in-response-to-service-limits)

## <a name="next-steps"></a>Nästa steg

Lär dig hur du felsöker Key Vault-autentiseringsfel: [Key Vault fel söknings guide](rest-error-codes.md).