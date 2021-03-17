---
title: Länka ett partner-ID till dina Power Apps-konton med dina Azure-autentiseringsuppgifter
description: Den här artikeln hjälper Microsoft-partner att använda sina Azure-autentiseringsuppgifter för att hjälpa kunder att använda Microsoft Power Apps.
author: bandersmsft
ms.reviewer: akangaw
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 03/16/2021
ms.author: banders
ms.openlocfilehash: adaff7a6b8559fe9604412a44eced6c490231e3c
ms.sourcegitcommit: 27cd3e515fee7821807c03e64ce8ac2dd2dd82d2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2021
ms.locfileid: "103603841"
---
# <a name="link-a-partner-id-to-your-power-apps-accounts"></a>Länka ett partner-ID till dina Power Apps-konton

Den här artikeln hjälper Microsoft-partner, som är Power Apps-leverantörer, att associera sina tjänster till kunder i Microsoft Power Apps. När du (Microsoft-partnern) hanterar, konfigurerar och stöder Power Apps-tjänster för din kund har du åtkomst till kundens miljö. Du kan använda dina Azure-autentiseringsuppgifter och en partner administratörs länk (PAL) för att koppla ditt partner nätverks-ID till de kontoautentiseringsuppgifter som används för tjänst leverans.

Med PAL kan Microsoft identifiera och identifiera partner som har Power Apps-kunder. Användning av Microsoft-attribut till en partner organisation baserat på kontots behörigheter (Power Apps-rollen) och omfattning (klient organisation, resurs grupp, resurs).

## <a name="get-access-from-your-customer"></a>Få åtkomst från kunden

Innan du länkar ditt partner-ID måste kunden ge dig åtkomst till sina resurser för Power Apps genom att använda något av följande alternativ:

- **Gäst användare** – din kund kan lägga till dig som gäst användare och tilldela alla Power Apps-roller. Mer information finns i [Lägga till gästanvändare från en annan katalog](../../active-directory/external-identities/what-is-b2b.md).
- **Katalog konto** – din kund kan skapa ett användar konto åt dig i en egen katalog och tilldela alla Power Apps-roller.
- **Tjänstens huvud namn** – din kund kan lägga till en app eller ett skript från din organisation i katalogen och tilldela alla Power Apps-roller. Appens eller skriptets identitet kallas för tjänstens huvudnamn.
- **Delegerad administratör** – din kund kan delegera en resurs grupp så att användarna kan arbeta med den inifrån din klient organisation. Mer information finns i [för partner: den delegerade administratören](/power-platform/admin/for-partners-delegated-administrator).

## <a name="link-customer-to-a-partner-id"></a>Länka kund till ett partner-ID

När du har åtkomst till kundens resurser använder du Azure Portal, PowerShell eller Azure CLI för att länka ditt Microsoft Partner Network-ID (MPN-ID) till ditt användar-ID eller tjänstens huvud namn. Länka partner-ID: t till varje kund klient organisation.

### <a name="use-the-azure-portal-to-link-to-a-new-partner-id"></a>Använd Azure-portalen för att länka till ett nytt partner-ID

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Gå till [Länka till ett partner-ID](https://portal.azure.com/#blade/Microsoft_Azure_Billing/managementpartnerblade) på Azure-portalen.
1. Ange [Microsoft Partner Network](https://partner.microsoft.com/) -ID för din organisation. Se till att använda det  **associerade MPN-ID: t**  som visas i din partner profil.  
    :::image type="content" source="./media/link-partner-id-power-apps-accounts/link-partner-id.png" alt-text="Skärm bild som visar länken till ett partner-ID-fönster." lightbox="./media/link-partner-id-power-apps-accounts/link-partner-id.png" :::
1. Om du vill länka ditt partner-ID till en annan kund byter du katalog. Under **växel katalog** väljer du lämplig katalog.  
    :::image type="content" source="./media/link-partner-id-power-apps-accounts/switch-directory.png" alt-text="Skärm bild som visar fönstret katalog + prenumeration där du kan byta katalog." lightbox="./media/link-partner-id-power-apps-accounts/switch-directory.png" :::

### <a name="use-powershell-to-link-to-a-new-partner-id"></a>Använd PowerShell för att länka till ett nytt partner-ID

Installera Azure PowerShell-modulen [AZ. ManagementPartner](https://www.powershellgallery.com/packages/Az.ManagementPartner/) .

Logga in på kundens klient organisation med antingen användar kontot eller tjänstens huvud namn. Mer information finns i [Logga in med PowerShell](/powershell/azure/authenticate-azureps).

```azurepowershell-interactive
Update-AzManagementPartner -PartnerId 12345
```

Länka till det nya partner-ID:t. Partner-ID:t är ID:t för [Microsoft Partner Network](https://partner.microsoft.com/) för din organisation. Se till att använda det **associerade MPN-ID: t**  som visas i din partner profil.

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

Logga in på kundens klient organisation med antingen användar kontot eller tjänstens huvud namn. Mer information finns i [Logga in med Azure CLI](/cli/azure/authenticate-azure-cli).

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

## <a name="frequently-asked-questions-faq"></a>Vanliga frågor och svar (FAQ)

I följande avsnitt beskrivs vanliga frågor om att länka ett partner-ID till Power Apps-konton.

### <a name="who-should-link-the-partner-id"></a>Vem ska länka till partner-ID: t?

Alla användare från partner organisationen som arbetar på en kunds Power Apps-resurser kan länka partner-ID: t till kontot. Vi rekommenderar att associationen i PAL ska göras i början av projektet. Det kan dock utföras när du har åtkomst till kundens katalog.

### <a name="can-a-partner-id-be-changed-after-its-linked"></a>Kan ett partner-ID ändras efter att det har länkats?

Ja. Ett länkat partner-ID kan ändras, läggas till eller tas bort. Ett exempel på den här situationen kan vara när en anställd från företaget lämnar din organisation. Ett annat exempel kan vara när ett projekt eller kontrakt med kunden slutar.

### <a name="what-if-a-user-has-an-account-in-more-than-one-customer-tenant"></a>Vad händer om en användare har ett konto i flera klientorganisationer för en kund?

Länken mellan partner-ID:t och kontot skapas för alla kundens klientorganisationer. Länka partner-ID:t i alla kundens klientorganisationer.

### <a name="can-other-partners-or-customers-edit-or-remove-the-link-to-the-partner-id"></a>Kan andra partner eller kunder redigera eller ta bort länken till partner-ID:t?

Länken är kopplad på användarkontonivå. Du är den enda som kan redigera eller ta bort länken till partner-ID:t. Kunden och andra partner kan inte ändra länken till partner-ID:t.

### <a name="which-mpn-id-should-i-use-if-my-company-has-multiple"></a>Vilket MPN-ID ska jag använda om mitt företag har flera?

Se till att använda **associerat MPN-ID** som visas i din partnerprofil. Det är vanligt vis den lokala konto-ID-associationen med din organisation.

### <a name="how-do-i-explain-pal-to-my-customer"></a>Hur gör jag för att förklara PAL till min kund?

PAL gör det möjligt för Microsoft att identifiera och identifiera de partner som hjälper kunder att uppnå affärs mål och inse värde i molnet. Kunderna måste först ge en partner åtkomst till sin Power Apps-resurs. När åtkomst beviljas är partnerns Microsoft Partner Network-ID (MPN-ID) associerat. Den här kopplingen hjälper Microsoft att förstå tjänst leverantörer och förfina de verktyg och program som behövs för att ge bästa möjliga support till kunder.

### <a name="what-data-does-pal-collect"></a>Vilka data samlas in av PAL?

PAL-associationen för befintliga autentiseringsuppgifter tillhandahåller inga nya kunddata till Microsoft. Den innehåller information till Microsoft där en partner aktivt ingår i en kunds Power Apps-miljöer. Microsoft kan använda användningen av och inflytande från kund miljö till partner organisation baserat på kontots behörigheter (Power Apps-rollen) och omfattningen (klient organisation, resurs grupp, resurs) som tillhandahålls till partnern av kunden.

### <a name="does-pal-association-affect-the-security-of-a-customers-power-apps-environment"></a>Påverkar PAL-associationen säkerheten i en kunds Power Apps-miljö?

PAL-associationen lägger bara till partnerns MPN-ID till de autentiseringsuppgifter som redan har tillhandahållits. Den ändrar inte några behörigheter (Power Apps-rollen) eller ger extra Power Apps-tjänstens data till partnern eller Microsoft.

### <a name="next-steps"></a>Nästa steg

- Delta i diskussionen i [Microsofts community för partner](https://aka.ms/PALdiscussion) och få uppdateringar eller skicka feedback.
- Läs [vanliga frågor och svar om att läsa vanliga frågor och svar om program utveckling med låg kod](https://assetsprod.microsoft.com/mpn/faq-low-code-app-development-advanced-specialization.pdf) för PAL-baserade Power Apps-associationer för avancerad specialisering av program utveckling
