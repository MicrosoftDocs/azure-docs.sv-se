---
title: Förstå enhets uppdatering för IoT Hub autentisering och auktorisering | Microsoft Docs
description: 'Förstå hur enhets uppdatering för IoT Hub använder Azure RBAC för att tillhandahålla autentisering och auktorisering för användare och tjänst-API: er.'
author: vimeht
ms.author: vimeht
ms.date: 2/11/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: ca55b1df347b47a6eb82557658d59a3de666b703
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105558405"
---
# <a name="azure-role-based-access-control-rbac-and-device-update"></a>Rollbaserad åtkomst kontroll i Azure (RBAC) och enhets uppdatering

Enhets uppdatering använder Azure RBAC för att tillhandahålla autentisering och auktorisering för användare och tjänst-API: er.

## <a name="configure-access-control-roles"></a>Konfigurera åtkomst kontroll roller

För att andra användare och program ska få åtkomst till enhets uppdateringar måste användare eller program beviljas åtkomst till den här resursen. Här är de roller som stöds av enhets uppdateringen

|   Rollnamn   | Beskrivning  |
| :--------- | :---- |
|  Administratör för enhets uppdatering | Har åtkomst till alla enhets uppdaterings resurser  |
|  Enhets uppdaterings läsare| Kan visa alla uppdateringar och distributioner |
|  Innehålls administratör för enhets uppdatering | Kan visa, importera och ta bort uppdateringar  |
|  Innehålls läsare för enhets uppdatering | Kan visa uppdateringar  |
|  Administratör för enhets uppdaterings distribution | Kan hantera distribution av uppdateringar till enheter|
|  Distributions läsare för enhets uppdatering| Kan visa distributioner av uppdateringar till enheter |

En kombination av roller kan användas för att ge rätt åtkomst nivå. En utvecklare kan till exempel importera och hantera uppdateringar med hjälp av innehålls administratörs rollen enhets uppdatering, men behöver en roll för distribution av enhets uppdaterings läsare för att se förloppet för en uppdatering. En lösnings operatör med rollen enhets uppdaterings läsare kan visa alla uppdateringar, men du måste använda administratörs rollen enhets uppdaterings distribution för att distribuera en enskild uppdatering till enheter.


## <a name="authenticate-to-device-update-rest-apis-for-publishing-and-management"></a>Autentisera till enhet uppdatera REST-API: er för publicering och hantering

Enhets uppdatering använder också Azure AD för autentisering för att publicera och hantera innehåll via tjänst-API: er. För att komma igång måste du skapa och konfigurera ett klient program.

### <a name="create-client-azure-ad-app"></a>Skapa klient Azure AD App

Om du vill integrera ett program eller en tjänst med Azure AD måste du [först registrera](../active-directory/develop/quickstart-register-app.md) ett program med Azure AD. Klient program installationen varierar beroende på vilket flöde som används.  Konfigurationen nedan är till hjälp när du använder REST-API: er för enhets uppdatering.

* Ange klientautentisering: omdirigerings-URI: er för intern eller webb klient.
* Ange API-behörigheter – enhets uppdatering för IoT Hub exponeras:
  * Delegerade behörigheter: user_impersonation
  * **Valfritt**, bevilja administratörs medgivande

[Nästa steg: skapa enhets uppdaterings resurser och konfigurera åtkomst kontroll roller](./create-device-update-account.md)