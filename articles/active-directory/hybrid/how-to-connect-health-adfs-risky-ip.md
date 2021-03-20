---
title: Azure AD Connect Health med AD FS riskfyllda IP-rapport | Microsoft Docs
description: Beskriver den Azure AD Connect Health AD FS riskfyllda IP-rapporten.
services: active-directory
documentationcenter: ''
ms.reviewer: zhiweiwangmsft
author: billmath
manager: daveba
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 02/26/2019
ms.author: billmath
ms.custom: H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: e0b76d2f943f254eb06208e2c190bae4d4088030
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98746078"
---
# <a name="risky-ip-report-public-preview"></a>Riskfylld IP-rapport (offentlig för hands version)
AD FS kunder kan exponera slut punkter för autentisering av lösen ord till Internet för att tillhandahålla autentiseringstjänster för slutanvändare att få åtkomst till SaaS-program som Microsoft 365. I det här fallet är det möjligt för någon obehörig att försöka logga in på ditt AD FS-system genom att gissa slutanvändarens lösenord och få åtkomst till programresurser. AD FS har innehållit en utelåsningsfunktion för extranätskonton som förhindrar dessa typer av angrepp sedan AD FS i Windows Server 2012 R2. Om du har en lägre version rekommenderar vi starkt att du uppgraderar ditt AD FS-system till Windows Server 2016. <br />

Dessutom är det möjligt att en enskild IP-adress kan försöka utföra flera inloggningar mot flera användare. I dessa fall kan antalet försök per användare ligga under tröskelvärdet för kontots utelåsningsskydd i AD FS. Azure AD Connect Health innehåller nu en ”rapport för riskfyllda IP-adresser” som identifierar det här tillståndet och meddelar administratörer om detta inträffar. Följande är viktiga fördelar med den här rapporten: 
- Identifiering av IP-adresser som överskrider ett tröskelvärde för misslyckade lösenordsbaserade inloggningar
- Stöd för misslyckade inloggningar på grund av felaktiga lösenord eller på grund av extranätsutelåsning
- E-postavisering som varnar administratörer när detta inträffar med anpassningsbara e-postinställningar
- Anpassningsbara tröskelvärdesinställningar som överensstämmer med säkerhetsprinciper i organisationen
- Nedladdningsbara rapporter för offlineanalys och integrering med andra system via automatisering

> [!NOTE]
> Om du ska kunna använda rapporten måste AD FS-granskning vara aktiverat. Mer information finns i [Aktivera granskning för AD FS](how-to-connect-health-agent-install.md#enable-auditing-for-ad-fs). <br />
> Om du vill ha åtkomst till förhandsversionen måste du ha behörighet som global administratör eller [säkerhetsläsare](../../role-based-access-control/built-in-roles.md#security-reader).  
>

## <a name="what-is-in-the-report"></a>Vad finns i rapporten?
Klientens IP-adresser för inloggning med misslyckade inloggningar sammanställs via Web Application Proxy-servrar. Varje objekt i rapporten för riskfyllda IP-adresser visar sammanställd information om misslyckade AD FS-inloggningsaktiviteter där angivet tröskelvärde har överskridits. Det innehåller följande information: ![ skärm bild som visar en riskfylld IP-rapport med kolumn rubriker markerad.](./media/how-to-connect-health-adfs/report4a.png)

| Rapportobjekt | Beskrivning |
| ------- | ----------- |
| Tidsstämpel | Visar tidsstämpeln baserat på Azure-portalens lokala tid när tidsperioden för identifiering startar.<br /> Alla dagliga händelser genereras vid midnatt UTC-tid. <br />Varje timhändelse har en tidsstämpel som är avrundad till timmens början. Första aktivitetens starttid från ”firstAuditTimestamp” hittar du i den exporterade filen. |
| Utlösartyp | Visar tidsperiod för identifieringstypen. De sammanställda utlösartyperna visas per timme eller per dag. Det här är användbart vid identifiering av en råstyrkeattack med hög frekvens, jämfört med en långsam attack där antalet försök är fördelade över hela dagen. |
| IP-adress | En enskild riskfylld IP-adress som antingen har ett felaktigt lösenord eller en extranätsutelåsning vid inloggning. Detta kan vara en IPv4-eller IPv6-adress. |
| Antal felaktiga lösenord | Antalet felaktiga lösenord från IP-adressen under tidsperioden för identifiering. Felaktiga lösenord kan inträffa flera gånger för vissa användare. Observera att detta inte inkluderar misslyckade försök på grund av utgångna lösenord. |
| Antal extranätsutelåsningar | Antalet extranätsutelåsningar från IP-adressen under tidsperioden för identifiering. Extranätsutelåsningar kan inträffa flera gånger för vissa användare. Detta visas bara om extranätsutelåsningen har konfigurerats i AD FS (version 2012 R2 eller senare). <b>Obs!</b> Vi rekommenderar starkt att aktivera den här funktionen om du tillåter extranätsinloggningar med lösenord. |
| Försök för unika användare | Antalet försök för unika användarkonton från IP-adressen under tidsperioden för identifiering. Detta är en mekanism för att särskilja ett mönster vid en enda användarattack jämfört med attackmönster mot flera användare.  |

Nedanstående rapportobjekt innebär från kl. 18:00 till 19:00 den 28/2 2018, innehöll IP-adressen <i>104.2XX.2XX.9</i> inte några felaktiga lösenord, men 284 extranätsutelåsningar. 14 unika användare påverkades inom kriterierna. Händelsen överskred tröskelvärdet per timme i rapporten. 

![Skärm bild som visar ett exempel på en riskfylld IP-rapport post.](./media/how-to-connect-health-adfs/report4b.png)

> [!NOTE]
> - Det är bara aktiviteter som överskrider angivet tröskelvärde visas i rapportlistan. 
> - Den här rapporten kan spåras bakåt i högst 30 dagar.
> - Aviseringsrapporten visar inte Exchange IP-adresser eller privata IP-adresser. De ingår dock fortfarande i exportlistan. 
>

![Skärm bild som visar den riskfyllda IP-rapporten med alternativet "Ladda ned", "meddelande inställningar" och "tröskel inställningar" markerat.](./media/how-to-connect-health-adfs/report4c.png)

## <a name="load-balancer-ip-addresses-in-the-list"></a>IP-adresser för belastnings utjämning i listan
Sammanställning av lastbalanserarens misslyckade inloggningar aktiviteter och uppnått tröskelvärde. Om du ser IP-adresser för lastbalanserare är det mycket troligt att en extern lastbalanserare inte skickar klientens IP-adress när den skickar sin begäran till servern för webbprogramproxyn. Konfigurera lastbalanseraren korrekt för att vidarebefordra klientens IP-adress. 

## <a name="download-risky-ip-report"></a>Hämta riskbaserade IP-rapporter 
Med hjälp av funktionen **Ladda ned** kan listan med riskfyllda IP-adresser under de senaste 30 dagarna exporteras från Connect Health-portalen. Exportresultatet inkluderar alla misslyckade AD FS-inloggningar i varje tidsfönster, så du kan anpassa filtreringen efter exporten. Förutom markerade sammanställningar i portalen visar exportresultatet också mer information om misslyckade inloggningar per IP-adress:

|  Rapportobjekt  |  Beskrivning  | 
| ------- | ----------- | 
| firstAuditTimestamp | Visar den första tidstämpel då misslyckade aktiviteter startades under tidsperioden för identifieringen.  | 
| lastAuditTimestamp | Visar den sista tidstämpel då misslyckade aktiviteter avslutades under tidsperioden för identifieringen.  | 
| attemptCountThresholdIsExceeded | Flagga ifall de aktuella aktiviteterna överstiger tröskelvärdet för aviseringar.  | 
| isWhitelistedIpAddress | Flagga ifall IP-adressen är filtrerad från avisering och rapportering. Privata IP-adresser (<i>10.x.x.x, 172.x.x.x och 192.168.x.x</i>) och Exchange-IP-adresser är filtrerade och markeras som True. Om du ser privata IP-adressintervall är det mycket troligt att en extern lastbalanserare inte skickar klientens IP-adress när den skickar sin begäran till servern för webbprogramproxyn.  | 

## <a name="configure-notification-settings"></a>Konfigurera meddelande inställningar
Administratörskontakter i rapporten kan uppdateras i **Meddelandeinställningar**. Som standard är aviseringar om riskfyllda IP-adresser inaktiverade. Du kan aktivera meddelanden genom att visa/dölja knappen under ”Get email notifications for IP addresses exceeding failed activity threshold report” (Hämta e-postmeddelanden för IP-adresser som överstiger tröskelvärdet för misslyckade aktiviteter). Precis som i inställningar av allmänna aviseringar i Connect Health, kan du anpassa vilka mottagare som ska få meddelanden om riskfyllda IP-adresser härifrån. Du kan även meddela alla globala administratörer när ändringen är gjord. 

## <a name="configure-threshold-settings"></a>Konfigurera tröskelvärdes inställningar
Aviseringströskelvärdet kan uppdateras i Tröskelinställningar. Systemet har tröskelvärdet inställt som standard. Standardvärdena anges nedan. Det finns fyra kategorier i tröskelinställningarna för rapporten om riskfyllda IP-adresser:

![Azure AD Connect Health-portalen](./media/how-to-connect-health-adfs/report4d.png)

| Tröskelobjekt | Beskrivning |
| --- | --- |
| (Felaktig U/P + extranätsutelåsning)/dag  | Tröskelinställningen rapporterar aktivitet och utlöser en varningsavisering när antalet felaktiga lösenord plus antalet extranätsutelåsningar överskrider den per **dag**. Standardvärdet är 100.|
| (Felaktig U/P + extranätsutelåsning)/timme | Tröskelinställningen rapporterar aktivitet och utlöser en varningsavisering när antalet felaktiga lösenord plus antalet extranätsutelåsningar överskrider den per **timme**. Standardvärdet är 50.|
| Extranätsutelåsning/dag | Tröskelinställningen rapporterar aktivitet och utlöser en varningsavisering när antalet extranätsutelåsningar överskrider den per **dag**. Standardvärdet är 50.|
| Extranätsutelåsning/timme| Tröskelinställningen rapporterar aktivitet och utlöser en varningsavisering när antalet extranätsutelåsningar överskrider den per **timme**. Standardvärdet är 25|

> [!NOTE]
> - Ändringar av rapportens tröskelvärde tillämpas en timme efter ändringen. 
> - Befintliga rapporterade objekt påverkas inte av tröskeländringen. 
> - Vi rekommenderar att du analyserar antalet händelser i din miljö och justerar tröskelvärdet i enlighet med detta. 
>
>

## <a name="faq"></a>Vanliga frågor
**Varför ser jag privata IP-adressintervall i rapporten?**  <br />
Privata IP-adresser (<i>10. x. x, 172. x. x. x & 192.168. x. x</i>) och Exchange IP-adresser filtreras och markeras som true i listan över godkända IP-adresser. Om du ser privata IP-adressintervall är det mycket troligt att en extern lastbalanserare inte skickar klientens IP-adress när den skickar sin begäran till servern för webbprogramproxyn.

**Varför ser jag IP-adresser för lastbalanserare i rapporten?**  <br />
Om du ser IP-adresser för lastbalanserare är det mycket troligt att en extern lastbalanserare inte skickar klientens IP-adress när den skickar sin begäran till servern för webbprogramproxyn. Konfigurera lastbalanseraren korrekt för att vidarebefordra klientens IP-adress. 

**Hur gör jag för att blockera IP-adressen?**  <br />
Du bör lägga till identifierade skadliga IP-adresser i brandväggen eller blockera dem i Exchange.   <br />

**Varför visas inte några objekt i rapporten?** <br />
- De misslyckade inloggningsaktiviteterna överskrider inte tröskelinställningarna.
- Kontrollera att det inte finns någon aktiv varning om att ”Hälsotjänsten är inte uppdaterad” i din AD FS-serverlista.  Läs mer om [felsökning av den här aviseringen](how-to-connect-health-data-freshness.md).
- Granskningar är inte aktiverade i AD FS-servergrupperna.

**Varför ser jag ingen åtkomst till rapporten?**  <br />
Du måste ha behörighet som global administratör eller [säkerhetsläsare](../../role-based-access-control/built-in-roles.md#security-reader). Kontakta en global administratör för att få åtkomst.


## <a name="next-steps"></a>Nästa steg
* [Azure AD Connect Health](./whatis-azure-ad-connect.md)
* [Installation av Azure AD Connect Health Agent](how-to-connect-health-agent-install.md)
