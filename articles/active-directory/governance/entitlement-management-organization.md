---
title: Lägg till en ansluten organisation i Azure AD-hantering av rättigheter – Azure Active Directory
description: Lär dig hur du tillåter att personer utanför organisationen kan begära åtkomst paket så att du kan samar beta med projekt.
services: active-directory
documentationCenter: ''
author: barclayn
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 06/18/2020
ms.author: barclayn
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3c4869859e11cb6c0cc868ec9deacb3e5cb972c6
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2020
ms.locfileid: "88586579"
---
# <a name="add-a-connected-organization-in-azure-ad-entitlement-management"></a>Lägga till en ansluten organisation i hantering av Azure AD-rättigheter

Med Azure Active Directory (Azure AD) hantering av rättigheter kan du samar beta med personer utanför organisationen. Om du ofta samarbetar med användare i en extern Azure AD-katalog eller domän kan du lägga till dem som en ansluten organisation. Den här artikeln beskriver hur du lägger till en ansluten organisation så att du kan tillåta användare utanför organisationen att begära resurser i din katalog.

## <a name="what-is-a-connected-organization"></a>Vad är en ansluten organisation?

En ansluten organisation är en extern Azure AD-katalog eller domän som du har en relation med.

Anta till exempel att du arbetar på Sparbanken och vill samar beta med två externa organisationer. Dessa två organisationer har olika konfigurationer:

- Graphic Design Institute använder Azure AD och deras användare har ett User Principal Name som slutar med *graphicdesigninstitute.com*.
- Contoso använder inte Azure AD än. Contoso-användare har ett User Principal Name som slutar med *contoso.com*.

I det här fallet kan du konfigurera två anslutna organisationer. Du skapar en ansluten organisation för Graphic Design Institute och en för contoso. Om du sedan lägger till de två anslutna organisationerna i en princip kan användare från varje organisation med en User Principal Name som matchar principen begära åtkomst paket. Användare med en User Principal Name som har en *graphicdesigninstitute.com* -domän skulle matcha grafiken design Institute-Connected och tillåtas att skicka begär Anden. Användare med en User Principal Name som har en *contoso.com* -domän skulle matcha contoso-anslutna organisationer och kan också tillåtas att begära paket. Eftersom Graphic Design Institute använder Azure AD, kan alla användare med ett huvud namn som matchar en [verifierad domän](../fundamentals/add-custom-domain.md#verify-your-custom-domain-name) som har lagts till i sin klient, t *. ex. graphicdesigninstitute*, även begära åtkomst paket med samma princip.

![Exempel på ansluten organisation](./media/entitlement-management-organization/connected-organization-example.png)

Hur användare från Azure AD-katalogen eller domänen autentiseras beror på autentiseringstypen. Typerna av autentisering för anslutna organisationer är:

- Azure AD
- [Direkt federation](../b2b/direct-federation.md)
- [Eng ång slö sen ord](../b2b/one-time-passcode.md) (domän)

Se följande videoklipp om du vill ha en demonstration av hur du lägger till en ansluten organisation:

>[!VIDEO https://www.microsoft.com/videoplayer/embed/RE4dskS]

## <a name="add-a-connected-organization"></a>Lägga till en ansluten organisation

Följ anvisningarna i det här avsnittet om du vill lägga till en extern Azure AD-katalog eller domän som en ansluten organisation.

**Nödvändig roll**: *Global administratör* eller *användar administratör*

1. I Azure Portal väljer du **Azure Active Directory**och väljer sedan **identitets styrning**.

1. I det vänstra fönstret väljer du **anslutna organisationer**och väljer sedan **Lägg till ansluten organisation**.

    ![Knappen Lägg till ansluten organisation](./media/entitlement-management-organization/connected-organization.png)

1. Välj fliken **grundläggande** och ange sedan ett visnings namn och en beskrivning av organisationen.

    ![Fönstret "Lägg till anslutna organisationer"](./media/entitlement-management-organization/organization-basics.png)

1. Välj fliken **katalog + domän** och välj sedan **Lägg till katalog + domän**.

    Fönstret **Välj kataloger + domäner** öppnas.

1. I rutan Sök anger du ett domän namn för att söka efter Azure AD-katalogen eller-domänen. Se till att du anger hela domän namnet.

1. Kontrol lera att organisations namnet och autentiseringstypen är korrekta. Hur användarna loggar in beror på autentiseringstypen.

    ![Fönstret Välj kataloger + domäner](./media/entitlement-management-organization/organization-select-directories-domains.png)

1. Välj **Lägg till** för att lägga till Azure AD-katalogen eller-domänen. För närvarande kan du bara lägga till en Azure AD-katalog eller-domän per ansluten organisation.

    > [!NOTE]
    > Alla användare från Azure AD-katalogen eller-domänen kan begära det här åtkomst paketet. Detta inkluderar användare i Azure AD från alla under domäner som är associerade med katalogen, om inte dessa domäner blockeras av listan över tillåtna eller nekade Azure AD Business to Business (B2B). Mer information finns i [tillåta eller blockera inbjudningar till B2B-användare från vissa organisationer](../b2b/allow-deny-list.md).

1. När du har lagt till Azure AD-katalogen eller-domänen väljer du **Välj**.

    Organisationen visas i listan.

    ![Fönstret "Directory + domän"](./media/entitlement-management-organization/organization-directory-domain.png)

1. Välj fliken **sponsorer** och Lägg till ytterligare sponsorer för den här anslutna organisationen.

    Sponsorer är interna eller externa användare som redan finns i din katalog som är kontakt punkten för relationen med den här anslutna organisationen. Interna sponsorer är medlems användare i din katalog. Externa sponsorer är gäst användare från den anslutna organisationen som tidigare bjudits in och redan finns i din katalog. Sponsorer kan användas som god kännare när användare i den här anslutna organisationen begär åtkomst till det här åtkomst paketet. Information om hur du bjuder in en gäst användare till din katalog finns i [lägga till Azure Active Directory B2B-samarbets användare i Azure Portal](../b2b/add-users-administrator.md).

    När du väljer **Lägg till/ta bort**öppnas ett fönster där du kan välja interna eller externa sponsorer. I fönstret visas en ofiltrerad lista över användare och grupper i din katalog.

    ![Fönstret sponsorer](./media/entitlement-management-organization/organization-sponsors.png)

1. Välj fliken **Granska + skapa** , granska organisationens inställningar och välj sedan **skapa**.

    ![Fönstret "granska + skapa"](./media/entitlement-management-organization/organization-review-create.png)

## <a name="update-a-connected-organization"></a>Uppdatera en ansluten organisation 

Om den anslutna organisationen ändras till en annan domän, om organisationens namn ändras eller om du vill ändra sponsorerna kan du uppdatera den anslutna organisationen genom att följa anvisningarna i det här avsnittet.

**Nödvändig roll**: *Global administratör* eller *användar administratör*

1. I Azure Portal väljer du **Azure Active Directory**och väljer sedan **identitets styrning**.

1. I det vänstra fönstret väljer du **anslutna organisationer**och väljer sedan den anslutna organisationen för att öppna den.

1. I fönstret Översikt i den anslutna organisationen väljer du **Redigera** för att ändra organisationens namn eller beskrivning.  

1. I fönstret **Directory + domän** väljer du **Uppdatera katalog + domän** för att ändra till en annan katalog eller domän.

1. I fönstret **sponsorer** väljer du **Lägg till interna sponsorer** eller **lägger till externa sponsorer** för att lägga till en användare som sponsor. Om du vill ta bort en sponsor väljer du sponsorn och väljer **ta bort**i den högra rutan.


## <a name="delete-a-connected-organization"></a>Ta bort en ansluten organisation

Om du inte längre har en relation med en extern Azure AD-katalog eller-domän kan du ta bort den anslutna organisationen.

**Nödvändig roll**: *Global administratör* eller *användar administratör*

1. I Azure Portal väljer du **Azure Active Directory**och väljer sedan **identitets styrning**.

1. I det vänstra fönstret väljer du **anslutna organisationer**och väljer sedan den anslutna organisationen för att öppna den.

1. I den anslutna organisationens översikts fönster väljer du **ta bort** för att ta bort den.

    För närvarande kan du bara ta bort en ansluten organisation om det inte finns några anslutna användare.

    ![Knappen för att ta bort den anslutna organisationen](./media/entitlement-management-organization/organization-delete.png)

## <a name="managing-a-connected-organization-programmatically"></a>Hantera en ansluten organisation program mässigt

Du kan också skapa, lista, uppdatera och ta bort anslutna organisationer med Microsoft Graph. En användare i en lämplig roll med ett program som har den delegerade `EntitlementManagement.ReadWrite.All` behörigheten kan anropa API: et för att hantera [connectedOrganization](https://docs.microsoft.com/graph/api/resources/connectedorganization?view=graph-rest-beta) -objekt och ange sponsorer för dem.

## <a name="next-steps"></a>Nästa steg

- [Styra åtkomst för externa användare](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-external-users)
- [Styr åtkomsten för användare som inte är i din katalog](entitlement-management-access-package-request-policy.md#for-users-not-in-your-directory)
