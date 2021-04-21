---
title: Självstudie – Importera ett certifikat i Key Vault med Azure Portal | Microsoft Docs
description: Självstudie som visar hur du importerar ett certifikat i Azure Key Vault
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: tutorial
ms.custom: mvc, devx-track-azurecli
ms.date: 04/16/2020
ms.author: sebansal
ms.openlocfilehash: aebb0fa5da4cef655acc424c5ac90b628a4ba3af
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107814450"
---
# <a name="tutorial-import-a-certificate-in-azure-key-vault"></a>Självstudie: Importera ett certifikat i Azure Key Vault

Azure Key Vault är en molntjänst som tillhandahåller ett säkert lager för hemligheter. Du kan på ett säkert sätt lagra nycklar, lösenord, certifikat och andra hemligheter. Du kan skapa och hantera Azure-nyckelvalv via Azure Portal. I den här självstudien skapar du ett nyckelvalv och använder det sedan för att importera ett certifikat. Mer information om nyckelvalv finns i [översikten](../general/overview.md).

Självstudien visar hur du:

> [!div class="checklist"]
> * Skapa ett nyckelvalv.
> * Importera ett certifikat i Key Vault med hjälp av portalen.
> * Importera ett certifikat i Key Vault med hjälp av CLI.
> * Importera ett certifikat i Key Vault powershell.


Innan du börjar bör du läsa [Key Vault grundläggande begreppen](../general/basic-concepts.md). 

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på https://portal.azure.com.

## <a name="create-a-vault"></a>Skapa ett valv

1. På Azure Portal menyn eller på **sidan Start** väljer du Skapa **en resurs**.
2. I rutan Sök anger du **Key Vault**.
3. Välj **Key Vault** i listan med resultat.
4. Välj **Skapa** i avsnittet Key Vault.
5. Ange följande information i avsnittet **Skapa nyckelvalv** avsnittet Ange följande information:
    - **Namn**: Ett unikt namn krävs. I den här snabbstarten använder vi **Example-Vault**. 
    - **Prenumeration**: Välj en prenumeration.
    - Under **Resursgrupp** väljer du **Skapa ny** och anger ett resursgruppsnamn.
    - Välj en plats i listrutan **Plats**.
    - Lämna standardvärdena för de andra alternativen.
6. När du har angett den här informationen väljer du **Skapa**.

Anteckna de två egenskaperna som visas nedan:

* **Valvnamn:** I det här exemplet är detta **Example-Vault**. Du kommer att använda det här namnet i senare steg.
* **Valvets URI**: I det här exemplet är det https://example-vault.vault.azure.net/. Program som använder ditt valv via dess REST-API måste använda denna URI.

Nu är ditt Azure-konto det enda kontot med behörighet att utföra åtgärder i det nya valvet.

![Utdata när nyckelvalvet har skapats](../media/certificates/tutorial-import-cert/vault-properties.png)

## <a name="import-a-certificate-to-key-vault"></a>Importera ett certifikat till Key Vault

Om du vill importera ett certifikat till valvet måste du ha en PEM- eller PFX-certifikatfil som ska finnas på disken. I det här fallet importerar vi ett certifikat med filnamnet **ExampleCertificate**.

> [!IMPORTANT]
> I Azure Key Vault är PFX och PEM de format som stöds. 
> - .pem-filformatet innehåller en eller flera X509-certifikatfiler.
> - .pfx-filformatet är ett arkivfilformat för lagring av flera kryptografiska objekt i en enda fil, t.ex. servercertifikat (utfärdat för din domän), en matchande privat nyckel och kan eventuellt innehålla en mellanliggande certifikatutfärdare.  

1. På Key Vault egenskaper väljer du **Certifikat**.
2. Klicka på **Generera/importera**.
3. På skärmen **Skapa ett** certifikat väljer du följande värden:
    - **Metod för att skapa certifikat:** Importera.
    - **Certifikatnamn:** ExampleCertificate.
    - **Ladda upp certifikatfil:** välj certifikatfilen från disken
    - **Lösenord:** Ange lösenordet här om du laddar upp en lösenordsskyddad certifikatfil. Annars lämnar du det tomt. När certifikatfilen har importerats tar Key Vault bort lösenordet.
4. Klicka på **Skapa**.

![Certifikategenskaper](../media/certificates/tutorial-import-cert/cert-import.png)

Genom att lägga till ett certifikat med **importmetoden** fyller Azure Key Vault automatiskt i certifikatparametrar (t.ex. giltighetsperiod, utfärdarnamn, aktiveringsdatum osv.).

När du får meddelandet om att certifikatet har importerats kan du klicka på det i listan för att visa dess egenskaper. 

![Skärmbild som visar var du kan visa certifikategenskaperna.](../media/certificates/tutorial-import-cert/current-version-hidden.png)

## <a name="import-a-certificate-using-azure-cli"></a>Importera ett certifikat med Azure CLI

Importera ett certifikat till ett angivet nyckelvalv. Om du vill importera ett befintligt giltigt certifikat som innehåller en privat nyckel till Azure Key Vault kan filen som ska importeras vara i PFX- eller PEM-format. Om certifikatet är i PEM-format måste PEM-filen innehålla nyckeln samt x509-certifikat. Den här åtgärden kräver certifikat-/importbehörighet.

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

Läs mer om [parametrarna](/cli/azure/keyvault/certificate#az_keyvault_certificate_import).

När du har importerat certifikatet kan du visa certifikatet med hjälp [av Certificate show](/cli/azure/keyvault/certificate#az_keyvault_certificate_show)


```azurecli
az keyvault certificate show [--id]
                             [--name]
                             [--only-show-errors]
                             [--subscription]
                             [--vault-name]
                             [--version]
```

Nu har du skapat ett nyckelvalv, importerat ett certifikat och visat certifikatets egenskaper.

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

Läs mer om [parametrarna](/powershell/module/azurerm.keyvault/import-azurekeyvaultcertificate?).


## <a name="clean-up-resources"></a>Rensa resurser

Andra snabbstarter och självstudier om Key Vault bygger på den här snabbstarten. Om du planerar att fortsätta med efterföljande snabbstarter och självstudier kan du lämna kvar de här resurserna.
När du inte behöver resursgruppen längre så tar du bort den. Då tas även nyckelvalvet och relaterade resurser bort. Så här tar du bort resursgruppen via portalen:

1. Skriv namnet på resursgruppen i rutan Sök längst upp i portalen. När du ser resursgruppen du använde i den här snabbstarten bland sökresultaten väljer du den.
2. Välj **Ta bort resursgrupp**.
3. I rutan **SKRIV RESURSGRUPPSNAMNET:** skriver du namnet på resursgruppen och väljer **Ta bort**.


## <a name="next-steps"></a>Nästa steg

I den här självstudien har du skapat Key Vault och importerat ett certifikat i den. Mer information om Key Vault och hur du integrerar det med dina program finns i artiklarna nedan.

- Läs mer om [att hantera skapande av certifikat i Azure Key Vault](./create-certificate-scenarios.md)
- Se exempel på import [av certifikat med hjälp av REST API:er](/rest/api/keyvault/importcertificate/importcertificate)
- Granska [Key Vault säkerhetsöversikten](../general/security-features.md)