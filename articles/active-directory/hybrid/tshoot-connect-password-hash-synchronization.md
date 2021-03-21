---
title: Felsöka hash-synkronisering av lösen ord med Azure AD Connect Sync | Microsoft Docs
description: Den här artikeln innehåller information om hur du felsöker problem med hash-synkronisering av lösen ord.
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
ms.date: 03/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8c6ec162ceb51c3bf19be42219b054d8371ff221
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98247360"
---
# <a name="troubleshoot-password-hash-synchronization-with-azure-ad-connect-sync"></a>Felsöka synkronisering av lösenordshash med Azure AD Connect-synkronisering

Det här avsnittet innehåller anvisningar för hur du felsöker problem med hash-synkronisering av lösen ord. Om lösen ord inte synkroniseras som förväntat, kan det vara antingen för en delmängd av användare eller för alla användare.

För Azure Active Directory (Azure AD) Connect-distribution med version 1.1.614.0 eller efter använder du fel söknings aktiviteten i guiden för att felsöka problem med hash-synkronisering av lösen ord:

* Om du har problem där inga lösen ord synkroniseras, se till att [inga lösen ord är synkroniserade: Felsök med hjälp av avsnittet fel söknings aktivitet](#no-passwords-are-synchronized-troubleshoot-by-using-the-troubleshooting-task) .

* Om du har problem med enskilda objekt, kan du se att [objektet inte synkroniserar lösen ord: Felsök med hjälp av avsnittet fel söknings aktivitet](#one-object-is-not-synchronizing-passwords-troubleshoot-by-using-the-troubleshooting-task) .

För distribution med version 1.1.524.0 eller senare finns det en diagnostisk cmdlet som du kan använda för att felsöka problem med hash-synkronisering av lösen ord:

* Om du har problem där inga lösen ord synkroniseras, se till att [inga lösen ord är synkroniserade: Felsök med hjälp av diagnostisk cmdlet](#no-passwords-are-synchronized-troubleshoot-by-using-the-diagnostic-cmdlet) -avsnittet.

* Om du har problem med enskilda objekt, kan du se att [objektet inte synkroniserar lösen ord: Felsök med hjälp av avsnittet diagnostisk cmdlet](#one-object-is-not-synchronizing-passwords-troubleshoot-by-using-the-diagnostic-cmdlet) .

För äldre versioner av Azure AD Connect-distribution:

* Om du har problem där inga lösen ord synkroniseras, se avsnittet [inga lösen ord synkroniserade: manuella fel söknings steg](#no-passwords-are-synchronized-manual-troubleshooting-steps) .

* Om du har problem med enskilda objekt, kan du läsa avsnittet om att [inte synkronisera lösen ord: avsnittet manuella fel söknings steg](#one-object-is-not-synchronizing-passwords-manual-troubleshooting-steps) .



## <a name="no-passwords-are-synchronized-troubleshoot-by-using-the-troubleshooting-task"></a>Inga lösen ord synkroniseras: Felsök med hjälp av fel söknings aktiviteten

Du kan använda fel söknings aktiviteten för att ta reda på varför inga lösen ord synkroniseras.

> [!NOTE]
> Fel söknings aktiviteten är endast tillgänglig för Azure AD Connect version 1.1.614.0 eller senare.

### <a name="run-the-troubleshooting-task"></a>Kör fel söknings aktiviteten

Så här felsöker du problem där inga lösen ord synkroniseras:

1. Öppna en ny Windows PowerShell-session på din Azure AD Connect-Server med alternativet **Kör som administratör** .

2. Kör `Set-ExecutionPolicy RemoteSigned` eller `Set-ExecutionPolicy Unrestricted` .

3. Starta guiden Azure AD Connect.

4. Gå till sidan **Ytterligare aktiviteter** , Välj **Felsök** och klicka på **Nästa**.

5. På sidan fel sökning klickar du på **Starta** för att starta fel söknings menyn i PowerShell.

6. I huvud menyn väljer du **Felsök hash-synkronisering av lösen ord**.

7. Välj **Password hash-synkronisering inte alls** på undermenyn.

### <a name="understand-the-results-of-the-troubleshooting-task"></a>Förstå resultaten av fel söknings aktiviteten

Fel söknings aktiviteten utför följande kontroller:

* Kontrollerar att funktionen Password hash-synkronisering är aktive rad för din Azure AD-klient.

* Verifierar att Azure AD Connect servern inte är i mellanlagrings läge.

* För varje befintlig lokal Active Directory-anslutning (som motsvarar en befintlig Active Directory skog):

   * Kontrollerar att funktionen Password hash-synkronisering är aktive rad.
   
   * Söker efter lösen ord för pulsslags händelser för Lösenordssynkronisering i Windows-programmets händelse loggar.

   * För varje Active Directory domän under den lokala Active Directory anslutningen:

      * Verifierar att domänen kan kontaktas från den Azure AD Connect servern.

      * Verifierar att de Active Directory Domain Services (AD DS) som används av den lokala Active Directory anslutningen har rätt användar namn, lösen ord och behörigheter som krävs för Lösenordssynkronisering.

Följande diagram illustrerar resultatet av cmdleten för en lokal Active Directory topologi med en domän:

![Diagnostiskt utdata för synkronisering av lösen ords-hash](./media/tshoot-connect-password-hash-synchronization/phsglobalgeneral.png)

I resten av det här avsnittet beskrivs specifika resultat som returneras av uppgiften och motsvarande problem.

#### <a name="password-hash-synchronization-feature-isnt-enabled"></a>funktionen lösen ords-hash-synkronisering är inte aktive rad

Om du inte har aktiverat hash-synkronisering av lösen ord med hjälp av guiden Azure AD Connect returneras följande fel:

![Lösenordssynkronisering är inte aktiverat](./media/tshoot-connect-password-hash-synchronization/phsglobaldisabled.png)

#### <a name="azure-ad-connect-server-is-in-staging-mode"></a>Azure AD Connect servern är i mellanlagrings läge

Om Azure AD Connect servern är i mellanlagrings läge inaktive ras hash-synkronisering för lösen ord tillfälligt och följande fel returneras:

![Azure AD Connect servern är i mellanlagrings läge](./media/tshoot-connect-password-hash-synchronization/phsglobalstaging.png)

#### <a name="no-password-hash-synchronization-heartbeat-events"></a>Inga pulsslags händelser för Lösenordssynkronisering för hash

Varje lokal Active Directory-anslutning har sin egen kanal för synkronisering av lösen ords-hash. När kanalen för Lösenordssynkronisering är upprättad och det inte finns några lösen ords ändringar att synkronisera, genereras en pulsslags händelse (EventId 654) var 30: e minut under händelse loggen i Windows-programmet. För varje lokal Active Directory anslutning söker cmdleten efter motsvarande pulsslags händelser under de senaste tre timmarna. Om ingen pulsslags händelse hittas returneras följande fel:

![Ingen lösen ords synkronisering hjärt takt, händelse](./media/tshoot-connect-password-hash-synchronization/phsglobalnoheartbeat.png)

#### <a name="ad-ds-account-does-not-have-correct-permissions"></a>AD DS-kontot har inte rätt behörigheter

Om det AD DS-konto som används av den lokala Active Directory-anslutningen för att synkronisera hashar för lösen ord inte har rätt behörigheter returneras följande fel:

![Skärm bild som visar felet som returneras om AD DS-kontot har ett felaktigt användar namn eller lösen ord.](./media/tshoot-connect-password-hash-synchronization/phsglobalaccountincorrectpermission.png)

#### <a name="incorrect-ad-ds-account-username-or-password"></a>Felaktigt användar namn eller lösen ord för AD DS-konto

Om AD DS-kontot som används av den lokala Active Directory-anslutningen för att synkronisera lösen ords-hashar har ett felaktigt användar namn eller lösen ord returneras följande fel:

![Felaktig autentiseringsuppgift](./media/tshoot-connect-password-hash-synchronization/phsglobalaccountincorrectcredential.png)



## <a name="one-object-is-not-synchronizing-passwords-troubleshoot-by-using-the-troubleshooting-task"></a>Ett objekt synkroniserar inte lösen ord: Felsök med hjälp av aktiviteten fel sökning

Du kan använda fel söknings aktiviteten för att avgöra varför ett objekt inte synkroniserar lösen ord.

> [!NOTE]
> Fel söknings aktiviteten är endast tillgänglig för Azure AD Connect version 1.1.614.0 eller senare.

### <a name="run-the-diagnostics-cmdlet"></a>Kör diagnostik-cmdleten

Så här felsöker du problem för ett bestämt användar objekt:

1. Öppna en ny Windows PowerShell-session på din Azure AD Connect-Server med alternativet **Kör som administratör** .

2. Kör `Set-ExecutionPolicy RemoteSigned` eller `Set-ExecutionPolicy Unrestricted` .

3. Starta guiden Azure AD Connect.

4. Gå till sidan **Ytterligare aktiviteter** , Välj **Felsök** och klicka på **Nästa**.

5. På sidan fel sökning klickar du på **Starta** för att starta fel söknings menyn i PowerShell.

6. I huvud menyn väljer du **Felsök hash-synkronisering av lösen ord**.

7. I undermenyn väljer du **lösen ord är inte synkroniserat för ett särskilt användar konto**.

### <a name="understand-the-results-of-the-troubleshooting-task"></a>Förstå resultaten av fel söknings aktiviteten

Fel söknings aktiviteten utför följande kontroller:

* Granskar status för Active Directory-objektet i Active Directory kopplings utrymme, metaversum och Azure AD Connector-utrymme.

* Kontrollerar att det finns regler för synkronisering av lösen ordets hash-synkronisering aktive rad och tillämpas på Active Directory-objektet.

* Försöker hämta och visa resultatet av det senaste försöket att synkronisera lösen ordet för objektet.

I följande diagram illustreras resultatet av cmdleten när du felsöker hash-synkronisering av lösen ord för ett enskilt objekt:

![Diagnostiskt utdata för hash-synkronisering av lösen ord – enskilt objekt](./media/tshoot-connect-password-hash-synchronization/phssingleobjectgeneral.png)

I resten av det här avsnittet beskrivs specifika resultat som returneras av cmdleten och motsvarande problem.

#### <a name="the-active-directory-object-isnt-exported-to-azure-ad"></a>Active Directory-objektet exporteras inte till Azure AD

hash-synkronisering av lösen ord för det här lokala Active Directory kontot Miss lyckas eftersom det inte finns något motsvarande objekt i Azure AD-klienten. Följande fel returneras:

![Azure AD-objekt saknas](./media/tshoot-connect-password-hash-synchronization/phssingleobjectnotexported.png)

#### <a name="user-has-a-temporary-password"></a>Användaren har ett tillfälligt lösen ord

För närvarande stöder Azure AD Connect inte synkronisering av tillfälliga lösen ord med Azure AD. Ett lösen ord anses vara temporärt om alternativet **ändra lösen ord vid nästa inloggning** är inställt på den lokala Active Directory användaren. Följande fel returneras:

![Tillfälligt lösen ord exporteras inte](./media/tshoot-connect-password-hash-synchronization/phssingleobjecttemporarypassword.png)

#### <a name="results-of-last-attempt-to-synchronize-password-arent-available"></a>Resultatet av det senaste försöket att synkronisera lösen ord är inte tillgängligt

Som standard lagrar Azure AD Connect resultatet av lösen ord för hash-synkronisering i sju dagar. Om det inte finns några tillgängliga resultat för det valda Active Directory-objektet returneras följande varning:

![Diagnostiska utdata för ett enskilt objekt – ingen lösen ords synkroniserings historik](./media/tshoot-connect-password-hash-synchronization/phssingleobjectnohistory.png)



## <a name="no-passwords-are-synchronized-troubleshoot-by-using-the-diagnostic-cmdlet"></a>Inga lösen ord synkroniseras: Felsök med hjälp av diagnostik-cmdleten

Du kan använda `Invoke-ADSyncDiagnostics` cmdleten för att ta reda på varför inga lösen ord synkroniseras.

> [!NOTE]
> `Invoke-ADSyncDiagnostics`Cmdleten är endast tillgänglig för Azure AD Connect version 1.1.524.0 eller senare.

### <a name="run-the-diagnostics-cmdlet"></a>Kör diagnostik-cmdleten

Så här felsöker du problem där inga lösen ord synkroniseras:

1. Öppna en ny Windows PowerShell-session på din Azure AD Connect-Server med alternativet **Kör som administratör** .

2. Kör `Set-ExecutionPolicy RemoteSigned` eller `Set-ExecutionPolicy Unrestricted` .

3. Kör `Import-Module ADSyncDiagnostics`.

4. Kör `Invoke-ADSyncDiagnostics -PasswordSync`.



## <a name="one-object-is-not-synchronizing-passwords-troubleshoot-by-using-the-diagnostic-cmdlet"></a>Ett objekt synkroniserar inte lösen ord: Felsök med hjälp av diagnostik-cmdleten

Du kan använda `Invoke-ADSyncDiagnostics` cmdleten för att avgöra varför ett objekt inte synkroniserar lösen ord.

> [!NOTE]
> `Invoke-ADSyncDiagnostics`Cmdleten är endast tillgänglig för Azure AD Connect version 1.1.524.0 eller senare.

### <a name="run-the-diagnostics-cmdlet"></a>Kör diagnostik-cmdleten

Så här felsöker du problem där inga lösen ord synkroniseras för en användare:

1. Öppna en ny Windows PowerShell-session på din Azure AD Connect-Server med alternativet **Kör som administratör** .

2. Kör `Set-ExecutionPolicy RemoteSigned` eller `Set-ExecutionPolicy Unrestricted` .

3. Kör `Import-Module ADSyncDiagnostics`.

4. Kör följande cmdlet:

   ```
   Invoke-ADSyncDiagnostics -PasswordSync -ADConnectorName <Name-of-AD-Connector> -DistinguishedName <DistinguishedName-of-AD-object>
   ```

   Exempel:

   ```powershell
   Invoke-ADSyncDiagnostics -PasswordSync -ADConnectorName "contoso.com" -DistinguishedName "CN=TestUserCN=Users,DC=contoso,DC=com"
   ```



## <a name="no-passwords-are-synchronized-manual-troubleshooting-steps"></a>Inga lösen ord synkroniseras: manuella fel söknings steg

Följ dessa steg för att avgöra varför inga lösen ord synkroniseras:

1. Är anslutnings servern i [mellanlagrings läge](how-to-connect-sync-staging-server.md)? En server i mellanlagrings läge synkroniserar inte lösen ord.

2. Kör skriptet i avsnittet [Hämta status för inställningar för lösen ords synkronisering](#get-the-status-of-password-sync-settings) . Du får en översikt över konfigurationen av Lösenordssynkronisering.  

    ![PowerShell-skriptets utdata från inställningarna för lösen ords synkronisering](./media/tshoot-connect-password-hash-synchronization/psverifyconfig.png)  

3. Om funktionen inte är aktive rad i Azure AD eller om synkroniserings kanal status inte är aktive rad kör du guiden Anslut installationen. Välj **Anpassa synkroniseringsalternativ** och avmarkera Lösenordssynkronisering. Den här ändringen inaktiverar tillfälligt funktionen. Kör sedan guiden igen och aktivera synkronisering av lösen ord igen. Kör skriptet igen för att kontrol lera att konfigurationen är korrekt.

4. Sök efter fel i händelse loggen. Leta efter följande händelser, vilket tyder på ett problem:
    * Källa: "katalog synkronisering" ID: 0, 611, 652, 655 om du ser dessa händelser har du problem med anslutningen. Händelse logg meddelandet innehåller information om skogar där du har problem. Mer information finns i [anslutnings problem](#connectivity problem).

5. Om du inte ser något pulsslag eller om inget annat har fungerat, kan du köra [en fullständig synkronisering av alla lösen ord](#trigger-a-full-sync-of-all-passwords). Kör bara skriptet en gång.

6. Se avsnittet Felsök ett objekt som inte synkroniserar lösen ord.

### <a name="connectivity-problems"></a>Anslutnings problem

Har du anslutning till Azure AD?

Har kontot behörighet att läsa lösen ordets hash-värden i alla domäner? Om du har installerat Anslut med hjälp av Express inställningar bör behörigheterna redan vara korrekta. 

Om du har använt anpassad installation anger du behörigheterna manuellt genom att göra följande:
    
1. Starta **Synchronization Service Manager** för att hitta kontot som används av Active Directory-anslutningen. 
 
2. Gå till **anslutningar** och Sök sedan efter den lokala Active Directorys skogen som du felsöker. 
 
3. Välj kopplingen och klicka sedan på **Egenskaper**. 
 
4. Gå till **Anslut till Active Directory skog**.  
    
    ![Konto som används av Active Directory Connector](./media/tshoot-connect-password-hash-synchronization/connectoraccount.png)  
    Anteckna användar namnet och domänen där kontot finns.
    
5. Starta **Active Directory användare och datorer** och kontrol lera att det konto som du har hittat tidigare har följande behörigheter som angetts i roten för alla domäner i skogen:
    * Replikera katalog ändringar
    * Replikera katalog ändringar alla

6. Kan domän kontrol Lanterna uppnås genom att Azure AD Connect? Om anslutnings servern inte kan ansluta till alla domänkontrollanter, konfigurerar du **Använd endast prioriterad domänkontrollant**.  
    
    ![Domänkontrollant som används av Active Directory anslutning](./media/tshoot-connect-password-hash-synchronization/preferreddc.png)  

7. Gå tillbaka till **Synchronization Service Manager** och **Konfigurera katalogpartitionen**. 
 
8. Välj din domän i **Välj katalogpartitioner**, markera kryss rutan **Använd bara önskade domänkontrollanter** och klicka sedan på **Konfigurera**. 

9. I listan anger du de domänkontrollanter som ansluter ska användas för synkronisering av lösen ord. Samma lista används även för import och export. Utför de här stegen för alla dina domäner.

> [!NOTE]
> Om du vill tillämpa ändringarna startar du om tjänsten **Microsoft Azure AD Sync** (ADSync).

10. Om skriptet visar att det inte finns något pulsslag kör du skriptet i [utlösa en fullständig synkronisering av alla lösen ord](#trigger-a-full-sync-of-all-passwords).

## <a name="one-object-is-not-synchronizing-passwords-manual-troubleshooting-steps"></a>Ett objekt synkroniserar inte lösen ord: manuella fel söknings steg

Du kan enkelt Felsöka problem med hash-synkronisering av lösen ord genom att granska status för ett objekt.

1. I **Active Directory användare och datorer** söker du efter användaren och kontrollerar sedan att kryss rutan **användaren måste byta lösen ord vid nästa inloggning** är avmarkerad.  

    ![Active Directory produktiva lösen ord](./media/tshoot-connect-password-hash-synchronization/adprodpassword.png)  

    Om kryss rutan är markerad ber du användaren att logga in och ändra lösen ordet. Tillfälliga lösen ord synkroniseras inte med Azure AD.

2. Om lösen ordet ser korrekt ut i Active Directory ska du följa användaren i Synkroniseringsmotorn. Genom att följa användaren från den lokala Active Directory till Azure AD kan du se om det finns ett beskrivande fel på objektet.

    a. Starta [Synchronization Service Manager](how-to-connect-sync-service-manager-ui.md).

    b. Klicka på **kopplingar**.

    c. Välj den **Active Directorys anslutning** där användaren finns.

    d. Välj **search Connector-utrymme**.

    e. I rutan **omfattning** väljer du **DN eller ankare** och anger sedan det fullständiga DN för den användare som du felsöker.

    ![Sök efter användare i anslutnings utrymme med DN](./media/tshoot-connect-password-hash-synchronization/searchcs.png)  

    f. Leta upp den användare du söker och klicka sedan på **Egenskaper** för att visa alla attribut. Om användaren inte är med i Sök resultatet kontrollerar du [filtrerings reglerna](how-to-connect-sync-configure-filtering.md) och kontrollerar att du kör [tillämpa och verifiera ändringar](how-to-connect-sync-configure-filtering.md#apply-and-verify-changes) för användaren som ska visas i Anslut.

    ex. Klicka på **logg** om du vill se information om lösen ords synkronisering för objektet under den senaste veckan.  

    ![Objekt logg information](./media/tshoot-connect-password-hash-synchronization/csobjectlog.png)  

    Om objekt loggen är tom har Azure AD Connect inte kunnat läsa lösen ordets hash-värde från Active Directory. Fortsätt med fel sökningen med anslutnings fel. Om du ser något annat värde än **slutfört**, referera till tabellen i [loggen för Lösenordssynkronisering](#password-sync-log).

    h. Välj fliken **härkomst** och se till att minst en Synkroniseringsregel i kolumnen **PasswordSync** är **True**. I standard konfigurationen finns namnet på synkroniseringsregeln **i från AD-User AccountEnabled**.  

    ![Härkomst information om en användare](./media/tshoot-connect-password-hash-synchronization/cspasswordsync.png)  

    i. Klicka på **metaversum objekt egenskaper** för att visa en lista över användarattribut.  

    ![Skärm bild som visar en lista med användarattribut för metaversum-objektets egenskaper.](./media/tshoot-connect-password-hash-synchronization/mvpasswordsync.png)  

    Kontrol lera att det inte finns något **cloudFiltered** -attribut. Se till att domänautentiseringsuppgifter (domainFQDN och domainNetBios) har de förväntade värdena.

    j. Klicka på fliken **anslutningar** . Se till att du ser anslutningar till både lokala Active Directory och Azure AD.

    ![Metaversum-information](./media/tshoot-connect-password-hash-synchronization/mvconnectors.png)  

    k. Välj den rad som representerar Azure AD, klicka på **Egenskaper** och klicka sedan på fliken **härkomst** . Objektet för anslutnings utrymmet måste ha en utgående regel i kolumnen **PasswordSync** inställd på **True**. I standard konfigurationen är namnet på synkroniseringsregeln **till AAD-User Join**.  

    ![Dialog rutan objekt egenskaper för kopplings utrymme](./media/tshoot-connect-password-hash-synchronization/cspasswordsync2.png)  

### <a name="password-sync-log"></a>Logg för lösen ords synkronisering

Status kolumnen kan ha följande värden:

| Status | Beskrivning |
| --- | --- |
| Klart |Lösen ordet har synkroniserats. |
| FilteredByTarget |Lösen ordet har angetts till **användaren måste byta lösen ord vid nästa inloggning**. Lösen ordet har inte synkroniserats. |
| NoTargetConnection |Inget objekt i metaversum eller i Azure AD Connector-utrymmet. |
| SourceConnectorNotPresent |Det finns inget objekt i det lokala Active Directory anslutnings utrymmet. |
| TargetNotExportedToDirectory |Objektet i Azure AD Connector-utrymmet har inte exporter ATS än. |
| MigratedCheckDetailsForMoreInfo |Logg posten skapades innan 1.0.9125.0 byggdes och visas i sitt äldre tillstånd. |
| Fel |Tjänsten returnerade ett okänt fel. |
| Okänt |Ett fel uppstod vid försök att bearbeta en batch med lösen ords-hashar.  |
| MissingAttribute |Vissa attribut (till exempel Kerberos-hash) som krävs av Azure AD Domain Services är inte tillgängliga. |
| RetryRequestedByTarget |Vissa attribut (till exempel Kerberos-hash) som krävs av Azure AD Domain Services var inte tillgängliga tidigare. Ett försök att synkronisera om användarens lösen ords-hash görs. |

## <a name="scripts-to-help-troubleshooting"></a>Skript som hjälper dig att felsöka

### <a name="get-the-status-of-password-sync-settings"></a>Hämta status för inställningar för lösen ords synkronisering

```powershell
Import-Module ADSync
$connectors = Get-ADSyncConnector
$aadConnectors = $connectors | Where-Object {$_.SubType -eq "Windows Azure Active Directory (Microsoft)"}
$adConnectors = $connectors | Where-Object {$_.ConnectorTypeName -eq "AD"}
if ($aadConnectors -ne $null -and $adConnectors -ne $null)
{
    if ($aadConnectors.Count -eq 1)
    {
        $features = Get-ADSyncAADCompanyFeature
        Write-Host
        Write-Host "Password sync feature enabled in your Azure AD directory: "  $features.PasswordHashSync
        foreach ($adConnector in $adConnectors)
        {
            Write-Host
            Write-Host "Password sync channel status BEGIN ------------------------------------------------------- "
            Write-Host
            Get-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector.Name
            Write-Host
            $pingEvents =
                Get-EventLog -LogName "Application" -Source "Directory Synchronization" -InstanceId 654  -After (Get-Date).AddHours(-3) |
                    Where-Object { $_.Message.ToUpperInvariant().Contains($adConnector.Identifier.ToString("D").ToUpperInvariant()) } |
                    Sort-Object { $_.Time } -Descending
            if ($pingEvents -ne $null)
            {
                Write-Host "Latest heart beat event (within last 3 hours). Time " $pingEvents[0].TimeWritten
            }
            else
            {
                Write-Warning "No ping event found within last 3 hours."
            }
            Write-Host
            Write-Host "Password sync channel status END ------------------------------------------------------- "
            Write-Host
        }
    }
    else
    {
        Write-Warning "More than one Azure AD Connectors found. Please update the script to use the appropriate Connector."
    }
}
Write-Host
if ($aadConnectors -eq $null)
{
    Write-Warning "No Azure AD Connector was found."
}
if ($adConnectors -eq $null)
{
    Write-Warning "No AD DS Connector was found."
}
Write-Host
```

#### <a name="trigger-a-full-sync-of-all-passwords"></a>Utlös en fullständig synkronisering av alla lösen ord

> [!NOTE]
> Kör bara det här skriptet en gång. Om du behöver köra det mer än en gång är det något annat som är problemet. Kontakta Microsoft-supporten för att felsöka problemet.

Du kan utlösa en fullständig synkronisering av alla lösen ord med hjälp av följande skript:

```powershell
$adConnector = "<CASE SENSITIVE AD CONNECTOR NAME>"
$aadConnector = "<CASE SENSITIVE AAD CONNECTOR NAME>"
Import-Module adsync
$c = Get-ADSyncConnector -Name $adConnector
$p = New-Object Microsoft.IdentityManagement.PowerShell.ObjectModel.ConfigurationParameter "Microsoft.Synchronize.ForceFullPasswordSync", String, ConnectorGlobal, $null, $null, $null
$p.Value = 1
$c.GlobalParameters.Remove($p.Name)
$c.GlobalParameters.Add($p)
$c = Add-ADSyncConnector -Connector $c
Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $aadConnector -Enable $false
Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $aadConnector -Enable $true
```

## <a name="next-steps"></a>Nästa steg

* [Implementera hash-synkronisering av lösen ord med Azure AD Connect Sync](how-to-connect-password-hash-synchronization.md)
* [Azure AD Connect synkronisering: Anpassa synkroniseringsalternativ](how-to-connect-sync-whatis.md)
* [Integrera dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md)
