---
title: Vad är Azure AD Connect Cloud Sync. | Microsoft Docs
description: Beskriver Azure AD Connect Cloud Sync.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 12/11/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: a3b880d70baf7c160f8235bd01a5000f83825c36
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98614864"
---
# <a name="what-is-azure-ad-connect-cloud-sync"></a>Vad är Azure AD Connect Cloud Sync?
Azure AD Connect Cloud Sync är ett nytt erbjudande från Microsoft som utformats för att möta och uppnå dina hybrid identitets mål för synkronisering av användare, grupper och kontakter till Azure AD.  Det åstadkommer detta genom att använda Azure AD Cloud Provisioning-agenten i stället för Azure AD Connect-programmet.  Det kan dock användas tillsammans med Azure AD Connect-synkronisering och ger följande fördelar:
    
- Stöd för synkronisering till en Azure AD-klient från en frånkopplad Active Directory skogs miljö: de vanliga scenarierna omfattar fusions & förvärv, där det förvärvade företagets AD-skogar är isolerade från moder bolagets AD-skogar och företag som har historiskt haft flera AD-skogar.
- Förenklad installation med låg vikts etablerings agenter: agenterna fungerar som en brygga från AD till Azure AD, med all synkroniserad konfiguration som hanteras i molnet. 
- Flera etablerings agenter kan användas för att förenkla distributioner med hög tillgänglighet, särskilt viktiga för organisationer som förlitar sig på lösen ords-hash-synkronisering från AD till Azure AD.
- Stöd för stora grupper med upp till 50 000-medlemmar. Vi rekommenderar att du bara använder det omfångs filter för OU vid synkronisering av stora grupper.


![Vad är Azure AD Connect?](media/what-is-cloud-sync/architecture-1.png)

## <a name="how-is-azure-ad-connect-cloud-sync-different-from-azure-ad-connect-sync"></a>Hur skiljer Azure AD Connect Cloud Sync från Azure AD Connect Sync?
Med Azure AD Connect Cloud Sync dirigeras etableringen från AD till Azure AD till Microsoft Online Services. En organisation behöver bara distribuera, i sin lokala och IaaS miljö, en Lightweight-agent som fungerar som en brygga mellan Azure AD och AD. Etablerings konfigurationen lagras i Azure AD och hanteras som en del av tjänsten.

## <a name="azure-ad-connect-cloud-sync-video"></a>Azure AD Connect Cloud Sync-Video
Följande korta video ger en utmärkt översikt över Azure AD Connect Cloud Sync:

> [!VIDEO https://youtube.com/embed/mOT3ID02_YQ]


## <a name="comparison-between-azure-ad-connect-and-cloud-sync"></a>Jämförelse mellan Azure AD Connect och molnbaserad synkronisering

Följande tabell innehåller en jämförelse mellan Azure AD Connect och Azure AD Connect moln synkronisering:

| Funktion | Azure Active Directory Connect synkronisering| Azure Active Directory Connect Cloud Sync |
|:--- |:---:|:---:|
|Ansluta till en enda lokal AD-skog|● |● |
| Ansluta till flera lokala AD-skogar |● |● |
| Anslut till flera frånkopplade lokala AD-skogar | |● |
| Installations modell för Lightweight agent | |● |
| Flera aktiva agenter för hög tillgänglighet | |● |
| Anslut till LDAP-kataloger|●| | 
| Stöd för användar objekt |● |● |
| Stöd för grupp objekt |● |● |
| Stöd för kontakt objekt |● |● |
| Stöd för enhets objekt |● | |
| Tillåt grundläggande anpassning av Attribute-flöden |● |● |
| Synkronisera Exchange Online-attribut |● |● |
| Synkronisera tilläggets attribut 1-15 |● |● |
| Synkronisera kunddefinierade AD-attribut (katalog tillägg) |● | |
| Stöd för synkronisering av lösen ords-hash |●|●|
| Stöd för Pass-Through autentisering |●||
| Stöd för Federation |●|●|
| Smidig enkel inloggning|● |●|
| Stöder installation på en domänkontrollant |● |● |
| Stöd för Windows Server 2012 och Windows Server 2012 R2 |● |● |
| Filtrera på domäner/organisationsenheter/grupper |● |● |
| Filtrera utifrån objekts attributvärden |● | |
| Tillåt att en minimal uppsättning attribut synkroniseras (MinSync) |● |● |
| Tillåt borttagning av attribut som flödar från AD till Azure AD |● |● |
| Tillåt avancerad anpassning av attributflöden |● | |
| Stöd för tillbakaskrivning (lösen ord, enheter, grupper) |● | |
| Azure AD Domain Services support|● | |
| [Exchange hybrid tillbakaskrivning](../hybrid/reference-connect-sync-attributes-synchronized.md#exchange-hybrid-writeback) |● | |
| Stöd för upp till 150 000 objekt per AD-domän |● |● |
| Stöd för stora grupper – grupper med upp till 50 000 medlemmar |● |● |
| Kors domän referenser|● | |
| Etablering på begäran| |● |

## <a name="next-steps"></a>Nästa steg 

- [Vad är etablering?](what-is-provisioning.md)
- [Installera Cloud Sync](how-to-install.md)
