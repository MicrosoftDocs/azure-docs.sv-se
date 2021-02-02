---
title: Integrera med AD-programproxy på en NDES-Server
titleSuffix: Azure Active Directory
description: Vägledning om hur du distribuerar en Azure Active Directory-programproxy för att skydda NDES-servern.
services: active-directory
author: kenwith
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 01/17/2020
ms.author: kenwith
ms.reviewer: mimart
ms.openlocfilehash: 69193296069765ae6e94ffe97913c136a0d033d9
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/02/2021
ms.locfileid: "99257904"
---
# <a name="integrate-with-azure-ad-application-proxy-on-a-network-device-enrollment-service-ndes-server"></a>Integrera med Azure AD-programproxy på en server för registrerings tjänsten för nätverks enheter (NDES)

Med Azure Active Directory (AD) Application Proxy kan du publicera program i nätverket. Dessa program är sådana som SharePoint-webbplatser, Microsoft Outlook Web App och andra webb program. Den ger också säker åtkomst till användare utanför nätverket via Azure.

Om du är nybörjare på Azure AD-programproxy och vill veta mer, se [fjärråtkomst till lokala program via Azure AD-programproxy](application-proxy.md).

Azure AD-programproxy bygger på Azure. Det ger en enorm mängd nätverks bandbredd och Server infrastruktur för bättre skydd mot distribuerade DDOS-attacker (Denial-of-Service) och utmärkt tillgänglighet. Dessutom behöver du inte öppna externa brand Väggs portar till ditt lokala nätverk och ingen DMZ-server krävs. All trafik har sitt ursprung. En fullständig lista över utgående portar finns i [Självstudier: Lägg till ett lokalt program för fjärråtkomst via Application Proxy i Azure Active Directory](./application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment).

> Azure AD-programproxy är en funktion som bara är tillgänglig om du använder Premium-eller Basic-versionerna av Azure Active Directory. Mer information finns i [Azure Active Directory prissättning](https://azure.microsoft.com/pricing/details/active-directory/). 
> Om du har licens för Enterprise Mobility Suite (EMS) är du berättigad till att använda den här lösningen.
> Azure AD-programproxy Connector installeras bara på Windows Server 2012 R2 eller senare. Detta är också ett krav på NDES-servern.

## <a name="install-and-register-the-connector-on-the-ndes-server"></a>Installera och registrera anslutningen på NDES-servern

1. Logga in på [Azure-portalen](https://portal.azure.com/) som programadministratör för den katalog som använder programproxy. Om klientdomänen exempelvis är contoso.com ska administratören vara admin@contoso.com eller något annat administratörsalias på den domänen.
1. Välj ditt användar namn i det övre högra hörnet. Kontrol lera att du är inloggad på en katalog som använder programproxy. Om du behöver ändra kataloger väljer du **Växla katalog** och väljer en katalog som använder Application Proxy.
1. I den vänstra navigerings panelen väljer du **Azure Active Directory**.
1. Under **Hantera** väljer du **programproxy**.
1. Välj **Hämta anslutnings tjänst**.

    ![Hämta kopplings tjänsten om du vill se villkoren för tjänsten](./media/active-directory-app-proxy-protect-ndes/application-proxy-download-connector-service.png)

1. Läs användningsvillkoren. När du är klar väljer du **Godkänn villkoren & Ladda ned**.
1. Kopiera installations filen för Azure AD-programproxy Connector till NDES-servern. 
   > Du kan installera anslutningen på vilken server som helst i företags nätverket med åtkomst till NDES. Du behöver inte installera det på själva NDES-servern.
1. Kör installations filen, t. ex. *AADApplicationProxyConnectorInstaller.exe*. Godkänn licens villkoren för program varan.
1. Under installationen uppmanas du att registrera anslutnings programmet med programproxyn i Azure AD-katalogen.
   * Ange autentiseringsuppgifter för en global eller program administratör i Azure AD-katalogen. Autentiseringsuppgifterna för Global Azure AD-eller program administratör kan skilja sig från dina Azure-autentiseringsuppgifter i portalen.

        > [!NOTE]
        > Det globala kontot eller det program administratörs konto som används för att registrera anslutningen måste tillhöra samma katalog som du aktiverar tjänsten Application Proxy.
        >
        > Om till exempel Azure AD-domänen är *contoso.com* ska global/program administratören vara `admin@contoso.com` eller ett annat giltigt alias i domänen.

   * Om förbättrad säkerhets konfiguration i Internet Explorer är aktive rad för den server där du installerar anslutningen, kan registrerings skärmen blockeras. Om du vill tillåta åtkomst följer du anvisningarna i fel meddelandet eller stänger av förbättrad säkerhet i Internet Explorer under installationen.
   * Om anslutnings registreringen Miss lyckas, se [Felsöka programproxyn](application-proxy-troubleshoot.md).
1. I slutet av installationen visas en anteckning för miljöer med en utgående proxy. Om du vill konfigurera Azure AD-programproxy-anslutningen så att den fungerar via den utgående proxyn kör du det tillhandahållna skriptet, till exempel `C:\Program Files\Microsoft AAD App Proxy connector\ConfigureOutBoundProxy.ps1` .
1. På sidan Application Proxy i Azure Portal visas den nya anslutningen med statusen *aktiv*, som visas i följande exempel:

    ![Den nya Azure AD-programproxy-anslutningen visas som aktiv i Azure Portal](./media/active-directory-app-proxy-protect-ndes/connected-app-proxy.png)

    > [!NOTE]
    > Om du vill ge hög tillgänglighet för program som autentiseras via Azure-AD-programproxy kan du installera anslutningar på flera virtuella datorer. Upprepa samma steg som i föregående avsnitt för att installera anslutnings programmet på andra servrar som är anslutna till den hanterade Azure AD DS-domänen.

1. När installationen är klar går du tillbaka till Azure Portal.

1. Välj **företags program**.

   ![Se till att du är intressant för rätt intressenter](./media/active-directory-app-proxy-protect-ndes/azure-active-directory-enterprise-applications.png)

1. Välj **+ nytt program** och välj sedan **lokalt program**. 

1. På sidan **Lägg till ett eget lokalt program** konfigurerar du följande fält:

   * **Namn**: Ange ett namn för programmet.
   * **Intern URL**: Ange den interna URL: en/FQDN för den NDES-server som du installerade anslutningen på.
   * **Förautentisering**: Välj **passthrough**. Det går inte att använda någon form av förautentisering. Protokollet som används för certifikat begär Anden (SCEP) tillhandahåller inte det här alternativet.
   * Kopiera den angivna **externa webb adressen** till Urklipp.

1. Välj **+ Lägg till** för att spara ditt program.

1. Testa om du kan komma åt NDES-servern via Azure AD-programproxyn genom att klistra in länken som du kopierade i steg 15 i en webbläsare. Du bör se en standard välkomst sida för IIS.

1. Som ett slutligt test lägger du till *mscep.dll* sökvägen till den befintliga URL: en som du klistrade in i föregående steg:

   https://scep-test93635307549127448334.msappproxy.net/certsrv/mscep/mscep.dll

1. Du bör se ett **http-fel 403 – otillåtet** svar.

1. Ändra NDES-URL: en som tillhandahölls (via Microsoft Intune) till enheter. Den här ändringen kan antingen finnas i Microsoft Endpoint Configuration Manager eller i administrations centret för Microsoft Endpoint Manager.

   * För Configuration Manager går du till certifikat registrerings platsen och justerar URL: en. Den här URL: en är vilka enheter som anropar och visar sin utmaning.
   * För fristående Intune kan du antingen redigera eller skapa en ny SCEP-princip och lägga till den nya URL: en.

## <a name="next-steps"></a>Nästa steg

Med Azure AD-programproxy integrerat med NDES kan du publicera program som användare kan komma åt. Mer information finns i [Publicera program med hjälp av Azure AD-programproxy](./application-proxy-add-on-premises-application.md).
