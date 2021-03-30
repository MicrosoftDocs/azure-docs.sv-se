---
title: Fjärrans luta till din StorSimple-enhet
description: Förklarar hur du konfigurerar enheten för fjärrhantering och hur du ansluter till Windows PowerShell för StorSimple via HTTP eller HTTPS.
author: alkohli
ms.service: storsimple
ms.topic: how-to
ms.date: 01/02/2018
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1da688dfb00b26ca6b561d5aa0fb548c221381c5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "85514566"
---
# <a name="connect-remotely-to-your-storsimple-8000-series-device"></a>Fjärrans luta till din StorSimple 8000-serie enhet

## <a name="overview"></a>Översikt

Du kan fjärrans luta till din enhet via Windows PowerShell. När du ansluter på det här sättet ser du ingen meny. (Du ser bara en meny om du använder serie konsolen på enheten för att ansluta.) Med Windows PowerShell-fjärrkommunikation ansluter du till en speciell körnings utrymme. Du kan också ange visnings språk.

Om du vill ha mer information om hur du använder Windows PowerShell-fjärrkommunikation för att hantera enheten går du till [använda Windows PowerShell för StorSimple för att administrera din StorSimple-enhet](storsimple-8000-windows-powershell-administration.md).

I den här självstudien beskrivs hur du konfigurerar din enhet för fjärrhantering och hur du ansluter till Windows PowerShell för StorSimple. Du kan använda HTTP eller HTTPS för fjärr anslutning via Windows PowerShell. Men när du bestämmer dig för att ansluta till Windows PowerShell för StorSimple bör du tänka på följande:

* Anslutning direkt till enhetens serie konsol är säker, men det går inte att ansluta till serie konsolen över nätverks växlar. Var försiktig med säkerhets risken när du ansluter till enhetens serie konsol över nätverks växlar.
* Att ansluta via en HTTP-session kan erbjuda mer säkerhet än att ansluta via serie konsolen över nätverket. Även om detta inte är den säkraste metoden, är det acceptabelt i betrodda nätverk.
* Att ansluta via en HTTPS-session med ett självsignerat certifikat är det säkraste och rekommenderade alternativet.

Du kan fjärrans luta till Windows PowerShell-gränssnittet. Fjärråtkomst till din StorSimple-enhet via Windows PowerShell-gränssnittet är dock inte aktiverat som standard. Du måste aktivera fjärrhantering på enheten först och sedan på klienten som används för att få åtkomst till enheten.

Stegen som beskrivs i den här artikeln utfördes på ett värd system som kör Windows Server 2012 R2.

## <a name="connect-through-http"></a>Anslut via HTTP

Att ansluta till Windows PowerShell för StorSimple via en HTTP-session ger högre säkerhet än att ansluta via StorSimple-enhetens serie konsol. Även om detta inte är den säkraste metoden, är det acceptabelt i betrodda nätverk.

Du kan använda antingen Azure Portal eller serie konsolen för att konfigurera fjärrhantering. Välj någon av följande procedurer:

* Använd Azure Portal för att aktivera fjärrhantering över HTTP
* [Använd serie konsolen för att aktivera fjärrhantering över HTTP](#use-the-serial-console-to-enable-remote-management-over-http)

När du har aktiverat fjärrhantering kan du använda följande procedur för att förbereda klienten för en fjärr anslutning.

* [Förbered klienten för fjärr anslutning](#prepare-the-client-for-remote-connection)

### <a name="use-the-azure-portal-to-enable-remote-management-over-http"></a>Använd Azure Portal för att aktivera fjärrhantering över HTTP

Utför följande steg i Azure Portal för att aktivera fjärrhantering över HTTP.

#### <a name="to-enable-remote-management-through-the-azure-portal"></a>Så här aktiverar du fjärrhantering via Azure Portal

1. Gå till StorSimple Device Manager-tjänsten. Välj **enheter** och välj sedan och klicka på den enhet som du vill konfigurera för fjärrhantering. Gå till **enhets inställningar > säkerhet**.
2. I bladet **säkerhets inställningar** klickar du på **fjärrhantering**.
3. På bladet **fjärrhantering** anger du **aktivera fjärrhantering** till **Ja**.
4. Nu kan du välja att ansluta med HTTP. (Standard är att ansluta via HTTPS.) Kontrol lera att HTTP är markerat.
   
   > [!NOTE]
   > Det är bara acceptabelt att ansluta över HTTP på betrodda nätverk.
   
5. Klicka på **Spara** och välj **Ja** när du uppmanas att bekräfta.

### <a name="use-the-serial-console-to-enable-remote-management-over-http"></a>Använd serie konsolen för att aktivera fjärrhantering över HTTP
Utför följande steg i enhetens serie konsol för att aktivera fjärrhantering.

#### <a name="to-enable-remote-management-through-the-device-serial-console"></a>Så här aktiverar du fjärrhantering via enhetens serie konsol
1. I menyn serie konsol väljer du alternativ 1. Mer information om hur du använder serie konsolen på enheten finns i [ansluta till Windows PowerShell för StorSimple via enhetens serie konsol](storsimple-8000-windows-powershell-administration.md#connect-to-windows-powershell-for-storsimple-via-the-device-serial-console).
2. Skriv följande i prompten: `Enable-HcsRemoteManagement –AllowHttp`
3. Du får ett meddelande om säkerhets sårbarheter i att använda HTTP för att ansluta till enheten. Bekräfta genom att skriva **Y** när du uppmanas till detta.
4. Verifiera att HTTP är aktiverat genom att skriva: `Get-HcsSystem`
5. Kontrol lera att fältet **RemoteManagementMode** visar **HttpsAndHttpEnabled**. Följande bild visar dessa inställningar i SparaTillFil.
   
     ![Seriellt HTTPS och HTTP aktiverat](./media/storsimple-remote-connect/HCS_SerialHttpsAndHttpEnabled.png)

### <a name="prepare-the-client-for-remote-connection"></a>Förbered klienten för fjärr anslutning
Utför följande steg på klienten för att aktivera fjärrhantering.

#### <a name="to-prepare-the-client-for-remote-connection"></a>Så här förbereder du klienten för fjärr anslutning
1. Starta en Windows PowerShell-session som administratör. Om du använder en Windows 10-klient är Windows Remote Management-tjänsten som standard inställd på manuell. Du kan behöva starta tjänsten genom att skriva:

    `Start-Service WinRM`
    
2. Skriv följande kommando för att lägga till IP-adressen för StorSimple-enheten i klientens lista över betrodda värdar:
   
     `Set-Item wsman:\localhost\Client\TrustedHosts <device_ip> -Concatenate -Force`
   
     Ersätt <*device_ip*> med ENHETens IP-adress. exempel: 
   
     `Set-Item wsman:\localhost\Client\TrustedHosts 10.126.173.90 -Concatenate -Force`
3. Skriv följande kommando för att spara autentiseringsuppgifterna för enheten i en variabel: 
   
    ```
    $cred = Get-Credential
    ```
    
4. I dialog rutan som visas:
   
   1. Ange användar namnet i följande format: *device_ip \ssadmin*.
   2. Ange enhetens administratörs lösen ord som angavs när enheten konfigurerades med installations guiden. Standard lösen ordet är *Password1*.
5. Starta en Windows PowerShell-session på enheten genom att skriva följande kommando:
   
     `Enter-PSSession -Credential $cred -ConfigurationName SSAdminConsole -ComputerName <device_ip>`
   
   > [!NOTE]
   > Om du vill skapa en Windows PowerShell-session för användning med den virtuella StorSimple-enheten lägger `–Port` du till parametern och anger den offentliga port som du konfigurerade i fjärr kommunikation för StorSimple Virtual-installation.
   
   
I det här läget bör du ha en aktiv Windows PowerShell-fjärrsession till enheten.
   
![PowerShell-fjärrkommunikation med HTTP](./media/storsimple-remote-connect/HCS_PSRemotingUsingHTTP.png)

## <a name="connect-through-https"></a>Anslut via HTTPS

Anslutning till Windows PowerShell för StorSimple via en HTTPS-session är den säkraste och rekommenderade metoden för fjärr anslutning till din Microsoft Azure StorSimple-enhet. Följande procedurer beskriver hur du konfigurerar serie konsolen och klient datorerna så att du kan använda HTTPS för att ansluta till Windows PowerShell för StorSimple.

Du kan använda antingen Azure Portal eller serie konsolen för att konfigurera fjärrhantering. Välj någon av följande procedurer:

* Använd Azure Portal för att aktivera fjärrhantering över HTTPS
* [Använd serie konsolen för att aktivera fjärrhantering över HTTPS](#use-the-serial-console-to-enable-remote-management-over-https)

När du har aktiverat fjärrhantering kan du använda följande procedurer för att förbereda värden för fjärrhantering och ansluta till enheten från fjärrvärden.

* [Förbereda värden för fjärrhantering](#prepare-the-host-for-remote-management)
* [Ansluta till enheten från fjärrvärden](#connect-to-the-device-from-the-remote-host)

### <a name="use-the-azure-portal-to-enable-remote-management-over-https"></a>Använd Azure Portal för att aktivera fjärrhantering över HTTPS

Utför följande steg i Azure Portal för att aktivera fjärrhantering över HTTPS.

#### <a name="to-enable-remote-management-over-https-from-the-azure-portal"></a>Så här aktiverar du fjärrhantering över HTTPS från Azure Portal

1. Gå till StorSimple Device Manager-tjänsten. Välj **enheter** och välj sedan och klicka på den enhet som du vill konfigurera för fjärrhantering. Gå till **enhets inställningar > säkerhet**.
2. I bladet **säkerhets inställningar** klickar du på **fjärrhantering**.
3. Ställ in **Aktivera fjärrhantering** på **Ja**.
4. Nu kan du välja att ansluta med HTTPS. (Standard är att ansluta via HTTPS.) Kontrol lera att HTTPS är markerat.
5. Klicka på... och klicka sedan på **Hämta certifikat för fjärrhantering**. Ange en plats där du vill spara filen. Du måste installera det här certifikatet på den klient eller värddator som du ska använda för att ansluta till enheten.
6. Klicka på **Spara** och sedan på **Ja** när du uppmanas att bekräfta.

### <a name="use-the-serial-console-to-enable-remote-management-over-https"></a>Använd serie konsolen för att aktivera fjärrhantering över HTTPS

Utför följande steg i enhetens serie konsol för att aktivera fjärrhantering.

#### <a name="to-enable-remote-management-through-the-device-serial-console"></a>Så här aktiverar du fjärrhantering via enhetens serie konsol
1. I menyn serie konsol väljer du alternativ 1. Mer information om hur du använder serie konsolen på enheten finns i [ansluta till Windows PowerShell för StorSimple via enhetens serie konsol](storsimple-8000-windows-powershell-administration.md#connect-to-windows-powershell-for-storsimple-via-the-device-serial-console).
2. Skriv följande i prompten:
   
     `Enable-HcsRemoteManagement`
   
    Detta bör Aktivera HTTPS på enheten.
3. Kontrol lera att HTTPS har Aktiver ATS genom att skriva: 
   
     `Get-HcsSystem`
   
    Kontrol lera att fältet **RemoteManagementMode** visar **HttpsEnabled**. Följande bild visar dessa inställningar i SparaTillFil.
   
     ![Seriell HTTPS aktive rad](./media/storsimple-remote-connect/HCS_SerialHttpsEnabled.png)
4. `Get-HcsSystem`Kopiera enhetens serie nummer från utdata från och spara den för senare användning.
   
   > [!NOTE]
   > Serie numret mappar till CN-namnet i certifikatet.
   
5. Hämta ett certifikat för fjärrhantering genom att skriva: 
   
     `Get-HcsRemoteManagementCert`
   
    Ett certifikat som liknar följande visas.
   
    ![Hämta certifikat för fjärrhantering](./media/storsimple-remote-connect/HCS_GetRemoteManagementCertificate.png)
6. Kopiera informationen i certifikatet från **-----Starta certifikat-----** till **-----slut certifikat-----** i en text redigerare, till exempel anteckningar, och spara den som en CER-fil. (Du kommer att kopiera den här filen till fjärrvärden när du förbereder värden.)
   
   > [!NOTE]
   > Använd cmdleten om du vill generera ett nytt certifikat `Set-HcsRemoteManagementCert` .
   
### <a name="prepare-the-host-for-remote-management"></a>Förbereda värden för fjärrhantering

Utför följande procedurer för att förbereda värddatorn för en fjärr anslutning som använder en HTTPS-session:

* [Importera. CER-filen till rot arkivet för klienten eller fjärrvärden](#to-import-the-certificate-on-the-remote-host).
* [Lägg till enhetens serie nummer i hosts-filen på fjärrvärden](#to-add-device-serial-numbers-to-the-remote-host).

Var och en av de föregående procedurerna beskrivs nedan.

#### <a name="to-import-the-certificate-on-the-remote-host"></a>Importera certifikatet på den fjärranslutna värden
1. Högerklicka på .cer-filen och välj **Installera certifikat**. Då startas guiden för att importera certifikat.
   
    ![Guiden Importera certifikat 1](./media/storsimple-remote-connect/HCS_CertificateImportWizard1.png)
2. För **Store location** (Lagringsplats) väljer du **Lokal dator** och klickar sedan på **Nästa**.
3. Välj **Place all certificates in the following store** (Placera alla certifikat i följande lagringsplats) och klicka sedan på **Bläddra**. Gå till rotcertifikatarkivet på fjärrvärden och klicka på **Nästa**.
   
    ![Guiden Importera certifikat 2](./media/storsimple-remote-connect/HCS_CertificateImportWizard2.png)
4. Klicka på **Finish**. Ett meddelande visas där det står att importen lyckades.
   
    ![Guiden Importera certifikat 3](./media/storsimple-remote-connect/HCS_CertificateImportWizard3.png)

#### <a name="to-add-device-serial-numbers-to-the-remote-host"></a>Så här lägger du till enhets serie nummer till fjärrvärden
1. Starta anteckningar som administratör och öppna sedan hosts-filen som finns på \Windows\System32\Drivers\etc.
2. Lägg till följande tre poster i hosts-filen: **data 0 IP-adress**, **kontrollant 0 fast IP-adress** och **domänkontrollant 1 fast IP-adress**.
3. Ange enhetens serie nummer som du sparade tidigare. Mappa detta till IP-adressen som det visas i följande bild. För Controller 0 och styrenhet 1 lägger du till **Controller0** och **Controller1** i slutet av serie numret (CN-namn).
   
    ![Lägger till CN-namn till värd filen](./media/storsimple-remote-connect/HCS_AddingCNNameToHostsFile.png)
4. Spara värdens fil.

### <a name="connect-to-the-device-from-the-remote-host"></a>Ansluta till enheten från fjärrvärden

Använd Windows PowerShell och TLS för att ange en SSAdmin-session på enheten från en fjärran sluten värd eller klient. SSAdmin-sessionen mappar till alternativ 1 i den [seriella konsol](storsimple-8000-windows-powershell-administration.md#connect-to-windows-powershell-for-storsimple-via-the-device-serial-console) menyn på enheten.

Utför följande procedur på den dator som du vill göra till en fjärran sluten Windows PowerShell-anslutning från.

#### <a name="to-enter-an-ssadmin-session-on-the-device-by-using-windows-powershell-and-tls"></a>Så här anger du en SSAdmin-session på enheten med Windows PowerShell och TLS
1. Starta en Windows PowerShell-session som administratör. Om du använder en Windows 10-klient är Windows Remote Management-tjänsten som standard inställd på manuell. Du kan behöva starta tjänsten genom att skriva:

    `Start-Service WinRM`

2. Lägg till enhetens IP-adress till klientens betrodda värdar genom att skriva:
   
     `Set-Item wsman:\localhost\Client\TrustedHosts <device_ip> -Concatenate -Force`
   
    Där <*device_ip*> är ENHETens IP-adress. exempel: 
   
     `Set-Item wsman:\localhost\Client\TrustedHosts 10.126.173.90 -Concatenate -Force`
3. Om du vill skapa en ny autentiseringsuppgift skriver du:
   
     `$cred = New-Object pscredential @("<IP of target device>\SSAdmin", (ConvertTo-SecureString -Force -AsPlainText "<Device Administrator Password>"))`
   
    Där <*IP-adress för mål enheten*> är IP-adressen för data 0 för enheten. till exempel **10.126.173.90** som visas i föregående bild av hosts-filen. Ange även administratörs lösen ordet för enheten.
4. Skapa en session genom att skriva:
   
     `$session = New-PSSession -UseSSL -ComputerName <Serial number of target device> -Credential $cred -ConfigurationName "SSAdminConsole"`
   
    För parametern-ComputerName i cmdleten anger du <*serie numret för mål enheten*>. Det här serie numret har mappats till IP-adressen för DATA 0 i värd filen på fjärrvärden. till exempel **SHX0991003G44MT** som visas i följande bild.
5. Ange:
   
     `Enter-PSSession $session`
6. Du kommer att behöva vänta några minuter och sedan ansluts du till din enhet via HTTPS över TLS. Du ser ett meddelande som anger att du är ansluten till din enhet.
   
    ![PowerShell-fjärrkommunikation med HTTPS och TLS](./media/storsimple-remote-connect/HCS_PSRemotingUsingHTTPSAndSSL.png)

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om [att använda Windows PowerShell för att administrera din StorSimple-enhet](storsimple-8000-windows-powershell-administration.md).
* Lär dig mer om [att använda tjänsten StorSimple Enhetshanteraren för att administrera din StorSimple-enhet](storsimple-8000-manager-service-administration.md).

