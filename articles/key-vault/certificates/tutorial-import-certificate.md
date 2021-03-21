---
title: Självstudie – importera ett certifikat i Key Vault med Azure Portal | Microsoft Docs
description: Självstudie som visar hur du importerar ett certifikat i Azure Key Vault
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: tutorial
ms.custom: mvc, devx-track-azurecli
ms.date: 04/16/2020
ms.author: sebansal
ms.openlocfilehash: b7b15c1c4952f6e0a606b1ed46f1588c536b4ae2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102204090"
---
# <a name="tutorial-import-a-certificate-in-azure-key-vault"></a>Självstudie: importera ett certifikat i Azure Key Vault

Azure Key Vault är en molntjänst som tillhandahåller ett säkert lager för hemligheter. Du kan på ett säkert sätt lagra nycklar, lösenord, certifikat och andra hemligheter. Du kan skapa och hantera Azure-nyckelvalv via Azure Portal. I den här självstudien skapar du ett nyckel valv och använder det sedan för att importera ett certifikat. Mer information om nyckelvalv finns i [översikten](../general/overview.md).

Självstudien visar hur du:

> [!div class="checklist"]
> * Skapa ett nyckelvalv.
> * Importera ett certifikat i Key Vault med hjälp av portalen.
> * Importera ett certifikat i Key Vault med CLI.
> * Importera ett certifikat i Key Vault med PowerShell.


Läs [Key Vault grundläggande koncept](../general/basic-concepts.md)innan du börjar. 

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på https://portal.azure.com.

## <a name="create-a-vault"></a>Skapa ett valv

1. På Azure Portal-menyn eller på **Start** sidan väljer du **skapa en resurs**.
2. I rutan Sök anger du **Key Vault**.
3. Välj **Key Vault** i listan med resultat.
4. Välj **Skapa** i avsnittet Key Vault.
5. Ange följande information i avsnittet **Skapa nyckelvalv** avsnittet Ange följande information:
    - **Namn**: Ett unikt namn krävs. I den här snabb starten använder vi **exempel-valv**. 
    - **Prenumeration**: Välj en prenumeration.
    - Under **resurs grupp** väljer du **Skapa ny** och anger ett resurs grupp namn.
    - Välj en plats i listrutan **Plats**.
    - Lämna standardvärdena för de andra alternativen.
6. När du har angett den här informationen väljer du **Skapa**.

Anteckna de två egenskaperna som visas nedan:

* **Valv namn**: i exemplet är detta **exempel-valv**. Du kommer att använda det här namnet i senare steg.
* **Valvets URI**: I det här exemplet är det https://example-vault.vault.azure.net/. Program som använder ditt valv via dess REST-API måste använda denna URI.

Nu är ditt Azure-konto det enda kontot med behörighet att utföra åtgärder i det nya valvet.

![Utdata när nyckelvalvet har skapats](../media/certificates/tutorial-import-cert/vault-properties.png)

## <a name="import-a-certificate-to-key-vault"></a>Importera ett certifikat till Key Vault

Om du vill importera ett certifikat till valvet måste du ha en PEM-eller PFX-certifikatfil på disken. I det här fallet ska vi importera ett certifikat med fil namnet **ExampleCertificate**.

> [!IMPORTANT]
> I Azure Key Vault är PFX och PEM de format som stöds. 
> - . pem-filformat innehåller en eller flera X509-certifikatfiler.
> - PFX-filformat är ett Arkiv fil format för lagring av flera kryptografiska objekt i en enda fil, t. ex. Server certifikat (utfärdat för din domän), en matchande privat nyckel och eventuellt även en mellanliggande certifikat utfärdare.  

1. Välj **certifikat** på sidan Key Vault egenskaper.
2. Klicka på **Generera/importera**.
3. På skärmen **skapa ett certifikat** väljer du följande värden:
    - **Metod för att skapa certifikat**: importera.
    - **Certifikat namn**: ExampleCertificate.
    - **Ladda upp certifikat fil**: Välj certifikat filen från disk
    - **Lösen ord** : om du överför en lösenordsskyddad certifikat fil anger du detta lösen ord här. Annars lämnar du det tomt. När certifikat filen har importer ATS tar Key Vault bort lösen ordet.
4. Klicka på **Skapa**.

![Certifikategenskaper](../media/certificates/tutorial-import-cert/cert-import.png)

Genom att lägga till ett certifikat med **import** metoden fyller Azure Key Vault automatiskt certifikat parametrar (t. ex. giltighets period, utfärdarens namn, aktiverings datum osv.).

När du har fått ett meddelande om att certifikatet har importer ATS kan du klicka på det i listan för att visa dess egenskaper. 

![Skärm bild som visar var du kan se certifikat egenskaperna.](../media/certificates/tutorial-import-cert/current-version-hidden.png)

## <a name="import-a-certificate-using-azure-cli"></a>Importera ett certifikat med Azure CLI

Importera ett certifikat till ett angivet nyckel valv. Om du vill importera ett befintligt giltigt certifikat som innehåller en privat nyckel till Azure Key Vault, kan filen som ska importeras vara antingen i PFX-eller PEM-format. Om certifikatet är i PEM-format måste PEM-filen innehålla nyckeln och x509-certifikat. Den här åtgärden kräver certifikat-/importbehörighet.

```azurecli
az keyvault certificate import --file
                               --name
                               --vault-name
                               [--disabled {false, true}]
                               [--only-show-errors]
                               [--password]
                               [--policy]
                               [--subscription]
                               [--tags]
```

Läs mer om [parametrarna](/cli/azure/keyvault/certificate#az-keyvault-certificate-import).

När du har importerat certifikatet kan du Visa certifikatet med [certifikat visning](/cli/azure/keyvault/certificate#az-keyvault-certificate-show)


```azurecli
az keyvault certificate show [--id]
                             [--name]
                             [--only-show-errors]
                             [--subscription]
                             [--vault-name]
                             [--version]
```

Nu har du skapat ett nyckel valv, importerat ett certifikat och visat certifikat egenskaper.

## <a name="import-a-certificate-using-azure-powershell"></a>Importera ett certifikat med Azure PowerShell

```
Import-AzureKeyVaultCertificate
      [-VaultName] <String>
      [-Name] <String>
      -FilePath <String>
      [-Password <SecureString>]
      [-Tag <Hashtable>]
      [-DefaultProfile <IAzureContextContainer>]
      [-WhatIf]
      [-Confirm]
      [<CommonParameters>]
```

Läs mer om [parametrarna](/powershell/module/azurerm.keyvault/import-azurekeyvaultcertificate?view=azurermps-6.13.0).


## <a name="clean-up-resources"></a>Rensa resurser

Andra snabbstarter och självstudier om Key Vault bygger på den här snabbstarten. Om du planerar att fortsätta med efterföljande snabbstarter och självstudier kan du lämna kvar de här resurserna.
När du inte behöver resursgruppen längre så tar du bort den. Då tas även nyckelvalvet och relaterade resurser bort. Så här tar du bort resursgruppen via portalen:

1. Skriv namnet på resursgruppen i rutan Sök längst upp i portalen. När du ser resursgruppen du använde i den här snabbstarten bland sökresultaten väljer du den.
2. Välj **Ta bort resursgrupp**.
3. I rutan **SKRIV RESURSGRUPPSNAMNET:** skriver du namnet på resursgruppen och väljer **Ta bort**.


## <a name="next-steps"></a>Nästa steg

I den här självstudien har du skapat en Key Vault och importerat ett certifikat i det. Om du vill veta mer om Key Vault och hur du integrerar den med dina program, Fortsätt till artiklarna nedan.

- Läs mer om att [Hantera certifikat skapande i Azure Key Vault](./create-certificate-scenarios.md)
- Se exempel på hur du [importerar certifikat med hjälp av REST API: er](/rest/api/keyvault/importcertificate/importcertificate)
- Granska [Key Vault säkerhets översikt](../general/security-overview.md)