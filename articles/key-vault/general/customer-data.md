---
title: Azure Key Vault funktioner för kunddata – Azure Key Vault | Microsoft Docs
description: Lär dig mer om kunddata som Azure Key Vault tar emot när valv, nycklar, hemligheter, certifikat och hanterade lagringskonton skapas eller uppdateras.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.topic: reference
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: 4d45c019a6ba073d7553c09784736959faf89d27
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107749788"
---
# <a name="azure-key-vault-customer-data-features"></a>Azure Key Vault funktioner för kunddata

Azure Key Vault tar emot kunddata vid skapande eller uppdatering av valv, hanterade HSM-pooler, nycklar, hemligheter, certifikat och hanterade lagringskonton. Dessa kunddata är direkt synliga i Azure Portal och via REST API. Kunddata kan redigeras eller tas bort genom att uppdatera eller ta bort objektet som innehåller data.

Systemåtkomstloggar genereras när en användare eller ett program kommer åt Key Vault. Detaljerade åtkomstloggar är tillgängliga för kunder som använder Azure Insights.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="identifying-customer-data"></a>Identifiera kunddata

Följande information identifierar kunddata i Azure Key Vault:

- Åtkomstprinciper för Azure Key Vault innehåller objekt-ID:er som representerar användare, grupper eller program
- Certifikatämnen kan innehålla e-postadresser eller andra användar- eller organisationsidentifierare
- Certifikatkontakter kan innehålla användarens e-postadresser, namn eller telefonnummer
- Certifikatutfärdare kan innehålla e-postadresser, namn, telefonnummer, kontoautentiseringsuppgifter och organisationsinformation
- Godtyckliga taggar kan tillämpas på objekt i Azure Key Vault. Dessa objekt omfattar valv, nycklar, hemligheter, certifikat och lagringskonton. Taggarna som används kan innehålla personuppgifter
- Azure Key Vault innehåller objekt-ID: er, [UPN:er](../../active-directory/hybrid/plan-connect-userprincipalname.md)och IP-adresser för varje REST API anrop
- Azure Key Vault diagnostikloggar kan innehålla objekt-IP-adresser och IP-adresser för REST API anrop

## <a name="deleting-customer-data"></a>Ta bort kunddata

Samma REST API:er, portalupplevelsen och DEDK:er som används för att skapa valv, nycklar, hemligheter, certifikat och hanterade lagringskonton kan också uppdatera och ta bort dessa objekt.

Med mjuk borttagning kan du återställa borttagna data i 90 dagar efter borttagningen. När du använder mjuk borttagning kan data tas bort permanent innan kvarhållningsperioden på 90 dagar upphör att gälla genom att utföra en rensningsåtgärd. Om valvet eller prenumerationen har konfigurerats för att blockera rensningsåtgärder går det inte att permanent ta bort data förrän den schemalagda kvarhållningsperioden har passerat.

## <a name="exporting-customer-data"></a>Exportera kunddata

Med samma REST-API:er, portalupplevelse och SDK:er som används för att skapa valv, nycklar, hemligheter, certifikat och hanterade lagringskonton kan du också visa och exportera dessa objekt.

Azure Key Vault är en valfri funktion som kan aktiveras för att generera loggar för varje REST API anrop. Loggarna överförs till ett lagringskonto i din prenumeration där du tillämpar kvarhållningsprincipen som uppfyller organisationens krav.

Azure Key Vault diagnostikloggar som innehåller personuppgifter kan hämtas genom att göra en exportbegäran i portalen för användarsekretess. Den här begäran måste göras av klientadministratören.

## <a name="next-steps"></a>Nästa steg

- [Azure Key Vault loggning](logging.md)

- [Översikt av mjuk borttagning för Azure Key Vault](./key-vault-recovery.md)

- [Azure Key Vault nyckelåtgärder](/rest/api/keyvault/key-operations)

- [Azure Key Vault hemliga åtgärder](/rest/api/keyvault/secret-operations)

- [Azure Key Vault certifikat och principer](/rest/api/keyvault/certificates-and-policies)

- [Azure Key Vault för lagringskonto](/rest/api/keyvault/storage-account-key-operations)