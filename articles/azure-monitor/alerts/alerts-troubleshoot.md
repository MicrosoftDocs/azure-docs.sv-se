---
title: Felsöka Azure Monitor aviseringar och meddelanden
description: Vanliga problem med Azure Monitor aviseringar och möjliga lösningar.
author: ofirmanor
ms.author: ofmanor
ms.topic: reference
ms.date: 03/16/2020
ms.openlocfilehash: d65fa03a183c15022535debee6476844192ec6bf
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102037854"
---
# <a name="troubleshooting-problems-in-azure-monitor-alerts"></a>Felsöka problem i Azure Monitor aviseringar

I den här artikeln beskrivs vanliga problem i Azure Monitor aviseringar och meddelanden.

Azure Monitor aviseringar proaktivt meddela dig när viktiga villkor finns i dina övervaknings data. De gör att du kan identifiera och åtgärda problem innan användarna av systemet ser dem. Mer information om aviseringar finns i [Översikt över aviseringar i Microsoft Azure](./alerts-overview.md).

Om du har problem med att en avisering ska uppstå eller inte uppstår när du förväntar dig, se artiklarna nedan. Du kan se "utlöst" aviseringar i Azure Portal.

- [Felsöka Azure Monitor mått varningar i Microsoft Azure](alerts-troubleshoot-metric.md)  
- [Felsöka Azure Monitor logg aviseringar i Microsoft Azure](alerts-troubleshoot-metric.md)

Om aviseringen utlöses enligt Azure Portal men det inte går att utföra rätt meddelanden använder du informationen i resten av den här artikeln för att felsöka problemet.

## <a name="action-or-notification-on-my-alert-did-not-work-as-expected"></a>Åtgärden eller meddelandet på aviseringen fungerade inte som förväntat

Om du kan se en utlöst avisering i Azure Portal, men har problem med några av dess åtgärder eller aviseringar, se följande avsnitt.

## <a name="did-not-receive-expected-email"></a>Tog inte emot den förväntade e-postadressen

Om du kan se en utlöst avisering i Azure Portal, men inte har fått något e-postmeddelande som du har konfigurerat, följer du dessa steg:

1. **Ignorerades e-postmeddelandet av en [Åtgärds regel](../alerts/alerts-action-rules.md)**?

    Kontrollera det genom att klicka på den utlösta aviseringen på portalen och gå till historikfliken där du kan se utelämnade [åtgärdsgrupper](./action-groups.md):

    ![Varnings historik för varnings åtgärds regel](media/alerts-troubleshoot/history-action-rule.png)

1. **Är typen av åtgärd "e-Azure Resource Manager roll"?**

    Den här åtgärden kan bara se Azure Resource Manager roll tilldelningar som finns i prenumerations omfånget, och av typen *användare*.  Kontrollera att du har tilldelat rollen på prenumerationsnivå och inte på resursnivå eller resursgruppnivå.

1. **Accepterar e-postservern och post lådan externa e-postmeddelanden?**

    Kontrollera att e-post från dessa tre adresser inte blockeras:
      - azure-noreply@microsoft.com  
      - azureemail-noreply@microsoft.com
      - alerts-noreply@mail.windowsazure.com

    Det är vanligt att interna e-postlistor eller distributionslistor blockerar e-postmeddelanden från externa e-postadresser. Du måste tillåta e-post från ovanstående e-postadresser.  
    Du kan testa genom att lägga till en vanlig e-postadress för arbetet (inte en distributionslista) i åtgärdsgruppen och se om aviseringar kommer till den e-postadressen.

1. **Har e-postmeddelandet bearbetats av reglerna för Inkorgen eller ett skräp post filter?**

    Kontrollera att det inte finns några inkorgsregler som tar bort dessa e-postmeddelanden och flyttar dem till en sidomapp. Reglerna för Inkorgen kan till exempel fånga vissa avsändare eller vissa ord i ämnet.

    Kontrollera också:

   - skräppostinställningarna för e-postklienten (t.ex. Outlook och Gmail)
      - avsändaren begränsar/spam-inställningar/karantän inställningar för din e-postserver (t. ex. Exchange, Microsoft 365, G-Suite)
      - inställningarna för din e-säkerhetsenhet, om några (som Barracuda, Cisco).

1. **Har du av misstag avbrutit prenumerationen på åtgärds gruppen?**

    E-postaviseringarna innehåller en länk för att avbryta prenumerationen på åtgärdsgruppen. Du kan kontrollera om du har avbrutit prenumerationen på den här åtgärdsgruppen av misstag genom att göra något av följande:

    1. Öppna åtgärdsgruppen i portalen och kontrollera kolumnen Status:

    ![statuskolumn för åtgärdsgrupp](media/alerts-troubleshoot/action-group-status.png)

    2. Söka i e-posten efter bekräftelsen på den avbrutna prenumerationen:

    ![prenumerationen på aviseringsåtgärdsgruppen har avbrutits](media/alerts-troubleshoot/unsubscribe-action-group.png)

    Om du vill prenumerera igen använder du länken i e-postmeddelandet om att prenumerationen har avbrutits som du har fått eller ta bort e-postadressen från åtgärdsgruppen och lägg sedan till den igen. 
 
1. **Har du betygsatts begränsad på grund av många e-postmeddelanden som ska skickas till en enda e-postadress?**

    Frekvensen för e-postmeddelanden är [begränsad](alerts-rate-limiting.md) till 100 e-postmeddelanden i timmen till varje e-postadress. Om du går över den tröskeln tas ytterligare e-postaviseringar bort.  Kontrollera om du har fått ett meddelande som anger att din e-postadress tillfälligt har frekvensbegränsats: 
 
   ![Frekvensbegränsning för e-post](media/alerts-troubleshoot/email-paused.png)

   Om du vill kunna ta emot ett stort antal aviseringar utan frekvensbegränsning bör du överväga att använda en annan åtgärd, till exempel en webhook, logisk app, Azure-funktion eller Automation-runbook. Inga av dessa är begränsade. 

## <a name="did-not-receive-expected-sms-voice-call-or-push-notification"></a>Fick inget förväntat SMS, röst samtal eller push-meddelande

Om du kan se en utlöst avisering på portalen, men inte fick något SMS, röstsamtal eller push-meddelande som du har konfigurerat, följer du dessa steg: 

1. **Ignorerades åtgärden av en [Åtgärds regel](../alerts/alerts-action-rules.md)?**

    Kontrollera det genom att klicka på den utlösta aviseringen på portalen och gå till historikfliken där du kan se utelämnade [åtgärdsgrupper](./action-groups.md): 

    ![Varnings historik för varnings åtgärds regel](media/alerts-troubleshoot/history-action-rule.png)

   Om det var oavsiktligt kan du ändra, inaktivera eller ta bort åtgärdsregeln.
 
1. **SMS/röst: är telefonnumret korrekt?**

   Kontrollera om landskoden eller telefonnumret i SMS-åtgärden innehåller fel.
 
1. **SMS/röst: har du begränsat pris nivån?**

    SMS-meddelanden och röstsamtal är frekvensbegränsade till högst ett meddelande var femte minut per telefonnummer. Om du överskrider det här tröskelvärdet tas meddelandena bort.

      - Röstsamtal – Kontrollera samtalshistoriken och se om du har fått ett annat samtal från Azure under de senaste fem minuterna.
      - SMS – Kontrollera din SMS-historik och leta efter ett meddelande som anger att ditt telefonnummer har frekvensbegränsats.

    Om du vill kunna ta emot ett stort antal aviseringar utan frekvensbegränsning bör du överväga att använda en annan åtgärd, till exempel en webhook, logisk app, Azure-funktion eller Automation-runbook. Inga av dessa är begränsade. 
 
1. **SMS: har du av misstag avbrutit prenumerationen på åtgärds gruppen?**

    Öppna din SMS-historik och kontrol lera om du har valt bort SMS-leverans från den här åtgärds gruppen (med hjälp av alternativet Inaktivera action_group_short_name svar) eller från alla åtgärds grupper (med hjälp av STOP-svaret). Om du vill prenumerera igen kan du antingen skicka relevant SMS-kommando (ENABLE action_group_short_name eller START) eller ta bort SMS-åtgärden från åtgärdsgruppen och sedan lägga tillbaka den igen.  Mer information finns i avsnittet om [SMS-aviseringsbeteende i åtgärdsgrupper](alerts-sms-behavior.md).

1. **Har du av misstag blockerat meddelanden på din telefon?**

   På de flesta mobiltelefoner kan du blockera samtal eller SMS från specifika telefonnummer eller kortnummer eller blockera push-meddelanden från vissa appar (till exempel Azure-mobilappen). Du kan kontrollera om du har råkat blockera meddelanden på din telefon genom att läsa dokumentationen för ditt telefonoperativsystem och din telefonmodell eller prova med en annan telefon och ett annat telefonnummer.

## <a name="expected-another-type-of-action-to-trigger-but-it-did-not"></a>En annan typ av åtgärd förväntades för utlösare, men det gick inte 
   
Om du kan se en utlöst avisering på portalen, och om dess konfigurerade åtgärd inte utlöstes, följer du dessa steg:

1. **Ignorerades åtgärden av en åtgärds regel?**

    Kontrollera det genom att klicka på den utlösta aviseringen på portalen och gå till historikfliken där du kan se utelämnade [åtgärdsgrupper](./action-groups.md):

    ![Varnings historik för varnings åtgärds regel](media/alerts-troubleshoot/history-action-rule.png)
 
    Om det var oavsiktligt kan du ändra, inaktivera eller ta bort åtgärdsregeln.

1. **Utlöstes en webhook?**

    1. **Har käll-IP-adresserna blockerats?**
    
       Lägg till [IP-adresserna](./action-groups.md#action-specific-information) som webhooken kallas från i listan över tillåtna.

    1. **Fungerar webhook-slutpunkten som den ska?**

       Kontrollera att slutpunkten som du har konfigurerat för webhooken stämmer och att den fungerar som den ska. Kontrollera dina webhook-loggar eller instrumentera dess kod så att du kan undersöka den (logga exempelvis den inkommande nyttolasten).

    1. **Anropar du Slack eller Microsoft Teams?**  
    Var och en av dessa slutpunkter förväntar sig ett specifikt JSON-format. Följ [de här anvisningarna](../alerts/action-groups-logic-app.md) för att konfigurera en åtgärd för en logikapp i stället.

    1. **Slutade webhooken att svara eller returnerade fel?** 

        Vår timeout-period för webhook-svar är 10 sekunder. Ytterligare två webhook-anrop görs när följande HTTP-statuskoder returneras: 408, 429, 503 eller 504, eller om HTTP-slutpunkten inte svarar. Det första återförsöket görs efter 10 sekunder. Det andra och sista återförsöket görs efter 100 sekunder. Om det andra återförsöket misslyckas anropas inte slutpunkten igen under 30 minuter för någon åtgärdsgrupp.

## <a name="action-or-notification-happened-more-than-once"></a>En åtgärd eller ett meddelande har inträffat mer än en gång 

Om du har fått ett meddelande om en avisering (till exempel ett e-postmeddelande eller ett SMS) mer än en gång, eller om aviseringens åtgärd (som en webhook eller Azure-funktion) har utlösts flera gånger, gör du så här: 

1. **Är det verkligen samma avisering?** 

    I vissa fall utlöses flera liknande aviseringar vid samma tidpunkt. Det kan därför verka som om samma avisering utlöste sin åtgärd flera gånger. Till exempel kan en aviseringsregel i aktivitetsloggen konfigureras så att den utlöses både när en händelse har startats och när den har slutförts (lyckats eller misslyckats), om du inte filtrerar på fältet med händelsestatusen. 

    Om du vill kontrollera om åtgärderna eller meddelandena kommer från olika aviseringar kan du granska aviseringsinformationen, till exempel dess tidstämpel och antingen aviserings-ID:t eller korrelations-ID:t. Du kan också kontrollera listan med utlösta aviseringar i portalen. I så fall måste du anpassa varnings regelns logik eller på annat sätt konfigurera aviserings källan. 

1. **Upprepas åtgärden i flera åtgärds grupper?** 

    När en avisering utlöses bearbetas var och en av åtgärdsgrupperna oberoende av varandra. Om en åtgärd (till exempel en e-postadress) förekommer i flera utlösta åtgärdsgrupper anropas den alltså en gång per åtgärdsgrupp. 

    Du kan kontrollera vilka åtgärdsgrupper som har utlösts på fliken med aviseringshistorik. Där ser du både åtgärdsgrupper som är definierade i aviseringsregeln och åtgärdsgrupper som lagts till i aviseringen via åtgärdsregler: 

    ![Åtgärden upprepas i flera åtgärds grupper](media/alerts-troubleshoot/action-repeated-multi-action-groups.png)

## <a name="action-or-notification-has-an-unexpected-content"></a>Åtgärden eller meddelandet har ett oväntat innehåll

Om du har fått aviseringen men tror att några av fälten i aviseringen saknas eller är felaktiga, följer du dessa steg: 

1. **Valde du rätt format för åtgärden?** 

    Varje åtgärds typ (e-post, webhook osv.) har två format – standardformat, bakåtkompatibelt format och det [nyare vanliga schema formatet](../alerts/alerts-common-schema.md). När du skapar en åtgärdsgrupp anger du önskat format för varje åtgärd – olika åtgärder i åtgärdsgrupperna kan ha olika format. 

    Till exempel för webhook-åtgärd: 

    ![schema alternativ för webhook-åtgärd](media/alerts-troubleshoot/webhook.png)

    Kontrollera att formatet som anges på åtgärdsnivå stämmer. Du kanske exempelvis har skapat kod som svarar på aviseringar (webhook, funktion, logikapp osv.), som förväntar sig ett format, men senare i åtgärden har du eller någon annan angett ett annat format.  

    Kontrollera också nyttolastformatet (JSON) för [aktivitetsloggaviseringar](../alerts/activity-log-alerts-webhook.md), för [loggsökningsaviseringar](../alerts/alerts-log-webhook.md) (både Application Insights och Log Analytics), för [måttaviseringar](alerts-metric-near-real-time.md#payload-schema), för [det gemensamma aviseringsschemat](../alerts/alerts-common-schema-definitions.md) och för de föråldrade [klassiska måttaviseringarna](./alerts-webhooks.md).

 
1. **Aktivitets logg aviseringar: är informationen tillgänglig i aktivitets loggen?** 

    [Aktivitets logg aviseringar](./activity-log-alerts.md) är aviseringar som baseras på händelser som skrivits till Azures aktivitets logg, till exempel händelser för att skapa, uppdatera eller ta bort Azure-resurser, service hälsa och resurs hälso händelser eller resultat från Azure Advisor och Azure policy. Om du har fått en avisering baserat på aktivitetsloggen och vissa fält som du behöver saknas eller är felaktiga, börjar du med att kontrollera händelserna i själva aktivitetsloggen. Om Azure-resursen inte skrev de fält som du letar efter i aktivitetslogghändelsen, kommer dessa fält inte att finnas med i motsvarande avisering. 

## <a name="action-rule-is-not-working-as-expected"></a>Åtgärdsregeln fungerar inte som förväntat 

Om du ser en avisering som har utlösts på portalen, men en relaterad åtgärdsregel inte fungerade som förväntat, följer du dessa steg: 

1. **Är åtgärds regeln aktive rad?** 

    Kontrollera statuskolumnen för åtgärdsregeln för att verifiera att den relaterade åtgärdsrollen är aktiverad. 

    ![Infoga](media/alerts-troubleshoot/action-rule-status.png) 

    Om åtgärdsregeln inte är aktiverad kan du aktivera den genom att markera den och klicka på Aktivera. 

1. **Är det en hälso varning för tjänsten?** 

    Aviseringar om tjänsthälsa (övervakningstjänst = ”Service Health”) påverkas inte av åtgärdsregler. 

1. **Kördes åtgärdsregeln för din avisering?** 

    Kontrollera att åtgärdsregeln har bearbetat aviseringen genom att klicka på den utlösta aviseringen på portalen och gå till historikfliken.

    Här är ett exempel på en åtgärdsregel som utelämnar alla åtgärdsgrupper: 
 
     ![Varnings historik för varnings åtgärds regel](media/alerts-troubleshoot/history-action-rule.png)

    Här är ett exempel på en åtgärdsregel som lägger till en till åtgärdsgrupp:

    ![Åtgärden upprepas i flera åtgärds grupper](media/alerts-troubleshoot/action-repeated-multi-action-groups.png)
 

1. **Matchar åtgärdsregelns omfång och filter den utlösta aviseringen?** 

    Om du tror att åtgärdsregeln borde ha utlösts men inte gjorde det, eller att den inte borde ha utlösts, men gjorde det, kontrollerar du och jämför åtgärdsregelns omfång och filtervillkor med egenskaperna för den utlösta aviseringen. 


## <a name="how-to-find-the-alert-id-of-a-fired-alert"></a>Så här hittar du aviserings-ID för en utlöst avisering

När du öppnar ett ärende om en angiven utlöst avisering (till exempel – om du inte har fått e-postmeddelandet) måste du ange aviserings-ID: t. 

Följ de här stegen för att hitta den:

1. I Azure Portal navigerar du till listan över aktiverade aviseringar och söker efter den aktuella aviseringen. Du kan använda filtren för att hitta det. 

1. Klicka på aviseringen för att öppna aviserings informationen. 

1. Rulla nedåt i aviserings fälten på den första fliken (fliken Sammanfattning) tills du har hittat den och kopiera den. Fältet innehåller också hjälp knappen Kopiera till Urklipp som du kan använda.  

    ![Hitta aviserings-ID](media/alerts-troubleshoot/get-alert-id.png)

## <a name="problem-creating-updating-or-deleting-action-rules-in-the-azure-portal"></a>Problem med att skapa, uppdatera eller ta bort åtgärds regler i Azure Portal

Följ dessa steg om du fick ett fel meddelande när du försökte skapa, uppdatera eller ta bort en [Åtgärds regel](../alerts/alerts-action-rules.md): 

1. **Fick du ett behörighets fel?**  

    Du bör antingen ha den [inbyggda rollen övervaknings deltagare](../../role-based-access-control/built-in-roles.md#monitoring-contributor)eller de behörigheter som är relaterade till åtgärds regler och aviseringar.

1. **Verifierade du åtgärds regel parametrarna?**  

    Kontrol lera [Åtgärds regel dokumentationen](../alerts/alerts-action-rules.md)eller kommandot [Åtgärds regel PowerShell set-AzActionRule](/powershell/module/az.alertsmanagement/set-azactionrule) . 


## <a name="next-steps"></a>Nästa steg
- Om du använder en logg avisering kan du också se [fel söknings logg aviseringar](./alerts-troubleshoot-log.md).
- Gå tillbaka till [Azure Portal](https://portal.azure.com) för att kontrol lera om du har löst problemet med vägledning ovan