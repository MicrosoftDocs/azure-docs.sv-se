---
title: Jokertecken i Azure-AD-programproxy
description: Lär dig hur du använder program med jokertecken i Azure Active Directory Application Proxy.
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 09/06/2018
ms.author: kenwith
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: c2f3d1d47bd26167253296f06af5470818760850
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "99257988"
---
# <a name="wildcard-applications-in-the-azure-active-directory-application-proxy"></a>Program med jokertecken i Azure Active Directory Application Proxy

I Azure Active Directory (Azure AD) kan konfigurera ett stort antal lokala program snabbt bli ohanterat och introducera onödiga risker för konfigurations fel om många av dem kräver samma inställningar. Med [Azure AD-programproxy](application-proxy.md)kan du lösa det här problemet genom att använda program publicering med jokertecken för att publicera och hantera många program samtidigt. Det här är en lösning som gör att du kan:

- Förenkla din administrativa belastning
- Minska antalet möjliga konfigurations fel
- Ge användarna säker åtkomst till fler resurser

Den här artikeln innehåller den information du behöver för att konfigurera program publicering med jokertecken i din miljö.

## <a name="create-a-wildcard-application"></a>Skapa ett program med jokertecken

Du kan skapa ett jokertecken (*) om du har en grupp med program med samma konfiguration. Potentiella kandidater till ett program med jokertecken är program som delar följande inställningar:

- Gruppen med användare som har åtkomst till dem
- SSO-metoden
- Åtkomst protokollet (http, https)

Du kan publicera program med jokertecken om båda, de interna och externa URL: erna har följande format:

> http (s)://*.\<domain\>

Exempel: `http(s)://*.adventure-works.com`.

Även om interna och externa URL: er kan använda olika domäner, bör de vara desamma. När du publicerar programmet visas ett fel om en av URL: erna inte har ett jokertecken.

Att skapa ett program med jokertecken baseras på samma [program publicerings flöde](application-proxy-add-on-premises-application.md) som är tillgängligt för alla andra program. Den enda skillnaden är att du inkluderar ett jokertecken i URL: erna och eventuellt SSO-konfigurationen.

## <a name="prerequisites"></a>Förutsättningar

Kom igång genom att se till att du uppfyller dessa krav.

### <a name="custom-domains"></a>Anpassade domäner

[Anpassade domäner](application-proxy-configure-custom-domain.md) är valfria för alla andra program, men de är en förutsättning för program med jokertecken. När du skapar anpassade domäner måste du:

1. Skapa en verifierad domän i Azure.
1. Ladda upp ett TLS/SSL-certifikat i PFX-format till programproxyn.

Du bör överväga att använda ett jokertecken för att matcha det program som du planerar att skapa. 

Av säkerhets skäl är detta ett hårt krav och vi stöder inte jokertecken för program som inte kan använda en anpassad domän för den externa URL: en.

### <a name="dns-updates"></a>DNS-uppdateringar

När du använder anpassade domäner måste du skapa en DNS-post med en CNAME-post för den externa URL: en (till exempel  `*.adventure-works.com` ) som pekar på den externa URL: en för Application Proxy-slutpunkten. För program med jokertecken måste CNAME-posten peka på relevanta externa URL: er:

> `<yourAADTenantId>.tenant.runtime.msappproxy.net`

För att bekräfta att du har konfigurerat din CNAME korrekt kan du använda [nslookup](/windows-server/administration/windows-commands/nslookup) på en av mål slut punkterna, till exempel `expenses.adventure-works.com` .  Ditt svar bör innehålla det alias som redan nämnts ( `<yourAADTenantId>.tenant.runtime.msappproxy.net` ).

## <a name="considerations"></a>Överväganden

Här följer några överväganden som du bör tänka på när du använder jokertecken.

### <a name="accepted-formats"></a>Godkända format

För jokertecken måste den **interna webb adressen** formateras som `http(s)://*.<domain>` .

![Använd formatet http (s)://* för intern URL. \< domän>](./media/application-proxy-wildcard/22.png)

När du konfigurerar en **extern URL** måste du använda följande format: `https://*.<custom domain>`

![För extern URL använder du formatet https://*. \< anpassad domän>](./media/application-proxy-wildcard/21.png)

Andra lägen i jokertecken, flera jokertecken eller andra regex-strängar stöds inte och orsakar fel.

### <a name="excluding-applications-from-the-wildcard"></a>Utesluta program från jokertecknet

Du kan undanta ett program från programmet med jokertecken

- Publicera undantags programmet som ett vanligt program
- Aktivera endast jokertecken för vissa program via dina DNS-inställningar

Att publicera ett program som ett vanligt program är den bästa metoden att undanta ett program från ett jokertecken. Du bör publicera de undantagna programmen innan du använder jokertecken för att se till att dina undantag tillämpas från början. Det mest specifika programmet prioriteras alltid – ett program som publicerats som prioriteras `budgets.finance.adventure-works.com` över programmet `*.finance.adventure-works.com` , vilket i sin tur prioriteras i programmet `*.adventure-works.com` .

Du kan också begränsa jokertecken så att de endast fungerar för vissa program genom din DNS-hantering. Som bästa praxis bör du skapa en CNAME-post som innehåller ett jokertecken och som matchar formatet för den externa URL som du har konfigurerat. Du kan dock istället peka specifika program-URL: er till jokertecknen. Till exempel, i stället för `*.adventure-works.com` , `hr.adventure-works.com` peka `expenses.adventure-works.com` och `travel.adventure-works.com individually` till `000aa000-11b1-2ccc-d333-4444eee4444e.tenant.runtime.msappproxy.net` .

Om du använder det här alternativet behöver du också en annan CNAME-post för värdet `AppId.domain` , till exempel `00000000-1a11-22b2-c333-444d4d4dd444.adventure-works.com` , och även peka på samma plats. Du kan hitta **AppId** på sidan program egenskaper i programmet jokertecken:

![Hitta program-ID: t på appens egenskaps sida](./media/application-proxy-wildcard/01.png)

### <a name="setting-the-homepage-url-for-the-myapps-panel"></a>Ange start sidans URL för panelen för Mina appar

Programmet med jokertecken representeras bara av en panel i panelen för mina [appar](https://myapps.microsoft.com). Den här panelen är dold som standard. Så här visar du panelen och har användare på en speciell sida:

1. Följ rikt linjerna för att [ställa in en URL för start sidan](application-proxy-configure-custom-home-page.md).
1. Ange **Visa program** till **True** på sidan program egenskaper.

### <a name="kerberos-constrained-delegation"></a>Kerberos-begränsad delegering

För program [som använder Kerberos-begränsad delegering (KCD) som SSO-metod](application-proxy-configure-single-sign-on-with-kcd.md)kan SPN-namnet som anges för SSO-metoden också ha ett jokertecken. SPN kan till exempel vara: `HTTP/*.adventure-works.com` . Du måste fortfarande ha de enskilda SPN som kon figurer ATS på backend-servrarna (till exempel `HTTP/expenses.adventure-works.com and HTTP/travel.adventure-works.com` ).

## <a name="scenario-1-general-wildcard-application"></a>Scenario 1: allmänt program med jokertecken

I det här scenariot har du tre olika program som du vill publicera:

- `expenses.adventure-works.com`
- `hr.adventure-works.com`
- `travel.adventure-works.com`

Alla tre programmen:

- Används av alla dina användare
- Använd *integrerad Windows-autentisering*
- Ha samma egenskaper

Du kan publicera programmet jokertecken med hjälp av stegen som beskrivs i [Publicera program med hjälp av Azure AD-programproxy](application-proxy-add-on-premises-application.md). Det här scenariot förutsätter:

- En klient med följande ID: `000aa000-11b1-2ccc-d333-4444eee4444e`
- En verifierad domän som heter `adventure-works.com` har kon figurer ATS.
- En **CNAME** -post som pekar `*.adventure-works.com` på `000aa000-11b1-2ccc-d333-4444eee4444e.tenant.runtime.msappproxy.net` har skapats.

Följ de [dokumenterade stegen](application-proxy-add-on-premises-application.md)och skapa ett nytt program för programproxy i din klient organisation. I det här exemplet är jokertecknet i följande fält:

- Intern URL:

    ![Exempel: jokertecken i intern URL](./media/application-proxy-wildcard/42.png)

- Extern URL:

    ![Exempel: jokertecken i extern URL](./media/application-proxy-wildcard/43.png)

- Internt program-SPN:

    ![Exempel: jokertecken i SPN-konfigurationen](./media/application-proxy-wildcard/44.png)

Genom att publicera programmet jokertecken kan du nu komma åt dina tre program genom att gå till de URL: er som du använder till (till exempel `travel.adventure-works.com` ).

Konfigurationen implementerar följande struktur:

![Visar den struktur som implementeras i exempel konfigurationen](./media/application-proxy-wildcard/05.png)

| Färg | Beskrivning |
| ---   | ---         |
| Blue  | Program som uttryckligen publiceras och visas i Azure Portal. |
| Mörkgrå  | Program som du kan komma åt via det överordnade programmet. |

## <a name="scenario-2-general-wildcard-application-with-exception"></a>Scenario 2: allmänt program med jokertecken med undantag

I det här scenariot har du förutom de tre allmänna programmen i ett annat program, `finance.adventure-works.com` som endast bör vara tillgängliga via finansiell division. Med den aktuella program strukturen skulle ekonomi programmet vara tillgängligt via programmet jokertecken och av alla anställda. Om du vill ändra detta utesluter du ditt program från ditt jokertecken genom att konfigurera ekonomi som ett separat program med mer restriktiva behörigheter.

Du måste se till att det finns en CNAME-post som pekar på `finance.adventure-works.com` den programspecifika slut punkten som anges på sidan Application Proxy för programmet. I det här scenariot `finance.adventure-works.com` pekar du på `https://finance-awcycles.msappproxy.net/` .

Efter de [dokumenterade stegen](application-proxy-add-on-premises-application.md)kräver det här scenariot följande inställningar:

- I den **interna webb adressen** ställer du in **ekonomi** i stället för jokertecken.

    ![Exempel: Ange ekonomi i stället för ett jokertecken i en intern URL](./media/application-proxy-wildcard/52.png)

- I den **externa webb adressen** ställer du in **ekonomi** i stället för jokertecken.

    ![Exempel: Ange ekonomi i stället för ett jokertecken i en extern URL](./media/application-proxy-wildcard/53.png)

- Internt program-SPN du anger **ekonomi** i stället för ett jokertecken.

    ![Exempel: Ange ekonomi i stället för ett jokertecken i SPN-konfigurationen](./media/application-proxy-wildcard/54.png)

Den här konfigurationen implementerar följande scenario:

![Visar konfigurationen som implementeras i exempel scenariot](./media/application-proxy-wildcard/09.png)

Eftersom `finance.adventure-works.com` är en mer viss URL än `*.adventure-works.com` , prioriteras den. Användare navigerar för att `finance.adventure-works.com` få den erfarenhet som anges i programmet ekonomi resurser. I det här fallet kan endast ekonomi personal komma åt `finance.adventure-works.com` .

Om du har flera program publicerade för finansiering och du har `finance.adventure-works.com` en verifierad domän kan du publicera ett annat program med jokertecken `*.finance.adventure-works.com` . Eftersom detta är mer specifik än vad som är allmänt `*.adventure-works.com` , prioriteras det om en användare har åtkomst till ett program i ekonomi domänen.

## <a name="next-steps"></a>Nästa steg

- Mer information om **anpassade domäner** finns [i arbeta med anpassade domäner i Azure AD-programproxy](application-proxy-configure-custom-domain.md).
- Mer information om hur du **publicerar program** finns i [Publicera program med hjälp av Azure AD-programproxy](application-proxy-add-on-premises-application.md)