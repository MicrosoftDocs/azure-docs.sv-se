---
title: Azure-snabbstart – Ställ in och hämta en hemlighet från Key Vault med hjälp av Azure-portalen | Microsoft Docs
description: Snabbstart som visar hur du ställer in och hämtar en hemlighet från Azure Key Vault med hjälp av Azure-portalen
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: mvc
ms.date: 09/03/2019
ms.author: mbaldwin
ms.openlocfilehash: 904c00c26171854a32234d7b6ba5cac81ac43e5c
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/06/2021
ms.locfileid: "97936676"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-using-the-azure-portal"></a>Snabbstart: Ställ in och hämta en hemlighet från Azure Key Vault med hjälp av Azure-portalen

Azure Key Vault är en molntjänst som tillhandahåller ett säkert lager för hemligheter. Du kan på ett säkert sätt lagra nycklar, lösenord, certifikat och andra hemligheter. Du kan skapa och hantera Azure-nyckelvalv via Azure Portal. I den här snabbstarten skapar du ett nyckelvalv och använder sedan det för att lagra en hemlighet. 

Mer information om finns i 
- [Översikt över Key Vault](../general/overview.md)
- [Översikt över hemligheter](about-secrets.md).

## <a name="prerequisites"></a>Krav

Du behöver en Azure-prenumeration för att få åtkomst till Azure Key Vault. Om du inte redan har en prenumeration kan du skapa ett [kostnads fritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

All åtkomst till hemligheter sker via Azure Key Vault. I den här snabb starten skapar du ett nyckel valv med hjälp av [Azure Portal](../general/quick-create-portal.md), [Azure CLI](../general/quick-create-cli.md)eller [Azure PowerShell](../general/quick-create-powershell.md).

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på https://portal.azure.com.

## <a name="add-a-secret-to-key-vault"></a>Lägga till en hemlighet i Key Vault

Följ stegen nedan om du vill lägga till en hemlighet i valvet:

1. Navigera till ditt nya nyckel valv i Azure Portal
1. På sidan Key Vault inställningar väljer du **hemligheter**.
1. Klicka på **Generera/importera**.
1. Välj följande värden på skärmen **Skapa en hemlighet**:
    - **Uppladdningsalternativ**: Manuell.
    - **Namn**: Ange ett namn för hemligheten. Det hemliga namnet måste vara unikt inom en Key Vault. Namnet måste vara en 1-127-tecken sträng, med början på en bokstav och endast innehålla 0-9, a-z, A-Z och-. Mer information om namngivning finns i [Key Vault objekt, identifierare och versions hantering](../general/about-keys-secrets-certificates.md#objects-identifiers-and-versioning)
    - **Värde**: Ange ett värde för hemligheten. Key Vault API: er accepterar och returnerar hemliga värden som strängar. 
    - Lämna standardvärdena för de andra alternativen. Klicka på **Skapa**.

När du får ett meddelande om att hemligheten har skapats kan du klicka på den i listan. 

Mer information om hemligheter finns i [om Azure Key Vault hemligheter](./about-secrets.md)

## <a name="retrieve-a-secret-from-key-vault"></a>Hämta en hemlighet från Key Vault

Om du klickar på den aktuella versionen så kan du se värdet du angav i föregående steg.

![Egenskaper för hemlighet](../media/quick-create-portal/current-version-hidden.png)

Genom att klicka på knappen "Visa hemligt värde" i den högra rutan kan du se det dolda värdet. 

![Hemligt värde visades](../media/quick-create-portal/current-version-shown.png)

Du kan också använda [Azure CLI]()eller [Azure PowerShell]() för att hämta tidigare skapade hemligheter.

## <a name="clean-up-resources"></a>Rensa resurser

Andra snabbstarter och självstudier om Key Vault bygger på den här snabbstarten. Om du planerar att fortsätta med efterföljande snabbstarter och självstudier kan du lämna kvar de här resurserna.
När du inte behöver resursgruppen längre så tar du bort den. Då tas även nyckelvalvet och relaterade resurser bort. Så här tar du bort resursgruppen via portalen:

1. Skriv namnet på resursgruppen i rutan Sök längst upp i portalen. När du ser resursgruppen du använde i den här snabbstarten bland sökresultaten väljer du den.
2. Välj **Ta bort resursgrupp**.
3. I rutan **SKRIV RESURSGRUPPSNAMNET:** skriver du namnet på resursgruppen och väljer **Ta bort**.

> [!NOTE]
> Det är viktigt att Observera att när en hemlighet, nyckel, certifikat eller nyckel valv har tagits bort, fortsätter den att kunna återkrävas under en konfigurerbar period på 7 till 90 Kalender dagar. Om ingen konfiguration anges anges standard återställnings perioden till 90 dagar. Detta ger användare tillräckligt med tid för att uppmärksamma en oavsiktlig hemlig borttagning och svara. Mer information om hur du tar bort och återställer nyckel valv och Key Vault-objekt finns i [Azure Key Vault översikt över mjuk borttagning](../general/soft-delete-overview.md)

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du skapat en Key Vault och lagrat en hemlighet i den. Om du vill veta mer om Key Vault och hur du integrerar den med dina program, Fortsätt till artiklarna nedan.

- Läs en [Översikt över Azure Key Vault](../general/overview.md)
- Läs [säker åtkomst till en Key Vault](../general/secure-your-key-vault.md)
- Se [använda Key Vault med App Service-webbapp](../general/tutorial-net-create-vault-azure-web-app.md)
- Se [använda Key Vault med program distribuerad till virtuell dator](../general/tutorial-net-virtual-machine.md)
- Se [Azure Key Vault Developer ' s guide](../general/developers-guide.md)
- Granska [Key Vault säkerhets översikt](../general/security-overview.md)