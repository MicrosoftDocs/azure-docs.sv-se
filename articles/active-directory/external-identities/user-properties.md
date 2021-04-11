---
title: Egenskaper för en B2B-gäst användare – Azure Active Directory | Microsoft Docs
description: Azure Active Directory B2B-gäst användarens egenskaper och tillstånd före och efter inlösningen för inbjudan
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 03/18/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan, seoapril2019
ms.collection: M365-identity-device-management
ms.openlocfilehash: 08ed02c65db201e18ce710633b421edb5ecb2db0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104655707"
---
# <a name="properties-of-an-azure-active-directory-b2b-collaboration-user"></a>Egenskaper för en Azure Active Directory B2B-samarbets användare

I den här artikeln beskrivs egenskaperna och tillstånden för gäst användar objektet B2B i Azure Active Directory (Azure AD) före och efter inlösningen för inbjudan. En Azure AD Business-to-Business (B2B)-samarbets användare är en användare med UserType = gäst. Den här gäst användaren är vanligt vis från en partner organisation och har begränsad behörighet i den bjudande katalogen som standard.

Beroende på den bjudande organisationens behov kan en Azure AD B2B-samarbets användare ha ett av följande konto tillstånd:

- Tillstånd 1: i en extern instans av Azure AD som visas som gäst användare i den bjudande organisationen. I det här fallet loggar B2B-användaren in genom att använda ett Azure AD-konto som tillhör den inbjudna klienten. Om partner organisationen inte använder Azure AD skapas fortfarande gäst användaren i Azure AD. Kraven är att de löser in sin inbjudan och Azure AD verifierar sin e-postadress. Den här ordningen kallas även för ett just-in-Time-innehav (JIT) eller ett "viral"-innehav.

   > [!IMPORTANT]
   > **Från den 2021 oktober** kommer Microsoft inte längre att stödja inlösen av inbjudningar genom att skapa ohanterade Azure AD-konton och klienter för B2B-samarbets scenarier. Vi rekommenderar att kunderna väljer [autentisering med e-post med eng ång slö sen ord](one-time-passcode.md). Vi välkomnar din feedback om den här offentliga för hands versionen och är glada att skapa ännu fler sätt att samar beta.

- Tillstånd 2: i ett Microsoft-konto eller ett annat konto som representeras som gäst användare i värd organisationen. I det här fallet loggar gäst användaren in med ett Microsoft-konto eller ett socialt konto (google.com eller liknande). Den inbjudna användarens identitet skapas som en Microsoft-konto i den bjudande organisationens katalog under erbjudandet inlösen.

- Tillstånd 3: hemma i värd organisationens lokala Active Directory och synkroniseras med värd organisationens Azure AD. Du kan använda Azure AD Connect för att synkronisera partner kontona till molnet som Azure AD B2B-användare med UserType = gäst. Se [bevilja lokalt hanterad partner konton åtkomst till moln resurser](hybrid-on-premises-to-cloud.md).

- State 4: i värd organisationens Azure AD med UserType = gäst och autentiseringsuppgifter som värd organisationen hanterar.

  ![Diagram som illustrerar de fyra användar tillstånden](media/user-properties/redemption-diagram.png)


Nu ska vi se vad en Azure AD B2B-samarbets användare ser ut i Azure AD.

### <a name="before-invitation-redemption"></a>Före inlösen av inbjudan

Kontona State 1 och State 2 är resultatet av att bjuda in gäst användare att samar beta med gäst användarnas egna autentiseringsuppgifter. När inbjudan först skickas till gäst användaren skapas ett konto i din katalog. Det här kontot har inte några associerade autentiseringsuppgifter eftersom autentiseringen utförs av gäst användarens identitets leverantör. **Käll** egenskapen för gäst användar kontot i din katalog är inställd på **inbjuden användare**. 

![Skärm bild som visar användar egenskaper före erbjudandet om inlösen](media/user-properties/before-redemption.png)

### <a name="after-invitation-redemption"></a>Efter inlösen av inbjudan

När gäst användaren har accepterat inbjudan uppdateras egenskapen **källa** baserat på gäst användarens identitets leverantör.

För gäst användare i läge 1 är **källan** **extern Azure Active Directory**.

![Tillstånd 1 gäst användare efter inlösen av erbjudandet](media/user-properties/after-redemption-state1.png)

För gäst användare i läge 2 är **källan** Microsoft- **konto**.

![Tillstånd 2 gäst användare efter inlösen av erbjudandet](media/user-properties/after-redemption-state2.png)

För gäst användare i läge 3 och State 4 anges egenskapen **Source** till **Azure Active Directory** eller **Windows Server Active Directory**, enligt beskrivningen i nästa avsnitt.

## <a name="key-properties-of-the-azure-ad-b2b-collaboration-user"></a>Nyckel egenskaper för Azure AD B2B Collaboration-användare
### <a name="usertype"></a>UserType
Den här egenskapen anger användarens relation till värd innehavaren. Den här egenskapen kan ha två värden:
- Medlem: det här värdet anger en anställd hos värd organisationen och en användare i organisationens lön. Den här användaren förväntar sig till exempel att ha åtkomst till interna webbplatser. Den här användaren betraktas inte som en extern medarbetare.

- Gäst: det här värdet anger en användare som inte anses vara intern för företaget, till exempel en extern medarbetare, partner eller kund. En sådan användare förväntas inte ta emot en DIREKTÖRs interna PM eller ta emot företags förmåner, till exempel.

  > [!NOTE]
  > UserType har ingen relation till hur användaren loggar in, katalog rollen för användaren och så vidare. Den här egenskapen anger bara användarens relation till värd organisationen och gör att organisationen kan genomdriva principer som är beroende av den här egenskapen.

Mer information om priser finns i [Azure Active Directory prissättning](https://azure.microsoft.com/pricing/details/active-directory).

### <a name="source"></a>Källa
Den här egenskapen anger hur användaren loggar in.

- Inbjuden användare: den här användaren har bjudits in men har ännu inte löst en inbjudan.

- Externa Azure Active Directory: den här användaren är i en extern organisation och autentiseras med hjälp av ett Azure AD-konto som tillhör den andra organisationen. Den här typen av inloggning motsvarar tillstånd 1.

- Microsoft-konto: den här användaren är i ett Microsoft-konto och autentiseras med hjälp av en Microsoft-konto. Den här typen av inloggning motsvarar State 2.

- Windows Server-Active Directory: den här användaren är inloggad från den lokala Active Directory som tillhör den här organisationen. Den här typen av inloggning motsvarar State 3.

- Azure Active Directory: den här användaren autentiseras med hjälp av ett Azure AD-konto som tillhör den här organisationen. Den här typen av inloggning motsvarar State 4.
  > [!NOTE]
  > Käll-och UserType är oberoende egenskaper. Värdet källa innebär inte ett visst värde för UserType.

## <a name="can-azure-ad-b2b-users-be-added-as-members-instead-of-guests"></a>Kan Azure AD B2B-användare läggas till som medlemmar i stället för gäster?
Normalt är en Azure AD B2B-användare och gäst användare synonym. Därför läggs en Azure AD B2B-samarbets användare till som en användare med UserType = gäst som standard. Men i vissa fall är partner organisationen medlem i en större organisation som värd organisationen också tillhör. I så fall kanske värd organisationen vill behandla användare i partner organisationen som medlemmar i stället för gäster. Använd API: erna för Azure AD B2B-Inbjudnings hanteraren för att lägga till eller bjuda in en användare från partner organisationen till värd organisationen som medlem.

## <a name="filter-for-guest-users-in-the-directory"></a>Filter för gäst användare i katalogen

![Skärm bild som visar filtret för gäst användare](media/user-properties/filter-guest-users.png)

## <a name="convert-usertype"></a>Konvertera UserType
Det går att konvertera UserType från medlem till gäst och vice versa med hjälp av PowerShell. Men egenskapen UserType representerar användarens relation till organisationen. Därför bör du bara ändra den här egenskapen om relationen för användaren till organisationen ändras. Om användarens relation ändras bör User Principal Name (UPN) ändras? Ska användaren fortsätta att ha åtkomst till samma resurser? Ska en post låda tilldelas? Vi rekommenderar inte att du ändrar UserType med hjälp av PowerShell som en atomisk aktivitet. Om den här egenskapen till exempel blir oföränderlig med hjälp av PowerShell rekommenderar vi inte att du tar ett beroende på det här värdet.

## <a name="remove-guest-user-limitations"></a>Ta bort begränsningar för gäst användare
Det kan finnas fall där du vill ge gäst användarna högre privilegier. Du kan lägga till en gäst användare till vilken roll som helst och till och med ta bort standard begränsningarna för gäst användare i katalogen för att ge en användare samma behörigheter som medlemmar.

Det går att inaktivera standard begränsningarna så att en gäst användare i företags katalogen har samma behörigheter som en medlems användare.

![Skärm bild som visar alternativet externa användare i användar inställningarna](media/user-properties/remove-guest-limitations.png)

## <a name="can-i-make-guest-users-visible-in-the-exchange-global-address-list"></a>Kan jag göra gäst användare synliga i den globala adress listan i Exchange?
Ja. Gäst objekt visas som standard inte i din organisations globala adress lista, men du kan använda Azure Active Directory PowerShell för att göra dem synliga. Mer information finns i avsnittet "lägga till gäster i den globala adress listan" i [artikeln Microsoft 365 per grupp gäst åtkomst](/microsoft-365/solutions/per-group-guest-access).

## <a name="can-i-update-a-guest-users-email-address"></a>Kan jag uppdatera en gäst användares e-postadress?

Om en gäst användare accepterar din inbjudan och senare ändrar sin e-postadress, synkroniseras inte den nya e-postadressen automatiskt med gäst användar objekt i din katalog. E-postegenskapen skapas via [Microsoft Graph-API](/graph/api/resources/user). Du kan uppdatera e-postegenskapen via Microsoft Graph API, administrations Center för Exchange eller [Exchange Online PowerShell](/powershell/module/exchange/users-and-groups/set-mailuser). Ändringen visas i Azure AD-objektet gäst användare.

## <a name="next-steps"></a>Nästa steg

* [Vad är Azure AD B2B-samarbete?](what-is-b2b.md)
* [Användar-tokens för B2B-samarbete](user-token.md)
* [Mappning av användar anspråk för B2B-samarbete](claims-mapping.md)
