---
title: App-sidan visas inte korrekt för Application Proxy-appen | Microsoft Docs
description: Vägledning när sidan inte visas korrekt i ett program-proxy-program som du har integrerat med Azure AD
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 05/21/2018
ms.author: kenwith
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 99b09e7b15427eb33e1e85edd89f4c8a37c4a3eb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "99254662"
---
# <a name="application-page-does-not-display-correctly-for-an-application-proxy-application"></a>Program sidan visas inte korrekt för ett Application Proxy-program

Den här artikeln hjälper dig att felsöka problem med Azure Active Directory-programproxy program när du navigerar till sidan, men något på sidan ser inte korrekt ut.

## <a name="overview"></a>Översikt
När du publicerar en app proxy är det bara sidor under roten som är tillgängliga vid åtkomst till programmet. Om sidan inte visas korrekt kan det hända att den interna rot-URL: en som används för programmet saknar vissa sid resurser. Kontrol lera att du har publicerat *alla* resurser för sidan som en del av ditt program för att lösa problemet.

Du kan kontrol lera om det finns resurser som saknas genom att öppna nätverks spåraren (till exempel Fiddler eller F12-verktyg i Internet Explorer/Microsoft Edge), läsa in sidan och leta efter 404-fel. Det betyder att det inte går att hitta de sidor som för närvarande finns och att du behöver publicera dem.

Som ett exempel på det här fallet förutsätter vi att du har publicerat ett utgifts program med hjälp av den interna URL `http://myapps/expenses` : en, men appen använder format mal len `http://myapps/style.css` . I det här fallet publiceras inte format mal len i ditt program, så inläsningen av modulen utgifter genererar ett 404-fel vid försök att läsa in Style. CSS. I det här exemplet löses problemet genom att publicera programmet med en intern URL `http://myapp/` .

## <a name="problems-with-publishing-as-one-application"></a>Problem med att publicera som ett program

Om det inte går att publicera alla resurser i samma program måste du publicera flera program och aktivera länkar mellan dem.

För att göra det rekommenderar vi att du använder lösningen för [anpassade domäner](application-proxy-configure-custom-domain.md) . Den här lösningen kräver dock att du äger certifikatet för din domän och att programmen använder fullständigt kvalificerade domän namn (FQDN). Andra alternativ finns i [fel sökning av brutna länkar-dokumentation](application-proxy-page-links-broken-problem.md).

## <a name="next-steps"></a>Nästa steg
[Publicera program med Azure AD Application Proxy](application-proxy-add-on-premises-application.md)
