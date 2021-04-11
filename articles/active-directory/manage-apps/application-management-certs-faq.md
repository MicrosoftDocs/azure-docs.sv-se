---
title: Vanliga frågor och svar om Azure Active Directory program hanterings certifikat
description: Läs vanliga frågor och svar om hur du hanterar certifikat för appar som använder Azure Active Directory som identitets leverantör (IdP).
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: reference
ms.date: 03/19/2021
ms.author: kenwith
ms.reviewer: secherka, mifarca, shchaur, shravank, sureshja
ms.openlocfilehash: 928bf02e2d628379738483b40631e36f0f929176
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104803805"
---
# <a name="azure-active-directory-azure-ad-application-management-certificates-frequently-asked-questions"></a>Vanliga frågor och svar om program hanterings certifikat för Azure Active Directory (Azure AD)

På den här sidan besvaras vanliga frågor om att hantera certifikat för appar med hjälp av Azure Active Directory (Azure AD) som identitets leverantör (IdP).

## <a name="is-there-a-way-to-generate-a-list-of-expiring-saml-signing-certificates"></a>Finns det något sätt att generera en lista med förfallo datum för SAML-signerings certifikat?

Du kan exportera alla app-registreringar med utgångna hemligheter, certifikat och deras ägare för de angivna apparna från katalogen i en CSV-fil via [PowerShell-skript](app-management-powershell-samples.md). 

## <a name="where-can-i-find-the-information-about-soon-to-expire-certificates-renewal-steps"></a>Var hittar jag information om hur snart du ska upphöra med förnyelse av certifikat?

Du hittar anvisningar [här](manage-certificates-for-federated-single-sign-on.md#renew-a-certificate-that-will-soon-expire).

## <a name="how-can-i-customize-the-expiration-date-for-the-certificates-issued-by-azure-ad"></a>Hur kan jag anpassa utgångs datumet för certifikaten som utfärdas av Azure AD?

Som standard konfigurerar Azure AD ett certifikat så att det upphör att gälla efter tre år när det skapas automatiskt under konfigurationen av SAML enkel inloggning. Eftersom du inte kan ändra datum för ett certifikat när du har sparat det måste du skapa ett nytt certifikat. Anvisningar för hur du gör detta finns i [Anpassa förfallo datumet för ditt Federations certifikat och överföra det till ett nytt certifikat](manage-certificates-for-federated-single-sign-on.md#customize-the-expiration-date-for-your-federation-certificate-and-roll-it-over-to-a-new-certificate).

## <a name="how-can-i-automate-the-certificates-expiration-notifications"></a>Hur kan jag automatisera meddelanden om förfallo datum för certifikat?

Azure AD skickar ett e-postmeddelande 60, 30 och 7 dagar innan SAML-certifikatet upphör att gälla. Du kan lägga till fler än en e-postadress för att ta emot meddelanden. 

> [!NOTE]
> Du kan lägga till upp till 5 e-postadresser i meddelande listan (inklusive e-postadressen till den administratör som lade till programmet). Om du vill att fler personer ska meddelas använder du e-postmeddelandena för distributions listan. 

Om du vill ange de e-postmeddelanden som du vill att meddelandena ska skickas till, se [Lägg till e-postavisering om att certifikatet upphör att gälla](manage-certificates-for-federated-single-sign-on.md#add-email-notification-addresses-for-certificate-expiration).

Det finns inget alternativ för att redigera eller anpassa de här e-postaviseringarna som tas emot från `aadnotification@microsoft.com` . Du kan dock exportera registrerings program med utgångna hemligheter och certifikat med hjälp av [PowerShell-skript](app-management-powershell-samples.md).

## <a name="who-can-update-the-certificates"></a>Vem kan uppdatera certifikaten?

Ägaren till programmet eller den globala administratören eller program administratören kan uppdatera certifikaten genom Azure Portal användar gränssnitt, PowerShell eller Microsoft Graph.

## <a name="i-need-more-details-about-certificate-signing-options"></a>Jag behöver mer information om alternativ för certifikat signering.

I Azure AD kan du konfigurera alternativ för certifikat signering och algoritmen för certifikat signering. Mer information finns i [avancerade signerings alternativ för SAML-token för Azure AD-appar](certificate-signing-options.md).

## <a name="i-need-to-replace-the-certificate-for-azure-ad-application-proxy-applications-and-need-more-instructions"></a>Jag behöver ersätta certifikatet för Azure AD-programproxy-program och behöver fler instruktioner.

Om du vill ersätta certifikat för Azure AD-programproxy program, se [PowerShell-exempel – Ersätt certifikat i Application Proxy-appar](scripts/powershell-get-custom-domain-replace-cert.md).

## <a name="how-do-i-manage-certificates-for-custom-domains-in-azure-ad-application-proxy"></a>Hur gör jag för att hantera certifikat för anpassade domäner i Azure AD-programproxy?

Om du vill konfigurera en lokal app för att använda en anpassad domän behöver du en verifierad Azure Active Directory anpassad domän, ett PFX-certifikat för den anpassade domänen och en lokal app att konfigurera. Mer information finns i [anpassade domäner i Azure AD-programproxy](application-proxy-configure-custom-domain.md). 

## <a name="i-need-to-update-the-token-signing-certificate-on-the-application-side-where-can-i-get-it-on-azure-ad-side"></a>Jag behöver uppdatera token signerings certifikatet på program sidan. Var kan jag få det på Azure AD-Sidan?

Du kan förnya ett SAML X. 509-certifikat, se [SAML-signerings certifikat](configure-saml-single-sign-on.md#saml-signing-certificate).

## <a name="what-is-azure-ad-signing-key-rollover"></a>Vad är förnyelse av Azure AD-signeringscertifikat?

Du hittar mer information [här](../develop/active-directory-signing-key-rollover.md). 

## <a name="how-do-i-renew-application-token-encryption-certificate"></a>Hur gör jag för att förnya krypterings certifikat för programtoken?

Information om hur du förnyar ett certifikat för certifikat-token finns i [förnya ett token Encryption-certifikat för ett företags program](howto-saml-token-encryption.md). 

## <a name="how-do-i-renew-application-token-signing-certificate"></a>Hur gör jag för att förnya signerings certifikat för programtoken?

Information om hur du förnyar ett certifikat för tokensignering finns i [så här förnyar du ett token signerings certifikat för ett företags program](manage-certificates-for-federated-single-sign-on.md).

## <a name="how-do-i-update-azure-ad-after-changing-my-federation-certificates"></a>Hur gör jag för att uppdatera Azure AD när du har ändrat mitt Federations certifikat?

Om du vill uppdatera Azure AD när du har ändrat dina Federations certifikat, se [förnya Federations certifikat för Microsoft 365 och Azure Active Directory](../hybrid/how-to-connect-fed-o365-certs.md).
