---
title: RDG och Azure MFA server med RADIUS-Azure Active Directory
description: Det här är sidan om Azure Multi-Factor-autentisering som beskriver hur du distribuerar Fjärrskrivbordsgateway (RDG) och Azure Multi-Factor Authentication Server med RADIUS.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/11/2018
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: ff89e8c803e0edf5245a62d625a6367d68de96ba
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96742076"
---
# <a name="remote-desktop-gateway-and-azure-multi-factor-authentication-server-using-radius"></a>Fjärrskrivbordsgateway och Azure Multi-Factor Authentication Server med RADIUS

Ofta använder gatewayen för fjärr skrivbord (RD) lokala [nätverks princip tjänster (NPS)](/windows-server/networking/core-network-guide/core-network-guide#BKMK_optionalfeatures) för att autentisera användare. I den här artikeln beskrivs hur du dirigerar RADIUS-förfrågningar från Fjärrskrivbordsgateway (via lokal NPS) till Multi-Factor Authentication-servern. Kombinationen Azure MFA och Fjärrskrivbordsgateway innebär att användarna har åtkomst till sina arbetsmiljöer var de än är samtidigt som du kan kräva stark autentisering.

Eftersom Windows-autentisering för Terminal Services inte stöds för Server 2012 R2 använder du Fjärrskrivbordsgateway och RADIUS för integrering med MFA-servern.

Installera Azure Multi-Factor Authentication Server på en separat server, som sedan skickar RADIUS-begäran via en proxyanslutning till NPS på RDG-servern. När NPS har verifierat användarnamnet och lösenordet returneras ett svar till Multi-Factor Authentication-servern. MFA-servern utför sedan den andra delen i autentiseringen och returnerar ett svar till gatewayen.

> [!IMPORTANT]
> Från den 1 juli 2019 erbjuder Microsoft inte längre MFA-Server för nya distributioner. Nya kunder som vill kräva Multi-Factor Authentication (MFA) vid inloggnings händelser bör använda molnbaserad Azure AD-Multi-Factor Authentication.
>
> Information om hur du kommer igång med molnbaserad MFA finns i [Självstudier: skydda användar inloggnings händelser med Azure AD Multi-Factor Authentication](tutorial-enable-azure-mfa.md).
>
> Om du använder molnbaserad MFA kan du läsa så här [integrerar du med RADIUS-autentisering för Azure Multi-Factor Authentication](howto-mfa-nps-extension.md).
>
> Befintliga kunder som aktiverade MFA server före den 1 juli 2019 kan ladda ned den senaste versionen, framtida uppdateringar och generera autentiseringsuppgifter för aktivering som vanligt.

## <a name="prerequisites"></a>Förutsättningar

- En domänansluten Azure MFA-server. Om du inte redan har installerat en sådan följer du anvisningarna i [Komma igång med Azure Multi-Factor Authentication Server](howto-mfaserver-deploy.md).
- En befintlig konfigurerad NPS-server.
- En fjärrskrivbordsgateway som autentiserar med Network Policy Services.

> [!NOTE]
> Den här artikeln bör endast användas med MFA Server-distributioner, inte Azure MFA (molnbaserad).

## <a name="configure-the-remote-desktop-gateway"></a>Konfigurera Fjärrskrivbordsgateway

Konfigurera Fjärrskrivbordsgateway så att RADIUS-autentisering skickas till en Azure Multi-Factor Authentication-server.

1. I Hanteraren för fjärrskrivbordsgateway högerklickar du på servernamnet och väljer **Egenskaper**.
2. Gå till fliken **Arkiv med auktoriseringsprinciper för fjärrskrivbordsanslutning** och välj **Central server som kör NPS**.
3. Lägg till en eller flera Azure Multi-Factor Authentication-servrar som RADIUS-servrar genom att ange namnet eller IP-adressen för varje server.
4. Skapa en delad hemlighet för varje server.

## <a name="configure-nps"></a>Konfigurera NPS

Fjärrskrivbordsgateway använder NPS för att skicka RADIUS-begäran till Azure Multi-Factor Authentication. När du konfigurerar NPS börjar du med att ändra tidsgränsinställningarna för att förhindra att tidsgränsen för Fjärrskrivbordsgateway nås innan tvåstegsverifieringen har slutförts. Sedan uppdaterar du NPS för att ta emot RADIUS-autentiseringar från MFA-servern. Följ stegen nedan när du konfigurerar NPS:

### <a name="modify-the-timeout-policy"></a>Ändra tidsgränsprincipen

1. Öppna menyn för **RADIUS-klienter och RADIUS-server** i den vänstra kolumnen i NPS och klicka på **Fjärr-RADIUS-servergrupper**.
2. Välj **TS GATEWAY SERVER GROUP** (TS GATEWAY-SERVERGRUPP).
3. Gå till fliken **Belastningsutjämning**.
4. Ändra både **Antal sekunder utan svar innan begäran anses vara förlorad** och **Antal sekunder mellan begäranden när servern anses vara otillgänglig** till mellan 30 och 60 sekunder. (Om du märker att tidsgränsen ändå nås för servern under autentiseringen kan du gå hit igen och öka antalet sekunder.)
5. Gå till fliken **Autentisering/konto** och kontrollera att de angivna RADIUS-portarna matchar de portar som Multi-Factor Authentication-servern lyssnar på.

### <a name="prepare-nps-to-receive-authentications-from-the-mfa-server"></a>Förbered NPS för att ta emot autentiseringar från MFA-servern

1. Högerklicka på **RADIUS-klienter** under RADIUS-klienter och -servrar i den vänstra kolumnen och välj **Ny**.
2. Lägg till Azure Multi-Factor Authentication Server som en RADIUS-klient. Välj ett eget namn och ange en delad hemlighet.
3. Öppna **Principer**-menyn i den vänstra kolumnen och välj **Principer för anslutningsbegäran**. Nu kan du se en princip med namnet TS GATEWAY AUTHORIZATION POLICY (TS GATEWAY-AUKTORISERINGSPRINCIP) som skapades när Fjärrskrivbordsgateway konfigurerades. Den här principen dirigerar RADIUS-förfrågningar till Multi-Factor Authentication Server.
4. Högerklicka på **TS Gateway-auktoriseringsprincipen** och välj **Duplicera princip**.
5. Öppna den nya principen och gå till fliken **Villkor**.
6. Lägg till ett villkor som matchar Eget namn för klient med det egna namnet som du angav i steg 2 för RADIUS-klienten för Azure Multi-Factor Authentication Server.
7. Gå till fliken **Inställningar** och välj **Autentisering**.
8. Ändra autentiseringsprovider till **Autentisera begäranden på den här servern**. Den här principen säkerställer att när NPS tar emot en RADIUS-begäran från Azure MFA Server så sker autentiseringen lokalt i stället för att en RADIUS-begäran skickas tillbaka till Azure Multi-Factor Authentication Server, vilket skulle resultera i ett looptillstånd.
9. För att förhindra looptillståndet måste den nya principen placeras OVANFÖR den ursprungliga principen i fönstret **Principer för anslutningsbegäran**.

## <a name="configure-azure-multi-factor-authentication"></a>Konfigurera Azure Multi-Factor Authentication

Azure Multi-Factor Authentication Server är konfigurerat som en RADIUS-proxy mellan Fjärrskrivbordsgateway och NPS.  Den bör installeras på en domänansluten server som är separat från RDG-servern. Konfigurera Azure Multi-Factor Authentication Server genom att följa stegen nedan.

1. Öppna Azure Multi-Factor Authentication-servern och välj ikonen för RADIUS-autentisering.
2. Markera kryssrutan **Aktivera RADIUS-autentisering**.
3. På fliken Klienter kontrollerar du att portarna matchar NPS-konfigurationen. Välj sedan **Lägg till**.
4. Lägg till fjärrskrivbordsgatewayserverns IP-adress, programnamnet (valfritt) och en delad hemlighet. Den delade hemligheten måste vara samma på både Azure Multi-Factor Authentication-servern och fjärrskrivbordsgatewayen.
3. Gå till fliken **Mål** och markera alternativknappen **RADIUS-servrar**.
4. Välj **Lägg till** och ange IP-adressen, den delade hemligheten och portarna för NPS-servern. Om du använder en central NPS-server är RADIUS-klienten och RADIUS-målet samma. Den delade hemligheten måste matcha den som konfigurerats i avsnittet RADIUS-klient på NPS-servern.

![RADIUS-autentisering i MFA Server](./media/howto-mfaserver-nps-rdg/radius.png)

## <a name="next-steps"></a>Nästa steg

- Integrera Azure MFA och [IIS-webbappar](howto-mfaserver-iis.md)

- Få svar på dina frågor i [Vanliga frågor och svar om Azure Multi-Factor Authentication](multi-factor-authentication-faq.md)