---
title: Webb tjänsten Azure MFA Server Mobile App – Azure Active Directory
description: Konfigurera MFA-servern till att skicka push-meddelanden till användare med Microsoft Authenticator-appen.
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
ms.openlocfilehash: 6263491ce5b319c3968c542ebbaf00294c5152cd
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96742433"
---
# <a name="enable-mobile-app-authentication-with-azure-multi-factor-authentication-server"></a>Aktivera mobilappautentisering och Azure Multi-Factor Authentication Server

Microsoft Authenticator-appen erbjuder ytterligare ett verifieringsalternativ utanför IP-nätverket. I stället för att placera ett automatiserat telefonsamtal eller SMS till användaren vid inloggningen skickar Azure Multi-Factor Authentication ett meddelande till Microsoft Authenticator-appen på användarens smartphone eller surfplatta. Användaren trycker bara på **Verifiera** (eller anger en PIN-kod och trycker på "autentisera") i appen för att slutföra inloggningen.

Att använda en mobilapp för tvåstegsverifiering rekommenderas när mobilmottagningen är opålitlig. Om du använder appen som en OATH-tokengenerator behövs ingen nätverks- eller internetanslutning.

> [!IMPORTANT]
> Från den 1 juli 2019 erbjuder Microsoft inte längre MFA-Server för nya distributioner. Nya kunder som vill kräva Multi-Factor Authentication (MFA) vid inloggnings händelser bör använda molnbaserad Azure AD-Multi-Factor Authentication.
>
> Information om hur du kommer igång med molnbaserad MFA finns i [Självstudier: skydda användar inloggnings händelser med Azure AD Multi-Factor Authentication](tutorial-enable-azure-mfa.md).
>
> Befintliga kunder som aktiverade MFA server före den 1 juli 2019 kan ladda ned den senaste versionen, framtida uppdateringar och generera autentiseringsuppgifter för aktivering som vanligt.

> [!IMPORTANT]
> Om du har installerat Azure Multi-Factor Authentication Server v8.x eller senare krävs inte de flesta av de nedanstående stegen. Mobilappsautentisering kan konfigureras med anvisningarna under [Konfigurera mobilappen](#configure-the-mobile-app-settings-in-the-azure-multi-factor-authentication-server).

## <a name="requirements"></a>Krav

För att använda Microsoft Authenticator-appen måste du köra Multi-Factor Authentication Server v8.x eller senare

## <a name="configure-the-mobile-app-settings-in-the-azure-multi-factor-authentication-server"></a>Konfigurera Mobile App-inställningarna i Azure Multi-Factor Authentication Server

1. I Multi-Factor Authentication-servern klickar du på ikonen Användarportal. Om användarna ska kunna styra sina autentiseringsmetoder markerar du **Mobilapp** under **Tillåt användare att välja metod** på fliken Inställningar. Om den här funktionen inte är aktiverad måste slutanvändarna kontakta din supportavdelning för att slutföra aktiveringen av mobilappen.
2. Markera kryssrutan **Tillåt användare att aktivera mobilapp**.
3. Markera kryssrutan **Tillåt användarregistrering**.
4. Klicka på ikonen för **Mobilapp**.
5. Fyll i fältet **Kontonamn** med företagets eller organisationens namn som ska visas i kontots mobilapp.
   ![MFA-serverkonfiguration för mobilappinställningar](./media/howto-mfaserver-deploy-mobileapp/mobile.png)

## <a name="next-steps"></a>Nästa steg

- [Avancerade scenarier med Azure Multi-Factor Authentication och VPN från tredje part](howto-mfaserver-nps-vpn.md).
