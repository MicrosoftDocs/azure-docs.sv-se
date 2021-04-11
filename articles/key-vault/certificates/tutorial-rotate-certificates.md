---
title: Självstudie – uppdatera certifikatets frekvens för automatisk rotation i Key Vault | Microsoft Docs
description: Självstudie som visar hur du uppdaterar ett certifikats frekvens för automatisk rotation i Azure Key Vault med hjälp av Azure Portal
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/16/2020
ms.author: sebansal
ms.openlocfilehash: ec648f02918717dad6b739aa6ae99206bcec1d6e
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/07/2021
ms.locfileid: "106581813"
---
# <a name="tutorial-configure-certificate-auto-rotation-in-key-vault"></a>Självstudie: Konfigurera automatisk rotation av certifikat i Key Vault

Du kan enkelt etablera, hantera och distribuera digitala certifikat med hjälp av Azure Key Vault. Certifikaten kan vara offentliga och privata Secure Sockets Layer (SSL)/Transport Layer Security (TLS) som signerats av en certifikat utfärdare (CA) eller ett självsignerat certifikat. Key Vault kan också begära och förnya certifikat via partnerskap med ca: er och tillhandahålla en robust lösning för hantering av certifikat livs cykeln. I den här självstudien får du uppdatera certifikatets giltighets period, frekvens för automatisk rotation och CA-attribut.

Självstudien visar hur du:

> [!div class="checklist"]
> * Hantera ett certifikat med hjälp av Azure Portal.
> * Lägg till ett konto för CA-providern.
> * Uppdatera certifikatets giltighets period.
> * Uppdatera certifikatets frekvens för automatisk rotation.
> * Uppdatera certifikatets attribut med hjälp av Azure PowerShell.

Läs [Key Vault grundläggande koncept](../general/basic-concepts.md)innan du börjar.

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på https://portal.azure.com.

## <a name="create-a-vault"></a>Skapa ett valv

Skapa en Azure Key Vault med hjälp av [Azure Portal](../general/quick-create-portal.md), [Azure CLI](../general/quick-create-cli.md)eller [Azure PowerShell](../general/quick-create-powershell.md). I exemplet är Key Vault **-namnet exempel-valv**.

![Utdata efter att nyckel valvet har skapats](../media/certificates/tutorial-import-cert/vault-properties.png)

## <a name="create-a-certificate-in-key-vault"></a>Skapa ett certifikat i Key Vault

Skapa ett certifikat eller importera ett certifikat till nyckel valvet (se [steg för att skapa ett certifikat i Key Vault](../secrets/quick-create-portal.md)). I det här fallet arbetar du med ett certifikat med namnet **ExampleCertificate**.

## <a name="update-certificate-lifecycle-attributes"></a>Uppdatera livs cykel attribut för certifikat

I Azure Key Vault kan du uppdatera ett certifikats livs cykel attribut både vid tidpunkten för att skapa certifikatet eller efter.

Ett certifikat som skapats i Key Vault kan vara:

- Ett självsignerat certifikat.
- Ett certifikat som har skapats med en certifikat utfärdare som är partner med Key Vault.
- Ett certifikat med en certifikat utfärdare som inte är partner med Key Vault.

Följande certifikat utfärdare är för närvarande partner leverantörer med Key Vault:

- DigiCert: Key Vault erbjuder TLS/SSL-certifikat för OV.
- GlobalSign: Key Vault erbjuder TLS/SSL-certifikat för OV.

Key Vault automatiskt roterar certifikat genom upprättade partnerskap med ca: er. Eftersom Key Vault automatiskt begär och förnyar certifikat via partnerskapet, är funktionen för automatisk rotation inte tillämplig för certifikat som skapats med ca: er som inte är partner med Key Vault.

> [!NOTE]
> En konto administratör för en CA-Provider skapar autentiseringsuppgifter som Key Vault använder för att skapa, förnya och använda TLS/SSL-certifikat.
![Certifikatutfärdare](../media/certificates/tutorial-rotate-cert/cert-authority-create.png)
>

### <a name="update-certificate-lifecycle-attributes-at-the-time-of-creation"></a>Uppdatera attribut för certifikat-livs cykel vid skapande tillfället

1. Välj **certifikat** på sidan Key Vault egenskaper.
1. Välj **generera/importera**.
1. På skärmen **skapa ett certifikat** uppdaterar du följande värden:

   - **Giltighets period**: Ange värdet (i månader). Att skapa kortsiktiga certifikat är en rekommenderad säkerhets rutin. Som standard är giltighets värdet för ett nyligen skapat certifikat 12 månader.
   - **Typ av livs längd**: Välj certifikatets automatiska förnyelse-och aviserings åtgärd och uppdatera sedan **livs längden för procent** eller **antal dagar innan det går ut**. Som standard anges ett certifikats automatiska förnyelse till 80 procent av sin livs längd. Välj något av följande alternativ på den nedrullningsbara menyn.

        |  Förnya automatiskt vid en specifik tidpunkt| Skicka e-post till alla kontakter vid en specifik tidpunkt |
        |-----------|------|
        |Om du väljer det här alternativet *aktive* ras autorotation. | Om du väljer det här alternativet roteras *inte* automatiskt, utan endast aviseringar skickas till dem.|
         You can learn about [setting up Email contact here](https://docs.microsoft.com/azure/key-vault/certificates/overview-renew-certificate#get-notified-about-certificate-expiration)
1. Välj **Skapa**.

![Certifikat livs cykel](../media/certificates/tutorial-rotate-cert/create-cert-lifecycle.png)

### <a name="update-lifecycle-attributes-of-a-stored-certificate"></a>Uppdatera livs cykel attribut för ett lagrat certifikat

1. Välj nyckel valvet.
1. Välj **certifikat** på sidan Key Vault egenskaper.
1. Välj det certifikat som du vill uppdatera. I det här fallet arbetar du med ett certifikat med namnet **ExampleCertificate**.
1. Välj **utgivnings princip** på den översta meny raden.

   ![Skärm bild som visar knappen för utgivnings princip.](../media/certificates/tutorial-rotate-cert/cert-issuance-policy.png)

1. På skärmen **utgivnings princip** uppdaterar du följande värden:

   - **Giltighets period**: uppdatera värdet (i månader).
   - **Typ av livs längd**: Välj certifikatets automatiska förnyelse-och aviserings åtgärd och uppdatera sedan **livs längden för procent** eller **antal dagar innan det går ut**.

   ![Certifikategenskaper](../media/certificates/tutorial-rotate-cert/cert-policy-change.png)

1. Välj **Spara**.

> [!IMPORTANT]
> Om du ändrar livs längds åtgärds typen för ett certifikat registreras ändringarna för de befintliga certifikaten omedelbart.


### <a name="update-certificate-attributes-by-using-powershell"></a>Uppdatera certifikatets attribut med hjälp av PowerShell

```azurepowershell


Set-AzureKeyVaultCertificatePolicy -VaultName $vaultName 
                                   -Name $certificateName 
                                   -RenewAtNumberOfDaysBeforeExpiry [276 or appropriate calculated value]
```

> [!TIP]
> Om du vill ändra förnyelse principen för en lista över certifikat, ange som `File.csv` innehåller `VaultName,CertName` som i följande exempel:
> <br/>
 `vault1,Cert1` <br/>
>  `vault2,Cert2`
>
>  ```azurepowershell
>  $file = Import-CSV C:\Users\myfolder\ReadCSVUsingPowershell\File.csv 
> foreach($line in $file)
> {
> Set-AzureKeyVaultCertificatePolicy -VaultName $vaultName -Name $certificateName -RenewAtNumberOfDaysBeforeExpiry [276 or appropriate calculated value]
> }
>  ```
> 
Mer information om parametrarna finns i AZ- [certifikat](/cli/azure/keyvault/certificate#az-keyvault-certificate-set-attributes)för nyckel valv.

## <a name="clean-up-resources"></a>Rensa resurser

Andra Key Vault själv studie kurser som bygger på den här självstudien. Om du planerar att arbeta med de här självstudierna kanske du vill lämna dessa befintliga resurser på plats.
När du inte längre behöver dem tar du bort resurs gruppen, som tar bort nyckel valvet och relaterade resurser.

Ta bort resurs gruppen med hjälp av portalen:

1. Ange namnet på din resurs grupp i rutan **Sök** högst upp i portalen. När resurs gruppen som används i den här snabb starten visas i Sök resultaten väljer du den.
1. Välj **Ta bort resursgrupp**.
1. Skriv namnet på resurs gruppen i rutan **Skriv resurs gruppens namn:** och välj sedan **ta bort**.


## <a name="next-steps"></a>Nästa steg

I den här självstudien har du uppdaterat ett certifikats livs cykel attribut. Om du vill veta mer om Key Vault och hur du integrerar den med dina program kan du fortsätta till följande artiklar:

- Läs mer om att [Hantera certifikat skapande i Azure Key Vault](./create-certificate-scenarios.md).
- Granska [Key Vault översikt](../general/overview.md).
