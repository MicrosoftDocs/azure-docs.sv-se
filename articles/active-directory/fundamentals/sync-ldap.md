---
title: LDAP-synkronisering med Azure Active Directory
description: Arkitektur vägledning för att uppnå LDAP-synkronisering med Azure Active Directory.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/10/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1f34e734b315c7c05ce77f5e168a452fc1c1c547
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96168686"
---
# <a name="ldap-synchronization-with-azure-active-directory"></a>LDAP-synkronisering med Azure Active Directory

LDAP (Lightweight Directory Access Protocol) är ett katalog tjänst protokoll som körs på TCP/IP-stacken. Den innehåller en mekanism som används för att ansluta till, söka och ändra Internet kataloger. LDAP-katalogtjänsten är baserad på en klient-server modell och dess funktion är att ge åtkomst till en befintlig katalog. Många företag är beroende av lokala LDAP-servrar för att lagra användare och grupper för sina kritiska företags-appar. 

Azure Active Directory (Azure AD) kan ersätta LDAP-synkronisering med Azure AD Connect. Tjänsten Azure AD Connect-synkronisering utför alla åtgärder som är relaterade till synkronisering av identitets data mellan dina lokala miljöer och Azure AD. 

## <a name="use-when"></a>Använd när

Du måste synkronisera identitets data mellan dina lokala LDAP v3-kataloger och Azure AD. 

![arkitektur diagram](./media/authentication-patterns/ldap-sync.png)

## <a name="components-of-system"></a>System komponenter

* **Användare**: använder ett program som förlitar sig på användningen av en LDAP v3-katalog för att sortera användare och lösen ord.

* **Webbläsare**: komponenten som användaren interagerar med för att få åtkomst till den externa URL: en för programmet

* **Webbapp**: program med beroenden på LDAP v3-kataloger.

* **Azure AD**: Azure AD synkroniserar identitets information (användare, grupper, lösen ord) från organisationens lokala LDAP-kataloger via Azure AD Connect. 

* **Azure AD Connect**: är ett verktyg för att ansluta lokala identitets infrastrukturer till Microsoft Azure AD. Guiden och de guidade upplevelserna hjälper till att distribuera och konfigurera krav och komponenter som krävs för anslutningen. 

* **Anpassad anslutning**: en allmän LDAP-anslutning gör att du kan integrera Azure AD Connect synkroniseringstjänst med en LDAP v3-Server. Den finns på Azure AD Connect.

* **Active Directory**: Active Directory är en katalog tjänst som ingår i de flesta Windows Server-operativsystem. Servrar som kör Active Directory Directory-tjänster kallas domänkontrollanter och de autentiserar och auktoriserar alla användare och datorer i en Windows-domän.

* **LDAP v3-Server**: LDAP-protokoll – kompatibel katalog som lagrar företags användare och lösen ord som används för Directory Services-autentisering.

## <a name="implement-ldap-synchronization-with-azure-ad"></a>Implementera LDAP-synkronisering med Azure AD

* [Verktyg för katalog integrering med hybrid identitet](../hybrid/plan-hybrid-identity-design-considerations-tools-comparison.md) 

* [Azure AD Connect installations översikt](../hybrid/how-to-connect-install-roadmap.md) 

* [Översikt och skapa en LDAP-koppling](/microsoft-identity-manager/reference/microsoft-identity-manager-2016-connector-genericldap) 

   > [!NOTE]
   > LDAP-anslutningar är en avancerad konfiguration som kräver en viss välbekanthet med Forefront Identity Manager och/eller Microsoft Identity Manager. Om det används i produktion, rekommenderar vi frågor om den här konfigurationen att gå via [Premier Support](https://support.microsoft.com/premier) eller Microsoft Partner Network.

