---
title: Säker åtkomst till en hanterad HSM-Azure Key Vault hanterad HSM
description: Lär dig hur du skyddar åtkomsten till hanterad HSM med Azure RBAC och hanterad HSM lokalt RBAC
services: key-vault
author: amitbapat
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: tutorial
ms.date: 09/15/2020
ms.author: ambapat
ms.openlocfilehash: 5a3fcc10f318f2a8065550a48eb2bfb4bbdd4915
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2021
ms.locfileid: "102218408"
---
# <a name="secure-access-to-your-managed-hsms"></a>Säker åtkomst till din hanterade HSM: er

Azure Key Vault hanterad HSM är en moln tjänst som skyddar krypterings nycklar. Eftersom dessa data är känsliga och affärs kritiska måste du skydda åtkomsten till dina hanterade HSM: er genom att bara tillåta behöriga program och användare att komma åt dem. Den här artikeln ger en översikt över den hanterade HSM-modellen för åtkomst kontroll. Den förklarar autentisering och auktorisering och beskriver hur du skyddar åtkomsten till dina hanterade HSM: er.

Den här självstudien vägleder dig genom ett enkelt exempel som visar hur du kan uppnå separering av uppgifter och åtkomst kontroll med Azure RBAC och hanterad HSM lokal RBAC. Se [hanterad HSM-åtkomstkontroll](access-control.md) om du vill lära dig mer om HANTERAd HSM-åtkomst kontroll modell.

## <a name="prerequisites"></a>Förutsättningar

För att slutföra stegen i den här artikeln, måste du ha följande objekt:

* En prenumeration på Microsoft Azure. Om du inte har ett konto kan du registrera dig för en [kostnadsfri utvärderingsversion](https://azure.microsoft.com/pricing/free-trial).
* Azure CLI-versionen 2.12.0 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI]( /cli/azure/install-azure-cli).
* En hanterad HSM i din prenumeration. Se [snabb start: etablera och aktivera en hanterad HSM med Azure CLI](quick-create-cli.md) för att etablera och aktivera en hanterad HSM.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="sign-in-to-azure"></a>Logga in på Azure

Du kan logga in i Azure via CLI genom att skriva:

```azurecli
az login
```

Mer information om inloggnings alternativ via CLI finns i [Logga in med Azure CLI](/cli/azure/authenticate-azure-cli)

## <a name="example"></a>Exempel

I det här exemplet ska vi utveckla ett program som använder en RSA 2 048-bitars nyckel för signerings åtgärder. Vårt program körs på en virtuell Azure-dator (VM) med en [hanterad identitet](../../active-directory/managed-identities-azure-resources/overview.md). Både den RSA-nyckel som används för signering lagras i vår hanterade HSM.

Vi har identifierat följande roller som hanterar, distribuerar och granskar vårt program:

- **Säkerhets team**: IT-personal från byråns skydds chef (säkerhets tjänsteman) eller liknande bidrags givare. Säkerhets teamet ansvarar för korrekt förvaring av nycklar. Nycklarna RSA eller EG-nycklar för signering, RSA-eller AES-nycklar för data kryptering.
- **Utvecklare och operatörer**: personal som utvecklar programmet och distribuerar det i Azure. Medlemmarna i det här teamet är inte en del av säkerhets personalen. De bör inte ha till gång till känsliga data som RSA-nycklar. Endast det program som de distribuerar bör ha åtkomst till dessa känsliga data.
- **Granskare**: den här rollen är för deltagare som inte är medlemmar i utvecklings-eller allmän IT-personal. De granskar användningen och underhållet av certifikat, nycklar och hemligheter för att säkerställa efterlevnaden av säkerhets standarder.

Det finns en annan roll som ligger utanför omfånget för programmet: prenumerationen (eller resurs gruppens) administratör. Prenumerations administratören konfigurerar inledande åtkomst behörighet för säkerhets teamet. De ger åtkomst till säkerhets teamet genom att använda en resurs grupp som har de resurser som krävs av programmet.

Vi måste auktorisera följande åtgärder för våra roller:

**Säkerhetsteamet**
- Skapa hanterad HSM.
- Ladda ned den hanterade HSM-säkerhetsdomänen (för haveri beredskap)
- Aktivera loggning.
- Generera eller importera nycklar
- Skapa hanterade HSM-säkerhetskopieringar för haveri beredskap.
- Ange hanterad HSM lokal RBAC för att bevilja behörigheter till användare och program för vissa åtgärder.
- Rulla nycklarna med jämna mellanrum.

**Utvecklare och operatörer**
- Hämta referens (nyckel-URI) från säkerhets teamet den RSA-nyckel som används för signering.
- Utveckla och distribuera programmet som har åtkomst till nyckeln program mässigt.

**Granskare**
- Granska förfallo datum för nycklar för att säkerställa att nycklarna är aktuella
- Övervaka roll tilldelningar för att säkerställa att nycklar endast kan nås av behöriga användare/program
- Granska hanterade HSM-loggar för att bekräfta korrekt användning av nycklar i överensstämmelse med data säkerhets standarder.

I följande tabell sammanfattas roll tilldelningarna för team och resurser för åtkomst till hanterad HSM.

| Roll | Hanterings Plans roll | Data Plans roll |
| --- | --- | --- |
| Säkerhetsteamet | Hanterad HSM-deltagare | Hanterad HSM-administratör |
| Utvecklare och operatörer | Inga | Inga |
| Granskare | Inget | Hanterad HSM-krypto |
| Hanterad identifiering av den virtuella datorn som används av programmet| Inget | Hanterad HSM-kryptografi användare |
| Hanterad identitet för det lagrings konto som används av programmet| Inget| Kryptering av hanterad HSM-tjänst |

De tre team rollerna behöver åtkomst till andra resurser tillsammans med hanterad HSM-behörighet. Utvecklare och operatörer behöver ha `Contributor` åtkomst till dessa resurs typer för att distribuera virtuella datorer (eller Web Apps-funktionen i Azure App Service). Granskare behöver Läs behörighet till lagrings kontot där de hanterade HSM-loggarna lagras.

Om du vill tilldela roller för hanterings plan (Azure RBAC) kan du använda Azure Portal eller något av de andra hanterings gränssnitten, till exempel Azure CLI eller Azure PowerShell. Om du vill tilldela hanterade HSM-dataplan-roller måste du använda Azure CLI.

Azure CLI-kodfragmenten i det här avsnittet är skapade med följande antaganden:

- Azure Active Directory-administratören har skapat säkerhets grupper som representerar de tre rollerna: contoso Security Team, contoso app DevOps och contoso app Auditers. Administratören har lagt till användare i deras respektive grupper.
- Alla resurser finns i resurs gruppen **ContosoAppRG** .
- Hanterade HSM-loggar lagras i **contosologstorage** lagrings konto.
- **ContosoMHSM** -hanterad HSM och **contosologstorage** lagrings konto finns på samma Azure-plats.

Prenumerations administratören tilldelar `Managed HSM Contributor` rollen till säkerhets teamet. Med den här rollen kan säkerhets teamet hantera befintliga hanterade HSM: er och skapa nya. Om det finns befintliga hanterade HSM: er måste de tilldelas rollen "hanterad HSM-administratör" för att kunna hantera dem.

```azurecli-interactive
# This role assignment allows Contoso Security Team to create new Managed HSMs
az role assignment create --assignee-object-id $(az ad group show -g 'Contoso Security Team' --query 'objectId' -o tsv) --assignee-principal-type Group --role "Managed HSM Contributor"

# This role assignment allows Contoso Security Team to become administrator of existing managed HSM
az keyvault role assignment create  --hsm-name ContosoMHSM --assignee $(az ad group show -g 'Contoso Security Team' --query 'objectId' -o tsv) --scope / --role "Managed HSM Administrator"
```

Säkerhets teamet ställer in loggning och tilldelar roller till granskare och VM-programmet.

```azurecli-interactive
# Enable logging
hsmresource=$(az keyvault show --hsm-name ContosoMHSM --query id -o tsv)
storageresource=$(az storage account show --name contosologstorage --query id -o tsv)
az monitor diagnostic-settings create --name MHSM-Diagnostics --resource $hsmresource --logs    '[{"category": "AuditEvent","enabled": true}]' --storage-account $storageresource

# Assign the "Crypto Auditor" role to Contoso App Auditors group. It only allows them to read.
az keyvault role assignment create  --hsm-name ContosoMHSM --assignee $(az ad group show -g 'Contoso App Auditors' --query 'objectId' -o tsv) --scope / --role "Managed HSM Crypto Auditor"

# Grant the "Crypto User" role to the VM's managed identity. It allows to create and use keys. 
# However it cannot permanently delete (purge) keys
az keyvault role assignment create  --hsm-name ContosoMHSM --assignee $(az vm identity show --name "vmname" --resource-group "ContosoAppRG" --query objectId -o tsv) --scope / --role "Managed HSM Crypto Auditor"

# Assign "Managed HSM Crypto Service Encryption" role to the Storage account ID
storage_account_principal=$(az storage account show --id $storageresource --query identity.principalId -o tsv)
# (if no identity exists), then assign a new one
[ "$storage_account_principal" ] || storage_account_principal=$(az storage account update --assign-identity --id $storageresource)

az keyvault role assignment create --hsm-name ContosoMHSM --role "Managed HSM Crypto Service Encryption" --assignee $storage_account_principal
```

Den här självstudien visar bara åtgärder som är relevanta för åtkomst kontrollen i den flesta delar. Andra åtgärder och åtgärder som rör distribution av program på den virtuella datorn. om du aktiverar kryptering med kundhanterad nyckel för ett lagrings konto visas inte skapa hanterad HSM här för att se till att exemplet fokuserar på åtkomst kontroll och roll hantering.

Vårt exempel beskriver ett enkelt scenario. Scenarier med real tid kan vara mer komplexa. Du kan justera behörigheterna till ditt nyckel valv baserat på dina behov. Vi förmodade att säkerhets teamet tillhandahåller nyckel-och hemlighet-referenser (URI: er och tumavtrycken), som används av DevOps-Personalen i sina program. Utvecklare och operatörer kräver ingen åtkomst till data planet. Vi fokuserar på hur du skyddar ditt nyckel valv. Ge liknande överväganden när du skyddar [dina virtuella datorer](https://azure.microsoft.com/services/virtual-machines/security/), [lagrings konton](../../storage/blobs/security-recommendations.md)och andra Azure-resurser.

## <a name="resources"></a>Resurser

- [Dokumentation om Azure RBAC](../../role-based-access-control/overview.md)
- [Azure RBAC: inbyggda roller](../../role-based-access-control/built-in-roles.md)
- [Hantera Azure RBAC med Azure CLI](../../role-based-access-control/role-assignments-cli.md)

## <a name="next-steps"></a>Nästa steg

En vägledning för att komma igång med en administratör finns i [Vad är hanterad HSM?](overview.md).

Mer information om användnings loggning för hanterad HSM-loggning finns i [hanterad HSM-loggning](logging.md).
