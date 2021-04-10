---
title: Active Directory autentisering – Azure Database for PostgreSQL-enskild server
description: Lär dig mer om begreppen för Azure Active Directory för autentisering med Azure Database for PostgreSQL-enskild server
author: rothja
ms.author: jroth
ms.service: postgresql
ms.topic: conceptual
ms.date: 07/23/2020
ms.openlocfilehash: c2265e0c134f3608830088e2a6754b6d3c88d0d6
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105935762"
---
# <a name="use-azure-active-directory-for-authenticating-with-postgresql"></a>Använda Azure Active Directory för autentisering med PostgreSQL

Microsoft Azure Active Directory (Azure AD)-autentisering är en mekanism för att ansluta till Azure Database for PostgreSQL med hjälp av identiteter som definierats i Azure AD.
Med Azure AD-autentisering kan du hantera användar identiteter för databaser och andra Microsoft-tjänster på en central plats, vilket fören klar behörighets hanteringen.

Fördelarna med att använda Azure AD är:

- Autentisering av användare i Azure-tjänster på ett enhetligt sätt
- Hantering av lösen ords principer och lösen ords rotation på en enda plats
- Flera typer av autentisering stöds av Azure Active Directory, vilket kan eliminera behovet av att lagra lösen ord
- Kunder kan hantera databas behörigheter med hjälp av externa (Azure AD) grupper.
- Azure AD-autentisering använder PostgreSQL databas roller för att autentisera identiteter på databas nivå
- Stöd för tokenbaserad autentisering för program som ansluter till Azure Database for PostgreSQL

Använd följande process för att konfigurera och använda Azure Active Directory autentisering:

1. Skapa och fyll i Azure Active Directory med användar identiteter efter behov.
2. Du kan också associera eller ändra Active Directory som för närvarande är associerad med din Azure-prenumeration.
3. Skapa en Azure AD-administratör för Azure Database for PostgreSQL-servern.
4. Skapa databas användare i databasen som har mappats till Azure AD-identiteter.
5. Anslut till din databas genom att hämta en token för en Azure AD-identitet och inloggning.

> [!NOTE]
> Information om hur du skapar och fyller i Azure AD och sedan konfigurerar Azure AD med Azure Database for PostgreSQL finns i [Konfigurera och logga in med Azure AD för Azure Database for PostgreSQL](howto-configure-sign-in-aad-authentication.md).

## <a name="architecture"></a>Arkitektur

Följande diagram på hög nivå sammanfattar hur autentisering fungerar med Azure AD-autentisering med Azure Database for PostgreSQL. Pilarna visar kommunikations vägar.

![autentiseringspaket][1]

## <a name="administrator-structure"></a>Administratörs struktur

När du använder Azure AD-autentisering finns det två administratörs konton för PostgreSQL-servern. den ursprungliga PostgreSQL-administratören och Azure AD-administratören. Endast administratören som baseras på ett Azure AD-konto kan skapa den första Azure AD-inneslutna databas användaren i en användar databas. Inloggningen för Azure AD-administratören kan vara en Azure AD-användare eller en Azure AD-grupp. När administratören är ett grupp konto kan den användas av alla grupp medlemmar, vilket möjliggör flera Azure AD-administratörer för PostgreSQL-servern. Att använda ett grupp konto som administratör förbättrar hanteringen genom att låta dig lägga till och ta bort grupp medlemmar centralt i Azure AD utan att ändra användare eller behörigheter i PostgreSQL-servern. Endast en Azure AD-administratör (en användare eller grupp) kan konfigureras när som helst.

![administratörs struktur][2]

## <a name="permissions"></a>Behörigheter

Om du vill skapa nya användare som kan autentiseras med Azure AD måste du ha `azure_ad_admin` rollen i-databasen. Den här rollen tilldelas genom att konfigurera Azure AD-administratörskontot för en särskild Azure Database for PostgreSQL Server.

Om du vill skapa en ny Azure AD Database-användare måste du ansluta som Azure AD-administratör. Detta visas i [Konfigurera och logga in med Azure AD för Azure Database for PostgreSQL](howto-configure-sign-in-aad-authentication.md).

En Azure AD-autentisering är bara möjlig om Azure AD-administratören skapades för Azure Database for PostgreSQL. Om Azure Active Directory administratören har tagits bort från servern kan befintliga Azure Active Directory användare som skapats tidigare inte längre ansluta till databasen med sina Azure Active Directory-autentiseringsuppgifter.

## <a name="connecting-using-azure-ad-identities"></a>Ansluta med Azure AD-identiteter

Azure Active Directory-autentisering stöder följande metoder för att ansluta till en databas med hjälp av Azure AD-identiteter:

- Azure Active Directory lösen ord
- Azure Active Directory integrerad
- Azure Active Directory universell med MFA
- Använda Active Directory program certifikat eller klient hemligheter
- [Hanterad identitet](howto-connect-with-managed-identity.md)

När du har autentiserat mot Active Directory kan du hämta en token. Denna token är ditt lösen ord för inloggning.

Observera att hanterings åtgärder, till exempel att lägga till nya användare, bara stöds för Azure AD-användarkonton i det här läget.

> [!NOTE]
> Mer information om hur du ansluter med en Active Directory-token finns i [Konfigurera och logga in med Azure AD för Azure Database for PostgreSQL](howto-configure-sign-in-aad-authentication.md).

## <a name="additional-considerations"></a>Annat som är bra att tänka på

- För att förbättra hanterbarheten rekommenderar vi att du etablerar en dedikerad Azure AD-grupp som administratör.
- Endast en Azure AD-administratör (en användare eller grupp) kan konfigureras för en Azure Database for PostgreSQL-server när som helst.
- Endast en Azure AD-administratör för PostgreSQL kan ansluta till den Azure Database for PostgreSQL med ett Azure Active Directory konto. Active Directory-administratören kan konfigurera efterföljande Azure AD Database-användare.
- Om en användare tas bort från Azure AD kommer den användaren inte längre att kunna autentisera med Azure AD och därför kommer den inte längre att kunna hämta en åtkomsttoken för den användaren. I det här fallet, även om den matchande rollen fortfarande kommer att finnas i databasen, går det inte att ansluta till servern med den rollen.
> [!NOTE]
> Inloggning med den borttagna Azure AD-användaren kan fortfarande göras till token upphör att gälla (upp till 60 minuter från utfärdande av token).  Om du också tar bort användaren från Azure Database for PostgreSQL kommer åtkomsten att återkallas omedelbart.
- Om Azure AD-administratören tas bort från servern kommer servern inte längre att vara associerad med en Azure AD-klient och därför inaktive ras alla Azure AD-inloggningar för servern. Om du lägger till en ny Azure AD-administratör från samma klient aktive ras Azure AD-inloggningar igen.
- Azure Database for PostgreSQL matchar åtkomsttoken till Azure Database for PostgreSQL-rollen med hjälp av användarens unika användar-ID i Azure AD, i stället för att använda användar namnet. Det innebär att om en Azure AD-användare tas bort i Azure AD och en ny användare som skapats med samma namn, Azure Database for PostgreSQL anses vara en annan användare. Om en användare tas bort från Azure AD och en ny användare med samma namn läggs till, kommer den nya användaren därför inte att kunna ansluta till den befintliga rollen. För att tillåta detta måste Azure Database for PostgreSQL Azure AD-administratören återkalla och sedan ge användaren rollen "azure_ad_user" för att uppdatera användar-ID: t för Azure AD.

## <a name="next-steps"></a>Nästa steg

- Information om hur du skapar och fyller i Azure AD och sedan konfigurerar Azure AD med Azure Database for PostgreSQL finns i [Konfigurera och logga in med Azure AD för Azure Database for PostgreSQL](howto-configure-sign-in-aad-authentication.md).
- En översikt över inloggningar, användare och databas roller Azure Database for PostgreSQL finns i [skapa användare i Azure Database for PostgreSQL-enskild server](howto-create-users.md).

<!--Image references-->

[1]: ./media/concepts-aad-authentication/authentication-flow.png
[2]: ./media/concepts-aad-authentication/admin-structure.png
