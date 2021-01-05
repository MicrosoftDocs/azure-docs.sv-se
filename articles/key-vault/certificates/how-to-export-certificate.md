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
ms.openlocfilehash: 0925b51ed960b8007d2df86115ea7e5cf627fe7e
ms.sourcegitcommit: e7179fa4708c3af01f9246b5c99ab87a6f0df11c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/30/2020
ms.locfileid: "97826128"
---
# <a name="export-certificates-from-azure-key-vault"></a>Exportera certifikat från Azure Key Vault

Lär dig hur du exporterar certifikat från Azure Key Vault. Du kan exportera certifikat med hjälp av Azure CLI, Azure PowerShell eller Azure Portal. Du kan också använda Azure Portal för att exportera Azure App Service certifikat.

## <a name="about-azure-key-vault-certificates"></a>Om Azure Key Vault-certifikat

Med Azure Key Vault kan du enkelt etablera, hantera och distribuera digitala certifikat för nätverket. Det möjliggör även säker kommunikation för program. Mer information finns i [Azure Key Vault certifikat](./about-certificates.md) .

### <a name="composition-of-a-certificate"></a>Sammansättning av ett certifikat

När ett Key Vault certifikat skapas skapas en adresserad *nyckel* och en *hemlighet* som har samma namn. Key Vault nyckeln tillåter nyckel åtgärder. Med Key Vault hemligheten kan du hämta certifikatets värde som en hemlighet. Ett Key Vault certifikat innehåller även metadata för offentliga x509-certifikat. Mer information finns i [sammansättningen av ett certifikat](./about-certificates.md#composition-of-a-certificate) .

### <a name="exportable-and-non-exportable-keys"></a>Nycklar som kan exporteras och som inte kan exporteras

När ett Key Vault-certifikat har skapats kan du hämta det från den adresser bara hemligheten med den privata nyckeln. Hämta certifikatet i PFX-eller PEM-format.

- Kan **exporteras**: den princip som används för att skapa certifikatet anger att nyckeln kan exporteras.
- Kan **inte exporteras**: den princip som används för att skapa certifikatet anger att nyckeln inte kan exporteras. I det här fallet är den privata nyckeln inte en del av värdet när den hämtas som en hemlighet.

Typer som stöds: RSA, RSA-HSM, EC, EC-HSM, okt (listade [här](/rest/api/keyvault/createcertificate/createcertificate#jsonwebkeytype)) kan exporteras endast med RSA, ec. HSM-nycklar kan inte exporteras.

Mer information finns i [om Azure Key Vault certifikat](./about-certificates.md#exportable-or-non-exportable-key) .

## <a name="export-stored-certificates"></a>Exportera lagrade certifikat

Du kan exportera lagrade certifikat i Azure Key Vault med hjälp av Azure CLI, Azure PowerShell eller Azure-portalen.

> [!NOTE]
> Kräv bara certifikat lösen ord när du importerar certifikatet i nyckel valvet. Key Vault sparar inte det associerade lösenordet. När du exporterar certifikatet är lösen ordet tomt.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Använd följande kommando i Azure CLI för att hämta den **offentliga delen** av ett Key Vault certifikat.

```azurecli
az keyvault certificate download --file
                                 [--encoding {DER, PEM}]
                                 [--id]
                                 [--name]
                                 [--subscription]
                                 [--vault-name]
                                 [--version]
```

Visa [exempel och parameter definitioner](/cli/azure/keyvault/certificate?view=azure-cli-latest#az-keyvault-certificate-download) för mer information.

Hämtning som certifikat innebär att den offentliga delen hämtas. Om du vill ha både den privata nyckeln och offentliga metadata kan du ladda ned den som hemlig.

```azurecli
az keyvault secret download -–file {nameofcert.pfx}
                            [--encoding {ascii, base64, hex, utf-16be, utf-16le, utf-8}]
                            [--id]
                            [--name]
                            [--subscription]
                            [--vault-name]
                            [--version]
```

Mer information finns i [parameter definitioner](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-download).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Använd det här kommandot i Azure PowerShell för att hämta certifikatet med namnet **TestCert01** från nyckel valvet med namnet **ContosoKV01**. Kör följande kommando för att ladda ned certifikatet som en PFX-fil. De här kommandona får åtkomst till **SecretId** och sparar sedan innehållet som en PFX-fil.

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

Det här kommandot exporterar hela kedjan med certifikat med privat nyckel. Certifikatet är lösenordsskyddat.
Mer information om kommandot **Get-AzKeyVaultCertificate** finns i [Get-AzKeyVaultCertificate-example 2](/powershell/module/az.keyvault/Get-AzKeyVaultCertificate?view=azps-4.4.0).

# <a name="portal"></a>[Portal](#tab/azure-portal)

När du har skapat/importerat ett certifikat på **certifikat** bladet på Azure Portal får du ett meddelande om att certifikatet har skapats. Välj certifikatet och den aktuella versionen för att se alternativet att ladda ned.

Om du vill hämta certifikatet väljer du **Hämta i CER-format** eller **Ladda ned i PFX/PEM-format**.

![Hämta certifikat](../media/certificates/quick-create-portal/current-version-shown.png)

**Exportera Azure App Service certifikat**

Azure App Service certifikat är ett bekvämt sätt att köpa SSL-certifikat. Du kan tilldela dem till Azure-appar inifrån portalen. Du kan också exportera dessa certifikat från portalen som PFX-filer som ska användas någon annan stans. När du har importerat dem finns App Service certifikaten under **hemligheter**.

Mer information finns i steg för att [exportera Azure App Service certifikat](https://social.technet.microsoft.com/wiki/contents/articles/37431.exporting-azure-app-service-certificates.aspx).

---

## <a name="read-more"></a>Läs mer
* [Olika typer av certifikat typer och definitioner](/archive/blogs/kaushal/various-ssltls-certificate-file-typesextensions)
