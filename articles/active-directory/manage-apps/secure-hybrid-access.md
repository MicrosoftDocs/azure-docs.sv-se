---
title: Azure AD Secure hybrid-åtkomst | Microsoft Docs
description: I den här artikeln beskrivs partner lösningar för att integrera ditt äldre lokala, offentliga moln eller privata moln program med Azure AD. Skydda dina äldre appar genom att ansluta program leverans styrenheter eller nätverk till Azure AD.
services: active-directory
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 2/16/2021
ms.author: gasinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 06ed37b914c352d7162db7f8409ac09b7615da47
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2021
ms.locfileid: "102174435"
---
# <a name="secure-hybrid-access-secure-legacy-apps-with-azure-active-directory"></a>Säker hybrid åtkomst: säkra äldre appar med Azure Active Directory

Nu kan du skydda dina lokala och molnbaserade program för äldre autentisering genom att ansluta dem till Azure Active Directory (AD) med:

- [Azure-AD-programproxy](#secure-hybrid-access-sha-through-azure-ad-application-proxy)

- [Dina befintliga program leverans styrenheter och nätverk](#sha-through-networking-and-delivery-controllers)

- [Program för virtuella privata nätverk (VPN) och Software-Defined perimeter (SDP)](#sha-through-vpn-and-sdp-applications)

Du kan överbrygga luckan och förstärka din säkerhets position över alla program med Azure AD-funktioner som Azure AD- [villkorlig åtkomst](../conditional-access/overview.md) och Azure AD [Identity Protection](../identity-protection/overview-identity-protection.md).

## <a name="secure-hybrid-access-sha-through-azure-ad-application-proxy"></a>Säker hybrid åtkomst (SHA) via Azure AD-programproxy
  
Med [Application Proxy](./what-is-application-proxy.md) kan du ge [säker fjärråtkomst](./application-proxy.md) till dina lokala webb program. Användarna behöver inte använda en VPN. Användare drar nytta av att enkelt ansluta till sina program från valfri enhet efter en [enkel inloggning](./add-application-portal-setup-sso.md). Programproxyn tillhandahåller fjärråtkomst som en tjänst och gör att du [enkelt kan publicera dina lokala program](./application-proxy-add-on-premises-application.md) till användare utanför företags nätverket. Det hjälper dig att skala din hantering av moln åtkomst utan att du behöver ändra dina lokala program. [Planera en Azure AD-programproxy-distribution](./application-proxy-deployment-plan.md) som nästa steg.

## <a name="azure-ad-partner-integrations"></a>Integrering av Azure AD-partner

### <a name="sha-through-networking-and-delivery-controllers"></a>SHA via nätverk och leverans kontroller

Förutom [Azure AD-programproxy](./what-is-application-proxy.md)så att du kan använda det här [ramverket utan förtroende](https://www.microsoft.com/security/blog/2020/04/02/announcing-microsoft-zero-trust-assessment-tool/), och Microsoft samarbetar med leverantörer från tredje part. Du kan använda dina befintliga nätverks-och leverans styrenheter och enkelt skydda äldre program som är viktiga för dina affärs processer, men som du inte kunde skydda tidigare med Azure AD. Det är troligt att du redan har allt du behöver för att börja skydda programmen.

![Bild som visar säker hybrid åtkomst med nätverks partners och App proxy](./media/secure-hybrid-access/secure-hybrid-access.png)

Följande nätverks leverantörer erbjuder färdiga lösningar och detaljerad vägledning för integrering med Azure AD.

- [Akamai Enterprise Application Access (EAA)](https://docs.microsoft.com/azure/active-directory/saas-apps/akamai-tutorial)

- [Citrix Application Delivery Controller (ADC)](https://docs.microsoft.com/azure/active-directory/saas-apps/citrix-netscaler-tutorial)

- [F5 Big-IP APM](https://docs.microsoft.com/azure/active-directory/manage-apps/f5-aad-integration)

- [Kemp:](https://docs.microsoft.com/azure/active-directory/saas-apps/kemp-tutorial)

- [Pulse Secure Virtual Traffic Manager (VTM)](https://docs.microsoft.com/azure/active-directory/saas-apps/pulse-secure-virtual-traffic-manager-tutorial)

### <a name="sha-through-vpn-and-sdp-applications"></a>SHA via VPN och SDP-program

Med hjälp av VPN-och SDP-lösningar kan du när som helst tillhandahålla säker åtkomst till företagets nätverk från vilken enhet som helst, oavsett var de befinner sig och skyddar din organisations data. Genom att ha Azure AD som en identitetsprovider (IDP) kan du använda moderna autentiserings-och auktoriseringsregler som Azure AD [enkel inloggning](./what-is-single-sign-on.md) och [Multi-Factor Authentication](../authentication/concept-mfa-howitworks.md) för att skydda dina lokala äldre program.  

![Bild som visar säker hybrid åtkomst med VPN-partners och App proxy ](./media/secure-hybrid-access/app-proxy-vpn.png)

Följande VPN-leverantörer erbjuder färdiga lösningar och detaljerad vägledning för integrering med Azure AD.

- [Cisco AnyConnect](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-anyconnect)

- [Fortinet](https://docs.microsoft.com/azure/active-directory/saas-apps/fortigate-ssl-vpn-tutorial)

- [F5 Big-IP APM](https://docs.microsoft.com/azure/active-directory/manage-apps/f5-aad-password-less-vpn)

- [Palo-nätverk global Protect](https://docs.microsoft.com/azure/active-directory/saas-apps/paloaltoadmin-tutorial)

- [Pulse Secure Pulse Connect Secure (PC)](https://docs.microsoft.com/azure/active-directory/saas-apps/pulse-secure-pcs-tutorial)

Följande SDP-leverantörer erbjuder färdiga lösningar och detaljerad vägledning för integrering med Azure AD.

- [Datawiza åtkomst utjämning](https://docs.microsoft.com/azure/active-directory/manage-apps/add-application-portal-setup-oidc-sso)

- [Perimeter 81](https://docs.microsoft.com/azure/active-directory/saas-apps/perimeter-81-tutorial#:~:text=For%20SSO%20to%20work,%20you%20need%20to%20establish,to%20test%20Azure%20AD%20single%20sign-on%20with%20B.Simon.)

- [Plattform för Silverfort-autentisering](https://docs.microsoft.com/azure/active-directory/manage-apps/add-application-portal-setup-oidc-sso)

- [Strata](https://docs.microsoft.com/azure/active-directory/saas-apps/maverics-identity-orchestrator-saml-connector-tutorial)

- [Zscaler Private Access (ZPA)](https://docs.microsoft.com/azure/active-directory/saas-apps/zscalerprivateaccess-tutorial)
