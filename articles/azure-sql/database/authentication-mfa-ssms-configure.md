---
title: Konfigurera multifaktorautentisering
titleSuffix: Azure SQL Database & SQL Managed Instance & Azure Synapse Analytics
description: Lär dig hur du använder multi-factord Authentication med SSMS för Azure SQL Database, Azure SQL-hanterad instans och Azure Synapse Analytics.
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.custom: has-adal-ref, sqldbrb=3
ms.devlang: ''
ms.topic: how-to
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 08/27/2019
ms.openlocfilehash: 4f90299daed46d06dad9ab37103e3b8f53763ed4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96454378"
---
# <a name="configure-multi-factor-authentication-for-sql-server-management-studio-and-azure-ad"></a>Konfigurera Multi-Factor Authentication för SQL Server Management Studio och Azure AD
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

Den här artikeln visar hur du använder Azure Active Directory (Azure AD) Multi-Factor Authentication (MFA) med SQL Server Management Studio (SSMS). Azure AD MFA kan användas när du ansluter SSMS eller SqlPackage.exe till [Azure SQL Database](sql-database-paas-overview.md), [Azure SQL-hanterad instans](../managed-instance/sql-managed-instance-paas-overview.md) och [Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md). En översikt över Multi-Factor Authentication finns i [Universal Authentication with SQL Database, SQL-hanterad instans och Azure Synapse (SSMS-stöd för MFA)](../database/authentication-mfa-ssms-overview.md).

> [!IMPORTANT]
> Databaser i Azure SQL Database, Azure SQL-hanterad instans och Azure-Synapse kallas kollektivt i resten av den här artikeln som databaser, och servern refererar till den [Server](logical-servers.md) som är värd för databaser för Azure SQL Database och Azure Synapse.

## <a name="configuration-steps"></a>Konfigurationssteg

1. **Konfigurera en Azure Active Directory** – mer information finns i [Administrera Azure AD-katalogen](/previous-versions/azure/azure-services/hh967611(v=azure.100)), [integrera dina lokala identiteter med Azure Active Directory](../../active-directory/hybrid/whatis-hybrid-identity.md), [lägga till ditt eget domän namn i Azure AD](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/), [Microsoft Azure nu stöder Federation med Windows Server Active Directory](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/)och [Hantera Azure AD med hjälp av Windows PowerShell](/previous-versions/azure/jj151815(v=azure.100)).
2. **Konfigurera MFA** -för steg-för-steg-instruktioner, se [Vad är Azure AD-Multi-Factor Authentication?](../../active-directory/authentication/concept-mfa-howitworks.md), [villkorlig åtkomst (MFA) med Azure SQL Database och informations lager](conditional-access-configure.md). (Fullständig villkorlig åtkomst kräver en Premium-Azure Active Directory. Begränsad MFA är tillgängligt med en standard Azure AD.)
3. **Konfigurera Azure AD-autentisering** – stegvisa anvisningar finns i [ansluta till SQL Database, SQL-hanterad instans eller Azure-Synapse med Azure Active Directory autentisering](authentication-aad-overview.md).
4. **Hämta SSMS** – på klient datorn laddar du ned de senaste SSMS från [nedladdnings SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms).

## <a name="connecting-by-using-universal-authentication-with-ssms"></a>Ansluta med hjälp av Universal Authentication med SSMS

Följande steg visar hur du ansluter med den senaste SSMS.

[!INCLUDE[ssms-connect-azure-ad](../includes/ssms-connect-azure-ad.md)]

1. Om du vill ansluta med Universal Authentication går du till dialog rutan **Anslut till Server** i SQL Server Management Studio (SSMS) och väljer **Active Directory-Universal med MFA-stöd**. (Om du ser **Active Directory Universal Authentication** får du inte den senaste versionen av SSMS.)

   ![Skärm bild av fliken anslutnings egenskaper i dialog rutan Anslut till server i s M S. "min databas" har marker ATS i list rutan Anslut till databas.](./media/authentication-mfa-ssms-configure/mfa-no-tenant-ssms.png)  
2. Fyll i rutan **användar namn** med Azure Active Directory autentiseringsuppgifter i formatet `user_name@domain.com` .

   ![Skärm bild av inställningarna för dialog rutan Anslut till Server för Server typ, Server namn, autentisering och användar namn.](./media/authentication-mfa-ssms-configure/1mfa-universal-connect-user.png)
3. Om du ansluter som en gäst användare behöver du inte längre fylla i fältet AD-domännamn eller klient-ID för gäst användare eftersom SSMS 18. x eller senare identifierar det automatiskt. Mer information finns i [Universal Authentication with SQL Database, SQL-hanterad instans och Azure Synapse (SSMS-stöd för MFA)](../database/authentication-mfa-ssms-overview.md).

   ![Skärm bild av fliken anslutnings egenskaper i dialog rutan Anslut till server i s M S. "min databas" har marker ATS i list rutan Anslut till databas.](./media/authentication-mfa-ssms-configure/mfa-no-tenant-ssms.png)

   Men om du ansluter som en gäst användare som använder SSMS 17. x eller äldre, måste du klicka på **alternativ** och i dialog rutan **anslutnings egenskaper** och fylla i rutan **AD-domännamn eller klient-ID** .

   ![Skärm bild av fliken anslutnings egenskaper i dialog rutan Anslut till server i n s. alternativet AD-domännamn eller klient-ID är ifyllt.](./media/authentication-mfa-ssms-configure/mfa-tenant-ssms.png)

4. Välj **alternativ** och ange databasen i dialog rutan **alternativ** . (Om den anslutna användaren är gäst användare (d.v.s. joe@outlook.com ) måste du markera kryss rutan och lägga till det aktuella AD-domännamnet eller klient-ID som en del av alternativen. Se [Universal Authentication with SQL Database och Azure Synapse Analytics (SSMS-stöd för MFA)](../database/authentication-mfa-ssms-overview.md). Klicka sedan på **Anslut**.  
5. När dialog rutan **Logga in på ditt konto** visas anger du Azure Active Directory identitetens konto och lösen ord. Inget lösen ord krävs om en användare är en del av en domän som är federerad med Azure AD.

   ![Skärm bild av dialog rutan logga in till ditt konto för Azure SQL Database och informations lagret. Kontot och lösen ordet fylls i.](./media/authentication-mfa-ssms-configure/2mfa-sign-in.png)  

   > [!NOTE]
   > För Universal Authentication med ett konto som inte kräver MFA ansluter du nu. Fortsätt med följande steg för användare som behöver MFA:
   >  

6. Dialog rutor för två MFA-inställningar kan visas. Denna tids åtgärd beror på inställningen MFA-administratör och kan därför vara valfri. För en MFA-aktiverad domän är det här steget ibland fördefinierat (till exempel kräver domänen att användare använder smartkort och PIN-kod).

   ![Skärm bild av dialog rutan logga in på ditt konto för Azure SQL Database och informations lagret med en uppräknings funktion för att ställa in ytterligare säkerhets verifiering.](./media/authentication-mfa-ssms-configure/3mfa-setup.png)
  
7. I dialog rutan andra möjliga en gång kan du välja information om autentiseringsmetoden. De möjliga alternativen konfigureras av administratören.

   ![Skärm bild av dialog rutan ytterligare säkerhets verifiering med alternativ för att välja och konfigurera en autentiseringsmetod.](./media/authentication-mfa-ssms-configure/4mfa-verify-1.png)  
8. Azure Active Directory skickar bekräftelse informationen till dig. När du tar emot verifierings koden anger du den i rutan **Ange verifierings kod** och klickar på **Logga** in.

   ![Skärm bild av dialog rutan logga in på ditt konto för Azure SQL Database och informations lagret med en uppräknings enhet för att ange en verifierings kod.](./media/authentication-mfa-ssms-configure/5mfa-verify-2.png)  

När verifieringen är klar, antas SSMS-anslutningar normalt giltiga autentiseringsuppgifter och brand Väggs åtkomst.

## <a name="next-steps"></a>Nästa steg

- En översikt över Multi-Factor Authentication finns i [Universal Authentication with SQL Database, SQL-hanterad instans och Azure Synapse (SSMS-stöd för MFA)](../database/authentication-mfa-ssms-overview.md).  
- Ge andra åtkomst till databasen: [SQL Database autentisering och auktorisering: bevilja åtkomst](logins-create-manage.md)  
- Se till att andra kan ansluta genom brand väggen: [Konfigurera en brand Väggs regel på server nivå med hjälp av Azure Portal](./firewall-configure.md)  
- När du använder **Active Directory-universell med MFA-** autentisering, är ADAL-spårning tillgängligt från och med [SSMS 17,3](/sql/ssms/download-sql-server-management-studio-ssms). Av som standard kan du aktivera ADAL-spårning genom att använda menyn **verktyg**, **alternativ** , under Azure- **tjänster**, **Azure Cloud**, **ADAL fönstret utmatning spårnings nivå**, och sedan aktivera **utdata**  i menyn **Visa** . Spårningarna är tillgängliga i fönstret utdata när du väljer **Azure Active Directory alternativ**.