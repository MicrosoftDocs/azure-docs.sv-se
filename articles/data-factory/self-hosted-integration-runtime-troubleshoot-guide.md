---
title: Felsöka integration runtime med egen värd i Azure Data Factory
description: Lär dig hur du felsöker problem med integration runtime med egen värd i Azure Data Factory.
services: data-factory
author: lrtoyou1223
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 11/17/2020
ms.author: lle
ms.openlocfilehash: ccebdbf428180f8ff4ab10dc6007c3ec35a66362
ms.sourcegitcommit: 2ba6303e1ac24287762caea9cd1603848331dd7a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/15/2020
ms.locfileid: "97503598"
---
# <a name="troubleshoot-self-hosted-integration-runtime"></a>Felsöka integration runtime med egen värd

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Den här artikeln visar vanliga fel söknings metoder för integration Runtime (IR) med egen värd i Azure Data Factory.

## <a name="gather-self-hosted-ir-logs-from-azure-data-factory"></a>Samla in IR-loggar med egen värd från Azure Data Factory

För misslyckade aktiviteter som körs på en IR eller en delad IR-överföring stöder Azure Data Factory att visa och ladda upp fel loggar. Hämta fel rapportens ID genom att följa anvisningarna här och ange sedan rapport-ID för att söka efter relaterade kända problem.

1. I Data Factory väljer du **pipeline-körningar**.

1. Under **aktivitets körningar** i kolumnen **fel** väljer du den markerade knappen för att Visa aktivitets loggarna, som du ser på följande skärm bild:

    ![Skärm bild av avsnittet "aktivitets körningar" i fönstret "alla pipeline-körningar".](media/self-hosted-integration-runtime-troubleshoot-guide/activity-runs-page.png)

    Aktivitets loggarna visas för körning av misslyckad aktivitet.

    ![Skärm bild av aktivitets loggarna för den misslyckade aktiviteten.](media/self-hosted-integration-runtime-troubleshoot-guide/send-logs.png) 
    
1. Om du behöver ytterligare hjälp väljer du **skicka loggar**.
 
   **Delningen av IR-loggarna (egen värd för integrering Runtime) med Microsoft** öppnas.

    ![Skärm bild av fönstret "dela den egna värdbaserade integrerings körningen (IR) med Microsoft".](media/self-hosted-integration-runtime-troubleshoot-guide/choose-logs.png)

1. Välj vilka loggar du vill skicka. 
    * För en *IR med egen värd* kan du överföra loggar som är relaterade till den misslyckade aktiviteten eller alla loggar på IR-noden med egen värd. 
    * För en *delad IR* kan du bara överföra loggar som är relaterade till den misslyckade aktiviteten.

1. När loggarna har laddats upp ska du behålla en post med rapport-ID: t för senare användning om du behöver hjälp med att lösa problemet.

    ![Skärm bild av det visade rapport-ID: t i fönstret för överförings förloppet för IR-loggar.](media/self-hosted-integration-runtime-troubleshoot-guide/upload-logs.png)

> [!NOTE]
> Logg visning och överförings begär Anden körs på alla online-IR-instanser online. Om några loggar saknas, se till att alla IR-instanser med egen värd är online. 


## <a name="self-hosted-ir-general-failure-or-error"></a>Allmänt fel eller fel i lokalt installerad IR

### <a name="out-of-memory-issue"></a>Slut på minnes problem

#### <a name="symptoms"></a>Symtom

Ett OutOfMemoryException-fel (OOM) inträffar när du försöker köra en söknings aktivitet med en länkad IR eller en lokal IR-åtgärd.

#### <a name="cause"></a>Orsak

En ny aktivitet kan utlösa ett OOM-fel om IR-datorn upplever en momentant hög minnes användning. Problemet kan bero på en stor mängd samtidig aktivitet och felet är avsiktligt.

#### <a name="resolution"></a>Lösning

Kontrol lera resursanvändningen och körningen av samtidiga aktiviteter på IR-noden. Justera den interna och Utlös ande tiden för aktivitets körningar för att undvika för mycket körning på en enskild IR-nod på samma gång.


### <a name="ssltls-certificate-issue"></a>Problem med SSL/TLS-certifikat

#### <a name="symptoms"></a>Symtom

När du försöker aktivera ett Secure Sockets Layer (SSL)/Transport Layer Security (TLS)-certifikat (avancerat) genom att välja certifikatet (när du har valt **IR-Configuration Manager**  >  **fjärråtkomst från intranätet**) får du följande fel meddelande:

"Inställningar för fjärråtkomst är ogiltiga. Identitets kontrollen misslyckades för utgående meddelande. Den förväntade DNS-identiteten för Fjärrslutpunkten var ' abc.microsoft.com ', men Fjärrslutpunkten tillhandahöll DNS-anspråket ' microsoft.com '. Om det här är en legitim fjärrslutpunkt kan du åtgärda problemet genom att uttryckligen ange DNS-identiteten "microsoft.com" som identitets egenskap för EndpointAddress när du skapar en kanal-proxy. "

I föregående exempel har det valda certifikatet "microsoft.com" bifogat till det.

#### <a name="cause"></a>Orsak

Detta är ett känt problem i Windows Communication Foundation (WCF). Verifieringen av WCF SSL/TLS kontrollerar bara för de senaste DNSName i fältet **Alternativt namn för certifikat mottagare** (San). 

#### <a name="resolution"></a>Lösning

Ett jokertecken stöds i Azure Data Factory v2-IR med egen värd. Det här problemet beror vanligt vis på att SSL-certifikatet är felaktigt. Den senaste DNSName i SAN ska vara giltig. 

Så här kontrollerar du och korrigerar DNSName: 

1. Öppna hanterings konsolen.
1. Under **certifikat information**, dubbelklickar du på värdet i rutorna namn på **ämne** och **mottagar namn** . Till exempel är "DNS-namn = microsoft.com.com" inte ett giltigt namn.
1. Kontakta företaget för certifikat utfärdaren om du vill att felaktiga DNSName ska tas bort.

### <a name="concurrent-jobs-limit-issue"></a>Problem med gräns för samtidiga jobb

#### <a name="symptoms"></a>Symtom

När du försöker öka gränsen för antalet samtidiga jobb från Azure Data Factory-gränssnittet låser sig processen under *uppdaterings* status.

Exempel scenario: det maximala värdet för samtidiga jobb är för närvarande 24 och du vill öka antalet så att jobben kan köras snabbare. Det minsta värde som du kan ange är 3 och det maximala värdet är 32. Du ökar värdet från 24 till 32 och väljer sedan knappen **Uppdatera** . Processen har fastnat i *uppdaterings* statusen, som visas i följande skärm bild. Du uppdaterar sidan och värdet visas fortfarande som 24. Den har inte uppdaterats till 32 eftersom du förväntade dig.

![Skärm bild av fönstret noder i integration runtime, som visar processen som har fastnat i status uppdatering.](media/self-hosted-integration-runtime-troubleshoot-guide/updating-status.png)

#### <a name="cause"></a>Orsak

Gränsen för antalet samtidiga jobb beror på datorns logik kärna och minne. Försök att justera värdet nedåt till ett värde, till exempel 24, och Visa sedan resultatet.

> [!TIP] 
> - Om du vill veta mer om Logic Core-antal och fastställa din dators logiska kärn antal, se [fyra sätt att hitta antalet kärnor i din CPU i Windows 10](https://www.top-password.com/blog/find-number-of-cores-in-your-cpu-on-windows-10/).
> - Om du vill veta hur du beräknar matematik. log går du till [logaritmen](https://www.rapidtables.com/calc/math/Log_Calculator.html).


### <a name="self-hosted-ir-high-availability-ha-ssl-certificate-issue"></a>SSL-certifikat med egen värd för IR-certifikat med hög tillgänglighet

#### <a name="symptoms"></a>Symtom

Den egna IR-arbetsnoden har rapporterat följande fel:

"Det gick inte att hämta delade tillstånd från den primära noden net. TCP://abc.cloud.corp.Microsoft.com: 8060/ExternalService. svc/. Aktivitets-ID: XXXXX X. 509-certifikatet CN = ABC. Cloud. Corp. Microsoft. com, OU = test, O = Microsoft kedja Building misslyckades. Certifikatet som användes har en förtroende kedja som inte kan verifieras. Ersätt certifikatet eller ändra certificateValidationMode. Återkallnings funktionen kunde inte kontrol lera återkallning eftersom åter kallelse servern var offline.

#### <a name="cause"></a>Orsak

När du hanterar ärenden som är relaterade till en SSL/TLS-handskakning kan du stöta på problem som rör verifiering av certifikat kedjan. 

#### <a name="resolution"></a>Lösning

- Här är ett snabbt och intuitivt sätt att felsöka ett X. 509-certifikat kedjans build-fel:
 
    1. Exportera certifikatet, som måste verifieras. Gör så här:
    
       a. I Windows väljer du **Start**, börjar skriva **certifikat** och väljer sedan **Hantera dator certifikat**.
       
       b. I Utforskaren i det vänstra fönstret söker du efter det certifikat som du vill kontrol lera, högerklickar på det och väljer sedan **alla aktiviteter**  >  **Exportera**.
    
        ![Skärm bild av kontrollen "alla aktiviteter" > export för ett certifikat i fönstret "hantera dator certifikat".](media/self-hosted-integration-runtime-troubleshoot-guide/export-tasks.png)

    2. Kopiera det exporterade certifikatet till klient datorn. 
    3. Kör följande kommando på klient sidan i kommando tolkens fönster. Se till att ersätta *\<certificate path>* och *\<output txt file path>* med de faktiska Sök vägarna.
    
        ```
        Certutil -verify -urlfetch    <certificate path>   >     <output txt file path> 
        ```

        Exempel:

        ```
        Certutil -verify -urlfetch c:\users\test\desktop\servercert02.cer > c:\users\test\desktop\Certinfo.txt
        ```
    4. Sök efter fel i output TXT-filen. Du kan hitta fel sammanfattningen i slutet av TXT-filen.

        Exempel: 

        ![Skärm bild av en fel Sammanfattning i slutet av TXT-filen.](media/self-hosted-integration-runtime-troubleshoot-guide/error-summary.png)

        Om du inte ser ett fel i slutet av logg filen, som du ser i följande skärm bild, kan du tänka på att certifikat kedjan har skapats på klient datorn.
        
        ![Skärm bild av en loggfil som visar inga fel.](media/self-hosted-integration-runtime-troubleshoot-guide/log-file.png)      

- Om en AIA (åtkomst till auktoritets information), CDP (CRL distributions plats) eller OCSP (Online Certificate Status Protocol) fil namns tillägg har kon figurer ATS i certifikat filen, kan du kontrol lera det på ett mer intuitivt sätt:
 
    1. Hämta den här informationen genom att kontrol lera certifikat informationen, som visas på följande skärm bild:
    
        ![Skärm bild av certifikat information.](media/self-hosted-integration-runtime-troubleshoot-guide/certificate-detail.png)
    
    1. Kör följande kommando. Se till att ersätta *\<certificate path>* med den faktiska sökvägen till certifikatet.
    
        ```
          Certutil   -URL    <certificate path> 
        ```
    
        URL-filhämtnings verktyget öppnas. 
        
    1. Om du vill verifiera certifikat med AIA-, CDP-och OCSP-filnamnstillägg väljer du **Hämta**.

        ![Skärm bild av verktyget URL-hämtning och knappen Hämta.](media/self-hosted-integration-runtime-troubleshoot-guide/retrieval-button.png)
 
        Du har skapat certifikat kedjan om certifikat statusen från AIA *verifieras* och certifikat status från CDP eller OCSP *verifieras*.

        Om du inte kan hämta AIA eller CDP, arbetar du med nätverks teamet för att ansluta klient datorn så att den kan ansluta till mål-URL: en. Det blir tillräckligt om antingen HTTP-sökvägen eller LDAP-sökvägen (Lightweight Directory Access Protocol) kan verifieras.

### <a name="self-hosted-ir-could-not-load-file-or-assembly"></a>Lokalt installerad IR kunde inte läsa in filen eller sammansättningen

#### <a name="symptoms"></a>Symtom

Du får följande fel meddelande:

"Det gick inte att läsa in filen eller sammansättningen" XXXXXXXXXXXXXXXX, version = 4.0.2.0, Culture = neutral, PublicKeyToken = XXXXXXXXX "eller något av dess beroenden. Det går inte att hitta den angivna filen. Aktivitets-ID: 92693b45-B4BF-4FC8-89da-2d3dc56f27c3 "
 
Här är ett mer specificerat fel meddelande: 

"Det gick inte att läsa in filen eller sammansättningen" system. ValueTuple, version = 4.0.2.0, Culture = neutral, PublicKeyToken = XXXXXXXXX "eller något av dess beroenden. Det går inte att hitta den angivna filen. Aktivitets-ID: 92693b45-B4BF-4FC8-89da-2d3dc56f27c3 "

#### <a name="cause"></a>Orsak

I Process Monitor kan du se följande resultat:

[![Skärm bild av listan sökvägar i process övervakaren.](media/self-hosted-integration-runtime-troubleshoot-guide/process-monitor.png)](media/self-hosted-integration-runtime-troubleshoot-guide/process-monitor.png#lightbox)

> [!TIP] 
> I Process Monitor kan du ange filter som visas i följande skärm bild.
>
> Föregående fel meddelande anger att DLL-systemet system. ValueTuple inte finns i den relaterade mappen *Global Assembly Cache* (GAC) i mappen *c:\Program\Microsoft integration Runtime\4.0\Gateway* , eller i mappen *c:\Program Files\Microsoft integration Runtime\4.0\Shared* .
>
> I princip laddar processen först DLL-filen från *GAC* -mappen, sedan från den *delade* mappen och slutligen från *Gateway* -mappen. Därför kan du läsa in DLL-filen från alla sökvägar som är användbara.

<br>

![Skärm bild av sidan "Process Monitor filter" som visar filter för DLL-filen.](media/self-hosted-integration-runtime-troubleshoot-guide/set-filters.png)

#### <a name="resolution"></a>Lösning

Du hittar *System.ValueTuple.dll* -filen i mappen *C:\Program Files\Microsoft integration Runtime\4.0\Gateway\DataScan* . Lös problemet genom att kopiera *System.ValueTuple.dll* -filen till mappen *c:\Program\Microsoft integration Runtime\4.0\Gateway* .

Du kan använda samma metod för att lösa andra problem som saknas i filen eller sammansättningen.

#### <a name="more-information-about-this-issue"></a>Mer information om det här problemet

Anledningen till att du ser *System.ValueTuple.dll* under *%windir%\Microsoft.NET\assembly* och *%windir%\assembly* är att detta är ett .net-beteende. 

I följande fel kan du tydligt se att sammansättningen *system. ValueTuple* saknas. Det här problemet uppstår när programmet försöker kontrol lera *System.ValueTuple.dll* sammansättningen.
 
" \<LogProperties> \<ErrorInfo> [{" Code ": 0," meddelande ":" typ initieraren för "Npgsql. PoolManager" utlöste ett undantag. "," EventType ": 0," kategori ": 5," data ": {} ," MsgId ": null," undantag ":" system. TypeInitializationException "," källa ":" Npgsql "," stacktrace ":" "," InnerEventInfos ": [{" Code ": 0," meddelande ":" Det gick inte att läsa in filen eller sammansättningen system. ValueTuple, version = 4.0.2.0, Culture = neutral, PublicKeyToken = xxxxxxxxx eller något av dess beroenden. Det går inte att hitta den angivna filen. ", EventType": 0, "kategori": 5, "data": {} , "MsgId":null, "ExceptionType":"system. io. FileNotFoundException", "source": "Npgsql", "stacktrace": "", "InnerEventInfos": []}]}] \</ErrorInfo> \</LogProperties> "
 
Mer information om GAC finns i [Global Assembly Cache](https://docs.microsoft.com/dotnet/framework/app-domains/gac).


### <a name="self-hosted-integration-runtime-authentication-key-is-missing"></a>Autentisering med egen värd för integration runtime saknas

#### <a name="symptoms"></a>Symtom

Den egen värdbaserade integrerings körningen är plötsligt offline utan en autentiseringsnyckel, och händelse loggen visar följande fel meddelande: 

"Autentiseringsnyckel är inte tilldelad än"

![Skärm bild av händelse fönstret för integration runtime som visar att nyckeln inte har tilldelats än.](media/self-hosted-integration-runtime-troubleshoot-guide/key-missing.png)

#### <a name="cause"></a>Orsak

- Den egna IR-noden eller den logiska, egna IR-noden i Azure Portal har tagits bort.
- En ren avinstallation utfördes.

#### <a name="resolution"></a>Lösning

Om ingen av föregående orsaker gäller kan du gå till mappen *%programdata%\Microsoft\Data Transfer\DataManagementGateway* för att se om *konfigurations* filen har tagits bort. Om den har tagits bort följer du anvisningarna i artikeln Netwrix [identifiera vem som tog bort en fil från dina Windows-filservrar](https://www.netwrix.com/how_to_detect_who_deleted_file.html).

![Skärm bild av händelse logg informations fönstret för att kontrol lera konfigurations filen.](media/self-hosted-integration-runtime-troubleshoot-guide/configurations-file.png)


### <a name="cant-use-self-hosted-ir-to-bridge-two-on-premises-datastores"></a>Det går inte att använda IR med egen värd för att överbrygga två lokala data lager

#### <a name="symptoms"></a>Symtom

När du har skapat en egen organisation för både käll-och mål data lagret vill du ansluta de två IRs till att slutföra en kopierings aktivitet. Om data lagringen har kon figurer ATS i olika virtuella nätverk, eller om data lagret inte kan förstå Gateway-mekanismen, får du något av följande fel: 

* "Det går inte att hitta driv rutinen för källan i målets IR-rutin"
* "Källan kan inte nås av mål-IR-filen"
 
#### <a name="cause"></a>Orsak

IR med egen värd är utformad som en central nod i en kopierings aktivitet, inte en klient agent som behöver installeras för varje data lager.
 
I det här fallet bör du skapa den länkade tjänsten för varje data lager med samma IR och IR: en ska kunna komma åt både data lagret via nätverket. Det spelar ingen roll om IR har installerats på käll data lagret eller på mål data lagret eller på en tredje dator. Om två länkade tjänster skapas med en annan IRs men används i samma kopierings aktivitet, används mål-IR och du måste installera driv rutinerna för båda data lagrings enheterna på IR-datorn för målet.

#### <a name="resolution"></a>Lösning

Installera driv rutiner för både käll-och mål data lager på mål-IR och se till att de kan komma åt käll data lagret.
 
Om trafiken inte kan passera nätverket mellan två data lager (till exempel att de har kon figurer ATS i två virtuella nätverk) kan du inte slutföra kopieringen i en aktivitet även om IR är installerat. Om du inte kan slutföra kopieringen i en enskild aktivitet, kan du skapa två kopierings aktiviteter med två IRs, var och en i en ventilation: 
* Kopiera en IR från data lagret 1 till Azure Blob Storage
* Kopiera en annan IR från Azure Blob Storage till ddatastore 2. 

Den här lösningen kan simulera kravet på att använda IR för att skapa en brygga som ansluter två frånkopplade data lager.


### <a name="credential-sync-issue-causes-credential-loss-from-ha"></a>Problem med synkronisering av autentiseringsuppgifter medför förlust av autentiseringsuppgifter från HA

#### <a name="symptoms"></a>Symtom

Om data källans autentiseringsuppgiften "XXXXXXXXXX" tas bort från den aktuella noden för integration runtime med nytto Last får du följande fel meddelande:

"När du tar bort länk tjänsten på Azure Portal, eller om aktiviteten har fel nytto Last, skapar du en ny länk tjänst med dina autentiseringsuppgifter igen."

#### <a name="cause"></a>Orsak

Din egen värd-IR har skapats i HA-läge med två noder, men noderna är inte i ett synkroniseringstillstånd för autentiseringsuppgifter. Det innebär att autentiseringsuppgifterna som lagras i dispatcher-noden inte synkroniseras med andra arbetsnoder. Om redundansväxlingen sker från dispatcher-noden till arbetsnoden, och autentiseringsuppgifterna endast finns i den tidigare noden dispatcher, kommer aktiviteten inte att fungera när du försöker få åtkomst till autentiseringsuppgifterna, och du får föregående fel.

#### <a name="resolution"></a>Lösning

Det enda sättet att undvika det här problemet är att se till att de två noderna är i Sync-läget för autentiseringsuppgifter. Om de inte är synkroniserade måste du ange autentiseringsuppgifterna för den nya Dispatchern igen.


### <a name="cant-choose-the-certificate-because-the-private-key-is-missing"></a>Det går inte att välja certifikatet eftersom den privata nyckeln saknas

#### <a name="symptoms"></a>Symtom

* Du har importerat en PFX-fil till certifikat arkivet.
* När du har valt certifikatet via IR Configuration Manager-ANVÄNDARGRÄNSSNITTET fick du följande fel meddelande:

   "Det gick inte att ändra krypterings läget för intranäts kommunikation. Det är sannolikt att certifikatet \<*certificate name*> inte har en privat nyckel som kan användas med nyckel utbyte eller att processen inte har åtkomst behörighet för den privata nyckeln. Mer information finns i ursprungs undantaget. "

    ![Skärm bild av fel meddelandet "privat nyckel saknas" i rutan Integration Runtime Configuration Managers inställningar.](media/self-hosted-integration-runtime-troubleshoot-guide/private-key-missing.png)

#### <a name="cause"></a>Orsak

- Användar kontot har låg behörighets nivå och kan inte komma åt den privata nyckeln.
- Certifikatet genererades som en signatur, men inte som nyckel utbyte.

#### <a name="resolution"></a>Lösning

* Använd ett konto med rätt behörighet för att komma åt den privata nyckeln för att hantera användar gränssnittet.  
* Importera certifikatet genom att köra följande kommando:
    
    ```
    certutil -importpfx FILENAME.pfx AT_KEYEXCHANGE
    ```

## <a name="self-hosted-ir-setup"></a>IR-installation med egen värd

### <a name="integration-runtime-registration-error"></a>Registrerings fel för integration runtime 

#### <a name="symptoms"></a>Symtom

Ibland kanske du vill köra en egen värd-IR i ett annat konto av någon av följande orsaker:
- Företags principen tillåter inte tjänst kontot.
- Viss autentisering krävs.

När du har ändrat tjänst kontot i fönstret tjänst kan du se att integration runtime slutar fungera och du får följande fel meddelande:

"Integration Runtime (lokal installation)-noden har påträffat ett fel under registreringen. Det går inte att ansluta till Integration Runtime (lokal installation) värd tjänsten. "

![Skärm bild av fönstret Integration Runtime Configuration Manager som visar ett IR-registrerings fel.](media/self-hosted-integration-runtime-troubleshoot-guide/ir-registration-error.png)

#### <a name="cause"></a>Orsak

Många resurser beviljas enbart till tjänst kontot. När du ändrar tjänst kontot till ett annat konto förblir behörigheterna för alla beroende resurser oförändrade.

#### <a name="resolution"></a>Lösning

Gå till händelse loggen för integration runtime för att kontrol lera felet.

![Skärm bild av IR-händelseloggen som visar att ett körnings fel har inträffat.](media/self-hosted-integration-runtime-troubleshoot-guide/ir-event-log.png)

* Om felet i händelse loggen är "UnauthorizedAccessException" gör du följande:

    1. Kontrol lera inloggnings tjänst kontot för *dia Host service* på panelen Windows-tjänst.

        ![Skärm bild av fönstret Egenskaper för inloggnings tjänstens konto.](media/self-hosted-integration-runtime-troubleshoot-guide/logon-service-account.png)

    1. Kontrol lera om inloggnings tjänst kontot har Läs-/Skriv behörighet för *%programdata%\Microsoft\DataTransfer\DataManagementGateway* -mappen.

        - Om tjänst inloggnings kontot inte har ändrats bör som standard ha läs-/skriv behörighet.

            ![Skärm bild av fönstret tjänst behörigheter.](media/self-hosted-integration-runtime-troubleshoot-guide/service-permission.png)

        - Om du har ändrat tjänst inloggnings kontot kan du åtgärda problemet genom att göra följande:
 
            a. Utför en ren avinstallation av den aktuella IR-filen med egen värd.   
            b. Installera IR-BITS med egen värd.  
            c. Ändra tjänst kontot genom att göra följande:  

             i. Gå till mappen för IR-installation i egen värd och växla sedan till *Microsoft integration Runtime\4.0\Shared* -mappen.  
             ii. Öppna ett kommando tolks fönster med utökade privilegier. Ersätt *\<user>* och *\<password>* med ditt eget användar namn och lösen ord och kör sedan följande kommando:   
                `dmgcmd.exe -SwitchServiceAccount "<user>" "<password>"`  
             iii. Om du vill ändra till LocalSystem-kontot måste du använda rätt format för det här kontot: `dmgcmd.exe -SwitchServiceAccount "NT Authority\System" ""`  
                Använd *inte* det här formatet: `dmgcmd.exe -SwitchServiceAccount "LocalSystem" ""`     
             a. Alternativt, eftersom det lokala systemet har högre privilegier än administratör, kan du också ändra det direkt i "tjänster".  
             v. Du kan använda en lokal/domän användare för inloggnings kontot för IR-tjänsten.            

            d. Registrera integration Runtime.

* Om felet är "tjänsten Integration Runtime tjänsten (dia Host service) inte kunde starta. Kontrol lera att du har behörighet att starta system tjänster, "gör följande:

    1. Kontrol lera inloggnings tjänst kontot för *dia Host service* på panelen Windows-tjänst.
    
        ![Skärm bild av tjänst kontots inloggnings fönster.](media/self-hosted-integration-runtime-troubleshoot-guide/logon-service-account.png)

    1. Kontrol lera om inloggnings tjänst kontot har **Logga in som tjänst** behörighet för att starta Windows-tjänsten:

        ![Skärm bild av egenskaps rutan "logga in som tjänst".](media/self-hosted-integration-runtime-troubleshoot-guide/logon-as-service.png)

#### <a name="more-information"></a>Mer information

Om inget av de föregående två lösnings mönstren används i ditt fall försöker du samla in följande Windows-händelse loggar: 
- > Integration Runtime för program-och tjänst loggar
- Windows-loggar > program

### <a name="cant-find-the-register-button-to-register-a-self-hosted-ir"></a>Det går inte att hitta knappen Registrera för att registrera en IR med egen värd    

#### <a name="symptoms"></a>Symtom

När du registrerar en IR med egen värd visas inte knappen **Registrera** i fönstret Configuration Manager.

![Skärm bild av fönstret Configuration Manager som visar ett meddelande om att integration runtime-noden inte är registrerad.](media/self-hosted-integration-runtime-troubleshoot-guide/no-register-button.png)

#### <a name="cause"></a>Orsak

Från och med lanseringen av Integration Runtime 3,0 har knappen **Registrera** på befintliga noder för integration runtime tagits bort för att möjliggöra en renare och säkrare miljö. Om en nod har registrerats i en integration runtime, oavsett om den är online eller inte, omregistrera den till en annan integrerings körning genom att avinstallera den tidigare noden och sedan installera och registrera noden.

#### <a name="resolution"></a>Lösning

1. Avinstallera den befintliga integrerings körningen på kontroll panelen.

    > [!IMPORTANT] 
    > I följande process väljer du **Ja**. Behåll inte data under avinstallations processen.

    ![Skärm bild av knappen "Ja" för att ta bort alla användar data från integration Runtime.](media/self-hosted-integration-runtime-troubleshoot-guide/delete-data.png)

1. Om du inte har MSI-filen för integration runtime-installationsprogrammet går du till [Download Center](https://www.microsoft.com/en-sg/download/details.aspx?id=39717) för att ladda ned den senaste integrerings körningen.
1. Installera MSI-filen och registrera integrerings körningen.


### <a name="unable-to-register-the-self-hosted-ir-because-of-localhost"></a>Det gick inte att registrera IR på egen värd på grund av localhost    

#### <a name="symptoms"></a>Symtom

Det går inte att registrera IR med egen värd på en ny dator när du använder get_LoopbackIpOrName.

**Fel sökning:** Ett körnings fel har inträffat.
Typinitieraren för Microsoft.DataTransfer.DIAgentHost.DataSourceCache utlöste ett undantag.
Ett oåterkalleligt fel inträffade under en databas sökning.
 
**Undantags information:** System. TypeInitializationException: typ initieraren för "Microsoft. DataTransfer. DIAgentHost. DataSourceCache" utlöste ett undantag. ---> system .net. Sockets. SocketException: ett oåterkalleligt fel inträffade under en databas sökning på system .net. DNS. GetAddrInfo (sträng namn).

#### <a name="cause"></a>Orsak

Problemet uppstår vanligt vis när localhost är löst.

#### <a name="resolution"></a>Lösning

Använd localhost IP-adress 127.0.0.1 som värd för filen och Lös problemet.

### <a name="self-hosted-setup-failed"></a>Installation av egen värd misslyckades    

#### <a name="symptoms"></a>Symtom

Det går inte att avinstallera en befintlig IR, installera en ny IR eller uppgradera en befintlig IR till en ny IR.

#### <a name="cause"></a>Orsak

Installationen av integration runtime beror på Windows Installer tjänsten. Installations problem kan uppstå av följande orsaker:
- Otillräckligt med ledigt disk utrymme.
- Saknar behörighet.
- Windows NT-tjänsten är låst.
- PROCESSOR användningen är för hög.
- MSI-filen finns på en långsam nätverks plats.
- Vissa systemfiler eller register har berörings av oavsiktligt.

### <a name="the-ir-service-account-failed-to-fetch-certificate-access"></a>Kontot för IR-tjänsten kunde inte hämta certifikat åtkomst

#### <a name="symptoms"></a>Symtom

När du installerar en IR med egen värd via Microsoft Integration Runtime Configuration Manager genereras ett certifikat med en betrodd certifikat utfärdare (CA). Det gick inte att använda certifikatet för att kryptera kommunikationen mellan två noder och följande fel meddelande visas: 

"Det gick inte att ändra krypterings läget för intranäts kommunikation: det gick inte att tilldela Integration Runtime tjänst kontot åtkomsten till certifikatet \<*certificate name*> . Felkod 103 "

![Skärm bild som visar fel meddelandet "... Det gick inte att bevilja åtkomst till Integration Runtime tjänst konto certifikat.](media/self-hosted-integration-runtime-troubleshoot-guide/integration-runtime-service-account-certificate-error.png)

#### <a name="cause"></a>Orsak

Certifikatet använder KSP-lagring (Key Storage Provider), vilket inte stöds ännu. Till-datum stöder IR-baserad (Cryptographic Service Provider) endast lagring med egen värd.

#### <a name="resolution"></a>Lösning

Vi rekommenderar att du använder CSP-certifikat i det här fallet.

**Lösning 1** 

Kör följande kommando för att importera certifikatet:

`Certutil.exe -CSP "CSP or KSP" -ImportPFX FILENAME.pfx`

![Skärm bild av Certutil-kommandot för att importera certifikatet.](media/self-hosted-integration-runtime-troubleshoot-guide/use-certutil.png)

**Lösning 2** 

Om du vill konvertera certifikatet kör du följande kommandon:

`openssl pkcs12 -in .\xxxx.pfx -out .\xxxx_new.pem -password pass: <EnterPassword>`
`openssl pkcs12 -export -in .\xxxx_new.pem -out xxxx_new.pfx`

Före och efter konvertering:

![Skärm bild av resultatet före certifikat konverteringen.](media/self-hosted-integration-runtime-troubleshoot-guide/before-certificate-change.png)

![Skärm bild av resultatet efter certifikat konverteringen.](media/self-hosted-integration-runtime-troubleshoot-guide/after-certificate-change.png)

### <a name="self-hosted-integration-runtime-version-5x"></a>Egen värd för integration runtime, version 5. x
För uppgraderingen till version 5. x av Azure Data Factory integration runtime med egen värd, kräver vi **.NET Framework runtime 4.7.2** eller senare. På sidan Hämta hittar du hämtnings Länkar för de senaste 4. x-versionerna och de senaste två 5. x versionerna. 

För Azure Data Factory v2-kunder:
- Om automatisk uppdatering är aktiverat och du redan har uppgraderat .NET Framework runtime till 4.7.2 eller senare, uppgraderas den lokala integrerings körningen automatiskt till den senaste 5. x-versionen.
- Om automatisk uppdatering är aktiverat och du inte har uppgraderat .NET Framework runtime till 4.7.2 eller senare, uppgraderas inte den lokala integrerings körningen automatiskt till den senaste 5. x-versionen. Integration runtime med egen värd förblir i den aktuella 4. x-versionen. Du kan se en varning för en .NET Framework runtime-uppgradering i portalen och klienten för lokal integration Runtime.
- Om automatisk uppdatering är inaktiverat och du redan har uppgraderat .NET Framework runtime till 4.7.2 eller senare, kan du manuellt hämta det senaste 5. x och installera det på din dator.
- Om automatisk uppdatering är inaktive rad och du inte har uppgraderat .NET Framework runtime till 4.7.2 eller senare. När du försöker installera den automatiskt värdbaserade integrerings körningen 5. x och registrera nyckeln måste du först uppgradera .NET Framework runtime-versionen.


För Azure Data Factory v1-kunder:
- Integration runtime 5. X med egen värd stöder inte Azure Data Factory v1.
- Den egna värdbaserade integrerings körningen uppgraderas automatiskt till den senaste versionen av 4. x. Och den senaste versionen av 4. x upphör att gälla. 
- Om du försöker installera den automatiskt värdbaserade integrerings körningen 5. x och registrera nyckeln får du ett meddelande om att integration runtime 5. x med egen värd inte stöder Azure Data Factory v1.


## <a name="self-hosted-ir-connectivity-issues"></a>Problem med IR-anslutning via egen värd

### <a name="self-hosted-integration-runtime-cant-connect-to-the-cloud-service"></a>Integration runtime med egen värd kan inte ansluta till moln tjänsten

#### <a name="symptoms"></a>Symtom

När du försöker registrera integration runtime med egen värd visas följande fel meddelande i Configuration Manager:

"Integration Runtime (lokal installation)-noden har påträffat ett fel under registreringen."

![Skärm bild av meddelandet "Integration Runtime (lokal installation)-noden har påträffat ett fel under registreringen".](media/self-hosted-integration-runtime-troubleshoot-guide/unable-to-connect-to-cloud-service.png)

#### <a name="cause"></a>Orsak 

IR med egen värd kan inte ansluta till Server delen för Azure Data Factory Server. Det här problemet orsakas vanligt vis av nätverks inställningar i brand väggen.

#### <a name="resolution"></a>Lösning

1. Kontrol lera om integration runtime-tjänsten körs. Om så är fallet går du till steg 2.
    
   ![Skärm bild som visar att den egen värdbaserade IR-tjänsten körs.](media/self-hosted-integration-runtime-troubleshoot-guide/integration-runtime-service-running-status.png)
    
1. Om ingen proxy har kon figurer ATS på den lokala IR-enheten, vilket är standardinställningen, kör du följande PowerShell-kommando på den dator där den lokala integrerings körningen är installerad:

    ```powershell
    (New-Object System.Net.WebClient).DownloadString("https://wu2.frontend.clouddatahub.net/")
    ```
        
   > [!NOTE]     
   > URL: en för tjänsten kan variera beroende på var Data Factory-instansen finns. Om du vill hitta tjänstens URL väljer du **ADF UI**-  >  **anslutningar**  >  **integrerings körningar**  >  **Redigera egen värd IR**  >    >  **-noder Visa tjänst-URL: er**.
            
    Följande är det förväntade svaret:
            
    ![Skärm bild av PowerShell-kommandots svar.](media/self-hosted-integration-runtime-troubleshoot-guide/powershell-command-response.png)
            
1. Om du inte får det svar du förväntade dig kan du använda någon av följande metoder:
            
    * Om du får ett meddelande om att det inte gick att lösa problemet, finns det ett Domain Name System (DNS-problem). Kontakta nätverks teamet för att åtgärda problemet.
    * Om du får ett meddelande om att SSL/TLS-certifikat inte är betrott kan du [kontrol lera certifikatet](https://wu2.frontend.clouddatahub.net/) för att se om det är betrott på datorn och sedan installera det offentliga certifikatet med hjälp av certifikat hanteraren. Den här åtgärden bör minimera problemet.
    * Gå till **Windows**  >  **logg boken (loggar)**  >  **program-och tjänst loggar**  >  **integration runtime** och kontrol lera om det finns några problem som orsakas av DNS, brand Väggs regel eller företagets nätverks inställningar. Om du upptäcker ett sådant problem ska du stänga anslutningen. Eftersom alla företag har egna anpassade nätverks inställningar, kontaktar du nätverks teamet för att felsöka problemen.

1. Om proxyn har kon figurer ATS på integration runtime med egen värd, kontrollerar du att proxyservern kan komma åt tjänstens slut punkt. Ett exempel kommando finns i [PowerShell, webb förfrågningar och proxyservrar](https://stackoverflow.com/questions/571429/powershell-web-requests-and-proxies).    
                
    ```powershell
    $user = $env:username
    $webproxy = (get-itemproperty 'HKCU:\Software\Microsoft\Windows\CurrentVersion\Internet
    Settings').ProxyServer
    $pwd = Read-Host "Password?" -assecurestring
    $proxy = new-object System.Net.WebProxy
    $proxy.Address = $webproxy
    $account = new-object System.Net.NetworkCredential($user,[Runtime.InteropServices.Marshal]::PtrToStringAuto([Runtime.InteropServices.Marshal]::SecureStringToBSTR($pwd)), "")
    $proxy.credentials = $account
    $url = "https://wu2.frontend.clouddatahub.net/"
    $wc = new-object system.net.WebClient
    $wc.proxy = $proxy
    $webpage = $wc.DownloadData($url)
    $string = [System.Text.Encoding]::ASCII.GetString($webpage)
    $string
    ```

Följande är det förväntade svaret:
            
![Skärm bild av förväntat PowerShell-kommando svar.](media/self-hosted-integration-runtime-troubleshoot-guide/powershell-command-response.png)

> [!NOTE] 
> Synpunkter på proxy:
> * Kontrol lera om proxyservern måste placeras i listan Betrodda mottagare. Om så är fallet ser du till att [dessa domäner](./data-movement-security-considerations.md#firewall-requirements-for-on-premisesprivate-network) finns i listan Betrodda mottagare.
> * Se efter om SSL/TLS-certifikatet "wu2.frontend.clouddatahub.net/" är betrott på proxyservern.
> * Om du använder Active Directory autentisering på proxyn ändrar du tjänst kontot till det användar konto som har åtkomst till proxyn som "Integration Runtime tjänst".

### <a name="error-message-self-hosted-integration-runtime-nodelogical-self-hosted-ir-is-in-inactive-running-limited-state"></a>Fel meddelande: egen värd för integration runtime-noden/den logiska egna IR-noden som är inaktiv/"körs (begränsad)"

#### <a name="cause"></a>Orsak 

Den egna värdbaserade integrerade runtime-noden kan ha statusen **inaktiv**, som visas på följande skärm bild:

![Skärm bild av den egna värdbaserade integrerade runtime-noden med inaktiv status](media/self-hosted-integration-runtime-troubleshoot-guide/inactive-self-hosted-ir-node.png)

Det här problemet uppstår när noder inte kan kommunicera med varandra.

#### <a name="resolution"></a>Lösning

1. Logga in på den nod-värdbaserade virtuella datorn (VM). Under **program-och tjänst loggar**  >  **integration runtime** öppnar du Loggboken och filtrerar fel loggarna.

1. Kontrol lera om det finns följande fel i fel loggen: 
    
    ```
    System.ServiceModel.EndpointNotFoundException: Could not connect to net.tcp://xxxxxxx.bwld.com:8060/ExternalService.svc/WorkerManager. The connection attempt lasted for a time span of 00:00:00.9940994. TCP error code 10061: No connection could be made because the target machine actively refused it 10.2.4.10:8060. 
    System.Net.Sockets.SocketException: No connection could be made because the target machine actively refused it. 
    10.2.4.10:8060
    at System.Net.Sockets.Socket.DoConnect(EndPoint endPointSnapshot, SocketAddress socketAddress)
    at System.Net.Sockets.Socket.Connect(EndPoint remoteEP)
    at System.ServiceModel.Channels.SocketConnectionInitiator.Connect(Uri uri, TimeSpan timeout)
    ```
       
1. Om det här felet visas kör du följande kommando i ett kommando tolks fönster: 

   ```
   telnet 10.2.4.10 8060
   ```
   
1. Om du får den "Det gick inte att öppna anslutningen till värd kommandot" som visas på följande skärm bild kan du kontakta IT-avdelningen för att få hjälp med att åtgärda problemet. När du har lyckats använda Telnet kontaktar du Microsoft Support om du fortfarande har problem med integration runtime-nodens status.
        
   ![Skärm bild av kommando rads felet "Det gick inte att öppna anslutningen till värden".](media/self-hosted-integration-runtime-troubleshoot-guide/command-line-error.png)
        
1. Kontrol lera om fel loggen innehåller följande post:

    ```
    Error log: Cannot connect to worker manager: net.tcp://xxxxxx:8060/ExternalService.svc/ No DNS entries exist for host azranlcir01r1. No such host is known Exception detail: System.ServiceModel.EndpointNotFoundException: No DNS entries exist for host xxxxx. ---> System.Net.Sockets.SocketException: No such host is known at System.Net.Dns.GetAddrInfo(String name) at System.Net.Dns.InternalGetHostByName(String hostName, Boolean includeIPv6) at System.Net.Dns.GetHostEntry(String hostNameOrAddress) at System.ServiceModel.Channels.DnsCache.Resolve(Uri uri) --- End of inner exception stack trace --- Server stack trace: at System.ServiceModel.Channels.DnsCache.Resolve(Uri uri)
    ```
    
1. Lös problemet genom att prova en eller båda av följande metoder:
    - Lägg till alla noder i samma domän.
    - Lägg till IP-adressen som värd mappning i alla värdar för den värdbaserade virtuella datorn.

### <a name="connectivity-issue-between-the-self-hosted-ir-and-your-data-factory-instance-or-the-self-hosted-ir-and-the-data-source-or-sink"></a>Anslutnings problem mellan den egen värdbaserade IR-instansen, din Data Factory-instans eller den egna IR-instansen och data källan eller sinken

För att felsöka problem med nätverks anslutningen bör du veta hur du samlar in nätverks spårningen, hur du använder det och [analyserar Microsoft Network Monitor (Netmon)-spårningen](#analyze-the-netmon-trace) innan du använder Netmon-verktygen i reala fall från den egna IR-enheten.

#### <a name="symptoms"></a>Symtom

Ibland kan du behöva felsöka vissa anslutnings problem mellan IR och din Data Factory-instans, som du ser i följande skärm bild eller mellan den egna IR-IR-filen och data källan eller sinken. 

![Skärm bild av meddelandet "behandlade HTTP-begäran misslyckades"](media/self-hosted-integration-runtime-troubleshoot-guide/http-request-error.png)

I båda instanserna kan du stöta på följande fel:

* "Kopieringen misslyckades med felet: typ = Microsoft. DataTransfer. Common. Shared. HybridDeliveryException, Message = det går inte att ansluta till SQL Server:" IP-adress ""

* Ett eller flera fel inträffade. Ett fel uppstod när begäran skickades. Den underliggande anslutningen stängdes: ett oväntat fel inträffade vid mottagning. Det gick inte att läsa data från transport anslutningen: en befintlig anslutning tvingades stänga av den fjärranslutna värden. En befintlig anslutning tvingades stängas av fjärrvärdets aktivitets-ID. "

#### <a name="resolution"></a>Lösning

När du stöter på föregående fel kan du felsöka dem genom att följa anvisningarna i det här avsnittet.

- Samla in en Netmon-spårning för analys: 

    1. Du kan ställa in filtret för att se en återställning från servern till klient sidan. I följande exempel skärm bild kan du se att Server sidan är Data Factory-servern.

        ![Skärm bild av Data Factory-servern.](media/self-hosted-integration-runtime-troubleshoot-guide/data-factory-server.png)

    1. När du hämtar återställnings paketet kan du hitta konversationen genom att följa Transmission Control Protocol (TCP).

        ![Skärm bild av TCP-konversationen.](media/self-hosted-integration-runtime-troubleshoot-guide/find-conversation.png)

    1. Hämta konversationen mellan klienten och Data Factory servern nedan genom att ta bort filtret.

        ![Skärm bild av konversations information.](media/self-hosted-integration-runtime-troubleshoot-guide/get-conversation.png)

- En analys av NetMon-spåret som du har samlat in visar att den totala Time to Live (TTL) är 64. Enligt de värden som anges i artikeln [grundläggande om IP-Time to Live (TTL) och antal hopp gränser](https://packetpushers.net/ip-time-to-live-and-hop-limit-basics/) , som extraheras i följande lista, kan du se att det är Linux-systemet som återställer paketet och orsakar från koppling.

    Standardvärden för TTL och hopp varierar mellan olika operativ system, vilket visas här:
    - Linux kernel 2,4 (circa 2001): 255 för TCP, User Datagram Protocol (UDP) och Internet Control Message Protocol (ICMP)
    - Linux kernel 4,10 (2015): 64 för TCP, UDP och ICMP
    - Windows XP (2001): 128 för TCP, UDP och ICMP
    - Windows 10 (2015): 128 för TCP, UDP och ICMP
    - Windows Server 2008:128 för TCP, UDP och ICMP
    - Windows Server 2019 (2018): 128 för TCP, UDP och ICMP
    - macOS (2001): 64 för TCP, UDP och ICMP

    ![Skärm bild som visar ett TTL-värde på 61.](media/self-hosted-integration-runtime-troubleshoot-guide/ttl-61.png)
    
    I föregående exempel visas TTL-värdet som 61 i stället för 64, eftersom när nätverks paketet når målet måste det gå igenom olika hopp, till exempel routrar eller nätverks enheter. Antalet routrar eller nätverks enheter dras av för att producera det slutliga TTL-värdet.
    
    I så fall kan du se att en återställning kan skickas från Linux-systemet med TTL 64.

-  Om du vill kontrol lera var återställnings enheten kan komma från kontrollerar du det fjärde hoppet från den egna IR-enheten.
 
    *Nätverks paket från Linux system A med TTL 64-> B TTL 64 minus 1 = 63-> C TTL 63 minus 1 = 62-> TTL 62 minus 1 = 61 lokal IR*

- I en idealisk situation skulle TTL-hoppnummer vara 128, vilket innebär att operativ systemet Windows kör din Data Factory-instans. Som du ser i följande exempel, *128 minus 107 = 21 hopp*, vilket innebär att 21 hopp för paketet har skickats från Data Factory-instansen till den egna IR-instansen i TCP 3-hand skakningen.
 
    ![Skärm bild som visar ett TTL-värde på 107.](media/self-hosted-integration-runtime-troubleshoot-guide/ttl-107.png)

    Därför måste du be nätverks teamet att kontrol lera för att se vad det fjärde hoppet är från det egna IR-nätverket. Om det är brand väggen, som i Linux-systemet, kontrollerar du eventuella loggar för att se varför enheten återställer paketet efter TCP 3-handskakningen. 
    
    Om du är osäker på var du ska undersöka kan du försöka hämta Netmon-spåret från både den egna IR-IR-filen och brand väggen under den problematiska tiden. Den här metoden hjälper dig att ta reda på vilken enhet som kan ha återställt paketet och orsakade anslutningen. I så fall måste du också engagera ditt nätverks team för att gå vidare.

### <a name="analyze-the-netmon-trace"></a>Analysera Netmon-spårningen

> [!NOTE] 
> Följande instruktioner gäller för Netmon-spårningen. Eftersom Netmon-spårningen för närvarande inte stöds kan du använda wireshark för detta ändamål.

När du försöker Telnet **8.8.8.8 888** med det insamlade Netmon-spåret bör du se spårningen i följande skärm bilder:

![Skärm bild som visar fel meddelandet "Det gick inte att öppna anslutningen till värden på port 888".](media/self-hosted-integration-runtime-troubleshoot-guide/netmon-trace-1.png)

![Skärm bild som visar en beskrivning av NetMon-spåret.](media/self-hosted-integration-runtime-troubleshoot-guide/netmon-trace-2.png)
 

Föregående bilder visar att du inte kunde upprätta en TCP-anslutning till **8.8.8.8** -Server sidan på port **888**, så att du ser två **SynReTransmit** ytterligare paket där. Eftersom käll **-HOST2** inte kunde ansluta till **8.8.8.8** med det första paketet fortsätter det att försöka upprätta anslutningen.

> [!TIP]
> Prova följande lösning för att upprätta anslutningen:
> 1. Välj **load filter**  >  **standard filter**  >  **adresser**  >  **IPv4-adresser**.
> 1. Om du vill använda filtret anger du **IPv4. address = = 8.8.8.8** och väljer sedan **Använd**. Du bör sedan se kommunikationen från den lokala datorn till målet **8.8.8.8**.

![Skärm bild som visar filter adresser.](media/self-hosted-integration-runtime-troubleshoot-guide/filter-addresses-1.png)
        
![Skärm bild som visar fler filter adresser.](media/self-hosted-integration-runtime-troubleshoot-guide/filter-addresses-2.png)

Lyckade scenarier visas i följande exempel: 

- Om du kan Telnet **8.8.8.8 53** utan problem finns det en lyckad TCP 3-handskakning, och sessionen slutförs med en TCP 4-handskakning.

    ![Skärm bild som visar ett lyckat anslutnings scenario.](media/self-hosted-integration-runtime-troubleshoot-guide/good-scenario-1.png)
     
    ![Skärm bild som visar information om ett lyckat anslutnings scenario.](media/self-hosted-integration-runtime-troubleshoot-guide/good-scenario-2.png)

- Föregående TCP 3-handskakning skapar följande arbets flöde:

    ![Diagram över ett arbets flöde för en TCP 3 hand skakning.](media/self-hosted-integration-runtime-troubleshoot-guide/tcp-3-handshake-workflow.png)
 
- TCP 4-handskakningen för att avsluta sessionen illustreras av följande arbets flöden:

    ![Skärm bild av information om TCP 4 hand skakning.](media/self-hosted-integration-runtime-troubleshoot-guide/tcp-4-handshake.png)

    ![Diagram över ett arbets flöde för TCP 4-handskakningen.](media/self-hosted-integration-runtime-troubleshoot-guide/tcp-4-handshake-workflow.png) 

### <a name="microsoft-email-notification-about-updating-your-network-configuration"></a>Microsoft e-postavisering om uppdatering av din nätverks konfiguration

Du kan få följande e-postavisering, som rekommenderar att du uppdaterar nätverks konfigurationen för att tillåta kommunikation med nya IP-adresser för Azure Data Factory med 8 november 2020:

   ![Skärm bild av Microsoft-e-postaviseringar som begär uppdatering av nätverks konfiguration.](media/self-hosted-integration-runtime-troubleshoot-guide/email-notification.png)

#### <a name="determine-whether-this-notification-affects-you"></a>Ta reda på om det här meddelandet påverkar dig

Detta meddelande gäller följande scenarier:

##### <a name="scenario-1-outbound-communication-from-a-self-hosted-integration-runtime-thats-running-on-premises-behind-a-corporate-firewall"></a>Scenario 1: utgående kommunikation från en egen värd för integration runtime som körs lokalt bakom en företags brand vägg

Så här avgör du om du har påverkat:

- Du påverkas *inte* om du definierar brand Väggs regler baserade på fullständigt kvalificerade domän namn (FQDN) som använder den metod som beskrivs i [Konfigurera en brand Väggs konfiguration och listan över tillåtna IP-adresser](data-movement-security-considerations.md#firewall-configurations-and-allow-list-setting-up-for-ip-address-of-gateway).

- Du *påverkas om du uttryckligen* aktiverar listan över tillåtna för utgående IP-adresser i företags brand väggen.

   Om du påverkas kan du vidta följande åtgärder: senast den 8 november 2020, meddela din nätverks infrastruktur team att uppdatera nätverks konfigurationen så att den använder de senaste Data Factory-IP-adresserna. Om du vill hämta de senaste IP-adresserna går du till [identifiera tjänst etiketter med hjälp av nedladdnings bara JSON-filer](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files).

##### <a name="scenario-2-outbound-communication-from-a-self-hosted-integration-runtime-thats-running-on-an-azure-vm-inside-a-customer-managed-azure-virtual-network"></a>Scenario 2: utgående kommunikation från en egen värd för integration runtime som körs på en virtuell Azure-dator i ett kundhanterat Azure Virtual Network

Så här avgör du om du har påverkat:

- Kontrol lera om du har regler för utgående nätverks säkerhets grupper (NSG) i ett privat nätverk som innehåller integration runtime med egen värd. Om det inte finns några utgående begränsningar påverkas inte.

- Om du har regler för utgående trafik kontrollerar du om du använder tjänst taggar. Om du använder service märken påverkas inte du. Du behöver inte ändra eller lägga till något eftersom det nya IP-intervallet är under dina befintliga service märken. 

  ![Skärm bild av en mål kontroll som visar DataFactory som mål.](media/self-hosted-integration-runtime-troubleshoot-guide/destination-check.png)

- Du *påverkas om du uttryckligen* aktiverar listan över tillåtna för utgående IP-adresser i NSG-regler på det virtuella Azure-nätverket.

   Om du påverkas kan du vidta följande åtgärder: senast den 8 november 2020, meddela din nätverks infrastruktur team att uppdatera NSG-reglerna i din Azure Virtual Network-konfiguration så att de använder de senaste IP-adresserna för Data Factory. Om du vill hämta de senaste IP-adresserna går du till [identifiera tjänst etiketter med hjälp av nedladdnings bara JSON-filer](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files).

##### <a name="scenario-3-outbound-communication-from-ssis-integration-runtime-in-a-customer-managed-azure-virtual-network"></a>Scenario 3: utgående kommunikation från SSIS Integration Runtime i ett kundhanterat Azure Virtual Network

Så här avgör du om du har påverkat:

- Kontrol lera om det finns utgående NSG-regler i ett privat nätverk som innehåller SQL Server Integration Services (SSIS) Integration Runtime. Om det inte finns några utgående begränsningar påverkas inte.

- Om du har regler för utgående trafik kontrollerar du om du använder tjänst taggar. Om du använder service märken påverkas inte du. Du behöver inte ändra eller lägga till något eftersom det nya IP-intervallet finns under dina befintliga service märken.

- Du *påverkas om du uttryckligen* aktiverar listan över tillåtna för utgående IP-adresser i NSG-regler på det virtuella Azure-nätverket.

  Om du påverkas kan du vidta följande åtgärder: senast den 8 november 2020, meddela din nätverks infrastruktur team att uppdatera NSG-reglerna i din Azure Virtual Network-konfiguration så att de använder de senaste IP-adresserna för Data Factory. Om du vill hämta de senaste IP-adresserna går du till [identifiera tjänst etiketter med hjälp av nedladdnings bara JSON-filer](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files).

### <a name="couldnt-establish-a-trust-relationship-for-the-ssltls-secure-channel"></a>Det gick inte att upprätta en förtroende relation för den säkra SSL/TLS-kanalen 

#### <a name="symptoms"></a>Symtom

Det gick inte att ansluta till den Azure Data Factory tjänsten via den egna värd tjänsten.

När du kontrollerar IR-händelseloggen med egen värd eller klient meddelande loggarna i tabellen CustomLogEvent, visas följande fel meddelande:

"Den underliggande anslutningen stängdes: det gick inte att upprätta en förtroende relation för den säkra SSL/TLS-kanalen. Fjärrcertifikatet är ogiltigt enligt validerings proceduren. "

Det enklaste sättet att kontrol lera Server certifikatet för tjänsten Data Factory är att öppna URL: en för Data Factory tjänst i webbläsaren. Du kan till exempel öppna [länken kontrol lera Server certifikat](https://eu.frontend.clouddatahub.net/) på den dator där den lokala IR-enheten är installerad och sedan Visa information om Server certifikatet.

  ![Skärm bild av fönstret kontrol lera Server certifikat i Azure Data Factorys tjänsten.](media/self-hosted-integration-runtime-troubleshoot-guide/server-certificate.png)

  ![Skärm bild av fönstret för att kontrol lera Server certifierings Sök vägen.](media/self-hosted-integration-runtime-troubleshoot-guide/certificate-path.png)

#### <a name="cause"></a>Orsak

Det finns två möjliga orsaker till det här problemet:

- Orsak 1: rot-CA: n för det Data Factory tjänst Server certifikatet är inte betrott på den dator där den lokala IR-enheten är installerad. 
- Orsak 2: du använder en proxyserver i din miljö, Server certifikatet för tjänsten Data Factory ersätts av proxyservern och det ersatta Server certifikatet är inte betrott av den dator där IR för lokal installation är installerad.

#### <a name="resolution"></a>Lösning

- För orsak 1: se till att det Data Factory Server certifikatet och dess certifikat kedja är betrodda av den dator där IR för egen värd är installerad.
- Orsak 2: lita antingen på den ersatta rot certifikat utfärdaren på den lokala IR-datorn eller konfigurera proxyn så att den inte ersätter Data Factory Server certifikatet.

Mer information om att lita på certifikat i Windows finns i [installera det betrodda rot certifikatet](/skype-sdk/sdn/articles/installing-the-trusted-root-certificate).

#### <a name="additional-information"></a>Ytterligare information
Vi har lanserat ett nytt SSL-certifikat, som är signerat från DigiCert. Kontrol lera om den globala rot-DigiCert finns i den betrodda rot certifikat utfärdaren.

  ![Skärm bild som visar mappen DigiCert global root G2 i katalogen betrodda rot certifikat utfärdare.](media/self-hosted-integration-runtime-troubleshoot-guide/trusted-root-ca-check.png)

Om den inte finns i den betrodda rot certifikat utfärdaren kan [du ladda ned den här](http://cacerts.digicert.com/DigiCertGlobalRootG2.crt ). 


## <a name="self-hosted-ir-sharing"></a>Delning av lokalt installerad integrationskörning (IR)

### <a name="sharing-a-self-hosted-ir-from-a-different-tenant-is-not-supported"></a>Det finns inte stöd för att dela en lokal IR-anslutning från en annan klient 

#### <a name="symptoms"></a>Symtom

Du kan märka andra data fabriker (på olika klienter) när du försöker dela IR-IR från Azure Data Factory-gränssnittet, men du kan inte dela den över data fabriker som finns på olika klienter.

#### <a name="cause"></a>Orsak

IR med egen värd kan inte delas mellan klienter.

## <a name="next-steps"></a>Nästa steg

Om du behöver hjälp med fel sökning kan du prova följande resurser:

*  [Data Factory blogg](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory funktions begär Anden](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure-videor](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Microsoft Q&en sida](/answers/topics/azure-data-factory.html)
*  [Stack Overflow-forum för Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Twitter-information om Data Factory](https://twitter.com/hashtag/DataFactory)
*  [Prestanda guide för att mappa data flöden](concepts-data-flow-performance.md)
