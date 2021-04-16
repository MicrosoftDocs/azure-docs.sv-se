---
title: Azure AD Connect felsökning av molnsynkronisering
description: Den här artikeln beskriver hur du felsöker problem som kan uppstå med molnetableringsagenten.
author: billmath
ms.author: billmath
manager: daveba
ms.date: 01/19/2021
ms.topic: how-to
ms.prod: windows-server-threshold
ms.technology: identity-adfs
ms.openlocfilehash: 65022d98c7ee7e90d8f1fe5b6854605c841ad05b
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107530309"
---
# <a name="cloud-sync-troubleshooting"></a>Felsökning av molnsynkronisering

Cloud Sync har kontakt med många olika saker och har många olika beroenden. Det breda omfånget kan ge upphov till olika problem. Den här artikeln hjälper dig att felsöka dessa problem. Där introduceras några vanliga områden att fokusera på, hur du samlar in ytterligare information och de olika tekniker du kan använda till att spåra problem.


## <a name="common-troubleshooting-areas"></a>Vanliga felsökningsområden

|Name|Beskrivning|
|-----|-----|
|[Agentproblem](#agent-problems)|Kontrollera att agenten har installerats korrekt och att den kommunicerar med Azure Active Directory (Azure AD).|
|[Problem med objektsynkronisering](#object-synchronization-problems)|Använd etableringsloggar för att felsöka problem med objektsynkronisering.|
|[Problem med etablering i karantän](#provisioning-quarantined-problems)|Förstå etableringsproblem med karantän och hur du åtgärdar dem.|


## <a name="agent-problems"></a>Agentproblem
Några av de första sakerna som du vill verifiera med agenten är:

-  Är det installerat?
-  Körs agenten lokalt?
-  Finns agenten i portalen?
-  Är agenten markerad som felfri?

Dessa objekt kan verifieras i Azure Portal och på den lokala server som kör agenten.

### <a name="azure-portal-agent-verification"></a>Azure Portal agentverifiering

Följ dessa steg för att kontrollera att agenten visas i Azure och är felfri.

1. Logga in på Azure-portalen.
1. Till vänster väljer du **Azure Active Directory**  >  **Azure AD Connect**. I mitten väljer du **Hantera synkronisering.**
1. På skärmen **Azure AD Connect molnsynkronisering** väljer du **Granska alla agenter.**

   ![Granska alla agenter](media/how-to-install/install-7.png)</br>
 
1. På skärmen **Lokala etableringsagenter** visas de agenter som du har installerat. Kontrollera att agenten i fråga finns där och är markerad som *Felfri.*

   ![Skärmen Lokala etableringsagenter](media/how-to-install/install-8.png)</br>

### <a name="verify-the-port"></a>Kontrollera porten

Kontrollera att Azure lyssnar på port 443 och att din agent kan kommunicera med den. 

Det här testet verifierar att dina agenter kan kommunicera med Azure via port 443. Öppna en webbläsare och gå till den tidigare URL:en från servern där agenten är installerad.

![Verifiering av port nåbarhet](media/how-to-install/verify-2.png)

### <a name="on-the-local-server"></a>På den lokala servern

Följ dessa steg om du vill kontrollera att agenten körs.

1. På servern där agenten är installerad öppnar du **Tjänster** genom att antingen navigera till den eller genom att gå **till Starta**  >    >  **Kör Services.msc**.
1. Under **Tjänster** kontrollerar du **att Microsoft Azure AD Connect Agent Updater** och Microsoft Azure AD Connect **Provisioning Agent** finns där och att deras status är *Körs.*

   ![Skärmen Tjänster](media/how-to-troubleshoot/troubleshoot-1.png)

### <a name="common-agent-installation-problems"></a>Vanliga problem med agentinstallation

I följande avsnitt beskrivs några vanliga problem med agentinstallation och vanliga lösningar.

#### <a name="agent-failed-to-start"></a>Agenten kunde inte starta

Du kan få ett felmeddelande som säger:

**Det gick inte Microsoft Azure AD att starta tjänsten "Microsoft Azure AD Connect Provisioning Agent". Kontrollera att du har tillräcklig behörighet för att starta systemtjänsterna.** 

Det här problemet orsakas vanligtvis av en grupprincip som förhindrar att behörigheter tillämpas på det lokala INLOGGNINGskontot för NT-tjänsten som skapats av installationsprogrammet (NT SERVICE\AADConnectProvisioningAgent). De här behörigheterna krävs för att starta tjänsten.

Följ dessa steg för att lösa problemet.

1. Logga in på servern med ett administratörskonto.
1. Öppna **Tjänster** genom att antingen navigera till den eller genom att **gå till Starta**  >  **Run**  >  **Services.msc**.
1. Under **Tjänster** dubbelklickar du på **Microsoft Azure AD Anslut etableringsagenten**.
1. På fliken **Logga in** ändrar du Det här **kontot till** en domänadministratör. Starta sedan om tjänsten. 

   ![Fliken Logga in](media/how-to-troubleshoot/troubleshoot-3.png)

#### <a name="agent-times-out-or-certificate-is-invalid"></a>Agentens tidsfördring eller certifikatet är ogiltigt

Du kan få följande felmeddelande när du försöker registrera agenten.

![Felmeddelande om time out](media/how-to-troubleshoot/troubleshoot-4.png)

Det här problemet orsakas normalt av att agenten inte kan ansluta till hybrididentitetstjänsten och kräver att du konfigurerar en HTTP-proxy. Du kan lösa problemet genom att konfigurera en utgående proxy. 

Etableringsagenten stöder användning av en utgående proxy. Du kan konfigurera den genom att redigera agentkonfigurationsfilen *C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\AADConnectProvisioningAgent.exe.config*. Lägg till följande rader i den mot slutet av filen precis före den avslutande `</configuration>` taggen.
Ersätt variablerna `[proxy-server]` och med `[proxy-port]` proxyserverns namn och portvärden.

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

#### <a name="agent-registration-fails-with-security-error"></a>Agentregistreringen misslyckas med säkerhetsfel

Du kan få ett felmeddelande när du installerar molnetableringsagenten.

Det här problemet beror vanligtvis på att agenten inte kan köra PowerShell-registreringsskripten på grund av lokala PowerShell-körningsprinciper.

Lös problemet genom att ändra PowerShell-körningsprinciperna på servern. Du måste ha dator- och användarprinciper inställda som *Undefined* eller *RemoteSigned*. Om de är inställda *på Obegränsad visas* det här felet. Mer information finns i [PowerShell-körningsprinciper.](/powershell/module/microsoft.powershell.core/about/about_execution_policies) 

### <a name="log-files"></a>Loggfiler

Som standard avger agenten minimala felmeddelanden och stackspårningsinformation. Du hittar dessa spårningsloggar i mappen **C:\ProgramData\Microsoft\Azure AD Connect Provisioning Agent\Trace**.

Följ dessa steg om du vill samla in ytterligare information om felsökning av agentrelaterade problem.

1.  Installera PowerShell-modulen AADCloudSyncTools enligt beskrivningen [här.](reference-powershell.md#install-the-aadcloudsynctools-powershell-module)
2. Använd `Export-AADCloudSyncToolsLogs` PowerShell-cmdleten för att samla in informationen.  Du kan finjustera datainsamlingen med hjälp av följande växlar.
      - SkipVerboseTrace för att endast exportera aktuella loggar utan att samla in utförliga loggar (standard = falskt)
      - TracingDurationMins för att ange en annan avskiljningstid (standard = 3 minuter)
      - OutputPath för att ange en annan utdatasökväg (standard = Användarens dokument)


## <a name="object-synchronization-problems"></a>Problem med objektsynkronisering

Följande avsnitt innehåller information om felsökning av objektsynkronisering.

### <a name="provisioning-logs"></a>Etableringsloggar

I Azure Portal kan du använda etableringsloggar för att spåra och felsöka problem med objektsynkronisering. Om du vill visa loggarna väljer du **Loggar**.

![Knappen Loggar](media/how-to-troubleshoot/log-1.png)

Etableringsloggar ger en mängd information om tillståndet för de objekt som synkroniseras mellan din lokal Active Directory och Azure.

![Skärmen Etableringsloggar](media/how-to-troubleshoot/log-2.png)

Du kan använda listrutorna längst upp på sidan för att filtrera vyn till noll i vid specifika problem, till exempel datum. Dubbelklicka på en enskild händelse om du vill visa mer information.

![Information i listrutan Etableringsloggar](media/how-to-troubleshoot/log-3.png)

Den här informationen innehåller detaljerade steg och var synkroniseringsproblemet uppstår. På så sätt kan du hitta den exakta platsen för problemet.


## <a name="provisioning-quarantined-problems"></a>Problem med etablering i karantän

Molnsynkronisering övervakar hälsotillståndet för konfigurationen och placerar felaktiga objekt i karantäntillstånd. Om de flesta eller alla anrop som görs mot målsystemet konsekvent misslyckas på grund av ett fel, till exempel ogiltiga administratörsautentiseringsuppgifter, markeras synkroniseringsjobbet som i karantän.

![Karantänstatus](media/how-to-troubleshoot/quarantine-1.png)

Genom att välja status kan du se ytterligare information om karantänen. Du kan också hämta felkoden och meddelandet.

![Skärmbild som visar ytterligare information om karantänen.](media/how-to-troubleshoot/quarantine-2.png)

Om du högerklickar på statusen visas ytterligare alternativ:
    
   - visa etableringsloggar
   - visa agent
   - rensa karantän

![Skärmbild som visar alternativen för snabbmenyn.](media/how-to-troubleshoot/quarantine-4.png)


### <a name="resolve-a-quarantine"></a>Lösa en karantän
Det finns två olika sätt att lösa en karantän.  De är:

  - rensa karantän – rensar vattenstämpeln och kör en deltasynkronisering
  - starta om etableringsjobbet – rensar vattenstämpeln och kör en inledande synkronisering

#### <a name="clear-quarantine"></a>Rensa karantän
Om du vill rensa vattenstämpeln och köra en deltasynkronisering på etableringsjobbet när du har verifierat den högerklickar du bara på statusen och väljer **rensa karantänen.**

Du bör se ett meddelande om att karantänen rensas.

![Skärmbild som visar meddelandet om att karantänen rensas.](media/how-to-troubleshoot/quarantine-5.png)

Sedan bör du se statusen på din agent som felfri.

![Information om karantänstatus](media/how-to-troubleshoot/quarantine-6.png)

#### <a name="restart-the-provisioning-job"></a>Starta om etableringsjobbet
Använd Azure Portal för att starta om etableringsjobbet. På agentkonfigurationssidan väljer du **Starta om etableringen.**

  ![Starta om etableringen](media/how-to-troubleshoot/quarantine-3.png)

- Använd Microsoft Graph för [att starta om etableringsjobbet](/graph/api/synchronization-synchronizationjob-restart?tabs=http&view=graph-rest-beta&preserve-view=true). Du har fullständig kontroll över vad du startar om. Du kan välja att rensa:
  - Depositioner för att starta om depositionsräknaren som ackumuleras mot karantänstatus.
  - Sätt programmet i karantän för att ta bort programmet från karantänen.
  - Vattenstämplar. 
  
  Använd följande begäran:
 
  `POST /servicePrincipals/{id}/synchronization/jobs/{jobId}/restart`

## <a name="repairing-the-the-cloud-sync-service-account"></a>Reparera Cloud Sync-tjänstkontot
Om du behöver reparera tjänstkontot för molnsynkronisering kan du använda `Repair-AADCloudSyncToolsAccount` .  


   1.  Använd installationsstegen som beskrivs [här](reference-powershell.md#install-the-aadcloudsynctools-powershell-module) för att börja och sedan fortsätta med de återstående stegen.
   2.  Från en Windows PowerShell-session med administratörsbehörighet skriver eller kopierar du och klistrar in följande: 
    ```
    Connect-AADCloudSyncTools
    ```  
   3. Ange dina autentiseringsuppgifter som global Azure AD-administratör
   4. Skriv eller kopiera och klistra in följande: 
    ```
    Repair-AADCloudSyncToolsAccount
    ```  
   5. När det är klart bör det står att kontot har reparerats.

## <a name="next-steps"></a>Nästa steg 

- [Kända begränsningar](how-to-prerequisites.md#known-limitations)
- [Felkoder](reference-error-codes.md)
