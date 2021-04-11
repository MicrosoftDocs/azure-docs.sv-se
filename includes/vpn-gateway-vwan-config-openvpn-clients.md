---
title: ta med fil
description: ta med fil
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/17/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: ee9dc61169b182356e8ed272bc64785e9c3f6384
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2021
ms.locfileid: "106073571"
---
## <a name="windows-clients"></a><a name="windows"></a>Windows-klienter

1. Hämta och installera OpenVPN-klienten (version 2,4 eller senare) från den officiella [OpenVPN-webbplatsen](https://openvpn.net/index.php/open-source/downloads.html).
2. Ladda ned VPN-profilen för gatewayen. Detta kan göras från fliken punkt-till-plats-konfiguration i Azure Portal, eller "New-AzVpnClientConfiguration" i PowerShell.
3. Packa upp profilen. Öppna sedan konfigurations filen *vpnconfig. ovpn* från mappen OpenVPN med anteckningar.
4. Exportera det punkt-till-plats-klient certifikat som du har skapat och laddat upp till din P2S-konfiguration på gatewayen. Använd följande artikel länkar:

   * [VPN gateway](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md#clientexport) -instruktioner
   
   * [Virtuella WAN](../articles/virtual-wan/certificates-point-to-site.md#clientexport) -instruktioner
5. Extrahera den privata nyckeln och base64-tumavtrycket från *. pfx*-filen. Det finns flera sätt att göra detta på. Att använda OpenSSL på din dator är ett sätt. *profileinfo.txt* -filen innehåller den privata nyckeln och TUMAVTRYCK för ca: n och klient certifikatet. Se till att använda tumavtrycket för klient certifikatet.

   ```
   openssl pkcs12 -in "filename.pfx" -nodes -out "profileinfo.txt"
   ```
6. Öppna *profileinfo.txt* i anteckningar. Om du vill hämta tumavtrycket för klientens (underordnat) certifikat väljer du texten (inklusive och mellan) "-----BEGIN CERTIFICATe-----" och "-----END CERTIFICATe-----" för det underordnade certifikatet och kopierar det. Du kan identifiera det underordnade certifikatet genom att titta på subject =/rad.
7. Växla till filen *vpnconfig. ovpn* som du öppnade i anteckningar från steg 3. Hitta avsnittet som visas nedan och Ersätt allt mellan "cert" och "/cert".

   ```
   # P2S client certificate
   # please fill this field with a PEM formatted cert
   <cert>
   $CLIENTCERTIFICATE
   </cert>
   ```
8. Öppna *profileinfo.txt* i anteckningar. Om du vill hämta den privata nyckeln markerar du texten (inklusive och mellan) "-----börjar privat nyckel-----" och "-----END PRIVATE KEY-----" och kopierar den.
9. Gå tillbaka till filen vpnconfig. ovpn i anteckningar och hitta det här avsnittet. Klistra in den privata nyckeln och Ersätt allt mellan och "Key" och "/Key".

   ```
   # P2S client root certificate private key
   # please fill this field with a PEM formatted key
   <key>
   $PRIVATEKEY
   </key>
   ```
10. Ändra inte några andra fält. Använd den ifyllda konfigurationen i klientindata för att ansluta till VPN.
11. Kopiera filen vpnconfig.ovpn till mappen C:\Program Files\OpenVPN\config.
12. Högerklicka på OpenVPN-ikonen i systemfältet och klicka på Anslut.

## <a name="mac-clients"></a><a name="mac"></a>Mac-klienter

1. Hämta och installera en OpenVPN-klient, till exempel [TunnelBlick](https://tunnelblick.net/downloads.html). 
2. Ladda ned VPN-profilen för gatewayen. Detta kan göras från fliken punkt-till-plats-konfiguration i Azure Portal, eller med hjälp av "New-AzVpnClientConfiguration" i PowerShell.
3. Packa upp profilen. Öppna konfigurations filen vpnconfig. ovpn från mappen OpenVPN i en text redigerare.
4. Fyll i avsnittet för P2S-klientcertifikatet med P2S-klientcertifikatets offentliga nyckel i base64. I ett PEM-formaterat certifikat öppnar du bara CER-filen och kopierar över base64-nyckeln mellan certifikathuvudena. Använd följande artikel länkar om du vill ha information om hur du exporterar ett certifikat för att hämta den kodade offentliga nyckeln:

   * [VPN gateway](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md#cer) -instruktioner 
   
   * [Virtuella WAN](../articles/virtual-wan/certificates-point-to-site.md#cer) -instruktioner
5. Fyll i avsnittet för den privata nyckeln med P2S-klientcertifikatets privata nyckel i base64. Information om hur du extraherar en privat nyckel finns i [exportera din privata nyckel](https://openvpn.net/community-resources/how-to/#pki) på OpenVPN-webbplatsen.
6. Ändra inte några andra fält. Använd den ifyllda konfigurationen i klientindata för att ansluta till VPN.
7. Dubbelklicka på profil filen för att skapa profilen i Tunnelblick.
8. Starta Tunnelblick från mappen program.
9. Klicka på ikonen Tunnelblick i system fältet och Välj Anslut.

> [!IMPORTANT]
>Endast iOS 11,0 och senare och MacOS 10,13 och senare stöds med OpenVPN-protokollet.
>
## <a name="ios-clients"></a><a name="iOS"></a>iOS-klienter

1. Installera OpenVPN-klienten (version 2,4 eller senare) från App Store.
2. Ladda ned VPN-profilen för gatewayen. Detta kan göras från fliken punkt-till-plats-konfiguration i Azure Portal, eller med hjälp av "New-AzVpnClientConfiguration" i PowerShell.
3. Packa upp profilen. Öppna konfigurations filen vpnconfig. ovpn från mappen OpenVPN i en text redigerare.
4. Fyll i avsnittet för P2S-klientcertifikatet med P2S-klientcertifikatets offentliga nyckel i base64. I ett PEM-formaterat certifikat öppnar du bara CER-filen och kopierar över base64-nyckeln mellan certifikathuvudena. Använd följande artikel länkar om du vill ha information om hur du exporterar ett certifikat för att hämta den kodade offentliga nyckeln:

   * [VPN gateway](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md#cer) -instruktioner 
   
   * [Virtuella WAN](../articles/virtual-wan/certificates-point-to-site.md#cer) -instruktioner
5. Fyll i avsnittet för den privata nyckeln med P2S-klientcertifikatets privata nyckel i base64. Mer information om hur du extraherar en privat nyckel finns i [exportera din privata nyckel](https://openvpn.net/community-resources/how-to/#pki) på OpenVPN-webbplatsen.
6. Ändra inte några andra fält.
7. Skicka profil filen (. ovpn) till ditt e-postkonto som har kon figurer ATS i e-postappen på din iPhone. 
8. Öppna e-postmeddelandet i e-postappen på iPhone och tryck på den bifogade filen

    ![Öppna e-post](./media/vpn-gateway-vwan-config-openvpn-clients/ios2.png)

9. Tryck på **mer** om du inte ser alternativet **Kopiera till OpenVPN**

    ![Mer](./media/vpn-gateway-vwan-config-openvpn-clients/ios3.png)

10. Tryck på **Kopiera till OpenVPN** 

    ![Kopiera till OpenVPN](./media/vpn-gateway-vwan-config-openvpn-clients/ios4.png)

11. Tryck på **Lägg till** på sidan **Importera profil**

    ![Lägg till](./media/vpn-gateway-vwan-config-openvpn-clients/ios5.png)

12. Tryck på **Lägg till** på sidan **importerad profil**

    ![Tryck på Lägg till](./media/vpn-gateway-vwan-config-openvpn-clients/ios6.png)

13. Starta OpenVPN-appen och dra växeln på **profil** sidan till höger för att ansluta

    ![Ansluta](./media/vpn-gateway-vwan-config-openvpn-clients/ios8.png)


## <a name="linux-clients"></a><a name="linux"></a>Linux-klienter

1. Öppna en ny Terminal-session. Du kan öppna en ny session genom att trycka på Ctrl + Alt + t samtidigt.
2. Ange följande kommando för att installera nödvändiga komponenter:

   ```
   sudo apt-get install openvpn
   sudo apt-get -y install network-manager-openvpn
   sudo service network-manager restart
   ```
3. Ladda ned VPN-profilen för gatewayen. Detta kan göras från fliken punkt-till-plats-konfiguration i Azure Portal.
4. Exportera det P2S-klientcertifikat som du har skapat och laddat upp till din P2S-konfiguration på gatewayen. Använd följande artikel länkar:

   * [VPN gateway](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md#clientexport) -instruktioner 
   
   * [Virtuella WAN](../articles/virtual-wan/certificates-point-to-site.md#clientexport) -instruktioner
5. Extrahera den privata nyckeln och base64-tumavtrycket från. pfx-filen. Det finns flera sätt att göra detta på. Att använda OpenSSL på din dator är ett sätt.

    ```
    openssl pkcs12 -in "filename.pfx" -nodes -out "profileinfo.txt"
    ```
   *profileinfo.txt* -filen innehåller den privata nyckeln och TUMAVTRYCK för ca: n och klient certifikatet. Se till att använda tumavtrycket för klient certifikatet.

6. Öppna *profileinfo.txt* i en text redigerare. Om du vill hämta tumavtrycket för klientens (underordnat) certifikat väljer du texten inklusive och mellan "-----BEGIN CERTIFICATe-----" och "-----END CERTIFICATe-----" för det underordnade certifikatet och kopierar det. Du kan identifiera det underordnade certifikatet genom att titta på subject =/rad.

7. Öppna filen *vpnconfig. ovpn* och hitta avsnittet som visas nedan. Ersätt allt mellan och "cert" och "/cert".

   ```
   # P2S client certificate
   # please fill this field with a PEM formatted cert
   <cert>
   $CLIENTCERTIFICATE
   </cert>
   ```
8. Öppna profileinfo.txt i en text redigerare. Om du vill hämta den privata nyckeln markerar du texten inklusive och mellan "-----börjar privat nyckel-----" och "-----avsluta den privata nyckeln-----" och kopierar den.

9. Öppna filen vpnconfig. ovpn i en text redigerare och leta upp det här avsnittet. Klistra in den privata nyckeln och Ersätt allt mellan och "Key" och "/Key".

   ```
   # P2S client root certificate private key
   # please fill this field with a PEM formatted key
   <key>
   $PRIVATEKEY
   </key>
   ```

10. Ändra inte några andra fält. Använd den ifyllda konfigurationen i klientindata för att ansluta till VPN.
11. Om du vill ansluta med kommando raden skriver du följande kommando:
  
    ```
    sudo openvpn --config <name and path of your VPN profile file>&
    ```
12. Om du vill ansluta med det grafiska användar gränssnittet går du till Systeminställningar.
13. Klicka **+** om du vill lägga till en ny VPN-anslutning.
14. Välj **Importera från fil** under **Lägg till VPN**.
15. Bläddra till profil filen och dubbelklicka på eller Välj **Öppna**.
16. Klicka på **Lägg till** i fönstret **Lägg till VPN** .
  
    ![Importera från fil](./media/vpn-gateway-vwan-config-openvpn-clients/import.png)
17. Du kan ansluta genom att aktivera VPN **på** sidan **nätverks inställningar** eller under nätverks ikonen i system fältet.
