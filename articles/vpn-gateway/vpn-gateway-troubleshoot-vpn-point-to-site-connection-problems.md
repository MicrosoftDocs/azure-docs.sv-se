---
title: Felsök problem med Azure punkt-till-plats-anslutning
titleSuffix: Azure VPN Gateway
description: Lär dig att felsöka och lösa vanliga problem med punkt-till-plats-anslutning och andra problem och problem med virtuella privata nätverk.
services: vpn-gateway
author: chadmath
ms.service: vpn-gateway
ms.topic: troubleshooting
ms.date: 03/26/2020
ms.author: genli
ms.openlocfilehash: c316aaf02979008b9d2ebc691d54c0fb95a5a52d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "95994867"
---
# <a name="troubleshooting-azure-point-to-site-connection-problems"></a>Fel sökning: problem med Azure punkt-till-plats-anslutning

Den här artikeln innehåller vanliga problem med punkt-till-plats-anslutningen som kan uppstå. Det beskriver också möjliga orsaker och lösningar för dessa problem.

## <a name="vpn-client-error-a-certificate-could-not-be-found"></a>VPN-klient fel: det gick inte att hitta något certifikat

### <a name="symptom"></a>Symptom

När du försöker ansluta till ett virtuellt Azure-nätverk med hjälp av VPN-klienten visas följande fel meddelande:

**Det gick inte att hitta något certifikat som kan användas med det här utöknings bara autentiseringsprotokollet. (Fel 798)**

### <a name="cause"></a>Orsak

Det här problemet uppstår om klient certifikatet saknas i **certifikat – aktuell User\Personal\Certificates**.

### <a name="solution"></a>Lösning

Följ dessa steg för att lösa problemet:

1. Öppna certifikat hanteraren: Klicka på **Start**, Skriv **Hantera dator certifikat** och klicka sedan på **Hantera dator certifikat** i Sök resultatet.

2. Kontrol lera att följande certifikat finns på rätt plats:

    | Certifikat | Location |
    | ------------- | ------------- |
    | AzureClient. pfx  | Aktuell User\Personal\Certificates |
    | AzureRoot. cer    | Lokala Computer\Trusted rot certifikat utfärdare|

3. Gå till C:\Users \<UserName> \AppData\Roaming\Microsoft\Network\Connections\Cm \<GUID> , installera certifikatet (*. CER-filen) manuellt på användarens och datorns Arkiv.

Mer information om hur du installerar klient certifikatet finns i [skapa och exportera certifikat för punkt-till-plats-anslutningar](vpn-gateway-certificates-point-to-site.md).

> [!NOTE]
> När du importerar klient certifikatet ska du inte välja alternativet **Aktivera starkt privat nyckel skydd** .

## <a name="the-network-connection-between-your-computer-and-the-vpn-server-could-not-be-established-because-the-remote-server-is-not-responding"></a>Det gick inte att upprätta nätverks anslutningen mellan datorn och VPN-servern på grund av att fjärrservern inte svarar

### <a name="symptom"></a>Symptom

När du försöker ansluta till en virtuell Azure-nätverksgateway med IKEv2 i Windows får du följande fel meddelande:

**Det gick inte att upprätta nätverks anslutningen mellan datorn och VPN-servern på grund av att fjärrservern inte svarar**

### <a name="cause"></a>Orsak
 
 Problemet uppstår om Windows-versionen saknar stöd för IKE-fragmentering
 
### <a name="solution"></a>Lösning

IKEv2 stöds på Windows 10 och Server 2016. Om du vill använda IKEv2 måste du installera uppdateringar och ange ett registreringsnyckelvärde lokalt. Operativsystemversioner före Windows 10 stöds inte och kan endast använda SSTP.

Förbereda Windows 10 eller Server 2016 för IKEv2:

1. Installera uppdateringen.

   | OS-version | Datum | Antal/länk |
   |---|---|---|---|
   | Windows Server 2016<br>Windows 10, version 1607 | 17 januari 2018 | [KB4057142](https://support.microsoft.com/help/4057142/windows-10-update-kb4057142) |
   | Windows 10, version 1703 | 17 januari 2018 | [KB4057144](https://support.microsoft.com/help/4057144/windows-10-update-kb4057144) |
   | Windows 10 version 1709 | Den 22 mars 2018 | [KB4089848](https://www.catalog.update.microsoft.com/search.aspx?q=kb4089848) |
   |  |  |  |  |

2. Ange registernyckelvärdet. Skapa eller ange `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\RasMan\ IKEv2\DisableCertReqPayload` REG_DWORD nyckel i registret till 1.

## <a name="vpn-client-error-the-message-received-was-unexpected-or-badly-formatted"></a>VPN-klient fel: det mottagna meddelandet var oväntat eller felaktigt formaterat

### <a name="symptom"></a>Symptom

När du försöker ansluta till ett virtuellt Azure-nätverk med hjälp av VPN-klienten visas följande fel meddelande:

**Det mottagna meddelandet var oväntat eller felaktigt formaterat. (Fel 0x80090326)**

### <a name="cause"></a>Orsak

Det här problemet uppstår om något av följande villkor är uppfyllt:

- Använd användardefinierade vägar (UDR) med standard väg på Gateway-undernätet är felaktigt angiven.
- Rot certifikatets offentliga nyckel har inte överförts till Azure VPN-gatewayen. 
- Nyckeln är skadad eller har upphört att gälla.

### <a name="solution"></a>Lösning

Följ dessa steg för att lösa problemet:

1. Ta bort UDR i Gateway-undernätet. Kontrol lera att UDR vidarebefordrar all trafik korrekt.
2. Kontrol lera status för rot certifikatet i Azure Portal för att se om det har återkallats. Om den inte återkallas försöker du ta bort rot certifikatet och ladda upp det igen. Mer information finns i [Skapa certifikat](vpn-gateway-howto-point-to-site-classic-azure-portal.md#generatecerts).

## <a name="vpn-client-error-a-certificate-chain-processed-but-terminated"></a>VPN-klient fel: en certifikat kedja bearbetades men avslutades 

### <a name="symptom"></a>Symptom 

När du försöker ansluta till ett virtuellt Azure-nätverk med hjälp av VPN-klienten visas följande fel meddelande:

**En certifikat kedja bearbetades men avslutades i ett rot certifikat som inte är betrott av förtroende leverantören.**

### <a name="solution"></a>Lösning

1. Kontrol lera att följande certifikat finns på rätt plats:

    | Certifikat | Location |
    | ------------- | ------------- |
    | AzureClient. pfx  | Aktuell User\Personal\Certificates |
    | Azuregateway-*GUID*. cloudapp.net  | Aktuella User\Trusted rot certifikat utfärdare|
    | AzureGateway-*GUID*. cloudapp.net, AzureRoot. cer    | Lokala Computer\Trusted rot certifikat utfärdare|

2. Om certifikaten redan finns på platsen kan du försöka ta bort certifikaten och installera om dem. Certifikatet **azuregateway-*GUID*. cloudapp.net** finns i KONFIGURATIONS paketet för VPN-klienten som du laddade ned från Azure Portal. Du kan använda fil Arkiv för att extrahera filerna från paketet.

## <a name="file-download-error-target-uri-is-not-specified"></a>Fil hämtnings fel: mål-URI har inte angetts

### <a name="symptom"></a>Symptom

Du får följande fel meddelande:

**Fil hämtnings fel. Mål-URI har inte angetts.**

### <a name="cause"></a>Orsak 

Det här problemet beror på en felaktig Gateway-typ. 

### <a name="solution"></a>Lösning

VPN-gatewayens typ måste vara **VPN** och VPN-typen måste vara **routningsbaserad**.

## <a name="vpn-client-error-azure-vpn-custom-script-failed"></a>VPN-klient fel: det gick inte att anpassa Azure VPN-skriptet 

### <a name="symptom"></a>Symptom

När du försöker ansluta till ett virtuellt Azure-nätverk med hjälp av VPN-klienten visas följande fel meddelande:

**Det gick inte att uppdatera det anpassade skriptet (för att uppdatera routningstabellen). (Fel 8007026f)**

### <a name="cause"></a>Orsak

Det här problemet kan uppstå om du försöker öppna VPN-anslutningen för plats-till-plats med hjälp av en genväg.

### <a name="solution"></a>Lösning 

Öppna VPN-paketet direkt i stället för att öppna det från genvägen.

## <a name="cannot-install-the-vpn-client"></a>Det går inte att installera VPN-klienten

### <a name="cause"></a>Orsak 

Det krävs ytterligare ett certifikat för att lita på VPN-gatewayen för det virtuella nätverket. Certifikatet ingår i konfigurations paketet för VPN-klienten som genereras från Azure Portal.

### <a name="solution"></a>Lösning

Extrahera konfigurations paketet för VPN-klienten och leta upp CER-filen. Följ dessa steg om du vill installera certifikatet:

1. Öppna mmc.exe.
2. Lägg till snapin-modulen **certifikat** .
3. Välj **dator** kontot för den lokala datorn.
4. Högerklicka på noden **betrodda rot certifikat utfärdare** . Klicka på Importera **alla aktiviteter**  >  och bläddra till. CER-filen som du extraherade från konfigurations paketet för VPN-klienten.
5. Starta om datorn. 
6. Försök att installera VPN-klienten.

## <a name="azure-portal-error-failed-to-save-the-vpn-gateway-and-the-data-is-invalid"></a>Azure Portal fel: det gick inte att spara VPN-gatewayen och data är ogiltiga

### <a name="symptom"></a>Symptom

När du försöker spara ändringarna för VPN-gatewayen i Azure Portal visas följande fel meddelande:

**Det gick inte att spara Gateway-namnet för den virtuella Nätverksgatewayen &lt;  &gt; . Data för certifikat &lt; *certifikatets ID* &gt; är ogiltigt.**

### <a name="cause"></a>Orsak 

Det här problemet kan inträffa om rot certifikatets offentliga nyckel som du överförde innehåller ett ogiltigt tecken, till exempel ett blank steg.

### <a name="solution"></a>Lösning

Kontrol lera att data i certifikatet inte innehåller ogiltiga tecken, t. ex. rad brytningar (vagn returer). Hela värdet måste vara en lång rad. Följande text är ett exempel på certifikatet:

```text
-----BEGIN CERTIFICATE-----
MIIC5zCCAc+gAwIBAgIQFSwsLuUrCIdHwI3hzJbdBjANBgkqhkiG9w0BAQsFADAW
MRQwEgYDVQQDDAtQMlNSb290Q2VydDAeFw0xNzA2MTUwMjU4NDZaFw0xODA2MTUw
MzE4NDZaMBYxFDASBgNVBAMMC1AyU1Jvb3RDZXJ0MIIBIjANBgkqhkiG9w0BAQEF
AAOCAQ8AMIIBCgKCAQEAz8QUCWCxxxTrxF5yc5uUpL/bzwC5zZ804ltB1NpPa/PI
sa5uwLw/YFb8XG/JCWxUJpUzS/kHUKFluqkY80U+fAmRmTEMq5wcaMhp3wRfeq+1
G9OPBNTyqpnHe+i54QAnj1DjsHXXNL4AL1N8/TSzYTm7dkiq+EAIyRRMrZlYwije
407ChxIp0stB84MtMShhyoSm2hgl+3zfwuaGXoJQwWiXh715kMHVTSj9zFechYd7
5OLltoRRDyyxsf0qweTFKIgFj13Hn/bq/UJG3AcyQNvlCv1HwQnXO+hckVBB29wE
sF8QSYk2MMGimPDYYt4ZM5tmYLxxxvGmrGhc+HWXzMeQIDAQABozEwLzAOBgNVHQ8B
Af8EBAMCAgQwHQYDVR0OBBYEFBE9zZWhQftVLBQNATC/LHLvMb0OMA0GCSqGSIb3
DQEBCwUAA4IBAQB7k0ySFUQu72sfj3BdNxrXSyOT4L2rADLhxxxiK0U6gHUF6eWz
/0h6y4mNkg3NgLT3j/WclqzHXZruhWAXSF+VbAGkwcKA99xGWOcUJ+vKVYL/kDja
gaZrxHlhTYVVmwn4F7DWhteFqhzZ89/W9Mv6p180AimF96qDU8Ez8t860HQaFkU6
2Nw9ZMsGkvLePZZi78yVBDCWMogBMhrRVXG/xQkBajgvL5syLwFBo2kWGdC+wyWY
U/Z+EK9UuHnn3Hkq/vXEzRVsYuaxchta0X2UNRzRq+o706l+iyLTpe6fnvW6ilOi
e8Jcej7mzunzyjz4chN0/WVF94MtxbUkLkqP
-----END CERTIFICATE-----
```

## <a name="azure-portal-error-failed-to-save-the-vpn-gateway-and-the-resource-name-is-invalid"></a>Azure Portal fel: det gick inte att spara VPN-gatewayen och resurs namnet är ogiltigt

### <a name="symptom"></a>Symptom

När du försöker spara ändringarna för VPN-gatewayen i Azure Portal visas följande fel meddelande: 

**Det gick inte att spara Gateway-namnet för den virtuella Nätverksgatewayen &lt;  &gt; . &lt; *Namnet på resurs namn certifikatet du försöker överföra* &gt; är ogiltigt**.

### <a name="cause"></a>Orsak

Det här problemet beror på att namnet på certifikatet innehåller ett ogiltigt tecken, till exempel ett blank steg. 

## <a name="azure-portal-error-vpn-package-file-download-error-503"></a>Azure Portal fel: fil hämtnings fel för VPN-paketfil 503

### <a name="symptom"></a>Symptom

När du försöker hämta konfigurations paketet för VPN-klienten visas följande fel meddelande:

**Det gick inte att hämta filen. Fel information: fel 503. Servern är upptagen.**
 
### <a name="solution"></a>Lösning

Det här felet kan orsakas av ett tillfälligt nätverks problem. Försök att ladda ned VPN-paketet igen om några minuter.

## <a name="azure-vpn-gateway-upgrade-all-point-to-site-clients-are-unable-to-connect"></a>Uppgradering av Azure-VPN Gateway: alla plats-till-plats-klienter kan inte ansluta

### <a name="cause"></a>Orsak

Om certifikatet är mer än 50 procent per livs längd överförs certifikatet över.

### <a name="solution"></a>Lösning

Lös problemet genom att ladda ned och distribuera om platsen till plats paketet på alla klienter.

## <a name="too-many-vpn-clients-connected-at-once"></a>För många VPN-klienter är anslutna samtidigt

Det maximala antalet tillåtna anslutningar har nåtts. Du kan se det totala antalet anslutna klienter i Azure Portal.

## <a name="vpn-client-cannot-access-network-file-shares"></a>VPN-klienten kan inte komma åt nätverks fil resurser

### <a name="symptom"></a>Symptom

VPN-klienten är ansluten till det virtuella Azure-nätverket. Klienten kan dock inte komma åt nätverks resurser.

### <a name="cause"></a>Orsak

SMB-protokollet används för fil resurs åtkomst. När anslutningen initieras lägger VPN-klienten till autentiseringsuppgifter för sessionen och fel meddelandet. När anslutningen har upprättats tvingas klienten att använda autentiseringsuppgifterna för autentisering i Kerberos-autentisering. Den här processen initierar frågor till Key Distribution Center (en domänkontrollant) för att hämta en token. Eftersom klienten ansluter från Internet kanske den inte kan komma åt domänkontrollanten. Klienten kan därför inte redundansväxla från Kerberos till NTLM. 

Den enda tid som klienten uppmanas att ange autentiseringsuppgifter är när den har ett giltigt certifikat (med SAN = UPN) utfärdat av den domän som den är ansluten till. Klienten måste också vara fysiskt ansluten till domän nätverket. I det här fallet försöker klienten använda certifikatet och når domänkontrollanten. Sedan returnerar Key Distribution Center ett "KDC_ERR_C_PRINCIPAL_UNKNOWN"-fel. Klienten tvingas redundansväxla till NTLM. 

### <a name="solution"></a>Lösning

Undvik problemet genom att inaktivera cachelagring av domänautentiseringsuppgifter från följande register under nyckel: 

`HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Lsa\DisableDomainCreds - Set the value to 1`


## <a name="cannot-find-the-point-to-site-vpn-connection-in-windows-after-reinstalling-the-vpn-client"></a>Det går inte att hitta punkt-till-plats-VPN-anslutningen i Windows efter ominstallation av VPN-klienten

### <a name="symptom"></a>Symptom

Du tar bort punkt-till-plats-VPN-anslutningen och installerar sedan om VPN-klienten. I det här fallet har VPN-anslutningen inte kon figurer ATS korrekt. Du ser inte VPN-anslutningen i inställningarna för **nätverks anslutningar** i Windows.

### <a name="solution"></a>Lösning

Lös problemet genom att ta bort de gamla konfigurationsfilerna för VPN-klienten **från \<VirtualNetworkId> C:\Users\UserName\AppData\Roaming\Microsoft\Network\Connections** och kör sedan installations programmet för VPN-klienten igen.

## <a name="point-to-site-vpn-client-cannot-resolve-the-fqdn-of-the-resources-in-the-local-domain"></a>Punkt-till-plats-VPN-klienten kan inte matcha det fullständiga domän namnet för resurserna i den lokala domänen

### <a name="symptom"></a>Symptom

När klienten ansluter till Azure med hjälp av punkt-till-plats-VPN-anslutning kan den inte matcha det fullständiga domän namnet för resurserna i din lokala domän.

### <a name="cause"></a>Orsak

Punkt-till-plats-VPN-klienten använder normalt Azure DNS servrar som är konfigurerade i det virtuella Azure-nätverket. Azure DNS-servrarna prioriteras över de lokala DNS-servrarna som har kon figurer ATS i klienten (om inte Ethernet-Gränssnittets mått är lägre), så att alla DNS-frågor skickas till Azure DNS-servrarna. Om det inte finns några poster för de lokala resurserna i Azure DNS-servrarna Miss lyckas frågan.

### <a name="solution"></a>Lösning

Lös problemet genom att kontrol lera att de Azure DNS-servrar som används i det virtuella Azure-nätverket kan matcha DNS-poster för lokala resurser. Om du vill göra detta kan du använda DNS-vidarebefordrare eller villkorliga vidarebefordrare. Mer information finns i [namn matchning med din egen DNS-Server](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)

## <a name="the-point-to-site-vpn-connection-is-established-but-you-still-cannot-connect-to-azure-resources"></a>Punkt-till-plats-VPN-anslutningen har upprättats, men du kan fortfarande inte ansluta till Azure-resurser 

### <a name="cause"></a>Orsak

Det här problemet kan inträffa om VPN-klienten inte hämtar vägarna från Azure VPN-gatewayen.

### <a name="solution"></a>Lösning

Lös problemet genom att [återställa Azure VPN gateway](./reset-gateway.md). För att se till att de nya vägarna används, måste punkt-till-plats-VPN-klienterna laddas ned igen efter att det har kon figurer ATS för virtuella nätverks-peering.

## <a name="error-the-revocation-function-was-unable-to-check-revocation-because-the-revocation-server-was-offlineerror-0x80092013"></a>Fel: "återkallnings funktionen kunde inte kontrol lera återkallning eftersom åter kallelse servern var offline. (Fel 0x80092013) "

### <a name="causes"></a>Orsaker
Det här fel meddelandet visas om klienten inte kan komma åt http://crl3.digicert.com/ssca-sha2-g1.crl och http://crl4.digicert.com/ssca-sha2-g1.crl .  Återkallnings kontrollen kräver åtkomst till dessa två platser.  Det här problemet inträffar vanligt vis på klienten där proxyservern har kon figurer ATS. I vissa miljöer, om begär Anden inte går via proxyservern, kommer den att nekas i gräns brand väggen.

### <a name="solution"></a>Lösning

Kontrol lera inställningarna för proxyservern, se till att klienten har åtkomst till http://crl3.digicert.com/ssca-sha2-g1.crl och http://crl4.digicert.com/ssca-sha2-g1.crl .

## <a name="vpn-client-error-the-connection-was-prevented-because-of-a-policy-configured-on-your-rasvpn-server-error-812"></a>VPN-klient fel: anslutningen förhindrades på grund av en princip som kon figurer ATS på RAS/VPN-servern. (Fel 812)

### <a name="cause"></a>Orsak

Det här felet uppstår om RADIUS-servern som du använde för att autentisera VPN-klienten har felaktiga inställningar, eller om Azure Gateway inte kan komma åt RADIUS-servern.

### <a name="solution"></a>Lösning

Kontrol lera att RADIUS-servern är korrekt konfigurerad. Mer information finns i [integrera RADIUS-autentisering med Azure AD Multi-Factor Authentication-Server](../active-directory/authentication/howto-mfaserver-dir-radius.md).

## <a name="error-405-when-you-download-root-certificate-from-vpn-gateway"></a>"Fel 405" när du laddar ned rot certifikat från VPN Gateway

### <a name="cause"></a>Orsak

Rot certifikatet hade inte installerats. Rot certifikatet installeras i klientens arkiv med **betrodda certifikat** .

## <a name="vpn-client-error-the-remote-connection-was-not-made-because-the-attempted-vpn-tunnels-failed-error-800"></a>VPN-klient fel: fjärr anslutningen upprättades inte på grund av att det inte gick att utföra VPN-tunnlar. (Fel 800) 

### <a name="cause"></a>Orsak

NÄTVERKSKORT driv rutinen är inaktuell.

### <a name="solution"></a>Lösning

Uppdatera NIC-drivrutinen:

1. Klicka på **Start**, Skriv **Enhetshanteraren** och välj den i listan över resultat. Om du uppmanas att ange ett administratörs lösen ord eller en bekräftelse anger du lösen ordet eller anger en bekräftelse.
2. Leta upp det nätverkskort som du vill uppdatera i kategorierna **nätverkskort** .  
3. Dubbelklicka på enhetens namn, Välj **Uppdatera driv rutin**, Välj **Sök automatiskt efter uppdaterad driv rutins program vara**.
4.  Om Windows inte hittar någon kan du söka efter en drivrutin på enhetstillverkarens webbplats och installera den enligt deras instruktioner.
5. Starta om datorn och försök ansluta igen.

## <a name="vpn-client-error-dialing-vpn-connection-vpn-connection-name-status--vpn-platform-did-not-trigger-connection"></a>VPN-klient fel: uppringning av VPN-anslutning <VPN Connection Name> , status = VPN-plattform utlöste inte anslutning

Du kan också se följande fel i Loggboken från RasClient: "användaren <User> kunde inte ansluta till en anslutning med namnet <VPN Connection Name> . Felkoden som returnerades vid felet är 1460. "

### <a name="cause"></a>Orsak

Azure VPN-klienten har inte appen "Background Apps" aktive rad i appinställningar för Windows.

### <a name="solution"></a>Lösning

1. I Windows går du till inställningar-> sekretess->-huvudappar
2. Växla "Låt appar köras i bakgrunden" till på

## <a name="error-file-download-error-target-uri-is-not-specified"></a>Fel: fel mål-URI för fil hämtning har inte angetts

### <a name="cause"></a>Orsak

Detta beror på att en felaktig Gateway-typ har kon figurer ATS.

### <a name="solution"></a>Lösning

Azure VPN gateway-typen måste vara VPN och VPN-typen måste vara **routningsbaserad**.

## <a name="vpn-package-installer-doesnt-complete"></a>Installations programmet för VPN-paketet slutförs inte

### <a name="cause"></a>Orsak

Det här problemet kan orsakas av de tidigare installationerna av VPN-klienten. 

### <a name="solution"></a>Lösning

Ta bort de gamla konfigurationsfilerna för VPN-klienten från **C:\Users\UserName\AppData\Roaming\Microsoft\Network\Connections \<VirtualNetworkId>** och kör installations programmet för VPN-klienten igen. 

## <a name="the-vpn-client-hibernates-or-sleep-after-some-time"></a>VPN-klienten försätts i vilo läge eller ström spar läge efter en stund

### <a name="solution"></a>Lösning

Kontrol lera inställningarna för vilo läge och vilo läge på den dator där VPN-klienten körs.