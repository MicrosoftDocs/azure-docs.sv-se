---
title: Det går inte att komma åt detta företags program fel med App proxy-appen
description: Så här löser du vanliga åtkomst problem med Azure AD-programproxy-program.
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
ms.date: 05/21/2019
ms.author: kenwith
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0b9fb68643c48c685194fa7ba1f1e5050d2d3cc7
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/02/2021
ms.locfileid: "99254939"
---
# <a name="cant-access-this-corporate-application-error-when-using-an-application-proxy-application"></a>Fel meddelandet "det går inte att komma åt det här företags programmet" visas när ett program för programproxy används

Den här artikeln hjälper dig att felsöka vanliga problem för fel meddelandet "den här företags appen kan inte nås" i ett Azure AD-programproxy-program.

## <a name="overview"></a>Översikt

När du ser det här felet hittar du status koden på fel sidan. Koden är förmodligen en av följande status koder:

- **Gateway-timeout**: tjänsten Application Proxy kan inte ansluta till anslutnings tjänsten. Det här felet indikerar vanligt vis ett problem med kopplings tilldelningen, själva kopplingen eller nätverks reglerna runt anslutnings tjänsten.
- **Felaktig Gateway**: anslutningen kan inte komma åt backend-programmet. Det här felet kan tyda på en felaktig konfiguration av programmet.
- **Tillåts** inte: användaren har inte behörighet att komma åt programmet. Felet kan inträffa när användaren inte är tilldelad till programmet i Azure Active Directory, eller om användaren inte har behörighet att komma åt programmet.

Du hittar koden genom att titta på texten längst ned till vänster i fel meddelandet för fältet status kod. Leta också efter ytterligare tips längst ned på sidan.

![Exempel: Gateway-timeout-fel](./media/application-proxy-sign-in-bad-gateway-timeout-error/connection-problem.png)

Mer information om hur du felsöker rotor saken till dessa fel och mer information om de föreslagna korrigeringarna finns i motsvarande avsnitt nedan.

## <a name="gateway-timeout-errors"></a>Gateway-timeout-fel

En gateway-timeout inträffar när tjänsten försöker ansluta till anslutningen och inte kan ligga inom tids perioden. Det här felet orsakas vanligt vis av ett program som har tilldelats till en anslutnings grupp utan fungerande kopplingar, eller att vissa portar som krävs av anslutningen inte är öppna.

## <a name="bad-gateway-errors"></a>Felaktiga Gateway-fel

Ett felaktigt Gateway-fel indikerar att anslutningen inte kan komma åt backend-programmet. kontrol lera att du har publicerat rätt program. Vanliga misstag som orsakar felet är:

- Ett skrivfel eller fel i den interna URL: en
- Inte att publicera programmets rot. Till exempel publicera `http://expenses/reimbursement` men försök att komma åt `http://expenses`
- Problem med KCD-konfigurationen (Kerberos-begränsad delegering)
- Problem med Server dels programmet

## <a name="forbidden-errors"></a>Förbjudna fel

Om du ser ett otillåtet fel har användaren inte tilldelats programmet. Felet kan vara antingen i Azure Active Directory eller i Server dels programmet.

Information om hur du tilldelar användare till programmet i Azure finns i [konfigurations dokumentationen](application-proxy-add-on-premises-application.md#test-the-application).

Om du bekräftar att användaren är tilldelad till programmet i Azure, kontrollerar du användar konfigurationen i Server dels programmet. Om du använder Kerberos-begränsad delegering/integrerad Windows-autentisering, se sidan KCD fel sökning för rikt linjer.

## <a name="check-the-applications-internal-url"></a>Kontrol lera programmets interna URL

Som första snabb steg, dubbelklicka och korrigera den interna URL: en genom att öppna programmet via **företags program** och sedan välja menyn **Application Proxy** . Kontrol lera att den interna URL: en används från ditt lokala nätverk för att få åtkomst till programmet.

## <a name="check-the-application-is-assigned-to-a-working-connector-group"></a>Kontrol lera att programmet är tilldelat en fungerande kopplings grupp

Så här kontrollerar du att programmet är kopplat till en fungerande kopplings grupp:

1. Öppna programmet i portalen genom att gå till **Azure Active Directory**, klicka på **företags program** och sedan på **alla program.** Öppna programmet och välj sedan **Application Proxy** på den vänstra menyn.
1. Titta på fältet kopplings grupp. Om det inte finns några aktiva kopplingar i gruppen visas en varning. Om du inte ser några varningar kan du gå vidare för att kontrol lera att alla [nödvändiga portar](application-proxy-add-on-premises-application.md) är tillåtna.
1. Om fel kopplings grupp visas använder du List rutan för att välja rätt grupp och bekräftar att du inte längre ser några varningar. Om den avsedda anslutnings gruppen visas klickar du på varnings meddelandet för att öppna sidan med anslutnings hantering.
1. Härifrån finns det några sätt att gå vidare:

   - Flytta en aktiv koppling till gruppen: om du har en aktiv anslutning som ska tillhöra den här gruppen och har en detaljerad information för mål server delen kan du flytta anslutningen till den tilldelade gruppen. Det gör du genom att klicka på kopplingen. I fältet "kopplings grupp" använder du List rutan för att välja rätt grupp och klickar på Spara.
   - Hämta en ny anslutning för gruppen: från den här sidan kan du hämta en länk för att [Ladda ned en ny anslutning](https://download.msappproxy.net/Subscription/d3c8b69d-6bf7-42be-a529-3fe9c2e70c90/Connector/Download). Installera anslutningen på en dator med direkt insikter för Server dels programmet. Normalt installeras anslutningen på samma server som programmet. Använd länken Hämta koppling för att ladda ned en anslutning till mål datorn. Klicka sedan på kopplingen och Använd List rutan "kopplings grupp" för att se till att den tillhör rätt grupp.
   - Undersök en inaktiv koppling: om en koppling visas som inaktiv kan den inte komma åt tjänsten. Det här felet beror vanligt vis på att några av de portar som krävs blockeras. Lös problemet genom att gå vidare för att kontrol lera att alla nödvändiga portar är tillåtna.

När du har använt de här stegen för att se till att programmet har tilldelats en grupp med fungerande kopplingar testar du programmet igen. Fortsätt till nästa avsnitt om det fortfarande inte fungerar.

## <a name="check-all-required-ports-are-open"></a>Kontrol lera att alla nödvändiga portar är öppna

Kontrol lera att alla nödvändiga portar är öppna. Information om vilka portar som krävs finns i avsnittet öppna portar i [Självstudier: Lägg till ett lokalt program för fjärråtkomst via programproxy i Azure Active Directory](application-proxy-add-on-premises-application.md). Om alla portar som krävs är öppna, gå vidare till nästa avsnitt.

## <a name="check-for-other-connector-errors"></a>Sök efter andra anslutnings fel

Om inget av ovanstående löser problemet, är nästa steg att leta efter problem eller fel med själva kopplingen. Du kan se några vanliga fel i [fel söknings dokumentet](./application-proxy-troubleshoot.md#connector-errors).

Du kan också gå direkt till kopplings loggarna för att identifiera eventuella fel. Många av fel meddelandena delar vissa rekommendationer för korrigeringar. Information om hur du visar loggarna finns i [anslutnings dokumentationen](application-proxy-connectors.md#under-the-hood).

## <a name="additional-resolutions"></a>Ytterligare lösningar

Om ovanstående inte löste problemet, finns det några olika möjliga orsaker. Identifiera problemet:

Om ditt program är konfigurerat för att använda integrerad Windows-autentisering (IWA) testar du programmet utan enkel inloggning. Annars flyttar du till nästa stycke. Om du vill kontrol lera programmet utan enkel inloggning öppnar du ditt program via **företags program** och går till menyn för **enkel inloggning** . Ändra List rutan från "integrerad Windows-autentisering" till "Azure AD-enkel inloggning" inaktive rad ".

Öppna en webbläsare och försök att komma åt programmet igen. Du måste uppmanas att autentisera och komma in i programmet. Om du kan autentisera är problemet med KCD-konfigurationen (Kerberos-begränsad delegering) som möjliggör enkel inloggning. Mer information finns på sidan för fel sökning av KCD.

Om du fortsätter att se felet går du till den dator där anslutningen är installerad, öppnar en webbläsare och försöker komma åt den interna URL: en som används för programmet. Kopplingen fungerar som en annan klient från samma dator. Om du inte kan nå programmet bör du undersöka varför datorn inte kan nå programmet eller använda en anslutning på en server som kan komma åt programmet.

Om du kan komma åt programmet från den datorn kan du söka efter problem eller fel med själva kopplingen. Du kan se några vanliga fel i [fel söknings dokumentet](application-proxy-troubleshoot.md#connector-errors). Du kan också gå direkt till kopplings loggarna för att identifiera eventuella fel. Många av våra fel meddelanden kan dela mer exakta rekommendationer för korrigeringar. Information om hur du visar loggarna finns i [vår anslutnings dokumentation](application-proxy-connectors.md#under-the-hood).

## <a name="next-steps"></a>Nästa steg

[Förstå Azure AD-programproxy-kopplingar](application-proxy-connectors.md)