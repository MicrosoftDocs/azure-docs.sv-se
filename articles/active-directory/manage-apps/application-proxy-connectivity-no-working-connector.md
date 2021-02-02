---
title: Ingen fungerande anslutnings grupp hittades för en Application Proxy-app
description: Åtgärda problem som kan uppstå när det inte finns någon fungerande koppling i en kopplings grupp för ditt program med Azure-AD-programproxy
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.assetid: ''
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
ms.openlocfilehash: cec028dec2bf2e6295f59aae9c2e243da2209827
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/02/2021
ms.locfileid: "99257042"
---
# <a name="no-working-connector-group-found-for-an-application-proxy-application"></a>Ingen fungerande anslutnings grupp hittades för ett Application Proxy-program

Den här artikeln hjälper till att lösa vanliga problem när det inte finns någon koppling som har identifierats för ett Application Proxy-program som är integrerat med Azure Active Directory.

## <a name="overview-of-steps"></a>Översikt över steg
Om det inte finns någon fungerande koppling i en kopplings grupp för ditt program finns det några sätt att lösa problemet:

-   Om du inte har några kopplingar i gruppen kan du:

    -   Hämta en ny anslutning till höger på den lokala servern och tilldela den till den här gruppen

    -   Flytta en aktiv koppling till gruppen

-   Om du inte har några aktiva kopplingar i gruppen kan du:

    -   Identifiera orsaken till att din anslutning är inaktiv och lös

    -   Flytta en aktiv koppling till gruppen

Du kan ta reda på problemet genom att öppna menyn "programproxy" i programmet och titta på varnings meddelandet för kopplings gruppen. Om det inte finns några kopplingar i gruppen anger varnings meddelandet att gruppen behöver minst en anslutning. Om du inte har några aktiva kopplingar, förklarar varnings meddelandet att. Det är vanligt att ha inaktiva kopplingar. 

   ![Val av kopplings grupp i Azure Portal](./media/application-proxy-connectivity-no-working-connector/no-active-connector.png)

Mer information om vart och ett av dessa alternativ finns i motsvarande avsnitt nedan. Anvisningarna förutsätter att du startar från sidan anslutnings hantering. Om du tittar på fel meddelandet ovan kan du gå till den här sidan genom att klicka på varnings meddelandet. Du kan också gå till sidan genom att gå till **Azure Active Directory**, klicka på **företags program** och sedan på **programproxy.**

   ![Hantering av kopplings grupper i Azure Portal](./media/application-proxy-connectivity-no-working-connector/app-proxy.png)

## <a name="download-a-new-connector"></a>Hämta en ny anslutning

Om du vill hämta en ny anslutning använder du knappen "Hämta koppling" överst på sidan.

Installera anslutningen på en dator med direkt insikter för Server dels programmet. Normalt installeras anslutningen på samma server som programmet. När du har laddat ned ska kopplingen visas på den här menyn. Klicka på kopplingen och Använd List rutan "kopplings grupp" för att se till att den tillhör rätt grupp. Spara ändringen.

   ![Hämta anslutningen från Azure Portal](./media/application-proxy-connectivity-no-working-connector/download-connector.png)
   
## <a name="move-an-active-connector"></a>Flytta en aktiv koppling

Om du har en aktiv anslutning som ska tillhöra gruppen och har en detaljerad information till mål server delen kan du flytta anslutningen till den tilldelade gruppen. Det gör du genom att klicka på kopplingen. I fältet "kopplings grupp" använder du List rutan för att välja rätt grupp och klickar på Spara.

## <a name="resolve-an-inactive-connector"></a>Lösa en inaktiv koppling

Om de enda anslutningarna i gruppen är inaktiva, är de troligt vis på en dator som inte har alla nödvändiga portar avblockerade.

Mer information om hur du utlöser det här problemet finns i portarna felsöka dokument.

## <a name="next-steps"></a>Nästa steg
[Förstå Azure AD-programproxy-kopplingar](application-proxy-connectors.md)


