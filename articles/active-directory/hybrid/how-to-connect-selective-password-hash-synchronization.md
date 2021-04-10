---
title: Selektiv hash-synkronisering av lösen ord för Azure AD Connect
description: I den här artikeln beskrivs hur du konfigurerar och konfigurerar det selektiva lösen ordets hash-synkronisering som ska användas med Azure AD Connect.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/16/2021
ms.subservice: hybrid
ms.author: billmath
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 248d5e163eb046edd130d69307a1c553d434b92d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105604676"
---
# <a name="selective-password-hash-synchronization-configuration-for-azure-ad-connect"></a>Konfiguration av konfiguration av selektivt lösen ord för Azure AD Connect

[Hash-synkronisering av lösen ord](whatis-phs.md) är en av de inloggnings metoder som används för att utföra hybrid identitet. Azure AD Connect synkroniserar en hash-hash för en användares lösen ord från en lokal Active Directory instans till en molnbaserad Azure AD-instans.  Som standard sker synkroniseringen av lösen ordets hash-synkronisering för alla användare som du synkroniserar när den har kon figurer ATS.

Om du vill ha en delmängd användare som undantas från att synkronisera sin lösen ords-hash till Azure AD kan du konfigurera en selektiv hash-synkronisering för lösen ord med hjälp av de guidade stegen som beskrivs i den här artikeln.

>[!Important]
> Microsoft stöder inte ändring eller operativ Azure AD Connect synkronisering utanför de konfigurationer eller åtgärder som dokumenteras formellt. Någon av dessa konfigurationer eller åtgärder kan resultera i ett inkonsekvent eller tillstånd som inte stöds för Azure AD Connect Sync. Därför kan Microsoft inte garantera att vi kan tillhandahålla effektiv teknisk support för sådana distributioner. 


## <a name="consider-your-implementation"></a>Överväg din implementering  
För att minska den administrativa ansträngningen för konfigurationen bör du först fundera över antalet användar objekt som du vill undanta från synkronisering av lösen ords-hash. Kontrol lera vilka av scenarierna nedan som är ömsesidigt uteslutande, justeras efter dina behov och välj rätt konfigurations alternativ åt dig.
- Om antalet användare som ska **undantas** är **mindre** än antalet användare som ska **inkluderas** följer du stegen i det här [avsnittet](#excluded-users-is-smaller-than-included-users).
- Om antalet användare som ska **undantas** är **större** än antalet användare som ska **inkluderas** följer du stegen i det här [avsnittet](#excluded-users-is-larger-than-included-users).

> [!Important]
> Med något av de valda konfigurations alternativen utförs en nödvändig inledande synkronisering (fullständig synkronisering) för att tillämpa ändringarna automatiskt under nästa synkronisering.

> [!Important]
> Konfigurera selektiv lösenordsautentisering för hash direkt påverkar tillbakaskrivning av lösen ord. Lösen ords ändringar eller lösen ords återställningar som initieras i Azure Active Directory bara skriva tillbaka till lokala Active Directory om användaren är i omfång för Lösenordssynkronisering. 

### <a name="the-admindescription-attribute"></a>Attributet adminDescription
Båda scenarierna förlitar sig på att ange adminDescription-attributet för användare till ett angivet värde.  Detta gör att reglerna kan tillämpas och är det som gör selektiv PHS arbete.

|Scenario|adminDescription-värde|
|-----|-----|
|Exkluderade användare är mindre än inkluderade användare|PHSFiltered|
|Exkluderade användare är större än inkluderade användare|PHSIncluded|

Det här attributet kan anges antingen:

- använda användar gränssnittet för Active Directory användare och datorer
- använda `Set-ADUser` PowerShell-cmdlet.  Mer information finns i [set-ADUser](https://docs.microsoft.com/powershell/module/addsadministration/set-aduser).

 


### <a name="disable-the-synchronization-scheduler"></a>Inaktivera synkroniseringsschemat:
Innan du startar något av scenarierna måste du inaktivera synkroniseringsschemat när du gör ändringar i reglerna för synkronisering.
 1. Starta Windows PowerShell-RETUR.

     ```Set-ADSyncScheduler -SyncCycleEnabled $false``` 
 
2.  Bekräfta att Scheduler har inaktiverats genom att köra följande cmdlet:
     
    ```Get-ADSyncScheduler```

Mer information om Scheduler finns i [Azure AD Connect Sync Scheduler](how-to-connect-sync-feature-scheduler.md).




## <a name="excluded-users-is-smaller-than-included-users"></a>Exkluderade användare är mindre än inkluderade användare
I följande avsnitt beskrivs hur du aktiverar det selektiva lösen ordets hash-synkronisering när antalet användare som ska **exkluderas** är **mindre** än antalet användare som ska **tas med**.

>[!Important]
> Innan du fortsätter ser du till att synkronisering Scheduler är inaktive rad enligt ovan.

- Skapa en redigerbar kopia av **modulen i från AD – User AccountEnabled** med alternativet att **Aktivera synkronisering av lösen ords-hash avmarkerat** och definiera dess omfångs filter 
- Skapa en annan redigerbar kopia av standardinställningen **i från AD – User AccountEnabled** med alternativet att **aktivera hash-synkronisering av lösen ord valt** och definiera dess omfångs filter 
- Återaktivera synkroniseringen av synkroniseringsschemat 
- Ange attributvärdet i Active Directory, som definierats som ett scope-attribut för de användare som du vill tillåta i synkronisering av lösen ords-hash. 

>[!Important]
>Stegen för att konfigurera en selektiv hash-synkronisering av lösen ord påverkar endast användar objekt som har attributet **adminDescription** ifylld i Active Directory med värdet **PHSFiltered**.
Om det här attributet inte är ifyllt eller om värdet är något annat än **PHSFiltered** , kommer dessa regler inte att tillämpas på användar objekt.


### <a name="configure-the-necessary-synchronization-rules"></a>Konfigurera de nödvändiga reglerna för synkronisering:

 1. Starta redigeraren för regler för synkronisering och Ställ in filtreringen av **lösen ordet** till **på** och **regel typ** till **standard**.
     ![Starta redigeraren för regler för synkronisering](media/how-to-connect-selective-password-hash-synchronization/exclude-1.png)
 2. Välj regeln **i från AD – User AccountEnabled** för den Active Directory skogs anslutning som du vill konfigurera selektivt lösen ord på har hash-synkronisering och klicka på **Redigera**. Välj **Ja** i nästa dialog ruta för att skapa en redigerbar kopia av den ursprungliga regeln.
     ![Välj regel](media/how-to-connect-selective-password-hash-synchronization/exclude-2.png)
 3. Den första regeln inaktiverar synkronisering av lösen ords-hash. Ange följande namn för den nya anpassade regeln: **i från AD-User AccountEnabled-filter användare från PHS**.
 Ändra prioritet svärdet till ett tal som är lägre än 100 (till exempel **90** eller det som är det lägsta tillgängliga värdet i din miljö).
 Kontrol lera att kryss rutorna **Aktivera synkronisering av lösen ord** och **inaktive rad** är avmarkerade och c.
 Klicka på **Nästa**.
  ![Redigera inkommande](media/how-to-connect-selective-password-hash-synchronization/exclude-3.png)
 4. I **omfångs filter** klickar du på **Lägg till sats**.
 Välj **adminDescription** i kolumnen Attribute, **är lika** med i Operator-kolumnen och ange **PHSFiltered** som värde.
     ![Omfångs filter](media/how-to-connect-selective-password-hash-synchronization/exclude-4.png)
 5. Inga ytterligare ändringar krävs. **Kopplings regler** och **transformeringar** ska vara kvar med de kopierade standardvärdena så att du kan klicka på **Spara** nu.
 Klicka på **OK** i dialog rutan varning som informerar att en fullständig synkronisering körs vid nästa synkronisering av anslutnings cykeln.
     ![Spara regel](media/how-to-connect-selective-password-hash-synchronization/exclude-5.png)
 6. Skapa sedan en annan anpassad regel med synkronisering av lösen ord för hash aktiverat. Välj igen standard regeln **i från AD – User AccountEnabled** för den Active Directory skog som du vill konfigurera selektivt lösen ord hade synkronisering på och klicka på **Redigera**. Välj **Ja** i nästa dialog ruta för att skapa en redigerbar kopia av den ursprungliga regeln.
     ![Anpassad regel](media/how-to-connect-selective-password-hash-synchronization/exclude-6.png)
 7. Ange följande namn för den nya anpassade regeln: **i från AD-User AccountEnabled – användare som ingår i PHS**.
 Ändra prioritet svärdet till ett tal som är lägre än regeln som skapades tidigare (i det här exemplet är **89**).
 Kontrol lera att kryss rutan **Aktivera synkronisering av lösen ord** är markerad och kryss rutan **inaktive rad** är avmarkerad.
 Klicka på **Nästa**.  
     ![Redigera ny regel](media/how-to-connect-selective-password-hash-synchronization/exclude-7.png)
 8. I **omfångs filter** klickar du på **Lägg till sats**.
 Välj **adminDescription** i kolumnen Attribute, **NOTEQUAL** i kolumnen Operator och ange **PHSFiltered** som värde.
     ![Omfattnings regel](media/how-to-connect-selective-password-hash-synchronization/exclude-8.png)
 9. Inga ytterligare ändringar krävs. **Kopplings regler** och **transformeringar** ska vara kvar med de kopierade standardvärdena så att du kan klicka på **Spara** nu.
 Klicka på **OK** i dialog rutan varning som informerar att en fullständig synkronisering körs vid nästa synkronisering av anslutnings cykeln.
     ![Anslut till regler](media/how-to-connect-selective-password-hash-synchronization/exclude-9.png)
 10. Bekräfta skapande av regler. Ta bort filtreringen av **lösen ord** **för** synkronisering och **regel typ** **standard**. Och du bör se de båda nya reglerna som du nyss skapade.
     ![Bekräfta regler](media/how-to-connect-selective-password-hash-synchronization/exclude-10.png) 


### <a name="re-enable-synchronization-scheduler"></a>Återaktivera synkronisering på nytt:  
När du har slutfört stegen för att konfigurera de nödvändiga reglerna för synkronisering, återaktivera du synkroniseringen av synkroniseringsschemat med följande steg:
 1. I Windows PowerShell kör du:

     ```Set-ADSyncScheduler -SyncCycleEnabled $true```
 2. Bekräfta sedan att den har Aktiver ATS genom att köra:

     ```Get-ADSyncScheduler```

Mer information om Scheduler finns i [Azure AD Connect Sync Scheduler](how-to-connect-sync-feature-scheduler.md).

### <a name="edit-users-admindescription-attribute"></a>Redigera användare **adminDescription** attribut:
När alla konfigurationer har slutförts måste du redigera attributet **adminDescription** för alla användare som du vill **undanta** från synkronisering av lösen ords-hash i Active Directory och lägga till strängen som används i omfångs filtret: **PHSFiltered**.
   
  ![Redigera attribut](media/how-to-connect-selective-password-hash-synchronization/exclude-11.png)

Du kan också använda följande PowerShell-kommando för att redigera en användares **adminDescription** -attribut:

```Set-ADUser myuser -Replace @{adminDescription="PHSFiltered"}```

## <a name="excluded-users-is-larger-than-included-users"></a>Exkluderade användare är större än inkluderade användare
I följande avsnitt beskrivs hur du aktiverar det selektiva lösen ordets hash-synkronisering när antalet användare som ska **undantas** är **större** än antalet användare som ska **tas med**.

>[!Important]
> Innan du fortsätter ser du till att synkronisering Scheduler är inaktive rad enligt ovan.

Följande är en sammanfattning av de åtgärder som utförs i stegen nedan:

- Skapa en redigerbar kopia av **modulen i från AD – User AccountEnabled** med alternativet att **Aktivera synkronisering av lösen ords-hash avmarkerat** och definiera dess omfångs filter 
- Skapa en annan redigerbar kopia av standardinställningen **i från AD – User AccountEnabled** med alternativet att **aktivera hash-synkronisering av lösen ord valt** och definiera dess omfångs filter 
- Återaktivera synkroniseringen av synkroniseringsschemat 
- Ange attributvärdet i Active Directory, som definierats som ett scope-attribut för de användare som du vill tillåta i synkronisering av lösen ords-hash. 

>[!Important]
>Stegen för att konfigurera en selektiv hash-synkronisering av lösen ord påverkar endast användar objekt som har attributet **adminDescription** ifylld i Active Directory med värdet **PHSIncluded**.
Om det här attributet inte är ifyllt eller om värdet är något annat än **PHSIncluded** , kommer dessa regler inte att tillämpas på användar objekt.


### <a name="configure-the-necessary-synchronization-rules"></a>Konfigurera de nödvändiga reglerna för synkronisering:

 1. Starta redigeraren för regler för synkronisering och ange filtrering av **lösen ord** **för Lösenordssynkronisering på** och **regel typ** **standard**.
     ![Regeltyp](media/how-to-connect-selective-password-hash-synchronization/include-1.png)
 2. Välj regeln **i från AD – User AccountEnabled** för den Active Directory skog som du vill konfigurera selektivt lösen ord hade synkronisering på och klicka på **Redigera**. Välj **Ja** i nästa dialog ruta för att skapa en redigerbar kopia av den ursprungliga regeln.
     ![I från AD](media/how-to-connect-selective-password-hash-synchronization/include-2.png)
 3. Den första regeln inaktiverar synkronisering av lösen ords-hash. Ange följande namn för den nya anpassade regeln: **i från AD-User AccountEnabled-filter användare från PHS**.
 Ändra prioritet svärdet till ett tal som är lägre än 100 (till exempel **90** eller det som är det lägsta tillgängliga värdet i din miljö).
 Kontrol lera att kryss rutorna **Aktivera synkronisering av lösen ord** och **inaktive rad** är avmarkerade.
 Klicka på **Nästa**.
  ![Ange prioritet](media/how-to-connect-selective-password-hash-synchronization/include-3.png)
 4. I **omfångs filter** klickar du på **Lägg till sats**.
Välj **adminDescription** i kolumnen Attribute, **NOTEQUAL** i kolumnen Operator och ange **PHSIncluded** som värde.
     ![Lägg till sats](media/how-to-connect-selective-password-hash-synchronization/include-4.png)
 5. Inga ytterligare ändringar krävs. **Kopplings regler** och **transformeringar** ska vara kvar med de kopierade standardvärdena så att du kan klicka på **Spara** nu.
 Klicka på **OK** i dialog rutan varning som informerar att en fullständig synkronisering körs vid nästa synkronisering av anslutnings cykeln.
     ![Datatransformering](media/how-to-connect-selective-password-hash-synchronization/include-5.png)
 6. Skapa sedan en annan anpassad regel med synkronisering av lösen ord för hash aktiverat. Välj igen standard regeln **i från AD – User AccountEnabled** för den Active Directory skog som du vill konfigurera selektivt lösen ord hade synkronisering på och klicka på **Redigera**. Välj **Ja** i nästa dialog ruta för att skapa en redigerbar kopia av den ursprungliga regeln.
     ![Användarens AccountEnabled](media/how-to-connect-selective-password-hash-synchronization/include-6.png)
 7. Ange följande namn för den nya anpassade regeln: **i från AD-User AccountEnabled – användare som ingår i PHS**.
 Ändra prioritet svärdet till ett tal som är lägre än regeln som skapades tidigare (i det här exemplet är **89**).
 Kontrol lera att kryss rutan **Aktivera synkronisering av lösen ord** är markerad och kryss rutan **inaktive rad** är avmarkerad.
 Klicka på **Nästa**.
     ![Aktivera synkronisering av lösen ord](media/how-to-connect-selective-password-hash-synchronization/include-7.png)
 8. I **omfångs filter** klickar du på **Lägg till sats**.
 Välj **adminDescription** i kolumnen Attribute, **är lika** med i Operator-kolumnen och ange **PHSIncluded** som värde.
     ![PHSIncluded](media/how-to-connect-selective-password-hash-synchronization/include-8.png)
 9. Inga ytterligare ändringar krävs. **Kopplings regler** och **transformeringar** ska vara kvar med de kopierade standardvärdena så att du kan klicka på **Spara** nu.
 Klicka på **OK** i dialog rutan varning som informerar att en fullständig synkronisering körs vid nästa synkronisering av anslutnings cykeln.
     ![Spara nu](media/how-to-connect-selective-password-hash-synchronization/include-9.png)
 10.    Bekräfta skapande av regler. Ta bort filtreringen av **lösen ord** **för** synkronisering och **regel typ** **standard**. Och du bör se de båda nya reglerna som du nyss skapade.
     ![Synkronisera på](media/how-to-connect-selective-password-hash-synchronization/include-10.png)

### <a name="re-enable-synchronization-scheduler"></a>Återaktivera synkronisering på nytt:  
När du har slutfört stegen för att konfigurera de nödvändiga reglerna för synkronisering, återaktivera du synkroniseringen av synkroniseringsschemat med följande steg:
 1. I Windows PowerShell kör du:

     ```Set-ADSyncScheduler -SyncCycleEnabled $true```
 2. Bekräfta sedan att den har Aktiver ATS genom att köra:

     ```Get-ADSyncScheduler```

Mer information om Scheduler finns i [Azure AD Connect Sync Scheduler](how-to-connect-sync-feature-scheduler.md).

### <a name="edit-users-admindescription-attribute"></a>Redigera användare **adminDescription** attribut:
När alla konfigurationer har slutförts måste du redigera attributet **adminDescription** för alla användare som du vill ska **ingå** för synkronisering av lösen ords-hash i Active Directory och lägga till strängen som används i omfångs filtret: **PHSIncluded**.

  ![Redigera attribut](media/how-to-connect-selective-password-hash-synchronization/include-11.png)
 
 Du kan också använda följande PowerShell-kommando för att redigera en användares **adminDescription** -attribut:

 ```Set-ADUser myuser -Replace @{adminDescription="PHSIncluded"}``` 

## <a name="next-steps"></a>Nästa steg
- [Vad är synkronisering av lösenordshash?](whatis-phs.md)
- [Så här fungerar hash-synkronisering av lösen ord](how-to-connect-password-hash-synchronization.md)
