---
title: Felsök problem med konfigurations servern vid haveri beredskap för virtuella VMware-datorer och fysiska servrar till Azure med hjälp av Azure Site Recovery | Microsoft Docs
description: Den här artikeln innehåller felsöknings information för att distribuera konfigurations servern för haveri beredskap för virtuella VMware-datorer och fysiska servrar till Azure med hjälp av Azure Site Recovery.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 02/13/2019
ms.author: ramamill
ms.openlocfilehash: b5fd014732fd4cdfaa52f971b5e4d2c74db580d2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92371961"
---
# <a name="troubleshoot-configuration-server-issues"></a>Felsöka konfigurationsserverfel

Den här artikeln hjälper dig att felsöka problem när du distribuerar och hanterar [Azure Site Recovery](site-recovery-overview.md) konfigurations servern. Konfigurations servern fungerar som en hanterings Server. Använd konfigurations servern för att konfigurera haveri beredskap för lokala virtuella VMware-datorer och fysiska servrar till Azure med hjälp av Site Recovery. I följande avsnitt beskrivs de vanligaste felen som kan uppstå när du lägger till en ny konfigurations Server och när du hanterar en konfigurations Server.

## <a name="registration-failures"></a>Registreringsfel

Käll datorn registreras med konfigurations servern när du installerar mobilitets agenten. Du kan felsöka eventuella fel under det här steget genom att följa dessa rikt linjer:

1. Öppna filen C:\ProgramData\ASR\home\svsystems\var\ configurator_register_host_static_info. log. (Mappen program data kan vara en dold mapp. Om du inte ser mappen program data går du till fliken **Visa** i avsnittet **Visa/Dölj** och markerar kryss rutan **dolda objekt** i Utforskaren.) Fel kan bero på flera problem.

2. Sök efter strängen **ingen giltig IP-adress hittades**. Om strängen hittas:
   1. Kontrol lera att det begärda värd-ID: t är detsamma som käll datorns värd-ID.
   2. Kontrol lera att käll datorn har minst en IP-adress tilldelad till det fysiska NÄTVERKSKORTet. För att agent registreringen med konfigurations servern ska lyckas måste käll datorn ha minst en giltig IP v4-adress tilldelad till det fysiska NÄTVERKSKORTet.
   3. Kör något av följande kommandon på käll datorn för att hämta alla IP-adresser för käll datorn:
      - För Windows: `> ipconfig /all`
      - För Linux: `# ifconfig -a`

3. Om strängen **ingen giltig IP-adress** hittades söker du efter sträng **Orsaken =>null**. Det här felet uppstår om käll datorn använder en tom värd för att registrera med konfigurations servern. Om strängen hittas:
    - När du har löst problemen följer du rikt linjerna i [Registrera käll datorn med konfigurations servern](vmware-azure-troubleshoot-configuration-server.md#register-source-machine-with-configuration-server) för att försöka registrera manuellt.

4. Om sträng **Orsaken =>null** inte hittas går du till käll datorn och öppnar C:\ProgramData\ASRSetupLogs\UploadedLogs\ASRUnifiedAgentInstaller.log-filen. (Mappen program data kan vara en dold mapp. Om du inte ser mappen program data går du till fliken **Visa** i avsnittet **Visa/Dölj** och markerar kryss rutan **dolda objekt** i Utforskaren.) Fel kan bero på flera problem. 

5. Sök efter strängen **post-begäran: (7) – Det gick inte att ansluta till servern**. Om strängen hittas:
    1. Lös nätverks problemen mellan käll datorn och konfigurations servern. Kontrol lera att konfigurations servern kan anslutas från käll datorn med hjälp av nätverks verktyg som ping, traceroute eller en webbläsare. Se till att käll datorn kan komma åt konfigurations servern via port 443.
    2. Kontrol lera om brand Väggs reglerna på käll datorn blockerar anslutningen mellan käll datorn och konfigurations servern. Arbeta med nätverks administratörerna för att avblockera eventuella anslutnings problem.
    3. Se till att de mappar som anges i [Site Recovery mapp undantag från antivirus program](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program) undantas från antivirus program varan.
    4. När nätverks problem är lösta gör du ett nytt försök med registreringen genom att följa rikt linjerna i [Registrera käll datorn med konfigurations servern](vmware-azure-troubleshoot-configuration-server.md#register-source-machine-with-configuration-server).

6. Om strängen **post-begäran: (7) – det inte går att ansluta till servern** går du till den i samma loggfil **: (60) – peer-certifikatet kan inte autentiseras med de certifikat som utfärdas**. Det här felet kan inträffa eftersom konfigurations serverns certifikat har upphört att gälla eller om käll datorn inte stöder TLS 1,0 eller senare protokoll. Det kan också inträffa om en brand vägg blockerar TLS-kommunikation mellan käll datorn och konfigurations servern. Om strängen hittas: 
    1. Lös problemet genom att ansluta till konfigurations serverns IP-adress med hjälp av en webbläsare på käll datorn. Använd URI https: \/ \/<konfigurations SERVERns IP-adress \> : 443/. Se till att käll datorn kan komma åt konfigurations servern via port 443.
    2. Kontrol lera om brand Väggs regler på käll datorn måste läggas till eller tas bort för att käll datorn ska kunna kommunicera med konfigurations servern. På grund av de olika brand Väggs program som kanske används kan vi inte lista alla nödvändiga brand Väggs konfigurationer. Arbeta med nätverks administratörerna för att avblockera eventuella anslutnings problem.
    3. Se till att de mappar som anges i [Site Recovery mapp undantag från antivirus program](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program) undantas från antivirus program varan.  
    4. När du har löst problemen, gör om registreringen genom att följa rikt linjerna i [Registrera käll datorn med konfigurations servern](vmware-azure-troubleshoot-configuration-server.md#register-source-machine-with-configuration-server).

7. Om värdet för plattformen i <INSTALLATION_DIR \> /etc/drscout.conf är skadat på Linux, så Miss lyckas registreringen. Du kan identifiera det här problemet genom att öppna filen/var/log/ua_install. log. Sök efter strängen **som avbryter konfigurationen eftersom VM_PLATFORM värdet är antingen null eller inte VMware/Azure**. Plattformen ska vara inställd på antingen **VMware** eller **Azure**. Om filen drscout. conf är skadad, rekommenderar vi att du [avinstallerar mobilitets agenten](vmware-physical-manage-mobility-service.md#uninstall-mobility-service) och sedan installerar om mobilitets agenten. Om avinstallationen Miss lyckas utför du följande steg: a. Öppna filen Installation_Directory/Uninstall.sh och kommentera ut anropet till funktionen **StopServices** .
    b. Öppna filen Installation_Directory/VX/bin/Uninstall.sh och kommentera ut anropet till funktionen **stop_services** .
    c. Öppna filen Installation_Directory/FX/Uninstall.sh och kommentera ut hela avsnittet som försöker stoppa FX-tjänsten.
    d. [Avinstallera](vmware-physical-manage-mobility-service.md#uninstall-mobility-service) mobilitets agenten. Efter en lyckad avinstallation startar du om systemet och försöker sedan installera om mobilitets agenten.

8. Se till att Multi-Factor Authentication inte är aktiverat för användar kontot. Azure Site Recovery stöder inte Multi-Factor Authentication för användar kontot från och med nu. Registrera konfigurations servern utan Multi-Factor Authentication-aktiverat användar konto.  

## <a name="installation-failure-failed-to-load-accounts"></a>Installations fel: det gick inte att läsa in konton

Felet uppstår när tjänsten inte kan läsa data från transport anslutningen när den installerar mobilitets agenten och registrerar med konfigurations servern. Lös problemet genom att se till att TLS 1,0 är aktiverat på käll datorn.

## <a name="vcenter-discovery-failures"></a>problem med vCenter-identifiering

Om du vill lösa vCenter-identifierings felen lägger du till vCenter-servern i listan över proxyinställningar. 

- Hämta PsExec-verktyget [härifrån för att få åtkomst till innehåll](/sysinternals/downloads/psexec) i systemet.
- Öppna Internet Explorer i system användar innehåll genom att köra följande kommando rad PsExec-s-i "%programfiles%\Internet Explorer\iexplore.exe"
- Lägg till proxyinställningar i IE och starta om tmanssvc-tjänsten.
- Om du vill konfigurera inställningar för proxyserver kör du CD C:\Program\Microsoft Azure Site Recovery Provider
- Kör sedan DRCONFIGURATOR.EXE/Configure/AddBypassUrls [Lägg till IP-adress/fullständigt domän namn för vCenter Server som anges under **konfigurera vCenter Server/vSphere ESXi Server-** steg för [distribution av konfigurations Server](vmware-azure-deploy-configuration-server.md#configure-settings)]

## <a name="change-the-ip-address-of-the-configuration-server"></a>Ändra konfigurations serverns IP-adress

Vi rekommenderar starkt att du inte ändrar IP-adressen för en konfigurations Server. Se till att alla IP-adresser som är tilldelade till konfigurations servern är statiska IP-adresser. Använd inte DHCP IP-adresser.

## <a name="acs50008-saml-token-is-invalid"></a>ACS50008: SAML-token är ogiltig

Undvik det här felet genom att se till att tiden på system klockan inte skiljer sig från den lokala tiden med mer än 15 minuter. Kör installationsprogrammet igen för att slutföra registreringen.

## <a name="failed-to-create-a-certificate"></a>Det gick inte att skapa ett certifikat

Det går inte att skapa ett certifikat som krävs för att autentisera Site Recovery. Kör installations programmet igen när du har säkerställt att du kör installations programmet som lokal administratör.

## <a name="failure-to-activate-windows-license-from-server-standard-evaluation-to-server-standard"></a>Det gick inte att aktivera Windows-licens från Server standard utvärdering till Server standard

1. Som en del av konfigurations serverns distribution via OVF används en utvärderings licens som är giltig i 180 dagar. Du måste aktivera den här licensen innan detta upphör att gälla. Annars kan detta leda till att konfigurations servern ofta stängs av och därför kan det orsaka hinder för replikering av aktiviteter.
2. Om du inte kan aktivera Windows-licens kan du kontakta [support teamet för Windows](https://aka.ms/Windows_Support) för att lösa problemet.

## <a name="register-source-machine-with-configuration-server"></a>Registrera käll dator med konfigurations Server

### <a name="if-the-source-machine-runs-windows"></a>Om käll datorn kör Windows

Kör följande kommando på käll datorn:

```
  cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
  UnifiedAgentConfigurator.exe  /CSEndPoint <configuration server IP address> /PassphraseFilePath <passphrase file path>
```

Inställningen | Information
--- | ---
Användning | UnifiedAgentConfigurator.exe/CSEndPoint <konfigurations serverns IP-adress \> /PassphraseFilePath <sökväg till lösen fras\>
Agent konfigurations loggar | Finns under%ProgramData%\ASRSetupLogs\ASRUnifiedAgentConfigurator.log.
/CSEndPoint | Obligatorisk parameter. Anger konfigurations serverns IP-adress. Använd en giltig IP-adress.
/PassphraseFilePath |  Obligatorisk. Platsen för lösen frasen. Använd en giltig UNC-eller lokal fil Sök väg.

### <a name="if-the-source-machine-runs-linux"></a>Om käll datorn kör Linux

Kör följande kommando på käll datorn:

```
  /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <configuration server IP address> -P /var/passphrase.txt
  ```

Inställningen | Information
--- | ---
Användning | cd-/usr/local/ASR/Vx/bin<br /><br /> UnifiedAgentConfigurator.sh-i <konfigurations serverns IP-adress \> -P <lösen ords Sök väg\>
-i | Obligatorisk parameter. Anger konfigurations serverns IP-adress. Använd en giltig IP-adress.
-P |  Obligatorisk. Den fullständiga sökvägen till filen där lösen frasen sparas. Använd en giltig mapp.

## <a name="unable-to-configure-the-configuration-server"></a>Det gick inte att konfigurera konfigurations servern

Om du installerar andra program än konfigurations servern på den virtuella datorn kan du kanske inte konfigurera huvud målet. 

Konfigurations servern måste vara en enda syftes Server och använda den som en delad server stöds inte. 

Mer information finns i vanliga frågor och svar om konfiguration i [distribuera en konfigurations Server](vmware-azure-deploy-configuration-server.md#faqs). 

## <a name="remove-the-stale-entries-for-protected-items-from-the-configuration-server-database"></a>Ta bort inaktuella poster för skyddade objekt från konfigurations Server databasen 

Använd följande steg för att ta bort föråldrad skyddad dator på konfigurations servern. 
 
1. Så här fastställer du käll datorn och IP-adressen för den inaktuella posten: 

    1. Öppna MYSQL cmdline i administratörs läge. 
    2. Kör följande kommandon. 
   
        ```
        mysql> use svsdb1;
        mysql> select id as hostid, name, ipaddress, ostype as operatingsystem, from_unixtime(lasthostupdatetime) as heartbeat from hosts where name!='InMageProfiler'\G;
        ```

        Detta returnerar listan över registrerade datorer tillsammans med deras IP-adresser och den senaste pulsslags takten. Hitta värden som har inaktuella replikeringsinställningar.

2. Öppna en kommando tolk med förhöjd behörighet och navigera till C:\ProgramData\ASR\home\svsystems\bin. 
4. Om du vill ta bort informationen om registrerade värdar och inaktuell information från konfigurations servern kör du följande kommando med käll datorn och IP-adressen för den inaktuella posten. 
   
    `Syntax: Unregister-ASRComponent.pl -IPAddress <IP_ADDRESS_OF_MACHINE_TO_UNREGISTER> -Component <Source/ PS / MT>`
 
    Om du har en käll Server post av "OnPrem-VM01" med IP-adressen 10.0.0.4 använder du följande kommando i stället.
 
    `perl Unregister-ASRComponent.pl -IPAddress 10.0.0.4 -Component Source`
 
5. Starta om följande tjänster på käll datorn för att omregistrera med konfigurations servern. 
 
    - InMage Scout Application Service
    - InMage Scout VX agent-Sentinel/utpost

## <a name="upgrade-fails-when-the-services-fail-to-stop"></a>Uppgraderingen Miss lyckas när tjänsterna inte kan stoppas

Uppgraderingen av konfigurations servern Miss lyckas när vissa tjänster inte stoppas. 

Identifiera problemet genom att navigera till C:\ProgramData\ASRSetupLogs\ CX_TP_InstallLogFile på konfigurations servern. Om du hittar följande fel kan du använda stegen nedan för att lösa problemet: 

```output
2018-06-28 14:28:12.943   Successfully copied php.ini to C:\Temp from C:\thirdparty\php5nts
2018-06-28 14:28:12.943   svagents service status - SERVICE_RUNNING
2018-06-28 14:28:12.944   Stopping svagents service.
2018-06-28 14:31:32.949   Unable to stop svagents service.
2018-06-28 14:31:32.949   Stopping svagents service.
2018-06-28 14:34:52.960   Unable to stop svagents service.
2018-06-28 14:34:52.960   Stopping svagents service.
2018-06-28 14:38:12.971   Unable to stop svagents service.
2018-06-28 14:38:12.971   Rolling back the install changes.
2018-06-28 14:38:12.971   Upgrade has failed.
```

Så här löser du problemet:

Stoppa följande tjänster manuellt:

- cxprocessserver
- InMage Scout VX agent – Sentinel/utpost, 
- Microsoft Azure Recovery Services agent, 
- Microsoft Azure Site Recovery-tjänsten, 
- tmansvc
  
Om du vill uppdatera konfigurations servern kör du den [enhetliga installationen](service-updates-how-to.md#links-to-currently-supported-update-rollups) igen.

## <a name="azure-active-directory-application-creation-failure"></a>Det gick inte att skapa Azure Active Directory program

Du har inte behörighet att skapa ett program i Azure Active Directory (AAD) med hjälp av mallen för att [Öppna virtualiseringsvärdservrar (ägg)](vmware-azure-deploy-configuration-server.md#deploy-a-configuration-server-through-an-ova-template
) .

Lös problemet genom att logga in på Azure Portal och gör något av följande:

- Be rollen program utvecklare i AAD. Mer information om rollen programutvecklare finns [i administratörs roll behörigheter i Azure Active Directory](../active-directory/roles/permissions-reference.md).
- Kontrol lera att flaggan **användare kan skapa program** har angetts till *True* i AAD. Mer information finns i [så här gör du: Använd portalen för att skapa ett Azure AD-program och tjänstens huvud namn som har åtkomst till resurser](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app).

## <a name="process-servermaster-target-are-unable-to-communicate-with-the-configuration-server"></a>Processervern/huvud mål servern kan inte kommunicera med konfigurations servern 

Modulerna processervern (PS) och Master Target (MT) kan inte kommunicera med konfigurations servern (CS) och deras status visas som ej ansluten på Azure Portal.

Detta beror vanligt vis på ett fel med port 443. Använd följande steg för att avblockera porten och återaktivera kommunikation med CS.

**Verifiera att MARS-agenten anropas av huvud mål agenten**

Kontrol lera att huvud mål agenten kan skapa en TCP-session för konfigurations serverns IP-adress genom att leta efter en spårning som liknar följande i huvud mål agentens loggar:

TCP \<Replace IP with CS IP here> : 52739 \<Replace IP with CS IP here> : 443 SYN_SENT 

TCP-192.168.1.40:52739 192.168.1.40:443 SYN_SENT//Ersätt IP med CS-IP här

Om du hittar spår som liknar följande i MT-agentens loggar, rapporterar MT-agenten fel på port 443:

```output
#~> (11-20-2018 20:31:51):   ERROR  2508 8408 313 FAILED : PostToSVServer with error [at curlwrapper.cpp:CurlWrapper::processCurlResponse:212]   failed to post request: (7) - Couldn't connect to server
#~> (11-20-2018 20:31:54):   ERROR  2508 8408 314 FAILED : PostToSVServer with error [at curlwrapper.cpp:CurlWrapper::processCurlResponse:212]   failed to post request: (7) - Couldn't connect to server
```
 
Det här felet kan uppstå när andra program också använder port 443 eller på grund av en brand Väggs inställning som blockerar porten.

Så här löser du problemet:

- Kontrol lera att port 443 inte blockeras av brand väggen.
- Om porten inte kan anslutas på grund av ett annat program som använder den porten, stoppar och avinstallerar du appen.
  - Om det inte är möjligt att stoppa appen måste du konfigurera en ny ren CS.
- Starta om konfigurations servern.
- Starta om IIS-tjänsten.

### <a name="configuration-server-is-not-connected-due-to-incorrect-uuid-entries"></a>Konfigurations servern är inte ansluten på grund av felaktiga UUID-poster

Det här felet kan inträffa när det finns flera instanser av konfigurations serverns UUID (CS) i databasen. Problemet uppstår ofta när du klonar konfigurations serverns virtuella dator.

Så här löser du problemet:

1. Ta bort föråldrad/gammal virtuell dator från vCenter. Mer information finns i  [ta bort servrar och inaktivera skydd](site-recovery-manage-registration-and-protection.md).
2. Logga in på den virtuella datorns konfigurations Server och Anslut till MySQL svsdb1-databasen. 
3. Kör följande fråga:

    > [!IMPORTANT]
    >
    > Kontrol lera att du anger UUID-information för den klonade konfigurations servern eller den inaktuella posten för konfigurations servern som inte längre används för att skydda virtuella datorer. Om du anger ett felaktigt UUID kommer informationen för alla befintliga skyddade objekt att förloras.
   
    ```
        MySQL> use svsdb1;
        MySQL> delete from infrastructurevms where infrastructurevmid='<Stale CS VM UUID>';
        MySQL> commit; 
    ```
4. Uppdatera portal sidan.

## <a name="an-infinite-sign-in-loop-occurs-when-entering-your-credentials"></a>En oändlig inloggnings slinga inträffar när du anger dina autentiseringsuppgifter

När du har angett rätt användar namn och lösen ord på konfigurations servern OVF, fortsätter Azure logga in för att uppmana dig att ange rätt autentiseringsuppgifter.

Det här problemet kan inträffa när system tiden är felaktig.

Så här löser du problemet:

Ange rätt tid på datorn och försök sedan logga in igen. 
