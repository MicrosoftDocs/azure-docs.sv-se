---
title: Länkar på sidan fungerar inte för ett Application Proxy-program
description: Så här felsöker du problem med brutna länkar i Application Proxy-program som du har integrerat med Azure AD
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: troubleshooting
ms.date: 09/10/2018
ms.author: kenwith
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9a8ecb908063a40da32c1397fd4249811f9c2712
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "99254622"
---
# <a name="links-on-the-page-dont-work-for-an-application-proxy-application"></a>Länkar på sidan fungerar inte för ett Application Proxy-program

Den här artikeln hjälper dig att felsöka varför länkar i Azure Active Directory-programproxy programmet inte fungerar som de ska.

## <a name="overview"></a>Översikt 
När du har publicerat en app för programproxy är de enda länkarna som fungerar som standard i programmet länkar till destinationer som finns i den publicerade rot-URL: en. Länkarna i programmen fungerar inte, den interna URL: en för programmet innehåller antagligen inte alla mål för länkar i programmet.

**Varför sker det här?** När du klickar på en länk i ett program försöker programproxyn matcha webb adressen antingen som en intern URL i samma program, eller som en extern URL. Om länken pekar på en intern URL som inte finns i samma program, tillhör den inte någon av dessa buckets och resulterar i ett fel som inte hittas.

## <a name="ways-you-can-resolve-broken-links"></a>Sätt att lösa brutna länkar

Det finns tre sätt att lösa det här problemet. Alternativen nedan visas i ökande komplexitet.

1.  Kontrol lera att den interna URL: en är en rot som innehåller alla relevanta länkar för programmet. Detta gör att alla länkar kan matchas när innehåll publiceras i samma program.

    Om du ändrar den interna URL: en men inte vill ändra landnings sidan för användare ändrar du Start sidans URL till den tidigare publicerade interna URL: en. Detta kan göras genom att gå till "Azure Active Directory"- &gt; app-registreringar – &gt; Välj program &gt; varu anpassning. I avsnittet anpassning ser du fältet "hem sidans URL", som du kan anpassa till den önskade landnings sidan. Om du fortfarande använder den äldre Appregistreringar-upplevelsen visas information om start sidans webb adress i fliken Egenskaper. 
    
    > [!IMPORTANT]
    > För att göra ovanstående ändringar måste du ha behörighet att ändra program objekt i Azure AD. Användaren måste tilldelas rollen [program administratör](../roles/delegate-app-roles.md#assign-built-in-application-admin-roles) som beviljar programmet modificaion-rättigheter i Azure AD till användaren.
    >

2.  Om dina program använder fullständigt kvalificerade domän namn (FQDN) kan du använda [anpassade domäner](application-proxy-configure-custom-domain.md) för att publicera dina program. Den här funktionen tillåter att samma URL används både internt och externt.

    Med det här alternativet ser du till att länkarna i programmet kan nås externt via programproxyn, eftersom länkarna i programmet till interna URL: er också känns igen externt. Alla länkar måste redan tillhöra ett publicerat program. Men med det här alternativet behöver länkarna inte tillhöra samma program och kan tillhöra flera program.

3.  Om inget av dessa alternativ är möjligt finns det flera alternativ för att aktivera översättning av infogade länkar. De här alternativen omfattar att använda Intune Managed Browser, mina apps-tillägg eller använda länk översättnings inställningen i ditt program. Läs mer om var och en av dessa alternativ och hur du aktiverar dem i [omdirigera hårdkodad-Länkar för appar som publicerats med Azure AD-programproxy](application-proxy-configure-hard-coded-link-translation.md).

## <a name="next-steps"></a>Nästa steg
[Arbeta med befintliga lokala proxyservrar](application-proxy-configure-connectors-with-proxy-servers.md)

