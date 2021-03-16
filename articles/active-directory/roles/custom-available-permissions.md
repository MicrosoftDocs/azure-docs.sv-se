---
title: Anpassade roll behörigheter för app-registrering – Azure AD | Microsoft Docs
description: Delegera roll behörigheter för anpassad administratör för hantering av app-registreringar.
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: how-to
ms.date: 11/04/2020
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1af2c1b912afbcf44cefbfb021c592836dbde5b8
ms.sourcegitcommit: 3ea12ce4f6c142c5a1a2f04d6e329e3456d2bda5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/15/2021
ms.locfileid: "103466294"
---
# <a name="application-registration-permissions-for-custom-roles-in-azure-active-directory"></a>Program registrerings behörigheter för anpassade roller i Azure Active Directory

Den här artikeln innehåller tillgängliga registrerings behörigheter för program för anpassade roll definitioner i Azure Active Directory (Azure AD).

## <a name="permissions-for-managing-single-tenant-applications"></a>Behörigheter för att hantera program med en enda klient

När du väljer behörigheter för den anpassade rollen har du möjlighet att ge åtkomst till endast hantera program med en klient. program med en enda klient är bara tillgängliga för användare i Azure AD-organisationen där programmet är registrerat. program med en enda klient organisation definieras som att de **konto typer som stöds** har angetts till "konton endast i den här organisations katalogen". I Graph API har signInAudience-egenskapen angetts till "AzureADMyOrg" i en enda klient.

Om du vill bevilja åtkomst till enbart hantering av program med en enda klient använder du behörigheterna nedan med under typen **program. organisationen**. Till exempel Microsoft. Directory/Applications. min organisation/Basic/Update.

Se [översikten över anpassade roller](custom-overview.md) för att få en förklaring av det allmänna villkorets undertyp, behörighet och egenskaps uppsättnings medelvärde. Följande information är specifik för program registreringar.

### <a name="create-and-delete"></a>Skapa och ta bort

Det finns två tillgängliga behörigheter för att ge möjlighet att skapa program registreringar, var och en med olika beteenden:

#### <a name="microsoftdirectoryapplicationscreateasowner"></a>Microsoft. Directory/Applications/createAsOwner

Genom att tilldela det här behörighets resultatet i skaparen som lagts till som första ägare till den skapade app-registreringen, så räknas den skapade program registreringen mot skapare objekt kvoten 250 skapade objekt.

#### <a name="microsoftdirectoryapplicationscreate"></a>Microsoft. Directory/program/skapa

Om du tilldelar det här behörighets resultatet i skaparen, läggs det inte till som första ägare till den skapade app-registreringen, och den skapade app-registreringen räknas inte mot skapare objekt kvoten 250 skapade objekt. Använd den här behörigheten noggrant, eftersom det inte finns något hinder för att skapa registrerings program förrän kvoten på katalog nivå har nåtts. Om båda behörigheterna tilldelas, prioriteras den här behörigheten.

Om båda behörigheterna tilldelas har/Create-behörigheten företräde. Även om/createAsOwner-behörigheten inte automatiskt lägger till skaparen som den första ägaren, kan ägarna anges under skapandet av appens registrering när de använder Graph API: er eller PowerShell-cmdletar.

Skapa behörigheter bevilja åtkomst till det **nya registrerings** kommandot.

[De här behörigheterna ger åtkomst till den nya registrerings Portal kommandot](./media/custom-available-permissions/new-custom-role.png)

Det finns två tillgängliga behörigheter för att ge möjlighet att ta bort registreringar för appar:

#### <a name="microsoftdirectoryapplicationsdelete"></a>Microsoft. Directory/Applications/Delete

Ger möjlighet att ta bort registrerings program oavsett undertyp, det vill säga både en-klient och flera klient program.

#### <a name="microsoftdirectoryapplicationsmyorganizationdelete"></a>Microsoft. Directory/Applications. min organisation/ta bort

Ger möjlighet att ta bort program registreringar som är begränsade till dem som endast är tillgängliga för konton i din organisation eller program med en enskild klient (organisations under typ).

![De här behörigheterna ger åtkomst till kommandot Ta bort app-registrering](./media/custom-available-permissions/delete-app-registration.png)

> [!NOTE]
> När du tilldelar en roll som innehåller behörigheten Skapa, måste roll tilldelningen göras i katalog omfånget. En Create-behörighet som tilldelas i ett resurs omfång ger inte möjlighet att skapa registrerings program.

### <a name="read"></a>Läs

Alla medlems användare i organisationen kan läsa registrerings information för appar som standard. Gäst användare och program tjänstens huvud namn kan dock inte. Om du planerar att tilldela en roll till en gäst användare eller ett program måste du inkludera lämpliga Läs behörigheter.

#### <a name="microsoftdirectoryapplicationsallpropertiesread"></a>Microsoft. Directory/Applications/allProperties/Read

Möjlighet att läsa alla egenskaper för program med en enda klient och flera innehavare utanför egenskaper som inte kan läsas i någon situation som autentiseringsuppgifter.

#### <a name="microsoftdirectoryapplicationsmyorganizationallpropertiesread"></a>Microsoft. Directory/Applications. min organisation/allProperties/läsa

Ger samma behörigheter som Microsoft. Directory/Applications/allProperties/Read, men endast för program med en enda klient.

#### <a name="microsoftdirectoryapplicationsownersread"></a>Microsoft. Directory/program/ägare/läsa

Ger möjlighet att läsa egenskapen ägare för en enskild klient och flera klient program. Ger åtkomst till alla fält på sidan program registrerings ägare:

![Den här behörigheten beviljar åtkomst till sidan för program registrerings ägare](./media/custom-available-permissions/app-registration-owners.png)

#### <a name="microsoftdirectoryapplicationsstandardread"></a>Microsoft. Directory/program/standard/Read

Ger åtkomst till läsa standard program registrerings egenskaper. Detta inkluderar egenskaper för program registrerings sidor.

#### <a name="microsoftdirectoryapplicationsmyorganizationstandardread"></a>Microsoft. Directory/Applications. min organisation/standard/läsa

Ger samma behörigheter som Microsoft. Directory/Applications/standard/Read, men endast för program med en enda klient.

### <a name="update"></a>Uppdatera

#### <a name="microsoftdirectoryapplicationsallpropertiesupdate"></a>Microsoft. Directory/Applications/allProperties/Update

Möjlighet att uppdatera alla egenskaper för program med en enda klient och flera innehavare.

#### <a name="microsoftdirectoryapplicationsmyorganizationallpropertiesupdate"></a>Microsoft. Directory/Applications. min organisation/allProperties/Update

Ger samma behörigheter som Microsoft. Directory/Applications/allProperties/Update, men endast för program med en klient.

#### <a name="microsoftdirectoryapplicationsaudienceupdate"></a>Microsoft. Directory/program/mål grupp/uppdatering

Möjlighet att uppdatera den signInAudience-egenskap som stöds för program med en enda klient och flera innehavare.

![Den här behörigheten beviljar åtkomst till den konto typ egenskap som stöds på sidan för program registrering](./media/custom-available-permissions/supported-account-types.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationaudienceupdate"></a>Microsoft. Directory/Applications. min organisation/mål grupp/uppdatering

Ger samma behörigheter som Microsoft. Directory/program/mål grupp/uppdatering, men endast för program med en klient.

#### <a name="microsoftdirectoryapplicationsauthenticationupdate"></a>Microsoft. Directory/program/autentisering/uppdatering

Möjlighet att uppdatera svars-URL: en, inloggnings-URL, implicit flöde och publicerings domän egenskaper för en enskild klient och flera klient program. Ger åtkomst till alla fält på sidan för autentisering av program registrering, förutom de konto typer som stöds:

![Beviljar åtkomst till app Registration-autentisering men inte konto typer som stöds](./media/custom-available-permissions/supported-account-types.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationauthenticationupdate"></a>Microsoft. Directory/Applications. min organisation/autentisering/uppdatering

Ger samma behörigheter som Microsoft. Directory/program/autentisering/uppdatering, men endast för program med en klient.

#### <a name="microsoftdirectoryapplicationsbasicupdate"></a>Microsoft. Directory/Applications/Basic/Update

Möjlighet att uppdatera namn, logo typ, URL-adress för webb sidor, användnings villkor för URL-adresser och URL-egenskaper för sekretess policy för en klient och flera klient program. Ger åtkomst till alla fält på anpassnings sidan för program registrering:

![Den här behörigheten ger åtkomst till anpassnings sidan för program registrering](./media/custom-available-permissions/app-registration-branding.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationbasicupdate"></a>Microsoft. Directory/Applications. min organisation/Basic/Update

Ger samma behörigheter som Microsoft. Directory/Applications/Basic/Update, men endast för program med en klient.

#### <a name="microsoftdirectoryapplicationscredentialsupdate"></a>Microsoft. Directory/program/autentiseringsuppgifter/uppdatera

Möjlighet att uppdatera egenskaperna för certifikat och klient hemligheter på program med en enda klient och flera innehavare. Ger åtkomst till alla fält på sidan program registrerings certifikat & hemligheter:

![Den här behörigheten ger åtkomst till sidan för app-registrering av certifikat & hemligheter](./media/custom-available-permissions/app-registration-secrets.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationcredentialsupdate"></a>Microsoft. Directory/Applications. min organisation/autentiseringsuppgifter/uppdatera

Ger samma behörigheter som Microsoft. Directory/Applications/credentials/Update, men endast för program med en enda klient.

#### <a name="microsoftdirectoryapplicationsownersupdate"></a>Microsoft. Directory/program/ägare/uppdatering

Möjlighet att uppdatera egenskapen owner på en enskild klient och flera innehavare. Ger åtkomst till alla fält på sidan program registrerings ägare:

![Den här behörigheten beviljar åtkomst till sidan för program registrerings ägare](./media/custom-available-permissions/app-registration-owners.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationownersupdate"></a>Microsoft. Directory/Applications. min organisation/ägare/uppdatera

Ger samma behörigheter som Microsoft. Directory/program/Owners/Update, men endast för program med en enda klient.

#### <a name="microsoftdirectoryapplicationspermissionsupdate"></a>Microsoft. Directory/program/behörigheter/uppdatera

Möjlighet att uppdatera delegerade behörigheter, program behörigheter, auktoriserade klient program, nödvändiga behörigheter och bevilja medgivande egenskaper för en enskild klient och flera klient program. Ger inte möjlighet att genomföra medgivande. Ger åtkomst till alla fält i API-behörigheter för program registrering och visar en API-sida:

![Den här behörigheten ger åtkomst till sidan registrerings-API-behörigheter för appen](./media/custom-available-permissions/app-registration-api-permissions.png)

![Den här behörigheten beviljar åtkomst till appens registrering visar en API-sida](./media/custom-available-permissions/app-registration-expose-api.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationpermissionsupdate"></a>Microsoft. Directory/Applications. min organisation/behörigheter/uppdatera

Ger samma behörigheter som Microsoft. Directory/program/behörigheter/uppdatera, men endast för program med en klient.

## <a name="required-license-plan"></a>Obligatorisk licens plan

[!INCLUDE [License requirement for using custom roles in Azure AD](../../../includes/active-directory-p1-license.md)]

## <a name="next-steps"></a>Nästa steg

- Skapa anpassade roller med hjälp [av Azure Portal, Azure AD PowerShell och Graph API](custom-create.md)
- [Visa lista över rolltilldelningar](view-assignments.md)
