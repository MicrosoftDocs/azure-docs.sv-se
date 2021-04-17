---
title: Lägga till en ansluten organisation i Azure AD-berättigandehantering – Azure Active Directory
description: Lär dig hur du tillåter att personer utanför organisationen begär åtkomstpaket så att du kan samarbeta i projekt.
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
ms.openlocfilehash: 44b4e4bccde07d078c9749ee76c1653e6d431e63
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107532088"
---
# <a name="add-a-connected-organization-in-azure-ad-entitlement-management"></a>Lägga till en ansluten organisation i Azure AD-berättigandehantering

Med Azure Active Directory berättigandehantering (Azure AD) kan du samarbeta med personer utanför organisationen. Om du ofta samarbetar med användare i en extern Azure AD-katalog eller -domän kan du lägga till dem som en ansluten organisation. Den här artikeln beskriver hur du lägger till en ansluten organisation så att du kan tillåta användare utanför organisationen att begära resurser i din katalog.

## <a name="what-is-a-connected-organization"></a>Vad är en ansluten organisation?

En ansluten organisation är en annan organisation som du har en relation med.  För att användarna i organisationen ska kunna komma åt dina resurser, till exempel dina SharePoint Online-webbplatser eller appar, behöver du en representation av organisationens användare i katalogen.  Eftersom användarna i den organisationen i de flesta fall inte redan finns i din Azure AD-katalog kan du använda rättighetshantering för att föra in dem i Azure AD-katalogen efter behov.  

Det finns tre sätt som berättigandehantering gör att du kan ange de användare som utgör en ansluten organisation.  Det kan vara

* användare i en annan Azure AD-katalog,
* användare i en annan icke-Azure AD-katalog som har konfigurerats för direkt federation, eller
* användare i en annan katalog än Azure AD, vars e-postadresser har samma domännamn gemensamt.

Anta till exempel att du arbetar på Woodgrove Bank och vill samarbeta med två externa organisationer. Dessa två organisationer har olika konfigurationer:

- Graphic Design Institute använder Azure AD och deras användare har ett huvudnamn för användaren som slutar med *graphicdesigninstitute.com*.
- Contoso använder ännu inte Azure AD. Contosos användare har ett huvudnamn för användaren som slutar med *contoso.com*.

I det här fallet kan du konfigurera två anslutna organisationer. Du skapar en ansluten organisation för Graphic Design Institute och en för Contoso. Om du sedan lägger till de två anslutna organisationerna i en princip kan användare från varje organisation med ett användarhuvudnamn som matchar principen begära åtkomstpaket. Användare med ett användarhuvudnamn som har en domän contoso.com skulle matcha den Contoso-anslutna organisationen och skulle också kunna begära paket. Användare med ett användarens huvudnamn som har *en domän graphicdesigninstitute.com* skulle matcha den grafiska Design Institute-anslutna organisationen och ha tillåtelse att skicka begäranden. Och eftersom Graphic Design Institute använder Azure AD kan alla [](../fundamentals/add-custom-domain.md#verify-your-custom-domain-name) användare med ett huvudnamn som matchar en verifierad domän som har lagts till i deras klientorganisation, till exempel *graphicdesigndesigne.example,* också begära åtkomstpaket med hjälp av samma princip. Om du har aktiverat autentisering med engångslösenord [(OTP)](../external-identities/one-time-passcode.md) via e-post, som omfattar användare från de domäner som ännu inte har Azure AD-konton som autentiseras med hjälp av OTP för e-post vid åtkomst till dina resurser. 

![Exempel på ansluten organisation](./media/entitlement-management-organization/connected-organization-example.png)

Hur användare från Azure AD-katalogen eller domänen autentiseras beror på autentiseringstypen. Autentiseringstyperna för anslutna organisationer är:

- Azure AD
- [Direkt federation](../external-identities/direct-federation.md)
- [Ett lösenord (domän)](../external-identities/one-time-passcode.md)

En demonstration av hur du lägger till en ansluten organisation finns i följande video:

>[!VIDEO https://www.microsoft.com/videoplayer/embed/RE4dskS]

## <a name="add-a-connected-organization"></a>Lägga till en ansluten organisation

Om du vill lägga till en extern Azure AD-katalog eller -domän som en ansluten organisation följer du anvisningarna i det här avsnittet.

**Förutsättningsroll:** *Global administratör* eller *användaradministratör*

1. I Azure Portal väljer du **Azure Active Directory** och sedan Identity **Governance**.

1. I den vänstra rutan väljer du **Anslutna organisationer** och sedan Lägg till **ansluten organisation.**

    ![Knappen Lägg till ansluten organisation](./media/entitlement-management-organization/connected-organization.png)

1. Välj **fliken Grundläggande** inställningar och ange sedan ett visningsnamn och en beskrivning för organisationen.

    ![Fönstret "Lägg till ansluten organisation" Grundläggande](./media/entitlement-management-organization/organization-basics.png)

1. Tillståndet ställs automatiskt in på **Konfigurerad när** du skapar en ny ansluten organisation. Mer information om tillståndsegenskaper finns i [Tillståndsegenskaper för anslutna organisationer](#state-properties-of-connected-organizations)

1. Välj fliken **Katalog + domän** och välj sedan Lägg till katalog + **domän.**

    Fönstret **Välj kataloger +** domäner öppnas.

1. I sökrutan anger du ett domännamn för att söka efter Azure AD-katalogen eller domänen. Se till att ange hela domännamnet.

1. Kontrollera att organisationsnamnet och autentiseringstypen är korrekta. Hur användare loggar in beror på autentiseringstypen.

    ![Fönstret "Välj kataloger + domäner"](./media/entitlement-management-organization/organization-select-directories-domains.png)

1. Välj **Lägg till** för att lägga till Azure AD-katalogen eller -domänen. För närvarande kan du bara lägga till en Azure AD-katalog eller domän per ansluten organisation.

    > [!NOTE]
    > Alla användare från Azure AD-katalogen eller -domänen kommer att kunna begära det här åtkomstpaketet. Detta inkluderar användare i Azure AD från alla underdomäner som är associerade med katalogen, såvida inte dessa domäner blockeras av listan över tillåtna eller nekande i Azure AD business to business (B2B). Mer information finns i Tillåt [eller blockera inbjudningar till B2B-användare från specifika organisationer.](../external-identities/allow-deny-list.md)

1. När du har lagt till Azure AD-katalogen eller -domänen väljer du **Välj**.

    Organisationen visas i listan.

    ![Fönstret "Katalog + domän"](./media/entitlement-management-organization/organization-directory-domain.png)

1. Välj fliken **Sponsring och** lägg sedan till valfria sponsringar för den här anslutna organisationen.

    Sponsring är interna eller externa användare som redan finns i din katalog och som är kontaktpunkten för relationen med den här anslutna organisationen. Interna sponsringar är medlemsanvändare i din katalog. Externa sponsringar är gästanvändare från den anslutna organisationen som tidigare bjudits in och som redan finns i din katalog. Sponsring kan användas som godkännare när användare i den här anslutna organisationen begär åtkomst till det här åtkomstpaketet. Information om hur du bjuder in en gästanvändare till din katalog finns i [Lägga till Azure Active Directory B2B-samarbetsanvändare i Azure Portal](../external-identities/add-users-administrator.md).

    När du väljer **Lägg till/ta** bort öppnas ett fönster där du kan välja interna eller externa sponsringar. Fönstret visar en ofiltrerad lista över användare och grupper i din katalog.

    ![Fönstret Sponsring](./media/entitlement-management-organization/organization-sponsors.png)

1. Välj fliken **Granska + skapa,** granska organisationens inställningar och välj sedan **Skapa.**

    ![Fönstret Granska + skapa](./media/entitlement-management-organization/organization-review-create.png)

## <a name="update-a-connected-organization"></a>Uppdatera en ansluten organisation 

Om den anslutna organisationen ändras till en annan domän, organisationens namn ändras eller om du vill ändra sponsringarna kan du uppdatera den anslutna organisationen genom att följa anvisningarna i det här avsnittet.

**Förutsättningsroll:** *Global administratör* eller *användaradministratör*

1. I Azure Portal väljer du **Azure Active Directory** och sedan Identity **Governance**.

1. I den vänstra rutan väljer du **Anslutna organisationer** och sedan den anslutna organisationen för att öppna den.

1. I den anslutna organisationens översiktsfönster väljer du **Redigera** för att ändra organisationsnamn, beskrivning eller tillstånd.  

1. I fönstret **Katalog + domän** väljer du Uppdatera katalog + domän för **att** ändra till en annan katalog eller domän.

1. I fönstret **Sponsring väljer du** Lägg till interna sponsringar eller Lägg till externa **sponsringar för** att lägga till en användare som sponsringssponsor.  Om du vill ta bort en sponsring väljer du sponsringsponsorn och väljer Ta bort i den **högra rutan.**


## <a name="delete-a-connected-organization"></a>Ta bort en ansluten organisation

Om du inte längre har en relation med en extern Azure AD-katalog eller -domän kan du ta bort den anslutna organisationen.

**Förutsättningsroll:** *Global administratör* eller *användaradministratör*

1. I Azure Portal väljer du **Azure Active Directory** och sedan Identity **Governance**.

1. I den vänstra rutan väljer du **Anslutna organisationer** och sedan den anslutna organisationen för att öppna den.

1. I den anslutna organisationens översiktsfönster väljer du Ta bort **för att** ta bort den.

    ![Knappen Ta bort för ansluten organisation](./media/entitlement-management-organization/organization-delete.png)

## <a name="managing-a-connected-organization-programmatically"></a>Hantera en ansluten organisation programmatiskt

Du kan också skapa, lista, uppdatera och ta bort anslutna organisationer med hjälp av Microsoft Graph. En användare i en lämplig roll med ett program som har delegerad behörighet kan anropa API:et för att hantera `EntitlementManagement.ReadWrite.All` [anslutnaOrganiseringsobjekt](/graph/api/resources/connectedorganization?view=graph-rest-beta&preserve-view=true) och ange sponsring för dem.

## <a name="state-properties-of-connected-organizations"></a>Tillståndsegenskaper för anslutna organisationer

Det finns två olika typer av tillståndsegenskaper för anslutna organisationer i Azure AD-rättighetshantering för närvarande, konfigurerade och föreslagna: 

- En konfigurerad ansluten organisation är en fullt fungerande ansluten organisation som ger användare i organisationen åtkomst till paket. När en administratör skapar en ny ansluten organisation i Azure Portal  är den i det konfigurerade tillståndet som standard sedan administratören skapade och vill använda den här anslutna organisationen. När en ansluten organisation skapas programmatiskt via API:et  ska dessutom standardtillståndet konfigureras om det inte uttryckligen anges till ett annat tillstånd. 

    Konfigurerade anslutna organisationer visas i väljarna för anslutna organisationer och kommer att omfattas av alla principer som riktar sig mot "alla" anslutna organisationer.

- En föreslagen ansluten organisation är en ansluten organisation som har skapats automatiskt, men som inte har haft någon administratör som skapar eller godkänner organisationen. När en användare registrerar sig för ett åtkomstpaket utanför en konfigurerad ansluten  organisation, kommer alla automatiskt skapade anslutna organisationer att vara i det föreslagna tillståndet eftersom ingen administratör i klientorganisationen har konfigurerat denna koppling. 
    
    Föreslagna anslutna organisationer omfattas inte av inställningen "alla konfigurerade anslutna organisationer" för några principer, men kan endast användas i principer för principer som riktar sig till specifika organisationer. 

Endast användare från konfigurerade anslutna organisationer kan begära åtkomstpaket som är tillgängliga för användare från alla konfigurerade organisationer. Användare från föreslagna anslutna organisationer har en upplevelse som om det inte finns någon ansluten organisation för den domänen. kan bara se och begära åtkomstpaket som är begränsade till deras specifika organisation eller begränsade till en användare.

> [!NOTE]
> Som en del av att lansera den här nya funktionen anses alla anslutna organisationer som skapades före 20-09-09 **vara konfigurerade.** Om du har ett åtkomstpaket som gör att användare från en organisation kan registrera sig, bör du granska listan över anslutna organisationer som skapades före det datumet för att se till att inga är felkategoriserade enligt **konfigurerad**.  En administratör kan uppdatera egenskapen **State** efter behov. Vägledning finns i Uppdatera [en ansluten organisation](#update-a-connected-organization).

## <a name="next-steps"></a>Nästa steg

- [Styra åtkomst för externa användare](./entitlement-management-external-users.md)
- [Styra åtkomst för användare som inte finns i din katalog](entitlement-management-access-package-request-policy.md#for-users-not-in-your-directory)
