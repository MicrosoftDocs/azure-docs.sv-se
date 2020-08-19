---
title: Exportera certifikat från Azure Key Vault
description: Exportera certifikat från Azure Key Vault
services: key-vault
author: sebansal
tags: azure-key-vault
ms.service: key-vault
ms.subservice: certificates
ms.topic: how-to
ms.custom: mvc, devx-track-azurecli
ms.date: 08/11/2020
ms.author: sebansal
ms.openlocfilehash: afab65b22d9487f30da458346bf143a557bec0d8
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2020
ms.locfileid: "88588900"
---
# <a name="export-certificate-from-azure-key-vault"></a>Exportera certifikat från Azure Key Vault

Med Azure Key Vault kan du enkelt etablera, hantera och distribuera digitala certifikat för nätverket och aktivera säker kommunikation för program. Mer allmän information om certifikat finns i [Azure Key Vault certifikat](https://docs.microsoft.com/azure/key-vault/certificates/about-certificates)

## <a name="about-azure-key-vault-certificate"></a>Om Azure Key Vault-certifikat

### <a name="composition-of-certificate"></a>Sammansättning av certifikat
När ett Key Vault certifikat skapas, skapas även en adresserad nyckel och hemlighet med samma namn. Key Vault-nyckeln tillåter nyckel åtgärder och Key Vault hemligheten kan hämta certifikatets värde som en hemlighet. Ett Key Vault certifikat innehåller även metadata för offentliga x509-certifikat. [Läs mer](https://docs.microsoft.com/azure/key-vault/certificates/about-certificates#composition-of-a-certificate)

### <a name="exportable-or-non-exportable-keys"></a>Nycklar som kan exporteras eller som inte kan exporteras
När ett Key Vault-certifikat skapas, kan det hämtas från den adresser bara hemligheten med den privata nyckeln i antingen PFX-eller PEM-format. Principen som används för att skapa certifikatet måste ange att nyckeln kan exporteras. Om principen anger att det inte går att exportera, är den privata nyckeln inte en del av värdet när den hämtas som en hemlighet.

Två typer av nycklar stöds – RSA eller RSA HSM med certifikat. Exportable tillåts endast med RSA, stöds inte av RSA HSM. [Läs mer](https://docs.microsoft.com/azure/key-vault/certificates/about-certificates#exportable-or-non-exportable-key)

Lagrat certifikat i Azure Key Vault kan exporteras med Azure CLI, PowerShell eller Portal.

> [!NOTE]
> Det är viktigt att Observera att du bara behöver lösen ordet för ett certifikat när du importerar det i nyckel valvet. Key Vault skulle inte spara det associerade lösen ordet, vilket innebär att om du vill exportera certifikatet måste lösen ordet vara tomt.

## <a name="exporting-certificate-using-cli"></a>Exportera certifikat med CLI
Med följande kommando kan du ladda ned den **offentliga delen** av ett Key Vault certifikat.

```azurecli
az keyvault certificate download --file
                                 [--encoding {DER, PEM}]
                                 [--id]
                                 [--name]
                                 [--subscription]
                                 [--vault-name]
                                 [--version]
```
Exempel och parameter definitioner visas [här](https://docs.microsoft.com/cli/azure/keyvault/certificate?view=azure-cli-latest#az-keyvault-certificate-download)



Om du vill ladda ned hela certifikatet, t. ex. **både offentliga och privata delar av sin sammansättning**, kan du göra det genom att ladda ned certifikatet som en hemlighet.

```azurecli
az keyvault secret download –file {nameofcert.pfx}
                            [--encoding {ascii, base64, hex, utf-16be, utf-16le, utf-8}]
                            [--id]
                            [--name]
                            [--subscription]
                            [--vault-name]
                            [--version]
```
För parameter definitioner, [Se här](https://docs.microsoft.com/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-download)


## <a name="exporting-certificate-using-powershell"></a>Exportera certifikat med PowerShell

Det här kommandot hämtar certifikatet med namnet TestCert01 från nyckel valvet med namnet ContosoKV01. Kör följande kommando för att ladda ned certifikatet som PFX-fil. De här kommandona får åtkomst till SecretId och sparar sedan innehållet som en PFX-fil.

```azurepowershell
$cert = Get-AzKeyVaultCertificate -VaultName "ContosoKV01" -Name "TestCert01"
$kvSecret = Get-AzKeyVaultSecret -VaultName "ContosoKV01" -Name $Cert.Name
$kvSecretBytes = [System.Convert]::FromBase64String($kvSecret.SecretValueText)
$certCollection = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2Collection
$certCollection.Import($kvSecretBytes,$null,[System.Security.Cryptography.X509Certificates.X509KeyStorageFlags]::Exportable)
$password = '******'
$protectedCertificateBytes = $certCollection.Export([System.Security.Cryptography.X509Certificates.X509ContentType]::Pkcs12, $password)
$pfxPath = [Environment]::GetFolderPath("Desktop") + "\MyCert.pfx"
[System.IO.File]::WriteAllBytes($pfxPath, $protectedCertificateBytes)
```
Detta kommer att exportera hela kedjan med certifikat med privat nyckel och det här certifikatet kommer att vara lösenordsskyddat.
Mer information om ```Get-AzKeyVaultCertificate``` kommandon och parametrar finns i [exempel 2](https://docs.microsoft.com/powershell/module/az.keyvault/Get-AzKeyVaultCertificate?view=azps-4.4.0)

## <a name="exporting-certificate-using-portal"></a>Exportera certifikat med hjälp av portalen

När du skapar/importerar ett certifikat på certifikat bladet på portalen visas ett meddelande om att certifikatet har skapats. När du väljer certifikatet kan du klicka på den aktuella versionen så visas alternativet för att ladda ned.


Genom att klicka på knappen Ladda ned i CER-format eller ladda ned i PFX/PEM format kan du hämta certifikatet.


![Hämta certifikat](../media/certificates/quick-create-portal/current-version-shown.png)


## <a name="exporting-app-service-certificate-from-key-vault"></a>Exportera App Service Certificate från Key Vault

Azure App Service certifikat är ett bekvämt sätt att köpa SSL-certifikat och tilldela dem till Azure-appar direkt i portalen. Dessa certifikat kan också exporteras från portalen som PFX-filer som ska användas någon annan stans.
När du har importerat kan App Service-certifikat finnas **under hemligheter**.

Anvisningar för att exportera App Service certificates finns [här](https://social.technet.microsoft.com/wiki/contents/articles/37431.exporting-azure-app-service-certificates.aspx)

## <a name="read-more"></a>Läs mer
* [Olika typer av certifikat typer och definitioner](https://docs.microsoft.com/archive/blogs/kaushal/various-ssltls-certificate-file-typesextensions)