---
title: Skydda PaaS-webb& mobila program
titleSuffix: Azure App Service
description: 'Lär dig mer om Azure App Service säkerhets metoder för att skydda dina PaaS webb-och mobil program. '
services: security
documentationcenter: na
author: techlake
manager: barbkess
editor: ''
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/18/2019
ms.author: terrylan
ms.openlocfilehash: 849743dd756eff27ec0670cc39d2419c60538183
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94701022"
---
# <a name="best-practices-for-securing-paas-web-and-mobile-applications-using-azure-app-service"></a>Metod tips för att skydda PaaS-webb program och mobilappar med hjälp av Azure App Service

I den här artikeln diskuterar vi en samling [Azure App Service](../../app-service/overview.md) rekommenderade säkerhets metoder för att skydda dina PaaS webb-och mobil program. Dessa bästa metoder härleds från vår erfarenhet av Azure och våra kunders upplevelser som du själv har.

Azure App Service är ett PaaS-erbjudande (Platform-as-a-Service) som gör att du kan skapa webb-och mobilappar för alla plattformar och enheter och ansluta till data var som helst, i molnet eller lokalt. App Service innehåller de webb-och mobil funktioner som tidigare levererades separat som Azure Websites och Azure Mobile Services. Det finns nya funktioner för att automatisera affärsprocesser och hantera moln-API:er. Som en enda integrerad tjänst ger App Service en omfattande uppsättning funktioner för webb-, mobil-och integrations scenarier.

## <a name="authenticate-through-azure-active-directory-ad"></a>Autentisera via Azure Active Directory (AD)
App Service tillhandahåller en OAuth 2,0-tjänst för din identitets leverantör. OAuth 2,0 fokuserar på Client Developer enkelhet samtidigt som du tillhandahåller vissa auktoriseringsarkiv för webb program, skriv bords program och mobil telefoner. Azure AD använder OAuth 2,0 för att ge åtkomst till mobil-och webb program. Mer information finns [i autentisering och auktorisering i Azure App Service](../../app-service/overview-authentication-authorization.md).

## <a name="restrict-access-based-on-role"></a>Begränsa åtkomst baserat på roll
Det är absolut nödvändigt att begränsa åtkomsten för organisationer som vill tillämpa säkerhets principer för data åtkomst. Du kan använda rollbaserad åtkomst kontroll i Azure (Azure RBAC) för att tilldela behörigheter till användare, grupper och program i ett visst omfång, till exempel behovet av att känna till och minst behörighets säkerhets principer. Mer information om hur du beviljar användare åtkomst till program finns i [Vad är rollbaserad åtkomst kontroll i Azure (Azure RBAC)](../../role-based-access-control/overview.md).

## <a name="protect-your-keys"></a>Skydda dina nycklar
Det spelar ingen roll om du förlorar dina prenumerations nycklar. Azure Key Vault hjälper dig att skydda krypteringsnycklar och hemligheter som används av molnprogram och molntjänster. Med Key Vault kan du kryptera nycklar och hemligheter (till exempel autentiseringsnyckel, lagrings konto nycklar, data krypterings nycklar). PFX-filer och lösen ord) med hjälp av nycklar som skyddas av HSM: er (Hardware Security modules). För ytterligare säkerhet kan du importera eller generera nycklar i HSM-moduler. Du kan också använda Key Vault för att hantera dina TLS-certifikat med automatisk förnyelse. Se [vad som är Azure Key Vault](../../key-vault/general/overview.md) om du vill veta mer.

## <a name="restrict-incoming-source-ip-addresses"></a>Begränsa inkommande käll-IP-adresser
[App Service miljöer](../../app-service/environment/intro.md) har en funktion för integrering av virtuella nätverk som hjälper dig att begränsa inkommande käll-IP-adresser via nätverks säkerhets grupper (NSG: er). Om du inte är bekant med Azure Virtual Networks (virtuella nätverk), är det här en funktion som gör att du kan placera många av dina Azure-resurser i ett nätverk som inte är Internet och som du styr åtkomsten till. Mer information finns i [integrera din app med en Azure-Virtual Network](../../app-service/web-sites-integrate-with-vnet.md).

För App Service i Windows kan du också begränsa IP-adresser dynamiskt genom att konfigurera web.config. Mer information finns i [dynamisk IP-säkerhet](/iis/configuration/system.webServer/security/dynamicIpSecurity/).


## <a name="next-steps"></a>Nästa steg
I den här artikeln har vi introducerat en samling av App Service säkerhets metoder för att skydda dina PaaS webb-och mobil program. Mer information om hur du skyddar dina PaaS-distributioner finns i:

- [Skydda PaaS-distributioner](paas-deployments.md)
- [Skydda PaaS-databaser i Azure](paas-applications-using-sql.md)