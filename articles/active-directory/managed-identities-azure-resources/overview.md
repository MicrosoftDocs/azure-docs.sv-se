---
title: Hanterade identiteter för Azure-resurser
description: En översikt över hanterade identiteter för Azure-resurser.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.assetid: 0232041d-b8f5-4bd2-8d11-27999ad69370
ms.service: active-directory
ms.subservice: msi
ms.devlang: ''
ms.topic: overview
ms.custom: mvc
ms.date: 10/06/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 728ca38cc3ef3bf989a75d757c69f7ca1993d82d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91803132"
---
# <a name="what-are-managed-identities-for-azure-resources"></a>Vad är hanterade identiteter för Azure-resurser?

En vanlig utmaning för utvecklare är hanteringen av hemligheter och autentiseringsuppgifter för att skydda kommunikationen mellan olika tjänster. I Azure eliminerar Managed identiteter behovet av utvecklare som behöver hantera autentiseringsuppgifter genom att tillhandahålla en identitet för Azure-resursen i Azure AD och använda den för att hämta Azure Active Directory (Azure AD)-token. Detta ger även åtkomst till [Azure Key Vault](../../key-vault/general/overview.md) där utvecklare kan lagra autentiseringsuppgifter på ett säkert sätt. Hanterade identiteter för Azure-resurser löser detta problem genom att tillhandahålla Azure-tjänster med en automatiskt hanterad identitet i Azure AD.

Vad kan en hanterad identitet användas för?

   > [!VIDEO https://www.youtube.com/embed/5lqayO_oeEo]

Här följer några av fördelarna med att använda hanterade identiteter:

- Du behöver inte hantera autentiseringsuppgifter. Autentiseringsuppgifterna är inte ens tillgängliga för dig.
- Du kan använda hanterade identiteter för att autentisera till valfri Azure-tjänst som stöder Azure AD-autentisering, inklusive Azure Key Vault.
- Hanterade identiteter kan användas utan ytterligare kostnader.

> [!NOTE]
> Hanterade identiteter för Azure-resurser är det nya namnet på tjänsten som tidigare hade namnet Hanterad tjänstidentitet (MSI).

## <a name="managed-identity-types"></a>Hanterade identitets typer

Det finns två typer av hanterade identiteter:

- **Systemtilldelad** Med vissa Azure-tjänster kan du aktivera en hanterad identitet direkt på en tjänst instans. När du aktiverar en systemtilldelad hanterad identitet skapas en identitet i Azure AD som är kopplad till livs cykeln för tjänst instansen. Så när resursen tas bort, tar Azure automatiskt bort identiteten åt dig. Enligt design kan bara Azure-resursen använda den här identiteten för att begära token från Azure AD.
- **Tilldelade användare** Du kan också skapa en hanterad identitet som en fristående Azure-resurs. Du kan [skapa en användardefinierad hanterad identitet](how-to-manage-ua-identity-portal.md) och tilldela den till en eller flera instanser av en Azure-tjänst. När det gäller användarspecifika hanterade identiteter hanteras identiteten separat från de resurser som använder den. </br></br>

  > [!VIDEO https://www.youtube.com/embed/OzqpxeD3fG0]

Tabellen nedan visar skillnaderna mellan de två typerna av hanterade identiteter.

|  Egenskap    | Systemtilldelad hanterad identitet | Användartilldelad hanterad identitet |
|------|----------------------------------|--------------------------------|
| Skapa |  Skapas som en del av en Azure-resurs (till exempel en virtuell Azure-dator eller Azure App Service) | Skapas som fristående Azure-resurs |
| Livscykel | Delad livs cykel med den Azure-resurs som den hanterade identiteten skapas med. <br/> När den överordnade resursen tas bort, tas även den hanterade identiteten bort. | Oberoende livs cykel. <br/> Måste tas bort explicit. |
| Dela mellan Azure-resurser | Kan inte delas. <br/> Den kan bara kopplas till en enda Azure-resurs. | Kan delas <br/> Samma användare-tilldelade hanterade identitet kan associeras med fler än en Azure-resurs. |
| Vanliga användarsituationer | Arbets belastningar som finns i en enda Azure-resurs <br/> Arbets belastningar för vilka du behöver oberoende identiteter. <br/> Till exempel ett program som körs på en enskild virtuell dator | Arbets belastningar som körs på flera resurser och som kan dela en enda identitet. <br/> Arbets belastningar som behöver förautentisering till en säker resurs som en del av ett etablerings flöde. <br/> Arbets belastningar där resurser återvinns ofta, men behörigheter bör vara konsekventa. <br/> Till exempel en arbets belastning där flera virtuella datorer behöver åtkomst till samma resurs |

>[!IMPORTANT]
>Oavsett vilken typ av identitet som valts för en hanterad identitet, är tjänstens huvud namn av en särskild typ som bara kan användas med Azure-resurser. När den hanterade identiteten tas bort tas motsvarande tjänst objekt bort automatiskt.

## <a name="how-can-i-use-managed-identities-for-azure-resources"></a>Hur använder jag hanterade identiteter för Azure-resurser?

![några exempel på hur en utvecklare kan använda hanterade identiteter för att få åtkomst till resurser från sin kod utan att hantera autentiseringsinformation](media/overview/azure-managed-identities-examples.png)

## <a name="what-azure-services-support-the-feature"></a>Vilka Azure-tjänster stöder funktionen?<a name="which-azure-services-support-managed-identity"></a>

Hanterade identiteter för Azure-resurser kan användas för att autentisera till tjänster som stöder Azure AD-autentisering. En lista över Azure-tjänster som stöder funktionen Hanterade identiteter för Azure-resurser finns i [Tjänster som stöder hanterade identiteter för Azure-resurser](./services-support-managed-identities.md).

## <a name="next-steps"></a>Nästa steg

* [Använda en systemtilldelad hanterad identitet för en virtuell Windows-dator för åtkomst till Resource Manager](tutorial-windows-vm-access-arm.md)
* [Använda en systemtilldelad hanterad identitet för en virtuell Linux-dator för åtkomst till Resource Manager](tutorial-linux-vm-access-arm.md)
* [Använda hanterade identiteter för App Service och Azure Functions](../../app-service/overview-managed-identity.md)
* [Använda hanterade identiteter med Azure Container Instances](../../container-instances/container-instances-managed-identity.md)
* [Implementera hanterade identiteter för Microsoft Azure-resurser](https://www.pluralsight.com/courses/microsoft-azure-resources-managed-identities-implementing).