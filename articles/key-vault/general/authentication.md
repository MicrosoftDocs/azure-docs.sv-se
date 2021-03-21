---
title: Autentisera till Azure Key Vault
description: Lär dig att autentisera till Azure Key Vault
author: ShaneBala-keyvault
ms.author: sudbalas
ms.date: 08/27/2020
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.openlocfilehash: ed83d52d19df126ef9b0e68f984f88f5dfd40c42
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "99492948"
---
# <a name="authenticate-to-azure-key-vault"></a>Autentisera till Azure Key Vault

Med Azure Key Vault kan du lagra hemligheter och kontrol lera deras distribution i en centraliserad, säker moln lagrings plats, vilket eliminerar behovet av att lagra autentiseringsuppgifter i program. Program behöver bara autentisera med Key Vault vid körning för att få åtkomst till dessa hemligheter.

## <a name="app-identity-and-security-principals"></a>App-identitet och säkerhets objekt

Autentisering med Key Vault fungerar tillsammans med [Azure Active Directory (Azure AD)](../../active-directory/fundamentals/active-directory-whatis.md), som ansvarar för att autentisera identiteten för ett specifikt **säkerhets objekt**.

Ett säkerhets objekt är ett objekt som representerar en användare, grupp, tjänst eller ett program som begär åtkomst till Azure-resurser. Azure tilldelar ett unikt **objekt-ID** till varje säkerhets objekt.

* Ett säkerhets objekt för **användare** identifierar en person som har en profil i Azure Active Directory.

* En **grupp** säkerhets objekt identifierar en uppsättning användare som skapats i Azure Active Directory. Alla roller eller behörigheter som tilldelas gruppen beviljas till alla användare i gruppen.

* Ett **huvud namn för tjänsten** är en typ av säkerhets objekt som identiteter ett program eller en tjänst, vilket är att säga en kod del i stället för en användare eller grupp. Ett objekt-ID för tjänstens huvud namn kallas för **klient-ID** och fungerar som sitt användar namn. Tjänst objektets **klient hemlighet** fungerar som sitt lösen ord.

För program finns det två sätt att hämta ett huvud namn för tjänsten:

* Rekommenderas: Aktivera en systemtilldelad **hanterad identitet** för programmet.

    Med hanterad identitet hanterar Azure internt programmets tjänst huvud namn och autentiserar automatiskt programmet med andra Azure-tjänster. Hanterad identitet är tillgänglig för program som distribueras till en mängd olika tjänster.

    Mer information finns i [Översikt över hanterade identiteter](../../active-directory/managed-identities-azure-resources/overview.md). Se även [Azure-tjänster som stöder hanterad identitet](../../active-directory/managed-identities-azure-resources/services-support-managed-identities.md), som länkar till artiklar som beskriver hur du aktiverar hanterad identitet för vissa tjänster (till exempel App Service, Azure Functions, Virtual Machines osv.).

* Om du inte kan använda hanterad identitet **registrerar** du i stället programmet med din Azure AD-klient, enligt beskrivningen i [snabb start: registrera ett program med Azure Identity Platform](../../active-directory/develop/quickstart-register-app.md). Registreringen skapar också ett andra program objekt som identifierar appen för alla klienter.

## <a name="authorize-a-security-principal-to-access-key-vault"></a>Auktorisera ett säkerhets objekt för åtkomst Key Vault

Key Vault fungerar med två olika nivåer av auktorisering:

- **Åtkomst principer** styr om en användare, grupp eller tjänstens huvud namn har behörighet att få åtkomst till hemligheter, nycklar och certifikat *inom* en befintlig Key Vault resurs (kallas ibland "data Plans åtgärder"). Åtkomst principer beviljas vanligt vis till användare, grupper och program.

    Information om hur du tilldelar åtkomst principer finns i följande artiklar:

    - [Azure-portalen](assign-access-policy-portal.md)
    - [Azure CLI](assign-access-policy-cli.md)
    - [Azure PowerShell](assign-access-policy-portal.md)

- **Roll behörigheter** kontrollerar om en användare, grupp eller tjänstens huvud namn har behörighet att skapa, ta bort och på annat sätt hantera en Key Vault resurs (kallas ibland "hanterings Plans åtgärder"). Sådana roller beviljas oftast bara för administratörer.
 
    Information om hur du tilldelar och hanterar roller finns i följande artiklar:

    - [Azure-portalen](../../role-based-access-control/role-assignments-portal.md)
    - [Azure CLI](../../role-based-access-control/role-assignments-cli.md)
    - [Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md)

    Key Vault stöder för närvarande [deltagar](../../role-based-access-control/built-in-roles.md#key-vault-contributor) rollen, som tillåter hanterings åtgärder på Key Vault resurser. Ett antal andra roller är för närvarande en för hands version. Du kan också skapa anpassade roller enligt beskrivningen i [Azures anpassade roller](../../role-based-access-control/custom-roles.md).

    Allmän information om roller finns i [Vad är Azures rollbaserad åtkomst kontroll (Azure RBAC)?](../../role-based-access-control/overview.md).


> [!IMPORTANT]
> För bästa säkerhet ska du alltid följa huvud kontot för minsta behörighet och endast bevilja de mest särskilda åtkomst principerna och rollerna som behövs. 
    
## <a name="configure-the-key-vault-firewall"></a>Konfigurera Key Vault brand vägg

Som standard ger Key Vault åtkomst till resurser via offentliga IP-adresser. För ökad säkerhet kan du också begränsa åtkomsten till vissa IP-adressintervall, tjänst slut punkter, virtuella nätverk eller privata slut punkter.

Mer information finns i [åtkomst Azure Key Vault bakom en brand vägg](./access-behind-firewall.md).


## <a name="the-key-vault-authentication-flow"></a>Flödet för Key Vault autentisering

1. En tjänst huvud namn begär att autentisera med Azure AD, till exempel:
    * En användare loggar in på Azure Portal med ett användar namn och lösen ord.
    * Ett program anropar en Azure-REST API som presenterar ett klient-ID och en hemlighet eller ett klient certifikat.
    * En Azure-resurs, till exempel en virtuell dator med en hanterad identitet, kontaktar [azure instance metadata service (IMDS)](../../virtual-machines/windows/instance-metadata-service.md) REST-slutpunkten för att få en åtkomsttoken.

1. Om autentisering med Azure AD lyckas beviljas tjänstens huvud namn en OAuth-token.

1. Tjänstens huvud namn gör ett anrop till Key Vault REST API via Key Vaults slut punkt (URI).

1. Key Vault brand väggen kontrollerar följande kriterier. Om något kriterium är uppfyllt, tillåts anropet. Annars blockeras anropet och ett förbjudet svar returneras.

    * Brand väggen är inaktive rad och den offentliga slut punkten för Key Vault kan kontaktas från det offentliga Internet.
    * Anroparen är en [Key Vault betrodd tjänst](./overview-vnet-service-endpoints.md#trusted-services), vilket gör att den kan kringgå brand väggen.
    * Anroparen visas i brand väggen med IP-adress, virtuellt nätverk eller tjänst slut punkt.
    * Anroparen kan uppnå Key Vault över en konfigurerad anslutning till en privat länk.    

1. Om brand väggen tillåter anropet, Key Vault anropar Azure AD för att verifiera tjänstens huvud-åtkomsttoken.

1. Key Vault kontrollerar om tjänstens huvud namn har den nödvändiga åtkomst principen för den begärda åtgärden. Annars returnerar Key Vault ett otillåtet svar.

1. Key Vault utför den begärda åtgärden och returnerar resultatet.

Följande diagram illustrerar processen för ett program som anropar en Key Vault "Get Secret"-API:

![Flödet för Azure Key Vault autentisering](../media/authentication/authentication-flow.png)

> [!NOTE]
> Key Vault SDK-klienter för hemligheter, certifikat och nycklar gör ett ytterligare anrop till Key Vault utan åtkomsttoken, vilket resulterar i 401-svar för att hämta klient information. Mer information finns i [autentisering, begär Anden och svar](authentication-requests-and-responses.md)

## <a name="code-examples"></a>Kodexempel

Följande tabell länkar till olika artiklar som visar hur du arbetar med Key Vault i program kod med hjälp av Azure SDK-bibliotek för språket i fråga. Andra gränssnitt, till exempel Azure CLI och Azure Portal, ingår för enkelhetens skull.

| Key Vault hemligheter | Key Vault nycklar | Key Vault certifikat |
|  --- | --- | --- |
| [Python](../secrets/quick-create-python.md) | [Python](../keys/quick-create-python.md) | [Python](../certificates/quick-create-python.md) | 
| [.NET](../secrets/quick-create-net.md) | [.NET](../keys/quick-create-net.md) | [.NET](../certificates/quick-create-net.md) |
| [Java](../secrets/quick-create-java.md) | [Java](../keys/quick-create-java.md) | [Java](../certificates/quick-create-java.md) |
| [JavaScript](../secrets/quick-create-node.md) | [JavaScript](../keys/quick-create-node.md) | [JavaScript](../certificates/quick-create-node.md) | 
| [Azure-portalen](../secrets/quick-create-portal.md) | [Azure-portalen](../keys/quick-create-portal.md) | [Azure-portalen](../certificates/quick-create-portal.md) |
| [Azure CLI](../secrets/quick-create-cli.md) | [Azure CLI](../keys/quick-create-cli.md) | [Azure CLI](../certificates/quick-create-cli.md) |
| [Azure PowerShell](../secrets/quick-create-powershell.md) | [Azure PowerShell](../keys/quick-create-powershell.md) | [Azure PowerShell](../certificates/quick-create-powershell.md) |
| [ARM-mall](../secrets/quick-create-net.md) | -- | -- |

## <a name="next-steps"></a>Nästa steg

- [Fel sökning av Key Vault åtkomst princip](troubleshooting-access-issues.md)
- [Fel koder för Key Vault REST API](rest-error-codes.md)
- [Guide för Key Vault utvecklare](developers-guide.md)
- [Vad är rollbaserad åtkomstkontroll i Azure (Azure RBAC)?](../../role-based-access-control/overview.md)
