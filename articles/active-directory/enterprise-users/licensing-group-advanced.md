---
title: Ytterligare scenarier med gruppbaserad licensiering – Azure AD | Microsoft Docs
description: Fler scenarier för Azure Active Directory gruppbaserad licensiering
services: active-directory
keywords: Azure AD-licensiering
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: enterprise-users
ms.topic: how-to
ms.workload: identity
ms.date: 12/02/2020
ms.author: curtand
ms.reviewer: sumitp
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 72a28aebe71809f3e47f9cc6936366cd027a32bb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96546683"
---
# <a name="scenarios-limitations-and-known-issues-using-groups-to-manage-licensing-in-azure-active-directory"></a>Scenarier, begränsningar och kända problem med grupper för att hantera licensiering i Azure Active Directory

Använd följande information och exempel för att få en mer avancerad förståelse av Azure Active Directory (Azure AD) Group-based Licensing.

## <a name="usage-location"></a>Användnings plats

Vissa Microsoft-tjänster är inte tillgängliga på alla platser. Innan en licens kan tilldelas en användare måste administratören ange egenskapen för **användnings plats** för användaren. I [Azure Portal](https://portal.azure.com)kan du ange användnings plats i inställningar för **användar** &gt; **profil** &gt; .

För grupp licens tilldelningen ärver alla användare som saknar en användnings plats platsen för katalogen. Om du har användare på flera platser måste du se till att de stämmer överens med dina användar resurser innan du lägger till användare i grupper med licenser.

> [!NOTE]
> Grupp licens tilldelningen kommer aldrig att ändra ett befintligt värde för användnings plats för en användare. Vi rekommenderar att du alltid anger användnings plats som en del av ditt flöde för att skapa användare i Azure AD (t. ex. via AAD Connect-konfiguration) – som säkerställer att licens tilldelningen alltid är korrekt och användare inte får tjänster på platser som inte är tillåtna.

## <a name="use-group-based-licensing-with-dynamic-groups"></a>Använda gruppbaserad licensiering med dynamiska grupper

Du kan använda gruppbaserad licensiering med valfri säkerhets grupp, vilket innebär att den kan kombineras med dynamiska Azure AD-grupper. Dynamiska grupper kör regler mot användar resursens attribut för att automatiskt lägga till och ta bort användare från grupper.

Du kan till exempel skapa en dynamisk grupp för en uppsättning produkter som du vill tilldela till användare. Varje grupp fylls i av en regel som lägger till användare baserat på deras attribut, och varje grupp tilldelas de licenser som du vill att de ska ta emot. Du kan tilldela attributet lokalt och synkronisera det med Azure AD, eller så kan du hantera attributet direkt i molnet.

Licenser tilldelas användaren strax efter att de lagts till i gruppen. När attributet ändras lämnar användaren grupperna och licenserna tas bort.

### <a name="example"></a>Exempel

Överväg exemplet på en lokal identitets hanterings lösning som avgör vilka användare som ska ha åtkomst till Microsofts webb tjänster. Den använder **extensionAttribute1** för att lagra ett sträng värde som representerar de licenser som användaren ska ha. Azure AD Connect synkroniseras med Azure AD.

Användare kan behöva en licens, men inte en annan, eller kan behöva båda. Här är ett exempel där du distribuerar licenser för Office 365 Enterprise E5 och Enterprise Mobility + Security (EMS) till användare i grupper:

#### <a name="office-365-enterprise-e5-base-services"></a>Office 365 Enterprise E5: bas tjänster

![Skärm bild av Office 365 Enterprise E5 Base Services](./media/licensing-group-advanced/o365-e5-base-services.png)

#### <a name="enterprise-mobility--security-licensed-users"></a>Enterprise Mobility + Security: licensierade användare

![Skärm bild av Enterprise Mobility + Security licensierade användare](./media/licensing-group-advanced/o365-e5-licensed-users.png)

I det här exemplet ändrar du en användare och anger deras extensionAttribute1 till värdet för `EMS;E5_baseservices;` om du vill att användaren ska ha båda licenserna. Du kan göra den här ändringen lokalt. När ändringen synkroniseras med molnet läggs användaren automatiskt till i båda grupperna och licenserna tilldelas.

![Skärm bild som visar hur du ställer in användarens extensionAttribute1](./media/licensing-group-advanced/user-set-extensionAttribute1.png)

> [!WARNING]
> Var försiktig när du ändrar en befintlig grupps medlemskaps regel. När en regel ändras kommer medlemskapet i gruppen att utvärderas igen och användare som inte längre matchar den nya regeln kommer att tas bort (användare som fortfarande matchar den nya regeln påverkas inte under den här processen). Dessa användare kommer att ha sina licenser borttagna under processen, vilket kan leda till förlust av tjänster, eller i vissa fall, data förlust.
> 
> Om du har en stor dynamisk grupp som du är beroende av för licens tilldelning bör du överväga att verifiera eventuella större ändringar i en mindre test grupp innan du tillämpar dem på huvud gruppen.

## <a name="multiple-groups-and-multiple-licenses"></a>Flera grupper och flera licenser

En användare kan vara medlem i flera grupper med licenser. Här följer några saker att tänka på:

- Flera licenser för samma produkt kan överlappa och medför att alla aktiverade tjänster används för användaren. I följande exempel visas två licens grupper: *E3 Base Services* innehåller de grundläggande tjänster som ska distribueras först till alla användare. Och *E3 utökade tjänster* innehåller ytterligare tjänster (Sway och Planner) som endast distribueras till vissa användare. I det här exemplet har användaren lagts till i båda grupperna:

  ![Skärm bild av aktiverade tjänster](./media/licensing-group-advanced/view-enabled-services.png)

  Användaren har därför 7 av de 12 tjänsterna i produkten aktive rad, medan endast en licens används för den här produkten.

- Om du väljer *E3* -licensen visas mer information, inklusive information om vilka tjänster som har Aktiver ATS för användaren av grupp licens tilldelningen.

## <a name="direct-licenses-coexist-with-group-licenses"></a>Direkta licenser tillsammans med grupp licenser

När en användare ärver en licens från en grupp kan du inte ta bort eller ändra licens tilldelningen direkt i användarens egenskaper. Ändringar måste göras i gruppen och sedan spridas till alla användare.

Det går dock att tilldela samma produkt licens direkt till användaren, utöver den ärvda licensen. Du kan aktivera ytterligare tjänster från produkten precis för en användare, utan att påverka andra användare.

Direkt tilldelade licenser kan tas bort och påverkar inte ärvda licenser. Överväg att användaren som ärver en Office 365 Enterprise E3-licens från en grupp.

Användaren ärver inlednings vis bara licensen från *E3 Basic Services* -gruppen, som möjliggör fyra tjänste planer.

1. Välj **tilldela** för att tilldela användaren en E3-licens direkt. I så fall kommer du att inaktivera alla Service planer förutom Yammer Enterprise.

    Detta innebär att användaren fortfarande använder en licens av E3-produkten. Men direkt tilldelningen aktiverar endast Yammer Enterprise-tjänsten för den användaren. Du kan se vilka tjänster som aktive ras av grupp medlemskapet jämfört med direkt tilldelningen.

1. När du använder direkt tilldelning tillåts följande åtgärder:

   - Yammer Enterprise kan stängas av direkt på användar resursen. Växla **på/av** i bilden har Aktiver ATS för den här tjänsten, i stället för att växla till den andra tjänsten. Eftersom tjänsten är aktive rad direkt för användaren kan den ändras.
   - Ytterligare tjänster kan även aktive ras som en del av den direkt tilldelade licensen.
   - Knappen **ta bort** kan användas för att ta bort direkt licensen från användaren. Du kan se att användaren nu bara har den ärvda grupp licensen och att endast de ursprungliga tjänsterna förblir aktiverade:

## <a name="managing-new-services-added-to-products"></a>Hantera nya tjänster som har lagts till i produkter

När Microsoft lägger till en ny tjänst i en produkt licens plan aktive ras den som standard i alla grupper som du har tilldelat produkt licensen. Användare i din organisation som prenumererar på meddelanden om produkt ändringar får e-postmeddelanden före den tid som meddelar dem om de kommande tjänst tilläggen.

Som administratör kan du granska alla grupper som påverkas av ändringen och vidta åtgärder, till exempel inaktivera den nya tjänsten i varje grupp. Om du till exempel har skapat grupper som riktar sig enbart till specifika tjänster för distribution kan du gå tillbaka till dessa grupper och se till att alla nyligen tillagda tjänster är inaktiverade.

Här är ett exempel på hur den här processen kan se ut:

1. Ursprungligen tilldelade du *Office 365 Enterprise E5* -produkten till flera grupper. En av dessa grupper som kallas *O365 E5-Exchange har endast* utformats för att endast aktivera *Exchange Online-tjänsten (plan 2)* för dess medlemmar.

2. Du har fått ett meddelande från Microsoft att E5-produkten kommer att utökas med en ny tjänst *Microsoft Stream*. När tjänsten blir tillgänglig i din organisation kan du göra följande:

3. Gå till bladet [**Azure Active Directory > licenser > alla produkter**](https://portal.azure.com/#blade/Microsoft_AAD_IAM/LicensesMenuBlade/Products) och välj *Office 365 Enterprise E5*. Välj sedan **licensierade grupper** för att visa en lista över alla grupper med produkten.

4. Klicka på den grupp som du vill granska (i det här fallet *O365 E5-endast Exchange*). Då öppnas fliken **licenser** . Om du klickar på E5-licensen öppnas ett blad med alla aktiverade tjänster.
   > [!NOTE]
   > Tjänsten *Microsoft Stream* har lagts till automatiskt och Aktiver ATS i den här gruppen, förutom *Exchange Online* -tjänsten:

   ![Skärm bild av ny tjänst som lagts till i en grupp licens](./media/licensing-group-advanced/manage-new-services.png)

5. Om du vill inaktivera den nya tjänsten i den här gruppen klickar du på **/på** växla bredvid tjänsten och klickar sedan på knappen **Spara** för att bekräfta ändringen. Azure AD kommer nu att bearbeta alla användare i gruppen för att tillämpa ändringen. nya användare som läggs till i gruppen kommer inte att ha tjänsten *Microsoft Stream* aktive rad.

   > [!NOTE]
   > Användare kan fortfarande ha tjänsten aktive rad via en annan licens tilldelning (en annan grupp som de är medlemmar i eller en direkt licens tilldelning).

6. Om det behövs utför du samma steg för andra grupper med den här produkten tilldelad.

## <a name="use-powershell-to-see-who-has-inherited-and-direct-licenses"></a>Använd PowerShell för att se vem som har ärvt och direkta licenser
Du kan använda ett PowerShell-skript för att kontrol lera om användarna har en licens som tilldelats direkt eller ärvts från en grupp.

1. Kör `connect-msolservice` cmdleten för att autentisera och ansluta till din organisation.

2. `Get-MsolAccountSku` kan användas för att identifiera alla etablerade produkt licenser i Azure AD-organisationen.

   ![Skärm bild av Get-Msolaccountsku-cmdlet](./media/licensing-group-advanced/get-msolaccountsku-cmdlet.png)

3. Använd *AccountSkuId* -värdet för den licens som du är intresse rad av med [det här PowerShell-skriptet](licensing-ps-examples.md#check-if-user-license-is-assigned-directly-or-inherited-from-a-group). Då skapas en lista med användare som har den här licensen med information om hur licensen tilldelas.

## <a name="use-audit-logs-to-monitor-group-based-licensing-activity"></a>Använd gransknings loggar för att övervaka gruppbaserad licensierings aktivitet

Du kan använda [Azure AD audit-loggar](../reports-monitoring/concept-audit-logs.md#audit-logs) för att se all aktivitet som är relaterad till gruppbaserad licensiering, inklusive:
- Vem ändrade licenser för grupper
- När systemet startade bearbetningen av en grupp licens ändring och när den är färdig
- vilka licens ändringar har gjorts till en användare till följd av en grupp licens tilldelning.

>[!NOTE]
> Gransknings loggar är tillgängliga på de flesta blad i avsnittet Azure Active Directory i portalen. Beroende på var du har åtkomst till dem kan filter användas i förväg för att endast visa aktiviteter som är relevanta för bladets sammanhang. Om du inte ser de resultat du förväntar dig kan du undersöka [filtrerings alternativen](../reports-monitoring/concept-audit-logs.md#filtering-audit-logs) eller komma åt de ofiltrerade gransknings loggarna under [**Azure Active Directory > aktivitet > gransknings loggar**](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Audit).

### <a name="find-out-who-modified-a-group-license"></a>Ta reda på vem som ändrade en grupp licens

1. Ange **aktivitets** filtret för att *Ange grupp licens* och klicka på **Använd**.
2. Resultatet inkluderar alla fall av licenser som anges eller ändras för grupper.
   >[!TIP]
   > Du kan också ange namnet på gruppen i *mål* filtret för att begränsa resultaten.

3. Markera ett objekt i listan om du vill se information om vad som har ändrats. Under *ändrade egenskaper* visas både gamla och nya värden för licens tilldelningen.

Här är ett exempel på de senaste grupp licens ändringarna, med information:

![Skärm bild som visar sidan "gransknings loggar" där ett List objekt har marker ATS och fönstret "aktivitets informations Gransknings logg" öppnas.](./media/licensing-group-advanced/audit-group-license-change.png)

### <a name="find-out-when-group-changes-started-and-finished-processing"></a>Ta reda på när grupp ändringar startade och har bearbetats

När en licens ändras i en grupp kommer Azure AD att börja tillämpa ändringarna på alla användare.

1. Om du vill se när grupper har börjat bearbeta anger du **aktivitets** filtret för att *börja använda gruppbaserad licens för användare*. Observera att aktören för åtgärden är *Microsoft Azure AD Group-Based-licensiering* – ett system konto som används för att köra alla grupp licens ändringar.
   >[!TIP]
   > Klicka på ett objekt i listan om du vill se fältet *ändrade egenskaper* – det visar de licens ändringar som har hämtats för bearbetning. Detta är användbart om du har gjort flera ändringar i en grupp och inte är säker på vilken som bearbetades.

2. På samma sätt kan du se när grupper har slutfört bearbetningen genom att använda filter värdet *Slutför tillämpning av gruppbaserad licens för användare*.
   > [!TIP]
   > I det här fallet innehåller fältet *ändrade egenskaper* en sammanfattning av resultaten – detta är användbart för att snabbt kontrol lera om bearbetningen resulterade i fel. Exempel på utdata:
   > ```
   > Modified Properties
   > ...
   > Name : Result
   > Old Value : []
   > New Value : [Users successfully assigned licenses: 6, Users for whom license assignment failed: 0.];
   > ```

3. Om du vill se en fullständig logg för hur en grupp har bearbetats, inklusive alla användar ändringar, anger du följande filter:
   - **Initierad av (aktör)**: "Microsoft Azure AD Group-Based licensiering"
   - **Datum intervall** (valfritt): det anpassade intervallet för när du vet att en speciell grupp har startats och bearbetats

I det här exemplet på utdata visas starten av bearbetningen, alla resulterande användar ändringar och bearbetnings processen.

![Ändringar av skärm bilds grupp licens](./media/licensing-group-advanced/audit-group-processing-log.png)

>[!TIP]
> Om du klickar på objekt som är relaterade till *ändra användar licens* visas information om licens ändringar som tillämpas på varje enskild användare.

## <a name="deleting-a-group-with-an-assigned-license"></a>Ta bort en grupp med en tilldelad licens

Det går inte att ta bort en grupp med en aktiv tilldelad licens. En administratör kan ta bort en grupp som inte realiserar att den kommer att göra att licenser tas bort från användarna – av den anledningen kräver vi att alla licenser tas bort från gruppen först innan den kan tas bort.

När du försöker ta bort en grupp i Azure Portal kan ett fel meddelande visas som detta: ![ Det gick inte att ta bort skärm bilds gruppen](./media/licensing-group-advanced/groupdeletionfailed.png)

Gå till fliken **licenser** i gruppen och se om det finns några tilldelade licenser. Om ja, ta bort dessa licenser och försök att ta bort gruppen igen.

Du kan se liknande fel när du försöker ta bort gruppen via PowerShell eller Graph API. Om du använder en grupp som synkroniseras lokalt kan Azure AD Connect också rapportera fel om det inte går att ta bort gruppen i Azure AD. I alla sådana fall måste du kontrol lera om det finns några licenser som har tilldelats gruppen och ta bort dem först.

## <a name="limitations-and-known-issues"></a>Begränsningar och kända problem

Om du använder gruppbaserad licensiering, är det en bra idé att bekanta dig med följande lista över begränsningar och kända problem.

- Gruppbaserad licensiering stöder för närvarande inte grupper som innehåller andra grupper (kapslade grupper). Om du använder en licens för en kapslad grupp är det bara den första nivån av användarmedlemmar i gruppen som har licenser.

- Funktionen kan bara användas med säkerhets grupper och Microsoft 365 grupper som har securityEnabled = TRUE.

- [Microsoft 365 administrations Center](https://admin.microsoft.com) stöder för närvarande inte gruppbaserad licensiering. Om en användare ärver en licens från en grupp visas den här licensen i Office Admin-portalen som en vanlig användar licens. Om du försöker ändra licensen eller försöker ta bort licensen, returnerar portalen ett fel meddelande. Ärvda grupp licenser kan inte ändras direkt på en användare.

- När licenser tilldelas eller ändras för en stor grupp (till exempel 100 000 användare) kan det påverka prestandan. Mer specifikt kan mängden ändringar som genereras av Azure AD Automation påverka prestandan för din katalog synkronisering mellan Azure AD och lokala system.

- Om du använder dynamiska grupper för att hantera dina användares medlemskap kontrollerar du att användaren är medlem i gruppen, vilket är nödvändigt för licenstilldelning. Om inte [kontrollerar du bearbetningsstatusen för medlemskapsregeln](groups-create-rule.md) för den dynamiska gruppen.

- I vissa situationer med hög belastning kan det ta lång tid att bearbeta licens ändringar för grupper eller medlemskaps ändringar i grupper med befintliga licenser. Om du ser att ändringarna tar mer än 24 timmar att bearbeta grupp storleken för 60K-användare eller mindre, [öppnar du ett support ärende](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/supportRequest) för att vi ska kunna undersöka. 

- Automatisering av licens hantering reagerar inte automatiskt på alla typer av ändringar i miljön. Du kan till exempel ha slut på licenser, vilket gör att vissa användare har fel tillstånd. Du kan frigöra antalet tillgängliga platser genom att ta bort vissa direkt tilldelade licenser från andra användare. Systemet reagerar dock inte automatiskt på den här ändringen och korrigerar användare i det fel tillstånd.

  Som en lösning på dessa typer av begränsningar kan du gå till **grupp** bladet i Azure AD och klicka på **reprocesse**. Det här kommandot bearbetar alla användare i gruppen och löser fel tillstånd, om möjligt.

## <a name="next-steps"></a>Nästa steg

Mer information om andra scenarier för licenshantering via gruppbaserad licensiering finns här:

* [Vad är gruppbaserad licensiering i Azure Active Directory?](../fundamentals/active-directory-licensing-whatis-azure-portal.md)
* [Tilldela licenser till en grupp i Azure Active Directory](licensing-groups-assign.md)
* [Identifiera och lösa licensproblem för en grupp i Azure Active Directory](licensing-groups-resolve-problems.md)
* [Migrera enskilda licensierade användare till gruppbaserad licensiering i Azure Active Directory](licensing-groups-migrate-users.md)
* [Så här migrerar du användare mellan produkt licenser med gruppbaserad licensiering i Azure Active Directory](licensing-groups-change-licenses.md)
