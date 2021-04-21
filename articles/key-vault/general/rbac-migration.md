---
title: Migrera till rollbaserad åtkomstkontroll i Azure | Microsoft Docs
description: Lär dig hur du migrerar från principer för valvåtkomst till Azure-roller.
services: key-vault
author: msmbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 8/30/2020
ms.author: mbaldwin
ms.openlocfilehash: a369ed26ca91dbf951b28b99250c6307608c5eb3
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107749086"
---
# <a name="migrate-from-vault-access-policy-to-an-azure-role-based-access-control-permission-model"></a>Migrera från principen för valvåtkomst till en azure-modell för rollbaserad åtkomstkontroll

Valvåtkomstprincipmodellen är ett befintligt auktoriseringssystem som är inbyggt Key Vault ger åtkomst till nycklar, hemligheter och certifikat. Du kan styra åtkomsten genom att tilldela enskilda behörigheter till säkerhetsobjekt (användare, grupp, tjänstens huvudnamn, hanterad identitet) Key Vault omfånget. 

Rollbaserad åtkomstkontroll i Azure (Azure RBAC) är ett auktoriseringssystem som bygger [på Azure Resource Manager](../../azure-resource-manager/management/overview.md) som ger mer information om åtkomsthantering av Azure-resurser. Med Azure RBAC kan du styra åtkomsten till resurser genom att skapa rolltilldelningar som består av tre element: säkerhetsobjekt, rolldefinition (fördefinierad uppsättning behörigheter) och omfång (grupp med resurser eller enskild resurs). Mer information finns i [Rollbaserad åtkomstkontroll i Azure (Azure RBAC).](../../role-based-access-control/overview.md)

Innan du migrerar till Azure RBAC är det viktigt att förstå dess fördelar och begränsningar.

Viktiga fördelar med Azure RBAC framför åtkomstprinciper för valv:
- Tillhandahåller en enhetlig åtkomstkontrollmodell för Azure-resurser – samma API för alla Azure-tjänster
- Centraliserad åtkomsthantering för administratörer – hantera alla Azure-resurser i en enda vy
- Integrerad med [Privileged Identity Management](../../active-directory/privileged-identity-management/pim-configure.md) för tidsbaserad åtkomstkontroll
- Neka tilldelningar – möjlighet att undanta säkerhetsobjekt i ett visst omfång. Mer information finns i [Förstå tilldelningsnekanden i Azure](../../role-based-access-control/deny-assignments.md)

Azure RBAC-nackdelar:
- Svarstid för rolltilldelningar – det kan ta flera minuter för rolltilldelningen att tillämpas. Åtkomstprinciper för valv tilldelas direkt.
- Begränsat antal rolltilldelningar – 2 000 rolltilldelningar per prenumeration jämfört med 1 024 åtkomstprinciper per Key Vault

## <a name="access-policies-to-azure-roles-mapping"></a>Åtkomstprinciper till mappning av Azure-roller

Azure RBAC har flera inbyggda Azure-roller som du kan tilldela till användare, grupper, tjänstens huvudnamn och hanterade identiteter. Om de inbyggda rollerna inte uppfyller organisationens specifika behov kan du skapa egna anpassade [Azure-roller.](../../role-based-access-control/custom-roles.md)

Key Vault inbyggda roller för åtkomsthantering av nycklar, certifikat och hemligheter:
- Key Vault administratör
- Key Vault Läsare
- Key Vault certifikatutfärdare
- Key Vault Crypto Officer
- Key Vault Crypto-användare
- Key Vault kryptografitjänstkrypteringsanvändare
- Key Vault Secrets Officer
- Key Vault hemlighetsanvändare

Mer information om befintliga inbyggda roller finns i [Inbyggda roller i Azure](../../role-based-access-control/built-in-roles.md)

Principer för valvåtkomst kan tilldelas med individuellt valda behörigheter eller med fördefinierade behörighetsmallar.

Fördefinierade behörighetsmallar för åtkomstprinciper:
- Nyckel, hemlighet, certifikathantering
- Key & Secret Management
- Secret & Certificate Management
- Nyckelhantering
- Hemlighetshantering
- Certifikathantering
- SQL Server-anslutning
- Azure Data Lake Storage eller Azure Storage
- Azure Backup
- Exchange Online-kundnyckel
- SharePoint Online-kundnyckel
- Azure Information BYOK

### <a name="access-policies-templates-to-azure-roles-mapping"></a>Få åtkomst till principer för mappning av Azure-roller
| Åtkomstprincipmall | Operations | Azure-roll |
| --- | --- | --- |
| Hantering av nycklar, hemligheter och certifikat | Nycklar: alla åtgärder <br>Certifikat: alla åtgärder<br>Hemligheter: alla åtgärder | Key Vault administratör |
| Key & Secret Management | Nycklar: alla åtgärder <br>Hemligheter: alla åtgärder| Key Vault Crypto Officer <br> Key Vault Secrets Officer |
| Hantering av &-certifikat | Certifikat: alla åtgärder <br>Hemligheter: alla åtgärder| Key Vault certifikatansvarig <br> Key Vault Secrets Officer|
| Nyckelhantering | Nycklar: alla åtgärder| Key Vault Crypto Officer|
| Hemlighetshantering | Hemligheter: alla åtgärder| Key Vault Secrets Officer|
| Certifikathantering | Certifikat: alla åtgärder | Key Vault certifikatansvarig|
| SQL Server-anslutning | Nycklar: hämta, lista, omsluta nyckel, packa upp nyckel | Key Vault kryptografitjänstkrypteringsanvändare|
| Azure Data Lake Storage eller Azure Storage | Nycklar: hämta, lista, packa upp nyckel | Ej tillämpligt<br> Anpassad roll krävs|
| Azure Backup | Nycklar: hämta, lista, säkerhetskopiera<br> Certifikat: hämta, lista, säkerhetskopiera | Ej tillämpligt<br> Anpassad roll krävs|
| Exchange Online-kundnyckel | Nycklar: hämta, lista, omsluta nyckel, packa upp nyckel | Key Vault kryptografitjänstkrypteringsanvändare|
| Exchange Online-kundnyckel | Nycklar: hämta, lista, omsluta nyckel, packa upp nyckel | Key Vault kryptografitjänstkrypteringsanvändare|
| Azure Information BYOK | Nycklar: get, dekryptera, signera | Ej tillämpligt<br>Anpassad roll krävs|


## <a name="assignment-scopes-mapping"></a>Mappning av tilldelningsomfång  

Azure RBAC för Key Vault tillåter rolltilldelning i följande omfång:
- Hanteringsgrupp
- Prenumeration
- Resursgrupp
- Key Vault resurs
- Enskild nyckel, hemlighet och certifikat

Behörighetsmodellen för valvåtkomstprincip är begränsad till att endast tilldela Key Vault på resursnivå, vilket 

I allmänhet är det bästa praxis att ha ett nyckelvalv per program och hantera åtkomst på nyckelvalvsnivå. Det finns scenarier när hantering av åtkomst i andra omfång kan förenkla åtkomsthanteringen.

- **Infrastruktur, säkerhetsadministratörer och operatörer: hantering av grupper av nyckelvalv på hanteringsgrupps-, prenumerations- eller resursgruppsnivå med valvåtkomstprinciper kräver att principer upprätthålls för varje nyckelvalv. Med Azure RBAC kan du skapa en rolltilldelning för hanteringsgrupp, prenumeration eller resursgrupp. Tilldelningen gäller för alla nya nyckelvalv som skapats under samma omfång. I det här scenariot rekommenderar vi att du använder Privileged Identity Management just-in-time-åtkomst över att ge permanent åtkomst.
 
- **Program: det finns scenarier när programmet skulle behöva dela en hemlighet med andra program. Med hjälp av valvåtkomst finns separata nyckelvalv som måste skapas för att undvika att ge åtkomst till alla hemligheter. Med Azure RBAC kan du tilldela en roll med omfång för en enskild hemlighet i stället med hjälp av ett enda nyckelvalv.

## <a name="vault-access-policy-to-azure-rbac-migration-steps"></a>Migreringssteg för valvåtkomstprincip till Azure RBAC
Det finns många skillnader mellan Azure RBAC och behörighetsmodellen för valvåtkomstprincip. För att undvika avbrott under migreringen rekommenderas stegen nedan.
 
1. **Identifiera och tilldela roller:** identifiera inbyggda roller baserat på mappningstabellen ovan och skapa anpassade roller när det behövs. Tilldela roller i omfång baserat på riktlinjer för mappning av omfång. Mer information om hur du tilldelar roller till nyckelvalv finns i [Ge åtkomst till Key Vault med en rollbaserad åtkomstkontroll i Azure](rbac-guide.md)
1. **Verifiera rolltilldelning:** det kan ta flera minuter att sprida rolltilldelningar i Azure RBAC. Guide om hur du kontrollerar rolltilldelningar finns i [Lista över rolltilldelningar i omfånget](../../role-based-access-control/role-assignments-list-portal.md#list-role-assignments-for-a-user-at-a-scope)
1. **Konfigurera övervakning och aviseringar för nyckelvalvet:** det är viktigt att aktivera loggning och konfigurera aviseringar för undantag om nekad åtkomst. Mer information finns i [Övervakning och aviseringar för Azure Key Vault](./alert.md)
1. **Ange en azure-modell för rollbaserad åtkomstkontrollbehörighet på Key Vault:** om du aktiverar Azure RBAC-behörighetsmodellen ogiltigförklaras alla befintliga åtkomstprinciper. Om ett fel uppstår kan behörighetsmodellen växlas tillbaka med alla befintliga åtkomstprinciper oförändrade.

> [!NOTE]
> För att ändra behörighetsmodellen krävs behörigheten Microsoft.Authorization/roleAssignments/write, som är en del av [rollerna](../../role-based-access-control/built-in-roles.md#owner) Ägare och [Administratör för användaråtkomst.](../../role-based-access-control/built-in-roles.md#user-access-administrator) Administratörsroller för klassiska prenumerationer som Tjänstadministratör och Medadministratör stöds inte.

> [!NOTE]
> När Azure RBAC-behörighetsmodellen är aktiverad misslyckas alla skript som försöker uppdatera åtkomstprinciper. Det är viktigt att uppdatera skripten så att de använder Azure RBAC.

## <a name="troubleshooting"></a>Felsökning
-  Rolltilldelningen fungerar inte efter flera minuter – det finns situationer när rolltilldelningar kan ta längre tid. Det är viktigt att skriva omprövningslogik i kod för att täcka dessa fall.
- Rolltilldelningar försvinner när Key Vault togs bort (mjuk borttagning) och återställdes – det är för närvarande en begränsning för funktionen för mjuk borttagning i alla Azure-tjänster. Du måste återskapa alla rolltilldelningar efter återställningen.    

## <a name="learn-more"></a>Läs mer

- [Översikt över Azure RBAC](../../role-based-access-control/overview.md)
- [Självstudie om anpassade roller](../../role-based-access-control/tutorial-custom-role-cli.md)
- [Privileged Identity Management](../../active-directory/privileged-identity-management/pim-configure.md)