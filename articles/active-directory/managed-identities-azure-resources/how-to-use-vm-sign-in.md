---
title: Använda hanterade identiteter på en virtuell Azure-dator för inloggning – Azure ADV
description: Stegvisa instruktioner och exempel för att använda azure VM-hanterade identiteter för tjänstens huvudnamn för Azure-resurser för skriptklientens inloggning och resursåtkomst.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/29/2021
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 59366f1a5b4bd0572af1b36f7be2f5bf91392660
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107784793"
---
# <a name="how-to-use-managed-identities-for-azure-resources-on-an-azure-vm-for-sign-in"></a>Använda hanterade identiteter för Azure-resurser på en virtuell Azure-dator för inloggning 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]  
Den här artikeln innehåller PowerShell- och CLI-skriptexempel för inloggning med hanterade identiteter för Tjänstens huvudnamn för Azure-resurser och vägledning om viktiga ämnen som felhantering.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

Om du planerar att använda Azure PowerShell eller Azure CLI-exemplen i den här artikeln måste du installera den senaste versionen [av Azure PowerShell](/powershell/azure/install-az-ps) eller [Azure CLI.](/cli/azure/install-azure-cli) 

> [!IMPORTANT]
> - Alla exempelskript i den här artikeln förutsätter att kommandoradsklienten körs på en virtuell dator med hanterade identiteter för Azure-resurser aktiverade. Använd funktionen "Anslut" för den virtuella datorn i Azure Portal för att fjärransluta till den virtuella datorn. Mer information om hur du aktiverar hanterade identiteter för Azure-resurser på en virtuell dator finns i Konfigurera hanterade identiteter för [Azure-resurser](qs-configure-portal-windows-vm.md)på en virtuell dator med hjälp av Azure Portal , eller någon av variantartiklarna (med PowerShell, CLI, en mall eller ett Azure SDK). 
> - För att förhindra fel under resursåtkomst måste den virtuella datorns hanterade identitet ges minst "läsaråtkomst" i lämpligt omfång (den virtuella datorn eller högre) för att tillåta Azure Resource Manager på den virtuella datorn. Mer [information finns i Tilldela hanterade identiteter för Azure-resurser åtkomst till en Azure Portal](howto-assign-access-portal.md) resurs.

## <a name="overview"></a>Översikt

Hanterade identiteter för Azure-resurser tillhandahåller ett [objekt](../develop/developer-glossary.md#service-principal-object) för tjänstens huvudnamn som skapas när du aktiverar hanterade identiteter [för Azure-resurser på](overview.md) den virtuella datorn. Tjänstens huvudnamn kan ges åtkomst till Azure-resurser och användas som en identitet av skript-/kommandoradsklienter för inloggning och resursåtkomst. För att få åtkomst till skyddade resurser med sin egen identitet skulle en skriptklient traditionellt behöva:  

   - vara registrerad och godkännas med Azure AD som ett konfidentiellt klientprogram/webbklientprogram
   - logga in under tjänstens huvudnamn med hjälp av appens autentiseringsuppgifter (som sannolikt är inbäddade i skriptet)

Med hanterade identiteter för Azure-resurser behöver inte skriptklienten längre göra något, eftersom den kan logga in under de hanterade identiteterna för tjänstens huvudnamn för Azure-resurser. 

## <a name="azure-cli"></a>Azure CLI

Följande skript visar hur du:

1. Logga in på Azure AD under den virtuella datorns hanterade identitet för tjänstens huvudnamn för Azure-resurser  
2. Anropa Azure Resource Manager hämta den virtuella datorns ID för tjänstens huvudnamn. CLI tar hand om hanteringen av tokenförvärv/-användning automatiskt. Ersätt namnet på den virtuella datorn med `<VM-NAME>` .  

   ```azurecli
   az login --identity
   
   spID=$(az resource list -n <VM-NAME> --query [*].identity.principalId --out tsv)
   echo The managed identity for Azure resources service principal ID is $spID
   ```

## <a name="azure-powershell"></a>Azure PowerShell

Följande skript visar hur du:

1. Logga in på Azure AD under den virtuella datorns hanterade identitet för tjänstens huvudnamn för Azure-resurser  
2. Anropa en Azure Resource Manager cmdlet för att få information om den virtuella datorn. PowerShell tar hand om hanteringen av tokenanvändning automatiskt.  

   ```azurepowershell
   Add-AzAccount -identity

   # Call Azure Resource Manager to get the service principal ID for the VM's managed identity for Azure resources. 
   $vmInfoPs = Get-AzVM -ResourceGroupName <RESOURCE-GROUP> -Name <VM-NAME>
   $spID = $vmInfoPs.Identity.PrincipalId
   echo "The managed identity for Azure resources service principal ID is $spID"
   ```

## <a name="resource-ids-for-azure-services"></a>Resurs-ID:er för Azure-tjänster

En [lista över resurser som](services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) stöder Azure AD och som har testats med hanterade identiteter för Azure-resurser och deras respektive resurs-ID finns i Azure-tjänster som stöder Azure AD-autentisering.

## <a name="error-handling-guidance"></a>Vägledning för felhantering 

Svar som följande kan indikera att den virtuella datorns hanterade identitet för Azure-resurser inte har konfigurerats korrekt:

- PowerShell: *Invoke-WebRequest: Det går inte att ansluta till fjärrservern*
- CLI: MSI: Det gick inte att hämta en token från med felet *`http://localhost:50342/oauth2/token` "HTTPConnectionPool(host='localhost', port=50342)* 

Om du får något av dessa fel går du tillbaka till den  virtuella Azure-datorn [i Azure Portal](https://portal.azure.com) och går till sidan Identitet och ser till att System assigned (System **assigned)** är inställt på "Yes" (Ja).

## <a name="next-steps"></a>Nästa steg

- Information om hur du aktiverar hanterade identiteter för Azure-resurser på en virtuell Azure-dator finns i Konfigurera hanterade identiteter för Azure-resurser på en virtuell Azure-dator med Hjälp av [PowerShell](qs-configure-powershell-windows-vm.md)eller Konfigurera hanterade identiteter för Azure-resurser på en virtuell Azure-dator med Hjälp av [Azure CLI](qs-configure-cli-windows-vm.md)
