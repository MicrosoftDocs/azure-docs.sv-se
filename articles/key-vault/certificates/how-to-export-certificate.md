---
title: Exportera certifikat från Azure Key Vault
description: Lär dig hur du exporterar certifikat från Azure Key Vault.
services: key-vault
author: sebansal
tags: azure-key-vault
ms.service: key-vault
ms.subservice: certificates
ms.topic: how-to
ms.custom: mvc
ms.date: 08/11/2020
ms.author: sebansal
ms.openlocfilehash: 0efe0164d8e1a4e5bc3b9d6d7313855740afd316
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107767915"
---
# <a name="export-certificates-from-azure-key-vault"></a>Exportera certifikat från Azure Key Vault

Lär dig hur du exporterar certifikat från Azure Key Vault. Du kan exportera certifikat med hjälp av Azure CLI, Azure PowerShell eller Azure Portal. 

## <a name="about-azure-key-vault-certificates"></a>Om Azure Key Vault-certifikat

Azure Key Vault kan du enkelt etablera, hantera och distribuera digitala certifikat för nätverket. Det möjliggör även säker kommunikation för program. Mer information Azure Key Vault finns i [Azure Key Vault certifikat.](./about-certificates.md)

### <a name="composition-of-a-certificate"></a>Sammansättning av ett certifikat

När ett Key Vault-certifikat skapas skapas en *adresserbar* nyckel *och* hemlighet med samma namn. Nyckelnyckeln Key Vault nyckelåtgärder. Den Key Vault hemligheten tillåter hämtning av certifikatvärdet som en hemlighet. Ett Key Vault certifikat innehåller också offentliga x509-certifikatmetadata. Mer information [finns i Sammansättning av ett](./about-certificates.md#composition-of-a-certificate) certifikat.

### <a name="exportable-and-non-exportable-keys"></a>Nycklar som kan exporteras och inte kan exporteras

När ett Key Vault certifikat har skapats kan du hämta det från den adresserbara hemligheten med den privata nyckeln. Hämta certifikatet i PFX- eller PEM-format.

- **Exporterbar:** Principen som används för att skapa certifikatet anger att nyckeln kan exporteras.
- **Kan inte exporteras:** Principen som används för att skapa certifikatet anger att nyckeln inte kan exporteras. I det här fallet är den privata nyckeln inte en del av värdet när den hämtas som en hemlighet.

Nyckeltyper som stöds: RSA, RSA-HSM, EC, EC-HSM, okt (anges [här](/rest/api/keyvault/createcertificate/createcertificate#jsonwebkeytype)) Exporterbar tillåts endast med RSA, EC. HSM-nycklar skulle inte kunna exporteras.

Se [Om Azure Key Vault certifikat](./about-certificates.md#exportable-or-non-exportable-key) för mer information.

## <a name="export-stored-certificates"></a>Exportera lagrade certifikat

Du kan exportera lagrade certifikat i Azure Key Vault med hjälp av Azure CLI, Azure PowerShell eller Azure-portalen.

> [!NOTE]
> Kräv endast ett certifikatlösenord när du importerar certifikatet i nyckelvalvet. Key Vault sparar inte det associerade lösenordet. När du exporterar certifikatet är lösenordet tomt.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Använd följande kommando i Azure CLI för att ladda ned den **offentliga delen av** Key Vault certifikat.

```azurecli
az keyvault certificate download --file
                                 [--encoding {DER, PEM}]
                                 [--id]
                                 [--name]
                                 [--subscription]
                                 [--vault-name]
                                 [--version]
```

Visa [exempel och parameterdefinitioner](/cli/azure/keyvault/certificate#az_keyvault_certificate_download) för mer information.

Att ladda ned som certifikat innebär att hämta den offentliga delen. Om du vill ha både den privata nyckeln och offentliga metadata kan du ladda ned den som hemlighet.

```azurecli
az keyvault secret download -–file {nameofcert.pfx}
                            [--encoding {ascii, base64, hex, utf-16be, utf-16le, utf-8}]
                            [--id]
                            [--name]
                            [--subscription]
                            [--vault-name]
                            [--version]
```

Mer information finns i [parameterdefinitioner](/cli/azure/keyvault/secret#az_keyvault_secret_download).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Använd det här kommandot i Azure PowerShell att hämta certifikatet med namnet **TestCert01** från nyckelvalvet med namnet **ContosoKV01.** Om du vill ladda ned certifikatet som en PFX-fil kör du följande kommando. Dessa kommandon har **åtkomst till SecretId** och sparar sedan innehållet som en PFX-fil.

```azurepowershell
$cert = Get-AzKeyVaultCertificate -VaultName "ContosoKV01" -Name "TestCert01"
$secret = Get-AzKeyVaultSecret -VaultName "ContosoKV01" -Name $cert.Name
$secretValueText = '';
$ssPtr = [System.Runtime.InteropServices.Marshal]::SecureStringToBSTR($secret.SecretValue)
try {
    $secretValueText = [System.Runtime.InteropServices.Marshal]::PtrToStringBSTR($ssPtr)
} finally {
    [System.Runtime.InteropServices.Marshal]::ZeroFreeBSTR($ssPtr)
}
$secretByte = [Convert]::FromBase64String($secretValueText)
$x509Cert = new-object System.Security.Cryptography.X509Certificates.X509Certificate2
$x509Cert.Import($secretByte, "", "Exportable,PersistKeySet")
$type = [System.Security.Cryptography.X509Certificates.X509ContentType]::Pfx
$pfxFileByte = $x509Cert.Export($type, $password)

# Write to a file
[System.IO.File]::WriteAllBytes("KeyVault.pfx", $pfxFileByte)
```

Det här kommandot exporterar hela certifikatkedjan med privat nyckel (det vill säga samma som den importerades). Certifikatet är lösenordsskyddat.
Mer information om kommandot och parametrarna **Get-AzKeyVaultCertificate** finns [i Get-AzKeyVaultCertificate – exempel 2.](/powershell/module/az.keyvault/Get-AzKeyVaultCertificate)

# <a name="portal"></a>[Portal](#tab/azure-portal)

När Azure Portal har skapat/importerat ett certifikat på  bladet Certifikat får du ett meddelande om att certifikatet har skapats. Välj certifikatet och den aktuella versionen för att se alternativet att ladda ned.

Om du vill ladda ned certifikatet väljer **du Ladda ned i CER-format** eller Ladda ned i **PFX/PEM-format.**

![Nedladdning av certifikat](../media/certificates/quick-create-portal/current-version-shown.png)

**Exportera Azure App Service certifikat**

Azure App Service är ett praktiskt sätt att köpa SSL-certifikat. Du kan tilldela dem till Azure Apps från portalen. När du har importerat App Service certifikaten under **hemligheterna**.

Mer information finns i stegen för att [exportera Azure App Service certifikat](https://social.technet.microsoft.com/wiki/contents/articles/37431.exporting-azure-app-service-certificates.aspx).

---

## <a name="read-more"></a>Läs mer
* [Olika typer och definitioner av certifikatfiler](/archive/blogs/kaushal/various-ssltls-certificate-file-typesextensions)
