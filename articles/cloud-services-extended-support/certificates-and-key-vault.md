---
title: Lagra och använda certifikat i Azure Cloud Services (utökad support)
description: Processer för att lagra och använda certifikat i Azure Cloud Services (utökad support)
ms.topic: how-to
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: 4d771e77fcca05b090e5d47d70ae93ece8f79e3e
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2021
ms.locfileid: "104865711"
---
# <a name="use-certificates-with-azure-cloud-services-extended-support"></a>Använda certifikat med Azure Cloud Services (utökad support)

Key Vault används för att lagra certifikat som är kopplade till Cloud Services (utökad support). Nyckel valv kan skapas via [Azure Portal](../key-vault/general/quick-create-portal.md) och [PowerShell](../key-vault/general/quick-create-powershell.md). Lägg till certifikaten i Key Vault och referera sedan till certifikatets tumavtrycken i tjänst konfigurations filen. Du måste också aktivera Key Vault för lämpliga behörigheter så att Cloud Services (utökad support) resurs kan hämta certifikat som lagras som hemligheter från Key Vault.  

## <a name="upload-a-certificate-to-key-vault"></a>Ladda upp ett certifikat till Key Vault 

1.  Logga in på Azure Portal och navigera till Key Vault. Om du inte har en Key Vault konfigurerad kan du välja att skapa en i samma fönster.

2. Välj **åtkomst principer**

    :::image type="content" source="media/certs-and-key-vault-1.png" alt-text="Bilden visar hur du väljer åtkomst principer från bladet Key Vault.":::

3. Se till att åtkomst principerna innehåller följande egenskap:
    - **Aktivera åtkomst till Azure-Virtual Machines för distribution**

    :::image type="content" source="media/certs-and-key-vault-2.png" alt-text="Bild som visar fönstret åtkomst principer i Azure Portal.":::
 
4.  Välj **certifikat** 

    :::image type="content" source="media/certs-and-key-vault-3.png" alt-text="Bild visar hur du väljer alternativet certifikat i fönstret för blad principer för nyckel valv i Azure Portal.":::

5. Välj **generera/importera**

    :::image type="content" source="media/certs-and-key-vault-4.png" alt-text="Bild visar hur du väljer alternativet för att generera/importera":::

4.  Slutför den information som krävs för att slutföra överföringen av certifikatet. Certifikatet måste finnas i **. PFX** -format.

    :::image type="content" source="media/certs-and-key-vault-5.png" alt-text="Bild som visar hur du importerar fönster i Azure Portal.":::

5.  Lägg till certifikat informationen i rollen i tjänst konfigurations filen (. cscfg). Se till att certifikatets tumavtryck i Azure Portal matchar tumavtrycket i tjänst konfigurations filen (. cscfg). 
    
    ```json
    <Certificate name="<your cert name>" thumbprint="<thumbprint in key vault" thumbprintAlgorithm="sha1" /> 
    ```
6.  För distribution via ARM-mallen kan certificateUrl hittas genom att navigera till certifikatet i nyckel valvet som är märkt som hemligt ID

    :::image type="content" source="media/certs-and-key-vault-6.png" alt-text="Bild visar fältet hemligt ID i nyckel valvet.":::

## <a name="next-steps"></a>Nästa steg 
- Granska [distributions kraven](deploy-prerequisite.md) för Cloud Services (utökad support).
- Läs igenom [vanliga frågor och svar](faq.md) om Cloud Services (utökad support).
- Distribuera en moln tjänst (utökad support) med hjälp av [Azure Portal](deploy-portal.md), [PowerShell](deploy-powershell.md), [mall](deploy-template.md) eller [Visual Studio](deploy-visual-studio.md).
