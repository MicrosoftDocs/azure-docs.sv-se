---
title: Autentisera till Azure Key Vault
description: Lär dig hur du autentiserar till Azure Key Vault
author: ShaneBala-keyvault
ms.author: sudbalas
ms.date: 08/27/2020
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.openlocfilehash: 8a8fe4ed0c24d2ccda5fb844005a33a93e85a169
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107365526"
---
# <a name="authenticate-to-azure-key-vault"></a>Autentisera till Azure Key Vault

Azure Key Vault kan du lagra hemligheter och kontrollera deras distribution i en centraliserad, säker molnlagringsplats, vilket eliminerar behovet av att lagra autentiseringsuppgifter i program. Program behöver bara autentiseras Key Vault vid körning för att få åtkomst till dessa hemligheter.

## <a name="app-identity-and-security-principals"></a>Appidentitet och säkerhetsobjekt

Autentisering med Key Vault fungerar tillsammans [med Azure Active Directory (Azure AD)](../../active-directory/fundamentals/active-directory-whatis.md), som ansvarar för att autentisera identiteten för ett visst **säkerhetsobjekt**.

Ett säkerhetsobjekt är ett objekt som representerar en användare, grupp, tjänst eller ett program som begär åtkomst till Azure-resurser. Azure tilldelar ett unikt **objekt-ID till** varje säkerhetsobjekt.

* Ett **säkerhetsobjekt** för användaren identifierar en person som har en profil i Azure Active Directory.

* Ett  gruppsäkerhetsobjekt identifierar en uppsättning användare som skapats i Azure Active Directory. Alla roller eller behörigheter som tilldelas gruppen beviljas till alla användare i gruppen.

* Ett **huvudnamn för** tjänsten är en typ av säkerhetsobjekt som identiteter för ett program eller en tjänst, det vill säga ett kodstycke i stället för en användare eller grupp. Objekt-ID:t för tjänstens huvudnamn kallas dess **klient-ID** och fungerar som dess användarnamn. Tjänstens huvudnamns **klienthemlighet** fungerar som dess lösenord.

För program finns det två sätt att hämta ett huvudnamn för tjänsten:

* Rekommenderat: Aktivera en system tilldelad **hanterad** identitet för programmet.

    Med hanterad identitet hanterar Azure internt programmets huvudnamn för tjänsten och autentiserar automatiskt programmet med andra Azure-tjänster. Hanterad identitet är tillgänglig för program som distribueras till en mängd olika tjänster.

    Mer information finns i Översikt över [hanterad identitet.](../../active-directory/managed-identities-azure-resources/overview.md) Se även [Azure-tjänster](../../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)som stöder hanterad identitet , som länkar till artiklar som beskriver hur du aktiverar hanterad identitet för specifika tjänster (till exempel App Service, Azure Functions, Virtual Machines osv.).

* Om du inte kan använda  hanterad identitet registrerar du i stället programmet med din Azure AD-klientorganisation enligt beskrivningen i [Snabbstart:](../../active-directory/develop/quickstart-register-app.md)Registrera ett program med Azure Identity Platform. Registreringen skapar också ett andra programobjekt som identifierar appen för alla klienter.

## <a name="authorize-a-security-principal-to-access-key-vault"></a>Auktorisera ett säkerhetsobjekt för åtkomst Key Vault

Key Vault fungerar med två separata auktoriseringsnivåer:

- **Åtkomstprinciper** styr om en användare, grupp eller tjänstens huvudnamn  har behörighet att komma åt hemligheter, nycklar och certifikat inom en befintlig Key Vault-resurs (kallas ibland för "dataplansåtgärder"). Åtkomstprinciper beviljas vanligtvis till användare, grupper och program.

    Information om hur du tilldelar åtkomstprinciper finns i följande artiklar:

    - [Azure-portalen](assign-access-policy-portal.md)
    - [Azure CLI](assign-access-policy-cli.md)
    - [Azure PowerShell](assign-access-policy-portal.md)

- **Rollbehörigheter** styr om en användare, grupp eller tjänstens huvudnamn har behörighet att skapa, ta bort och på annat sätt hantera en Key Vault-resurs (kallas ibland för hanteringsplansåtgärder). Sådana roller beviljas oftast endast till administratörer.
 
    Information om hur du tilldelar och hanterar roller finns i följande artiklar:

    - [Azure-portalen](../../role-based-access-control/role-assignments-portal.md)
    - [Azure CLI](../../role-based-access-control/role-assignments-cli.md)
    - [Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md)

    Allmän information om roller finns i [Vad är rollbaserad åtkomstkontroll i Azure (Azure RBAC)?](../../role-based-access-control/overview.md).


> [!IMPORTANT]
> För bästa säkerhet bör du alltid följa huvudprincipen för minsta behörighet och endast bevilja de mest specifika åtkomstprinciper och roller som är nödvändiga. 
    
## <a name="configure-the-key-vault-firewall"></a>Konfigurera Key Vault brandväggen

Som standard Key Vault åtkomst till resurser via offentliga IP-adresser. För bättre säkerhet kan du också begränsa åtkomsten till specifika IP-intervall, tjänstslutpunkter, virtuella nätverk eller privata slutpunkter.

Mer information finns i Access [Azure Key Vault bakom en brandvägg.](./access-behind-firewall.md)


## <a name="the-key-vault-authentication-flow"></a>Det Key Vault autentiseringsflödet

1. En begäran om tjänstens huvudnamn för autentisering med Azure AD, till exempel:
    * En användare loggar in på Azure Portal med ett användarnamn och lösenord.
    * Ett program anropar en Azure REST API och presenterar ett klient-ID och en hemlighet eller ett klientcertifikat.
    * En Azure-resurs, till exempel en virtuell dator med en hanterad identitet, [kontaktar REST-slutpunkten för Azure Instance Metadata Service (IMDS)](../../virtual-machines/windows/instance-metadata-service.md) för att hämta en åtkomsttoken.

1. Om autentiseringen med Azure AD lyckas beviljas tjänstens huvudnamn en OAuth-token.

1. Tjänstens huvudnamn gör ett anrop till Key Vault REST API via Key Vault slutpunkt (URI).

1. Key Vault Firewall kontrollerar följande kriterier. Om något villkor uppfylls tillåts anropet. Annars blockeras anropet och ett förbjudet svar returneras.

    * Brandväggen är inaktiverad och den offentliga slutpunkten för Key Vault kan nås från det offentliga Internet.
    * Anroparen är en [Key Vault betrodd tjänst,](./overview-vnet-service-endpoints.md#trusted-services)så att den kan kringgå brandväggen.
    * Anroparen visas i brandväggen efter IP-adress, virtuellt nätverk eller tjänstslutpunkt.
    * Anroparen kan nå Key Vault via en konfigurerad privat länkanslutning.    

1. Om brandväggen tillåter anropet anropar Key Vault Azure AD för att verifiera åtkomsttoken för tjänstens huvudnamn.

1. Key Vault kontrollerar om tjänstens huvudnamn har den åtkomstprincip som krävs för den begärda åtgärden. Annars returnerar Key Vault ett förbjudet svar.

1. Key Vault utför den begärda åtgärden och returnerar resultatet.

Följande diagram illustrerar processen för ett program som anropar ett api Key Vault "Hämta hemlighet":

![Det Azure Key Vault autentiseringsflödet](../media/authentication/authentication-flow.png)

> [!NOTE]
> Key Vault SDK-klienter för hemligheter, certifikat och nycklar gör ytterligare ett anrop till Key Vault utan åtkomsttoken, vilket resulterar i ett 401-svar för att hämta klientinformation. Mer information finns i [Autentisering, begäranden och svar](authentication-requests-and-responses.md)

## <a name="code-examples"></a>Kodexempel

Följande tabell innehåller länkar till olika artiklar som visar hur du arbetar med Key Vault i programkod med hjälp av Azure SDK-biblioteken för språket i fråga. Andra gränssnitt, till exempel Azure CLI och Azure Portal ingår för enkelhetens skull.

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

- [Key Vault felsökning av åtkomstprincip](troubleshooting-access-issues.md)
- [Key Vault REST API felkoder](rest-error-codes.md)
- [Key Vault utvecklarguide](developers-guide.md)
- [Vad är rollbaserad åtkomstkontroll i Azure (Azure RBAC)?](../../role-based-access-control/overview.md)
