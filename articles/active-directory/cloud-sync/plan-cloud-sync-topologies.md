---
title: Topologier och scenarier som stöds i Azure AD Connect Cloud Sync
description: Lär dig mer om olika lokala och Azure Active Directory (Azure AD)-topologier som använder moln synkronisering i Azure AD Connect.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/26/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2fd14ed8d149cdc5296229c52ceb74afb2ce7b23
ms.sourcegitcommit: 8a74ab1beba4522367aef8cb39c92c1147d5ec13
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/20/2021
ms.locfileid: "98614032"
---
# <a name="azure-ad-connect-cloud-sync-supported-topologies-and-scenarios"></a>Topologier och scenarier som stöds i Azure AD Connect Cloud Sync
I den här artikeln beskrivs olika lokala och Azure Active Directory (Azure AD)-topologier som använder moln synkronisering i Azure AD Connect. Den här artikeln innehåller endast konfigurationer och scenarier som stöds.

> [!IMPORTANT]
> Microsoft stöder inte ändring eller drift Azure AD Connect molnets synkronisering utanför de konfigurationer eller åtgärder som dokumenteras formellt. Någon av dessa konfigurationer eller åtgärder kan resultera i ett inkonsekvent eller tillstånd som inte stöds för Azure AD Connect moln synkronisering. Därför kan Microsoft inte tillhandahålla teknisk support för sådana distributioner.

## <a name="things-to-remember-about-all-scenarios-and-topologies"></a>Saker att komma ihåg om alla scenarier och topologier
Följande är en lista med information som du bör tänka på när du väljer en lösning.

- Användare och grupper måste unikt identifieras i alla skogar
- Matchning i skogar sker inte med molnbaserad synkronisering
- En användare eller grupp får bara representeras en gång i alla skogar
- Käll fäst punkten för objekt väljs automatiskt.  Den använder MS-DS-ConsistencyGuid om den finns, annars används ObjectGUID.
- Du kan inte ändra attributet som används för käll ankare.

## <a name="single-forest-single-azure-ad-tenant"></a>En enda skog, en enda Azure AD-klient
![Diagram som visar topologin för en enskild skog och en enda klient.](media/tutorial-single-forest/diagram-2.png)

Den enklaste topologin är en enda lokal skog med en eller flera domäner och en enda Azure AD-klient.  Ett exempel på det här scenariot finns i [Självstudier: en enda skog med en enda Azure AD-klient](tutorial-single-forest.md)


## <a name="multi-forest-single-azure-ad-tenant"></a>Flera skogar, en enda Azure AD-klient
![Topologi för en multi-skog och en enskild klient](media/plan-cloud-provisioning-topologies/multi-forest-2.png)

En vanlig topologi är flera AD-skogar med en eller flera domäner och en enda Azure AD-klient.  

## <a name="existing-forest-with-azure-ad-connect-new-forest-with-cloud-provisioning"></a>Befintlig skog med Azure AD Connect, ny skog med moln etablering
![Diagram som visar topologin för en befintlig skog och en ny skog.](media/tutorial-existing-forest/existing-forest-new-forest-2.png)

Det här scenariot är en topologi som liknar scenariot med flera skogar, men det här är en befintlig Azure AD Connect miljö och sedan en ny skog med hjälp av Azure AD Connect Cloud Sync.  Ett exempel på det här scenariot finns i [självstudie: en befintlig skog med en enda Azure AD-klient](tutorial-existing-forest.md)

## <a name="piloting-azure-ad-connect-cloud-sync-in-an-existing-hybrid-ad-forest"></a>Pilotering Azure AD Connect Cloud Sync i en befintlig hybrid AD-skog
![Topologi för en enskild skog och en enskild klient som utgångs ](media/tutorial-migrate-aadc-aadccp/diagram-2.png) scenariot inbegriper att både Azure AD Connect och Azure AD Connect molnbaserad synkronisering förekommer i samma skog och omfånget användare och grupper därefter. Obs: ett objekt ska finnas i omfånget i ett av verktygen. 

Ett exempel på det här scenariot finns i [självstudie: Pilot Azure AD Connect Cloud Sync i en befintlig synkroniserad AD-skog](tutorial-pilot-aadc-aadccp.md)



## <a name="next-steps"></a>Nästa steg 

- [Vad är etablering?](what-is-provisioning.md)
- [Vad är Azure AD Connect Cloud Sync?](what-is-cloud-sync.md)

