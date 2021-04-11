---
title: Självstudie – pilot Azure AD Connect Cloud Sync för en befintlig synkroniserad AD-skog
description: Lär dig hur du piloterar Cloud Sync för en test Active Directory skog som redan har synkroniserats med Azure Active Directory (Azure AD) Connect-synkronisering.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: tutorial
ms.date: 03/22/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: e5053bd0c3d63b13b1021476a09dca39dd08f581
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105108765"
---
# <a name="pilot-cloud-sync-for-an-existing-synced-ad-forest"></a>Pilot molnets synkronisering för en befintlig synkroniserad AD-skog 

Den här självstudien vägleder dig genom pilotering av Cloud Sync för en test Active Directory skog som redan har synkroniserats med Azure Active Directory (Azure AD) Connect-synkronisering.

![Skapa](media/tutorial-migrate-aadc-aadccp/diagram-2.png)

## <a name="considerations"></a>Överväganden
Tänk på följande innan du provar den här självstudien:
1. Se till att du är bekant med grunderna i Cloud Sync. 
2. Se till att du kör Azure AD Connect Sync version 1.4.32.0 eller senare och har konfigurerat reglerna för synkronisering enligt dokumentationen. Vid pilotering kommer du att ta bort en test-OU eller-grupp från Azure AD Connect Sync-omfånget. Att flytta objekt utanför omfattning leder till att dessa objekt tas bort i Azure AD. Om det rör sig om användar objekt är objekten i Azure AD mjuk-borttagna och kan återställas. I händelse av grupp objekt tas objekten i Azure AD bort och kan inte återställas. En ny länktyp har introducerats i Azure AD Connect Sync som förhindrar borttagningen om ett pilot scenario används. 
3. Se till att objekten i pilot omfånget har ms-DS-consistencyGUID ifyllda så att molnets synkroniserings hård matchar objekten. 

   > [!NOTE]
   > Azure AD Connect Sync fyller inte i *MS-DS-consistencyGUID* som standard för grupp objekt.

4. Det här är ett avancerat scenario. Se till att du följer de steg som beskrivs i den här självstudien noggrant.

## <a name="prerequisites"></a>Förutsättningar
Följande är förutsättningar som krävs för den här självstudien
- En test miljö med Azure AD Connect Sync version 1.4.32.0 eller senare
- En ORGANISATIONSENHET eller grupp som finns i omfånget och kan användas som pilot. Vi rekommenderar att du börjar med en liten uppsättning objekt.
- En server som kör Windows Server 2012 R2 eller senare och som kommer att vara värd för etablerings agenten.
- Käll fäst punkten för Azure AD Connect Sync ska vara antingen *objectGUID* eller *MS-DS-consistencyGUID*

## <a name="update-azure-ad-connect"></a>Uppdatera Azure AD Connect

Som minst bör du ha [Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) -1.4.32.0. Slutför stegen i Azure AD Connect för att uppdatera Azure AD Connect synkronisering [: uppgradera till den senaste versionen](../hybrid/how-to-upgrade-previous-version.md).  

## <a name="stop-the-scheduler"></a>Stoppa Scheduler
Azure AD Connect synkronisera synkroniserar ändringar som sker i din lokala katalog med hjälp av en Scheduler. För att kunna ändra och lägga till anpassade regler vill du inaktivera Scheduler så att synkroniseringarna inte körs medan du arbetar med detta.  Gör så här:

1.  På den server som kör Azure AD Connect synkroniserar du öppna PowerShell med administratörs behörighet.
2.  Kör `Stop-ADSyncSyncCycle`.  Tryck på RETUR.
3.  Kör `Set-ADSyncScheduler -SyncCycleEnabled $false`.

>[!NOTE] 
>Om du kör en egen schemaläggare för Azure AD Connect synkronisering inaktiverar du Scheduler. 

## <a name="create-custom-user-inbound-rule"></a>Skapa anpassad regel för inkommande användare

 1. Starta redigeraren för synkronisering från program-menyn i Skriv bordet enligt nedan:</br>
 ![Redigerings meny för Synkroniseringsregel](media/tutorial-migrate-aadc-aadccp/user-8.png)</br>
 
 2. Välj **inkommande** i list rutan för riktning och klicka på **Lägg till ny regel**.
 ![Skärm bild som visar fönstret "Visa och hantera regler för synkronisering" med "inkommande" och knappen "Lägg till ny regel" vald.](media/tutorial-migrate-aadc-aadccp/user-1.png)</br>
 
 3. På sidan **Beskrivning** anger du följande och klickar på **Nästa**:

    **Namn:** Ge regeln ett beskrivande namn<br>
    **Beskrivning:** Lägg till en meningsfull beskrivning<br>
    **Anslutet system:** Välj den AD-anslutning som du vill skriva den anpassade synkroniseringsregeln för<br>
    **Ansluten system objekt typ:** Användarvänlig<br>
    **Metaversum-objekt typ:** Sända<br>
    **Länktyp:** Ansluta<br>
    **Prioritet:** Ange ett värde som är unikt i systemet<br>
    **Tagg:** Lämna detta tomt<br>
    ![Skärm bild som visar sidan "Skapa regel för inkommande synkronisering-Beskrivning" med värden som anges.](media/tutorial-migrate-aadc-aadccp/user-2.png)</br>
 
 4. På sidan **omfångs filter** anger du den organisationsenhet eller säkerhets grupp som du vill att piloten ska baseras på.  Om du vill filtrera på OU lägger du till OU-delen av det unika namnet. Den här regeln gäller för alla användare som finns i ORGANISATIONSENHETen.  Så om DN slutar med "OU = processors, DC = contoso, DC = com, lägger du till det här filtret.  Klicka på **Nästa**. 

    |Regel|Attribut|Operator|Värde|
    |-----|----|----|-----|
    |Scope OU|NAMNET|ENDSWITH|Unikt namn på ORGANISATIONSENHETen.|
    |Omfångs grupp||ISMEMBEROF|Unikt namn på säkerhets gruppen.|

    ![Skärm bild som visar sidan "Skapa regel för inkommande synkronisering" med ett omfångs filter värde angivet.](media/tutorial-migrate-aadc-aadccp/user-3.png)</br>
 
 5. Klicka på **Nästa** på sidan **Anslut** regler.
 6. På sidan **omvandlingar** lägger du till en konstant omvandling: Flow true to cloudNoFlow-attribut. Klicka på **Lägg till**.
 ![Skärm bild som visar sidan "Skapa regel för inkommande synkronisering-transformeringar" med flödet "konstant omvandling" tillagd.](media/tutorial-migrate-aadc-aadccp/user-4.png)</br>

Samma steg måste följas för alla typer av objekt (användare, grupp och kontakt). Upprepa steg per konfigurerad AD-anslutning/per AD-skog. 

## <a name="create-custom-user-outbound-rule"></a>Skapa anpassad regel för utgående användare

 1. Välj **utgående** i list rutan för riktning och klicka på **Lägg till regel**.
 ![Skärm bild som visar "utgående"-riktningen markerad och knappen Lägg till ny regel är markerad.](media/tutorial-migrate-aadc-aadccp/user-5.png)</br>
 
 2. På sidan **Beskrivning** anger du följande och klickar på **Nästa**:

    **Namn:** Ge regeln ett beskrivande namn<br>
    **Beskrivning:** Lägg till en meningsfull beskrivning<br>
    **Anslutet system:** Välj den Azure AD-anslutning som du vill använda för att skriva den anpassade synkroniseringsregeln för<br>
    **Ansluten system objekt typ:** Användarvänlig<br>
    **Metaversum-objekt typ:** Sända<br>
    **Länktyp:** JoinNoFlow<br>
    **Prioritet:** Ange ett värde som är unikt i systemet<br>
    **Tagg:** Lämna detta tomt<br>
    
    ![Skärm bild som visar sidan beskrivning med angivna egenskaper.](media/tutorial-migrate-aadc-aadccp/user-6.png)</br>
 
 3. På sidan **omfångs filter** väljer du **CloudNoFlow** lika med **Sant**. Klicka på **Nästa**.
 ![Anpassad regel](media/tutorial-migrate-aadc-aadccp/user-7.png)</br>
 
 4. Klicka på **Nästa** på sidan **Anslut** regler.
 5. På sidan **omvandlingar** klickar du på **Lägg till**.

Samma steg måste följas för alla typer av objekt (användare, grupp och kontakt).

## <a name="install-the-azure-ad-connect-provisioning-agent"></a>Installera Azure AD Connect etablerings agenten
1. Logga in på den server som du vill använda med företags administratörs behörighet.  Om du använder  [Basic AD-och Azure-miljön](tutorial-basic-ad-azure.md) blir det CP1.
2. Hämta Azure AD Connect Cloud Provisioning agent med hjälp av stegen som beskrivs [här](how-to-install.md#install-the-agent).
3. Kör Azure AD Connect Cloud Sync (AADConnectProvisioningAgent. Installer)
3. På Välkomst skärmen **godkänner** du licens villkoren och klickar på **Installera**.</br>
![Skärm bild som visar välkomst skärmen "Microsoft Azure A D Connect Provisioning agent".](media/how-to-install/install-1.png)</br>

4. När den här åtgärden har slutförts startas konfigurations guiden.  Logga in med ditt globala administratörs konto för Azure AD.
5. På skärmen **anslut Active Directory** klickar du på **Lägg till katalog** och loggar sedan in med ditt Active Directory administratörs konto.  Den här åtgärden lägger till din lokala katalog.  Klicka på **Nästa**.</br>
![Skärm bild som visar skärmen "Anslut Active Directory" med ett katalog värde angivet.](media/how-to-install/install-3a.png)</br>

6. Klicka på **Bekräfta** på sidan **konfiguration slutförd** .  Den här åtgärden registrerar och startar om agenten.</br>
![Skärm bild som visar skärmen "konfigurationen är klar" med knappen "bekräfta" markerad.](media/how-to-install/install-4a.png)</br>

7. När den här åtgärden har slutförts bör du se ett meddelande om **att det har verifierats.**  Du kan klicka på **Avsluta**.</br>
![Välkomstskärmen](media/how-to-install/install-5.png)</br>
8. Om du fortfarande ser den inledande välkomst skärmen klickar du på **Stäng**.

## <a name="verify-agent-installation"></a>Verifiera agent installation
Agent verifiering sker i Azure Portal och på den lokala server som kör-agenten.

### <a name="azure-portal-agent-verification"></a>Azure Portal agent verifiering
Följ dessa steg för att kontrol lera att agenten visas i Azure:

1. Logga in på Azure-portalen.
2. Till vänster väljer du **Azure Active Directory**, klickar på **Azure AD Connect** och i mitten väljer du **hantera synkronisering av moln**.</br>
![Azure-portalen](media/how-to-install/install-6.png)</br>

3.  Klicka på **Granska alla agenter** på skärmen **Azure AD Connect Cloud Sync** .
![Azure AD-etablering](media/how-to-install/install-7.png)</br>
 
4. På **skärmen lokala etablerings agenter** visas de agenter som du har installerat.  Kontrol lera att agenten i fråga finns där och har marker ATS som **inaktive rad**.  Agenten är inaktive rad som standard ![ etablerings agenter](media/how-to-install/verify-1.png)</br>

### <a name="on-the-local-server"></a>På den lokala servern
Verifiera att agenten körs genom att följa dessa steg:

1.  Logga in på servern med ett administratörs konto
2.  Öppna **tjänster** genom att antingen navigera till den eller genom att gå till starta/köra/Services. msc.
3.  Under **tjänster** ser du till **Microsoft Azure AD ansluta Agent updateer** och **Microsoft Azure AD ansluta etablerings agenten** finns där, och statusen **körs**.
![Tjänster](media/how-to-install/troubleshoot-1.png)

## <a name="configure-azure-ad-connect-cloud-sync"></a>Konfigurera Azure AD Connect Cloud Sync
Använd följande steg för att konfigurera etableringen:

 1. Logga in på Azure AD-portalen.
 2. Klicka på **Azure Active Directory**
 3. Klicka på **Azure AD Connect**
 4. Välj **Hantera Cloud Sync**- 
  ![ skärm bild som visar länken hantera molnbaserad synkronisering.](media/how-to-configure/manage-1.png)</br>
 5.  Klicka på **ny konfiguration** 
  ![ skärm bild av skärmen Azure AD Connect Cloud Sync med länken "ny konfiguration" markerad.](media/tutorial-single-forest/configure-1.png)</br>
 6.  På sidan konfiguration anger du ett **e-postmeddelande för aviseringar**, flyttar väljaren för att **Aktivera** och klickar på **Spara**.
 ![Skärm bild av konfigurera skärmen med e-postmeddelandet ifyllt och aktivera valt.](media/tutorial-single-forest/configure-2.png)</br>
 7. Under **Konfigurera** väljer du **alla användare** för att ändra omfånget för konfigurations regeln.
 ![Skärm bild av skärmen konfigureras med "alla användare" markerat bredvid "omfattnings användare".](media/how-to-configure/scope-2.png)</br>
 8. Till höger ändrar du omfattningen så att den innehåller den angivna ORGANISATIONSENHETen som du nyss skapade "OU = processorer, DC = contoso, DC = com".
 ![Skärm bild av skärmen omfattnings användare som markerar omfånget som har ändrats till ORGANISATIONSENHETen som du skapade.](media/tutorial-existing-forest/scope-2.png)</br>
 9.  Klicka på **färdig** och **Spara**.
 10. Omfånget ska nu vara inställt på en organisationsenhet. 
 ![Skärm bild av skärmen konfigurera med "1 organisationsenhet" markerad bredvid "omfattnings användare".](media/tutorial-existing-forest/scope-3.png)</br>
 

## <a name="verify-users-are-provisioned-by-cloud-sync"></a>Verifiera att användarna har tillhandahållits av en molnbaserad synkronisering
Nu ska du kontrol lera att användarna som du hade i vår lokala katalog har synkroniserats och nu finns i Azure AD-klienten.  Observera att det kan ta några timmar att slutföra.  Följ dessa steg om du vill verifiera att användarna har etablering av en molnbaserad synkronisering:

1. Bläddra till [Azure-portalen](https://portal.azure.com) och logga in med ett konto som har en Azure-prenumeration.
2. Välj **Azure Active Directory** till vänster
3. Klicka på **Azure AD Connect**
4. Klicka på **hantera synkronisering av moln**
5. Klicka på **loggar** -knappen
6. Sök efter ett användar namn för att bekräfta att användaren har tillhandahållits av en molnbaserad synkronisering

Dessutom kan du kontrol lera att användaren och gruppen finns i Azure AD.

## <a name="start-the-scheduler"></a>Starta Scheduler
Azure AD Connect synkronisera synkroniserar ändringar som sker i din lokala katalog med hjälp av en Scheduler. Nu när du har ändrat reglerna kan du starta om Scheduler.  Gör så här:

1.  Öppna PowerShell med administratörs behörighet på den server som kör Azure AD Connect Sync
2.  Kör `Set-ADSyncScheduler -SyncCycleEnabled $true`.
3.  Kör `Start-ADSyncSyncCycle`.  Tryck på RETUR.  

>[!NOTE] 
>Aktivera Scheduler om du kör en egen schemaläggare för Azure AD Connect synkronisering. 

När Scheduler har Aktiver ATS kommer Azure AD Connect att sluta exportera ändringar på objekt med `cloudNoFlow=true` i metaversum, om inte något Reference-attribut (t. ex. Manager) håller på att uppdateras. Om det finns en uppdatering av säkerhetsattribut för objektet, kommer Azure AD Connect ignorera `cloudNoFlow` signalen och exportera alla uppdateringar på objektet.

## <a name="something-went-wrong"></a>Något gick fel
Om piloten inte fungerar som förväntat kan du gå tillbaka till Azure AD Connect Sync-installationen genom att följa stegen nedan:
1.  Inaktivera etablerings konfiguration i Azure Portal. 
2.  Inaktivera alla regler för anpassad synkronisering som skapats för moln etablering med hjälp av verktyget Sync Rule Editor. Om du inaktiverar, bör det orsaka fullständig synkronisering för alla anslutningar.



## <a name="next-steps"></a>Nästa steg 

- [Vad är etablering?](what-is-provisioning.md)
- [Vad är Azure AD Connect Cloud Sync?](what-is-cloud-sync.md)

