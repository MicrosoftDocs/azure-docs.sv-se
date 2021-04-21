---
title: Azure Key Vault översikt över | Microsoft Docs
description: Key Vault Recovery-funktioner är utformade för att förhindra oavsiktlig eller skadlig borttagning av nyckelvalvet och hemligheter, nycklar och certifikat som lagras i nyckelvalvet.
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.author: mbaldwin
author: msmbaldwin
ms.date: 09/30/2020
ms.openlocfilehash: c3ffbba9546ada54a42c3f2c2aa5d98da599b353
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107749748"
---
# <a name="azure-key-vault-recovery-management-with-soft-delete-and-purge-protection"></a>Azure Key Vault med mjuk borttagning och rensningsskydd

Den här artikeln beskriver två återställningsfunktioner i Azure Key Vault, mjuk borttagning och rensningsskydd. Det här dokumentet innehåller en översikt över dessa funktioner och visar hur du hanterar dem via Azure Portal, Azure CLI och Azure PowerShell.

Mer information om Key Vault finns i
- [Översikt över Key Vault](overview.md)
- [Azure Key Vault översikt över nycklar, hemligheter och certifikat](about-keys-secrets-certificates.md)

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration [– skapa en kostnadsfritt](https://azure.microsoft.com/free/dotnet)
* [PowerShell-modulen](/powershell/azure/install-az-ps).
* [Azure CLI](/cli/azure/install-azure-cli)
* En Key Vault – du kan skapa en [med Azure Portal](../general/quick-create-portal.md) [Azure CLI](../general/quick-create-cli.md)eller [Azure PowerShell](../general/quick-create-powershell.md)
* Användaren behöver följande behörigheter (på prenumerationsnivå) för att utföra åtgärder på mjukt borttagna valv:

  | Behörighet | Description |
  |---|---|
  |Microsoft.KeyVault/locations/deletedVaults/read|Visa egenskaperna för ett mjukt borttagna nyckelvalv|
  |Microsoft.KeyVault/locations/deletedVaults/purge/action|Rensa ett mjukt borttagna nyckelvalv|


## <a name="what-are-soft-delete-and-purge-protection"></a>Vad är mjuk borttagning och rensningsskydd

[Skydd för mjuk](soft-delete-overview.md) borttagning och rensning är två olika funktioner för återställning av nyckelvalv.

> [!IMPORTANT]
> Det är viktigt att aktivera mjuk borttagning för att säkerställa att dina nyckelvalv och autentiseringsuppgifter skyddas mot oavsiktlig borttagning. Att aktivera mjuk borttagning anses dock vara en stor ändring eftersom det kan kräva att du ändrar programlogiken eller ger ytterligare behörigheter till tjänstens huvudnamn. Innan du slår på mjuk borttagning med hjälp av anvisningarna nedan kontrollerar du att ditt program är kompatibelt med ändringen med hjälp av det här [ **dokumentet här.**](soft-delete-change.md)

**Mjuk borttagning är** utformat för att förhindra oavsiktlig borttagning av nyckelvalvet och nycklar, hemligheter och certifikat som lagras i nyckelvalvet. Tänk på mjuk borttagning som en papperskorg. När du tar bort ett nyckelvalv eller ett nyckelvalvsobjekt kan det fortfarande återställas under en användarkonfigurerbar kvarhållningsperiod eller som standard 90 dagar. Nyckelvalv i läget för mjuk borttagning kan också **rensas, vilket** innebär att de tas bort permanent. På så sätt kan du återskapa nyckelvalv och nyckelvalvsobjekt med samma namn. Både återställning och borttagning av nyckelvalv och objekt kräver utökade behörigheter för åtkomstprinciper. **När mjuk borttagning har aktiverats kan den inte inaktiveras.**

Det är viktigt att observera att nyckelvalvsnamnen är globalt **unika,** så du kan inte skapa ett nyckelvalv med samma namn som ett nyckelvalv i läget för mjuk borttagning. På samma sätt är namnen på nycklar, hemligheter och certifikat unika i ett nyckelvalv. Du kommer inte att kunna skapa en hemlighet, nyckel eller certifikat med samma namn som ett annat i läget för mjuk borttagning.

**Rensningsskydd är** utformat för att förhindra borttagning av ditt nyckelvalv, nycklar, hemligheter och certifikat av en illvillig insider. Tänk på detta som en papperskorg med ett tidsbaserat lås. Du kan återställa objekt när som helst under den konfigurerbara kvarhållningsperioden. **Du kommer inte att kunna ta bort eller rensa ett nyckelvalv permanent förrän kvarhållningsperioden har gått ut.** När kvarhållningsperioden har förflutit rensas nyckelvalvet eller nyckelvalvsobjektet automatiskt.

> [!NOTE]
> Rensningsskydd är utformat så att ingen administratörsroll eller behörighet kan åsidosätta, inaktivera eller kringgå rensningsskydd. **När rensningsskydd har aktiverats kan det inte inaktiveras eller åsidosättas av någon, inklusive Microsoft.** Det innebär att du måste återställa ett borttagna nyckelvalv eller vänta tills kvarhållningsperioden har gått ut innan du återanvänder nyckelvalvsnamnet.

Mer information om mjuk borttagning finns i Azure Key Vault [översikt över mjuk borttagning](soft-delete-overview.md)

# <a name="azure-portal"></a>[Azure-portalen](#tab/azure-portal)

## <a name="verify-if-soft-delete-is-enabled-on-a-key-vault-and-enable-soft-delete"></a>Kontrollera om mjuk borttagning är aktiverat för ett nyckelvalv och aktivera mjuk borttagning

1. Logga in på Azure Portal.
1. Välj ditt nyckelvalv.
1. Klicka på bladet "Egenskaper".
1. Kontrollera om alternativknappen bredvid mjuk borttagning är inställd på "Aktivera återställning".
1. Om mjuk borttagning inte är aktiverat i nyckelvalvet klickar du på alternativknappen för att aktivera mjuk borttagning och klickar på "Spara".

:::image type="content" source="../media/key-vault-recovery-1.png" alt-text="I Egenskaper är Mjuk borttagning markerat, liksom värdet för att aktivera det.":::

## <a name="grant-access-to-a-service-principal-to-purge-and-recover-deleted-secrets"></a>Bevilja åtkomst till ett huvudnamn för tjänsten för att rensa och återställa borttagna hemligheter

1. Logga in på Azure Portal.
1. Välj ditt nyckelvalv.
1. Klicka på bladet Åtkomstprincip.
1. I tabellen hittar du raden för det säkerhetsobjekt som du vill bevilja åtkomst till (eller lägga till ett nytt säkerhetsobjekt).
1. Klicka på listrutan för nycklar, certifikat och hemligheter.
1. Rulla längst ned i listrutan och klicka på "Återställ" och "Rensa"
1. Säkerhetsobjekt behöver också hämta och lista funktioner för att utföra de flesta åtgärder.

:::image type="content" source="../media/key-vault-recovery-2.png" alt-text="Åtkomstprinciper är markerade i det vänstra navigeringsfönstret. I Åtkomstprinciper visas listrutan Hemliga positioner och fyra objekt är markerade: Hämta, Lista, Återställ och Rensa.":::

## <a name="list-recover-or-purge-a-soft-deleted-key-vault"></a>Lista, återställa eller rensa ett mjukt borttagna nyckelvalv

1. Logga in på Azure Portal.
1. Klicka på sökfältet överst på sidan.
1. Under Senaste tjänster klickar du på "Key Vault". Klicka inte på ett enskilt nyckelvalv.
1. Längst upp på skärmen klickar du på alternativet för att "Hantera borttagna valv"
1. Ett kontextfönster öppnas till höger på skärmen.
1. Välj din prenumeration.
1. Om nyckelvalvet har mjuk borttagning visas det i kontextfönstret till höger.
1. Om det finns för många valv kan du antingen klicka på "Läs in mer" längst ned i kontextfönstret eller använda CLI eller PowerShell för att hämta resultatet.
1. När du har hittat valvet som du vill återställa eller rensa markerar du kryssrutan bredvid det.
1. Välj återställningsalternativet längst ned i kontextfönstret om du vill återställa nyckelvalvet.
1. Välj rensningsalternativet om du vill ta bort nyckelvalvet permanent.

:::image type="content" source="../media/key-vault-recovery-3.png" alt-text="Alternativet Hantera borttagna valv är markerat i Nyckelvalv.":::

:::image type="content" source="../media/key-vault-recovery-4.png" alt-text="På Hantera borttagna nyckelvalv är det enda nyckelvalvet i listan markerat och markerat och knappen Återställ är markerad.":::

## <a name="list-recover-or-purge-soft-deleted-secrets-keys-and-certificates"></a>Visa, återställa eller rensa mjukt borttagna hemligheter, nycklar och certifikat

1. Logga in på Azure Portal.
1. Välj ditt nyckelvalv.
1. Välj bladet som motsvarar den hemlighetstyp som du vill hantera (nycklar, hemligheter eller certifikat).
1. Överst på skärmen klickar du på "Hantera borttagna (nycklar, hemligheter eller certifikat)
1. Ett kontextfönster visas till höger på skärmen.
1. Om din hemlighet, nyckel eller certifikat inte visas i listan är den inte i läget för mjuk borttagning.
1. Välj hemligheten, nyckeln eller certifikatet som du vill hantera.
1. Välj alternativet för att återställa eller rensa längst ned i kontextfönstret.

:::image type="content" source="../media/key-vault-recovery-5.png" alt-text="Alternativet Hantera borttagna nycklar är markerat i Nycklar.":::

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

## <a name="key-vault-cli"></a>Key Vault (CLI)

* Kontrollera om mjuk borttagning är aktiverat för ett nyckelvalv

    ```azurecli
    az keyvault show --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} -n {VAULT NAME}
    ```

* Aktivera mjuk borttagning för nyckelvalv

    Alla nya nyckelvalv har mjuk borttagning aktiverat som standard. Om du för närvarande har ett nyckelvalv som inte har mjuk borttagning aktiverat använder du följande kommando för att aktivera mjuk borttagning.

    ```azurecli
    az keyvault update --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} -n {VAULT NAME} --enable-soft-delete true
    ```

* Ta bort nyckelvalv (kan återställas om mjuk borttagning är aktiverat)

    ```azurecli
    az keyvault delete --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} -n {VAULT NAME}
    ```

* Lista alla mjukt borttagna nyckelvalv

    ```azurecli
    az keyvault list-deleted --subscription {SUBSCRIPTION ID} --resource-type vault
    ```

* Återställa mjukt borttagna nyckelvalv

    ```azurecli
    az keyvault recover --subscription {SUBSCRIPTION ID} -n {VAULT NAME}
    ```

* Rensa mjuk borttagning av nyckelvalv **(VARNING! DEN HÄR ÅTGÄRDEN TAR PERMANENT BORT DITT NYCKELVALV)**

    ```azurecli
    az keyvault purge --subscription {SUBSCRIPTION ID} -n {VAULT NAME}
    ```

* Aktivera rensningsskydd för nyckelvalv

    ```azurecli
    az keyvault update --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} -n {VAULT NAME} --enable-purge-protection true
    ```

## <a name="certificates-cli"></a>Certifikat (CLI)

* Bevilja åtkomst för att rensa och återställa certifikat

    ```azurecli
    az keyvault set-policy --upn user@contoso.com --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} -n {VAULT NAME}  --certificate-permissions recover purge
    ```

* Ta bort certifikat

    ```azurecli
    az keyvault certificate delete --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {CERTIFICATE NAME}
    ```

* Lista över borttagna certifikat

    ```azurecli
    az keyvault certificate list-deleted --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME}
    ```

* Återställa borttagna certifikat

    ```azurecli
    az keyvault certificate recover --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {CERTIFICATE NAME}
    ```

* Rensa mjukt borttagna certifikat **(VARNING! DEN HÄR ÅTGÄRDEN TAR BORT CERTIFIKATET PERMANENT)**

    ```azurecli
    az keyvault certificate purge --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {CERTIFICATE NAME}
    ```

## <a name="keys-cli"></a>Nycklar (CLI)

* Bevilja åtkomst för att rensa och återställa nycklar

    ```azurecli
    az keyvault set-policy --upn user@contoso.com --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} -n {VAULT NAME}  --key-permissions recover purge
    ```

* Ta bort nyckel

    ```azurecli
    az keyvault key delete --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {KEY NAME}
    ```

* Visa en lista över borttagna nycklar

    ```azurecli
    az keyvault key list-deleted --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME}
    ```

* Återställa borttagna nycklar

    ```azurecli
    az keyvault key recover --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {KEY NAME}
    ```

* Rensa mjuk borttagningsnyckel **(VARNING! DEN HÄR ÅTGÄRDEN TAR BORT NYCKELN PERMANENT)**

    ```azurecli
    az keyvault key purge --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {KEY NAME}
    ```

## <a name="secrets-cli"></a>Hemligheter (CLI)

* Bevilja åtkomst för att rensa och återställa hemligheter

    ```azurecli
    az keyvault set-policy --upn user@contoso.com --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} -n {VAULT NAME}  --secret-permissions recover purge
    ```

* Ta bort hemlighet

    ```azurecli
    az keyvault secret delete --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {SECRET NAME}
    ```

* Lista borttagna hemligheter

    ```azurecli
    az keyvault secret list-deleted --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME}
    ```

* Återställa borttagna hemligheter

    ```azurecli
    az keyvault secret recover --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {SECRET NAME}
    ```

* Rensa mjukt borttagna hemligheter **(VARNING! DEN HÄR ÅTGÄRDEN TAR PERMANENT BORT DIN HEMLIGHET)**

    ```azurecli
    az keyvault secret purge --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {SECRET NAME}
    ```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

## <a name="key-vault-powershell"></a>Key Vault (PowerShell)

* Kontrollera om mjuk borttagning är aktiverat för ett nyckelvalv

    ```powershell
    Get-AzKeyVault -VaultName "ContosoVault"
    ```

* Ta bort nyckelvalv

    ```powershell
    Remove-AzKeyVault -VaultName 'ContosoVault'
    ```

* Lista alla mjukt borttagna nyckelvalv

    ```powershell
    Get-AzKeyVault -InRemovedState
    ```

* Återställa mjukt borttagna nyckelvalv

    ```powershell
    Undo-AzKeyVaultRemoval -VaultName ContosoVault -ResourceGroupName ContosoRG -Location westus
    ```

* Rensa mjukt borttagna nyckelvalv **(VARNING! DEN HÄR ÅTGÄRDEN TAR PERMANENT BORT DITT NYCKELVALV)**

    ```powershell
    Remove-AzKeyVault -VaultName ContosoVault -InRemovedState -Location westus
    ```

* Aktivera rensningsskydd för key-vault

    ```powershell
    ($resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "ContosoVault").ResourceId).Properties | Add-Member -MemberType "NoteProperty" -Name "enablePurgeProtection" -Value "true"

    Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
    ```

## <a name="certificates-powershell"></a>Certifikat (PowerShell)

* Bevilja behörigheter för att återställa och rensa certifikat

    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName ContosoVault -UserPrincipalName user@contoso.com -PermissionsToCertificates recover,purge
    ```

* Ta bort ett certifikat

  ```powershell
  Remove-AzKeyVaultCertificate -VaultName ContosoVault -Name 'MyCert'
  ```

* Lista alla borttagna certifikat i ett nyckelvalv

  ```powershell
  Get-AzKeyVaultCertificate -VaultName ContosoVault -InRemovedState
  ```

* Återställa ett certifikat i borttagna tillstånd

  ```powershell
  Undo-AzKeyVaultCertificateRemoval -VaultName ContosoVault -Name 'MyCert'
  ```

* Rensa ett mjukt borttagna certifikat **(VARNING! DEN HÄR ÅTGÄRDEN TAR BORT CERTIFIKATET PERMANENT)**

  ```powershell
  Remove-AzKeyVaultcertificate -VaultName ContosoVault -Name 'MyCert' -InRemovedState
  ```

## <a name="keys-powershell"></a>Nycklar (PowerShell)

* Bevilja behörigheter för att återställa och rensa nycklar

    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName ContosoVault -UserPrincipalName user@contoso.com -PermissionsToKeys recover,purge
    ```

* Ta bort en nyckel

  ```powershell
  Remove-AzKeyVaultKey -VaultName ContosoVault -Name 'MyKey'
  ```

* Lista alla borttagna certifikat i ett nyckelvalv

  ```powershell
  Get-AzKeyVaultKey -VaultName ContosoVault -InRemovedState
  ```

* Så här återställer du en mjuk borttagningsnyckel

    ```powershell
    Undo-AzKeyVaultKeyRemoval -VaultName ContosoVault -Name ContosoFirstKey
    ```

* Rensa en mjuk borttagningsnyckel **(VARNING! DEN HÄR ÅTGÄRDEN TAR BORT NYCKELN PERMANENT)**

    ```powershell
    Remove-AzKeyVaultKey -VaultName ContosoVault -Name ContosoFirstKey -InRemovedState
    ```

## <a name="secrets-powershell"></a>Hemligheter (PowerShell)

* Bevilja behörigheter för att återställa och rensa hemligheter

    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName ContosoVault -UserPrincipalName user@contoso.com -PermissionsToSecrets recover,purge
    ```

* Ta bort en hemlighet med namnet SQLPassword

  ```powershell
  Remove-AzKeyVaultSecret -VaultName ContosoVault -name SQLPassword
  ```

* Lista alla borttagna hemligheter i ett nyckelvalv

  ```powershell
  Get-AzKeyVaultSecret -VaultName ContosoVault -InRemovedState
  ```

* Återställa en hemlighet i borttagna tillstånd

  ```powershell
  Undo-AzKeyVaultSecretRemoval -VaultName ContosoVault -Name SQLPAssword
  ```

* Rensa en hemlighet i borttagna tillstånd **(VARNING! DEN HÄR ÅTGÄRDEN TAR BORT NYCKELN PERMANENT)**

  ```powershell
  Remove-AzKeyVaultSecret -VaultName ContosoVault -InRemovedState -name SQLPassword
  ```
---

## <a name="next-steps"></a>Nästa steg

- [Azure Key Vault PowerShell-cmdlets](/powershell/module/az.keyvault)
- [Key Vault Azure CLI-kommandon](/cli/azure/keyvault)
- [Azure Key Vault säkerhetskopiering](backup.md)
- [Aktivera Key Vault loggning](howto-logging.md)
- [Säker åtkomst till ett nyckelvalv](security-overview.md)
- [Azure Key Vault utvecklarguide](developers-guide.md)
- [Metodtips för att använda ett nyckelvalv](security-overview.md)