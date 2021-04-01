---
title: 'Azure AD Connect synkronisering: ändra standard konfigurationen'
description: Vägleder dig genom hur du gör en ändring i konfigurationen i Azure AD Connect Sync.
services: active-directory
author: billmath
manager: daveba
ms.assetid: 7b9df836-e8a5-4228-97da-2faec9238b31
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/30/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2044653673da10de59d5ff125da44ac1f89e22f9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96861858"
---
# <a name="azure-ad-connect-sync-make-a-change-to-the-default-configuration"></a>Azure AD Connect synkronisering: ändra standard konfigurationen
Syftet med den här artikeln är att hjälpa dig att göra ändringar i standard konfigurationen i Azure Active Directory (Azure AD) Connect-synkronisering. Den innehåller steg för några vanliga scenarier. Med den här kunskapen bör du kunna göra enkla ändringar i din egen konfiguration utifrån dina egna affärs regler.

> [!WARNING]
> Om du gör ändringar i standard reglerna för osynkroniserade synkronisering kommer dessa ändringar att skrivas över nästa gång Azure AD Connect uppdateras, vilket resulterar i oväntade och sannolika synkroniseringsresultat.
>
> Standard reglerna för synkronisering av standardinställda osynkroniserade har ett tumavtryck. Om du gör en ändring av de här reglerna matchar inte tumavtrycket längre. Du kan ha problem i framtiden när du försöker tillämpa en ny version av Azure AD Connect. Gör bara ändringar på det sätt som beskrivs i den här artikeln.

## <a name="synchronization-rules-editor"></a>Redigerare för regler för synkronisering
Redigeraren för regler för synkronisering används för att se och ändra standard konfigurationen. Du hittar den på **Start** -menyn under gruppen **Azure AD Connect** .  
![Start-menyn med regel redigeraren för synkronisering](./media/how-to-connect-sync-change-the-configuration/startmenu2.png)

När du öppnar Redigeraren visas standard reglerna som är färdiga.

![Regel redigeraren för synkronisering](./media/how-to-connect-sync-change-the-configuration/sre2.png)

### <a name="navigating-in-the-editor"></a>Navigera i redigeraren
Med hjälp av list rutorna längst upp i redigeraren kan du snabbt hitta en speciell regel. Om du till exempel vill se reglerna där attributet proxyAddresses ingår kan du ändra List rutorna till följande:  
![SRE-filtrering](./media/how-to-connect-sync-change-the-configuration/filtering.png)  
Tryck på F5 på tangent bordet för att återställa filtrering och läsa in en ny konfiguration.

Uppe till höger är knappen **Lägg till ny regel** . Använd den här knappen för att skapa en egen anpassad regel.

Längst ned finns knappar för att agera på en vald Synkroniseringsregel. **Redigera** och **ta bort** det du förväntar dig. **Export** skapar ett PowerShell-skript för att återskapa synkroniseringsregeln. Med den här proceduren kan du flytta en Synkroniseringsregel från en server till en annan.

## <a name="create-your-first-custom-rule"></a>Skapa din första anpassade regel
De vanligaste ändringarna är för attributets flöden. Data i käll katalogen kanske inte är samma som i Azure AD. I exemplet i det här avsnittet ser du till att namnet på en användare alltid är i *rätt fall*.

### <a name="disable-the-scheduler"></a>Inaktivera Scheduler
[Scheduler](how-to-connect-sync-feature-scheduler.md) körs var 30: e minut som standard. Kontrol lera att det inte startar när du gör ändringar och felsöka dina nya regler. Starta PowerShell och kör om du vill inaktivera Scheduler tillfälligt `Set-ADSyncScheduler -SyncCycleEnabled $false` .

![Inaktivera Scheduler](./media/how-to-connect-sync-change-the-configuration/schedulerdisable.png)  

### <a name="create-the-rule"></a>Skapa regeln
1. Klicka på **Lägg till ny regel**.
2. På sidan **Beskrivning** anger du följande:  
   ![Filtrering av inkommande regler](./media/how-to-connect-sync-change-the-configuration/description2.png)  
   * **Namn**: ge regeln ett beskrivande namn.
   * **Beskrivning**: ge en förklaring så att någon annan kan förstå vad regeln gäller.
   * **Anslutet system**: det här är det system där objektet kan hittas. I det här fallet väljer du **Active Directory koppling**.
   * **Ansluten system-metaversum objekt typ**: Välj **användare** respektive **person**.
   * **Länktyp**: ändra värdet till **Join**.
   * **Prioritet**: Ange ett värde som är unikt i systemet. Ett lägre numeriskt värde anger högre prioritet.
   * **Tag**: lämna tomt. Endast färdiga regler från Microsoft ska ha den här rutan ifylld med ett värde.
3. På sidan **omfångs filter** anger du **givenName ISNOTNULL**.  
   ![Omfångs filter för inkommande regel](./media/how-to-connect-sync-change-the-configuration/scopingfilter.png)  
   Det här avsnittet används för att definiera vilka objekt som regeln ska gälla. Om det lämnas tomt gäller regeln för alla användar objekt. Det kan dock omfatta konferens rum, tjänst konton och andra användar objekt som inte är personer.
4. Lämna fältet tomt på sidan **Anslut regler** .
5. På sidan **omvandlingar** ändrar du **FlowType** till **Expression**. För **målattribut** väljer du **givenName**. Och för **källa** anger du **PCase ([givenName])**.
   ![Transformeringar för inkommande regel](./media/how-to-connect-sync-change-the-configuration/transformations.png)  
   Synkroniseringsmotorn är Skift läges känslig för både funktions namnet och namnet på attributet. Om du anger något fel visas en varning när du lägger till regeln. Du kan spara och fortsätta, men du måste öppna och korrigera regeln igen.
6. Klicka på **Lägg till** för att spara regeln.

Den nya anpassade regeln bör vara synlig med de andra reglerna för synkronisering i systemet.

### <a name="verify-the-change"></a>Verifiera ändringen
Med den här nya ändringen vill du se till att den fungerar som förväntat och inte utlöser några fel. Beroende på antalet objekt finns det två sätt att göra detta steg:

- Kör en fullständig synkronisering för alla objekt.
- Kör en för hands version och fullständig synkronisering på ett enda objekt.

Öppna **synkroniseringstjänsten** på **Start** -menyn. Stegen i det här avsnittet är alla i det här verktyget.

**Fullständig synkronisering för alla objekt**  

   1. Välj **kopplingar** överst. Identifiera den koppling som du ändrade i föregående avsnitt (i det här fallet Active Directory Domain Services) och markera den. 
   2. För **åtgärder** väljer du **Kör**.
   3. Välj **fullständig synkronisering** och välj sedan **OK**.
   ![Fullständig synkronisering](./media/how-to-connect-sync-change-the-configuration/fullsync.png)  
   Objekten har nu uppdaterats i metaversum. Verifiera ändringarna genom att titta på objektet i metaversum.

**För hands version och fullständig synkronisering på ett enskilt objekt**  

   1. Välj **kopplingar** överst. Identifiera den koppling som du ändrade i föregående avsnitt (i det här fallet Active Directory Domain Services) och markera den.
   2. Välj **search Connector-utrymme**. 
   3. Använd **omfång** för att hitta ett objekt som du vill använda för att testa ändringen. Markera objektet och klicka på **Förhandsgranska**. 
   4. På den nya skärmen väljer du **Förhandsgranskning av incheckning**.  
   ![Bekräfta för hands version](./media/how-to-connect-sync-change-the-configuration/commitpreview.png)  
   Ändringen allokeras nu till metaversum.

**Visa objektet i metaversum**  

1. Välj några exempel objekt för att se till att värdet förväntas och att regeln tillämpas. 
2. Välj **metaversum-sökning** längst upp. Lägg till eventuella filter som du behöver för att hitta relevanta objekt. 
3. Öppna ett objekt från Sök resultatet. Titta på attributvärdena och kontrol lera också i kolumnen **regler för synkronisering** som regeln tillämpade som förväntat.  
![Metaversumsökning](./media/how-to-connect-sync-change-the-configuration/mvsearch.png)  

### <a name="enable-the-scheduler"></a>Aktivera Scheduler
Om allt är som förväntat kan du aktivera Scheduler igen. Från PowerShell kör du `Set-ADSyncScheduler -SyncCycleEnabled $true` .

## <a name="other-common-attribute-flow-changes"></a>Andra vanliga ändringar i attributändringar
I föregående avsnitt beskrivs hur du gör ändringar i ett attribut flöde. I det här avsnittet finns ytterligare exempel. Stegen för att skapa en Synkroniseringsregel är förkortad, men du kan hitta de fullständiga stegen i föregående avsnitt.

### <a name="use-an-attribute-other-than-the-default"></a>Använd ett annat attribut än standard
I det här Fabrikam-scenariot finns det en skog där det lokala alfabetet används för angivet namn, efter namn och visnings namn. Den latinska tecken representationen för dessa attribut finns i attributen för tillägg. Organisationen vill använda dessa attribut i stället för att skapa en global adress lista i Azure AD och Microsoft 365.

Med en standard konfiguration ser ett objekt från den lokala skogen ut så här:  
![Attribut flöde 1](./media/how-to-connect-sync-change-the-configuration/attributeflowjp1.png)

Gör så här om du vill skapa en regel med andra attribut flöden:

1. Öppna **Redigeraren för Synkroniseringsregel** från **Start** -menyn.
2. Med **inkommande** fortfarande valt till vänster klickar du på knappen **Lägg till ny regel** .
3. Ge regeln ett namn och en beskrivning. Välj den lokala Active Directory-instansen och relevanta objekt typer. I **Länktyp** väljer du **Anslut**. För **prioritet** väljer du ett tal som inte används av en annan regel. De färdiga reglerna börjar med 100, så värdet 50 kan användas i det här exemplet.
  ![Attribut flöde 2](./media/how-to-connect-sync-change-the-configuration/attributeflowjp2.png)
4. Lämna **omfångs filter** tomt. (Det vill säga det ska gälla för alla användar objekt i skogen.)
5. Lämna **kopplings regler** tomma. (Det innebär att du kan använda den färdiga regeln som hanterar alla kopplingar.)
6. I **transformeringar** skapar du följande flöden:  
  ![Attribut flöde 3](./media/how-to-connect-sync-change-the-configuration/attributeflowjp3.png)
7. Klicka på **Lägg till** för att spara regeln.
8. Gå till **Synchronization Service Manager**. På **kopplingar** väljer du den koppling där du lade till regeln. Välj **Kör** och välj sedan **fullständig synkronisering**. En fullständig synkronisering beräknar om alla objekt med de aktuella reglerna.

Detta är resultatet av samma objekt med den här anpassade regeln:  
![Attributets flöde 4](./media/how-to-connect-sync-change-the-configuration/attributeflowjp4.png)

### <a name="length-of-attributes"></a>Längd på attribut
String-attribut kan indexeras som standard och den maximala längden är 448 tecken. Om du arbetar med String-attribut som kan innehålla mer, se till att ta med följande i attributet Flow:  
`attributeName` <- `Left([attributeName],448)`.

### <a name="changing-the-userprincipalsuffix"></a>Ändra userPrincipalSuffix
Attributet userPrincipalName i Active Directory är inte alltid känt av användarna och kanske inte är lämpligt som inloggnings-ID. Med installations guiden för Azure AD Connect Sync kan du välja ett annat attribut, till exempel *e-post*. Men i vissa fall måste attributet beräknas.

Företaget Contoso har till exempel två Azure AD-kataloger, en för produktion och en för testning. De vill att användarna i sin test klient ska använda ett annat suffix i inloggnings-ID: t:  
`userPrincipalName` <- `Word([userPrincipalName],1,"@") & "@contosotest.com"`.

I det här uttrycket ska du ta allt från början av det första @-sign (ordet) och sammanfoga med en fast sträng.

### <a name="convert-a-multi-value-attribute-to-single-value"></a>Konvertera ett flervärdesattribut till ett enskilt värde
Vissa attribut i Active Directory är Multivärdes i schemat, även om de ser ut med enkel värde i Active Directory användare och datorer. Ett exempel är attributet Description:  
`description` <- `IIF(IsNullOrEmpty([description]),NULL,Left(Trim(Item([description],1)),448))`.

I det här uttrycket, om attributet har ett värde, tar du det första objektet (*objekt*) i attributet, tar bort inledande och avslutande blank steg (*trim*) och behåller de första 448 tecknen (*vänster*) i strängen.

### <a name="do-not-flow-an-attribute"></a>Flöda inget attribut
För bakgrund i scenariot för det här avsnittet, se [styra attributets Flödes process](concept-azure-ad-connect-sync-declarative-provisioning.md#control-the-attribute-flow-process).

Det finns två sätt att inte flöda ett attribut. Det första är genom att använda installations guiden för att [ta bort valda attribut](how-to-connect-install-custom.md#azure-ad-app-and-attribute-filtering). Det här alternativet fungerar om du aldrig har synkroniserat attributet tidigare. Men om du har börjat synkronisera det här attributet och senare tar bort det med den här funktionen slutar Synkroniseringsmotorn hantera attributet och de befintliga värdena är kvar i Azure AD.

Om du vill ta bort värdet för ett attribut och se till att det inte flödar i framtiden måste du skapa en anpassad regel.

I det här Fabrikam-scenariot har vi realiserat att några av de attribut som vi synkroniserar till molnet inte ska finnas där. Vi vill se till att dessa attribut tas bort från Azure AD.  
![Felaktiga attribut för tillägg](./media/how-to-connect-sync-change-the-configuration/badextensionattribute.png)

1. Skapa en ny regel för inkommande synkronisering och fyll i beskrivningen.
  ![Förklaringar](./media/how-to-connect-sync-change-the-configuration/syncruledescription.png)
2. Skapa attribut flöden med **uttryck** för **FlowType** och med **AuthoritativeNull** för **källa**. Litteral **AuthoritativeNull** anger att värdet ska vara tomt i metaversum, även om en regel för lägre prioritet försöker fylla i värdet.
  ![Transformering för tilläggets attribut](./media/how-to-connect-sync-change-the-configuration/syncruletransformations.png)
3. Spara synkroniseringsregeln. Starta **synkroniseringstjänsten**, hitta anslutningen, Välj **Kör** och välj sedan **fullständig synkronisering**. Det här steget beräknar om alla attribut flöden.
4. Kontrol lera att de avsedda ändringarna ska exporteras genom att söka i anslutnings utrymmet.
  ![Mellanlagrad borttagning](./media/how-to-connect-sync-change-the-configuration/deletetobeexported.png)

## <a name="create-rules-with-powershell"></a>Skapa regler med PowerShell
Användning av regel redigeraren för synkronisering fungerar bra om du bara har några ändringar att göra. Om du behöver göra många ändringar kan PowerShell vara ett bättre alternativ. Vissa avancerade funktioner är bara tillgängliga med PowerShell.

### <a name="get-the-powershell-script-for-an-out-of-box-rule"></a>Hämta PowerShell-skriptet för en out-of-Box-regel
Om du vill se PowerShell-skriptet som skapade en regel som är inaktuell väljer du regeln i redigeraren för regler för synkronisering och klickar på **Exportera**. Den här åtgärden ger dig det PowerShell-skript som skapade regeln.

### <a name="advanced-precedence"></a>Avancerad prioritet
De färdiga Sync-reglerna börjar med prioritet svärdet 100. Om du har många skogar och behöver göra många anpassade ändringar, kan det hända att reglerna för 99-synkronisering inte räcker.

Du kan instruera den Synkroniseringsmotorn som du vill att ytterligare regler ska infogas före de färdiga reglerna. Följ dessa steg för att få det här problemet:

1. Markera den första synkroniseringsregeln (**i från AD-User koppling**) i redigeraren för regler för synkronisering och välj **Exportera**. Kopiera värdet för SR-identifieraren.  
![PowerShell före ändring](./media/how-to-connect-sync-change-the-configuration/powershell1.png)  
2. Skapa den nya synkroniseringsregeln. Du kan använda redigeraren för synkronisering av regler för att skapa den. Exportera regeln till ett PowerShell-skript.
3. I egenskapen **PrecedenceBefore** infogar du ID-värdet från regeln som är inaktuell. Ange **prioriteten** till **0**. Kontrol lera att attributet Identifier är unikt och att du inte använder ett GUID från en annan regel. Kontrol lera också att egenskapen **ImmutableTag** inte har angetts. Den här egenskapen ska endast anges för en regel som inte är i regel.
4. Spara PowerShell-skriptet och kör det. Resultatet är att den anpassade regeln tilldelas prioritet svärdet 100 och alla andra regler som inte ingår i rutan ökar.  
![PowerShell efter ändring](./media/how-to-connect-sync-change-the-configuration/powershell2.png)  

Du kan ha många anpassade regler för synkronisering med samma **PrecedenceBefore** -värde när det behövs.

## <a name="enable-synchronization-of-usertype"></a>Aktivera synkronisering av UserType
Azure AD Connect stöder synkronisering av attributet **UserType** för **användar** objekt i version 1.1.524.0 och senare. Mer specifikt har följande ändringar införts:

- Schemat för objekt typen **användare** i Azure AD-kopplingen har utökats till att omfatta attributet UserType, som är av typen sträng och är ett enkelvärdesattribut.
- Schemat för objekt typens **person** i metaversum har utökats till att omfatta attributet UserType, som är av typen sträng och är ett enkelvärdesattribut.

Som standard är attributet UserType inte aktiverat för synkronisering eftersom det inte finns något motsvarande UserType-attribut i lokala Active Directory. Du måste aktivera synkronisering manuellt. Innan du gör detta måste du anteckna följande beteende som tillämpas av Azure AD:

- Azure AD accepterar bara två värden för attributet UserType: **medlem** och **gäst**.
- Om attributet UserType inte är aktiverat för synkronisering i Azure AD Connect skulle Azure AD-användare som har skapats via Directory-synkronisering ha attributet UserType inställt på **medlem**.
- Innan version 1.5.30.0 tillät inte Azure AD UserType-attributet på befintliga Azure AD-användare att ändras av Azure AD Connect. I äldre versioner kunde det bara anges när Azure AD-användare skapas och [ändras via PowerShell](/powershell/module/azuread/set-azureaduser).

Innan du aktiverar synkronisering av UserType-attributet måste du först bestämma hur attributet härleds från lokala Active Directory. Följande är de vanligaste metoderna:

- Ange ett oanvänt lokalt AD-attribut (till exempel extensionAttribute1) som ska användas som källattribut. Det angivna lokala AD-attributet ska vara av typen **sträng**, vara ett enkelvärdesattribut och innehålla värdet **medlem** eller **gäst**. 

    Om du väljer den här metoden måste du se till att det angivna attributet är ifyllt med rätt värde för alla befintliga användar objekt i lokala Active Directory som synkroniseras med Azure AD innan du aktiverar synkronisering av attributet UserType.

- Du kan också härleda värdet för attributet UserType från andra egenskaper. Till exempel vill du synkronisera alla användare som **gäst** om deras lokala AD userPrincipalName-attribut slutar med domän del <em>@partners.fabrikam123.org</em> . 

    Som tidigare nämnts tillåter äldre versioner av Azure AD Connect inte att attributet UserType på befintliga Azure AD-användare ändras av Azure AD Connect. Därför måste du se till att den logik du har valt stämmer överens med hur UserType-attributet redan har kon figurer ATS för alla befintliga Azure AD-användare i din klient organisation.

Stegen för att aktivera synkronisering av UserType-attributet kan sammanfattas som:

1.  Inaktivera Schemaläggaren och kontrol lera att ingen synkronisering pågår.
2.  Lägg till källattributet i det lokala AD Connector-schemat.
3.  Lägg till UserType i Azure AD Connector-schemat.
4.  Skapa en regel för inkommande synkronisering för att flöda attributvärdet från den lokala Active Directory.
5.  Skapa en regel för utgående synkronisering för att flöda attributvärdet till Azure AD.
6.  Kör en fullständig synkronisering.
7.  Aktivera synkronisering av Schemaläggaren.

>[!NOTE]
> Resten av det här avsnittet beskriver de här stegen. De beskrivs i kontexten för en Azure AD-distribution med en topologi med en skog och utan anpassade regler för synkronisering. Om du har en topologi för flera skogar, anpassade synkroniseringsinställningar som kon figurer ATS eller har en fristående server, måste du justera stegen enligt detta.

### <a name="step-1-disable-the-sync-scheduler-and-verify-there-is-no-synchronization-in-progress"></a>Steg 1: inaktivera synkroniseringsschemat och kontrol lera att ingen synkronisering pågår
Undvik att exportera oönskade ändringar till Azure AD genom att se till att ingen synkronisering äger rum när du är i mitten av uppdateringen av regler för synkronisering. Så här inaktiverar du den inbyggda Sync Scheduler:

 1. Starta en PowerShell-session på Azure AD Connect servern.
 2. Inaktivera schemalagd synkronisering genom att köra cmdleten `Set-ADSyncScheduler -SyncCycleEnabled $false` .
 3. Öppna Synchronization Service Manager genom att gå till **Starta**  >  **synkroniseringstjänsten**.
 4. Gå till fliken **åtgärder** och bekräfta att det inte finns någon åtgärd med statusen *pågår*.

### <a name="step-2-add-the-source-attribute-to-the-on-premises-ad-connector-schema"></a>Steg 2: Lägg till källattributet i det lokala AD Connector-schemat
Alla Azure AD-attribut importeras inte till det lokala AD Connector-utrymmet. Så här lägger du till källattributet i listan över importerade attribut:

 1. Gå till fliken **anslutningar** i Synchronization Service Manager.
 2. Högerklicka på den lokala AD-anslutningen och välj **Egenskaper**.
 3. I dialog rutan popup går du till fliken **Välj attribut** .
 4. Kontrol lera att källattributet är markerat i attributlistan.
 5. Klicka på **OK** för att spara.
![Lägg till källattribut till det lokala AD Connector-schemat](./media/how-to-connect-sync-change-the-configuration/usertype1.png)

### <a name="step-3-add-the-usertype-attribute-to-the-azure-ad-connector-schema"></a>Steg 3: Lägg till attributet UserType i Azure AD Connector-schemat
Som standard importeras inte attributet UserType till Azure AD Connect utrymmet. Så här lägger du till attributet UserType i listan över importerade attribut:

 1. Gå till fliken **anslutningar** i Synchronization Service Manager.
 2. Högerklicka på **Azure AD-anslutaren** och välj **Egenskaper**.
 3. I dialog rutan popup går du till fliken **Välj attribut** .
 4. Kontrol lera att attributet UserType är markerat i attributlistan.
 5. Klicka på **OK** för att spara.

![Lägg till källattribut till Azure AD Connector-schemat](./media/how-to-connect-sync-change-the-configuration/usertype2.png)

### <a name="step-4-create-an-inbound-synchronization-rule-to-flow-the-attribute-value-from-on-premises-active-directory"></a>Steg 4: skapa en regel för inkommande synkronisering för att flöda attributvärdet från den lokala Active Directory
Regeln för inkommande synkronisering tillåter att attributvärdet flödar från källattributet från lokala Active Directory till metaversum:

1. Öppna redigeraren för regler för synkronisering genom att gå till **Starta**  >  **Redigera regler för synkronisering av synkronisering**.
2. Ange att Sök filter **riktningen** ska vara **inkommande**.
3. Klicka på knappen **Lägg till ny regel** för att skapa en ny regel för inkommande trafik.
4. Ange följande konfiguration på fliken **Beskrivning** :

    | Attribut | Värde | Information |
    | --- | --- | --- |
    | Name | *Ange ett namn* | Till exempel *i från AD – User UserType* |
    | Beskrivning | *Ange en beskrivning* |  |
    | Anslutet system | *Välj lokal AD-anslutning* |  |
    | Ansluten system objekt typ | **Användare** |  |
    | Metaversum objekt typ | **Person** |  |
    | Länktyp | **Join** |  |
    | Prioritet | *Välj ett tal mellan 1 – 99* | 1 – 99 är reserverad för anpassade regler för synkronisering. Välj inte ett värde som används av en annan Synkroniseringsregel. |

5. Gå till fliken **omfångs filter** och Lägg till en **enda omfångs filter grupp** med följande sats:

    | Attribut | Operator | Värde |
    | --- | --- | --- |
    | adminDescription | NOTSTARTWITH | Användarvänlig\_ |

    Omfångs filtret avgör vilka lokala AD-objekt som den här inkommande synkroniseringsregeln tillämpas på. I det här exemplet använder vi samma omfångs filter som används i den inbyggda synkroniseringsregeln för *AD – användare* , vilket förhindrar att synkroniseringsregeln tillämpas på användar objekt som skapats via funktionen tillbakaskrivning av Azure AD-användare. Du kan behöva justera omfångs filtret enligt din Azure AD Connect-distribution.

6. Gå till fliken **omvandling** och implementera önskad omvandlings regel. Om du till exempel har angett ett oanvänt lokalt AD-attribut (till exempel extensionAttribute1) som källattribut för UserType, kan du implementera ett dirigerat attributarkiv:

    | Flödes typ | Target-attribut | Källa | Använd en gång | Sammanslagnings typ |
    | --- | --- | --- | --- | --- |
    | Direct | UserType | extensionAttribute1 | Avmarkerat | Uppdatera |

    I ett annat exempel vill du härleda värdet för attributet UserType från andra egenskaper. Till exempel vill du synkronisera alla användare som gäst om deras lokala AD userPrincipalName-attribut slutar med domän del <em>@partners.fabrikam123.org</em> . Du kan implementera ett uttryck som detta:

    | Flödes typ | Target-attribut | Källa | Använd en gång | Sammanslagnings typ |
    | --- | --- | --- | --- | --- |
    | Uttryck | UserType | IIF (IsPresent ([userPrincipalName]), IIF (CBool (InStr (LCase ([userPrincipalName]), " @partners.fabrikam123.org ") = 0), "medlem", "gäst"), fel ("userPrincipalName finns inte för att fastställa UserType")) | Avmarkerat | Uppdatera |

7. Klicka på **Lägg till** för att skapa regeln för inkommande trafik.

![Skapa regel för inkommande synkronisering](./media/how-to-connect-sync-change-the-configuration/usertype3.png)

### <a name="step-5-create-an-outbound-synchronization-rule-to-flow-the-attribute-value-to-azure-ad"></a>Steg 5: skapa en regel för utgående synkronisering för att flöda attributvärdet till Azure AD
Regeln för utgående synkronisering tillåter att attributvärdet flödar från metaversum till UserType-attributet i Azure AD:

1. Gå till redigeraren för regler för synkronisering.
2. Ange att Sök filter **riktningen** ska vara **utgående**.
3. Klicka på knappen **Lägg till ny regel** .
4. Ange följande konfiguration på fliken **Beskrivning** :

    | Attribut | Värde | Information |
    | ----- | ------ | --- |
    | Name | *Ange ett namn* | Till exempel *till AAD – User UserType* |
    | Beskrivning | *Ange en beskrivning* ||
    | Anslutet system | *Välj AAD-koppling* ||
    | Ansluten system objekt typ | **Användare** ||
    | Metaversum objekt typ | **Person** ||
    | Länktyp | **Join** ||
    | Prioritet | *Välj ett tal mellan 1 – 99* | 1 – 99 är reserverad för anpassade regler för synkronisering. Välj inte ett värde som används av en annan Synkroniseringsregel. |

5. Gå till fliken **omfångs filter** och Lägg till en **enda omfångs filter grupp** med två satser:

    | Attribut | Operator | Värde |
    | --- | --- | --- |
    | sourceObjectType | SKEPPNINGSKVANTITETEN | User |
    | cloudMastered | NOTEQUAL | Sant |

    Omfångs filtret fastställer till vilka Azure AD-objekt denna utgående Synkroniseringsregel ska tillämpas. I det här exemplet använder vi samma omfångs filter från regeln *ut till AD – användarens identitet* utanför box. Det förhindrar att synkroniseringsregeln tillämpas på användar objekt som inte är synkroniserade från lokala Active Directory. Du kan behöva justera omfångs filtret enligt din Azure AD Connect-distribution.

6. Gå till fliken **omvandling** och implementera följande omvandlings regel:

    | Flödes typ | Target-attribut | Källa | Använd en gång | Sammanslagnings typ |
    | --- | --- | --- | --- | --- |
    | Direct | UserType | UserType | Avmarkerat | Uppdatera |

7. Klicka på **Lägg till** för att skapa regeln för utgående trafik.

![Skapa regel för utgående synkronisering](./media/how-to-connect-sync-change-the-configuration/usertype4.png)

### <a name="step-6-run-a-full-synchronization-cycle"></a>Steg 6: kör en fullständig synkronisering
I allmänhet krävs en fullständig synkronisering eftersom vi har lagt till nya attribut för både Active Directory-och Azure AD Connector-scheman och infört anpassade regler för synkronisering. Du vill verifiera ändringarna innan du exporterar dem till Azure AD. 

Du kan använda följande steg för att kontrol lera ändringarna samtidigt som du kör stegen som utgör en fullständig synkronisering.

1. Kör en **fullständig import** på den **lokala AD-anslutningen**:

   1. Gå till fliken **anslutningar** i Synchronization Service Manager.
   2. Högerklicka på den **lokala AD-anslutningen** och välj **Kör**.
   3. I dialog rutan popup väljer du **fullständig import** och klickar sedan på **OK**.
   4. Vänta tills åtgärden har slutförts.

      > [!NOTE]
      > Du kan hoppa över en fullständig import av den lokala AD-anslutningen om källattributet redan finns i listan över importerade attribut. Med andra ord behöver du inte göra några ändringar under [steg 2: Lägg till källattributet till det lokala AD Connector-schemat](#step-2-add-the-source-attribute-to-the-on-premises-ad-connector-schema).

2. Kör en **fullständig import** på **Azure AD-anslutningen**:

   1. Högerklicka på **Azure AD-anslutaren** och välj **Kör**.
   2. I dialog rutan popup väljer du **fullständig import** och klickar sedan på **OK**.
   3. Vänta tills åtgärden har slutförts.

3. Verifiera ändringarna i synkroniseringsregeln för ett befintligt användar objekt:

    Källattribut från lokala Active Directory och UserType från Azure AD har importer ATS till respektive kopplings utrymmen. Innan du fortsätter med en fullständig synkronisering ska du göra en **förhands granskning** av ett befintligt användar objekt i det lokala AD Connector-utrymmet. Det valda objektet ska ha attributet source ifyllt.
    
    En lyckad för **hands version** med den UserType som är ifylld i metaversum är en bra indikator som du har konfigurerat för att synkronisera reglerna på rätt sätt. Information om hur du gör en **förhands granskning** finns i avsnittet [Verifiera ändringen](#verify-the-change).

4. Kör en **fullständig synkronisering** för den **lokala AD-anslutningen**:

   1. Högerklicka på den **lokala AD-anslutningen** och välj **Kör**.
   2. I dialog rutan popup väljer du **fullständig synkronisering** och klickar sedan på **OK**.
   3. Vänta tills åtgärden har slutförts.

5. Verifiera **väntande exporter** till Azure AD:

   1. Högerklicka på **Azure AD-anslutaren** och välj **search Connector-utrymme**.
   2. I popup-dialog rutan för **Sök kopplings utrymmen** :

      - Ange **omfång** till **väntande export**.
      - Markera alla tre kryss rutorna: **Lägg till**, **ändra** och **ta bort**.
      - Klicka på **Sök** knappen för att hämta listan över objekt med ändringar som ska exporteras. Om du vill granska ändringarna för ett objekt dubbelklickar du på objektet.
      - Kontrol lera att ändringarna förväntas.

6. Kör **export** på **Azure AD-anslutningen**:

   1. Högerklicka på **Azure AD-anslutaren** och välj **Kör**.
   2. I dialog rutan **Kör anslutningsprogram** väljer du **Exportera** och klickar sedan på **OK**.
   3. Vänta tills exporten till Azure AD har slutförts.

> [!NOTE]
> De här stegen omfattar inte fullständig synkronisering och export steg på Azure AD-anslutningen. De här stegen är inte obligatoriska eftersom attributvärdena flödar från lokala Active Directory endast till Azure AD.

### <a name="step-7-re-enable-the-sync-scheduler"></a>Steg 7: återaktivera synkroniseringen av Schemaläggaren
Återaktivera den inbyggda Sync Scheduler:

1. Starta en PowerShell-session.
2. Återaktivera den schemalagda synkroniseringen genom att köra cmdleten `Set-ADSyncScheduler -SyncCycleEnabled $true` .


## <a name="next-steps"></a>Nästa steg
* Läs mer om konfigurations modellen i att [förstå deklarativ etablering](concept-azure-ad-connect-sync-declarative-provisioning.md).
* Läs mer om uttrycks språket i [förstå deklarativ etablerings uttryck](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md).

**Översikts avsnitt**

* [Azure AD Connect-synkronisering: Förstå och anpassa synkronisering](how-to-connect-sync-whatis.md)
* [Integrera dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md)
