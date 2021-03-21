---
title: Felsöka ett objekt som inte synkroniseras med Azure Active Directory | Microsoft Docs
description: Felsök ett objekt som inte synkroniseras med Azure Active Directory.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 08/10/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1a0c8a42edad08308095469039c048f8dd8552af
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94413470"
---
# <a name="troubleshoot-an-object-that-is-not-synchronizing-with-azure-active-directory"></a>Felsöka ett objekt som inte synkroniseras med Azure Active Directory

Om ett objekt inte synkroniseras som förväntat med Microsoft Azure Active Directory (Azure AD) kan det bero på flera olika orsaker. Om du har fått ett fel meddelande från Azure AD eller om du ser felet i Azure AD Connect Health kan du läsa fel [söknings fel under synkroniseringen](tshoot-connect-sync-errors.md) i stället. Men om du felsöker ett problem där objektet inte finns i Azure AD är den här artikeln för dig. Den beskriver hur du hittar fel i den lokala komponenten Azure AD Connect-synkronisering.

>[!IMPORTANT]
>För Azure AD Connect-distribution med version 1.1.749.0 eller högre, använder du [fel söknings aktiviteten](tshoot-connect-objectsync.md) i guiden för att felsöka problem med att synkronisera objekt. 

## <a name="synchronization-process"></a>Synkroniseringsprocess

Innan vi undersöker synkroniseringsproblem kan vi förstå Azure AD Connect syncing-processen:

  ![Diagram över Azure AD Connect Sync-processen](./media/tshoot-connect-object-not-syncing/syncingprocess.png)

### <a name="terminology"></a>**Terminologi**

* **CS:** Kopplings utrymme, en tabell i en databas
* **MV:** Metaversum, en tabell i en databas

### <a name="synchronization-steps"></a>**Synkroniserings steg**
Synkroniseringsprocessen omfattar följande steg:

1. **Importera från AD:** Active Directory objekt hämtas till Active Directory CS.

2. **Importera från Azure AD:** Azure AD-objekt hämtas till Azure AD CS.

3. **Synkronisering:** Regler för inkommande synkronisering och regler för utgående synkronisering körs i prioritetsordning med nummer, från lägre till högre. Om du vill visa reglerna för synkronisering går du till redigeraren för Synkroniseringsregel från Skriv bords programmen. Reglerna för inkommande synkronisering hämtar data från CS till MV. Reglerna för utgående synkronisering flyttar data från MV till CS.

4. **Exportera till AD:** Efter synkroniseringen exporteras objekt från Active Directory CS till Active Directory.

5. **Exportera till Azure AD:** Efter synkroniseringen exporteras objekt från Azure AD CS till Azure AD.

## <a name="troubleshooting"></a>Felsökning

Du hittar felen genom att titta på några olika platser i följande ordning:

1. [Åtgärden loggar](#operations) för att hitta fel som identifieras av Synkroniseringsmotorn under import och synkronisering.
2. [Kopplings utrymmet](#connector-space-object-properties) för att hitta saknade objekt och synkroniseringsfel.
3. [Metaversum](#metaverse-object-properties) för att hitta data relaterade problem.

Starta [Synchronization Service Manager](how-to-connect-sync-service-manager-ui.md) innan du börjar med de här stegen.

## <a name="operations"></a>Operations
På fliken **åtgärder** i Synchronization Service Manager kan du starta fel sökningen. Den här fliken visar resultatet från de senaste åtgärderna. 

![Skärm bild av fliken Synchronization Service Manager, som visar fliken åtgärder valt](./media/tshoot-connect-object-not-syncing/operations.png)  

Den övre halvan av fliken **åtgärder** visar alla körningar i kronologisk ordning. Som standard behålls information om de senaste sju dagarna i drifts loggen, men den här inställningen kan ändras med [Scheduler](how-to-connect-sync-feature-scheduler.md). Sök efter alla körningar som inte visar statusen **lyckades** . Du kan ändra sorteringen genom att klicka på rubrikerna.

Kolumnen **status** innehåller den viktigaste informationen och visar det allvarligaste problemet för en körning. Här är en snabb sammanfattning av de vanligaste statuserna i prioritetsordning för undersökningen (där * anger flera möjliga fel strängar).

| Status | Kommentar |
| --- | --- |
| stoppad-* |Det gick inte att slutföra körningen. Detta kan inträffa, till exempel om fjärrdatorn inte är tillgänglig och inte kan kontaktas. |
| stoppat-fel-gräns |Det finns fler än 5 000 fel. Körningen stoppades automatiskt på grund av det stora antalet fel. |
| slutförd- \* -fel |Körningen är färdig, men det finns fel (mindre än 5 000) som bör undersökas. |
| slutförd- \* -varningar |Körningen är färdig, men vissa data är inte i förväntat tillstånd. Om du har fel är det här meddelandet vanligt vis bara ett problem. Undersök inte varningar förrän du har åtgärdat fel. |
| lyckades |Inga problem. |

När du väljer en rad uppdateras slutet av fliken **åtgärder** för att visa information om den här körningen. Längst till vänster i det här fältet kan du ha en lista med namnet **steg #**. Den här listan visas bara om du har flera domäner i din skog och varje domän representeras av ett steg. Du kan hitta domän namnet under Heading- **partitionen**. Under rubriken **synkroniserings statistik** hittar du mer information om antalet ändringar som har bearbetats. Välj Länkar för att hämta en lista över de ändrade objekten. Om du har objekt med fel visas dessa fel under rubriken **synkroniseringsfel** .

### <a name="errors-on-the-operations-tab"></a>Fel på fliken åtgärder
När du har fel visas Synchronization Service Manager både objektet i fel och felet som länkar som innehåller mer information.

![Skärm bild av fel i Synchronization Service Manager](./media/tshoot-connect-object-not-syncing/errorsync.png)  
Börja med att välja fel strängen. (I föregående bild är fel strängen **Synkronisera-regel-fel-Function-triggerd**.) Du visas först en översikt över objektet. Om du vill se det faktiska felet väljer du **stack spårning**. Den här spårningen innehåller information om felsöknings nivå för felet.

Högerklicka på informations rutan **anrops stack** , klicka på **Markera alla** och välj sedan **Kopiera**. Kopiera sedan stacken och titta på felet i din favorit redigerare, till exempel Anteckningar.

Om felet är från **SyncRulesEngine**, visar anrops stacken information först alla attribut på objektet. Rulla nedåt tills du ser rubriken **InnerException =>**.  

  ![Skärm bild av Synchronization Service Manager som visar fel information under rubriken InnerException =>](./media/tshoot-connect-object-not-syncing/errorinnerexception.png)
  
Raden efter rubriken visar felet. I föregående bild är felet från en anpassad Synkroniseringsregel som skapats av Fabrikam.

Om felet inte ger tillräckligt med information, är det dags att titta på själva informationen. Välj länken med objekt identifieraren och fortsätt felsöka det [importerade objektet för anslutnings utrymmet](#cs-import).

## <a name="connector-space-object-properties"></a>Objektegenskaper för anslutarplats
Om fliken [**åtgärder**](#operations) visar inga fel följer du kopplings rummets objekt från Active Directory till metaversum till Azure AD. I den här sökvägen bör du ta reda på var problemet finns.

### <a name="searching-for-an-object-in-the-cs"></a>Söker efter ett objekt i CS

I Synchronization Service Manager väljer du **kopplingar**, väljer Active Directorys anslutningen och väljer **Sök kopplings utrymme**.

I rutan **omfattning** väljer du **RDN** när du vill söka efter attributet CN eller väljer **DN eller ankare** när du vill söka efter **distinguishedName** -attributet. Ange ett värde och välj **Sök**. 
 
![Skärm bild av en kopplings utrymmes sökning](./media/tshoot-connect-object-not-syncing/cssearch.png)  

Om du inte hittar det objekt som du söker efter, kan det ha filtrerats med [domänbaserade filtrering](how-to-connect-sync-configure-filtering.md#domain-based-filtering) eller [OU-baserad filtrering](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering). Verifiera att filtreringen är konfigurerad som förväntat genom att läsa [Azure AD Connect synkronisering: Konfigurera filtrering](how-to-connect-sync-configure-filtering.md).

Du kan utföra en annan användbar sökning genom att välja Azure AD-anslutaren. I rutan **omfattning** väljer du **väntande import** och markerar sedan kryss rutan **Lägg till** . Den här sökningen ger dig alla synkroniserade objekt i Azure AD som inte kan associeras med ett lokalt objekt.  

![Skärm bild av överblivna i en kopplings utrymmes sökning](./media/tshoot-connect-object-not-syncing/cssearchorphan.png) 
 
De objekten har skapats av en annan synkroniserings motor eller en synkroniseringstjänst med en annan filtrerings konfiguration. Dessa överblivna objekt hanteras inte längre. Granska den här listan och ta bort dessa objekt med hjälp av [Azure AD PowerShell](/previous-versions/azure/jj151815(v=azure.100)) -cmdlets.

### <a name="cs-import"></a>CS-import
När du öppnar ett CS-objekt finns det flera flikar överst. Fliken **Importera** visar de data som mellanlagras efter en import.  

![Skärm bild av objektet kopplings utrymme Fönstret Egenskaper med fliken Importera markerad](./media/tshoot-connect-object-not-syncing/csobject.png)    

Kolumnen **gammalt värde** visar vad som för närvarande lagras i Connect, och kolumnen **nytt värde** visar vad som har tagits emot från käll systemet och ännu inte har använts. Om det finns ett fel på objektet bearbetas inte ändringarna.

Fliken **synkroniseringsfel** visas i fönstret **objekt egenskaper för anslutnings utrymme** endast om det är problem med objektet. Mer information finns i så här [felsöker du synkroniseringsfel på fliken **åtgärder**](#errors-on-the-operations-tab).

![Skärm bild av fliken synkroniseringsfel i objektet kopplings utrymme Fönstret Egenskaper](./media/tshoot-connect-object-not-syncing/cssyncerror.png)  

### <a name="cs-lineage"></a>CS-härkomst
Fliken **härkomst** i fönstret **objekt egenskaper för anslutnings utrymme** visar hur kopplings områdets objekt är relaterat till metaversum-objektet. Du kan se när Connector senast importerade en ändring från det anslutna systemet och vilka regler som tillämpades för att fylla i data i metaversum.  

![Skärm bild som visar härkomst-fliken i objektet kopplings utrymme Fönstret Egenskaper](./media/tshoot-connect-object-not-syncing/cslineage.png)  

I föregående bild visar kolumnen **åtgärd** en regel för inkommande synkronisering med åtgärden **etablera**. Det innebär att så länge det här kopplings utrymmes objektet finns kvar, är metaversum-objektet kvar. Om listan över regler för synkronisering i stället visar en regel för utgående synkronisering med en **etablerings** åtgärd, tas objektet bort när metaversum-objektet tas bort.  

![Skärm bild av ett härkomst-fönster på fliken härkomst i objektet kopplings utrymme Fönstret Egenskaper](./media/tshoot-connect-object-not-syncing/cslineageout.png)  

I föregående bild kan du också se i kolumnen **PasswordSync** att det inkommande anslutnings utrymmet kan bidra till att ändra lösen ordet eftersom en Synkroniseringsregel har värdet **Sant**. Det här lösen ordet skickas till Azure AD via regeln för utgående trafik.

På fliken **härkomst** kan du gå till metaversum genom att välja [**metaversum objekt egenskaper**](#mv-attributes).

### <a name="preview"></a>Förhandsgranskning
I det nedre vänstra hörnet av objekt fönstret **Egenskaper för kopplings utrymme** är knappen **Förhandsgranska** . Välj den här knappen för att öppna **förhands gransknings** sidan där du kan synkronisera ett enskilt objekt. Den här sidan är användbar om du felsöker vissa anpassade regler för synkronisering och vill se resultatet av en ändring på ett enskilt objekt. Du kan välja en **fullständig synkronisering** eller en **delta-synkronisering**. Du kan också välja **generera förhands granskning**, som endast behåller ändringen i minnet. Eller Välj **Förhandsgranskning av incheckning**, som uppdaterar metaversum och steg alla ändringar i mål kopplings utrymmen.  

![Skärm bild av sidan för förhands granskning med starta förhands granskning valt](./media/tshoot-connect-object-not-syncing/preview.png)  

I förhands granskningen kan du kontrol lera objektet och se vilken regel som tillämpas för ett visst attribut flöde.  

![Skärm bild av sidan för förhands granskning som visar flödet importera attribut](./media/tshoot-connect-object-not-syncing/previewresult.png)

### <a name="log"></a>Loggas
Klicka på knappen **Logga** bredvid knappen **Förhandsgranska** för att öppna **logg** sidan. Här kan du se status och historik för Lösenordssynkronisering. Mer information finns i [Felsöka hash-synkronisering av lösen ord med Azure AD Connect Sync](tshoot-connect-password-hash-synchronization.md).

## <a name="metaverse-object-properties"></a>Egenskaper för metaversum-objekt
Det är vanligt vis bättre att börja söka från käll Active Directory kopplings utrymme. Men du kan också börja söka från metaversum.

### <a name="searching-for-an-object-in-the-mv"></a>Söker efter ett objekt i MV
I Synchronization Service Manager väljer du **metaversum search**, som i följande bild. Skapa en fråga som du vet hittar användaren. Sök efter vanliga attribut, till exempel **accountName** (**sAMAccountName**) och **userPrincipalName**. Mer information finns i avsnittet om [synkronisering Service Manager metaversum search](how-to-connect-sync-service-manager-ui-mvsearch.md).

![Skärm bild av Synchronization Service Manager med fliken Sök metaversum vald](./media/tshoot-connect-object-not-syncing/mvsearch.png)  

I fönstret **Sök Resultat** klickar du på objektet.

Om du inte har hittat objektet har det inte nått metaversum ännu. Fortsätt att söka efter objektet i Active Directory [kopplings utrymme](#connector-space-object-properties). Om du hittar objektet i Active Directory anslutnings utrymmet kan det finnas ett synkroniseringsfel som blockerar objektet från att komma till metaversum, eller så kan ett omfångs filter för synkroniseringsregeln tillämpas.

### <a name="object-not-found-in-the-mv"></a>Objektet hittades inte i MV
Om objektet finns i Active Directory CS men inte finns i MV används ett omfångs filter. Om du vill titta på omfångs filtret går du till menyn Skriv bords program och väljer **Redigeraren för regler för synkronisering**. Filtrera reglerna som är tillämpliga för objektet genom att justera filtret nedan.

  ![Skärm bild av redigeraren för regler för synkronisering som visar en sökning efter regler för inkommande synkronisering](./media/tshoot-connect-object-not-syncing/syncrulessearch.png)

Visa varje regel i listan ovan och kontrol lera **omfångs filtret**. I följande omfångs filter, om **isCriticalSystemObject** -värdet är null eller falskt eller tomt, finns det i omfånget.

  ![Skärm bild av ett omfångs filter i en regel sökning för inkommande synkronisering](./media/tshoot-connect-object-not-syncing/scopingfilter.png)

Gå till listan [CS importera](#cs-import) attribut och kontrol lera vilket filter som hindrar objektet från att flyttas till MV. Attributlistan för **kopplings utrymme** visar endast icke-null-attribut och icke-tomma attribut. Om t. ex. **isCriticalSystemObject** inte visas i listan, är värdet för det här attributet null eller tomt.

### <a name="object-not-found-in-the-azure-ad-cs"></a>Objektet hittades inte i Azure AD CS
Om objektet inte finns i anslutnings utrymmet för Azure AD, men finns i MV, tittar du på omfångs filtret för utgående regler för motsvarande kopplings utrymme och tar reda på om objektet är filtrerat eftersom [MV-attributen](#mv-attributes) inte uppfyller kriterierna.

Om du vill titta på filtret för utgående scope väljer du tillämpliga regler för objektet genom att justera filtret nedan. Visa varje regel och titta på motsvarande värdet [MV-attributvärde](#mv-attributes) .

  ![Skärm bild av en sökning i regler för regler för utgående synkronisering](./media/tshoot-connect-object-not-syncing/outboundfilter.png)


### <a name="mv-attributes"></a>MV-attribut
På fliken **attribut** kan du se värdena och vilka kopplingar som bidragit till dem.  

![Skärm bild av metaversum-objektet Fönstret Egenskaper med fliken attribut valt](./media/tshoot-connect-object-not-syncing/mvobject.png)  

Om ett objekt inte synkroniseras ställer du följande frågor om attribut tillstånd i metaversum:
- Är attributet **cloudFiltered** tillgängligt och inställt på **Sant**? Om så är fallet har den filtrerats enligt stegen i [attributbaserade filtrering](how-to-connect-sync-configure-filtering.md#attribute-based-filtering).
- Finns attributet **sourceAnchor** ? Om inte, har du en topologi för konto resurs skogen? Om ett objekt identifieras som en länkad post låda (attributet **msExchRecipientTypeDetails** har värdet **2**), bidrogs **sourceAnchor** av skogen med ett aktiverat Active Directory-konto. Kontrol lera att huvud kontot har importer ATS och synkroniserats korrekt. Huvud kontot måste anges bland [anslutningarna](#mv-connectors) för objektet.

### <a name="mv-connectors"></a>MV-kopplingar
Fliken **anslutningar** visar alla kopplings utrymmen som har en representation av objektet. 
 
![Skärm bild av metaversum-objektet Fönstret Egenskaper med fliken anslutningar valt](./media/tshoot-connect-object-not-syncing/mvconnectors.png)  

Du bör ha en koppling för att:

- Varje Active Directory skog som användaren representeras i. Den här representationen kan innehålla **foreignSecurityPrincipals** -och **kontakt** objekt.
- En anslutning i Azure AD.

Om du saknar anslutningen till Azure AD läser du avsnittet om [MV-attribut](#mv-attributes) för att kontrol lera villkoren för etablering i Azure AD.

På fliken **anslutningar** kan du också gå till [objektet kopplings utrymme](#connector-space-object-properties). Markera en rad och klicka på **Egenskaper**.

## <a name="next-steps"></a>Nästa steg
- Läs mer om [Azure AD Connect Sync](how-to-connect-sync-whatis.md).
- Läs mer om [hybrid identitet](whatis-hybrid-identity.md).