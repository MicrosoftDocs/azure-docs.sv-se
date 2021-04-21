---
title: Självstudie – Uppdatera frekvensen för automatisk rotation av certifikat i Key Vault | Microsoft Docs
description: Självstudie som visar hur du uppdaterar ett certifikats automatiska rotationsfrekvens i Azure Key Vault med hjälp av Azure Portal
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/16/2020
ms.author: sebansal
ms.openlocfilehash: 48a531e054bf92c8ddc7761689b8fdf1df8a28a7
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107750022"
---
# <a name="tutorial-configure-certificate-auto-rotation-in-key-vault"></a>Självstudie: Konfigurera automatisk rotering av certifikat i Key Vault

Du kan enkelt etablera, hantera och distribuera digitala certifikat med hjälp av Azure Key Vault. Certifikaten kan vara offentliga och privata Secure Sockets Layer(SSL)/Transport Layer Security-certifikat (TLS) som signerats av en certifikatutfärdare (CA) eller ett själv signerat certifikat. Key Vault också begära och förnya certifikat via partnerskap med certifikatutfärdare, vilket ger en robust lösning för livscykelhantering av certifikat. I den här självstudien uppdaterar du certifikatets giltighetsperiod, automatisk rotationsfrekvens och CA-attribut.

Självstudien visar hur du:

> [!div class="checklist"]
> * Hantera ett certifikat med hjälp av Azure Portal.
> * Lägg till ett CA-providerkonto.
> * Uppdatera certifikatets giltighetsperiod.
> * Uppdatera certifikatets frekvens för automatisk rotation.
> * Uppdatera certifikatets attribut med hjälp av Azure PowerShell.

Innan du börjar bör du läsa [Key Vault grundläggande begreppen](../general/basic-concepts.md).

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på https://portal.azure.com.

## <a name="create-a-vault"></a>Skapa ett valv

Skapa en Azure Key Vault med [Azure Portal,](../general/quick-create-portal.md) [Azure CLI](../general/quick-create-cli.md)eller [Azure PowerShell](../general/quick-create-powershell.md). I det här exemplet är nyckelvalvsnamnet **Example-Vault**.

![Utdata när nyckelvalvet har skapats](../media/certificates/tutorial-import-cert/vault-properties.png)

## <a name="create-a-certificate-in-key-vault"></a>Skapa ett certifikat i Key Vault

Skapa ett certifikat eller importera ett certifikat till nyckelvalvet (se [Steg för att skapa ett certifikat i Key Vault](../secrets/quick-create-portal.md)). I det här fallet arbetar du med ett certifikat som heter **ExampleCertificate**.

## <a name="update-certificate-lifecycle-attributes"></a>Uppdatera certifikatlivscykelattribut

I Azure Key Vault kan du uppdatera ett certifikats livscykelattribut både när certifikatet skapas eller efter.

Ett certifikat som skapas i Key Vault kan vara:

- Ett själv signerat certifikat.
- Ett certifikat som skapats med en certifikatutfärdare som är partner med Key Vault.
- Ett certifikat med en certifikatutfärdare som inte är partner med Key Vault.

Följande CA:er är för närvarande partnerproviders med Key Vault:

- DigiCert: Key Vault erbjuder OV TLS/SSL-certifikat.
- GlobalSign: Key Vault erbjuder OV TLS/SSL-certifikat.

Key Vault automatiskt certifikat genom etablerade samarbeten med certifikat återkallade certifikat. Eftersom Key Vault begär och förnyar certifikat automatiskt via partnerskap, gäller inte funktionen för automatisk rotation för certifikat som skapas med certifikat som inte är partner med Key Vault.

> [!NOTE]
> En kontoadministratör för en CA-provider skapar autentiseringsuppgifter som Key Vault använder för att skapa, förnya och använda TLS/SSL-certifikat.
![Certifikatutfärdare](../media/certificates/tutorial-rotate-cert/cert-authority-create.png)
>

### <a name="update-certificate-lifecycle-attributes-at-the-time-of-creation"></a>Uppdatera certifikatets livscykelattribut när det skapas

1. På sidan Key Vault egenskaper väljer du **Certifikat**.
1. Välj **Generera/importera**.
1. På skärmen **Skapa ett** certifikat uppdaterar du följande värden:

   - **Giltighetsperiod:** Ange värdet (i månader). Att skapa kortvariga certifikat är en rekommenderad säkerhetspraxis. Som standard är giltighetsvärdet för ett nyligen skapat certifikat 12 månader.
   - **Typ av livslängdsåtgärd:** Välj certifikatets automatiska förnyelse och  aviseringsåtgärd och uppdatera sedan procentandelen livslängd eller **Antal dagar innan det upphör att gälla.** Som standard anges ett certifikats automatiska förnyelse till 80 procent av dess livslängd. Välj något av följande alternativ på den nedrullningsna menyn.

      |  Förnya automatiskt vid en viss tidpunkt| Skicka e-post till alla kontakter vid en viss tidpunkt |
      |-----------|------|
      |Om du väljer det *här alternativet aktiverar* du automatiskrotation. | Om du väljer det *här alternativet* roteras inte automatiskt, utan avisering kommer endast att meddela kontakter.|
      
      Du kan läsa mer om att [konfigurera e-postkontakt här](https://docs.microsoft.com/azure/key-vault/certificates/overview-renew-certificate#get-notified-about-certificate-expiration)

1. Välj **Skapa**.

![Certifikatets livscykel](../media/certificates/tutorial-rotate-cert/create-cert-lifecycle.png)

### <a name="update-lifecycle-attributes-of-a-stored-certificate"></a>Uppdatera livscykelattribut för ett lagrat certifikat

1. Välj nyckelvalvet.
1. På Key Vault egenskaper väljer du **Certifikat**.
1. Välj det certifikat som du vill uppdatera. I det här fallet arbetar du med ett certifikat som heter **ExampleCertificate**.
1. Välj **Utfärdandeprincip** på den översta menyraden.

   ![Skärmbild som visar knappen Utfärdandeprincip.](../media/certificates/tutorial-rotate-cert/cert-issuance-policy.png)

1. Uppdatera **följande värden på** skärmen Utfärdandeprincip:

   - **Giltighetsperiod:** Uppdatera värdet (i månader).
   - **Typ av livslängdsåtgärd:** Välj certifikatets åtgärd för automatisk  förnyelse och avisering och uppdatera sedan den procentuella livslängden eller Antalet **dagar innan det upphör att gälla.**

   ![Certifikategenskaper](../media/certificates/tutorial-rotate-cert/cert-policy-change.png)

1. Välj **Spara**.

> [!IMPORTANT]
> Om du ändrar åtgärdstyp för livslängd för ett certifikat kommer ändringar för befintliga certifikat att registreras omedelbart.


### <a name="update-certificate-attributes-by-using-powershell"></a>Uppdatera certifikatattribut med hjälp av PowerShell

```azurepowershell


Set-AzureKeyVaultCertificatePolicy -VaultName $vaultName 
                                   -Name $certificateName 
                                   -RenewAtNumberOfDaysBeforeExpiry [276 or appropriate calculated value]
```

> [!TIP]
> Om du vill ändra förnyelseprincipen för en lista med certifikat anger du `File.csv` innehåller som i följande `VaultName,CertName` exempel:
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
Mer information om parametrarna finns i [az keyvault certificate](/cli/azure/keyvault/certificate#az-keyvault-certificate-set-attributes).

## <a name="clean-up-resources"></a>Rensa resurser

Andra Key Vault självstudier bygger på den här självstudien. Om du planerar att arbeta med de här självstudierna kanske du vill lämna de befintliga resurserna på plats.
När du inte längre behöver dem tar du bort resursgruppen, vilket tar bort nyckelvalvet och relaterade resurser.

Så här tar du bort resursgruppen med hjälp av portalen:

1. Ange namnet på resursgruppen i **sökrutan** överst i portalen. När resursgruppen som används i den här snabbstarten visas i sökresultatet väljer du den.
1. Välj **Ta bort resursgrupp**.
1. I rutan **SKRIV RESURSGRUPPENS NAMN:** skriver du namnet på resursgruppen och väljer sedan Ta **bort.**


## <a name="next-steps"></a>Nästa steg

I den här självstudien har du uppdaterat ett certifikats livscykelattribut. Om du vill veta Key Vault och hur du integrerar det med dina program kan du fortsätta med följande artiklar:

- Läs mer om [att hantera skapande av certifikat i Azure Key Vault](./create-certificate-scenarios.md).
- Granska [Key Vault översikt.](../general/overview.md)
