---
title: Azure Active Directory vanliga frågor och svar om certifikat för programhantering
description: Få svar på vanliga frågor och svar om att hantera certifikat för appar med hjälp Azure Active Directory en identitetsprovider (IdP).
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: reference
ms.date: 03/19/2021
ms.author: iangithinji
ms.reviewer: secherka, mifarca, shchaur, shravank, sureshja
ms.openlocfilehash: 0868c942a023662a1a6d3053477d85b0245fef4b
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107376247"
---
# <a name="azure-active-directory-azure-ad-application-management-certificates-frequently-asked-questions"></a>Azure Active Directory (Azure AD) Programhanteringscertifikat – vanliga frågor och svar

Den här sidan besvarar vanliga frågor om att hantera certifikat för appar med hjälp Azure Active Directory (Azure AD) som identitetsprovider (IdP).

## <a name="is-there-a-way-to-generate-a-list-of-expiring-saml-signing-certificates"></a>Finns det något sätt att generera en lista över utgående SAML-signeringscertifikat?

Du kan exportera alla appregistreringar med utgångna hemligheter, certifikat och deras ägare för de angivna apparna från din katalog i en CSV-fil via [PowerShell-skript](app-management-powershell-samples.md). 

## <a name="where-can-i-find-the-information-about-soon-to-expire-certificates-renewal-steps"></a>Var hittar jag information om att snart förfalla förnyelsesteg för certifikat?

Du hittar anvisningar [här](manage-certificates-for-federated-single-sign-on.md#renew-a-certificate-that-will-soon-expire).

## <a name="how-can-i-customize-the-expiration-date-for-the-certificates-issued-by-azure-ad"></a>Hur anpassar jag förfallodatumet för de certifikat som utfärdats av Azure AD?

Som standard konfigurerar Azure AD ett certifikat så att det upphör att gälla efter tre år när det skapas automatiskt under saml-konfiguration med enkel inloggning. Eftersom du inte kan ändra datumet för ett certifikat när du har sparat det måste du skapa ett nytt certifikat. Anvisningar för hur du gör detta finns i Anpassa förfallodatumet för federationscertifikatet [och återställa det till ett nytt certifikat.](manage-certificates-for-federated-single-sign-on.md#customize-the-expiration-date-for-your-federation-certificate-and-roll-it-over-to-a-new-certificate)

## <a name="how-can-i-automate-the-certificates-expiration-notifications"></a>Hur automatiserar jag meddelanden om förfallodatum för certifikat?

Azure AD skickar ett e-postmeddelande 60, 30 och 7 dagar innan SAML-certifikatet upphör att gälla. Du kan lägga till fler än en e-postadress för att ta emot meddelanden. 

> [!NOTE]
> Du kan lägga till upp till 5 e-postadresser i meddelandelistan (inklusive e-postadressen för den administratör som lade till programmet). Om du vill att fler personer ska meddelas kan du använda e-postmeddelanden i distributionslistan. 

Information om vilka e-postmeddelanden du vill att meddelandena ska skickas till finns i Lägga till [e-postadresser för förfallodatum för certifikatet.](manage-certificates-for-federated-single-sign-on.md#add-email-notification-addresses-for-certificate-expiration)

Det finns inget alternativ för att redigera eller anpassa de här e-postmeddelandena som tas emot från `aadnotification@microsoft.com` . Du kan dock exportera appregistreringar med utgångna hemligheter och certifikat via [PowerShell-skript.](app-management-powershell-samples.md)

## <a name="who-can-update-the-certificates"></a>Vem kan uppdatera certifikaten?

Ägaren av programmet, den globala administratören eller programadministratören kan uppdatera certifikaten via Azure Portal användargränssnitt, PowerShell eller Microsoft Graph.

## <a name="i-need-more-details-about-certificate-signing-options"></a>Jag behöver mer information om alternativ för certifikatsignering.

I Azure AD kan du konfigurera alternativ för certifikatsignering och certifikatsigneringsalgoritmen. Mer information finns i Avancerade [signeringsalternativ för SAML-tokencertifikat för Azure AD-appar.](certificate-signing-options.md)

## <a name="i-need-to-replace-the-certificate-for-azure-ad-application-proxy-applications-and-need-more-instructions"></a>Jag behöver ersätta certifikatet för Azure AD Programproxy program och behöver fler instruktioner.

Information om hur du ersätter certifikat för Azure AD Programproxy program finns [i PowerShell-exempel – Ersätta certifikat i Programproxy appar.](scripts/powershell-get-custom-domain-replace-cert.md)

## <a name="how-do-i-manage-certificates-for-custom-domains-in-azure-ad-application-proxy"></a>Hur gör jag för att hantera certifikat för anpassade domäner i Azure AD Programproxy?

För att konfigurera en lokal app att använda en anpassad domän behöver du en verifierad anpassad Azure Active Directory-domän, ett PFX-certifikat för den anpassade domänen och en lokal app som ska konfigureras. Mer information finns i Anpassade [domäner i Azure AD Programproxy](application-proxy-configure-custom-domain.md). 

## <a name="i-need-to-update-the-token-signing-certificate-on-the-application-side-where-can-i-get-it-on-azure-ad-side"></a>Jag behöver uppdatera certifikatet för tokensignering på programsidan. Var hittar jag det på Azure AD-sidan?

Du kan förnya ett SAML X.509-certifikat. Se [SAML-signeringscertifikat.](configure-saml-single-sign-on.md#saml-signing-certificate)

## <a name="what-is-azure-ad-signing-key-rollover"></a>Vad är rollover för Azure AD-signeringsnyckel?

Du hittar mer information [här.](../develop/active-directory-signing-key-rollover.md) 

## <a name="how-do-i-renew-application-token-encryption-certificate"></a>Hur gör jag för att förnya krypteringscertifikatet för programtoken?

Information om hur du förnyar ett krypteringscertifikat för programtoken finns i [Förnya ett certifikat för tokenkryptering för ett företagsprogram.](howto-saml-token-encryption.md) 

## <a name="how-do-i-renew-application-token-signing-certificate"></a>Hur gör jag för att förnya signeringscertifikat för programtoken?

Information om hur du förnyar ett signeringscertifikat för [programtoken finns i Förnya ett certifikat för tokensignering för ett företagsprogram.](manage-certificates-for-federated-single-sign-on.md)

## <a name="how-do-i-update-azure-ad-after-changing-my-federation-certificates"></a>Hur gör jag för att jag uppdatera Azure AD när jag har ändrat mina federationscertifikat?

Information om hur du uppdaterar Azure AD när du har ändrat dina federationscertifikat finns i [Förnya federationscertifikat för Microsoft 365 och Azure Active Directory](../hybrid/how-to-connect-fed-o365-certs.md).
