---
title: Länka ett partner-ID till dina Power Apps med dina autentiseringsuppgifter för Azure
description: Den här artikeln hjälper Microsoft-partner att använda sina Azure-autentiseringsuppgifter för att hjälpa kunder att använda Microsoft Power Apps.
author: bandersmsft
ms.reviewer: akangaw
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 03/16/2021
ms.author: banders
ms.openlocfilehash: acb22cc4b2a461e476131a83972db3e782425a39
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2021
ms.locfileid: "107727716"
---
# <a name="link-a-partner-id-to-your-power-apps-accounts"></a>Länka ett partner-ID till dina Power Apps konton

Den här artikeln hjälper Microsoft-partner, Power Apps tjänstleverantörer, att associera sina tjänster till kunder på Microsoft Power Apps. När du (Microsoft-partnern) hanterar, konfigurerar och Power Apps tjänster för din kund har du åtkomst till kundens miljö. Du kan använda dina Azure-autentiseringsuppgifter och en partneradministratörslänk (PAL) för att associera ditt partnernätverks-ID med de kontoautentiseringsuppgifter som används för tjänstleverans.

PAL gör det möjligt för Microsoft att identifiera och identifiera partner som har Power Apps kunder. Microsoft attributanvändning till en partners organisation baserat på kontots behörigheter (Power Apps roll) och omfång (klientorganisation, resursgrupp, resurs).

## <a name="get-access-from-your-customer"></a>Få åtkomst från kunden

Innan du länkar ditt partner-ID måste kunden ge dig åtkomst till sina Power Apps med hjälp av något av följande alternativ:

- **Gästanvändare** – Din kund kan lägga till dig som gästanvändare och tilldela alla Power Apps roller. Mer information finns i [Lägga till gästanvändare från en annan katalog](../../active-directory/external-identities/what-is-b2b.md).
- **Katalogkonto** – Kunden kan skapa ett användarkonto åt dig i en egen katalog och tilldela valfri Power Apps roll.
- **Tjänstens** huvudnamn – Kunden kan lägga till en app eller ett skript från din organisation i katalogen och tilldela valfri Power Apps roll. Appens eller skriptets identitet kallas för tjänstens huvudnamn.
- **Delegerad** administratör – Kunden kan delegera en resursgrupp så att användarna kan arbeta med den inifrån din klientorganisation. Mer information finns i [För partner: Delegerad administratör.](/power-platform/admin/for-partners-delegated-administrator)

## <a name="link-customer-to-a-partner-id"></a>Länka kunden till ett partner-ID

När du har åtkomst till kundens resurser använder du Azure Portal, PowerShell eller Azure CLI för att länka ditt Microsoft Partner Network-ID (MPN-ID) till ditt användar-ID eller tjänstens huvudnamn. Länka partner-ID:t till varje kundklientorganisation.

### <a name="use-the-azure-portal-to-link-to-a-new-partner-id"></a>Använd Azure-portalen för att länka till ett nytt partner-ID

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Gå till [Länka till ett partner-ID](https://portal.azure.com/#blade/Microsoft_Azure_Billing/managementpartnerblade) på Azure-portalen.
1. Ange [Microsoft Partner Network](https://partner.microsoft.com/) ID för din organisation. Se till att använda det  **associerade MPN-ID som**  visas i din partnerprofil.  
    :::image type="content" source="./media/link-partner-id-power-apps-accounts/link-partner-id.png" alt-text="Skärmbild som visar fönstret Länka till ett partner-ID." lightbox="./media/link-partner-id-power-apps-accounts/link-partner-id.png" :::
1. Om du vill länka ditt partner-ID till en annan kund byter du katalog. Under **Växla katalog** väljer du lämplig katalog.  
    :::image type="content" source="./media/link-partner-id-power-apps-accounts/switch-directory.png" alt-text="Skärmbild som visar fönstret Katalog + prenumeration där du kan byta katalog." lightbox="./media/link-partner-id-power-apps-accounts/switch-directory.png" :::

### <a name="use-powershell-to-link-to-a-new-partner-id"></a>Använd PowerShell för att länka till ett nytt partner-ID

Installera [modulen Az.ManagementPartner](https://www.powershellgallery.com/packages/Az.ManagementPartner/) Azure PowerShell.

Logga in på kundens klientorganisation med antingen användarkontot eller tjänstens huvudnamn. Mer information finns i [Logga in med PowerShell](/powershell/azure/authenticate-azureps).

```azurepowershell-interactive
Update-AzManagementPartner -PartnerId 12345
```

Länka till det nya partner-ID:t. Partner-ID:t är ID:t för [Microsoft Partner Network](https://partner.microsoft.com/) för din organisation. Se till att använda det **associerade MPN-ID som**  visas i din partnerprofil.

```azurepowershell-interactive
new-AzManagementPartner -PartnerId 12345
```

Hämta det länkade partner-ID:t

```azurepowershell-interactive
get-AzManagementPartner
```

Uppdatera det länkade partner-ID:t

```azurepowershell-interactive
Update-AzManagementPartner -PartnerId 12345
```

Ta bort det länkade partner-ID:t

```azurepowershell-interactive
remove-AzManagementPartner -PartnerId 12345
```

### <a name="use-the-azure-cli-to-link-to-a-new-partner-id"></a>Använd Azure CLI för att länka till ett nytt partner-ID

Installera först Azure CLI-tillägget.

```azurecli-interactive
az extension add --name managementpartner
```

Logga in på kundens klientorganisation med antingen användarkontot eller tjänstens huvudnamn. Mer information finns i [Logga in med Azure CLI](/cli/azure/authenticate-azure-cli).

```azurecli-interactive
az login --tenant TenantName
```

Länka till det nya partner-ID:t. Partner-ID:t är ID:t för [Microsoft Partner Network](https://partner.microsoft.com/) för din organisation.

```azurecli-interactive
az managementpartner create --partner-id 12345
```

Hämta det länkade partner-ID:t

```azurecli-interactive
az managementpartner show
```

Uppdatera det länkade partner-ID:t

```azurecli-interactive
az managementpartner update --partner-id 12345
```

Ta bort det länkade partner-ID:t

```azurecli-interactive
az managementpartner delete --partner-id 12345
```

### <a name="next-steps"></a>Nästa steg

- Läs vanliga [Cost Management + Billing och svar](../cost-management-billing-faq.yml) om hur du länkar ett partner-ID till Power Apps-konton.
- Delta i diskussionen i [Microsofts community för partner](https://aka.ms/PALdiscussion) och få uppdateringar eller skicka feedback.
- Läs vanliga frågor och svar om avancerad [specialisering](https://assetsprod.microsoft.com/mpn/faq-low-code-app-development-advanced-specialization.pdf) för programutveckling med lite kod för PAL-baserad Power Apps för avancerad specialisering för programutveckling med låg kod.
