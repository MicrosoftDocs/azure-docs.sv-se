---
title: Krav för api Azure Active Directory rapportering för | Microsoft Docs
description: Lär dig mer om kraven för åtkomst till Azure AD-rapporterings-API:et
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: ada19f69-665c-452a-8452-701029bf4252
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 03/04/2020
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 00c519ef06637c5193b347f0bbc906c6232a7ca8
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107532544"
---
# <a name="prerequisites-to-access-the-azure-active-directory-reporting-api"></a>Krav för åtkomst till API:Azure Active Directory rapportering

[Azure Active Directory reporting API: er](./concept-reporting-api.md) ger programmässig åtkomst till data via en uppsättning REST-baserade API: er. Du kan anropa dessa API:er från programmeringsspråk och verktyg.

Rapporterings-API:et [använder OAuth](../../api-management/api-management-howto-protect-backend-with-aad.md) för att auktorisera åtkomst till webb-API:er.

För att förbereda din åtkomst till rapporterings-API:et måste du:

1. [Tilldela roller](#assign-roles)
2. [Licenskrav](#license-requirements)
3. [Registrera ett program](#register-an-application)
4. [Bevilja behörigheter](#grant-permissions)
5. [Samla in konfigurationsinställningar](#gather-configuration-settings)

## <a name="assign-roles"></a>Tilldela roller

För att få åtkomst till rapporteringsdata via API:et måste du ha någon av följande roller tilldelade:

- Säkerhetsläsare

- Säkerhetsadministratör

- Global administratör

## <a name="license-requirements"></a>Licenskrav

För att få åtkomst till inloggningsrapporterna för en klientorganisation måste en Azure AD-klient ha Azure AD Premium licens. Azure AD Premium P1-licens (eller högre) krävs för åtkomst till inloggningsrapporter för alla Azure AD-klienter. Om katalogtypen är Azure AD B2C inloggningsrapporterna tillgängliga via API:et utan ytterligare licenskrav. 


## <a name="register-an-application"></a>Registrera ett program

Registrering krävs även om du använder rapporterings-API:et med hjälp av ett skript. Registreringen ger dig ett **program-ID**, som krävs för auktoriseringssamtalen och gör att din kod kan ta emot token.

Om du vill konfigurera din katalog för åtkomst till Azure AD-rapporterings-API:et måste du logga in på [Azure Portal](https://portal.azure.com) med ett Azure-administratörskonto som också är medlem i katalogrollen **Global** administratör i din Azure AD-klientorganisation.

> [!IMPORTANT]
> Program som körs med autentiseringsuppgifter med administratörsbehörighet kan vara mycket kraftfulla, så se till att programmets ID och hemliga autentiseringsuppgifter finns på en säker plats.
> 

**Registrera ett Azure AD-program:**

1. I fönstret [Azure Portal](https://portal.azure.com)väljer **du Azure Active Directory** i det vänstra navigeringsfönstret.
   
    ![Skärmbild som Azure Active Directory valt från Azure Portal menyn.](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2. På sidan **Azure Active Directory** väljer du **Appregistreringar**.

    ![Skärmbild som Appregistreringar valt från menyn Hantera.](./media/howto-configure-prerequisites-for-reporting-api/02.png) 

3. På **Appregistreringar** väljer du **Ny registrering.**

    ![Skärmbild som visar Ny registrering vald.](./media/howto-configure-prerequisites-for-reporting-api/03.png)

4. Sidan **Registrera ett** program:

    ![Skärmbild som visar sidan Registrera ett program där du kan ange värdena i det här steget.](./media/howto-configure-prerequisites-for-reporting-api/04.png)

    a. I **textrutan** Namn skriver du `Reporting API application` .

    b. För **Kontotyp som stöds** väljer du Endast konton i den här **organisationen.**

    c. I **textrutan Omdirigerings-URL** **väljer** du Webb och skriver `https://localhost` .

    d. Välj **Register** (Registrera). 


## <a name="grant-permissions"></a>Bevilja behörigheter 

Beroende på vilket API du vill komma åt måste du ge appen följande behörigheter:  

| API | Behörighet |
| --- | --- |
| Windows Azure Active Directory | Läs katalogdata |
| Microsoft Graph | Läsa alla granskningsloggdata |

![Skärmbild som visar var du kan välja Lägg till en behörighet i fönstret A P I-behörigheter.](./media/howto-configure-prerequisites-for-reporting-api/36.png)

I följande avsnitt visas stegen för båda API:erna. Om du inte vill komma åt något av API:erna kan du hoppa över de relaterade stegen.

**Så här beviljar du programmet behörighet att använda API:erna:**


1. Välj **API-behörigheter** och sedan **Lägg till en behörighet**. 

    ![Skärmbild som visar sidan A P I-behörigheter där du kan välja Lägg till en behörighet.](./media/howto-configure-prerequisites-for-reporting-api/05.png)

2. På sidan **Begär API-behörigheter letar** du upp **Stöd för äldre API-Azure Active Directory** **Graph**. 

    ![Skärmbild som visar sidan Begär A P I-behörigheter där du kan välja Azure Active Directory Graph.](./media/howto-configure-prerequisites-for-reporting-api/06.png)

3. På sidan **Nödvändiga behörigheter** väljer du **Programbehörigheter,** **expanderar Katalog** **kryssruta Directory.ReadAll.**  Välj **Lägg till behörigheter.**

    ![Skärmbild som visar sidan Begär A P I-behörigheter där du kan välja Programbehörigheter.](./media/howto-configure-prerequisites-for-reporting-api/07.png)

4. På sidan **Reporting API Application - API Permissions (Rapporterings-API-program – API-behörigheter)** väljer du Grant admin consent **(Bevilja administratörsmedgivande).** 

    ![Skärmbild som visar sidan rapportering A P I-program A P I-behörigheter där du kan välja Bevilja administratörsmedgivande.](./media/howto-configure-prerequisites-for-reporting-api/08.png)

5. Obs! **Microsoft Graph** läggs till som standard under API-registrering.

    ![Skärmbild som visar sidan A P I-behörigheter där du kan välja Lägg till en behörighet.](./media/howto-configure-prerequisites-for-reporting-api/15.png)

## <a name="gather-configuration-settings"></a>Samla in konfigurationsinställningar 

Det här avsnittet visar hur du hämtar följande inställningar från din katalog:

- Domännamn
- Klient-ID
- Klienthemlighet

Du behöver dessa värden när du konfigurerar anrop till rapporterings-API:et. 

### <a name="get-your-domain-name"></a>Hämta domännamnet

**Så här hämtar du ditt domännamn:**

1. I fönstret [Azure Portal](https://portal.azure.com)väljer du i det vänstra navigeringsfönstret **Azure Active Directory**.
   
    ![Skärmbild som Azure Active Directory valt från Azure Portal menyn.](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2. På **Azure Active Directory** väljer du **Anpassade domännamn**.

    ![Skärmbild som visar anpassade domännamn som valts från Azure Active Directory.](./media/howto-configure-prerequisites-for-reporting-api/09.png) 

3. Kopiera ditt domännamn från listan över domäner.


### <a name="get-your-applications-client-id"></a>Hämta programmets klient-ID

**Så här hämtar du programmets klient-ID:**

1. I fönstret [Azure Portal](https://portal.azure.com)i det vänstra navigeringsfönstret klickar du på **Azure Active Directory**.
   
    ![Skärmbild som Azure Active Directory valt från Azure Portal menyn.](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2. Välj ditt program på **sidan Appregistreringar.**

3. På programsidan går du till **Program-ID och väljer** Klicka **för att kopiera**.

    ![Skärmbild som visar sidan Rapport-A P I-program där du kan kopiera program-ID.](./media/howto-configure-prerequisites-for-reporting-api/11.png) 


### <a name="get-your-applications-client-secret"></a>Hämta programmets klienthemlighet
 Undvik fel när du försöker komma åt granskningsloggar eller logga in med hjälp av API:et.

**Så här hämtar du programmets klienthemlighet:**

1. I fönstret [Azure Portal](https://portal.azure.com)i det vänstra navigeringsfönstret klickar du på **Azure Active Directory**.
   
    ![Skärmbild som Azure Active Directory valt från Azure Portal menyn.](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2.  Välj ditt program på **sidan Appregistreringar.**

3.  Välj **Certifikat och hemligheter** på sidan **API-program.** I avsnittet **Klienthemligheter** klickar du **på + Ny klienthemlighet.** 

    ![Skärmbild som visar sidan Certifikat & hemligheter där du kan lägga till en klienthemlighet.](./media/howto-configure-prerequisites-for-reporting-api/12.png)

5. På sidan **Lägg till en klienthemlighet** lägger du till:

    a. I **textrutan** Beskrivning skriver du `Reporting API` .

    b. Som **Förfaller** väljer du **Om 2 år**.

    c. Klicka på **Spara**.

    d. Kopiera nyckelvärdet.

## <a name="troubleshoot-errors-in-the-reporting-api"></a>Felsöka fel i rapporterings-API:et

I det här avsnittet visas vanliga felmeddelanden som du kan få vid åtkomst till aktivitetsrapporter med hjälp av Microsoft Graph-API:et och stegen för deras lösning.

### <a name="error-failed-to-get-user-roles-from-microsoft-graph"></a>Fel: Det gick inte att hämta användarroller från Microsoft Graph

 Logga in på ditt konto med båda inloggningsknapparna i Graph Explorer-användargränssnittet för att undvika att få ett felmeddelande när du försöker logga in med Graph Explorer. 

![Graph-testaren](./media/troubleshoot-graph-api/graph-explorer.png)

### <a name="error-failed-to-do-premium-license-check-from-microsoft-graph"></a>Fel: Det gick inte att göra premiumlicenskontroll från Microsoft Graph 

Om du får det här felmeddelandet när du försöker komma åt inloggningar med Graph Explorer väljer du Ändra behörigheter **under** ditt konto i det vänstra navigeringsfältet och väljer **Tasks.ReadWrite** och **Directory.Read.All.** 

![Ändra användargränssnittet för behörigheter](./media/troubleshoot-graph-api/modify-permissions.png)

### <a name="error-tenant-is-not-b2c-or-tenant-doesnt-have-premium-license"></a>Fel: Klientorganisationen är inte B2C eller så har klientorganisationen inte någon Premium-licens

Åtkomst till inloggningsrapporter kräver en Azure Active Directory premium 1-licens (P1). Om du ser det här felmeddelandet när du använder inloggningar kontrollerar du att din klientorganisation är licensierad med en Azure AD P1-licens.

### <a name="error-the-allowed-roles-does-not-include-user"></a>Fel: De tillåtna rollerna omfattar inte Användare. 

 Undvik fel vid försök att komma åt granskningsloggar eller logga in med HJÄLP av API:et. Kontrollera att ditt konto är en del av rollen **Säkerhetsläsare eller** **Rapportläsare** i din Azure Active Directory klientorganisation.

### <a name="error-application-missing-aad-read-directory-data-permission"></a>Fel: Programmet saknar behörigheten Läsa katalogdata för AAD 

### <a name="error-application-missing-microsoft-graph-api-read-all-audit-log-data-permission"></a>Fel: Program som saknar Microsoft Graph API-behörigheten "Läsa alla granskningsloggdata"

Följ stegen i Krav för att få åtkomst [till Azure Active Directory reporting-API:et](howto-configure-prerequisites-for-reporting-api.md) för att se till att programmet körs med rätt uppsättning behörigheter. 

## <a name="next-steps"></a>Nästa steg

* [Hämta data med hjälp av Azure Active Directory Reporting-API:et med certifikat](tutorial-access-api-with-certificates.md)
* [Granska API-referens](/graph/api/resources/directoryaudit) 
* [API-referens för inloggningsaktivitetsrapport](/graph/api/resources/signin)