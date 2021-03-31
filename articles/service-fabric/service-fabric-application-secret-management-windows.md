---
title: Konfigurera ett krypterings certifikat på Windows-kluster
description: Lär dig hur du konfigurerar ett krypterings certifikat och krypterar hemligheter på Windows-kluster.
ms.topic: conceptual
ms.date: 01/04/2019
ms.openlocfilehash: eb4909d62a2627c368f24dab572b25c6f1df30ec
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "82583285"
---
# <a name="set-up-an-encryption-certificate-and-encrypt-secrets-on-windows-clusters"></a>Konfigurera ett krypterings certifikat och kryptera hemligheter på Windows-kluster
Den här artikeln visar hur du konfigurerar ett krypterings certifikat och använder det för att kryptera hemligheter i Windows-kluster. För Linux-kluster, se [Konfigurera ett krypterings certifikat och kryptera hemligheter på Linux-kluster.][secret-management-linux-specific-link]

[Azure Key Vault][key-vault-get-started] används här som en säker lagrings plats för certifikat och som ett sätt att hämta certifikat som är installerade på Service Fabric kluster i Azure. Om du inte distribuerar till Azure behöver du inte använda Key Vault för att hantera hemligheter i Service Fabric program. Att *använda* hemligheter i ett program är dock Cloud Platform-oberoende för att tillåta att program distribueras till ett kluster som finns var som helst. 

## <a name="obtain-a-data-encipherment-certificate"></a>Hämta ett certifikat för data kryptering
Ett certifikat för datachiffer används enbart för kryptering och dekryptering av [parametrar][parameters-link] i en tjänsts Settings.xml-och [miljövariabler][environment-variables-link] i en tjänsts ServiceManifest.xml. Den används inte för autentisering eller signering av cipher-text. Certifikatet måste uppfylla följande krav:

* Certifikatet måste innehålla en privat nyckel.
* Certifikatet måste skapas för nyckel utbyte, exporteras till en personal information Exchange-fil (. pfx).
* Användningen av certifikat nyckeln måste innehålla datachiffer (10) och ska inte omfatta serverautentisering eller klientautentisering. 
  
  Till exempel, när du skapar ett självsignerat certifikat med hjälp av PowerShell, `KeyUsage` måste flaggan anges till `DataEncipherment` :
  
  ```powershell
  New-SelfSignedCertificate -Type DocumentEncryptionCert -KeyUsage DataEncipherment -Subject mydataenciphermentcert -Provider 'Microsoft Enhanced Cryptographic Provider v1.0'
  ```

## <a name="install-the-certificate-in-your-cluster"></a>Installera certifikatet i klustret
Det här certifikatet måste installeras på varje nod i klustret. Se [hur du skapar ett kluster med hjälp av Azure Resource Manager][service-fabric-cluster-creation-via-arm] installations anvisningar. 

## <a name="encrypt-application-secrets"></a>Kryptera program hemligheter
Följande PowerShell-kommando används för att kryptera en hemlighet. Det här kommandot krypterar bara värdet. krypterings texten signeras **inte** . Du måste använda samma krypterings certifikat som är installerat i klustret för att skapa chiffertexten för hemliga värden:

```powershell
Invoke-ServiceFabricEncryptText -CertStore -CertThumbprint "<thumbprint>" -Text "mysecret" -StoreLocation CurrentUser -StoreName My
```

Den resulterande bas-64-kodade strängen innehåller både den hemliga chiffertexten och information om det certifikat som användes för att kryptera den.

## <a name="next-steps"></a>Nästa steg
Lär dig hur du [anger krypterade hemligheter i ett program.][secret-management-specify-encrypted-secrets-link]

<!-- Links -->
[key-vault-get-started]:../key-vault/general/overview.md
[service-fabric-cluster-creation-via-arm]: service-fabric-cluster-creation-via-arm.md
[parameters-link]:service-fabric-how-to-parameterize-configuration-files.md
[environment-variables-link]: service-fabric-how-to-specify-environment-variables.md
[secret-management-linux-specific-link]: service-fabric-application-secret-management-linux.md
[secret-management-specify-encrypted-secrets-link]: service-fabric-application-secret-management.md#specify-encrypted-secrets-in-an-application
