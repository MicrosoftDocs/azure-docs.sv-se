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
ms.date: 04/07/2021
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7fabb8bbdb42212dffd3781f4e98204abb518e6b
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105586"
---
# <a name="what-are-managed-identities-for-azure-resources"></a>Vad är hanterade identiteter för Azure-resurser?

En vanlig utmaning för utvecklare är hanteringen av hemligheter och autentiseringsuppgifter som används för att skydda kommunikationen mellan olika komponenter som utgör en lösning. Hanterade identiteter eliminerar behovet av utvecklare att hantera autentiseringsuppgifter. Hanterade identiteter ger en identitet för program som ska användas vid anslutning till resurser som stöder Azure Active Directory (Azure AD)-autentisering. Program kan använda den hanterade identiteten för att hämta Azure AD-token. Ett program kan till exempel använda en hanterad identitet för att få åtkomst till resurser som [Azure Key Vault](../../key-vault/general/overview.md) där utvecklare kan lagra autentiseringsuppgifter på ett säkert sätt eller komma åt lagrings konton.

Vad kan en hanterad identitet användas för?</br>

> [!VIDEO https://www.youtube.com/embed/5lqayO_oeEo]

Här följer några av fördelarna med att använda hanterade identiteter:

- Du behöver inte hantera autentiseringsuppgifter. Autentiseringsuppgifterna är inte ens tillgängliga för dig.
- Du kan använda hanterade identiteter för att autentisera till en resurs som stöder [Azure Active Directory autentisering](../authentication/overview-authentication.md) , inklusive dina egna program.
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

> [!IMPORTANT]
> Oavsett vilken typ av identitet som valts för en hanterad identitet, är tjänstens huvud namn av en särskild typ som bara kan användas med Azure-resurser. När den hanterade identiteten tas bort tas motsvarande tjänst objekt bort automatiskt.

## <a name="how-can-i-use-managed-identities-for-azure-resources"></a>Hur använder jag hanterade identiteter för Azure-resurser?

![några exempel på hur en utvecklare kan använda hanterade identiteter för att få åtkomst till resurser från sin kod utan att hantera autentiseringsinformation](media/overview/when-use-managed-identities.png)

## <a name="what-azure-services-support-the-feature"></a>Vilka Azure-tjänster stöder funktionen?<a name="which-azure-services-support-managed-identity"></a>

Hanterade identiteter för Azure-resurser kan användas för att autentisera till tjänster som stöder Azure AD-autentisering. En lista över Azure-tjänster som stöder funktionen Hanterade identiteter för Azure-resurser finns i [Tjänster som stöder hanterade identiteter för Azure-resurser](./services-support-managed-identities.md).

## <a name="which-operations-can-i-perform-using-managed-identities"></a>Vilka åtgärder kan jag utföra med hanterade identiteter?

Resurser som stöder systemtilldelade hanterade identiteter gör att du kan:

- Aktivera eller inaktivera hanterade identiteter på resurs nivå.
- Använd RBAC-roller för att [bevilja behörigheter](howto-assign-access-portal.md).
- Visa åtgärder för att skapa, läsa, uppdatera, ta bort (CRUD) i [Azure aktivitets loggar](../../azure-resource-manager/management/view-activity-logs.md).
- Visa inloggnings aktivitet i [inloggnings loggar](../reports-monitoring/concept-sign-ins.md)för Azure AD.

Om du väljer en användare som tilldelats hanterad identitet i stället:

- Du kan [skapa, läsa, uppdatera och ta bort](how-to-manage-ua-identity-portal.md) identiteterna.
- Du kan använda RBAC-roll tilldelningar för att [bevilja behörigheter](howto-assign-access-portal.md).
- Användare som tilldelats hanterade identiteter kan användas på fler än en resurs.
- CRUD-åtgärder är tillgängliga för granskning i [Azures aktivitets loggar](../../azure-resource-manager/management/view-activity-logs.md).
- Visa inloggnings aktivitet i [inloggnings loggar](../reports-monitoring/concept-sign-ins.md)för Azure AD.

Åtgärder på hanterade identiteter kan utföras med hjälp av en Azure Resource Manager-mall (ARM), Azure-portalen, Azure CLI-, PowerShell-och REST-API: erna.

## <a name="next-steps"></a>Nästa steg

* [Använda en systemtilldelad hanterad identitet för en virtuell Windows-dator för åtkomst till Resource Manager](tutorial-windows-vm-access-arm.md)
* [Använda en systemtilldelad hanterad identitet för en virtuell Linux-dator för åtkomst till Resource Manager](tutorial-linux-vm-access-arm.md)
* [Använda hanterade identiteter för App Service och Azure Functions](../../app-service/overview-managed-identity.md)
* [Använda hanterade identiteter med Azure Container Instances](../../container-instances/container-instances-managed-identity.md)
* [Implementera hanterade identiteter för Microsoft Azure-resurser](https://www.pluralsight.com/courses/microsoft-azure-resources-managed-identities-implementing).
