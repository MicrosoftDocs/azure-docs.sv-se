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
ms.date: 12/11/2020
ms.author: barclayn
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8f08c25749bbd21e3624dee898d9a8c97fd74164
ms.sourcegitcommit: 31cfd3782a448068c0ff1105abe06035ee7b672a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/10/2021
ms.locfileid: "98059389"
---
# <a name="add-a-connected-organization-in-azure-ad-entitlement-management"></a>Lägga till en ansluten organisation i hantering av Azure AD-rättigheter

Med Azure Active Directory (Azure AD) hantering av rättigheter kan du samar beta med personer utanför organisationen. Om du ofta samarbetar med användare i en extern Azure AD-katalog eller domän kan du lägga till dem som en ansluten organisation. Den här artikeln beskriver hur du lägger till en ansluten organisation så att du kan tillåta användare utanför organisationen att begära resurser i din katalog.

## <a name="what-is-a-connected-organization"></a>Vad är en ansluten organisation?

En ansluten organisation är en annan organisation som du har en relation till.  För att användarna i organisationen ska kunna komma åt dina resurser, t. ex. dina SharePoint Online-webbplatser eller-appar, behöver du en representation av organisationens användare i den katalogen.  Eftersom användarna i de flesta fall inte redan finns i din Azure AD-katalog kan du använda rättighets hantering för att ta dem till din Azure AD-katalog vid behov.  

Det finns tre sätt som hanterings hantering gör det möjligt för dig att ange de användare som utgör en ansluten organisation.  Det kan vara

* användare i en annan Azure AD-katalog,
* användare i en annan icke-Azure AD-katalog som har kon figurer ATS för direkt Federation eller
* användare i en annan icke-Azure AD-katalog, vars e-postadresser har samma domän namn gemensamt.

Anta till exempel att du arbetar på Sparbanken och vill samar beta med två externa organisationer. Dessa två organisationer har olika konfigurationer:

- Graphic Design Institute använder Azure AD och deras användare har ett User Principal Name som slutar med *graphicdesigninstitute.com*.
- Contoso använder inte Azure AD än. Contoso-användare har ett User Principal Name som slutar med *contoso.com*.

I det här fallet kan du konfigurera två anslutna organisationer. Du skapar en ansluten organisation för Graphic Design Institute och en för contoso. Om du sedan lägger till de två anslutna organisationerna i en princip kan användare från varje organisation med en User Principal Name som matchar principen begära åtkomst paket. Användare med en User Principal Name som har en *graphicdesigninstitute.com* -domän skulle matcha grafiken design Institute-Connected och tillåtas att skicka begär Anden. Användare med en User Principal Name som har en *contoso.com* -domän skulle matcha contoso-anslutna organisationer och kan också tillåtas att begära paket. Eftersom Graphic Design Institute använder Azure AD, kan alla användare med ett huvud namn som matchar en [verifierad domän](../fundamentals/add-custom-domain.md#verify-your-custom-domain-name) som har lagts till i sin klient, t *. ex. graphicdesigninstitute*, även begära åtkomst paket med samma princip.

![Exempel på ansluten organisation](./media/entitlement-management-organization/connected-organization-example.png)

Hur användare från Azure AD-katalogen eller domänen autentiseras beror på autentiseringstypen. Typerna av autentisering för anslutna organisationer är:

- Azure AD
- [Direkt federation](../external-identities/direct-federation.md)
- [Eng ång slö sen ord](../external-identities/one-time-passcode.md) (domän)

Se följande videoklipp om du vill ha en demonstration av hur du lägger till en ansluten organisation:

>[!VIDEO https://www.microsoft.com/videoplayer/embed/RE4dskS]

## <a name="add-a-connected-organization"></a>Lägga till en ansluten organisation

Följ anvisningarna i det här avsnittet om du vill lägga till en extern Azure AD-katalog eller domän som en ansluten organisation.

**Nödvändig roll**: *Global administratör* eller *användar administratör*

1. I Azure Portal väljer du **Azure Active Directory** och väljer sedan **identitets styrning**.

1. I det vänstra fönstret väljer du **anslutna organisationer** och väljer sedan **Lägg till ansluten organisation**.

    ![Knappen Lägg till ansluten organisation](./media/entitlement-management-organization/connected-organization.png)

1. Välj fliken **grundläggande** och ange sedan ett visnings namn och en beskrivning av organisationen.

    ![Fönstret "Lägg till anslutna organisationer"](./media/entitlement-management-organization/organization-basics.png)

1. Statusen ställs automatiskt in på **konfigureras** när du skapar en ny ansluten organisation. Mer information om tillstånds egenskaper finns i [tillstånds egenskaper för anslutna organisationer](#state-properties-of-connected-organizations)

1. Välj fliken **katalog + domän** och välj sedan **Lägg till katalog + domän**.

    Fönstret **Välj kataloger + domäner** öppnas.

1. I rutan Sök anger du ett domän namn för att söka efter Azure AD-katalogen eller-domänen. Se till att du anger hela domän namnet.

1. Kontrol lera att organisations namnet och autentiseringstypen är korrekta. Hur användarna loggar in beror på autentiseringstypen.

    ![Fönstret Välj kataloger + domäner](./media/entitlement-management-organization/organization-select-directories-domains.png)

1. Välj **Lägg till** för att lägga till Azure AD-katalogen eller-domänen. För närvarande kan du bara lägga till en Azure AD-katalog eller-domän per ansluten organisation.

    > [!NOTE]
    > Alla användare från Azure AD-katalogen eller-domänen kan begära det här åtkomst paketet. Detta inkluderar användare i Azure AD från alla under domäner som är associerade med katalogen, om inte dessa domäner blockeras av listan över tillåtna eller nekade Azure AD Business to Business (B2B). Mer information finns i [tillåta eller blockera inbjudningar till B2B-användare från vissa organisationer](../external-identities/allow-deny-list.md).

1. När du har lagt till Azure AD-katalogen eller-domänen väljer du **Välj**.

    Organisationen visas i listan.

    ![Fönstret "Directory + domän"](./media/entitlement-management-organization/organization-directory-domain.png)

1. Välj fliken **sponsorer** och Lägg till ytterligare sponsorer för den här anslutna organisationen.

    Sponsorer är interna eller externa användare som redan finns i din katalog som är kontakt punkten för relationen med den här anslutna organisationen. Interna sponsorer är medlems användare i din katalog. Externa sponsorer är gäst användare från den anslutna organisationen som tidigare bjudits in och redan finns i din katalog. Sponsorer kan användas som god kännare när användare i den här anslutna organisationen begär åtkomst till det här åtkomst paketet. Information om hur du bjuder in en gäst användare till din katalog finns i [lägga till Azure Active Directory B2B-samarbets användare i Azure Portal](../external-identities/add-users-administrator.md).

    När du väljer **Lägg till/ta bort** öppnas ett fönster där du kan välja interna eller externa sponsorer. I fönstret visas en ofiltrerad lista över användare och grupper i din katalog.

    ![Fönstret sponsorer](./media/entitlement-management-organization/organization-sponsors.png)

1. Välj fliken **Granska + skapa** , granska organisationens inställningar och välj sedan **skapa**.

    ![Fönstret "granska + skapa"](./media/entitlement-management-organization/organization-review-create.png)

## <a name="update-a-connected-organization"></a>Uppdatera en ansluten organisation 

Om den anslutna organisationen ändras till en annan domän, om organisationens namn ändras eller om du vill ändra sponsorerna kan du uppdatera den anslutna organisationen genom att följa anvisningarna i det här avsnittet.

**Nödvändig roll**: *Global administratör* eller *användar administratör*

1. I Azure Portal väljer du **Azure Active Directory** och väljer sedan **identitets styrning**.

1. I det vänstra fönstret väljer du **anslutna organisationer** och väljer sedan den anslutna organisationen för att öppna den.

1. I fönstret Översikt i den anslutna organisationen väljer du **Redigera** för att ändra organisationens namn, beskrivning eller tillstånd.  

1. I fönstret **Directory + domän** väljer du **Uppdatera katalog + domän** för att ändra till en annan katalog eller domän.

1. I fönstret **sponsorer** väljer du **Lägg till interna sponsorer** eller **lägger till externa sponsorer** för att lägga till en användare som sponsor. Om du vill ta bort en sponsor väljer du sponsorn och väljer **ta bort** i den högra rutan.


## <a name="delete-a-connected-organization"></a>Ta bort en ansluten organisation

Om du inte längre har en relation med en extern Azure AD-katalog eller-domän kan du ta bort den anslutna organisationen.

**Nödvändig roll**: *Global administratör* eller *användar administratör*

1. I Azure Portal väljer du **Azure Active Directory** och väljer sedan **identitets styrning**.

1. I det vänstra fönstret väljer du **anslutna organisationer** och väljer sedan den anslutna organisationen för att öppna den.

1. I den anslutna organisationens översikts fönster väljer du **ta bort** för att ta bort den.

    ![Knappen för att ta bort den anslutna organisationen](./media/entitlement-management-organization/organization-delete.png)

## <a name="managing-a-connected-organization-programmatically"></a>Hantera en ansluten organisation program mässigt

Du kan också skapa, lista, uppdatera och ta bort anslutna organisationer med Microsoft Graph. En användare i en lämplig roll med ett program som har den delegerade `EntitlementManagement.ReadWrite.All` behörigheten kan anropa API: et för att hantera [connectedOrganization](/graph/api/resources/connectedorganization?view=graph-rest-beta) -objekt och ange sponsorer för dem.

## <a name="state-properties-of-connected-organizations"></a>Tillstånds egenskaper för anslutna organisationer

Det finns två olika typer av tillstånds egenskaper för anslutna organisationer i hantering av Azure AD-hantering för närvarande, konfigurerade och föreslagna: 

- En konfigurerad ansluten organisation är en helt funktionell ansluten organisation som gör det möjligt för användare inom organisationen att få åtkomst till paket. När en administratör skapar en ny ansluten organisation i Azure Portal, kommer den att vara i det **konfigurerade** läget som standard eftersom administratören skapade och vill använda den här anslutna organisationen. När ett anslutet org-program skapas via programmering via API: t, ska standard läget **konfigureras** om det inte anges till ett annat tillstånd explicit. 

    Konfigurerade anslutna organisationer visas i väljare för anslutna organisationer och bevaras i omfånget för alla principer som är riktade till "alla" anslutna organisationer.

- En föreslagen ansluten organisation är en ansluten organisation som har skapats automatiskt, men som inte har fått en administratör att skapa eller godkänna organisationen. När en användare registrerar sig för ett Access-paket utanför en konfigurerad ansluten organisation, kommer alla automatiskt skapade anslutna organisationer att vara i det **föreslagna** läget eftersom ingen administratör i klient organisationen har konfigurerat det partnerskapet. 
    
    Föreslagna anslutna organisationer omfattas inte av inställningen "alla konfigurerade anslutna organisationer" på principer, men kan bara användas i principer för principer som riktar sig mot specifika organisationer. 

Endast användare från konfigurerade anslutna organisationer kan begära åtkomst paket som är tillgängliga för användare från alla konfigurerade organisationer. Användare från föreslagna anslutna organisationer har erfarenhet av att det inte finns någon ansluten organisation för domänen. kan bara se och begära åtkomst paket som är begränsade till den aktuella organisationen eller omfattas av en användare.

> [!NOTE]
> Som en del av den här nya funktionen, ansågs alla anslutna organisationer som skapats före 09/09/20 vara **konfigurerade**. Om du har ett Access-paket som tillåts användare från en organisation att registrera sig bör du gå igenom listan över anslutna organisationer som skapades före det datumet för att se till att ingen är felkategoriserad som **konfigurerad**.  En administratör kan uppdatera **tillstånds** egenskapen efter behov. Vägledning finns i [Uppdatera en ansluten organisation](#update-a-connected-organization).

## <a name="next-steps"></a>Nästa steg

- [Styra åtkomst för externa användare](./entitlement-management-external-users.md)
- [Styr åtkomsten för användare som inte är i din katalog](entitlement-management-access-package-request-policy.md#for-users-not-in-your-directory)
