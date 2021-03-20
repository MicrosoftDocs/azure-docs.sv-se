---
title: Lagra hemligheter i ett nyckel valv i Azure DevTest Labs | Microsoft Docs
description: Lär dig hur du lagrar hemligheter i en Azure Key Vault och använder dem när du skapar en virtuell dator, formel eller miljö.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 5714279ef183cb930d643575466dae3d6cb69bba
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "85481654"
---
# <a name="store-secrets-in-a-key-vault-in-azure-devtest-labs"></a>Lagra hemligheter i ett nyckel valv i Azure DevTest Labs
Du kan behöva ange en komplex hemlighet när du använder Azure DevTest Labs: lösen ord för din virtuella Windows-dator, Offentlig SSH-nyckel för din virtuella Linux-dator eller en personlig åtkomsttoken för att klona git-lagrings platsen via en artefakt. Hemligheter är vanligt vis långa och har slumpmässiga tecken. Därför kan det vara svårt och praktiskt att ange dem, särskilt om du använder samma hemlighet flera gånger.

För att lösa det här problemet och även bevara dina hemligheter på ett säkert ställe har DevTest Labs stöd för att lagra hemligheter i ett [Azure Key Vault](../key-vault/general/overview.md). När en användare sparar en hemlighet för första gången skapar DevTest Labs-tjänsten automatiskt ett nyckel valv i samma resurs grupp som innehåller labbet och lagrar hemligheten i nyckel valvet. DevTest Labs skapar ett separat nyckel valv för varje användare. 

Observera att labb användaren måste först skapa en virtuell labb dator innan de kan skapa en hemlighet i nyckel valvet. Detta beror på att DevTest Lab-tjänsten måste associera labb användaren med ett giltigt användar dokument innan de tillåts skapa och lagra hemligheter i nyckel valvet. 


## <a name="save-a-secret-in-azure-key-vault"></a>Spara en hemlighet i Azure Key Vault
Gör så här för att spara din hemlighet i Azure Key Vault:

1. Välj **mina hemligheter** på den vänstra menyn.
2. Ange ett **namn** för hemligheten. Du ser det här namnet i den nedrullningsbara listan när du skapar en virtuell dator, formel eller en miljö. 
3. Ange hemligheten som **värde**.

    ![Lagra hemlighet](media/devtest-lab-store-secrets-in-key-vault/store-secret.png)

## <a name="use-a-secret-from-azure-key-vault"></a>Använd en hemlighet från Azure Key Vault
När du behöver ange en hemlighet för att skapa en virtuell dator, formel eller en miljö kan du antingen ange en hemlighet manuellt eller välja en sparad hemlighet från nyckel valvet. Gör så här om du vill använda en hemlighet som lagras i ditt nyckel valv:

1. Välj **Använd en sparad hemlighet**. 
2. Välj din hemlighet i list rutan för **Välj en hemlighet**. 

    ![Använd hemlighet i virtuell dator](media/devtest-lab-store-secrets-in-key-vault/secret-store-pick-a-secret.png)

## <a name="use-a-secret-in-an-azure-resource-manager-template"></a>Använda en hemlighet i en Azure Resource Manager mall
Du kan ange ditt hemliga namn i en Azure Resource Manager mall som används för att skapa en virtuell dator som visas i följande exempel:

![Använd hemlighet i formel eller miljö](media/devtest-lab-store-secrets-in-key-vault/secret-store-arm-template.png)

## <a name="next-steps"></a>Nästa steg

- [Skapa en virtuell dator med hjälp av hemligheten](devtest-lab-add-vm.md) 
- [Skapa en formel med hjälp av hemligheten](devtest-lab-manage-formulas.md)
- [Skapa en miljö med hjälp av hemligheten](devtest-lab-create-environment-from-arm.md)
