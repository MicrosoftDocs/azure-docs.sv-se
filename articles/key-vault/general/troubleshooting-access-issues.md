---
title: Felsöka problem med åtkomst principer för Azure Key Vault
description: Felsöka problem med åtkomst principer för Azure Key Vault
author: sebansal
ms.author: sebansal
ms.date: 08/10/2020
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.openlocfilehash: f31782fb4e91b72f51d6f0550fe9010cba7ca3d6
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2020
ms.locfileid: "88585465"
---
# <a name="troubleshooting-azure-key-vault-access-policy-issues"></a>Felsöka problem med åtkomst principer för Azure Key Vault

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

### <a name="how-can-i-identify-how-and-when-key-vaults-are-accessed"></a>Hur gör jag för att identifiera hur och när nyckel valv används?
När du har skapat ett eller flera nyckel valv vill du förmodligen övervaka hur och när nyckel valven nås, och av vem. Du kan göra detta genom att aktivera loggning för Azure Key Vault, för steg-för-steg-guide för att aktivera loggning, [Läs mer](https://docs.microsoft.com/azure/key-vault/general/logging).

### <a name="how-can-i-monitor-vault-availability-service-latency-periods-or-other-performance-metrics-for-key-vault"></a>Hur kan jag övervaka valv tillgänglighet, tjänst svars perioder eller andra prestanda mått för Key Vault?
När du börjar skala din tjänst kommer antalet förfrågningar som skickas till ditt nyckel valv att öka. Detta har möjlighet att öka svars tiden för dina begär Anden och i extrema fall, orsaka att dina förfrågningar begränsas som påverkar tjänstens prestanda. Du kan övervaka prestanda mått för nyckel valv och få aviseringar för vissa tröskelvärden, för steg-för-steg-guide för att konfigurera övervakning, [läsa mer](https://docs.microsoft.com/azure/key-vault/general/alert).

### <a name="how-can-i-assign-access-control-per-key-vault-object"></a>Hur tilldelar jag åtkomst kontroll per Key Vault-objekt? 
Tillgänglighet för användar kontroll per hemlighet/nyckel/certifikat kommer att meddelas här, [läsa mer](https://feedback.azure.com/forums/906355-azure-key-vault/suggestions/.32213176-per-secret-key-certificate-access-control)

### <a name="how-can-i-provide-key-vault-authenticate-using-access-control-policy"></a>Hur kan jag tillhandahålla Key Vault-autentisering med hjälp av principer för åtkomst kontroll?
Det enklaste sättet att autentisera ett molnbaserad program till Key Vault är med en hanterad identitet. Mer information finns i [använda en app service hanterad identitet för att få åtkomst till Azure Key Vault]( https://docs.microsoft.com/azure/key-vault/general/managed-identity) .
Om du skapar ett lokal program, gör en lokal utveckling eller inte kan använda en hanterad identitet, kan du istället registrera ett huvud namn för tjänsten manuellt och ge åtkomst till ditt nyckel valv med hjälp av en princip för åtkomst kontroll, [Läs mer](https://docs.microsoft.com/azure/key-vault/general/group-permissions-for-apps).


### <a name="how-can-i-give-the-ad-group-access-to-the-key-vault"></a>Hur kan jag ge AD-gruppen åtkomst till nyckel valvet?
Ge AD-gruppen behörighet till ditt nyckel valv med hjälp av Azure CLI-AZ för att ange princip, eller cmdleten Azure PowerShell set-AzKeyVaultAccessPolicy. Granska till exempel [ge programmet, Azure AD-gruppen eller användar åtkomst till ditt nyckel valv](https://docs.microsoft.com/azure/key-vault/general/group-permissions-for-apps#give-the-principal-access-to-your-key-vault).

Programmet behöver också minst en roll för identitets-och åtkomst hantering (IAM) som har tilldelats nyckel valvet. Annars kommer den inte att kunna logga in och kommer att Miss lyckas med otillräcklig behörighet för att komma åt prenumerationen. Azure AD-grupper med hanterade identiteter kan kräva upp till 8hr för att uppdatera token och bli gällande.

### <a name="how-can-i-redeploy-key-vault-with-arm-template-without-deleting-existing-access-policies"></a>Hur kan jag distribuera om Key Vault med ARM-mall utan att ta bort befintliga åtkomst principer?
För närvarande tar Key Vault ARM redopleyment bort alla åtkomst principer i Key Vault och ersätter dem med åtkomst principen i ARM-mallen. Det finns inget stegvist alternativ för Key Vault åtkomst principer. Om du vill bevara åtkomst principerna i Key Vault du läsa befintliga åtkomst principer i Key Vault och fylla ARM-mallen med dessa principer för att undvika eventuella åtkomst avbrott.

### <a name="recommended-troubleshooting-steps-for-following-error-types"></a>Rekommenderade fel söknings steg för följande feltyper
* HTTP 401: oautentiserad begäran – [fel söknings steg](https://docs.microsoft.com/azure/key-vault/general/rest-error-codes#http-401-unauthenticated-request)
* HTTP 403: otillräcklig behörighet – [fel söknings steg](https://docs.microsoft.com/azure/key-vault/general/rest-error-codes#http-403-insufficient-permissions)
* HTTP 429: för många begär Anden – [fel söknings steg](https://docs.microsoft.com/azure/key-vault/general/rest-error-codes#http-429-too-many-requests)
* Kontrollera om du har tagit bort åtkomstbehörigheten för nyckelvalvet: [Åtkomstprinciper för nyckelvalvet](https://docs.microsoft.com/azure/key-vault/general/group-permissions-for-apps)
* Om du har problem med att autentisera till nyckelvalv i kod använder du [SDK för autentisering](https://azure.github.io/azure-sdk/posts/2020-02-25/defaultazurecredentials.html)

### <a name="what-are-the-best-practices-i-should-implement-when-key-vault-is-getting-throttled"></a>Vilka är de bästa metoderna jag bör implementera när Key Vault får en begränsning?
Följ de bästa metoderna, dokumenterade [här](https://docs.microsoft.com/azure/key-vault/general/overview-throttling#how-to-throttle-your-app-in-response-to-service-limits)

## <a name="next-steps"></a>Efterföljande moment

Lär dig hur du felsöker Key Vault-autentiseringsfel. [Key Vault fel söknings guide](https://docs.microsoft.com/azure/key-vault/general/rest-error-codes)
