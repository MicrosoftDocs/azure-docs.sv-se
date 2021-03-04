---
title: Svara på händelser med Azure Log Analytics-aviseringar | Microsoft Docs
description: Den här självstudien hjälper dig att förstå aviseringar i Log Analytics som visar viktig information på din arbetsyta. Du kan få tidiga meddelanden om problem eller anropa åtgärder om du vill försöka åtgärda dem.
ms.topic: tutorial
author: bwren
ms.author: bwren
ms.date: 10/05/2018
ms.custom: mvc
ms.openlocfilehash: 2564711fce6e8578829fb15fdbc04ae2b9d64959
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102039435"
---
# <a name="respond-to-events-with-azure-monitor-alerts"></a>Svara på händelser med Azure Monitor-aviseringar
Med aviseringar i Azure Monitor kan du identifiera viktig information på Log Analytics-lagringsplatsen. De skapas från varningsregler som automatiskt kör regelbundna loggsökningar. Om resultatet från loggsökningen matchar särskilda kriterier, skapas en aviseringspost som kan konfigureras till en automatisk åtgärd.  Den här självstudien är en fortsättning på självstudien [Skapa och dela instrumentpaneler med Log Analytics-data](../visualize/tutorial-logs-dashboards.md).   

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa en varningsregel
> * Konfigurera en åtgärdsgrupp som skickar ett e-postmeddelande

Du måste ha en befintlig virtuell dator [som är ansluten till Log Analytics-arbetsytan](../vm/quick-collect-azurevm.md) för att kunna utföra exemplet i självstudien.  

## <a name="sign-in-to-azure-portal"></a>Logga in på Azure-portalen
Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com). 

## <a name="create-alerts"></a>Skapa aviseringar
Aviseringar skapas av aviseringsregler i Azure Monitor och kan automatiskt köra sparade frågor eller anpassade loggsökningar med jämna mellanrum.  Du kan skapa aviseringar baserat på specifika prestandamått, när vissa händelser skapas, om en händelse saknas, eller om flera händelser skapas inom ett visst tidsintervall.  Aviseringar kan exempelvis användas för att meddela dig när den genomsnittliga CPU-användningen överskrider ett visst tröskelvärde, när en saknad uppdatering eller när en händelse genereras när det upptäckts att en specifik Windows-tjänst eller Linux-daemon inte körs.  Om resultatet av loggsökningen matchar särskilda villkor, skapas en avisering. Regeln kan sedan automatiskt köra en eller flera åtgärder, som att t.ex. meddela dig om aviseringen eller anropa en annan process. 

I följande exempel skapar du ett måttaviseringsregel som baseras på frågan *Virtuella datorer i Azure – processoranvändning* som sparats i kursen [Visualisera data](../visualize/tutorial-logs-dashboards.md).  En avisering skapas för varje virtuell dator som överskrider ett tröskelvärde på 90 %.  

1. Klicka på **Alla tjänster** i Azure-portalen. I listan över resurser skriver du **Log Analytics**. När du börjar skriva filtreras listan baserat på det du skriver. Välj **Log Analytics**.
2. Skapa en ny avisering genom att välja **Aviseringar** det vänstra fönstret och sedan klicka på **Ny aviseringsregel** högst upp på sidan.<br><br> ![Skapa en ny aviseringsregel](./media/tutorial-response/alert-rule-02.png)<br>
3. I det första steget ska du välja din Log Analytics-arbetsyta som resurs i avsnittet **Skapa avisering** eftersom detta är en loggbaserad aviseringssignal.  Filtrera resultaten genom att välja en specifik **prenumeration** från den nedrullningsbara listan, om du har mer än en prenumeration, som innehåller den virtuella datorn och Log Analytics-arbetsytan som du skapade tidigare.  Filtrera **resurstypen** genom att välja **Log Analytics** i den nedrullningsbara listan.  Välj slutligen **resursen** **DefaultLAWorkspace** och klicka spå **Klar**.<br><br> ![Skapa en aviseringssteg 1-uppgift](./media/tutorial-response/alert-rule-03.png)<br>
4. Välj din sparade fråga genom att klicka på **Lägg till villkor** i avsnittet **Aviseringskriterier** och ange sedan den logik som aviseringsregeln följer.  Välj *Virtuella datorer i Azure – processoranvändning* från listan i rutan **Konfigurera signallogik** .  Fönstret uppdateras och visar aviseringens konfigurationsinställningar.  Högst upp visas resultaten för den valda signalens senaste 30 minuter och själva sökfrågan.  
5. Konfigurera aviseringen med följande information:  
   a. Välj **mått mått** i list rutan **baserad på** .  Ett metriskt mått skapar en avisering för varje objekt i frågan med ett värde som överstiger det angivna tröskelvärdet.  
   b. Välj **Större än** som **Villkor** och ange **90** som **Tröskelvärde**.  
   c. Välj **Efterföljande överträdelser** i avsnittet Utlös avisering baserat på och välj **Större än** i listrutan och ange värdet 3.  
   d. Under Utvärdering, baserat på avsnittet, ändrar du värdet **Period** till **30** minuter. Regeln körs var femte minut och returnera poster som har skapats i det här intervallet under de senaste 30 minuterna.  Om tidsperioden ställs in på ett bredare fönster tas risken för datafördröjning med i beräkningen, och det ser till att frågan returnerar data för att undvika ett falskt negativt svar där aviseringen aldrig utlöses.  
6. Slutför aviseringsregeln genom att klicka på **Spara**.<br><br> ![Konfigurera aviseringssignal](./media/tutorial-response/alert-signal-logic-02.png)<br> 
7. Nu när vi går vidare till nästa steg ska du ge aviseringen ett namn i fältet **Aviseringsregelns namn**, t.ex. **CPU % högre än 90**.  Ge en **beskrivning** med information om aviseringen och välj alternativet **Critical(Sev 0)** som **Allvarlighetsgrad**.<br><br> ![Konfigurera aviseringsinformation](./media/tutorial-response/alert-signal-logic-04.png)<br>
8. Du kan aktivera aviseringsregeln omedelbart genom att acceptera standardvärdet för **Aktivera regel när du skapar**.
9. I det tredje och sista steget anger du en **åtgärdsgrupp**, vilket säkerställer att samma åtgärder vidtas varje gång en avisering utlöses och kan användas för varje regel som du definierar.  Konfigurera en ny åtgärdsgrupp med följande information:  
   a. Välj **Ny åtgärdsgrupp** varvid fönstret **Lägg till grupp** visas.  
   b. Ange ett namn, t.ex. **IT-avdelningen – meddela**, i **Namn på åtgärdsgrupp** och ange ett **kortnamn**, t.ex. **it-avd-m**.  
   c. Kontrollera att standardvärdena för **Prenumeration** och **Resursgrupp** är korrekta. Om så inte skulle vara fallet väljer du korrekta värden från den nedrullningsbara listan.   
   d. Ge åtgärden ett namn i avsnittet Åtgärder, t.ex. **Skicka e-post** och välj **e-post/SMS/push/röst** i listrutan under **Åtgärdstyp**. Egenskapsrutan **e-post/SMS/Push/röst** öppnas till höger och ger ytterligare information.  
   e. Aktivera **E-post** i rutan **e-post/SMS/Push/röst** och ge en giltig SMTP-e-postadress som meddelandet kan levereras till.  
   f. Spara ändringarna genom att klicka på **OK**.<br><br> 

    ![Skapa ny åtgärdsgrupp](./media/tutorial-response/action-group-properties-01.png)

10. Klicka på **OK** för att slutföra åtgärdsgruppen. 
11. Färdigställ aviseringsregeln genom att klicka på **Skapa aviseringsregel**. Den börjar köras omedelbart.<br><br> ![Slutför skapandet av ny aviseringsregel](./media/tutorial-response/alert-rule-01.png)<br> 

## <a name="view-your-alerts-in-azure-portal"></a>Visa dina aviseringar i Azure Portal
Nu när du har skapat en avisering kan du visa Azure-aviseringar i ett enskilt fönster och hantera alla aviseringsregler för alla dina Azure-prenumerationer. Det listar alla aviseringsregler (aktiverade såväl som inaktiverade) och det kan sorteras efter målresurser, resursgrupper, regelnamn eller status. En sammanfattning av alla utlösta aviseringar och det totala antalet konfigurerade/aktiverade aviseringsregler ingår.<br><br> ![Statussida för Azure-aviseringar](./media/tutorial-response/azure-alerts-02.png)  

När aviseringen utlöses speglar tabellen villkoret och hur många gånger det har förekommit under det valda tidsintervallet (standardinställningen är de senaste sex timmarna).  Det ska finnas ett e-postmeddelande i din inkorg, liknande det i följande exempel, som visar den felaktiga virtuella datorn och de översta resultaten som matchar sökfrågan i det här fallet.<br><br> ![Exempel på e-postmeddelande med åtgärdsavisering](./media/tutorial-response/azure-alert-email-notification-01.png)

## <a name="next-steps"></a>Nästa steg
I den här självstudien har du lärt dig hur varningsregler proaktivt kan identifiera och åtgärda problem när de kör loggsökningar i schemalagda intervall och matchar ett särskilt villkor.

Följ den här länken om du vill se inbyggda skriptexempel för Log Analytics.  

> [!div class="nextstepaction"]
> [Skriptexempel för Log Analytics](../powershell-samples.md)