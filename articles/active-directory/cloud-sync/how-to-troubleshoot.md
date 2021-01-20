---
title: Azure AD Connect fel sökning av Cloud Sync
description: Den här artikeln beskriver hur du felsöker problem som kan uppstå med moln etablerings agenten.
author: billmath
ms.author: billmath
manager: daveba
ms.date: 01/19/2021
ms.topic: how-to
ms.prod: windows-server-threshold
ms.technology: identity-adfs
ms.openlocfilehash: 33af92c1987d9cd0c88e689dd7bafccadd60cb06
ms.sourcegitcommit: 8a74ab1beba4522367aef8cb39c92c1147d5ec13
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/20/2021
ms.locfileid: "98614058"
---
# <a name="cloud-sync-troubleshooting"></a>Fel sökning av Cloud Sync

Cloud Sync vidrör många olika saker och har många olika beroenden. Detta breda omfång kan ge upphov till olika problem. Den här artikeln hjälper dig att felsöka problemen. Den introducerar typiska områden som du kan fokusera på, hur du samlar in ytterligare information och de olika tekniker som du kan använda för att spåra problem.


## <a name="common-troubleshooting-areas"></a>Vanliga fel söknings områden

|Name|Beskrivning|
|-----|-----|
|[Agent problem](#agent-problems)|Kontrol lera att agenten har installerats korrekt och att den kommunicerar med Azure Active Directory (Azure AD).|
|[Problem med synkronisering av objekt](#object-synchronization-problems)|Använd etablerings loggar för att felsöka problem med synkronisering av objekt.|
|[Etablering av problem i karantän](#provisioning-quarantined-problems)|Förstå hur du kan åtgärda karantän problem och åtgärda dem.|


## <a name="agent-problems"></a>Agent problem
Några av de första saker som du vill verifiera med agenten är:

-  Är det installerat?
-  Körs agenten lokalt?
-  Är agenten i portalen?
-  Har agenten marker ATS som felfri?

Dessa objekt kan verifieras i Azure Portal och på den lokala server som kör-agenten.

### <a name="azure-portal-agent-verification"></a>Azure Portal agent verifiering

Följ dessa steg för att kontrol lera att agenten visas av Azure och är felfri.

1. Logga in på Azure-portalen.
1. Välj **Azure Active Directory**  >  **Azure AD Connect** till vänster. I mitten väljer du **hantera synkronisering**.
1. På skärmen **Azure AD Connect Cloud Sync** väljer du **Granska alla agenter**.

   ![Granska alla agenter](media/how-to-install/install-7.png)</br>
 
1. På skärmen **lokala etablerings agenter** visas de agenter som du har installerat. Kontrol lera att agenten i fråga finns där och har marker ATS som *felfri*.

   ![Skärmen lokala etablerings agenter](media/how-to-install/install-8.png)</br>

### <a name="verify-the-port"></a>Verifiera porten

Kontrol lera att Azure lyssnar på port 443 och att agenten kan kommunicera med den. 

Det här testet kontrollerar att dina agenter kan kommunicera med Azure via port 443. Öppna en webbläsare och gå till föregående URL från servern där agenten är installerad.

![Verifiering av portens tillgänglighet](media/how-to-install/verify-2.png)

### <a name="on-the-local-server"></a>På den lokala servern

Kontrol lera att agenten körs genom att följa dessa steg.

1. På servern där agenten är installerad öppnar du **tjänster** genom att antingen navigera till den eller **Starta**  >  **köra**  >  **Services. msc**.
1. Under **tjänster** kontrollerar du att **Microsoft Azure AD ansluter agent uppdaterings** **agenten och Microsoft Azure AD ansluta etablerings agenten** är där och att deras status är *igång*.

   ![Sidan tjänster](media/how-to-troubleshoot/troubleshoot-1.png)

### <a name="common-agent-installation-problems"></a>Vanliga problem med agent installation

I följande avsnitt beskrivs några vanliga problem med agent installation och vanliga lösningar.

#### <a name="agent-failed-to-start"></a>Det gick inte att starta agenten

Du kan få ett fel meddelande som säger:

**Det gick inte att starta tjänsten Microsoft Azure AD ansluta etablerings agenten. Kontrol lera att du har behörighet att starta system tjänsterna.** 

Det här problemet orsakas vanligt vis av en grup princip som förhindrade att behörigheter appliceras på det lokala inloggnings kontot för NT-tjänst som skapats av installations programmet (NT SERVICE\AADConnectProvisioningAgent). De här behörigheterna krävs för att starta tjänsten.

Följ dessa steg för att lösa problemet.

1. Logga in på servern med ett administratörs konto.
1. Öppna **tjänster** genom att antingen navigera till den eller genom att **Starta**  >  **köra**  >  **Services. msc**.
1. Under **tjänster** dubbelklickar du på **Microsoft Azure AD ansluta etablerings agent**.
1. På fliken **Logga in** ändrar du **det här kontot** till en domän administratör. Starta sedan om tjänsten. 

   ![Fliken Logga in](media/how-to-troubleshoot/troubleshoot-3.png)

#### <a name="agent-times-out-or-certificate-is-invalid"></a>Agentens tids gräns eller certifikat är ogiltigt

Du kan få följande fel meddelande när du försöker registrera agenten.

![Fel meddelande vid timeout](media/how-to-troubleshoot/troubleshoot-4.png)

Det här problemet orsakas normalt av att agenten inte kan ansluta till hybrididentitetstjänsten och kräver att du konfigurerar en HTTP-proxy. Du kan lösa problemet genom att konfigurera en utgående proxy. 

Etablerings agenten stöder användning av en utgående proxy. Du kan konfigurera den genom att redigera agentens konfigurations fil *C:\Program Files\Microsoft Azure AD Connect etablerings Agent\AADConnectProvisioningAgent.exe.config*. Lägg till följande rader i den i slutet av filen precis innan den avslutande `</configuration>` taggen.
Ersätt variablerna `[proxy-server]` och `[proxy-port]` med proxyserverns namn och port värden.

```xml
    <system.net>
        <defaultProxy enabled="true" useDefaultCredentials="true">
            <proxy
                usesystemdefault="true"
                proxyaddress="http://[proxy-server]:[proxy-port]"
                bypassonlocal="true"
            />
        </defaultProxy>
    </system.net>
```

#### <a name="agent-registration-fails-with-security-error"></a>Agent registreringen Miss lyckas med ett säkerhets fel

Du kan få ett fel meddelande när du installerar Cloud Provisioning-agenten.

Det här problemet orsakas vanligt vis av att agenten inte kan köra PowerShell-registrerings skripten på grund av lokala PowerShell-körnings principer.

Lös problemet genom att ändra körnings principerna för PowerShell på-servern. Du måste ha dator-och användar principer inställda som *odefinierade* eller *RemoteSigned*. Om de är inställda som *obegränsade* visas det här felet. Mer information finns i [körnings principer för PowerShell](/powershell/module/microsoft.powershell.core/about/about_execution_policies?view=powershell-6). 

### <a name="log-files"></a>Loggfiler

Som standard avger agenten minimala felmeddelanden och stackspårningsinformation. Du hittar dessa spårnings loggar i mappen **C:\PROGRAMDATA\MICROSOFT\AZURE AD Connect-etablering Agent\Trace**.

Följ dessa steg om du vill samla in ytterligare information om fel sökning av Agent-relaterade problem.

1.  Installera AADCloudSyncTools PowerShell-modulen enligt beskrivningen [här](reference-powershell.md#install-the-aadcloudsynctools-powershell-module).
2. Använd `Export-AADCloudSyncToolsLogs` PowerShell-cmdleten för att samla in informationen.  Du kan använda följande växlar för att finjustera din data insamling.
      - SkipVerboseTrace för att endast exportera aktuella loggar utan att samla in utförliga loggar (standard = falskt)
      - TracingDurationMins för att ange en annan hämtnings tid (standard = 3 minuter)
      - OutputPath för att ange en annan sökväg för utdata (standard = användarens dokument)


## <a name="object-synchronization-problems"></a>Problem med synkronisering av objekt

Följande avsnitt innehåller information om fel sökning av objekt synkronisering.

### <a name="provisioning-logs"></a>Etableringsloggar

I Azure Portal kan etablerings loggar användas för att spåra och felsöka problem med synkronisering av objekt. Om du vill visa loggarna väljer du **loggar**.

![Knappen loggar](media/how-to-troubleshoot/log-1.png)

Med etablerings loggar får du en mängd information om status för de objekt som synkroniseras mellan din lokala Active Directorys miljö och Azure.

![Skärmen etablering av loggar](media/how-to-troubleshoot/log-2.png)

Du kan använda List rutorna överst på sidan för att filtrera vyn till noll i vid vissa problem, t. ex. datum. Dubbelklicka på en enskild händelse om du vill se ytterligare information.

![List Rute information för etablerings loggar](media/how-to-troubleshoot/log-3.png)

Den här informationen innehåller detaljerade steg och var synkroniseringsproblem inträffar. På så sätt kan du hitta den exakta punkten för problemet.


## <a name="provisioning-quarantined-problems"></a>Etablering av problem i karantän

Cloud Sync övervakar hälso tillståndet för din konfiguration och placerar Felaktiga objekt i ett karantäns tillstånd. Om de flesta eller alla anrop som görs mot mål systemet inte fungerar konsekvent på grund av ett fel, till exempel ogiltiga administratörsautentiseringsuppgifter, markeras synkroniseringsschemat som i karantän.

![Karantänstatus](media/how-to-troubleshoot/quarantine-1.png)

Genom att välja status kan du se ytterligare information om karantänen. Du kan också hämta fel koden och meddelandet.

![Information om karantän status](media/how-to-troubleshoot/quarantine-2.png)

Genom att högerklicka på statusen visas fler alternativ:
    
   - Visa etablerings loggar
   - Visa agent
   - Rensa karantän

![Information om karantän status](media/how-to-troubleshoot/quarantine-4.png)


### <a name="resolve-a-quarantine"></a>Lösa en karantän
Det finns två olika sätt att lösa en karantän.  De är:

  - Rensa karantän – rensar vattenstämpeln och kör en Delta-synkronisering
  - Starta om etablerings jobbet – rensar vattenstämpeln och kör en inledande synkronisering

#### <a name="clear-quarantine"></a>Rensa karantän
Om du vill rensa vattenstämpeln och köra en Delta-synkronisering på etablerings jobbet när du har verifierat det, högerklickar du bara på status och väljer **Rensa karantän**.

Du bör se ett meddelande om att karantänen har rensats.

![Information om karantän status](media/how-to-troubleshoot/quarantine-5.png)

Sedan bör du se statusen på agenten som felfri.

![Information om karantän status](media/how-to-troubleshoot/quarantine-6.png)

#### <a name="restart-the-provisioning-job"></a>Starta om etablerings jobbet
Använd Azure Portal för att starta om etablerings jobbet. På sidan agent konfiguration väljer du **starta om etablering**.

  ![Starta om etablering](media/how-to-troubleshoot/quarantine-3.png)

- Använd Microsoft Graph för att [starta om etablerings jobbet](/graph/api/synchronization-synchronizationjob-restart?tabs=http&view=graph-rest-beta). Du har fullständig kontroll över vad du vill starta om. Du kan välja att rensa:
  - Escrows för att starta om depositions-räknaren som påförs till karantäns status.
  - Karantän för att ta bort programmet från karantänen.
  - Vattenstämplar. 
  
  Använd följande begäran:
 
  `POST /servicePrincipals/{id}/synchronization/jobs/{jobId}/restart`

## <a name="repairing-the-the-cloud-sync-service-account"></a>Reparera kontot för Cloud Sync-tjänsten
Om du behöver reparera Cloud Sync Service-kontot kan du använda `Repair-AADCloudSyncToolsAccount` .  


   1.  Använd de installations steg som beskrivs [här](reference-powershell.md#install-the-aadcloudsynctools-powershell-module) för att börja och fortsätt sedan med återstående steg.
   2.  Från en Windows PowerShell-session med administratörs behörighet skriver eller kopierar och klistrar du in följande: 
    ```
    Connect-AADCloudSyncTools
    ```  
   3. Ange dina autentiseringsuppgifter för Global Azure AD-administratör
   4. Skriv eller kopiera och klistra in följande: 
    ```
    Repair-AADCloudSyncToolsAccount
    ```  
   5. När detta är klart bör det stå att kontot har reparerats.

## <a name="next-steps"></a>Nästa steg 

- [Kända begränsningar](how-to-prerequisites.md#known-limitations)
- [Felkoder](reference-error-codes.md)
