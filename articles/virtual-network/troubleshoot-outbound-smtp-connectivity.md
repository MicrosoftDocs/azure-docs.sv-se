---
title: Felsöka utgående SMTP-anslutningar i Azure | Microsoft Docs
description: Lär dig den rekommenderade metoden för att skicka e-post och felsöka problem med utgående SMTP-anslutningar i Azure.
services: virtual-network
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/04/2021
ms.author: genli
ms.openlocfilehash: 518b8e33ac9adfdd1aa121e0cb8d1558545980e7
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2021
ms.locfileid: "99054863"
---
# <a name="troubleshoot-outbound-smtp-connectivity-problems-in-azure"></a>Felsöka utgående SMTP-anslutningsproblem i Azure

Från den 15 november 2017 görs utgående e-postmeddelanden som skickas direkt till externa domäner (t. ex. outlook.com och gmail.com) från en virtuell dator (VM) som endast är tillgängliga för vissa prenumerations typer i Azure. Utgående SMTP-anslutningar som använder TCP-port 25 blockerades. (Port 25 används huvudsakligen för oautentiserad e-postleverans.)

Den här förändringen gäller enbart för prenumerationer och distributioner som skapats efter den 15 november 2017.

## <a name="recommended-method-of-sending-email"></a>Rekommenderad metod för att skicka e-post

Vi rekommenderar att du använder autentiserade SMTP Relay-tjänster för att skicka e-post från virtuella Azure-datorer eller från Azure App Service. (Dessa relä tjänster ansluter vanligt vis via TCP-port 587, men de stöder andra portar.) Dessa tjänster används för att underhålla IP-eller domän rykte för att minimera risken att e-postleverantörer från tredje part kommer att avvisa meddelanden. [SendGrid](https://sendgrid.com/partners/azure/) är en sådan SMTP Relay-tjänst, men det finns andra. Du kan också ha en säker SMTP-Relay-tjänst som körs lokalt och som du kan använda.

Att använda dessa e-postleverans tjänster är inte begränsat i Azure, oavsett prenumerations typ.

## <a name="enterprise-agreement"></a>Enterprise-avtal

För Enterprise-avtal Azure-användare finns det ingen ändring av den tekniska möjligheten att skicka e-post utan att använda ett autentiserat relä. Både nya och befintliga Enterprise-avtal användare kan testa utgående e-postleverans från virtuella Azure-datorer direkt till externa e-postleverantörer utan begränsningar från Azure-plattformen. Det finns ingen garanti för att e-postleverantörer kommer att acceptera inkommande e-post från en specifik användare. Men Azure-plattformen blockerar inte leverans försök för virtuella datorer inom Enterprise-avtal prenumerationer. Du måste arbeta direkt med e-postleverantörer för att åtgärda eventuella meddelande leverans-eller skräp post filtrerings problem som berör vissa leverantörer.

## <a name="pay-as-you-go"></a>Betala per användning

Om du registrerade dig före den 15 november 2017 för en prenumeration där du betalar per användning, sker ingen ändring i den tekniska möjligheten att testa utgående e-postleverans. Du kommer fortfarande att kunna testa utgående e-postleverans från virtuella Azure-datorer i dessa prenumerationer direkt till externa e-postleverantörer utan begränsningar från Azure-plattformen. Återigen finns det ingen garanti för att e-postleverantörerna kommer att acceptera inkommande e-post från en specifik användare. Användarna kommer att behöva arbeta direkt med e-postleverantörer för att åtgärda eventuella problem med meddelande leverans eller skräp post filtrering som rör specifika leverantörer.

För prenumerationer enligt principen betala per användning som skapades efter den 15 november 2017 kommer det att finnas tekniska begränsningar som blockerar e-post som skickas direkt från virtuella datorer i prenumerationerna. Om du vill kunna skicka e-post från virtuella Azure-datorer direkt till externa e-postleverantörer (utan att använda ett autentiserat SMTP-relä) och du har ett konto som är i bra position med en betalnings historik kan du begära att begränsningen tas bort. Det kan du göra i avsnittet **anslutning** på bladet **diagnostisera och lösa** för en Azure Virtual Network-resurs i Azure Portal. Om din begäran godkänns, aktive ras din prenumeration eller så får du instruktioner för nästa steg. 

När en prenumeration enligt principen betala per användning är undantagen och de virtuella datorerna stoppas och startas om i Azure Portal, undantas alla virtuella datorer i den prenumerationen. Undantaget gäller endast för den prenumeration som begärs och endast för VM-trafik som dirigeras direkt till Internet.

> [!NOTE]
> Microsoft förbehåller sig rätten att återkalla dessa undantag om det fastställs att en överträdelse av tjänst villkoren har inträffat.

## <a name="msdn-azure-pass-azure-in-open-education-azure-for-students-visual-studio-and-free-trial"></a>MSDN, Azure-pass, Azure i Open, utbildning, Azure för studenter, Visual Studio och kostnads fri utvärderings version

Om du har skapat någon av följande prenumerations typer efter den 15 november 2017 har du tekniska begränsningar som blockerar e-post som skickas från virtuella datorer i prenumerationen direkt till e-postleverantörer:
- MSDN
- Azure-pass
- Azure i Open
- Education
- Microsoft Azure for Students
- Kostnadsfri utvärdering
- Alla Visual Studio-prenumerationer  

Begränsningarna är på plats för att förhindra missbruk. Begär Anden om att ta bort dessa begränsningar kommer inte att beviljas.

Om du använder dessa prenumerations typer rekommenderar vi att du använder SMTP-relätjänster, som beskrivs tidigare i den här artikeln, eller för att ändra din prenumerations typ.

## <a name="cloud-solution-provider"></a>Cloud Solution Provider

Om du använder Azure-resurser via en moln lösnings leverantör kan du göra en begäran om att ta bort begränsningen i **anslutnings** avsnittet i fönstret **diagnostisera och lösa** för en virtuell nätverks resurs i Azure Portal. Om din begäran godkänns, aktive ras din prenumeration eller så får du instruktioner för nästa steg.

## <a name="microsoft-partner-network-bizspark-plus-or-azure-sponsorship"></a>Microsoft Partner Network, BizSpark Plus eller Azure-sponsring

För prenumerationer av följande typer som har skapats efter den 15 november 2017 kommer det att finnas tekniska begränsningar som blockerar e-post som skickas direkt från virtuella datorer i prenumerationerna:

- Microsoft Partner Network (MPN)
- BizSpark Plus
- Azure-sponsring

Om du vill kunna skicka e-post från virtuella Azure-datorer direkt till externa e-postleverantörer (utan att använda ett autentiserat SMTP-relä) kan du göra en begäran genom att öppna ett support ärende genom att använda följande typ av problem: **teknisk**  >  **Virtual Network**  >  **anslutning**  >  **kan inte skicka e-post (SMTP/port 25)**. Se till att lägga till information om varför din distribution måste skicka e-post direkt till e-postleverantörer i stället för att använda ett autentiserat relä. Begär Anden kommer att granskas och godkännas enligt Microsofts gottfinnande. Begär Anden beviljas endast efter att ytterligare antibedrägeri kontroller har slutförts. 

När en prenumeration är undantagen och de virtuella datorerna har stoppats och startats om i Azure Portal, undantas alla virtuella datorer i den prenumerationen. Undantaget gäller endast för den prenumeration som begärs och endast för VM-trafik som dirigeras direkt till Internet.

## <a name="need-help-contact-support"></a>Behöver du hjälp? Kontakta supporten

Om du fortfarande behöver hjälp kan du [kontakta supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) för att lösa problemet snabbt. Använd den här typen av problem: **teknisk**  >  **Virtual Network**  >  **anslutning**  >  **kan inte skicka e-post (SMTP/port 25)**.
