---
title: Azure-snabbstart – Konfigurera och hämta ett certifikat från Key Vault med Azure Portal | Microsoft Docs
description: Snabbstart som visar hur du ställer in och hämtar ett certifikat Azure Key Vault med hjälp av Azure Portal
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/24/2020
ms.author: mbaldwin
ms.openlocfilehash: b7a3126aef94ccdef7f6c9120ddeb6f20d872ab5
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107815782"
---
# <a name="quickstart-set-and-retrieve-a-certificate-from-azure-key-vault-using-the-azure-portal"></a>Snabbstart: Ange och hämta ett certifikat från Azure Key Vault med hjälp av Azure Portal

Azure Key Vault är en molntjänst som tillhandahåller ett säkert lager för hemligheter. Du kan på ett säkert sätt lagra nycklar, lösenord, certifikat och andra hemligheter. Du kan skapa och hantera Azure-nyckelvalv via Azure Portal. I den här snabbstarten skapar du ett nyckelvalv och använder det sedan för att lagra ett certifikat. Mer information om nyckelvalv finns i [översikten](../general/overview.md).

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på https://portal.azure.com.

## <a name="create-a-vault"></a>Skapa ett valv

1. På Azure Portal menyn eller på sidan **Start** väljer du **Skapa en resurs**.
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

* **Valvnamn:** I det här exemplet är det **Example-Vault**. Du kommer att använda det här namnet i senare steg.
* **Valvets URI**: I det här exemplet är det `https://example-vault.vault.azure.net/`. Program som använder ditt valv via dess REST-API måste använda denna URI.

Nu är ditt Azure-konto det enda kontot med behörighet att utföra åtgärder i det nya valvet.

![Utdata när nyckelvalvet har skapats](../media/certificates/quick-create-portal/vault-properties.png)

## <a name="add-a-certificate-to-key-vault"></a>Lägga till ett certifikat i Key Vault

Om du vill lägga till ett certifikat i valvet behöver du bara vidta några ytterligare steg. I det här fallet lägger vi till ett själv signerat certifikat som kan användas av ett program. Certifikatet kallas **ExampleCertificate**.

1. På sidan Key Vault egenskaper väljer du **Certifikat**.
2. Klicka på **Generera/importera**.
3. På skärmen **Skapa ett** certifikat väljer du följande värden:
    - **Metod för att skapa certifikat:** Generera.
    - **Certifikatnamn:** ExampleCertificate.
    - **Ämne:** CN=ExampleDomain
    - Lämna standardvärdena för de andra alternativen. (Om du inte anger något särskilt i Avancerad princip kan det som standard användas som ett klientauthcertifikat.)
 4. Klicka på **Skapa**.

När du får meddelandet om att certifikatet har skapats kan du klicka på det i listan. Du ser då vissa av egenskaperna. Om du klickar på den aktuella versionen så kan du se värdet du angav i föregående steg.

![Certifikategenskaper](../media/certificates/quick-create-portal/current-version-hidden.png)

## <a name="export-certificate-from-key-vault"></a>Exportera certifikat från Key Vault
Genom att klicka på knappen "Ladda ned i CER-format" eller "Ladda ned i PFX/PEM-format" kan du ladda ned certifikatet. 

![Nedladdning av certifikat](../media/certificates/quick-create-portal/current-version-shown.png)

## <a name="clean-up-resources"></a>Rensa resurser

Andra snabbstarter och självstudier om Key Vault bygger på den här snabbstarten. Om du planerar att fortsätta med efterföljande snabbstarter och självstudier kan du lämna kvar de här resurserna.
När du inte behöver resursgruppen längre så tar du bort den. Då tas även nyckelvalvet och relaterade resurser bort. Så här tar du bort resursgruppen via portalen:

1. Skriv namnet på resursgruppen i rutan Sök längst upp i portalen. När du ser resursgruppen du använde i den här snabbstarten bland sökresultaten väljer du den.
2. Välj **Ta bort resursgrupp**.
3. I rutan **SKRIV RESURSGRUPPSNAMNET:** skriver du namnet på resursgruppen och väljer **Ta bort**.


## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du skapat Key Vault och lagrat ett certifikat i den. Mer information om Key Vault och hur du integrerar det med dina program finns i artiklarna nedan.

- Läs en [översikt över Azure Key Vault](../general/overview.md)
- Se [Azure Key Vault utvecklarhandbok](../general/developers-guide.md)
- Granska Key Vault [säkerhetsöversikten](../general/security-features.md)
