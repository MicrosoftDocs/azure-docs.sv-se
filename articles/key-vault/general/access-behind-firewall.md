---
title: Åtkomst Key Vault bakom en brand vägg – Azure Key Vault | Microsoft Docs
description: Läs om de portar, värdar eller IP-adresser som ska öppnas för att aktivera ett nyckel valvs klient program bakom en brand vägg för att få åtkomst till ett nyckel valv.
services: key-vault
author: amitbapat
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 08/12/2019
ms.author: ambapat
ms.openlocfilehash: 1ab5ae7bf9f1d13458e3bbeeec564fe642eb3303
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2020
ms.locfileid: "88588736"
---
# <a name="access-azure-key-vault-behind-a-firewall"></a>Få åtkomst till Azure Key Vault bakom en brandvägg

## <a name="what-ports-hosts-or-ip-addresses-should-i-open-to-enable-my-key-vault-client-application-behind-a-firewall-to-access-key-vault"></a>Vilka portar, värdar eller IP-adresser ska jag öppna för att aktivera mitt Key Vault-klientcertifikat bakom en brand vägg för att få åtkomst till Key Vault?

För att få åtkomst till ett nyckelvalv måste ditt klientprogram ha åtkomst till flera slutpunkter för olika funktioner:

* Autentisering via Azure Active Directory (Azure AD).
* Hantering av Azure Key Vault. Detta omfattar att skapa, läsa, uppdatera, ta bort och ange åtkomstprinciper via Azure Resource Manager.
* Åtkomst till och hantering av objekt (nycklar och hemligheter) som lagras i själva nyckelvalvet går via den specifika slutpunkten för nyckelvalv (t.ex. `https://yourvaultname.vault.azure.net`).  

Det finns vissa varianter beroende på din konfiguration och miljö.

## <a name="ports"></a>Portar

All trafik till nyckelvalvet för alla tre funktioner (autentisering, hantering och dataplanåtkomst) går över HTTPS: port 443. Det är dock ibland HTTP (port 80)-trafik för CRL. Klienter som har stöd för OCSP ska inte komma åt CRL, men kan ibland komma fram [http://cdp1.public-trust.com/CRL/Omniroot2025.crl](http://cdp1.public-trust.com/CRL/Omniroot2025.crl) .  

## <a name="authentication"></a>Autentisering

Nyckelvalv-klientprogram behöver åtkomst till Azure Active Directory-slutpunkter för autentisering. Den slutpunkt som används beror på Azure AD-klientkonfiguration, typ av huvudkonto (användarens eller tjänstens huvudnamn) och typ av konto, t.ex. ett Microsoft-konto eller ett arbets- eller skolkonto.  

| Typ av huvudkonto | Slutpunkt:port |
| --- | --- |
| Användare som använder Microsoft-konto<br> (till exempel user@hotmail.com) |**EAN**<br> login.microsoftonline.com:443<br><br> **Azure Kina:**<br> login.chinacloudapi.cn:443<br><br>**Azure amerikanska myndigheter:**<br> login.microsoftonline.us:443<br><br>**Azure Germany:**<br> login.microsoftonline.de:443<br><br> och <br>login.live.com:443 |
| Användarens eller tjänstens huvudkonto med ett arbets- eller skolkonto med Azure AD (t.ex. user@contoso.com) |**EAN**<br> login.microsoftonline.com:443<br><br> **Azure Kina:**<br> login.chinacloudapi.cn:443<br><br>**Azure amerikanska myndigheter:**<br> login.microsoftonline.us:443<br><br>**Azure Germany:**<br> login.microsoftonline.de:443 |
| Användarens eller tjänstens huvudkonto med ett arbets- eller skolkonto, plus Active Directory Federation Services (AD FS) eller annan federerad slutpunkt (t.ex. user@contoso.com) |Alla slutpunkter för ett arbets- eller skolkonto, plus AD FS eller andra federerade slutpunkter |

Det finns andra möjliga avancerade scenarier. Se [Azure Active Directory Authentication Flow](../../active-directory/develop/authentication-scenarios.md), [Integrera program med Azure Active Directory](../../active-directory/develop/active-directory-how-to-integrate.md) och [Active Directory-autentiseringsprotokoll](https://msdn.microsoft.com/library/azure/dn151124.aspx) för mer information.  

## <a name="key-vault-management"></a>Hantering av Nyckelvalv

För hantering av Nyckelvalv (CRUD och inställning av åtkomstprincip) måste klientprogrammet för nyckelvalv ha åtkomst till Azure Resource Manager-slutpunkten.  

| Typ av åtgärd | Slutpunkt:port |
| --- | --- |
| Kontrollplanåtgärder för Nyckelvalv<br> via Azure Resource Manager |**EAN**<br> management.azure.com:443<br><br> **Azure Kina:**<br> management.chinacloudapi.cn:443<br><br> **Azure amerikanska myndigheter:**<br> management.usgovcloudapi.net:443<br><br> **Azure Germany:**<br> management.microsoftazure.de:443 |
| Microsoft Graph API |**EAN**<br> graph.microsoft.com:443<br><br> **Azure Kina:**<br> graph.chinacloudapi.cn:443<br><br> **Azure amerikanska myndigheter:**<br> graph.microsoft.com:443<br><br> **Azure Germany:**<br> graph.cloudapi.de:443 |

## <a name="key-vault-operations"></a>Åtgärder i Nyckelvalv

Vid all hantering och kryptografiska åtgärder för nyckelvalvsobjekt (nycklar och hemligheter) måste klientprogrammet för nyckelvalv ha åtkomst till slutpunkten för nyckelvalvet. Slutpunktens DNS-suffix är olika beroende på placeringen av ditt nyckelvalv. Slutpunkten för Nyckelvalv har följande format: *valvnamn*.*regionsspecifikt dns-suffix* som beskrivs i följande tabell.  

| Typ av åtgärd | Slutpunkt:port |
| --- | --- |
| Åtgärder inklusive kryptografiska åtgärder på nycklar, skapa, läsa, uppdatera och ta bort nycklar och hemligheter, ställa in eller få taggar och andra attribut för nyckelvalvobjekt (nycklar eller hemligheter) |**EAN**<br> &lt;vault-name&gt;.vault.azure.net:443<br><br> **Azure Kina:**<br> &lt;vault-name&gt;.vault.azure.cn:443<br><br> **Azure amerikanska myndigheter:**<br> &lt;vault-name&gt;.vault.usgovcloudapi.net:443<br><br> **Azure Germany:**<br> &lt;vault-name&gt;.vault.microsoftazure.de:443 |

## <a name="ip-address-ranges"></a>IP-adressintervall

Key Vault-tjänsten använder andra Azure-resurser, som PaaS-infrastruktur. Därför är det inte möjligt att ange ett specifikt IP-adressintervall som Key Vault-tjänstens slutpunkter har vid given tidpunkt. Om din brand vägg bara stöder IP-adressintervall, se Microsoft Azure Data Center IP Range-dokument som finns på:
* [Offentlig](https://www.microsoft.com/en-us/download/details.aspx?id=56519)
* [US Gov](https://www.microsoft.com/en-us/download/details.aspx?id=57063)
* [Tyskland](https://www.microsoft.com/en-us/download/details.aspx?id=57064)
* [Kina](https://www.microsoft.com/en-us/download/details.aspx?id=57062)

Autentisering och identiteter (Azure Active Directory) är en global tjänst och växlas över till andra regioner eller flytta trafik utan föregående meddelande. I det här scenariot alla IP-adressintervall som anges i [ IP-adresser för autentisering och identitet](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2#bkmk_identity_ip) läggas till i brandväggen.

## <a name="next-steps"></a>Nästa steg

Om du har frågor om Key Vault går du till [sidan Microsoft Q&en fråga för Azure Key Vault](https://docs.microsoft.com/answers/topics/azure-key-vault.html).
