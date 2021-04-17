---
title: Azure Storage Explorer felsökningsguide | Microsoft Docs
description: Översikt över felsökningstekniker för Azure Storage Explorer
services: storage
author: Deland-Han
manager: dcscontentpm
ms.service: storage
ms.topic: troubleshooting
ms.date: 07/28/2020
ms.author: delhan
ms.openlocfilehash: dfc8fe0f1b4bc043feecd5c76340d48bc5421854
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107568547"
---
# <a name="azure-storage-explorer-troubleshooting-guide"></a>Felsökningsguide för Azure Storage Explorer

Microsoft Azure Storage Explorer är en fristående app som gör det enkelt att arbeta med Azure Storage-data i Windows, macOS och Linux. Appen kan anslutas till lagringskonton som finns i Azure, nationella moln och Azure Stack.

Den här guiden sammanfattar lösningar på problem som ofta förekommer i Storage Explorer.

## <a name="azure-rbac-permissions-issues"></a>Problem med Azure RBAC-behörigheter

Rollbaserad åtkomstkontroll i [Azure RBAC](../../role-based-access-control/overview.md) möjliggör mycket detaljerad åtkomsthantering av Azure-resurser genom att kombinera uppsättningar med behörigheter i _roller._ Här är några strategier för att få Azure RBAC att fungera optimalt i Storage Explorer.

### <a name="how-do-i-access-my-resources-in-storage-explorer"></a>Hur gör jag för att åtkomst till mina resurser i Storage Explorer?

Om du har problem med att komma åt lagringsresurser via Azure RBAC kanske du inte har tilldelats lämpliga roller. I följande avsnitt beskrivs de behörigheter Storage Explorer för närvarande kräver för åtkomst till dina lagringsresurser. Kontakta din Azure-kontoadministratör om du inte är säker på att du har rätt roller eller behörigheter.

#### <a name="read-listget-storage-accounts-permissions-issue"></a>Problem med behörigheterna "Read: List/Get Storage Account(s)"

Du måste ha behörighet att lista lagringskonton. För att få den här behörigheten måste du tilldelas _rollen_ Läsare.

#### <a name="list-storage-account-keys"></a>Lista lagringskontonycklar

Storage Explorer kan också använda kontonycklar för att autentisera begäranden. Du kan få åtkomst till kontonycklar via mer kraftfulla roller, till exempel _rollen_ Deltagare.

> [!NOTE]
> Åtkomstnycklar beviljar obegränsad behörighet till alla som har dem. Därför rekommenderar vi inte att du lämnar ut dessa nycklar till kontoanvändare. Om du behöver återkalla åtkomstnycklar kan du återskapa dem från [Azure Portal](https://portal.azure.com/).

#### <a name="data-roles"></a>Dataroller

Du måste tilldelas minst en roll som ger åtkomst till att läsa data från resurser. Om du till exempel vill visa eller ladda ned blobar behöver du minst rollen _Storage Blob Data Reader._

### <a name="why-do-i-need-a-management-layer-role-to-see-my-resources-in-storage-explorer"></a>Varför behöver jag en hanteringslagerroll för att se mina resurser i Storage Explorer?

Azure Storage har två åtkomstlager: _hantering_ och _data_. Prenumerationer och lagringskonton nås via hanteringslagret. Containrar, blobar och andra dataresurser nås via datalagret. Om du till exempel vill hämta en lista över dina lagringskonton från Azure skickar du en begäran till hanteringsslutpunkten. Om du vill ha en lista över blobcontainrar i ett konto skickar du en begäran till lämplig tjänstslutpunkt.

Azure-roller kan ge dig behörighet för hanterings- eller datalageråtkomst. Rollen Läsare ger till exempel skrivskyddad åtkomst till resurser på hanteringslager.

Strikt sett ger rollen Läsare inga behörigheter för datalager och är inte nödvändig för åtkomst till datalagret.

Storage Explorer gör det enkelt att komma åt dina resurser genom att samla in nödvändig information för att ansluta till dina Azure-resurser. Om du till exempel vill visa dina blobcontainrar Storage Explorer en begäran om att "lista containrar" till blobtjänstens slutpunkt. För att få den slutpunkten Storage Explorer i listan över prenumerationer och lagringskonton som du har åtkomst till. För att hitta dina prenumerationer och lagringskonton Storage Explorer också åtkomst till hanteringslagret.

Om du inte har en roll som beviljar behörigheter för hanteringslager kan Storage Explorer hämta den information som behövs för att ansluta till datalagret.

### <a name="what-if-i-cant-get-the-management-layer-permissions-i-need-from-my-administrator"></a>Vad händer om jag inte kan få de behörigheter för hanteringslagret som jag behöver från min administratör?

Om du vill komma åt blobcontainrar, ADLS Gen2 eller kataloger, eller köer, kan du ansluta till dessa resurser med dina autentiseringsuppgifter för Azure.

1. Öppna dialogrutan Anslut.
1. Välj den resurstyp som du vill ansluta till.
1. Välj **Logga in med Azure Active Directory (Azure AD).** Välj **Nästa**.
1. Välj det användarkonto och den klientorganisation som är associerad med den resurs som du kopplar till. Välj **Nästa**.
1. Ange URL:en till resursen och ange ett unikt visningsnamn för anslutningen. Välj **Nästa** och sedan **Anslut.**

För andra resurstyper har vi för närvarande inte någon Azure RBAC-relaterad lösning. Som en tillfällig lösning kan du begära en SAS-URL och sedan ansluta till resursen genom att följa dessa steg:

1. Öppna dialogrutan Anslut.
1. Välj den resurstyp som du vill ansluta till.
1. Välj **Signatur för delad åtkomst (SAS).** Välj **Nästa**.
1. Ange DEN SAS-URL som du fick och ange ett unikt visningsnamn för anslutningen. Välj **Nästa** och sedan **Anslut.**
 
Mer information om hur du kopplar till resurser finns i [Koppla till en enskild resurs.](../../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=linux#attach-to-an-individual-resource)

### <a name="recommended-azure-built-in-roles"></a>Rekommenderade inbyggda Azure-roller

Det finns flera inbyggda Azure-roller som kan ge de behörigheter som krävs för att använda Storage Explorer. Några av dessa roller är:
- [Ägare:](../../role-based-access-control/built-in-roles.md#owner)Hantera allt, inklusive åtkomst till resurser.
- [Deltagare:](../../role-based-access-control/built-in-roles.md#contributor)Hantera allt, exklusive åtkomst till resurser.
- [Läsare:](../../role-based-access-control/built-in-roles.md#reader)Läsa och lista resurser.
- [Lagringskontodeltagare:](../../role-based-access-control/built-in-roles.md#storage-account-contributor)Fullständig hantering av lagringskonton.
- [Storage Blob Data-ägare:](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)Fullständig åtkomst till Azure Storage blobcontainrar och data.
- [Storage Blob Data-deltagare:](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)Läsa, skriva och ta bort Azure Storage containrar och blobar.
- [Storage Blob Data-läsare:](../../role-based-access-control/built-in-roles.md#storage-blob-data-reader)Läsa och lista Azure Storage containrar och blobar.

> [!NOTE]
> Rollerna Ägare, Deltagare och Lagringskontodeltagare beviljar åtkomst till kontonyckeln.

## <a name="error-self-signed-certificate-in-certificate-chain-and-similar-errors"></a>Fel: Självloggat certifikat i certifikatkedjan (och liknande fel)

Certifikatfel uppstår vanligtvis i någon av följande situationer:

- Appen ansluts via en _transparent proxy_. Det innebär att en server (till exempel företagsservern) fångar upp HTTPS-trafik, dekrypterar den och sedan krypterar den med hjälp av ett själv signerat certifikat.
- Du kör ett program som matar in ett själv signerat TLS/SSL-certifikat i de HTTPS-meddelanden som du får. Exempel på program som matar in certifikat är antivirusprogram och kontrollsystem för nätverkstrafik.

När Storage Explorer ser ett själv signerat eller ej betroddt certifikat vet det inte längre om det mottagna HTTPS-meddelandet har ändrats. Om du har en kopia av det själv signerade certifikatet kan du instruera Storage Explorer att lita på det genom att följa dessa steg:

1. Hämta en Base-64-kodad X.509-kopia (.cer) av certifikatet.
2. Gå till **Redigera** SSL-certifikat Importera certifikat och använd  >    >  sedan filväljaren för att hitta, välja och öppna CER-filen.

Det här problemet kan också inträffa om det finns flera certifikat (rot och mellanliggande). För att åtgärda det här felet måste båda certifikaten läggas till.

Om du är osäker på var certifikatet kommer från följer du dessa steg för att hitta det:

1. Installera OpenSSL.
    * [Windows:](https://slproweb.com/products/Win32OpenSSL.html)Alla ljusversioner bör vara tillräckliga.
    * Mac och Linux: Bör ingå i ditt operativsystem.
2. Kör OpenSSL.
    * Windows: Öppna installationskatalogen, välj **/bin/** och dubbelklicka sedan på **openssl.exe**.
    * Mac och Linux: Kör `openssl` från en terminal.
3. Kör `s_client -showcerts -connect microsoft.com:443`.
4. Leta efter självsignerade certifikat. Om du är osäker på vilka certifikat som är själv signerade kan du anteckna var ämnet och `("s:")` utfärdaren `("i:")` är desamma.
5. När du hittar själv signerade certifikat kopierar och klistrar du in allt från (och inklusive) `-----BEGIN CERTIFICATE-----` till `-----END CERTIFICATE-----` en ny .cer-fil för var och en.
6. Öppna Storage Explorer och gå till **Redigera**  >  **SSL-certifikat**  >  **Importera certifikat.** Använd sedan filväljaren för att hitta, markera och öppna cer-filerna som du skapade.

Om du inte hittar några själv signerade certifikat genom att följa dessa steg kan du kontakta oss via feedbackverktyget. Du kan också Storage Explorer från kommandoraden med hjälp av `--ignore-certificate-errors` flaggan . När den öppnas med den här Storage Explorer ignorerar certifikatfel.

## <a name="sign-in-issues"></a>Inloggningsproblem

### <a name="understanding-sign-in"></a>Förstå inloggning

Kontrollera att du har läst dokumentationen [Logga in Storage Explorer](./storage-explorer-sign-in.md) inloggning.

### <a name="frequently-having-to-reenter-credentials"></a>Ofta måste du återautentiseringsuppgifter

Att behöva ange autentiseringsuppgifter igen beror troligen på principer för villkorlig åtkomst som angetts av AAD-administratören. När Storage Explorer ber dig att återautentiseringsuppgifter från kontopanelen  bör du se länken Felinformation.... Klicka på det för att se varför Storage Explorer ber dig att återautentiseringsuppgifter. Fel med principer för villkorlig åtkomst som kräver omkodning av autentiseringsuppgifter kan se ut ungefär så här:
- Uppdateringstoken har upphört att gälla...
- Du måste använda multifaktorautentisering för att komma åt...
- På grund av en konfigurationsändring som gjorts av administratören...

För att minska frekvensen för att behöva återinloggningar av autentiseringsuppgifter på grund av fel som de ovan måste du prata med AAD-administratören.

### <a name="conditional-access-policies"></a>Villkorliga åtkomstprinciper

Om du har principer för villkorlig åtkomst som måste uppfyllas  för ditt konto kontrollerar du att du använder standardwebbläsaren **för inställningen Logga in med.** Information om den inställningen finns i [Ändra var inloggning sker.](./storage-explorer-sign-in.md#changing-where-sign-in-happens)

### <a name="unable-to-acquire-token-tenant-is-filtered-out"></a>Det går inte att hämta token, klientorganisationen filtreras bort

Om du ser ett felmeddelande om att en token inte kan förvärvas eftersom en klientorganisation är filtrerad innebär det att du försöker komma åt en resurs som finns i en klientorganisation som du har filtrerat bort. Om du vill avfiltrera klienten går du **till** kontopanelen och kontrollerar att kryssrutan för den klientorganisation som anges i felet är markerad. Se Hantera konton [för mer](./storage-explorer-sign-in.md#managing-accounts) information om hur du filtrerar klienter i Storage Explorer.

## <a name="authentication-library-failed-to-start-properly"></a>Autentiseringsbiblioteket kunde inte starta korrekt

Om du vid start ser ett felmeddelande om att Storage Explorer [autentiseringsbiblioteket](../../vs-azure-tools-storage-manage-with-storage-explorer.md#prerequisites)inte startade korrekt kontrollerar du att installationsmiljön uppfyller alla krav. Att inte uppfylla kraven är den troligaste orsaken till det här felmeddelandet.

Om du tror att din installationsmiljö uppfyller alla krav öppnar [du ett ärende på GitHub.](https://github.com/Microsoft/AzureStorageExplorer/issues/new) När du öppnar problemet ska du se till att inkludera:
- Ditt operativsystem.
- Vilken version Storage Explorer du försöker använda.
- Om du har kontrollerat förutsättningarna.
- [Autentiseringsloggar](#authentication-logs) från en misslyckad start Storage Explorer. Utförlig autentiseringsloggning aktiveras automatiskt när den här typen av fel inträffar.

### <a name="blank-window-when-using-integrated-sign-in"></a>Tomt fönster vid användning av integrerad inloggning

Om du har valt att använda **integrerad** inloggning och ser ett tomt fönster måste du förmodligen växla till en annan inloggningsmetod. Tomma dialogrutor för inloggning inträffar oftast när en Active Directory Federation Services (AD FS) (ADFS)-server uppmanar Storage Explorer att utföra en omdirigering som inte stöds av Electron.

Om du vill ändra till en annan inloggningsmetod genom att ändra **inställningen Logga in med** under **Inställningar**  >  **Inloggning**  >  **för program.** Information om de olika typerna av inloggningsmetoder finns i [Ändra var inloggning sker.](./storage-explorer-sign-in.md#changing-where-sign-in-happens)

### <a name="reauthentication-loop-or-upn-change"></a>Omauthenticeringsloop eller UPN-ändring

Om du är i en omauthenticeringsloop eller har ändrat UPN för ett av dina konton kan du prova följande steg:

1. Öppna Storage Explorer
2. Gå till Hjälp > Återställning
3. Kontrollera att minst Autentisering är markerat. Du kan avmarkera andra objekt som du inte vill återställa.
4. Klicka på knappen Återställ
5. Starta Storage Explorer och försök att logga in igen.

Om du fortsätter att ha problem när du har gjort en återställning kan du prova följande steg:

1. Öppna Storage Explorer
2. Ta bort alla konton och stäng Storage Explorer.
3. Ta bort `.IdentityService` mappen från datorn. I Windows finns mappen på `C:\users\<username>\AppData\Local` . För Mac och Linux hittar du mappen i roten för användarkatalogen.
4. Om du kör Mac eller Linux måste du också ta bort posten Microsoft.Developer.IdentityService från operativsystemets nyckellager. På Mac är nyckelarkivet *Programmet Nyckelring.* I Linux kallas programmet vanligtvis _nyckelring, men_ namnet kan variera beroende på din distribution.
6. Starta Storage Explorer och försök att logga in igen.

### <a name="macos-keychain-errors-or-no-sign-in-window"></a>macOS: nyckelringsfel eller inget inloggningsfönster

MacOS-nyckelringen kan ibland ange ett tillstånd som orsakar problem för Storage Explorer autentiseringsbiblioteket. Följ dessa steg för att få nyckelringen ur det här tillståndet:

1. Stäng Storage Explorer.
2. Öppna nyckelringen (tryck på Kommando +blanksteg, skriv **nyckelring och** tryck på Retur).
3. Välj nyckelringen "logga in".
4. Välj hänglåsikonen för att låsa nyckelringen. (Hänglås visas låst när processen är klar. Det kan ta några sekunder, beroende på vilka appar du har öppen).

    ![Hänglåsikon](./media/storage-explorer-troubleshooting/unlockingkeychain.png)

5. Öppna Storage Explorer.
6. Du uppmanas att ange ett meddelande som "Tjänsthubb vill komma åt nyckelringen". Ange lösenordet för ditt Mac-administratörskonto **och välj** Tillåt **alltid (eller** **Tillåt om Tillåt** alltid inte är tillgängligt).
7. Försök att logga in.

### <a name="default-browser-doesnt-open"></a>Standardwebbläsaren öppnas inte

Om standardwebbläsaren inte öppnas när du försöker logga in kan du prova alla följande metoder:
- Starta om Storage Explorer
- Öppna webbläsaren manuellt innan du startar inloggningen
- Prova att **använda integrerad inloggning . Mer** information om hur du gör detta [finns](./storage-explorer-sign-in.md#changing-where-sign-in-happens) i Ändra var inloggning sker.

### <a name="other-sign-in-issues"></a>Andra inloggningsproblem

Om inget av ovanstående gäller ditt inloggningsproblem eller om de inte kan lösa inloggningsproblemet kan du [öppna ett ärende på GitHub.](https://github.com/Microsoft/AzureStorageExplorer/issues)

### <a name="missing-subscriptions-and-broken-tenants"></a>Saknade prenumerationer och brutna klienter

Om du inte kan hämta dina prenumerationer när du har loggat in kan du prova följande felsökningsmetoder:

* Kontrollera att ditt konto har åtkomst till de prenumerationer som du förväntar dig. Du kan verifiera din åtkomst genom att logga in på portalen för den Azure-miljö som du försöker använda.
* Kontrollera att du har loggat in via rätt Azure-miljö (Azure, Azure China 21Vianet, Azure Tyskland, Azure US Government eller Anpassad miljö).
* Om du är bakom en proxyserver kontrollerar du att du har konfigurerat Storage Explorer proxyservern korrekt.
* Försök att ta bort och lägga till kontot igen.
* Om det finns länken "Mer information" eller "Felinformation" kontrollerar du vilka felmeddelanden som rapporteras för de klienter som misslyckas. Om du inte vet hur du ska svara på felmeddelandena kan du öppna [ett ärende i GitHub.](https://github.com/Microsoft/AzureStorageExplorer/issues)

## <a name="cant-remove-an-attached-storage-account-or-resource"></a>Det går inte att ta bort ett anslutet lagringskonto eller en ansluten resurs

Om du inte kan ta bort ett anslutet konto eller en lagringsresurs via användargränssnittet kan du manuellt ta bort alla anslutna resurser genom att ta bort följande mappar:

* Windows: `%AppData%/StorageExplorer`
* Macos: `/Users/<your_name>/Library/Application Support/StorageExplorer`
* Linux: `~/.config/StorageExplorer`

> [!NOTE]
> Stäng Storage Explorer innan du tar bort dessa mappar.

> [!NOTE]
> Om du någon gång har importerat SSL-certifikat kan du då a upp innehållet i `certs` katalogen. Senare kan du använda säkerhetskopian för att importera SSL-certifikaten igen.

## <a name="proxy-issues"></a>Proxyproblem

Storage Explorer stöder anslutning till Azure Storage via en proxyserver. Här är några förslag om du får problem med att ansluta till Azure via proxy.

> [!NOTE]
> Storage Explorer stöder endast grundläggande autentisering med proxyservrar. Andra autentiseringsmetoder, till exempel NTLM, stöds inte.

> [!NOTE]
> Storage Explorer har inte stöd för autokonfiguration av proxyfiler för att konfigurera proxyinställningar.

### <a name="verify-storage-explorer-proxy-settings"></a>Kontrollera Storage Explorer proxyinställningar

**Konfigurationsinställningen Application → Proxy → Proxy** avgör vilka Storage Explorer hämtar proxykonfigurationen från.

Om du väljer "Använd miljövariabler" måste du ange miljövariablerna eller (miljövariablerna är fallkänsliga, så se till att ange `HTTPS_PROXY` `HTTP_PROXY` rätt variabler). Om dessa variabler är odefinierade eller ogiltiga Storage Explorer inte använda en proxyserver. Starta Storage Explorer när du har modifierat miljövariabler.

Om du väljer "Använd appproxyinställningar" kontrollerar du att proxyinställningarna i appen är korrekta.

### <a name="steps-for-diagnosing-issues"></a>Steg för att diagnostisera problem

Om du fortfarande har problem kan du prova följande felsökningsmetoder:

1. Om du kan ansluta till Internet utan att använda proxyservern kontrollerar du att Storage Explorer fungerar utan att proxyinställningarna är aktiverade. Om Storage Explorer kan det finnas ett problem med proxyservern. Ta hjälp av administratören för att identifiera problemen.
2. Kontrollera att andra program som använder proxyservern fungerar som förväntat.
3. Kontrollera att du kan ansluta till portalen för den Azure-miljö som du försöker använda.
4. Kontrollera att du kan ta emot svar från dina tjänstslutpunkter. Ange en av slutpunkts-URL:erna i webbläsaren. Om du kan ansluta bör du få ett eller `InvalidQueryParameterValue` liknande XML-svar.
5. Kontrollera om någon annan som Storage Explorer med samma proxyserver kan ansluta. Om de kan det kan du behöva kontakta proxyserveradministratören.

### <a name="tools-for-diagnosing-issues"></a>Verktyg för att diagnostisera problem

Ett nätverksverktyg, till exempel Fiddler, kan hjälpa dig att diagnostisera problem.

1. Konfigurera ditt nätverksverktyg som en proxyserver som körs på den lokala värden. Om du måste fortsätta arbeta bakom en faktisk proxy kan du behöva konfigurera nätverksverktyget för att ansluta via proxyn.
2. Kontrollera portnumret som används av nätverksverktyget.
3. Konfigurera Storage Explorer proxyinställningar för att använda den lokala värden och nätverksverktygets portnummer (till exempel "localhost:8888").
 
När nätverksverktyget är korrekt inställt loggar det nätverksbegäranden som görs av Storage Explorer till hanterings- och tjänstslutpunkter.
 
Om nätverksverktyget inte verkar logga trafik Storage Explorer kan du testa verktyget med ett annat program. Ange till exempel slutpunkts-URL:en för en av dina lagringsresurser (till exempel ) i en webbläsare så får du ett `https://contoso.blob.core.windows.net/` svar som liknar följande:

  ![Kodexempel](./media/storage-explorer-troubleshooting/4022502_en_2.png)

  Svaret antyder att resursen finns, även om du inte kan komma åt den.

Om nätverksverktyget endast visar trafik från andra program kan du behöva justera proxyinställningarna i Storage Explorer. Annars behövde du justera inställningarna för verktyget.

### <a name="contact-proxy-server-admin"></a>Kontakta proxyserveradministratören

Om proxyinställningarna är korrekta kan du behöva kontakta proxyserveradministratören för att:

* Kontrollera att proxyn inte blockerar trafik till Azure-hantering eller resursslutpunkter.
* Kontrollera autentiseringsprotokollet som används av proxyservern. Storage Explorer stöder endast grundläggande autentiseringsprotokoll. Storage Explorer stöder inte NTLM-proxy.

## <a name="unable-to-retrieve-children-error-message"></a>Felmeddelandet "Det går inte att hämta underordnade"

Om du är ansluten till Azure via en proxyserver kontrollerar du att proxyinställningarna är korrekta.

Om ägaren av en prenumeration eller ett konto har gett dig åtkomst till en resurs kontrollerar du att du har läs- eller listbehörigheter för den resursen.

## <a name="connection-string-doesnt-have-complete-configuration-settings"></a>Anslutningssträngen har inte fullständiga konfigurationsinställningar

Om du får det här felmeddelandet är det möjligt att du inte har de behörigheter som krävs för att hämta nycklarna för ditt lagringskonto. Bekräfta att så är fallet genom att gå till portalen och leta upp ditt lagringskonto. Du kan göra detta genom att högerklicka på noden för ditt lagringskonto och välja **Öppna i portalen.** Gå sedan till **bladet Åtkomstnycklar.** Om du inte har behörighet att visa nycklar visas meddelandet "Du har inte åtkomst". Du kan lösa det här problemet genom att antingen hämta kontonyckeln från någon annan och ansluta via namn och nyckel, eller så kan du be någon om en SAS till lagringskontot och använda den för att ansluta lagringskontot.

Om du ser kontonycklarna kan du öppna ett ärende i GitHub så att vi kan hjälpa dig att lösa problemet.

## <a name="error-occurred-while-adding-new-connection-typeerror-cannot-read-property-version-of-undefined"></a>Fel uppstod när ny anslutning skulle läggs till: TypeError: Det går inte att läsa egenskapen "version" för odefinierad

Om du får det här felmeddelandet när du försöker lägga till en anpassad anslutning kan anslutningsdata som lagras i den lokala hanteraren för autentiseringsuppgifter vara skadade. Du kan komma runt det här problemet genom att försöka ta bort de skadade lokala anslutningarna och sedan lägga till dem igen:

1. Starta Storage Explorer. Från menyn går du till **Hjälp**  >  **Växla Utvecklarverktyg**.
2. I fönstret som öppnas går du **till Lokal** lagring **(vänster** sida) på fliken Program och väljer > **file://**.
3. Beroende på vilken typ av anslutning du har problem med letar du upp dess nyckel och kopierar sedan dess värde till en textredigerare. Värdet är en matris med dina anpassade anslutningsnamn, som följande:
    * Lagringskonton
        * `StorageExplorer_CustomConnections_Accounts_v1`
    * Blobcontainrar
        * `StorageExplorer_CustomConnections_Blobs_v1`
        * `StorageExplorer_CustomConnections_Blobs_v2`
    * Filresurser
        * `StorageExplorer_CustomConnections_Files_v1`
    * Köer
        * `StorageExplorer_CustomConnections_Queues_v1`
    * Tabeller
        * `StorageExplorer_CustomConnections_Tables_v1`
4. När du har sparat dina aktuella anslutningsnamn anger du värdet i Utvecklarverktyg till `[]` .

Om du vill bevara de anslutningar som inte är skadade kan du använda följande steg för att hitta de skadade anslutningarna. Om du inte har något emot att förlora alla befintliga anslutningar kan du hoppa över de här stegen och följa de plattformsspecifika anvisningarna för att rensa dina anslutningsdata.

1. Från en textredigerare lägger du till varje anslutningsnamn på nytt till Utvecklarverktyg och kontrollerar sedan om anslutningen fortfarande fungerar.
2. Om en anslutning fungerar som den ska är den inte skadad och du kan lämna den där. Om en anslutning inte fungerar tar du bort dess värde från Utvecklarverktyg och registrerar den så att du kan lägga till den igen senare.
3. Upprepa tills du har undersökt alla dina anslutningar.

När du har gått igenom alla anslutningar måste du rensa deras skadade data (om det finns några) för alla anslutningsnamn som inte har lagts till igen och sedan lägga till dem igen med hjälp av standardstegen i Storage Explorer:

### <a name="windows"></a>[Windows](#tab/Windows)

1. På **Start-menyn** söker du efter **Autentiseringshanteraren** och öppnar den.
2. Gå till **Windows-autentiseringsuppgifter.**
3. Under **Allmänna autentiseringsuppgifter** letar du efter poster som `<connection_type_key>/<corrupted_connection_name>` har nyckeln (till exempel `StorageExplorer_CustomConnections_Accounts_v1/account1` ).
4. Ta bort dessa poster och lägg till anslutningarna på ett annat sätt.

### <a name="macos"></a>[macOS](#tab/macOS)

1. Öppna Spotlight (kommando+blanksteg) och sök efter **nyckelringsåtkomst.**
2. Leta efter poster som har `<connection_type_key>/<corrupted_connection_name>` nyckeln (till exempel `StorageExplorer_CustomConnections_Accounts_v1/account1` ).
3. Ta bort dessa poster och lägg till anslutningarna på ett annat sätt.

### <a name="linux"></a>[Linux](#tab/Linux)

Hanteringen av lokala autentiseringsuppgifter varierar beroende på Linux-distributionen. Om Linux-distributionen inte tillhandahåller ett inbyggt GUI-verktyg för lokal hantering av autentiseringsuppgifter kan du installera ett verktyg från tredje part för att hantera dina lokala autentiseringsuppgifter. Du kan till exempel använda [Seahorse](https://wiki.gnome.org/Apps/Seahorse/), ett GUI-verktyg med öppen källkod för att hantera lokala Linux-autentiseringsuppgifter.

1. Öppna ditt lokala verktyg för hantering av autentiseringsuppgifter och leta upp dina sparade autentiseringsuppgifter.
2. Leta efter poster som har `<connection_type_key>/<corrupted_connection_name>` nyckeln (till exempel `StorageExplorer_CustomConnections_Accounts_v1/account1` ).
3. Ta bort dessa poster och lägg till anslutningarna på ett annat sätt.
---

Om du fortfarande stöter på det här felet när du har kört de [](https://github.com/microsoft/AzureStorageExplorer/issues) här stegen, eller om du vill dela det du misstänker har skadat anslutningarna, öppnar du ett ärende på vår GitHub-sida.

## <a name="issues-with-sas-url"></a>Problem med SAS-URL

Om du ansluter till en tjänst via en SAS-URL och får ett fel:

* Kontrollera att URL:en innehåller de behörigheter som krävs för att läsa eller lista resurser.
* Kontrollera att URL:en inte har upphört att gälla.
* Om SAS-URL:en baseras på en åtkomstprincip kontrollerar du att åtkomstprincipen inte har återkallats.

Om du av misstag har kopplats med en ogiltig SAS-URL och nu inte kan koppla från följer du dessa steg:

1. När du kör en Storage Explorer trycker du på F12 för att öppna Utvecklarverktyg fönstret.
2. På fliken **Program** väljer du **Lokal**  >  **file://** i trädet till vänster.
3. Hitta nyckeln som är associerad med tjänsttypen för den problematiska SAS-URI:en. Om till exempel den dåliga SAS-URI:en är för en blobcontainer letar du efter nyckeln med namnet `StorageExplorer_AddStorageServiceSAS_v1_blob` .
4. Värdet för nyckeln ska vara en JSON-matris. Hitta objektet som är associerat med den dåliga URI:en och ta sedan bort det.
5. Tryck på Ctrl + R för att läsa Storage Explorer.

## <a name="linux-dependencies"></a>Linux-beroenden

### <a name="snap"></a>Snapin

Storage Explorer 1.10.0 och senare är tillgänglig som en fäst från Snapin-butiken. Den Storage Explorer snapin-modulen installerar alla sina beroenden automatiskt och uppdateras när en ny version av snapin-modulen är tillgänglig. Att installera Storage Explorer snapin-modulen är den rekommenderade installationsmetoden.

Storage Explorer kräver användning av en lösenordshanterare, som du kan behöva ansluta manuellt innan Storage Explorer fungerar korrekt. Du kan Storage Explorer till systemets lösenordshanterare genom att köra följande kommando:

```bash
snap connect storage-explorer:password-manager-service :password-manager-service
```

### <a name="targz-file"></a>.tar.gz-fil

Du kan också ladda ned programmet som en .tar.gz-fil, men du måste installera beroenden manuellt.

Storage Explorer som anges i .tar.gz-nedladdningen stöds endast för följande versioner av Ubuntu. Storage Explorer fungerar på andra Linux-distributioner, men de stöds inte officiellt.

- Ubuntu 20.04 x64
- Ubuntu 18.04 x64
- Ubuntu 16.04 x64

Storage Explorer kräver att .NET Core är installerat på datorn. Vi rekommenderar .NET Core 2.1, Storage Explorer fungerar även med 2.2.

> [!NOTE]
> Storage Explorer version 1.7.0 och tidigare kräver .NET Core 2.0. Om du har en nyare version av .NET Core installerad måste du [korrigera Storage Explorer](#patching-storage-explorer-for-newer-versions-of-net-core). Om du kör Storage Explorer 1.8.0 eller senare behöver du minst .NET Core 2.1.

### <a name="ubuntu-2004"></a>[Ubuntu 20.04](#tab/2004)

1. Ladda ned filen Storage Explorer .tar.gz.
2. Installera [.NET Core Runtime:](/dotnet/core/install/linux)
   ```bash
   wget https://packages.microsoft.com/config/ubuntu/20.04/packages-microsoft-prod.deb -O packages-microsoft-prod.deb; \
     sudo dpkg -i packages-microsoft-prod.deb; \
     sudo apt-get update; \
     sudo apt-get install -y apt-transport-https && \
     sudo apt-get update && \
     sudo apt-get install -y dotnet-runtime-2.1
   ```

### <a name="ubuntu-1804"></a>[Ubuntu 18.04](#tab/1804)

1. Ladda ned filen Storage Explorer .tar.gz.
2. Installera [.NET Core Runtime:](/dotnet/core/install/linux)
   ```bash
   wget https://packages.microsoft.com/config/ubuntu/18.04/packages-microsoft-prod.deb -O packages-microsoft-prod.deb; \
     sudo dpkg -i packages-microsoft-prod.deb; \
     sudo apt-get update; \
     sudo apt-get install -y apt-transport-https && \
     sudo apt-get update && \
     sudo apt-get install -y dotnet-runtime-2.1
   ```

### <a name="ubuntu-1604"></a>[Ubuntu 16.04](#tab/1604)

1. Ladda ned filen Storage Explorer .tar.gz.
2. Installera [.NET Core Runtime:](/dotnet/core/install/linux)
   ```bash
   wget https://packages.microsoft.com/config/ubuntu/16.04/packages-microsoft-prod.deb -O packages-microsoft-prod.deb; \
     sudo dpkg -i packages-microsoft-prod.deb; \
     sudo apt-get update; \
     sudo apt-get install -y apt-transport-https && \
     sudo apt-get update && \
     sudo apt-get install -y dotnet-runtime-2.1
   ```
---

Många bibliotek som behövs Storage Explorer kommer förinstallerade med Canonicals standardinstallationer av Ubuntu. Vissa av de här biblioteken kanske saknas i anpassade miljöer. Om du har problem med Storage Explorer rekommenderar vi att du kontrollerar att följande paket är installerade på datorn:

- iproute2
- libaunda2
- libatm1
- libgconf2-4
- libnspr4
- libnss3
- libpulse0
- libsecret-1-0
- libx11-xcb1
- libxss1
- libxtables11
- libxtst6
- xdg-utils

### <a name="patching-storage-explorer-for-newer-versions-of-net-core"></a>Korrigering Storage Explorer nyare versioner av .NET Core

För Storage Explorer 1.7.0 eller tidigare kan du behöva korrigera den version av .NET Core som används av Storage Explorer:

1. Ladda ned version 1.5.43 av StreamJsonRpc [från NuGet.](https://www.nuget.org/packages/StreamJsonRpc/1.5.43) Leta efter länken "Download package" (Ladda ned paket) till höger på sidan.
2. När du har hämtat paketet ändrar du dess filnamnstillägg `.nupkg` från till `.zip` .
3. Packa upp paketet.
4. Öppna mappen `streamjsonrpc.1.5.43/lib/netstandard1.1/`.
5. Kopiera `StreamJsonRpc.dll` till följande platser i Storage Explorer mappen:
   * `StorageExplorer/resources/app/ServiceHub/Services/Microsoft.Developer.IdentityService/`
   * `StorageExplorer/resources/app/ServiceHub/Hosts/ServiceHub.Host.Core.CLR.x64/`

## <a name="open-in-explorer-from-the-azure-portal-doesnt-work"></a>"Öppna i Utforskaren" Azure Portal inte fungerar

Om knappen **Öppna i Utforskaren** på Azure Portal inte fungerar kontrollerar du att du använder en kompatibel webbläsare. Följande webbläsare har testats för kompatibilitet:
* Microsoft Edge
* Mozilla Firefox
* Google Chrome
* Microsoft Internet Explorer

## <a name="gathering-logs"></a>Samla in loggar

När du rapporterar ett problem till GitHub kan du bli ombedd att samla in vissa loggar för att diagnostisera problemet.

### <a name="storage-explorer-logs"></a>Storage Explorer loggar

Från och med version 1.16.0 Storage Explorer olika saker till sina egna programloggar. Du kan enkelt komma åt loggarna genom att klicka på Hjälp > Öppna loggkatalog. Som standard Storage Explorer loggar på en låg nivå av verbositet. Om du vill ändra verbositetsnivån lägger du till en miljövariabel med namnet `STG_EX_LOG_LEVEL` och något av följande värden:
- `silent`
- `critical`
- `error`
- `warning`
- `info` (standardnivå)
- `verbose`
- `debug`

Loggar delas upp i mappar för varje session Storage Explorer som du kör. För alla loggfiler som du behöver dela rekommenderar vi att du placerar dem i ett zip-arkiv med filer från olika sessioner i olika mappar.

### <a name="authentication-logs"></a>Autentiseringsloggar

För problem som rör inloggning eller Storage Explorer autentiseringsbibliotek behöver du troligen samla in autentiseringsloggar. Autentiseringsloggar lagras på:
- Windows: `C:\Users\<your username>\AppData\Local\Temp\servicehub\logs`
- macOS och Linux `~/.ServiceHub/logs`

I allmänhet kan du följa dessa steg för att samla in loggarna:

1. Gå till Inställningar > Inloggning > kontrollera Utförlig autentiseringsloggning. Om Storage Explorer inte kan starta på grund av ett problem med autentiseringsbiblioteket görs detta åt dig.
2. Stäng Storage Explorer.
1. Valfritt/rekommenderat: ta bort befintliga loggar från `logs` mappen. Detta minskar mängden information som du behöver skicka till oss.
4. Öppna Storage Explorer och återskapa problemet
5. Stäng Storage Explorer
6. Zippa innehållet i `log` mappen.

### <a name="azcopy-logs"></a>AzCopy-loggar

Om du har problem med att överföra data kan du behöva hämta AzCopy-loggarna. AzCopy-loggar kan enkelt hittas via två olika metoder:
- För misslyckade överföringar som fortfarande finns i aktivitetsloggen klickar du på "Gå till AzCopy-loggfil"
- För tidigare misslyckade överföringar går du till mappen AzCopy logs. Den här mappen finns på:
  - Windows: `C:\Users\<your username>\.azcopy`
  - macOS och Linux '~/.azcopy

### <a name="network-logs"></a>Nätverksloggar

För vissa problem måste du ange loggar för nätverkssamtalen som görs av Storage Explorer. I Windows kan du göra detta med hjälp av Fiddler.

> [!NOTE]
> Fiddler-spårningar kan innehålla lösenord som du angav/skickade i webbläsaren under insamlingen av spårningen. Läs anvisningarna om hur du sanerar en Fiddler-spårning. Ladda inte upp Fiddler-spårningar till GitHub. Du får ett meddelande om var du kan skicka Fiddler-spårningen på ett säkert sätt.

Del 1: Installera och konfigurera Fiddler

1. Installera Fiddler
2. Starta Fiddler
3. Gå till Verktyg > alternativ
4. Klicka på fliken HTTPS
5. Kontrollera att Capture CONNECT och Dekryptera HTTPS-trafik är markerade
6. Klicka på knappen Åtgärder
7. Välj "Lita på rotcertifikat" och sedan "Ja" i nästa dialogruta
8. Klicka på knappen Åtgärder igen
9. Välj "Exportera rotcertifikat till skrivbordet"
10. Gå till skrivbordet
11. Hitta FiddlerRoot.cer-filen
12. Dubbelklicka för att öppna
13. Gå till fliken "Information"
14. Klicka på "Kopiera till fil..."
15. Välj följande alternativ i exportguiden
    - Base-64-kodad X.509
    - För filnamn bläddrar du... till C:\Users \<your user dir> \AppData\Roaming\StorageExplorer\certs, och sedan kan du spara det som ett filnamn
16. Stäng certifikatfönstret
17. Starta Storage Explorer
18. Gå till Redigera > Konfigurera proxy
19. I dialogrutan väljer du "Använd appproxyinställningar" och anger URL:en till http://localhost och porten till 8888
20. Klicka på OK
21. Starta om Storage Explorer
22. Du bör börja se nätverkssamtal från en `storageexplorer:` process som visas i Fiddler

Del 2: Återskapa problemet
1. Stäng alla andra appar än Fiddler
2. Rensa Fiddler-loggen (X-ikonen längst upp till vänster, nära menyn Visa)
3. Valfritt/rekommenderat: Låt Fiddler ange i några minuter. Om du ser nätverkssamtal visas högerklickar du på dem och väljer "Filtrera nu" > <process name> "Dölj"
4. Starta Storage Explorer
5. Återskapa problemet
6. Klicka på Arkiv > Spara > alla sessioner..., spara någonstans som du inte glömmer
7. Stäng Fiddler och Storage Explorer

Del 3: Sanera Fiddler-spårningen
1. Dubbelklicka på Fiddler-spårningen (.saz-fil)
2. Tryck `ctrl`+`f`
3. Kontrollera att följande alternativ är inställda i dialogrutan som visas: Search = Requests and responses (Sök = Begäranden och svar) Examine = Headers and bodies (Granska = rubriker och text)
4. Sök efter eventuella lösenord som du använde när du samlade in Fiddler-spårningen, alla poster som är markerade, högerklicka och välj Ta bort > valda sessioner
5. Om du definitivt har angett lösenord i webbläsaren när du samlar in spårningen men inte hittar några poster när du använder ctrl + f och du inte vill ändra dina lösenord/de lösenord som du använde används för andra konton kan du hoppa över att skicka .saz-filen till oss. Det är bättre att vara säker än att vara säker. :)
6. Spara spårningen igen med ett nytt namn
7. Valfritt: Ta bort den ursprungliga spårningen

## <a name="next-steps"></a>Nästa steg

Om ingen av dessa lösningar fungerar för dig kan du:
- Skapa ett supportärende
- [Öppna ett ärende på GitHub](https://github.com/Microsoft/AzureStorageExplorer/issues). Du kan också göra detta genom att **välja knappen Rapportera problem till GitHub** i det nedre vänstra hörnet.

![Feedback](./media/storage-explorer-troubleshooting/feedback-button.PNG)