---
title: 'Azure AD Connect synkronisering: Konfigurera filtrering | Microsoft Docs'
description: Förklarar hur du konfigurerar filtrering i Azure AD Connect Sync.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 880facf6-1192-40e9-8181-544c0759d506
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 03/26/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 595cf2c1dbc105634d33b426c67e5123b9751e6e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "95996550"
---
# <a name="azure-ad-connect-sync-configure-filtering"></a>Azure AD Connect-synkronisering: Konfigurera filtrering
Genom att använda filtrering kan du styra vilka objekt som visas i Azure Active Directory (Azure AD) från din lokala katalog. Standard konfigurationen tar alla objekt i alla domäner i de konfigurerade skogarna. I allmänhet är detta den rekommenderade konfigurationen. Användare som använder Microsoft 365 arbets belastningar, till exempel Exchange Online och Skype för företag, drar nytta av en fullständig global adress lista så att de kan skicka e-post och ringa alla. Med standard konfigurationen har de samma erfarenhet som de skulle ha med en lokal implementering av Exchange eller Lync.

I vissa fall är det dock nödvändigt att göra vissa ändringar i standard konfigurationen. Här är några exempel:

* Du planerar att använda Active [Directory-topologin för flera Azure](plan-connect-topologies.md#each-object-only-once-in-an-azure-ad-tenant). Sedan måste du använda ett filter för att styra vilka objekt som ska synkroniseras till en viss Azure AD-katalog.
* Du kör en pilot för Azure eller Microsoft 365 och du vill bara ha en delmängd av användarna i Azure AD. I den lilla piloten är det inte viktigt att ha en fullständig global adress lista för att demonstrera funktionerna.
* Du har många tjänst konton och andra oönskade konton som du inte vill ha i Azure AD.
* Av kompatibilitetsskäl tar du inte bort några användar konton lokalt. Du inaktiverar dem bara. Men i Azure AD vill du bara att aktiva konton ska vara tillgängliga.

Den här artikeln beskriver hur du konfigurerar olika filtrerings metoder.

> [!IMPORTANT]
> Microsoft stöder inte ändring eller hantering av Azure AD Connect-synkronisering utöver de åtgärder som är formellt dokumenterade. Någon av dessa åtgärder kan resultera i ett inkonsekvent eller tillstånd som inte stöds för Azure AD Connect Sync. Därför kan Microsoft inte tillhandahålla teknisk support för sådana distributioner.

## <a name="basics-and-important-notes"></a>Grundläggande och viktiga anteckningar
I Azure AD Connect Sync kan du aktivera filtrering när som helst. Om du börjar med en standard konfiguration av Active Directory-synkronisering och sedan konfigurerar filtrering, synkroniseras inte längre de objekt som filtreras ut till Azure AD. På grund av den här ändringen raderas alla objekt i Azure AD som tidigare har synkroniserats, men sedan filtreras de i Azure AD.

Innan du börjar göra ändringar i filtreringen bör du [inaktivera den schemalagda aktiviteten](#disable-the-scheduled-task) så att du inte oavsiktligt exporterar ändringar som du ännu inte har kontrollerat.

Eftersom filtrering kan ta bort flera objekt samtidigt, vill du se till att dina nya filter är korrekta innan du börjar exportera ändringar till Azure AD. När du har slutfört konfigurations stegen rekommenderar vi starkt att du följer [verifierings stegen](#apply-and-verify-changes) innan du exporterar och gör ändringar i Azure AD.

För att skydda dig från att ta bort många objekt av misstag är funktionen "[förhindra oavsiktliga borttagningar](how-to-connect-sync-feature-prevent-accidental-deletes.md)" aktive rad som standard. Om du tar bort flera objekt på grund av filtrering (500 som standard) måste du följa stegen i den här artikeln för att tillåta borttagningarna att gå till Azure AD.

Om du använder en version före november 2015 ([1.0.9125](reference-connect-version-history.md)), gör en ändring i en filter konfiguration och använder Lösenordssynkronisering, måste du utlösa en fullständig synkronisering av alla lösen ord när du har slutfört konfigurationen. Anvisningar om hur du utlöser en fullständig synkronisering av lösen ord finns i [utlösa en fullständig synkronisering av alla lösen ord](tshoot-connect-password-hash-synchronization.md#trigger-a-full-sync-of-all-passwords). Om du använder build 1.0.9125 eller senare, beräknar den vanliga **fullständiga synkroniseringsåtgärden** också om lösen orden ska synkroniseras och om det extra steget inte längre krävs.

Om **användar** objekt oavsiktligt har tagits bort i Azure AD på grund av ett filtrerings fel kan du återskapa användar objekt i Azure AD genom att ta bort filtrerings konfigurationerna. Sedan kan du synkronisera dina kataloger igen. Med den här åtgärden återställs användare från pappers korgen i Azure AD. Du kan dock inte ångra borttagning av andra objekt typer. Om du till exempel råkar ta bort en säkerhets grupp och den användes för att ACL: en resurs, kan inte gruppen och dess ACL: er återställas.

Azure AD Connect tar bara bort objekt som det har tänkt att vara i omfånget. Om det finns objekt i Azure AD som har skapats av en annan Sync-motor och dessa objekt inte är inom omfånget, tas de inte bort när du lägger till filtrering. Om du till exempel börjar med en DirSync-server som skapade en fullständig kopia av hela katalogen i Azure AD och du installerar en ny Azure AD Connect Sync-Server parallellt med filtrering aktive rad från början, tar Azure AD Connect inte bort de extra objekt som skapas av DirSync.

Filtrerings konfigurationen behålls när du installerar eller uppgraderar till en nyare version av Azure AD Connect. Vi rekommenderar alltid att du verifierar att konfigurationen inte oavsiktligt har ändrats efter en uppgradering till en nyare version innan den första synkroniseringen körs.

Om du har mer än en skog måste du använda de filtrerings konfigurationer som beskrivs i det här avsnittet för varje skog (förutsatt att du vill ha samma konfiguration för alla).

### <a name="disable-the-scheduled-task"></a>Inaktivera den schemalagda aktiviteten
Följ dessa steg om du vill inaktivera den inbyggda Schemaläggaren som utlöser en synkroniseringsprocess var 30: e minut:

1. Gå till en PowerShell-prompt.
2. Kör `Set-ADSyncScheduler -SyncCycleEnabled $False` för att inaktivera Scheduler.
3. Gör de ändringar som dokumenteras i den här artikeln.
4. Kör `Set-ADSyncScheduler -SyncCycleEnabled $True` för att aktivera Scheduler igen.

**Om du använder en Azure AD Connect version innan du 1.1.105.0**  
Följ dessa steg om du vill inaktivera den schemalagda aktiviteten som utlöser en synkroniseringsprocess var tredje timme:

1. Starta **Schemaläggaren** från **Start** -menyn.
2. Gå direkt under Schemaläggaren- **biblioteket**, leta upp aktiviteten med namnet **Azure AD Sync Scheduler**, högerklicka och välj **inaktivera**.  
   ![Schemaläggaren](./media/how-to-connect-sync-configure-filtering/taskscheduler.png)  
3. Nu kan du göra konfigurations ändringar och köra Synkroniseringsmotorn manuellt från **Synchronization Service Manager** -konsolen.

När du har slutfört alla filtrerings ändringar ska du inte glömma att komma tillbaka och **Aktivera** åtgärden igen.

## <a name="filtering-options"></a>Filtrerings alternativ
Du kan använda följande filtrerings konfigurations typer för Directory-synkroniseringsfunktionen:

* [**Gruppbaserad**](#group-based-filtering): filtrering som baseras på en enskild grupp kan bara konfigureras på den första installationen med hjälp av installations guiden.
* [**Domänbaserad**](#domain-based-filtering): med det här alternativet kan du välja vilka domäner som ska synkroniseras till Azure AD. Du kan också lägga till och ta bort domäner från Synkroniseringsmotorn när du gör ändringar i den lokala infrastrukturen när du har installerat Azure AD Connect Sync.
* [**Organisationsenhet (OU) – baserat**](#organizational-unitbased-filtering): genom att använda det här alternativet kan du välja vilka organisationsenheter som ska synkroniseras med Azure AD. Det här alternativet är för alla objekt typer i valda organisationsenheter.
* [**Attribute-based**](#attribute-based-filtering): genom att använda det här alternativet kan du filtrera objekt baserat på attributvärden i objekten. Du kan också ha olika filter för olika objekt typer.

Du kan använda flera filtrerings alternativ samtidigt. Du kan till exempel använda OU-baserad filtrering för att bara inkludera objekt i en ORGANISATIONSENHET. Samtidigt kan du använda attributbaserade filtrering för att filtrera objekten ytterligare. När du använder flera filtrerings metoder använder filtren en logisk "och" mellan filtren.

## <a name="domain-based-filtering"></a>Domän baserad filtrering
Det här avsnittet innehåller stegen för att konfigurera ditt domän filter. Om du har lagt till eller tagit bort domäner i din skog efter att du har installerat Azure AD Connect måste du också uppdatera filtrerings konfigurationen.

Det bästa sättet att ändra domänbaserad filtrering är genom att köra installations guiden och ändra domän- [och OU-filtrering](how-to-connect-install-custom.md#domain-and-ou-filtering). Installations guiden automatiserar alla uppgifter som dokumenteras i det här avsnittet.

Du bör bara följa dessa steg om du inte kan köra installations guiden av någon anledning.

Domänbaserad filtrerings konfiguration består av följande steg:

1. Välj de domäner som du vill inkludera i synkroniseringen.
2. Justera körnings profilerna för varje tillagd och borttagen domän.
3. [Tillämpa och verifiera ändringar](#apply-and-verify-changes).

### <a name="select-the-domains-to-be-synchronized"></a>Välj de domäner som ska synkroniseras
Det finns två sätt att välja de domäner som ska synkroniseras:
    - Använda synkroniseringstjänsten
    - Använda guiden Azure AD Connect.


#### <a name="select-the-domains-to-be-synchronized-using-the-synchronization-service"></a>Välj de domäner som ska synkroniseras med synkroniseringstjänsten
Gör så här om du vill ange ett domän filter:

1. Logga in på servern som kör Azure AD Connect Sync genom att använda ett konto som är medlem i säkerhets gruppen **ADSyncAdmins** .
2. Starta **synkroniseringstjänsten** från **Start** -menyn.
3. Välj **kopplingar** och i listan **anslutningar** väljer du anslutningen med typen **Active Directory Domain Services**. I **åtgärder** väljer du **Egenskaper**.  
   ![Kopplings egenskaper](./media/how-to-connect-sync-configure-filtering/connectorproperties.png)  
4. Klicka på **Konfigurera katalogpartitioner**.
5. I listan **Välj katalogpartitioner** väljer du och avmarkerar domäner efter behov. Kontrol lera att bara de partitioner som du vill synkronisera är markerade.  
   ![Skärm bild som visar katalogpartitioner i fönstret "egenskaper".](./media/how-to-connect-sync-configure-filtering/connectorpartitions.png)  
   Om du har ändrat din lokala Active Directory-infrastruktur och lagt till eller tagit bort domäner från skogen, klickar du på knappen **Uppdatera** för att hämta en uppdaterad lista. När du uppdaterar uppmanas du att ange autentiseringsuppgifter. Ange autentiseringsuppgifter med Läs behörighet till Windows Server Active Directory. Det behöver inte vara den användare som är förifylld i dialog rutan.  
   ![Uppdatering krävs](./media/how-to-connect-sync-configure-filtering/refreshneeded.png)  
6. När du är klar stänger du dialog rutan **Egenskaper** genom att klicka på **OK**. Om du har tagit bort domäner från skogen, visas ett popup-meddelande om att en domän har tagits bort och att konfigurationen kommer att rensas.
7. Fortsätt att justera körnings profilerna.

#### <a name="select-the-domains-to-be-synchronized-using-the-azure-ad-connect-wizard"></a>Välj de domäner som ska synkroniseras med hjälp av guiden Azure AD Connect
Gör så här om du vill ange ett domän filter:

1.  Starta guiden Azure AD Connect
2.  Klicka på **Konfigurera**.
3.  Välj **Anpassa alternativ för synkronisering** och klicka på **Nästa**.
4.  Ange dina autentiseringsuppgifter för Azure AD
5.  Klicka på **Nästa** på sidan **anslutna kataloger** .
6.  På **sidan domän-och OU-filtrering** klickar du på **Uppdatera**.  Nya domäner kommer nu att visas och borttagna domäner försvinner.
   ![Partitioner](./media/how-to-connect-sync-configure-filtering/update2.png)  

### <a name="update-the-run-profiles"></a>Uppdatera körnings profilerna
Om du har uppdaterat ditt domän filter måste du också uppdatera körnings profilerna.

1. I listan **kopplingar** ser du till att den koppling som du ändrade i föregående steg är markerad. I **åtgärder** väljer du **Konfigurera körnings profiler**.  
   ![Anslutnings körnings profiler 1](./media/how-to-connect-sync-configure-filtering/connectorrunprofiles1.png)  
2. Hitta och identifiera följande profiler:
    * Fullständig import
    * Fullständig synkronisering
    * Deltaimport
    * Deltasynkronisering
    * Exportera
3. Justera de **tillagda** och **borttagna** domänerna för varje profil.
    1. För var och en av de fem profilerna utför du följande steg för varje **tillagd** domän:
        1. Välj körnings profil och klicka på **nytt steg**.
        2. På sidan **Konfigurera steg** i list rutan **typ** väljer du stegtyp med samma namn som den profil som du konfigurerar. Klicka på **Nästa**.  
        ![Anslutnings körnings profiler 2](./media/how-to-connect-sync-configure-filtering/runprofilesnewstep1.png)  
        3. På sidan **anslutnings konfiguration** i list rutan **partition** väljer du namnet på den domän som du har lagt till i ditt domän filter.  
        ![Anslutnings körnings profiler 3](./media/how-to-connect-sync-configure-filtering/runprofilesnewstep2.png)  
        4. Stäng dialog rutan **Konfigurera körnings profil** genom att klicka på **Slutför**.
    2. Utför följande steg för varje **borttagen** domän för var och en av de fem profilerna:
        1. Välj körnings profilen.
        2. Om **värdet** för attributet **partition** är ett GUID väljer du steget Kör och klickar på **ta bort steg**.  
        ![Anslutnings körnings profiler 4](./media/how-to-connect-sync-configure-filtering/runprofilesdeletestep.png)  
    3. Verifiera din ändring. Varje domän som du vill synkronisera ska visas som ett steg i varje körnings profil.
4. Stäng dialog rutan **Konfigurera körnings profiler** genom att klicka på **OK**.
5.  För att slutföra konfigurationen måste du köra en **fullständig import** och en delta- **synkronisering**. Fortsätt läsa avsnittet [tillämpa och verifiera ändringar](#apply-and-verify-changes).

## <a name="organizational-unitbased-filtering"></a>Organisatorisk enhet – baserad filtrering
Det bästa sättet att ändra OU-baserad filtrering är genom att köra installations guiden och ändra [domän-och OU-filtrering](how-to-connect-install-custom.md#domain-and-ou-filtering). Installations guiden automatiserar alla uppgifter som dokumenteras i det här avsnittet.

Du bör bara följa dessa steg om du inte kan köra installations guiden av någon anledning.

Gör så här om du vill konfigurera organisationsenhets-baserad filtrering:

1. Logga in på servern som kör Azure AD Connect Sync genom att använda ett konto som är medlem i säkerhets gruppen **ADSyncAdmins** .
2. Starta **synkroniseringstjänsten** från **Start** -menyn.
3. Välj **kopplingar** och i listan **anslutningar** väljer du anslutningen med typen **Active Directory Domain Services**. I **åtgärder** väljer du **Egenskaper**.  
   ![Kopplings egenskaper](./media/how-to-connect-sync-configure-filtering/connectorproperties.png)  
4. Klicka på **Konfigurera katalogpartitioner**, Välj den domän som du vill konfigurera och klicka sedan på **behållare**.
5. När du uppmanas till det anger du eventuella autentiseringsuppgifter med Läs behörighet till din lokala Active Directory. Det behöver inte vara den användare som är förifylld i dialog rutan.
6. I dialog rutan **Välj behållare** avmarkerar du de organisationsenheter som du inte vill synkronisera med moln katalogen och klickar sedan på **OK**.  
   ![Organisationsenheter i dialog rutan Välj behållare](./media/how-to-connect-sync-configure-filtering/ou.png)  
   * Behållaren **datorer** måste väljas för att dina Windows 10-datorer ska kunna synkroniseras till Azure AD. Om dina domänanslutna datorer finns i andra organisationsenheter, se till att de är markerade.
   * Du ska markera containern **ForeignSecurityPrincipals** om du har flera skogar med förtroenden. Med den här containern kan säkerhetsgruppmedlemskap i flera skogar lösas.
   * **RegisteredDevices** ou måste väljas om du har aktiverat funktionen tillbakaskrivning av enhet. Om du använder en annan tillbakaskrivning-funktion, t. ex. tillbakaskrivning av grupp, kontrollerar du att platserna är markerade.
   * Välj en annan ORGANISATIONSENHET där användare, iNetOrgPerson, grupper, kontakter och datorer finns. I bilden finns alla dessa organisationsenheter i ManagedObjects OU.
   * Om du använder gruppbaserad filtrering måste ORGANISATIONSENHETen där gruppen finns finns.
   * Observera att du kan ange om nya organisationsenheter som läggs till när filtrerings konfigurationen slutförs synkroniseras eller inte synkroniseras. Mer information finns i nästa avsnitt.
7. När du är klar stänger du dialog rutan **Egenskaper** genom att klicka på **OK**.
8. För att slutföra konfigurationen måste du köra en **fullständig import** och en delta- **synkronisering**. Fortsätt läsa avsnittet [tillämpa och verifiera ändringar](#apply-and-verify-changes).

### <a name="synchronize-new-ous"></a>Synkronisera nya organisationsenheter
Nya organisationsenheter som skapas när filtrering har kon figurer ATS synkroniseras som standard. Det här läget anges av en markerad kryss ruta. Du kan också avmarkera vissa underordnade organisationsenheter. För att få det här problemet klickar du på rutan tills den blir vit med en blå kryss markering (dess standard tillstånd). Avmarkera sedan alla underordnade organisationsenheter som du inte vill synkronisera.

Om alla underordnade organisationsenheter är synkroniserade är rutan vit med en blå bock.  
![OU med alla rutor markerade](./media/how-to-connect-sync-configure-filtering/ousyncnewall.png)

Om vissa underordnade organisationsenheter har avmarkerats är rutan grå med en vit bock.  
![OU med vissa underordnade organisationsenheter har avmarkerats](./media/how-to-connect-sync-configure-filtering/ousyncnew.png)

Med den här konfigurationen synkroniseras en ny ORGANISATIONSENHET som skapades under ManagedObjects.

Installations guiden för Azure AD Connect skapar alltid den här konfigurationen.

### <a name="dont-synchronize-new-ous"></a>Synkronisera inte nya organisationsenheter
Du kan konfigurera Synkroniseringsmotorn så att den inte synkroniserar nya organisationsenheter när filtrerings konfigurationen är färdig. Det här läget anges i användar gränssnittet genom att rutan visas med solid grått utan kryss markering. För att få det här problemet klickar du på rutan tills den blir vit utan bock. Välj sedan de underordnade organisationsenheter som du vill synkronisera.

![OU med roten omarkerad](./media/how-to-connect-sync-configure-filtering/oudonotsyncnew.png)

Med den här konfigurationen synkroniseras inte en ny ORGANISATIONSENHET som skapades under ManagedObjects.

## <a name="attribute-based-filtering"></a>Attribut-baserad filtrering
Se till att du använder 2015 november ([1.0.9125](reference-connect-version-history.md)) eller senare för att kunna arbeta med de här stegen.

> [!IMPORTANT]
>Microsoft rekommenderar att inte ändra standard reglerna som skapats av **Azure AD Connect**. Om du vill ändra regeln, klona den och inaktivera den ursprungliga regeln. Gör ändringar i den klonade regeln. Observera att genom att göra så (inaktivera den ursprungliga regeln) kommer du att sakna eventuella fel korrigeringar eller funktioner som aktive ras via den regeln.

Attribut-baserad filtrering är det mest flexibla sättet att filtrera objekt. Du kan använda kraften i [deklarativ etablering](concept-azure-ad-connect-sync-declarative-provisioning.md) för att kontrol lera nästan varje aspekt av när ett objekt synkroniseras med Azure AD.

Du kan använda [inkommande](#inbound-filtering) filtrering från Active Directory till metaversum och [utgående](#outbound-filtering) filtrering från metaversum till Azure AD. Vi rekommenderar att du tillämpar inkommande filtrering eftersom det är det enklaste sättet att underhålla. Du bör endast använda utgående filtrering om det krävs för att ansluta objekt från fler än en skog innan utvärderingen kan utföras.

### <a name="inbound-filtering"></a>Inkommande filtrering
Inkommande filtrering använder standard konfigurationen, där objekt som ska till Azure AD måste ha attributet metaversum cloudFiltered inte inställda på ett värde för att synkroniseras. Om det här attributets värde är inställt på **Sant** synkroniseras inte objektet. Det får inte vara inställt på **falskt**, avsiktligt. För att se till att andra regler har möjlighet att bidra till ett värde, ska det här attributet bara ha värdena **True** eller **Null** (saknas).

I inkommande filtrering använder du **omfattnings** kraften för att avgöra vilka objekt som ska synkroniseras eller inte. Det är här du gör justeringar för att anpassa din organisations krav. Omfångs modulen har en **grupp** och en **sats** för att fastställa när en Synkroniseringsregel är i omfånget. En grupp innehåller en eller flera satser. Det finns ett logiskt "och" mellan flera satser och ett logiskt "eller" mellan flera grupper.

Låt oss titta på ett exempel:  
![En skärm bild som visar ett exempel på att lägga till definitions områdes filter.](./media/how-to-connect-sync-configure-filtering/scope.png)  
Detta bör läsas som **(avdelning = IT) eller (avdelning = Sales och c = US)**.

I följande exempel och steg använder du användarobjektet som exempel, men du kan använda detta för alla objekt typer.

I följande exempel börjar prioritet svärdet med 50. Detta kan vara vilken siffra som helst, utan bör vara lägre än 100.

#### <a name="negative-filtering-do-not-sync-these"></a>Negativ filtrering: "Synkronisera inte dessa"
I följande exempel filtrerar du bort (inte synkroniserar) alla användare där **extensionAttribute15** har värdet **nosync**.

1. Logga in på servern som kör Azure AD Connect Sync genom att använda ett konto som är medlem i säkerhets gruppen **ADSyncAdmins** .
2. Starta **Redigeraren för regler för synkronisering** från **Start** -menyn.
3. Kontrol lera att **inkommande** är markerat och klicka på **Lägg till ny regel**.
4. Ge regeln ett beskrivande namn, till exempel "*i från AD – User DoNotSyncFilter*". Välj rätt skog, Välj **användare** som **objekt typen CS** och välj **person** som **typ av objekt typen MV**. I **Länktyp** väljer du **Anslut**. I **prioritet** anger du ett värde som för närvarande inte används av en annan Synkroniseringsregel (till exempel 50) och klickar sedan på **Nästa**.  
   ![Ingående 1-Beskrivning](./media/how-to-connect-sync-configure-filtering/inbound1.png)  
5. I **omfångs filter**, klickar du på **Lägg till grupp** och klickar sedan på **Lägg till sats**. I **attribut** väljer du **ExtensionAttribute15**. Kontrol lera att **operatorn** är inställd på **lika** och skriv värdet **nosync** i rutan **värde** . Klicka på **Nästa**.  
   ![Inkommande 2-omfattning](./media/how-to-connect-sync-configure-filtering/inbound2.png)  
6. Lämna **kopplings** reglerna tomma och klicka sedan på **Nästa**.
7. Klicka på **Lägg till omvandling**, **Välj FlowType** som **konstant** och välj **cloudFiltered** som **target-attribut**. Skriv **Sant** i text rutan **källa** . Klicka på **Lägg till** för att spara regeln.  
   ![Inkommande 3-transformering](./media/how-to-connect-sync-configure-filtering/inbound3.png)
8. För att slutföra konfigurationen måste du köra en **fullständig synkronisering**. Fortsätt läsa avsnittet [tillämpa och verifiera ändringar](#apply-and-verify-changes).

#### <a name="positive-filtering-only-sync-these"></a>Positiv filtrering: "synkronisera bara dessa"
Att uttrycka positiv filtrering kan vara mer utmanande eftersom du också måste ta hänsyn till objekt som inte är uppenbara för att synkroniseras, till exempel konferens rum. Du kommer också att åsidosätta standard filtret i regeln som är inaktuell **i från AD-User Join**. När du skapar ett anpassat filter ska du se till att inte inkludera kritiska system objekt, objekt för replikeringskonflikter, särskilda post lådor och tjänst kontona för Azure AD Connect.

Alternativet för positiv filtrering kräver två regler för synkronisering. Du behöver en regel (eller flera) med rätt omfång för objekt som ska synkroniseras. Du behöver också en andra Synkroniseringsregel som filtrerar bort alla objekt som ännu inte har identifierats som ett objekt som ska synkroniseras.

I följande exempel synkroniserar du bara användar objekt där avdelnings attributet har värdet **Sales**.

1. Logga in på servern som kör Azure AD Connect Sync genom att använda ett konto som är medlem i säkerhets gruppen **ADSyncAdmins** .
2. Starta **Redigeraren för regler för synkronisering** från **Start** -menyn.
3. Kontrol lera att **inkommande** är markerat och klicka på **Lägg till ny regel**.
4. Ge regeln ett beskrivande namn, till exempel "*i från AD – användare Sales Sync*". Välj rätt skog, Välj **användare** som **objekt typen CS** och välj **person** som **typ av objekt typen MV**. I **Länktyp** väljer du **Anslut**. I **prioritet** anger du ett värde som för närvarande inte används av en annan Synkroniseringsregel (till exempel 51) och klickar sedan på **Nästa**.  
   ![Ingående 4-Beskrivning](./media/how-to-connect-sync-configure-filtering/inbound4.png)  
5. I **omfångs filter**, klickar du på **Lägg till grupp** och klickar sedan på **Lägg till sats**. I **attribut** väljer du **avdelning**. Kontrol lera att operatorn är inställd på **lika** och skriv värdet **Sales** i rutan **värde** . Klicka på **Nästa**.  
   ![Inkommande 5-omfattning](./media/how-to-connect-sync-configure-filtering/inbound5.png)  
6. Lämna **kopplings** reglerna tomma och klicka sedan på **Nästa**.
7. Klicka på **Lägg till omvandling**, Välj **konstant** som **FlowType** och välj **cloudFiltered** som Target- **attribut**. Skriv **falskt** i rutan **källa** . Klicka på **Lägg till** för att spara regeln.  
   ![Inkommande 6-transformering](./media/how-to-connect-sync-configure-filtering/inbound6.png)  
   Detta är ett specialfall där du uttryckligen anger cloudFiltered till **false**.
8. Nu måste vi skapa en regel för catch-all-synkronisering. Ge regeln ett beskrivande namn, till exempel "*i från AD – User catch-all filter*". Välj rätt skog, Välj **användare** som **objekt typen CS** och välj **person** som **typ av objekt typen MV**. I **Länktyp** väljer du **Anslut**. I **prioritet** anger du ett värde som för närvarande inte används av en annan Synkroniseringsregel (till exempel 99). Du har valt ett prioritets värde som är högre (lägre prioritet) än den tidigare synkroniseringsregeln. Men du har också lämnat lite plats så att du kan lägga till fler filtrerings regler för synkronisering senare när du vill börja synkronisera ytterligare avdelningar. Klicka på **Nästa**.  
   ![Ingående 7-Beskrivning](./media/how-to-connect-sync-configure-filtering/inbound7.png)  
9. Lämna **omfångs filter** tomt och klicka på **Nästa**. Ett tomt filter anger att regeln ska tillämpas på alla objekt.
10. Lämna **kopplings** reglerna tomma och klicka sedan på **Nästa**.
11. Klicka på **Lägg till omvandling**, Välj **konstant** som **FlowType** och välj **cloudFiltered** som **target-attribut**. Skriv **Sant** i rutan **källa** . Klicka på **Lägg till** för att spara regeln.  
    ![Inkommande 3-transformering](./media/how-to-connect-sync-configure-filtering/inbound3.png)  
12. För att slutföra konfigurationen måste du köra en **fullständig synkronisering**. Fortsätt läsa avsnittet [tillämpa och verifiera ändringar](#apply-and-verify-changes).

Om du behöver kan du skapa fler regler av den första typen där du inkluderar fler objekt i synkroniseringen.

### <a name="outbound-filtering"></a>Utgående filtrering
I vissa fall är det nödvändigt att bara filtrera efter att objekten har anslutits till metaversum. Det kan till exempel vara nödvändigt att titta på e-postattributet från resurs skogen och attributet userPrincipalName från konto skogen för att avgöra om ett objekt ska synkroniseras. I dessa fall skapar du filtreringen för regeln för utgående trafik.

I det här exemplet ska du ändra filtreringen så att endast användare som har både e-post och userPrincipalName som slutar med @contoso.com synkroniseras:

1. Logga in på servern som kör Azure AD Connect Sync genom att använda ett konto som är medlem i säkerhets gruppen **ADSyncAdmins** .
2. Starta **Redigeraren för regler för synkronisering** från **Start** -menyn.
3. Klicka på **utgående** under **typ av regel**.
4. Beroende på vilken version av Connect du använder kan du antingen hitta regeln med namnet **till Azure AD – användare anslutning** eller **ut till Azure AD-User Join-SOAInAD** och klicka på **Redigera**.
5. I popup-fönstret svarar du **Ja** för att skapa en kopia av regeln.
6. På sidan **Beskrivning** ändrar du **prioriteten** till ett oanvänt värde, till exempel 50.
7. Klicka på **omfångs filter** i det vänstra navigerings fältet och klicka sedan på **Lägg till sats**. I **attribut** väljer du **e-post**. I **operator** väljer du **ENDSWITH**. I **värde** skriver du **\@ contoso.com** och klickar sedan på **Lägg till sats**. I **attribut** väljer du **userPrincipalName**. I **operator** väljer du **ENDSWITH**. I **värde** skriver du **\@ contoso.com**.
8. Klicka på **Spara**.
9. För att slutföra konfigurationen måste du köra en **fullständig synkronisering**. Fortsätt läsa avsnittet [tillämpa och verifiera ändringar](#apply-and-verify-changes).

## <a name="apply-and-verify-changes"></a>Tillämpa och verifiera ändringar
När du har gjort dina konfigurations ändringar måste du tillämpa dem på de objekt som redan finns i systemet. Det kan också vara att de objekt som inte finns i Synkroniseringsmotorn ska bearbetas (och Synkroniseringsmotorn måste läsa käll systemet igen för att verifiera innehållet).

Om du har ändrat konfigurationen med hjälp  av domän **-eller organisations enhets** filtrering, måste du göra en **fullständig import**, följt av **delta-synkronisering**.

Om du har ändrat konfigurationen med hjälp av **attribut** filtrering måste du göra en **fullständig synkronisering**.

Gör så här:

1. Starta **synkroniseringstjänsten** från **Start** -menyn.
2. Välj **kopplingar**. I listan **kopplingar** väljer du den koppling där du gjorde en konfigurations ändring tidigare. I **åtgärder** väljer du **Kör**.  
   ![Kör koppling](./media/how-to-connect-sync-configure-filtering/connectorrun.png)  
3. I **Kör profiler** väljer du den åtgärd som nämndes i föregående avsnitt. Om du behöver köra två åtgärder kör du den andra när den första har avslut ATS. (Kolumnen **State** är **inaktiv** för den valda kopplingen.)

Efter synkroniseringen mellanlagras alla ändringar för att exporteras. Innan du gör ändringarna i Azure AD vill du kontrol lera att alla dessa ändringar är korrekta.

1. Starta en kommando tolk och gå till `%ProgramFiles%\Microsoft Azure AD Sync\bin` .
2. Kör `csexport "Name of Connector" %temp%\export.xml /f:x`.  
   Namnet på anslutnings tjänsten finns i synkroniseringstjänsten. Det har ett namn som liknar "contoso.com – Azure AD" för Azure AD.
3. Kör `CSExportAnalyzer %temp%\export.xml > %temp%\export.csv`.
4. Nu har du en fil i% temp% med namnet export.csv som kan undersökas i Microsoft Excel. Den här filen innehåller alla ändringar som ska exporteras.
5. Gör nödvändiga ändringar i data eller konfiguration och kör de här stegen igen (importera, synkronisera och verifiera) tills de ändringar som ska exporteras är det du förväntar dig.

När du är nöjd exporterar du ändringarna till Azure AD.

1. Välj **kopplingar**. I listan **anslutningar** väljer du Azure AD-anslutaren. I **åtgärder** väljer du **Kör**.
2. I **Kör profiler** väljer du **Exportera**.
3. Om konfigurationen ändras ta bort många objekt visas ett fel i exporten när antalet är större än det konfigurerade tröskelvärdet (som standard 500). Om du ser det här felet måste du tillfälligt inaktivera funktionen "[förhindra oavsiktliga borttagningar](how-to-connect-sync-feature-prevent-accidental-deletes.md)".

Nu är det dags att aktivera Scheduler igen.

1. Starta **Schemaläggaren** från **Start** -menyn.
2. Gå direkt under Schemaläggaren- **biblioteket**, leta upp aktiviteten med namnet **Azure AD Sync Scheduler**, högerklicka och välj **Aktivera**.

## <a name="group-based-filtering"></a>Gruppbaserad filtrering
Du kan konfigurera gruppbaserad filtrering första gången du installerar Azure AD Connect med [anpassad installation](how-to-connect-install-custom.md#sync-filtering-based-on-groups). Den är avsedd för en pilot distribution där du vill att endast en liten uppsättning objekt ska synkroniseras. När du inaktiverar gruppbaserad filtrering kan du inte aktivera den igen. Det finns *inte stöd* för att använda gruppbaserad filtrering i en anpassad konfiguration. Det finns bara stöd för att konfigurera den här funktionen med hjälp av installations guiden. När du har slutfört piloten kan du använda något av de andra filtrerings alternativen i det här avsnittet. När du använder OU-baserad filtrering tillsammans med gruppbaserad filtrering måste de ORGANISATIONSENHETer där gruppen och dess medlemmar finns inkluderas.

När du synkroniserar flera AD-skogar kan du konfigurera gruppbaserad filtrering genom att ange en annan grupp för varje AD-anslutning. Om du vill synkronisera en användare i en AD-skog och samma användare har ett eller flera motsvarande objekt i andra AD-skogar, måste du se till att användarobjektet och alla dess motsvarande objekt är i gruppbaserad filtrerings omfattning. Exempel:

* Du har en användare i en skog som har motsvarande FSP-objekt (främmande säkerhets objekt) i en annan skog. Båda objekten måste vara inom gruppbaserad filtrerings omfattning. Annars kommer användaren inte att synkroniseras med Azure AD.

* Du har en användare i en skog som har ett motsvarande resurs konto (t. ex. länkad post låda) i en annan skog. Vidare har du konfigurerat Azure AD Connect att länka användaren till resurs kontot. Båda objekten måste vara inom gruppbaserad filtrerings omfattning. Annars kommer användaren inte att synkroniseras med Azure AD.

* Du har en användare i en skog som har en motsvarande e-postkontakt i en annan skog. Vidare har du konfigurerat Azure AD Connect att länka användaren till e-postkontakten. Båda objekten måste vara inom gruppbaserad filtrerings omfattning. Annars kommer användaren inte att synkroniseras med Azure AD.


## <a name="next-steps"></a>Nästa steg
- Läs mer om [Azure AD Connect Sync](how-to-connect-sync-whatis.md) -konfiguration.
- Läs mer om hur [du integrerar dina lokala identiteter med Azure AD](whatis-hybrid-identity.md).
