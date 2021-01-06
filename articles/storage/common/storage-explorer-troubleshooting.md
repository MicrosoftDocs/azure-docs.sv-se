---
title: Azure Storage Explorer fel söknings guide | Microsoft Docs
description: Översikt över fel söknings tekniker för Azure Storage Explorer
services: storage
author: Deland-Han
manager: dcscontentpm
ms.service: storage
ms.topic: troubleshooting
ms.date: 07/28/2020
ms.author: delhan
ms.openlocfilehash: 4e87e99f16a89cab95f9bd07b75b80f1c13d47f1
ms.sourcegitcommit: 5e762a9d26e179d14eb19a28872fb673bf306fa7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/05/2021
ms.locfileid: "97900662"
---
# <a name="azure-storage-explorer-troubleshooting-guide"></a>Felsökningsguide för Azure Storage Explorer

Microsoft Azure Storage Explorer är en fristående app som gör det enkelt att arbeta med Azure Storage-data i Windows, macOS och Linux. Appen kan anslutas till lagringskonton som finns i Azure, nationella moln och Azure Stack.

I den här guiden sammanfattas lösningar för problem som ofta visas i Storage Explorer.

## <a name="azure-rbac-permissions-issues"></a>Problem med Azure RBAC-behörighet

Azure-rollbaserad åtkomst kontroll [Azure RBAC](../../role-based-access-control/overview.md) möjliggör mycket detaljerad åtkomst hantering av Azure-resurser genom att kombinera uppsättningar med behörigheter i _roller_. Här följer några strategier för att få Azure RBAC fungerar optimalt i Storage Explorer.

### <a name="how-do-i-access-my-resources-in-storage-explorer"></a>Hur gör jag för att åtkomst till mina resurser i Storage Explorer?

Om du har problem med att komma åt lagrings resurser via Azure RBAC kanske du inte har tilldelats lämpliga roller. I följande avsnitt beskrivs de behörigheter som Storage Explorer för närvarande kräver åtkomst till dina lagrings resurser. Kontakta administratören för Azure-kontot om du inte är säker på att du har lämpliga roller eller behörigheter.

#### <a name="read-listget-storage-accounts-permissions-issue"></a>Problem med behörigheten läsa: lista/Hämta lagrings konto (er)

Du måste ha behörighet att lista lagrings konton. För att få den här behörigheten måste du tilldelas rollen _läsare_ .

#### <a name="list-storage-account-keys"></a>Lista lagrings konto nycklar

Storage Explorer kan också använda konto nycklar för att autentisera begär Anden. Du kan få åtkomst till konto nycklar via mer kraftfulla roller, till exempel _deltagar_ rollen.

> [!NOTE]
> Åtkomst nycklar beviljar obegränsade behörigheter till alla som innehar dem. Därför rekommenderar vi inte att du skickar ut de här nycklarna till konto användare. Om du behöver återkalla åtkomst nycklar kan du återskapa dem från [Azure Portal](https://portal.azure.com/).

#### <a name="data-roles"></a>Data roller

Du måste tilldelas minst en roll som beviljar åtkomst för att läsa data från resurser. Om du till exempel vill lista eller hämta blobbar behöver du minst rollen _Storage BLOB data Reader_ .

### <a name="why-do-i-need-a-management-layer-role-to-see-my-resources-in-storage-explorer"></a>Varför behöver jag en hanterings skikt roll för att se mina resurser i Storage Explorer?

Azure Storage har två åtkomst nivåer: _hantering_ och _data_. Prenumerationer och lagrings konton nås via hanterings skiktet. Behållare, blobbar och andra data resurser nås via data lagret. Om du till exempel vill hämta en lista över dina lagrings konton från Azure skickar du en begäran till hanterings slut punkten. Om du vill ha en lista över BLOB-behållare i ett konto skickar du en begäran till lämplig tjänst slut punkt.

Azure-roller kan ge dig behörigheter för hantering eller åtkomst till data lager. Rollen läsare, till exempel beviljar skrivskyddad åtkomst till hanterings skikts resurser.

Utan att tala med rollen läsare får du inga data lager behörigheter och behöver inte komma åt data skiktet.

Storage Explorer gör det enkelt att komma åt dina resurser genom att samla in nödvändig information för att ansluta till dina Azure-resurser. Om du till exempel vill visa dina BLOB-behållare, Storage Explorer skickar en begäran "lista över behållare" till BLOB-tjänstens slut punkt. Storage Explorer söker i listan över prenumerationer och lagrings konton som du har åtkomst till för att hämta slut punkten. För att hitta dina prenumerationer och lagrings konton behöver Storage Explorer även åtkomst till hanterings skiktet.

Om du inte har en roll som beviljar behörigheter för hanterings lager kan Storage Explorer inte hämta den information som krävs för att ansluta till data lagret.

### <a name="what-if-i-cant-get-the-management-layer-permissions-i-need-from-my-administrator"></a>Vad händer om jag inte kan hämta de behörigheter för hanterings skikt jag behöver från min administratör?

Om du vill komma åt BLOB-behållare eller köer kan du koppla dem till dessa resurser med dina Azure-autentiseringsuppgifter.

1. Öppna dialog rutan Anslut.
2. Välj Lägg till en resurs via Azure Active Directory (Azure AD). Välj Nästa.
3. Välj det användar konto och den klient organisation som är associerad med resursen som du ansluter till. Välj Nästa.
4. Välj resurs typ, ange URL: en till resursen och ange ett unikt visnings namn för anslutningen. Välj nästa och sedan Anslut.

För andra resurs typer har vi för närvarande ingen Azure RBAC-relaterad lösning. Som en lösning kan du begära en SAS-URI för att [ansluta till din resurs](../../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=linux#use-a-shared-access-signature-uri).

### <a name="recommended-azure-built-in-roles"></a>Rekommenderade inbyggda Azure-roller

Det finns flera inbyggda Azure-roller som kan ge de behörigheter som krävs för att använda Storage Explorer. Några av dessa roller är:
- [Ägare](/azure/role-based-access-control/built-in-roles#owner): hantera allt, inklusive åtkomst till resurser.
- [Deltagare](/azure/role-based-access-control/built-in-roles#contributor): hantera allt, förutom åtkomst till resurser.
- [Läsare](/azure/role-based-access-control/built-in-roles#reader): läsa och lista resurser.
- [Lagrings konto deltagare](/azure/role-based-access-control/built-in-roles#storage-account-contributor): fullständig hantering av lagrings konton.
- [Storage BLOB data-ägare](/azure/role-based-access-control/built-in-roles#storage-blob-data-owner): fullständig åtkomst till Azure Storage BLOB-behållare och data.
- [Storage BLOB data-deltagare](/azure/role-based-access-control/built-in-roles#storage-blob-data-contributor): läsa, skriva och ta bort Azure Storage behållare och blobbar.
- [Storage BLOB data Reader](/azure/role-based-access-control/built-in-roles#storage-blob-data-reader): läsa och lista Azure Storage behållare och blobbar.

> [!NOTE]
> Rollerna ägare, deltagare och lagrings konto deltagare ger åtkomst till konto nycklar.

## <a name="error-self-signed-certificate-in-certificate-chain-and-similar-errors"></a>Fel: självsignerat certifikat i certifikat kedjan (och liknande fel)

Certifikat fel inträffar vanligt vis i någon av följande situationer:

- Appen är ansluten via en _transparent proxy_. Det innebär att en server (till exempel din företags server) fångar upp HTTPS-trafik, dekrypterar den och sedan krypterar den med hjälp av ett självsignerat certifikat.
- Du kör ett program som matar in ett självsignerat TLS/SSL-certifikat i de HTTPS-meddelanden som du får. Exempel på program som injicerar certifikat är antivirus-och kontroll program för nätverks trafik.

När Storage Explorer ser ett självsignerat eller ej betrott certifikat, vet det inte längre om det mottagna HTTPS-meddelandet har ändrats. Om du har en kopia av det självsignerade certifikatet kan du instruera Storage Explorer att lita på det genom att följa dessa steg:

1. Hämta en kopia av certifikatet med bas-64-kodad X. 509 (. cer).
2. Gå till **Redigera**  >  **SSL-certifikat**  >  **Importera certifikat** och Använd sedan fil väljaren för att söka efter, välja och öppna. CER-filen.

Det här problemet kan även uppstå om det finns flera certifikat (rot och mellanliggande). För att åtgärda det här felet måste båda certifikaten läggas till.

Om du är osäker på var certifikatet kommer från följer du de här stegen för att hitta det:

1. Installera OpenSSL.
    * [Windows](https://slproweb.com/products/Win32OpenSSL.html): vilken som helst av de ljusa versionerna bör vara tillräckliga.
    * Mac och Linux: bör ingå i operativ systemet.
2. Kör OpenSSL.
    * Windows: öppna installations katalogen, Välj **/bin/** och dubbelklicka på **openssl.exe**.
    * Mac och Linux: kör `openssl` från en Terminal.
3. Kör `s_client -showcerts -connect microsoft.com:443`.
4. Leta efter självsignerade certifikat. Om du är osäker på vilka certifikat som är självsignerade måste du anteckna var och en av dem `("s:")` och utfärdaren `("i:")` .
5. När du hittar självsignerade certifikat för var och en, kopierar och klistrar du in allt från (och inkluderar) till `-----BEGIN CERTIFICATE-----` `-----END CERTIFICATE-----` en ny. cer-fil.
6. Öppna Storage Explorer och gå till **Redigera**  >  **SSL-certifikat**  >  **Importera certifikat**. Använd sedan fil väljaren för att söka efter, välja och öppna CER-filerna som du skapade.

Om du inte hittar några självsignerade certifikat genom att följa dessa steg kan du kontakta oss via feedback-verktyget. Du kan också öppna Storage Explorer från kommando raden med hjälp av `--ignore-certificate-errors` flaggan. Vid öppning med den här flaggan ignorerar Storage Explorer certifikat fel.

## <a name="sign-in-issues"></a>Inloggningsproblem

### <a name="blank-sign-in-dialog-box"></a>Dialog rutan tom inloggning

Tomma inloggnings dialog rutor inträffar oftast när Active Directory Federation Services (AD FS) (AD FS) begär Storage Explorer att utföra en omdirigering, vilket inte stöds av Electron. För att undvika det här problemet kan du försöka använda enhets kod flödet för inloggning. Det gör du på följande sätt:

1. Öppna **Inställningar** i det vänstra lodräta verktygsfältet. I panelen Inställningar går du till **program**  >  **inloggning**. Aktivera **Använd enhets kod flödes inloggning**.
2. Öppna dialog rutan **Anslut** (antingen via plugin-ikonen på vänster lodrätt fält eller genom att välja **Lägg till konto** på konto panelen).
3. Välj den miljö som du vill logga in på.
4. Välj **Logga in.**
5. Följ anvisningarna på nästa panel.

Om du inte kan logga in på det konto som du vill använda eftersom standard webbläsaren redan är inloggad på ett annat konto gör du något av följande:

- Kopiera länken och koden manuellt till en privat session i webbläsaren.
- Kopiera länken och koden manuellt till en annan webbläsare.

### <a name="reauthentication-loop-or-upn-change"></a>Reautentisering av loop eller UPN-ändring

Följ dessa steg om du befinner dig i en loop för autentisering eller har ändrat UPN för något av dina konton:

1. Ta bort alla konton och Stäng Storage Explorer.
2. Ta bort. IdentityService-mappen från din dator. I Windows finns mappen på `C:\users\<username>\AppData\Local` . För Mac och Linux kan du hitta mappen i roten i din användar katalog.
3. Om du kör Mac eller Linux måste du också ta bort posten Microsoft. Developer. IdentityService från operativ systemets nyckel lager. I Mac är nyckel lagringen *gnome nyckel rings* program. I Linux kallas programmet vanligt vis för _nyckel_ ring, men namnet kan variera beroende på din distribution.

### <a name="conditional-access"></a>Villkorlig åtkomst

På grund av en begränsning i Azure AD-biblioteket som används av Storage Explorer, stöds inte villkorlig åtkomst när Storage Explorer används på Windows 10, Linux eller macOS.

## <a name="mac-keychain-errors"></a>Fel i Mac-nyckelring

MacOS-nyckel ringen kan ibland ange ett tillstånd som orsakar problem med biblioteket för Storage Explorer autentisering. Följ dessa steg om du vill hämta nyckel ringen ur det här läget:

1. Stäng Storage Explorer.
2. Öppna nyckel ringen (tryck på kommando + blank steg, Skriv **nyckel Ring** och tryck på RETUR).
3. Välj nyckel ringen för inloggning.
4. Välj hänglås ikonen för att låsa nyckel ringen. (Hänglåset blir låst när processen är klar. Det kan ta några sekunder, beroende på vilka appar som är öppna).

    ![Hänglås ikon](./media/storage-explorer-troubleshooting/unlockingkeychain.png)

5. Öppna Storage Explorer.
6. Du uppmanas att ange ett meddelande som "service Hub vill ha åtkomst till nyckel ringen". Ange lösen ordet för ditt Mac-administratörskonto och välj **Tillåt alltid** (eller **Tillåt** om **Tillåt alltid** är tillgängligt).
7. Försök att logga in.

### <a name="general-sign-in-troubleshooting-steps"></a>Allmän inloggning fel söknings steg

* Om du använder macOS och inloggnings fönstret aldrig visas i dialog rutan **väntar på autentisering** , kan du prova [de här stegen](#mac-keychain-errors).
* Starta om Storage Explorer.
* Om Authentication-fönstret är tomt väntar du minst en minut innan du stänger dialog rutan autentisering.
* Kontrol lera att inställningarna för proxy och certifikat är korrekt konfigurerade för både din dator och Storage Explorer.
* Om du kör Windows och har åtkomst till Visual Studio 2019 på samma dator och inloggnings uppgifterna, kan du försöka logga in i Visual Studio 2019. När du har loggat in på Visual Studio 2019 kan du öppna Storage Explorer och se ditt konto i konto panelen.

Om ingen av dessa metoder fungerar [öppnar du ett ärende i GitHub](https://github.com/Microsoft/AzureStorageExplorer/issues).

### <a name="missing-subscriptions-and-broken-tenants"></a>Saknade prenumerationer och brutna klienter

Om du inte kan hämta dina prenumerationer när du har loggat in, kan du prova följande fel söknings metoder:

* Kontrol lera att ditt konto har åtkomst till de prenumerationer som du förväntar dig. Du kan kontrol lera åtkomsten genom att logga in på portalen för den Azure-miljö som du försöker använda.
* Kontrol lera att du har loggat in via rätt Azure-miljö (Azure, Azure Kina 21Vianet, Azure Germany, Azure amerikanska myndigheter eller anpassad miljö).
* Om du är bakom en proxyserver ser du till att du har konfigurerat Storage Explorer proxyn korrekt.
* Försök att ta bort och lägga till kontot på nytt.
* Om länken "Mer information" visas kontrollerar du vilka fel meddelanden som rapporteras för de klienter som inte fungerar. Om du inte är säker på hur du ska svara på fel meddelanden kan du [öppna ett ärende i GitHub](https://github.com/Microsoft/AzureStorageExplorer/issues).

## <a name="cant-remove-an-attached-account-or-storage-resource"></a>Det går inte att ta bort ett kopplat konto eller en lagrings resurs

Om du inte kan ta bort ett kopplat konto eller en lagrings resurs via användar gränssnittet kan du manuellt ta bort alla anslutna resurser genom att ta bort följande mappar:

* Windows: `%AppData%/StorageExplorer`
* MacOS `/Users/<your_name>/Library/Application Support/StorageExplorer`
* Linux: `~/.config/StorageExplorer`

> [!NOTE]
> Stäng Storage Explorer innan du tar bort mapparna.

> [!NOTE]
> Om du någonsin har importerat några SSL-certifikat säkerhetskopierar du innehållet i `certs` katalogen. Senare kan du använda säkerhets kopieringen för att importera SSL-certifikat igen.

## <a name="proxy-issues"></a>Problem med proxy

Storage Explorer stöder anslutning till Azure Storage resurser via en proxyserver. Här är några förslag om du får problem med att ansluta till Azure via proxy.

> [!NOTE]
> Storage Explorer stöder endast grundläggande autentisering med proxyservrar. Andra autentiseringsmetoder, till exempel NTLM, stöds inte.

> [!NOTE]
> Storage Explorer stöder inte AutoConfig-installationsfiler för att konfigurera proxyinställningar.

### <a name="verify-storage-explorer-proxy-settings"></a>Verifiera Storage Explorer proxyinställningar

**Konfigurations inställningen för program → proxy → proxy** avgör vilken källa Storage Explorer hämtar proxykonfigurationen från.

Om du väljer Använd miljövariabler måste du ställa in `HTTPS_PROXY` variablerna eller för miljövariabler `HTTP_PROXY` (miljövariabler är Skift läges känsliga, så se till att ange rätt variabler). Om dessa variabler är odefinierade eller ogiltiga använder Storage Explorer inte en proxy. Starta om Storage Explorer när du har ändrat alla miljövariabler.

Om du väljer Använd proxy-inställningar för appar kontrollerar du att proxyinställningarna för appen är korrekta.

### <a name="steps-for-diagnosing-issues"></a>Steg för att diagnostisera problem

Om du fortfarande har problem kan du prova följande fel söknings metoder:

1. Om du kan ansluta till Internet utan att använda proxyservern kontrollerar du att Storage Explorer fungerar utan proxyinställningar aktiverade. Om Storage Explorer ansluter, kan det finnas ett problem med proxyservern. Arbeta med administratören för att identifiera problemen.
2. Kontrol lera att andra program som använder proxyservern fungerar som förväntat.
3. Kontrol lera att du kan ansluta till portalen för den Azure-miljö som du försöker använda.
4. Verifiera att du kan ta emot svar från dina tjänst slut punkter. Ange en av dina slut punkts-URL: er i webbläsaren. Om du kan ansluta bör du få ett `InvalidQueryParameterValue` eller liknande XML-svar.
5. Kontrol lera om någon annan använder Storage Explorer med samma proxyserver kan ansluta. Om de kan det kan du behöva kontakta din Proxy Server-administratör.

### <a name="tools-for-diagnosing-issues"></a>Verktyg för att diagnostisera problem

Ett nätverks verktyg, till exempel Fiddler, kan hjälpa dig att diagnostisera problem.

1. Konfigurera nätverks verktyget som en proxyserver som körs på den lokala värden. Om du måste fortsätta att arbeta bakom en faktisk proxy kan du behöva konfigurera nätverks verktyget för att ansluta via proxyservern.
2. Kontrol lera port numret som används av nätverks verktyget.
3. Konfigurera Storage Explorer proxyinställningar för att använda den lokala värden och nätverks verktygets port nummer (till exempel "localhost: 8888").
 
När nätverks verktyget är korrekt konfigurerat loggar nätverks begär Anden som görs av Storage Explorer till hanterings-och tjänst slut punkter.
 
Om nätverks verktyget inte verkar logga Storage Explorer trafik kan du prova med att testa verktyget med ett annat program. Ange till exempel slut punkts-URL: en för en av dina lagrings resurser (till exempel `https://contoso.blob.core.windows.net/` ) i en webbläsare och du får ett svar som liknar:

  ![Kodexempel](./media/storage-explorer-troubleshooting/4022502_en_2.png)

  Svaret antyder att resursen finns, även om du inte har åtkomst till den.

Om nätverks verktyget endast visar trafik från andra program kan du behöva justera proxyinställningarna i Storage Explorer. Annars måste du justera verktygs inställningarna.

### <a name="contact-proxy-server-admin"></a>Kontakta proxyserverns administratör

Om proxyinställningarna är korrekta kan du behöva kontakta proxyserverns administratör för att:

* Kontrol lera att proxyn inte blockerar trafik till Azure-hantering eller resurs slut punkter.
* Verifiera autentiseringsprotokollet som används av proxyservern. Storage Explorer stöder endast Basic Authentication Protocol. Storage Explorer stöder inte NTLM-proxyservrar.

## <a name="unable-to-retrieve-children-error-message"></a>Fel meddelandet "det går inte att hämta underordnade"

Om du är ansluten till Azure via en proxyserver kontrollerar du att proxyinställningarna är korrekta.

Om ägaren av en prenumeration eller ett konto har gett dig åtkomst till en resurs, kontrollerar du att du har Läs-eller List behörigheter för den resursen.

## <a name="connection-string-doesnt-have-complete-configuration-settings"></a>Anslutnings strängen har inte fullständiga konfigurations inställningar

Om du får det här fel meddelandet är det möjligt att du inte har de behörigheter som krävs för att hämta nycklarna för ditt lagrings konto. För att bekräfta att detta är fallet går du till portalen och letar upp ditt lagrings konto. Du kan göra detta genom att högerklicka på noden för ditt lagrings konto och välja **Öppna i portalen**. Gå sedan till bladet **åtkomst nycklar** . Om du inte har behörighet att Visa nycklar visas meddelandet "du har inte åtkomst". För att undvika det här problemet kan du antingen hämta konto nyckeln från någon annan och ansluta via namnet och nyckeln, eller så kan du be någon om en SAS till lagrings kontot och använda det för att ansluta lagrings kontot.

Om du ser konto nycklarna kan du ange ett problem i GitHub så att vi kan hjälpa dig att lösa problemet.

## <a name="error-occurred-while-adding-new-connection-typeerror-cannot-read-property-version-of-undefined"></a>Ett fel uppstod när ny anslutning lades till: TypeError: det går inte att läsa egenskapen version för undefined

Om du får det här fel meddelandet när du försöker lägga till en anpassad anslutning kan de anslutnings data som lagras i den lokala Autentiseringshanteraren vara skadade. Undvik det här problemet genom att försöka ta bort dina skadade lokala anslutningar och sedan lägga till dem på nytt:

1. Starta Storage Explorer. I menyn går du till **Hjälp** att  >  **Växla utvecklarverktyg**.
2. I det öppnade fönstret, på fliken **program** , går du till **lokal lagring** (vänster sida) > **File://**.
3. Beroende på vilken typ av anslutning du har problem med, letar du efter nyckeln och kopierar dess värde till en text redigerare. Värdet är en matris med dina anpassade anslutnings namn, som följande:
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
4. När du har sparat dina aktuella anslutnings namn ställer du in värdet i Utvecklarverktyg till `[]` .

Om du vill bevara de anslutningar som inte är skadade kan du använda följande steg för att hitta de skadade anslutningarna. Om du inte gör något med att förlora alla befintliga anslutningar kan du hoppa över de här stegen och följa de plattformsspecifika instruktionerna för att rensa dina anslutnings data.

1. I en text redigerare, Lägg till varje anslutnings namn i Utvecklarverktyg igen och kontrol lera sedan om anslutningen fortfarande fungerar.
2. Om en anslutning fungerar som den ska, är den inte skadad och du kan på ett säkert sätt lämna den där. Om en anslutning inte fungerar tar du bort dess värde från Utvecklarverktyg och spelar in det så att du kan lägga till den igen senare.
3. Upprepa tills du har undersökt alla dina anslutningar.

När du har gått igenom alla dina anslutningar, för alla anslutnings namn som inte läggs tillbaka, måste du rensa deras skadade data (om det finns några) och lägga till dem igen med hjälp av standard stegen i Storage Explorer:

# <a name="windows"></a>[Windows](#tab/Windows)

1. På **Start** -menyn söker du efter **Autentiseringshanteraren** och öppnar den.
2. Gå till **Windows-autentiseringsuppgifter**.
3. Under **allmänna autentiseringsuppgifter** söker du efter poster som har `<connection_type_key>/<corrupted_connection_name>` nyckeln (till exempel `StorageExplorer_CustomConnections_Accounts_v1/account1` ).
4. Ta bort dessa poster och Lägg till anslutningarna på nytt.

# <a name="macos"></a>[macOS](#tab/macOS)

1. Öppna Spotlight (kommando + blank steg) och Sök efter **nyckel rings åtkomst**.
2. Leta efter poster som har `<connection_type_key>/<corrupted_connection_name>` nyckeln (till exempel `StorageExplorer_CustomConnections_Accounts_v1/account1` ).
3. Ta bort dessa poster och Lägg till anslutningarna på nytt.

# <a name="linux"></a>[Linux](#tab/Linux)

Hantering av lokala autentiseringsuppgifter varierar beroende på Linux-distributionen. Om din Linux-distribution inte tillhandahåller ett inbyggt GUI-verktyg för lokal hantering av autentiseringsuppgifter, kan du installera ett verktyg från tredje part för att hantera dina lokala autentiseringsuppgifter. Du kan till exempel använda [Seahorse](https://wiki.gnome.org/Apps/Seahorse/), ett gui-verktyg med öppen källkod för att hantera lokala autentiseringsuppgifter för Linux.

1. Öppna det lokala hanterings verktyget för autentiseringsuppgifter och hitta dina sparade autentiseringsuppgifter.
2. Leta efter poster som har `<connection_type_key>/<corrupted_connection_name>` nyckeln (till exempel `StorageExplorer_CustomConnections_Accounts_v1/account1` ).
3. Ta bort dessa poster och Lägg till anslutningarna på nytt.
---

Om du fortfarande stöter på det här felet när du har kört dessa steg, eller om du vill dela det som du misstänker har skadat anslutningarna, [öppnar du ett problem](https://github.com/microsoft/AzureStorageExplorer/issues) på vår GitHub-sida.

## <a name="issues-with-sas-url"></a>Problem med SAS-URL

Om du ansluter till en tjänst via en SAS-URL och råkar ut för ett fel:

* Kontrol lera att URL: en ger de behörigheter som krävs för att läsa eller lista resurser.
* Kontrol lera att URL: en inte har upphört att gälla.
* Om SAS-URL: en baseras på en åtkomst princip kontrollerar du att åtkomst principen inte har återkallats.

Om du av misstag har kopplat med en ogiltig SAS-URL och inte kan koppla från, följer du dessa steg:

1. När du kör Storage Explorer trycker du på F12 för att öppna fönstret Utvecklarverktyg.
2. På fliken **program** väljer du **lokal lagring**  >  **File://** i trädet till vänster.
3. Hitta nyckeln som är associerad med tjänst typen för den problematiska SAS-URI: n. Om t. ex. den felaktiga SAS-URI: n är för en BLOB-behållare, letar du efter nyckeln med namnet `StorageExplorer_AddStorageServiceSAS_v1_blob` .
4. Värdet för nyckeln ska vara en JSON-matris. Hitta objektet som är associerat med den felaktiga URI: n och ta sedan bort det.
5. Tryck på CTRL + R för att läsa in Storage Explorer igen.

## <a name="linux-dependencies"></a>Linux-beroenden

### <a name="snap"></a>Snapin

Storage Explorer 1.10.0 och senare är tillgängligt som en fäst från snapin Store. Den Storage Explorer Snap installerar alla dess beroenden automatiskt och uppdateras när en ny version av Snap är tillgänglig. Installation av Storage Explorer Snap är den rekommenderade installations metoden.

Storage Explorer kräver att du använder en lösen ords hanterare, som du kan behöva ansluta manuellt innan Storage Explorer fungerar som den ska. Du kan ansluta Storage Explorer till systemets lösen ords hanterare genom att köra följande kommando:

```bash
snap connect storage-explorer:password-manager-service :password-manager-service
```

### <a name="targz-file"></a>. tar. gz-fil

Du kan också hämta programmet som en. tar. gz-fil, men du måste installera beroenden manuellt.

Storage Explorer som anges i hämtningen. tar. gz stöds endast för följande versioner av Ubuntu. Storage Explorer kan fungera på andra Linux-distributioner, men de stöds inte officiellt.

- Ubuntu 20,04 x64
- Ubuntu 18,04 x64
- Ubuntu 16,04 x64

Storage Explorer kräver att .NET Core installeras i systemet. Vi rekommenderar .NET Core 2,1, men Storage Explorer fungerar med 2,2 även.

> [!NOTE]
> Storage Explorer version 1.7.0 och tidigare kräver .NET Core 2,0. Om du har en nyare version av .NET Core installerad måste du [uppdatera Storage Explorer](#patching-storage-explorer-for-newer-versions-of-net-core). Om du kör Storage Explorer 1.8.0 eller senare behöver du minst .NET Core 2,1.

# <a name="ubuntu-2004"></a>[Ubuntu 20.04](#tab/2004)

1. Hämta filen Storage Explorer. tar. gz.
2. Installera [.net Core runtime](/dotnet/core/install/linux):
   ```bash
   wget https://packages.microsoft.com/config/ubuntu/20.04/packages-microsoft-prod.deb -O packages-microsoft-prod.deb; \
     sudo dpkg -i packages-microsoft-prod.deb; \
     sudo apt-get update; \
     sudo apt-get install -y apt-transport-https && \
     sudo apt-get update && \
     sudo apt-get install -y dotnet-runtime-2.1
   ```

# <a name="ubuntu-1804"></a>[Ubuntu 18.04](#tab/1804)

1. Hämta filen Storage Explorer. tar. gz.
2. Installera [.net Core runtime](/dotnet/core/install/linux):
   ```bash
   wget https://packages.microsoft.com/config/ubuntu/18.04/packages-microsoft-prod.deb -O packages-microsoft-prod.deb; \
     sudo dpkg -i packages-microsoft-prod.deb; \
     sudo apt-get update; \
     sudo apt-get install -y apt-transport-https && \
     sudo apt-get update && \
     sudo apt-get install -y dotnet-runtime-2.1
   ```

# <a name="ubuntu-1604"></a>[Ubuntu 16.04](#tab/1604)

1. Hämta filen Storage Explorer. tar. gz.
2. Installera [.net Core runtime](/dotnet/core/install/linux):
   ```bash
   wget https://packages.microsoft.com/config/ubuntu/16.04/packages-microsoft-prod.deb -O packages-microsoft-prod.deb; \
     sudo dpkg -i packages-microsoft-prod.deb; \
     sudo apt-get update; \
     sudo apt-get install -y apt-transport-https && \
     sudo apt-get update && \
     sudo apt-get install -y dotnet-runtime-2.1
   ```
---

Många bibliotek som krävs av Storage Explorer förinstalleras med kanoniska standard installationer av Ubuntu. Vissa av dessa bibliotek kanske saknas i anpassade miljöer. Om du har problem med att starta Storage Explorer rekommenderar vi att du ser till att följande paket är installerade i systemet:

- iproute2
- libasound2
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
- xdg – utils

### <a name="patching-storage-explorer-for-newer-versions-of-net-core"></a>Korrigera Storage Explorer för nyare versioner av .NET Core

För Storage Explorer 1.7.0 eller tidigare kan du behöva korrigera den version av .NET Core som används av Storage Explorer:

1. Hämta version 1.5.43 av StreamJsonRpc [från NuGet](https://www.nuget.org/packages/StreamJsonRpc/1.5.43). Leta upp länken "Ladda ned paket" på höger sida av sidan.
2. När du har laddat ned paketet ändrar du dess fil namns tillägg från `.nupkg` till `.zip` .
3. Packa upp paketet.
4. Öppna mappen `streamjsonrpc.1.5.43/lib/netstandard1.1/`.
5. Kopiera `StreamJsonRpc.dll` till följande platser i mappen Storage Explorer:
   * `StorageExplorer/resources/app/ServiceHub/Services/Microsoft.Developer.IdentityService/`
   * `StorageExplorer/resources/app/ServiceHub/Hosts/ServiceHub.Host.Core.CLR.x64/`

## <a name="open-in-explorer-from-the-azure-portal-doesnt-work"></a>"Öppna i Utforskaren" från Azure Portal fungerar inte

Om knappen **Öppna i Explorer** på Azure Portal inte fungerar kontrollerar du att du använder en kompatibel webbläsare. Följande webbläsare har testats för kompatibilitet:
* Microsoft Edge
* Mozilla Firefox
* Google Chrome
* Microsoft Internet Explorer

## <a name="next-steps"></a>Nästa steg

Om ingen av dessa lösningar fungerar för dig [öppnar du ett ärende i GitHub](https://github.com/Microsoft/AzureStorageExplorer/issues). Du kan också göra detta genom att välja knappen **rapportera ärende till GitHub** i det nedre vänstra hörnet.

![Feedback](./media/storage-explorer-troubleshooting/feedback-button.PNG)