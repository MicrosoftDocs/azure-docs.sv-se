---
title: E-postmeddelanden för Azure AD Domain Services | Microsoft Docs
description: Lär dig hur du konfigurerar e-postaviseringar för att varna dig om problem i en Azure Active Directory Domain Services hanterad domän
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: b9af1792-0b7f-4f3e-827a-9426cdb33ba6
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: 4242092af86f8bc06ef9c9eba068ce07725de01f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96619172"
---
# <a name="configure-email-notifications-for-issues-in-azure-active-directory-domain-services"></a>Konfigurera e-postaviseringar för problem i Azure Active Directory Domain Services

Hälsan för en Azure Active Directory Domain Services (Azure AD DS)-hanterad domän övervakas av Azure-plattformen. Sidan hälso status i Azure Portal visar alla aviseringar för den hanterade domänen. För att säkerställa att problem besvaras inom rimlig tid kan e-postmeddelanden konfigureras för att rapportera om hälso aviseringar så snart de upptäcks i den hanterade domänen i Azure AD DS.

Den här artikeln visar hur du konfigurerar mottagare av e-postavisering för en hanterad domän.

## <a name="email-notification-overview"></a>Översikt över e-postavisering

För att varna dig om problem med en hanterad domän kan du konfigurera e-postaviseringar. De här e-postaviseringarna anger den hanterade domänen som aviseringen finns på, samt för att ge identifierings tiden och en länk till hälso sidan i Azure Portal. Du kan sedan följa den angivna fel söknings typen för att lösa problemen.

Följande exempel på ett e-postmeddelande indikerar en kritisk varning eller avisering som genererats på den hanterade domänen:

![Exempel på e-postavisering](./media/active-directory-domain-services-alerts/email-alert.png)

> [!WARNING]
> Se alltid till att e-postmeddelandet kommer från en verifierad Microsoft-avsändare innan du klickar på länkarna i meddelandet. E-postaviseringarna kommer alltid från `azure-noreply@microsoft.com` adressen.

### <a name="why-would-i-receive-email-notifications"></a>Varför får jag e-postaviseringar?

Azure AD DS skickar e-postaviseringar för viktiga uppdateringar om den hanterade domänen. Dessa meddelanden är bara för brådskande problem som påverkar tjänsten och som bör åtgärdas omedelbart. Varje e-postavisering utlöses av en avisering på den hanterade domänen. Aviseringarna visas också i Azure Portal och kan visas på [sidan för Azure AD DS-hälsa][check-health].

Azure AD DS skickar inte e-post för annonsering, uppdateringar eller försäljnings syfte.

### <a name="when-will-i-receive-email-notifications"></a>När får jag e-postaviseringar?

Ett meddelande skickas direkt när en [ny avisering][troubleshoot-alerts] hittas på en hanterad domän. Om aviseringen inte har lösts skickas ytterligare e-postmeddelanden som en påminnelse var fjärde dag.

### <a name="who-should-receive-the-email-notifications"></a>Vem ska ta emot e-postaviseringar?

Listan över e-postmottagare för Azure AD DS bör bestå av personer som kan administrera och göra ändringar i den hanterade domänen. Den här e-postlistan bör ses som dina "första svarare" till eventuella aviseringar och problem.

Du kan lägga till upp till fem ytterligare e-postmottagare för e-postaviseringar. Om du vill ha fler än fem mottagare för e-postaviseringar skapar du en distributions lista och lägger till den i meddelande listan i stället.

Du kan också välja att ha alla *globala administratörer* i Azure AD-katalogen och alla medlemmar i gruppen *AAD DC-administratörer* får e-postaviseringar. Azure AD DS skickar bara meddelanden till upp till 100 e-postadresser, inklusive listan över globala administratörer och AAD DC-administratörer.

## <a name="configure-email-notifications"></a>Konfigurera e-postaviseringar

Utför följande steg för att granska befintliga e-postmottagare eller lägga till ytterligare mottagare:

1. I Azure Portal söker du efter och väljer **Azure AD Domain Services**.
1. Välj din hanterade domän, till exempel *aaddscontoso.com*.
1. På vänster sida av fönstret Azure AD DS-resurs väljer du **meddelande inställningar**. Befintliga mottagare för e-postaviseringar visas.
1. Om du vill lägga till en e-postmottagare anger du e-postadressen i tabellen ytterligare mottagare.
1. När du är färdig väljer du **Spara** i det övre navigerings fältet.

> [!WARNING]
> När du ändrar meddelande inställningarna uppdateras meddelande inställningarna för hela den hanterade domänen, inte bara dig själv.

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

### <a name="i-received-an-email-notification-for-an-alert-but-when-i-logged-on-to-the-azure-portal-there-was-no-alert-what-happened"></a>Jag har fått ett e-postmeddelande om en avisering, men när jag är inloggad på Azure Portal finns det ingen avisering. Vad hände?

Om en avisering har åtgärd ATS tas aviseringen bort från Azure Portal. Den troligaste orsaken är att någon annan som tar emot e-postmeddelanden har löst aviseringen på den hanterade domänen, eller att den löstes av Azure-plattformen.

### <a name="why-can-i-not-edit-the-notification-settings"></a>Varför kan jag inte redigera meddelande inställningarna?

Om du inte kan komma åt sidan meddelande inställningar i Azure Portal har du inte behörighet att redigera den hanterade domänen. Kontakta en global administratör för att antingen få behörighet att redigera Azure AD DS-resurs eller ta bort den från listan över mottagare.

### <a name="i-dont-seem-to-be-receiving-email-notifications-even-though-i-provided-my-email-address-why"></a>Jag verkar inte ta emot e-postmeddelanden trots att jag har angett min e-postadress. Varför?

Kontrol lera din skräp post eller skräppostmappen i din e-postadress för aviseringen och se till att tillåta avsändaren `azure-noreply@microsoft.com` .

## <a name="next-steps"></a>Nästa steg

Mer information om fel sökning av problem som kan rapporteras finns i [lösa aviseringar på en hanterad domän][troubleshoot-alerts].

<!-- INTERNAL LINKS -->
[check-health]: check-health.md
[troubleshoot-alerts]: troubleshoot-alerts.md
