---
title: Azure AD Connect och Federation | Microsoft Docs
description: Den här sidan är den centrala platsen för all dokumentation om AD FS åtgärder som använder Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: f9107cf5-0131-499a-9edf-616bf3afef4d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 10/09/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: a97142e0c512f4f95235ad08c94c852906d3efd8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92095864"
---
# <a name="azure-ad-connect-and-federation"></a>Azure AD Connect och federation
Med Azure Active Directory (Azure AD) Connect kan du konfigurera Federation med lokala Active Directory Federation Services (AD FS) (AD FS) och Azure AD. Med Federations inloggning kan du göra det möjligt för användarna att logga in på Azure AD-baserade tjänster med sina lokala lösen ord – och, i företags nätverket, utan att behöva ange sina lösen ord igen. Genom att använda Federations alternativet med AD FS kan du distribuera en ny installation av AD FS, eller så kan du ange en befintlig installation i en Windows Server 2012 R2-grupp.

Det här avsnittet är start för information om de Federations funktioner som är relaterade till Azure AD Connect. Den listar länkar till alla närliggande ämnen. Länkar till Azure AD Connect finns i [integrera dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md).

## <a name="azure-ad-connect-federation-topics"></a>Azure AD Connect: Federations ämnen
| Avsnitt | Vad det täcker och när det ska läsas |
|:--- |:--- |
| **Azure AD Connect user sign-in options** (Alternativ för användarinloggning i Azure AD Connect) | |
| [Förstå användar inloggnings alternativ](plan-connect-user-signin.md) |Lär dig mer om olika användar inloggnings alternativ och hur de påverkar användar upplevelsen i Azure-inloggningen. |
| **Installera AD FS med Azure AD Connect** | |
| [Förutsättningar](how-to-connect-install-custom.md#ad-fs-configuration-prerequisites) |Se kraven för en lyckad AD FS installation via Azure AD Connect. |
| [Konfigurera en AD FS server grupp](how-to-connect-install-custom.md#configuring-federation-with-ad-fs) |Installera en ny AD FS server grupp med hjälp av Azure AD Connect. |
| [Federera med Azure AD med hjälp av alternativt inloggnings-ID](how-to-connect-fed-management.md#alternateid) | Konfigurera Federation med hjälp av alternativt inloggnings-ID  |
| **Ändra AD FS-konfigurationen** | |
| [Reparera förtroendet](how-to-connect-fed-management.md#repairthetrust) |Reparera det aktuella förtroendet mellan lokala AD FS och Microsoft 365/Azure. |
| [Lägg till en ny AD FS server](how-to-connect-fed-management.md#addadfsserver) |Expandera en AD FS server grupp med ytterligare en AD FS server efter den första installationen. |
| [Lägg till en ny AD FS WAP-server](how-to-connect-fed-management.md#addwapserver) |Expandera en AD FS server grupp med ytterligare en server för webbprogramproxy (WAP) efter den första installationen. |
| [Lägg till en ny federerad domän](how-to-connect-fed-management.md#addfeddomain) |Lägg till en annan domän som ska federerades med Azure AD. |
| [Uppdatera TLS/SSL-certifikatet](how-to-connect-fed-ssl-update.md)| Uppdatera TLS/SSL-certifikatet för en AD FS server grupp. |
| [Förnya Federations certifikat för Microsoft 365 och Azure AD](how-to-connect-fed-o365-certs.md)|Förnya ditt O365-certifikat med Azure AD.|
| **Annan Federations konfiguration** | |
| [Federera flera instanser av Azure AD med en enda instans av AD FS](how-to-connect-fed-single-adfs-multitenant-federation.md) | Federera flera Azure AD med en enda AD FS server grupp| 
| [Lägg till en anpassad företags logo typ/bild](how-to-connect-fed-management.md#customlogo) |Ändra inloggnings upplevelsen genom att ange den anpassade logo typ som visas på AD FS inloggnings sida. |
| [Lägg till en inloggnings Beskrivning](how-to-connect-fed-management.md#addsignindescription) |Ändra inloggnings beskrivningen på AD FS inloggnings sida. |
| [Ändra AD FS anspråks regler](how-to-connect-fed-management.md#modclaims) |Ändra eller Lägg till anspråks regler i AD FS som motsvarar Azure AD Connect Sync-konfiguration. |


## <a name="additional-resources"></a>Ytterligare resurser
* [Federera två Azure AD med enkla AD FS](how-to-connect-fed-single-adfs-multitenant-federation.md)
* [AD FS-distribution i Azure](/windows-server/identity/ad-fs/deployment/how-to-connect-fed-azure-adfs)
* [Hög tillgänglighet över geografisk AD FS distribution i Azure med Azure Traffic Manager](/windows-server/identity/ad-fs/deployment/active-directory-adfs-in-azure-with-azure-traffic-manager)