---
title: Så här åtgärdar du ändrade standard regler – Azure AD Connect | Microsoft Docs
description: Lär dig hur du åtgärdar ändrade standard regler som medföljer Azure AD Connect.
services: active-directory
author: billmath
manager: daveba
editor: curtand
ms.reviewer: darora10
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/21/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: a0fc1bc3158e04c9b1f677af7ef2375ac3ed2ce7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91320055"
---
# <a name="fix-modified-default-rules-in-azure-ad-connect"></a>Korrigera ändrade standard regler i Azure AD Connect

Azure Active Directory (Azure AD) Connect använder standard regler för synkronisering.  Dessa regler gäller tyvärr inte universellt för alla organisationer. Utifrån dina krav kan du behöva ändra dem. I den här artikeln beskrivs två exempel på de vanligaste anpassningarna och en beskrivning av det korrekta sättet att uppnå dessa anpassningar.

>[!NOTE] 
> Det finns inte stöd för att ändra befintliga standard regler för att uppnå nödvändig anpassning. Om du gör det förhindrar den att du uppdaterar dessa regler till den senaste versionen i framtida versioner. Du får inte de fel korrigeringar du behöver eller nya funktioner. I det här dokumentet beskrivs hur du uppnår samma resultat utan att ändra de befintliga standard reglerna. 

## <a name="how-to-identify-modified-default-rules"></a>Så här identifierar du ändrade standard regler
Från och med version 1.3.7.0 av Azure AD Connect är det enkelt att identifiera den ändrade standard regeln. Gå till **appar på Skriv bordet** och välj **Redigeraren för regler för synkronisering**.

![Azure AD Connect, med redigeraren för regler för synkronisering](media/how-to-connect-fix-default-rules/default1.png)

I redigeraren visas eventuella ändrade standard regler med en varnings ikon framför namnet.

![Varningsikon](media/how-to-connect-fix-default-rules/default2.png)

 En inaktive rad regel med samma namn bredvid den visas också (standard regeln är standard).

![Redigerare för Synkroniseringsregel, som visar standard standard regel och ändrad standard regel](media/how-to-connect-fix-default-rules/default2a.png)

## <a name="common-customizations"></a>Vanliga anpassningar
Följande är vanliga anpassningar av standard reglerna:

- Ändra attributets flöde
- Ändra omfångs filter
- Ändra kopplings villkor

Innan du ändrar några regler:

- Inaktivera synkroniseringsschemat. Scheduler körs var 30: e minut som standard. Kontrol lera att det inte startar när du gör ändringar och felsöka dina nya regler. För att tillfälligt inaktivera Scheduler, starta PowerShell och kör `Set-ADSyncScheduler -SyncCycleEnabled $false` .
 ![PowerShell-kommandon för att inaktivera synkroniseringsschemat](media/how-to-connect-fix-default-rules/default3.png)

- Filtret ändra i omfånget kan resultera i att objekt i mål katalogen tas bort. Var försiktig innan du gör några ändringar i objektets omfattning. Vi rekommenderar att du gör ändringar i en fristående server innan du gör ändringar på den aktiva servern.
- Kör en förhands granskning på ett enskilt objekt, enligt vad som anges i avsnittet [Verifiera Synkroniseringsregel](#validate-sync-rule) , efter att du lagt till en ny regel.
- Kör en fullständig synkronisering när du har lagt till en ny regel eller ändrat en anpassad Synkroniseringsregel. Den här synkroniseringen tillämpar nya regler på alla objekt.

## <a name="change-attribute-flow"></a>Ändra attributets flöde
Det finns tre olika scenarier för att ändra attributet Flow:
- Lägger till ett nytt attribut.
- Åsidosätter värdet för ett befintligt attribut.
- Väljer att inte synkronisera ett befintligt attribut.

Du kan göra detta utan att ändra standard standard reglerna.

### <a name="add-a-new-attribute"></a>Lägg till ett nytt attribut
Om du upptäcker att ett attribut inte flödar från käll katalogen till mål katalogen använder du [Azure AD Connect Sync: Katalog tillägg](how-to-connect-sync-feature-directory-extensions.md) för att åtgärda detta.

Om tilläggen inte fungerar för dig kan du försöka lägga till två nya regler för synkronisering som beskrivs i följande avsnitt.


#### <a name="add-an-inbound-sync-rule"></a>Lägg till en regel för inkommande synkronisering
En regel för inkommande synkronisering innebär att källan för attributet är ett anslutnings utrymme och att målet är metaversum. Om du till exempel vill ha ett nytt attributarkiv från lokala Active Directory till Azure Active Directory skapar du en ny regel för inkommande synkronisering. Starta **Redigeraren för regler för synkronisering**, Välj **inkommande** som riktning och välj **Lägg till ny regel**. 

 ![Skärm bild som visar "redigeraren för regler för synkronisering" med "inkommande" och "Lägg till ny regel" vald.](media/how-to-connect-fix-default-rules/default3a.png)

Namnge regeln genom att följa din egen namngivnings konvention. Här använder vi **Anpassad i från AD-användare**. Det innebär att regeln är en anpassad regel och är en regel för inkommande trafik från Active Directory kopplings utrymme till metaversum.   

 ![Skapa regel för inkommande synkronisering](media/how-to-connect-fix-default-rules/default3b.png)

Ange en egen beskrivning av regeln, så att framtida underhåll av regeln är enkelt. Beskrivningen kan till exempel baseras på vad målet med regeln är och varför det behövs.

Gör dina val för fälten **ansluten system**, **ansluten system objekt typ** och **metaversum objekt typ** .

Ange prioritet svärdet från 0 till 99 (ju lägre siffra, desto högre prioritet). För **taggen** aktiverar du **synkronisering av lösen ord** och **inaktiverade** fält, använder standard alternativen.

Behåll **omfångs filter** tomt. Det innebär att regeln gäller för alla objekt som är anslutna mellan Active Directory anslutna systemet och metaversum.

Behåll **kopplings regler** tomma. Det innebär att regeln använder det kopplings villkor som definierats i standard standard regeln. Detta är ett annat skäl till att inte inaktivera eller ta bort standard standard regeln. Om det inte finns något kopplings villkor flödar inte attributet. 

Lägg till lämpliga omvandlingar för attributet. Du kan tilldela en konstant för att göra ett konstant värde flöde till målattributet. Du kan använda direkt mappning mellan käll-eller mål-attributet. Du kan också använda ett uttryck för attributet. Här är olika [uttrycks funktioner](./reference-connect-sync-functions-reference.md) som du kan använda.

#### <a name="add-an-outbound-sync-rule"></a>Lägg till en regel för utgående synkronisering
Om du vill länka attributet till mål katalogen måste du skapa en utgående regel. Det innebär att källan är metaversum och att målet är det anslutna systemet. Om du vill skapa en utgående regel startar du **Redigeraren för regler för synkronisering**, ändrar **riktningen** till **utgående** och väljer **Lägg till ny regel**. 

![Redigerare för regler för synkronisering](media/how-to-connect-fix-default-rules/default3c.png)

Precis som med regeln för inkommande trafik kan du använda en egen namngivnings konvention för att ge regeln ett namn. Välj det **anslutna systemet** som Azure AD-klient och välj det anslutna system objekt som du vill ange attributvärdet till. Ange prioriteten från 0 till 99. 

![Skapa regel för utgående synkronisering](media/how-to-connect-fix-default-rules/default3d.png)

Behåll **omfångs filter** och **Anslut till regler** tomma. Fyll i omvandlingen som konstant, direkt eller uttryck. 

Nu vet du hur du gör ett nytt attribut för ett användar objekt flöde från Active Directory till Azure Active Directory. Du kan använda de här stegen för att mappa alla attribut från alla objekt till källa och mål. Mer information finns i [skapa anpassade regler för synkronisering](how-to-connect-create-custom-sync-rule.md) och [förbereda för att etablera användare](/office365/enterprise/prepare-for-directory-synchronization).

### <a name="override-the-value-of-an-existing-attribute"></a>Åsidosätt värdet för ett befintligt attribut
Du kanske vill åsidosätta värdet för ett attribut som redan har mappats. Om du till exempel alltid vill ange ett null-värde för ett attribut i Azure AD skapar du bara en regel för inkommande trafik. Gör värdet konstant, `AuthoritativeNull` och flöda till målattributet. 

>[!NOTE] 
> Använd i `AuthoritativeNull` stället för `Null` i det här fallet. Detta beror på att värdet som inte är null ersätter null-värdet, även om det har lägre prioritet (ett högre värde i regeln). `AuthoritativeNull`, å andra sidan, ersätts inte med ett värde som inte är null av andra regler. 

### <a name="dont-sync-existing-attribute"></a>Synkronisera inte befintligt attribut
Om du vill undanta ett attribut från synkronisering använder du funktionen Filtrera attribut som finns i Azure AD Connect. Starta **Azure AD Connect** från Skriv bords ikonen och välj sedan **Anpassa alternativ för synkronisering**.

![Azure AD Connect alternativ för ytterligare aktiviteter](media/how-to-connect-fix-default-rules/default4.png)

 Kontrol lera att **Azure AD-App-och-Attribute-filtrering** är markerat och välj **Nästa**.

![Azure AD Connect valfria funktioner](media/how-to-connect-fix-default-rules/default5.png)

Ta bort de attribut som du vill undanta från synkroniseringen.

![Azure AD Connect attribut](media/how-to-connect-fix-default-rules/default6a.png)

## <a name="change-scoping-filter"></a>Ändra omfångs filter
Azure AD Sync tar hand om de flesta av objekten. Du kan minska objektets omfattning och minska antalet objekt som ska exporteras, utan att ändra standard reglerna för synkronisering. 

Använd någon av följande metoder för att minska omfånget för de objekt som du synkroniserar:

- cloudFiltered-attribut
- Organisations enhets filtrering

Om du minskar omfånget för de användare som synkroniseras stoppas även lösen ordets hash-synkronisering för de filtrerade användarna. Om objekten redan synkroniseras tas de filtrerade objekten bort från mål katalogen när du har minskat omfattningen. Därför bör du se till att du omfånget är mycket noggrant.

>[!IMPORTANT] 
> Det rekommenderas inte att utöka omfånget för objekt som kon figurer ATS av Azure AD Connect. Det gör det svårt för Microsoft Support-teamet att förstå anpassningarna. Om du måste öka objektets omfång, redigerar du den befintliga regeln, klonar den och inaktiverar den ursprungliga regeln. 

### <a name="cloudfiltered-attribute"></a>cloudFiltered-attribut
Du kan inte ange det här attributet i Active Directory. Ange värdet för det här attributet genom att lägga till en ny regel för inkommande trafik. Du kan sedan använda **transformering** och **uttryck** för att ange det här attributet i metaversum. I följande exempel visas att du inte vill synkronisera alla användare vars avdelnings namn börjar med **HRD** (inte Skift läges känsligt):

`cloudFiltered <= IIF(Left(LCase([department]), 3) = "hrd", True, NULL)`

Vi konverterade först avdelningen från källan (Active Directory) till gemener. Sedan `Left` tog vi bara de första tre tecknen och jämför det med med hjälp av funktionen `hrd` . Om den matchas anges värdet till `True` , annars `NULL` . Vid inställning av värdet till null, kan en annan regel med lägre prioritet (ett högre värde) skriva till den med ett annat villkor. Kör förhands granskning på ett objekt för att validera synkroniseringsregeln, enligt vad som anges i avsnittet [Verifiera synkroniseringsstatus](#validate-sync-rule) .

![Skapa regel alternativ för inkommande synkronisering](media/how-to-connect-fix-default-rules/default7a.png)

### <a name="organizational-unit-filtering"></a>Organisations enhets filtrering
Du kan skapa en eller flera organisationsenheter (OU) och flytta de objekt som du inte vill synkronisera till dessa organisationsenheter. Konfigurera sedan ORGANISATIONSENHETs filtrering i Azure AD Connect. Starta **Azure AD Connect** från Skriv bords ikonen och välj följande alternativ. Du kan också konfigurera ORGANISATIONSENHETs filtrering vid tidpunkten för installationen av Azure AD Connect. 

![Azure AD Connect ytterligare aktiviteter](media/how-to-connect-fix-default-rules/default8.png)

Följ guiden och rensa de organisationsenheter som du inte vill synkronisera.

![Azure AD Connect alternativ för domän-och OU-filtrering](media/how-to-connect-fix-default-rules/default9.png)

## <a name="change-join-condition"></a>Ändra kopplings villkor
Använd standard kraven för anslutning som kon figurer ATS av Azure AD Connect. Om du ändrar standard villkoren för anslutning blir det svårt för Microsoft-support att förstå anpassningarna och stödja produkten.

## <a name="validate-sync-rule"></a>Verifiera Synkroniseringsregel
Du kan validera den nyligen tillagda synkroniseringsregeln med hjälp av funktionen för förhands granskning utan att köra den fullständiga synkroniseringen. I Azure AD Connect väljer du **synkroniseringstjänst**.

![Azure AD Connect med markerad synkroniseringstjänst](media/how-to-connect-fix-default-rules/default10.png)

Välj **metaversum-sökning**. Välj scope-objektet som **person**, Välj **Lägg till sats** och ange Sök kriterierna. Välj sedan **Sök** och dubbelklicka på objektet i Sök resultatet. Se till att dina data i Azure AD Connect är uppdaterade för det objektet genom att köra import och synkronisering i skogen innan du kör det här steget.

![Synchronization Service Manager](media/how-to-connect-fix-default-rules/default11.png)

På **metaversum objekt egenskaper** väljer du **kopplingar**, väljer objektet i motsvarande koppling (skog) och väljer **Egenskaper.**...

![Egenskaper för metaversum-objekt](media/how-to-connect-fix-default-rules/default12.png)

Välj för **hands version...**

![Objekt egenskaper för kopplings utrymme](media/how-to-connect-fix-default-rules/default13a.png)

I förhands gransknings fönstret väljer du **generera förhands granskning** och **Importera attributarkiv** i det vänstra fönstret.

![Skärm bild som visar "för hands version"-fönstret med "Importera attributvärde" och "generera förhands granskning" markerat.](media/how-to-connect-fix-default-rules/default14.png)
 
Lägg märke till att den nyligen tillagda regeln körs på objektet och har ange `cloudFiltered` attributet till sant.

![Förhandsgranskning](media/how-to-connect-fix-default-rules/default15a.png)
 
Om du vill jämföra den ändrade regeln med standard regeln exporterar du båda reglerna separat som textfiler. Dessa regler exporteras som en PowerShell-skriptfil. Du kan jämföra dem med hjälp av valfritt verktyg för fil jämförelse (till exempel Windiff) för att se ändringarna. 
 
Observera att attributet i den ändrade regeln `msExchMailboxGuid` ändras till **uttrycks** typen, i stället för **direkt**. Värdet ändras också till **Null** och alternativet **ExecuteOnce** . Du kan ignorera identifierade skillnader och prioriteter. 

![utdata för Windiff-verktyget](media/how-to-connect-fix-default-rules/default17.png)
 
Om du vill åtgärda reglerna för att ändra tillbaka till standardinställningarna tar du bort den ändrade regeln och aktiverar standard regeln. Se till att du inte förlorar den anpassning som du försöker uppnå. När du är klar kan du köra **fullständig synkronisering**.

## <a name="next-steps"></a>Nästa steg
- [Maskin vara och krav](how-to-connect-install-prerequisites.md) 
- [Standardinställningar](how-to-connect-install-express.md)
- [Anpassade inställningar](how-to-connect-install-custom.md)