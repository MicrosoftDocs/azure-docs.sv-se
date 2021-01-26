---
title: Översikt över Azure Key Vault återställning | Microsoft Docs
description: Key Vault återställnings funktioner är utformade för att förhindra oavsiktlig eller skadlig borttagning av ditt nyckel valv och hemligheter, nycklar och certifikat som lagras i nyckel valvet.
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.author: mbaldwin
author: msmbaldwin
manager: rkarlin
ms.date: 09/30/2020
ms.openlocfilehash: a8e8e791f0dbe18322ad43364ae4ffd09b430caf
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/26/2021
ms.locfileid: "98790392"
---
# <a name="azure-key-vault-recovery-management-with-soft-delete-and-purge-protection"></a>Azure Key Vault återställnings hantering med mjuk borttagning och rensnings skydd

Den här artikeln beskriver två återställnings funktioner i Azure Key Vault, mjuk borttagning och tömning av skydd. Det här dokumentet ger en översikt över dessa funktioner och visar hur du hanterar dem via Azure Portal, Azure CLI och Azure PowerShell.

Mer information om Key Vault finns i
- [Översikt över Key Vault](overview.md)
- [Översikt över Azure Key Vault nycklar, hemligheter och certifikat](about-keys-secrets-certificates.md)

## <a name="prerequisites"></a>Krav

* En Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/dotnet)
* [PowerShell-modul](/powershell/azure/install-az-ps).
* [Azure CLI](/cli/azure/install-azure-cli)
* En Key Vault – du kan skapa en med hjälp av [Azure Portal](../general/quick-create-portal.md) [Azure CLI](../general/quick-create-cli.md)eller [Azure PowerShell](../general/quick-create-powershell.md)
* Användaren behöver följande behörigheter (på prenumerations nivå) för att utföra åtgärder på mjuka, borttagna valv:

  | Behörighet | Beskrivning |
  |---|---|
  |Microsoft. nyckel-valv/platser/deletedVaults/läsa|Visa egenskaperna för ett ej permanent borttaget nyckel valv|
  |Microsoft. nyckel-valv/platser/deletedVaults/rensning/åtgärd|Rensa ett ej permanent borttaget nyckel valv|


## <a name="what-are-soft-delete-and-purge-protection"></a>Vad är mjuk borttagning och rensnings skydd

Det [mjuka borttagnings](soft-delete-overview.md) -och tömnings skyddet är två olika återställnings funktioner för nyckel valv.

> [!IMPORTANT]
> Att aktivera mjuk borttagning är viktigt för att säkerställa att nyckel valven och autentiseringsuppgifterna skyddas från oavsiktlig borttagning. Att aktivera mjuk borttagning betraktas dock som en brytande ändring eftersom det kan krävas att du ändrar program logiken eller ger ytterligare behörighet till tjänstens huvud namn. Innan du aktiverar mjuk borttagning genom att följa anvisningarna nedan kontrollerar du att programmet är kompatibelt med ändringen med det här dokumentet [ **här**.](soft-delete-change.md)

**Mjuk borttagning** är utformad för att förhindra oavsiktlig borttagning av nyckel valvet och nycklar, hemligheter och certifikat som lagras i Key Vault. Tänk på mjuk borttagning som en pappers korg. När du tar bort ett nyckel valv eller ett Key Vault-objekt fortsätter det att kunna återskapas för en användar konfigurerbar kvarhållningsperiod eller standardvärdet på 90 dagar. Nyckel valv i läget Soft Deleted kan också **rensas** , vilket innebär att de tas bort permanent. På så sätt kan du återskapa nyckel valv och Key Vault-objekt med samma namn. Både återställning och borttagning av nyckel valv och objekt kräver förhöjd åtkomst princip behörigheter. **När mjuk borttagning har Aktiver ATS kan det inte inaktive ras.**

Det är viktigt att Observera att **nyckel valvs namn är globalt unika**, så du kan inte skapa ett nyckel valv med samma namn som ett nyckel valv i läget Soft Deleted. På samma sätt är namnen på nycklar, hemligheter och certifikat unika i ett nyckel valv. Du kommer inte att kunna skapa en hemlighet, nyckel eller certifikat med samma namn som ett annat i läget Soft Deleted.

**Rensnings skyddet** är utformat för att förhindra borttagning av nyckel valvet, nycklar, hemligheter och certifikat med skadlig Insider. Tänk på detta som en pappers korg med ett tidsbaserat lås. Du kan återställa objekt när som helst under den konfigurerbara kvarhållningsperioden. **Du kommer inte att kunna ta bort eller ta bort ett nyckel valv permanent förrän kvarhållningsperioden förflutit.** När kvarhållningsperioden har gått ur nyckel valvet eller Key Vault-objektet rensas det automatiskt.

> [!NOTE]
> Rensnings skyddet har utformats så att ingen administratörs roll eller-behörighet kan åsidosätta, inaktivera eller kringgå rensnings skyddet. **När rensnings skyddet har Aktiver ATS kan det inte inaktive ras eller åsidosättas av någon som inkluderar Microsoft.** Det innebär att du måste återställa ett borttaget nyckel valv eller vänta tills kvarhållningsperioden har gått ut innan du återanvänder nyckel valvets namn.

Mer information om mjuk borttagning finns i [Azure Key Vault översikt över mjuk borttagning](soft-delete-overview.md)

# <a name="azure-portal"></a>[Azure-portalen](#tab/azure-portal)

## <a name="verify-if-soft-delete-is-enabled-on-a-key-vault-and-enable-soft-delete"></a>Kontrol lera om mjuk borttagning är aktiverat på ett nyckel valv och aktivera mjuk borttagning

1. Logga in på Azure Portal.
1. Välj ditt nyckel valv.
1. Klicka på bladet "egenskaper".
1. Kontrol lera att alternativ knappen bredvid mjuk borttagning är inställd på Aktivera återställning.
1. Om mjuk borttagning inte är aktiverat i nyckel valvet, klickar du på alternativ knappen för att aktivera mjuk borttagning och klickar på Spara.

:::image type="content" source="../media/key-vault-recovery-1.png" alt-text="I egenskaper är mjuk borttagning markerat, precis som värdet för att aktivera det.":::

## <a name="grant-access-to-a-service-principal-to-purge-and-recover-deleted-secrets"></a>Bevilja åtkomst till ett huvud namn för tjänsten för att rensa och återställa borttagna hemligheter

1. Logga in på Azure Portal.
1. Välj ditt nyckel valv.
1. Klicka på bladet "åtkomst princip".
1. Leta upp raden för det säkerhets objekt som du vill bevilja åtkomst till (eller Lägg till ett nytt säkerhets objekt) i tabellen.
1. Klicka på list rutan för nycklar, certifikat och hemligheter.
1. Rulla längst ned i list rutan och klicka på "Återställ" och "Rensa"
1. Säkerhets objekt kommer också att behöva hämta och lista funktioner för att utföra de flesta åtgärder.

:::image type="content" source="../media/key-vault-recovery-2.png" alt-text="I det vänstra navigerings fönstret markeras åtkomst principer. I åtkomst principer visas List rutan hemliga positioner och fyra objekt väljs: Hämta, lista, Återställ och rensa.":::

## <a name="list-recover-or-purge-a-soft-deleted-key-vault"></a>Visa, återställa eller rensa ett mjukt borttaget nyckel valv

1. Logga in på Azure Portal.
1. Klicka på Sök fältet längst upp på sidan.
1. Under "senaste tjänster" klickar du på "Key Vault". Klicka inte på ett enskilt nyckel valv.
1. Längst upp på skärmen klickar du på alternativet för att "hantera borttagna valv"
1. Ett kontext fönster öppnas på höger sida av skärmen.
1. Välj din prenumeration.
1. Om nyckel valvet har varit mjukt Borttaget visas det i kontext fönstret till höger.
1. Om det finns för många valv kan du antingen klicka på Läs in mer längst ned i kontext fönstret eller använda CLI eller PowerShell för att hämta resultatet.
1. När du har hittat valvet du vill återställa eller rensa markerar du kryss rutan bredvid den.
1. Välj alternativet för att återställa längst ned i kontext fönstret om du vill återställa nyckel valvet.
1. Välj alternativet Rensa om du vill ta bort nyckel valvet permanent.

:::image type="content" source="../media/key-vault-recovery-3.png" alt-text="Alternativet hantera borttagna valv är markerat på nyckel valv.":::

:::image type="content" source="../media/key-vault-recovery-4.png" alt-text="Vid hantering av borttagna nyckel valv markeras och väljs det enda nyckel valvet som är markerat och knappen Återställ markeras.":::

## <a name="list-recover-or-purge-soft-deleted-secrets-keys-and-certificates"></a>Visa, återställa eller rensa mjuka borttagna hemligheter, nycklar och certifikat

1. Logga in på Azure Portal.
1. Välj ditt nyckel valv.
1. Välj det blad som motsvarar den hemliga typ som du vill hantera (nycklar, hemligheter eller certifikat).
1. Längst upp på skärmen klickar du på "hantera borttagna (nycklar, hemligheter eller certifikat)
1. Ett kontext fönster visas till höger på skärmen.
1. Om din hemliga nyckel eller ditt certifikat inte visas i listan är det inte i läget Soft-Deleted.
1. Välj den hemlighet, nyckel eller det certifikat som du vill hantera.
1. Välj alternativet för att återställa eller rensa längst ned i kontext fönstret.

:::image type="content" source="../media/key-vault-recovery-5.png" alt-text="Alternativet hantera borttagna nycklar är markerat på nycklar.":::

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

## <a name="key-vault-cli"></a>Key Vault (CLI)

* Verifiera om ett nyckel valv har mjuk borttagning aktiverat

    ```azurecli
    az keyvault show --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} -n {VAULT NAME}
    ```

* Aktivera mjuk borttagning vid nyckel-valvet

    Alla nya nyckel valv har mjuk borttagning aktiverat som standard. Om du för närvarande har ett nyckel valv som inte har mjuk borttagning aktiverat, använder du följande kommando för att aktivera mjuk borttagning.

    ```azurecli
    az keyvault update --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} -n {VAULT NAME} --enable-soft-delete true
    ```

* Ta bort nyckel valv (återställnings Bart om mjuk borttagning är aktiverat)

    ```azurecli
    az keyvault delete --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} -n {VAULT NAME}
    ```

* Visa en lista över alla mjuka borttagna nyckel valv

    ```azurecli
    az keyvault list-deleted --subscription {SUBSCRIPTION ID} --resource-type vault
    ```

* Återställa mjuk borttagnings nyckel – valv

    ```azurecli
    az keyvault recover --subscription {SUBSCRIPTION ID} -n {VAULT NAME}
    ```

* Rensa det mjuka borttagna nyckel valvet **(varning! MED den här åtgärden raderas nyckel VALVEt permanent**

    ```azurecli
    az keyvault purge --subscription {SUBSCRIPTION ID} -n {VAULT NAME}
    ```

* Aktivera rensnings skydd på nyckel-valvet

    ```azurecli
    az keyvault update --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} -n {VAULT NAME} --enable-purge-protection true
    ```

## <a name="certificates-cli"></a>Certifikat (CLI)

* Bevilja åtkomst till att rensa och återställa certifikat

    ```azurecli
    az keyvault set-policy --upn user@contoso.com --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} -n {VAULT NAME}  --certificate-permissions recover purge
    ```

* Ta bort certifikat

    ```azurecli
    az keyvault certificate delete --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {CERTIFICATE NAME}
    ```

* Lista borttagna certifikat

    ```azurecli
    az keyvault certificate list-deleted --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME}
    ```

* Återställ borttaget certifikat

    ```azurecli
    az keyvault certificate recover --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {CERTIFICATE NAME}
    ```

* Rensa ett mjukt borttaget certifikat **(varning! MED den här åtgärden raderas ditt certifikat permanent**

    ```azurecli
    az keyvault certificate purge --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {CERTIFICATE NAME}
    ```

## <a name="keys-cli"></a>Nycklar (CLI)

* Bevilja åtkomst till rensnings-och återställnings nycklar

    ```azurecli
    az keyvault set-policy --upn user@contoso.com --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} -n {VAULT NAME}  --key-permissions recover purge
    ```

* Ta bort nyckel

    ```azurecli
    az keyvault key delete --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {KEY NAME}
    ```

* Lista borttagna nycklar

    ```azurecli
    az keyvault key list-deleted --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME}
    ```

* Återställ borttagen nyckel

    ```azurecli
    az keyvault key recover --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {KEY NAME}
    ```

* Rensa mjuk borttagnings nyckel **(varning! MED den här åtgärden raderas nyckeln permanent**

    ```azurecli
    az keyvault key purge --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {KEY NAME}
    ```

## <a name="secrets-cli"></a>Hemligheter (CLI)

* Ge åtkomst till att rensa och återställa hemligheter

    ```azurecli
    az keyvault set-policy --upn user@contoso.com --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} -n {VAULT NAME}  --secret-permissions recover purge
    ```

* Ta bort hemlighet

    ```azurecli
    az keyvault secret delete --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {SECRET NAME}
    ```

* Lista över borttagna hemligheter

    ```azurecli
    az keyvault secret list-deleted --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME}
    ```

* Återställ borttagen hemlighet

    ```azurecli
    az keyvault secret recover --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {SECRET NAME}
    ```

* Rensa mjuk borttagen hemlighet **(varning! DEN här åtgärden kommer att ta bort din hemlighet permanent)**

    ```azurecli
    az keyvault secret purge --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {SECRET NAME}
    ```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

## <a name="key-vault-powershell"></a>Key Vault (PowerShell)

* Verifiera om ett nyckel valv har mjuk borttagning aktiverat

    ```powershell
    Get-AzKeyVault -VaultName "ContosoVault"
    ```

* Ta bort nyckel valv

    ```powershell
    Remove-AzKeyVault -VaultName 'ContosoVault'
    ```

* Visa en lista över alla mjuka borttagna nyckel valv

    ```powershell
    Get-AzKeyVault -InRemovedState
    ```

* Återställa mjuk borttagnings nyckel – valv

    ```powershell
    Undo-AzKeyVaultRemoval -VaultName ContosoVault -ResourceGroupName ContosoRG -Location westus
    ```

* Rensa mjuk borttagnings nyckel – valv **(varning! MED den här åtgärden raderas nyckel VALVEt permanent**

    ```powershell
    Remove-AzKeyVault -VaultName ContosoVault -InRemovedState -Location westus
    ```

* Aktivera rensnings skydd på nyckel-valvet

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

* Visa en lista med alla borttagna certifikat i ett nyckel valv

  ```powershell
  Get-AzKeyVaultCertificate -VaultName ContosoVault -InRemovedState
  ```

* Återställa ett certifikat i Borttaget tillstånd

  ```powershell
  Undo-AzKeyVaultCertificateRemoval -VaultName ContosoVault -Name 'MyCert'
  ```

* Rensa ett mjukt borttaget certifikat **(varning! MED den här åtgärden raderas ditt certifikat permanent**

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

* Visa en lista med alla borttagna certifikat i ett nyckel valv

  ```powershell
  Get-AzKeyVaultKey -VaultName ContosoVault -InRemovedState
  ```

* Återställa en mjuk borttagnings nyckel

    ```powershell
    Undo-AzKeyVaultKeyRemoval -VaultName ContosoVault -Name ContosoFirstKey
    ```

* Rensa en mjuk borttagnings nyckel **(varning! MED den här åtgärden raderas nyckeln permanent**

    ```powershell
    Remove-AzKeyVaultKey -VaultName ContosoVault -Name ContosoFirstKey -InRemovedState
    ```

## <a name="secrets-powershell"></a>Hemligheter (PowerShell)

* Bevilja behörighet att återställa och rensa hemligheter

    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName ContosoVault -UserPrincipalName user@contoso.com -PermissionsToSecrets recover,purge
    ```

* Ta bort en hemlighet med namnet SQLPassword

  ```powershell
  Remove-AzKeyVaultSecret -VaultName ContosoVault -name SQLPassword
  ```

* Visa en lista med alla borttagna hemligheter i ett nyckel valv

  ```powershell
  Get-AzKeyVaultSecret -VaultName ContosoVault -InRemovedState
  ```

* Återställa en hemlighet i Borttaget tillstånd

  ```powershell
  Undo-AzKeyVaultSecretRemoval -VaultName ContosoVault -Name SQLPAssword
  ```

* Rensa en hemlighet i Borttaget läge **(varning! MED den här åtgärden raderas nyckeln permanent**

  ```powershell
  Remove-AzKeyVaultSecret -VaultName ContosoVault -InRemovedState -name SQLPassword
  ```
---

## <a name="next-steps"></a>Nästa steg

- [Azure Key Vault PowerShell-cmdletar](/powershell/module/az.keyvault)
- [Key Vault Azure CLI-kommandon](/cli/azure/keyvault)
- [Azure Key Vault säkerhets kopiering](backup.md)
- [Så här aktiverar du Key Vault loggning](howto-logging.md)
- [Säker åtkomst till ett nyckelvalv](secure-your-key-vault.md)
- [Guide för Azure Key Vault utvecklare](developers-guide.md)
- [Metod tips för att använda ett nyckel valv](security-overview.md)