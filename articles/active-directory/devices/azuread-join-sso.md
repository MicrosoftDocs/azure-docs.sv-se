---
title: Hur SSO till lokala resurser fungerar på Azure AD-anslutna enheter | Microsoft Docs
description: Lär dig hur du utökar SSO-upplevelsen genom att konfigurera hybrid Azure Active Directory anslutna enheter.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: ravenn
ms.collection: M365-identity-device-management
ms.openlocfilehash: da22a4e5e9ab13ec18347e58bea6cfc5f45333de
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98630708"
---
# <a name="how-sso-to-on-premises-resources-works-on-azure-ad-joined-devices"></a>Så fungerar enkel inloggning till lokala resurser på Azure AD-anslutna enheter

Det är förmodligen inte en överraskning att en Azure Active Directory (Azure AD) ansluten enhet ger dig en enkel inloggning (SSO) till klientens molnappar. Om din miljö har en lokal Active Directory (AD) kan du också få enkel inloggning på Azure AD-anslutna enheter till resurser och program som är beroende av lokala AD. 

Den här artikeln förklarar hur det fungerar.

## <a name="prerequisites"></a>Förutsättningar

Lokal inloggning kräver detaljerad kommunikation med dina lokala AD DS-domänkontrollanter. Om Azure AD-anslutna enheter inte är anslutna till din organisations nätverk krävs en VPN-eller annan nätverks infrastruktur. 

## <a name="how-it-works"></a>Så här fungerar det 

Med en Azure AD-ansluten enhet har användarna redan en SSO-upplevelse till molnappar i din miljö. Om din miljö har en Azure AD och en lokal AD kan du vilja expandera omfattningen av din SSO-upplevelse till dina lokala branschspecifika appar, fil resurser och skrivare.

Azure AD-anslutna enheter har ingen kunskap om din lokala AD-miljö eftersom de inte är anslutna till den. Du kan dock ange ytterligare information om din lokala AD till dessa enheter med Azure AD Connect.

Om du har en hybrid miljö, med både Azure AD och lokala AD, är det troligt att du redan har Azure AD Connect distribuerat för att synkronisera din lokala identitets information till molnet. Som en del av synkroniseringsprocessen synkroniserar Azure AD Connect lokala användare och domän information till Azure AD. När en användare loggar in på en Azure AD-ansluten enhet i en hybrid miljö:

1. Azure AD skickar information om användarens lokala domän tillbaka till enheten, tillsammans med den [primära uppdateringstoken](concept-primary-refresh-token.md)
1. Tjänsten Local Security Authority (LSA) möjliggör Kerberos-och NTLM-autentisering på enheten.

>[!NOTE]
> Windows Hello för företag kräver ytterligare konfiguration för att aktivera lokal SSO från en Azure AD-ansluten enhet. Mer information finns i [Konfigurera Azure AD-anslutna enheter för lokala Single-Sign på användning av Windows Hello för företag](/windows/security/identity-protection/hello-for-business/hello-hybrid-aadj-sso-base). 

Under ett åtkomst försök till en resurs som begär Kerberos eller NTLM i användarens lokala miljö, enheten:

1. Skickar lokal domän information och användarautentiseringsuppgifter till den lokaliserade DOMÄNKONTROLLANTen för att få användaren autentiserad.
1. Tar emot en Kerberos [-biljett för biljett beviljande biljetter (TGT)](/windows/desktop/secauthn/ticket-granting-tickets) eller NTLM-token baserat på det protokoll som stöds av den lokala resursen eller programmet. Om försöket att hämta Kerberos-TGT eller NTLM-token för domänen Miss lyckas (en DCLocator tids gräns kan orsaka en fördröjning), görs ett försök av Autentiseringshanteraren-poster, eller så kan användaren få ett popup-meddelande om att begära autentiseringsuppgifter för mål resursen.

Alla appar som har kon figurer ATS för **Windows-integrerad autentisering** får sömlös inloggning när en användare försöker komma åt dem.

## <a name="what-you-get"></a>Det här får du

Med enkel inloggning kan du på en Azure AD-ansluten enhet: 

- Få åtkomst till en UNC-sökväg på en AD-medlems Server
- Åtkomst till en AD-medlems server som kon figurer ATS för Windows-integrerad säkerhet 

Om du vill hantera din lokala AD från en Windows-enhet installerar du [verktyg för fjärrserveradministration för Windows 10](https://www.microsoft.com/download/details.aspx?id=45520).

Du kan använda:

- Snapin-modulen Active Directory användare och datorer (ADUC) för att administrera alla AD-objekt. Du måste dock ange den domän som du vill ansluta till manuellt.
- Snapin-modulen DHCP för att administrera en AD-ansluten DHCP-server. Du kan dock behöva ange DHCP-servernamnets namn eller adress.
 
## <a name="what-you-should-know"></a>Det här bör du veta

Du kan behöva justera din [domänbaserade filtrering](../hybrid/how-to-connect-sync-configure-filtering.md#domain-based-filtering) i Azure AD Connect för att säkerställa att data om de nödvändiga domänerna är synkroniserade.

Appar och resurser som är beroende av Active Directory datorns autentisering fungerar inte eftersom Azure AD-anslutna enheter inte har något dator objekt i AD. 

Du kan inte dela filer med andra användare på en Azure AD-ansluten enhet.

## <a name="next-steps"></a>Nästa steg

Mer information finns i [Vad är enhets hantering i Azure Active Directory?](overview.md) 
