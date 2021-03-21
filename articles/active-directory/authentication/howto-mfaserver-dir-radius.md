---
title: RADIUS och Azure MFA Server – Azure Active Directory
description: Distribuera RADIUS-autentisering och Azure Multi-Factor Authentication-server.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 23e2eafae185551c79d651ec6dcf39ad11eb8534
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96742127"
---
# <a name="integrate-radius-authentication-with-azure-multi-factor-authentication-server"></a>Integrera RADIUS-autentisering och Azure Multi-Factor Authentication Server

RADIUS är ett standardprotokoll för att acceptera autentiseringsförfrågningar och för att bearbeta dessa. Azure Multi-Factor Authentication-servern fungerar som en RADIUS-server. Infoga det mellan RADIUS-klienten (VPN-installation) och autentiseringsmålet för att lägga till tvåstegsverifiering. Autentiseringsmål kan vara Active Directory, en LDAP-katalog eller en annan RADIUS-server. För att Azure Multi-Factor Authentication (MFA) ska fungera måste du konfigurera Azure MFA-servern så att den kan kommunicera med både klientservrarna och autentiseringsmålet. Azure MFA-servern tar emot förfrågningar från en RADIUS-klient, validerar autentiseringsuppgifter mot autentiseringsmålet, lägger till Azure Multi-Factor Authentication och skickar tillbaka ett svar till RADIUS-klienten. Autentiseringsbegäran lyckas bara om både den primära autentiseringen och Azure Multi-Factor Authentication lyckas.

> [!IMPORTANT]
> Från den 1 juli 2019 erbjuder Microsoft inte längre MFA-Server för nya distributioner. Nya kunder som vill kräva Multi-Factor Authentication (MFA) vid inloggnings händelser bör använda molnbaserad Azure AD-Multi-Factor Authentication.
>
> Information om hur du kommer igång med molnbaserad MFA finns i [Självstudier: skydda användar inloggnings händelser med Azure AD Multi-Factor Authentication](tutorial-enable-azure-mfa.md).
>
> Om du använder molnbaserad MFA, se [integrera din befintliga NPS-infrastruktur med Azure Multi-Factor Authentication](howto-mfa-nps-extension.md).
>
> Befintliga kunder som aktiverade MFA server före den 1 juli 2019 kan ladda ned den senaste versionen, framtida uppdateringar och generera autentiseringsuppgifter för aktivering som vanligt.

> [!NOTE]
> MFA-servern stöder endast RADIUS-protokollen PAP (Password Authentication Protocol) och MSCHAPv2 (Microsoft Challenge Handshake Authentication Protocol) när den fungerar som en RADIUS-server.  Andra protokoll som EAP (Extensible Authentication Protocol) kan användas när MFA-servern fungerar som en RADIUS-proxy till en annan RADIUS-server som stöder det protokollet.
>
> I den här konfigurationen fungerar inte enkelriktade SMS- och OATH-token eftersom MFA-servern inte kan initiera ett lyckat RADIUS-svar med hjälp av alternativa protokoll.

![RADIUS-autentisering i MFA Server](./media/howto-mfaserver-dir-radius/radius.png)

## <a name="add-a-radius-client"></a>Lägga till en RADIUS-klient

Om du vill konfigurera RADIUS-autentisering installerar du Azure Multi-Factor Authentication Server på en Windows-server. Om du har en Active Directory-miljö ansluter du servern till domänen i nätverket. Konfigurera Azure Multi-Factor Authentication Server genom att följa dessa steg:

1. Klicka på ikonen för RADIUS-autentisering på den vänstra menyn i Azure Multi-Factor Authentication Server.
2. Markera kryssrutan **Aktivera RADIUS-autentisering**.
3. På fliken Klienter ändrar du portarna för autentisering och kontohantering om Azure MFA RADIUS-tjänsten måste lyssna efter RADIUS-förfrågningar på andra portar än standardportarna.
4. Klicka på **Lägg till**.
5. Ange IP-adressen för enheten eller servern som ska autentisera mot Azure Multi-Factor Authentication-servern, ett programnamn (valfritt) och en delad hemlighet.

   Programnamnet visas i rapporter och kan visas i autentiseringsmeddelanden i SMS- eller mobilappar.

   Den delade hemligheten måste vara samma på både Azure Multi-Factor Authentication-servern och enheten eller servern.

6. Markera rutan om att **Multi-Factor Authentication-användarmatchning krävs** om alla användare har importerats till servern och om multifaktorautentisering används. Om ett stort antal användare inte har importerats till servern än, eller om de ska undantas från tvåstegsverifiering, lämnar du rutan avmarkerad.
7. Markera rutan **Aktivera OATH-reservtoken** om du vill använda OATH-lösenord från mobilverifieringsappar som reserv.
8. Klicka på **OK**.

Upprepa steg 4 till och med 8 och lägg till så många RADIUS-klienter som du behöver.

## <a name="configure-your-radius-client"></a>Konfigurera RADIUS-klienten

1. Klicka på fliken **Mål**.
   * Om Azure MFA-servern är installerad på en domänansluten server i en Active Directory miljö väljer du Windows- **domän**.
   * Om användarna ska autentiseras mot en LDAP-katalog väljer du **LDAP-bindning**.
      Välj ikonen för katalogintegrering och redigerar LDAP-konfigurationen på fliken Inställningar så att servern kan bindas till katalogen. Anvisningar för hur du konfigurerar LDAP finns i [LDAP-proxykonfigurationsguiden](howto-mfaserver-dir-ldap.md).
   * Om användarna ska autentiseras mot en annan RADIUS-server väljer du **RADIUS-server (er)**.
1. Klicka på **Lägg till** för att konfigurera servern som Azure MFA-servern ska vidarebefordra RADIUS-förfrågningarna till.
1. I dialogrutan Lägg till RADIUS-server anger du IP-adressen för RADIUS-servern och en delad hemlighet.

   Den delade hemligheten måste vara samma på både Azure Multi-Factor Authentication-servern och RADIUS-servern. Ändra autentiseringsporten och redovisningsporten om olika portar används av RADIUS-servern.

1. Klicka på **OK**.
1. Lägg till Azure MFA-servern som en RADIUS-klient på den andra RADIUS-servern så att den kan bearbeta åtkomstförfrågningar som Azure MFA-servern skickar till den. Använd samma delade hemlighet som konfigurerats i Azure Multi-Factor Authentication Server.

Upprepa dessa steg om du vill lägga till fler RADIUS-servrar. Använd knapparna **Flytta upp** och **Flytta ned** för att konfigurera i vilken ordning Azure MFA-servern anropar dem.

Du har konfigurerat Azure Multi-Factor Authentication-servern. Nu lyssnar servern på de konfigurerade portarna för RADIUS-åtkomstförfrågningar från de konfigurerade klienterna.

## <a name="radius-client-configuration"></a>RADIUS-klientkonfiguration

Följ dessa riktlinjer när du konfigurerar RADIUS-klienten:

* Konfigurera din installation/Server för att autentisera via RADIUS till Azures Multi-Factor Authentication-server IP-adress, som fungerar som RADIUS-server.
* Använd samma delade hemlighet som du konfigurerade ovan.
* Konfigurera RADIUS-timeout-värdet till 30-60 sekunder så att det finns tid att verifiera användarens autentiseringsuppgifter, utföra tvåstegsverifiering, ta emot sitt svar och svara på begäran om RADIUS-åtkomst.

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [integrerar med RADIUS-autentisering](howto-mfa-nps-extension.md) om du har Azure AD Multi-Factor Authentication i molnet. 
