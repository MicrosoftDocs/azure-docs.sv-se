---
title: 'Azure AD Connect: konfigurera önskad data plats för Microsoft 365 resurser'
description: Beskriver hur du sätter Microsoft 365 användar resurser nära användaren med Azure Active Directory Connect Sync.
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
ms.topic: how-to
ms.date: 11/11/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: e4c456e7788280b7ca5328342e1cd848ba3a583a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "95972767"
---
# <a name="azure-active-directory-connect-sync-configure-preferred-data-location-for-microsoft-365-resources"></a>Azure Active Directory Connect synkronisering: konfigurera önskad data plats för Microsoft 365 resurser
Syftet med det här avsnittet är att hjälpa dig att konfigurera attributet för önskad data plats i Azure Active Directory (Azure AD) Connect-synkronisering. När någon använder flera geo-funktioner i Microsoft 365 använder du det här attributet för att ange geo-platsen för användarens Microsoft 365 data. ( *Regions region* och *geo* används utbytbart.)

## <a name="enable-synchronization-of-preferred-data-location"></a>Aktivera synkronisering av önskad data plats
Microsoft 365 resurser för dina användare finns som standard i samma geografiska område som din Azure AD-klient. Om din klient organisation till exempel finns i Nordamerika, finns även användarens Exchange-postlådor i Nordamerika. För en multinationell organisation är detta kanske inte optimalt.

Genom att ange attributet **preferredDataLocation** kan du definiera en användares geo. Du kan ha användarens Microsoft 365 resurser, t. ex. post lådan och OneDrive, i samma geo som användaren och fortfarande ha en klient för hela organisationen.

> [!IMPORTANT]
> Multi-geo är för närvarande tillgängligt för kunder med en aktiv Enterprise-avtal och minst 250 Microsoft 365 Services-prenumerationer. Kontakta din Microsoft-representant om du vill ha mer information.
>
>

Du hittar en lista över alla geografiska områden för Microsoft 365 i [var finns dina data?](/microsoft-365/enterprise/o365-data-locations).

Geografiska områden i Microsoft 365 tillgängligt för multi-geo är:

| Geografi | preferredDataLocation-värde |
| --- | --- |
| Asien och stillahavsområdet | ANROP |
| Australien | ÖSTRA |
| Kanada | SKRIVA |
| Europeiska unionen | EUR |
| Frankrike | FRA |
| Indien | IND |
| Japan | JPN |
| Korea | KOR |
| Sydafrika | ZAF |
| Schweiz | CACHELAGRA |
| Förenade Arabemiraten | TILLHANDAHÅLLS |
| Storbritannien | GBR |
| USA | Vietnam |

* Om en geo inte visas i den här tabellen (till exempel södra Amerika) kan den inte användas för multi-geo.

* Det är inte alla Microsoft 365 arbets belastningar som stöder användning av att ställa in en användares geo.

### <a name="azure-ad-connect-support-for-synchronization"></a>Azure AD Connect stöd för synkronisering

Azure AD Connect stöder synkronisering av attributet **preferredDataLocation** för **användar** objekt i version 1.1.524.0 och senare. Specifikt:

* Schemat för objekt typen **användare** i Azure AD-kopplingen har utökats till att omfatta attributet **preferredDataLocation** . Attributet är av typen, en sträng med ett värde.
* Schemat för objekt typens **person** i metaversum har utökats till att omfatta attributet **preferredDataLocation** . Attributet är av typen, en sträng med ett värde.

**PreferredDataLocation** är som standard inte aktive rad för synkronisering. Den här funktionen är avsedd för större organisationer. Active Directory-schemat i Windows Server 2019 har attributet **msDS-preferredDataLocation** som du bör använda för detta ändamål. Om du inte har uppdaterat Active Directory schema och inte kan göra det måste du identifiera ett attribut som ska innehålla Microsoft 365 geo för dina användare. Detta kommer att vara detsamma för varje organisation.

> [!IMPORTANT]
> Med Azure AD kan **preferredDataLocation** -attributet i **moln användar objekt** konfigureras direkt med hjälp av Azure AD PowerShell. Om du vill konfigurera det här attributet på **synkroniserade användar objekt** måste du använda Azure AD Connect.

Innan du aktiverar synkronisering:

* Om du inte har uppgraderat Active Directory-schemat till 2019 bestämmer du vilket lokalt Active Directory attribut som ska användas som källattribut. Den ska vara av typen, **en sträng med en värde**.
* Om du tidigare har konfigurerat attributet **preferredDataLocation** på befintliga **synkroniserade användar objekt** i Azure AD med hjälp av Azure AD PowerShell, måste du Backport attributvärdena till motsvarande **användar** objekt i den lokala Active Directory.

    > [!IMPORTANT]
    > Om du inte Backport dessa värden tar Azure AD Connect bort de befintliga attributvärdena i Azure AD när synkroniseringen för attributet **preferredDataLocation** är aktive rad.

* Konfigurera källattributet på minst ett par lokala Active Directory användar objekt nu. Du kan använda det här för verifiering senare.

I följande avsnitt beskrivs stegen för att aktivera synkronisering av **preferredDataLocation** -attributet.

> [!NOTE]
> Stegen beskrivs i kontexten för en Azure AD-distribution med en topologi med en skog och utan anpassade regler för synkronisering. Om du har en topologi för flera skogar, anpassade regler för synkronisering som kon figurer ATS eller har en fristående server, bör du justera stegen enligt detta.

## <a name="step-1-disable-sync-scheduler-and-verify-there-is-no-synchronization-in-progress"></a>Steg 1: inaktivera Sync Scheduler och kontrol lera att ingen synkronisering pågår
För att undvika oönskade ändringar som exporteras till Azure AD kontrollerar du att ingen synkronisering sker när du är i mitten av uppdateringen av regler för synkronisering. Så här inaktiverar du den inbyggda Sync Scheduler:

1. Starta en PowerShell-session på Azure AD Connect servern.
2. Inaktivera schemalagd synkronisering genom att köra denna cmdlet: `Set-ADSyncScheduler -SyncCycleEnabled $false` .
3. Starta **Synchronization Service Manageren** genom att gå till **Starta**  >  **synkroniseringstjänsten**.
4. Välj fliken **åtgärder** och bekräfta att det inte finns någon åtgärd *med statusen* pågår.

![Skärm bild av Synchronization Service Manager](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-step1.png)

## <a name="step-2-refresh-the-schema-for-active-directory"></a>Steg 2: uppdatera schemat för Active Directory
Om du har uppdaterat Active Directory-schemat till 2019 och Connect har installerats före schema tillägget, har inte Connect schema-cachen det uppdaterade schemat. Du måste sedan uppdatera schemat från guiden för att det ska visas i användar gränssnittet.

1. Starta guiden Azure AD Connect från Skriv bordet.
2. Välj alternativet **Uppdatera katalog schema** och klicka på **Nästa**.
3. Ange dina autentiseringsuppgifter för Azure AD och klicka på **Nästa**.
4. På sidan **Uppdatera katalog schema** ser du till att alla skogar är markerade och klickar på **Nästa**.
5. När du är klar stänger du guiden.

![Skärm bild av uppdaterings katalogens schema i guiden Anslut](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-refreshschema.png)

## <a name="step-3-add-the-source-attribute-to-the-on-premises-active-directory-connector-schema"></a>Steg 3: Lägg till källattributet till det lokala Active Directory kopplings schema
**Det här steget behövs bara om du kör Connect version 1.3.21 eller äldre. Om du använder 1.4.18 eller senare går du vidare till steg 5.**  
Alla Azure AD-attribut importeras inte till det lokala Active Directory anslutnings utrymmet. Om du har valt att använda ett attribut som inte är synkroniserat som standard, måste du importera det. Så här lägger du till källattributet i listan över importerade attribut:

1. Välj fliken **anslutningar** i Synchronization Service Manager.
2. Högerklicka på den lokala Active Directory anslutningen och välj **Egenskaper**.
3. I dialog rutan popup går du till fliken **Välj attribut** .
4. Kontrol lera att det källattribut som du har valt att använda är markerat i attributlistan. Om du inte ser ditt attribut markerar du kryss rutan **Visa alla** .
5. Välj **OK** för att spara.

![Skärm bild som visar dialog rutan Synchronization Service Manager och egenskaper där listan "attribut" är markerad.](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-step2.png)

## <a name="step-4-add-preferreddatalocation-to-the-azure-ad-connector-schema"></a>Steg 4: Lägg till **preferredDataLocation** i Azure AD Connector-schemat
**Det här steget behövs bara om du kör Connect version 1.3.21 eller äldre. Om du använder 1.4.18 eller senare går du vidare till steg 5.**  
Som standard importeras inte attributet **preferredDataLocation** till Azure AD Connector-utrymmet. Lägga till den i listan över importerade attribut:

1. Välj fliken **anslutningar** i Synchronization Service Manager.
2. Högerklicka på Azure AD-anslutaren och välj **Egenskaper**.
3. I dialog rutan popup går du till fliken **Välj attribut** .
4. Välj attributet **preferredDataLocation** i listan.
5. Välj **OK** för att spara.

![Skärm bild av dialog rutan Synchronization Service Manager och egenskaper](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-step3.png)

## <a name="step-5-create-an-inbound-synchronization-rule"></a>Steg 5: skapa en regel för inkommande synkronisering
Regeln för inkommande synkronisering tillåter att attributvärdet flödar från källattributet i lokala Active Directory till metaversum.

1. Starta **Redigeraren för regler för synkronisering** genom att gå till **Starta**  >  **Redigeraren för regler för synkronisering**.
2. Ange att Sök filter **riktningen** ska vara **inkommande**.
3. Om du vill skapa en ny regel för inkommande trafik väljer du **Lägg till ny regel**.
4. Ange följande konfiguration på fliken **Beskrivning** :

    | Attribut | Värde | Information |
    | --- | --- | --- |
    | Name | *Ange ett namn* | Till exempel "i från AD – User preferredDataLocation" |
    | Beskrivning | *Ange en anpassad Beskrivning* |  |
    | Anslutet system | *Välj lokal Active Directory-anslutning* |  |
    | Ansluten system objekt typ | **Användare** |  |
    | Metaversum objekt typ | **Person** |  |
    | Länktyp | **Join** |  |
    | Prioritet | *Välj ett tal mellan 1 – 99* | 1 – 99 är reserverad för anpassade regler för synkronisering. Välj inte ett värde som används av en annan Synkroniseringsregel. |

5. Behåll **omfångs filtret** tomt om du vill inkludera alla objekt. Du kan behöva justera omfångs filtret enligt din Azure AD Connect-distribution.
6. Gå till **fliken omvandling** och implementera följande omvandlings regel:

    | Flödes typ | Target-attribut | Källa | Använd en gång | Sammanslagnings typ |
    | --- | --- | --- | --- | --- |
    |Direct | preferredDataLocation | Välj källattribut | Avmarkerat | Uppdatera |

7. Välj **Lägg till** för att skapa regeln för inkommande trafik.

![Skärm bild av Skapa regel för inkommande synkronisering](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-step4.png)

## <a name="step-6-create-an-outbound-synchronization-rule"></a>Steg 6: skapa en regel för utgående synkronisering
Regeln för utgående synkronisering tillåter att attributvärdet flödar från metaversum till **preferredDataLocation** -attributet i Azure AD:

1. Gå till **Redigeraren för regler för synkronisering**.
2. Ange att Sök filter **riktningen** ska vara **utgående**.
3. Välj **Lägg till ny regel**.
4. Ange följande konfiguration på fliken **Beskrivning** :

    | Attribut | Värde | Information |
    | ----- | ------ | --- |
    | Name | *Ange ett namn* | Till exempel "ut till Azure AD – User preferredDataLocation" |
    | Beskrivning | *Ange en beskrivning* ||
    | Anslutet system | *Välj Azure AD-anslutning* ||
    | Ansluten system objekt typ | **Användare** ||
    | Metaversum objekt typ | **Person** ||
    | Länktyp | **Join** ||
    | Prioritet | *Välj ett tal mellan 1 – 99* | 1 – 99 är reserverad för anpassade regler för synkronisering. Välj inte ett värde som används av en annan Synkroniseringsregel. |

5. Gå till fliken **omfångs filter** och Lägg till en enda omfångs filter grupp med två satser:

    | Attribut | Operator | Värde |
    | --- | --- | --- |
    | sourceObjectType | SKEPPNINGSKVANTITETEN | User |
    | cloudMastered | NOTEQUAL | Sant |

    Omfångs filtret bestämmer vilka Azure AD-objekt den här utgående synkroniseringsregeln tillämpas på. I det här exemplet använder vi samma omfångs filter från "ut till Azure AD – användar identitet" OOB (out-of-Box) synkroniseringsregeln. Det förhindrar att synkroniseringsregeln tillämpas på **användar** objekt som inte synkroniseras från en lokal Active Directory. Du kan behöva justera omfångs filtret enligt din Azure AD Connect-distribution.

6. Gå till fliken **omvandling** och implementera följande omvandlings regel:

    | Flödes typ | Target-attribut | Källa | Använd en gång | Sammanslagnings typ |
    | --- | --- | --- | --- | --- |
    | Direct | preferredDataLocation | preferredDataLocation | Avmarkerat | Uppdatera |

7. Stäng **Lägg till** för att skapa regeln för utgående trafik.

![Skärm bild av Skapa regel för utgående synkronisering](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-step5.png)

## <a name="step-7-run-full-synchronization-cycle"></a>Steg 7: kör en fullständig synkronisering
I allmänhet krävs en fullständig synkronisering. Det beror på att du har lagt till nya attribut till både Active Directory-och Azure AD Connector-schemat och infört anpassade regler för synkronisering. Verifiera ändringarna innan du exporterar dem till Azure AD. Du kan använda följande steg för att verifiera ändringarna, samtidigt som du kör de steg som utgör en fullständig synkronisering.

1. Kör **fullständig import** på den lokala Active Directory-anslutningen:

   1. Gå till fliken **åtgärder** i Synchronization Service Manager.
   2. Högerklicka på den **lokala Active Directory anslutningen** och välj **Kör**.
   3. I dialog rutan väljer du **fullständig import** och väljer **OK**.
   4. Vänta tills åtgärden har slutförts.

      > [!NOTE]
      > Du kan hoppa över fullständig import av den lokala Active Directory-anslutningen om källattributet redan finns i listan över importerade attribut. Det innebär att du inte behöver göra några ändringar under steg 2 tidigare i den här artikeln.

2. Kör **fullständig import** på Azure AD-anslutningen:

   1. Högerklicka på **Azure AD-anslutaren** och välj **Kör**.
   2. I dialog rutan väljer du **fullständig import** och väljer **OK**.
   3. Vänta tills åtgärden har slutförts.

3. Verifiera ändringarna i synkroniseringsregeln för ett befintligt **användar** objekt.

   Källattributet från lokala Active Directory och **preferredDataLocation** från Azure AD, har importer ATS till varje kopplings utrymme. Innan du fortsätter med steget fullständig synkronisering gör du en förhands granskning av ett befintligt **användar** objekt i det lokala Active Directory anslutnings utrymmet. Det objekt som du har valt ska ha attributet source ifyllt. En lyckad för hands version med **preferredDataLocation** ifylld i metaversum är en bra indikator att du har konfigurerat reglerna för synkronisering korrekt. Information om hur du gör en förhands granskning finns i [Verifiera ändringen](how-to-connect-sync-change-the-configuration.md#verify-the-change).

4. Kör **fullständig synkronisering** på den lokala Active Directory-anslutningen:

   1. Högerklicka på den **lokala Active Directory anslutningen** och välj **Kör**.
   2. I dialog rutan väljer du **fullständig synkronisering** och väljer **OK**.
   3. Vänta tills åtgärden har slutförts.

5. Verifiera **väntande exporter** till Azure AD:

   1. Högerklicka på **Azure AD-anslutaren** och välj **search Connector-utrymme**.
   2. I dialog rutan **Sök efter anslutnings utrymme** :

        a. Ange **omfång** till **väntande export**.<br>
        b. Markera alla tre kryss rutorna, inklusive **Lägg till, ändra och ta bort**.<br>
        c. Om du vill visa en lista över objekt med ändringar som ska exporteras väljer du **Sök**. Om du vill granska ändringarna för ett objekt dubbelklickar du på objektet.<br>
        d. Kontrol lera att ändringarna förväntas.

6. Kör **export** på **Azure AD-anslutningen**

   1. Högerklicka på **Azure AD-anslutaren** och välj **Kör**.
   2. I dialog rutan **Kör koppling** väljer du **Exportera** och väljer **OK**.
   3. Vänta tills åtgärden har slutförts.

> [!NOTE]
> Du kanske märker att stegen inte inkluderar steget fullständig synkronisering i Azure AD-anslutningen eller export steget på Active Directory-anslutningen. Stegen är inte obligatoriska eftersom attributvärdena flödar från lokala Active Directory endast till Azure AD.

## <a name="step-8-re-enable-sync-scheduler"></a>Steg 8: återaktivera synkronisering av Schemaläggaren
Återaktivera den inbyggda Sync Scheduler:

1. Starta en PowerShell-session.
2. Aktivera schemalagd synkronisering igen genom att köra denna cmdlet: `Set-ADSyncScheduler -SyncCycleEnabled $true`

## <a name="step-9-verify-the-result"></a>Steg 9: verifiera resultatet
Det är nu dags att verifiera konfigurationen och aktivera den för dina användare.

1. Lägg till geo till det valda attributet för en användare. Det går att hitta listan över tillgängliga geografiska områden i den här tabellen.  
![Skärm bild av attributet AD som har lagts till i en användare](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-adattribute.png)
2. Vänta tills attributet har synkroniserats till Azure AD.
3. Använd Exchange Online PowerShell och kontrol lera att post lådans region har angetts korrekt.  
![Skärm bild av Exchange Online PowerShell](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-mailboxregion.png)  
Om din klient har marker ATS för att kunna använda den här funktionen, flyttas post lådan till rätt geo. Du kan kontrol lera detta genom att titta på Server namnet där post lådan finns.

## <a name="next-steps"></a>Nästa steg

Lär dig mer om multi-geo i Microsoft 365:

* [Multi-geo-sessioner vid antändning](https://aka.ms/MultiGeoIgnite)
* [Multi-geo i OneDrive](/microsoft-365/enterprise/multi-geo-capabilities-in-onedrive-and-sharepoint-online-in-microsoft-365)
* [Multi-geo i SharePoint Online](/microsoft-365/enterprise/multi-geo-capabilities-in-onedrive-and-sharepoint-online-in-microsoft-365)

Läs mer om konfigurations modellen i Synkroniseringsmotorn:

* Läs mer om konfigurations modellen i att [förstå deklarativ etablering](concept-azure-ad-connect-sync-declarative-provisioning.md).
* Läs mer om uttrycks språket i [förstå deklarativ etablerings uttryck](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md).

Översikts avsnitt:

* [Azure AD Connect-synkronisering: Förstå och anpassa synkronisering](how-to-connect-sync-whatis.md)
* [Integrera dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md)