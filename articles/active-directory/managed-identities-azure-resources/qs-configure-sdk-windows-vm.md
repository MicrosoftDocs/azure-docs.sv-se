---
title: Använd en SDK för att konfigurera hanterade identiteter på en VM – Azure AD
description: Steg för steg-instruktioner för att konfigurera och använda hanterade identiteter för Azure-resurser på en virtuell Azure-dator med hjälp av Azure SDK.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/03/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 41d1be35d2add24cbbc171c3da3bd4d06bce4337
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93359671"
---
# <a name="configure-a-vm-with-managed-identities-for-azure-resources-using-an-azure-sdk"></a>Konfigurera en virtuell dator med hanterade identiteter för Azure-resurser med hjälp av Azure SDK

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Hanterade identiteter för Azure-resurser tillhandahåller Azure-tjänster med en automatiskt hanterad identitet i Azure Active Directory (AD). Du kan använda den här identiteten för att autentisera till en tjänst som stöder Azure AD-autentisering, utan att ha autentiseringsuppgifter i din kod. 

I den här artikeln får du lära dig hur du aktiverar och tar bort hanterade identiteter för Azure-resurser för en virtuell Azure-dator med hjälp av Azure SDK.

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

## <a name="azure-sdks-with-managed-identities-for-azure-resources-support"></a>Azure-SDK: er med hanterade identiteter för support för Azure-resurser 

Azure har stöd för flera olika programmerings plattformar via en serie [Azure SDK](https://azure.microsoft.com/downloads): er. Flera av dem har uppdaterats för att ge stöd för hanterade identiteter för Azure-resurser och ger motsvarande exempel för att demonstrera användningen. Den här listan uppdateras när ytterligare support läggs till:

| SDK | Exempel |
| --- | ------ | 
| .NET   | [Hantera resurser från en virtuell dator som är aktive rad med hanterade identiteter för Azure-resurser aktiverade](https://azure.microsoft.com/resources/samples/aad-dotnet-manage-resources-from-vm-with-msi/) |
| Java   | [Hantera lagring från en virtuell dator som är aktive rad med hanterade identiteter för Azure-resurser](https://azure.microsoft.com/resources/samples/compute-java-manage-resources-from-vm-with-msi-in-aad-group/)|
| Node.js| [Skapa en virtuell dator med systemtilldelad hanterad identitet aktive rad](https://azure.microsoft.com/resources/samples/compute-node-msi-vm/) |
| Python | [Skapa en virtuell dator med systemtilldelad hanterad identitet aktive rad](https://azure.microsoft.com/resources/samples/compute-python-msi-vm/) |
| Ruby   | [Skapa en virtuell Azure-dator med en systemtilldelad identitet aktive rad](https://github.com/Azure-Samples/compute-ruby-msi-vm/) |

## <a name="next-steps"></a>Nästa steg

- Se relaterade artiklar under **Konfigurera identitet för en virtuell Azure-dator** för att lära dig mer om hur du kan använda mallarna Azure Portal, POWERSHELL, CLI och resurser.
