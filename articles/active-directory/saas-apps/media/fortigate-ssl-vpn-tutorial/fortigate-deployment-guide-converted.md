---
title: Distributions guide för FortiGate | Microsoft Docs
description: Konfigurera och arbeta med Fortinet-FortiGate nästa generations brand Väggs produkt.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/30/2020
ms.author: jeedes
ms.openlocfilehash: cdaa6a9601452100ab90ef8b0f2191002f256b74
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "95025525"
---
# <a name="fortigate-azure-virtual-machine-deployment-guide"></a>Distributions guide för FortiGate Azure Virtual Machine

Med den här distributions guiden får du lära dig hur du konfigurerar och arbetar med Fortinet FortiGate nästa generations brand Väggs produkt som distribueras som en virtuell Azure-dator. Dessutom kommer du att konfigurera FortiGate SSL VPN för Azure AD-galleriet för att tillhandahålla VPN-autentisering via Azure Active Directory.

## <a name="redeem-the-fortigate-license"></a>Lös in FortiGate-licensen

Fortinet FortiGate nästa generations brand Väggs produkt är tillgänglig som en virtuell dator i Azure Infrastructure as a Service (IaaS). Det finns två licensierings lägen för den här virtuella datorn: betala per användning och hämta din egen licens (BYOL).

Om du har köpt en FortiGate-licens från Fortinet som ska användas med distributions alternativet BYOL virtuell dator, löser du in den från Fortinets sida för produkt aktivering – https://support.fortinet.com . Den resulterande licens filen har fil namns tillägget. av.

## <a name="download-firmware"></a>Ladda ned inbyggd program vara

Vid tidpunkten för skrivning levereras inte Fortinet FortiGate Azure VM med den inbyggda program vara som krävs för SAML-autentisering. Den senaste versionen måste hämtas från Fortinet.

1. Logga in på https://support.fortinet.com/ .
2. Gå till **Hämta**  >  **avbildningar av inbyggd program vara**.
3. Till höger om **versions anteckningar** väljer du **Hämta**.
4. Välj **v 6.00**  >  **6,4**-  >  **6.4.2**.
5. Ladda ned **FGT_VM64_AZURE-V6-build1723-Fortinet. out** genom att välja **https** -länken på samma rad.
6. Spara filen för senare.

## <a name="deploy-the-fortigate-vm"></a>Distribuera den virtuella FortiGate-datorn

1. Gå till Azure Portal och logga in på den prenumeration som du vill distribuera den virtuella FortiGate-datorn till.
2. Skapa en ny resurs grupp eller öppna resurs gruppen där du vill distribuera den virtuella FortiGate-datorn.
3. Välj **Lägg till**.
4. Skriv *Forti* i **Sök på Marketplace**. Välj **Fortinet-Fortigate nästa generations brand vägg**.
5. Välj program varu planen (ta egen licens om du har en licens eller betala per användning om du inte vill det). Välj **Skapa**.
6. Fyll i konfigurationen för den virtuella datorn.

    ![Skärm bild av skapa en virtuell dator.](virtual-machine.png)

7. Ange **Autentiseringstyp** till **lösen ord** och ange administratörs behörighet för den virtuella datorn.
8. Välj **Granska + skapa** > **Skapa**.
9. Vänta tills den virtuella dator distributionen har slutförts.


### <a name="set-a-static-public-ip-address-and-assign-a-fully-qualified-domain-name"></a>Ange en statisk offentlig IP-adress och tilldela ett fullständigt kvalificerat domän namn

För en konsekvent användar upplevelse anger du den offentliga IP-adress som tilldelats den virtuella FortiGate-datorn som ska tilldelas statiskt. Mappa dessutom det till ett fullständigt kvalificerat domän namn (FQDN).

1. Gå till Azure Portal och öppna inställningarna för den virtuella datorn FortiGate.
2. På sidan **Översikt** väljer du den offentliga IP-adressen.

    ![Skärm bild av Fortigate SSL VPN.](public-ip-address.png)

3. Välj **statiskt**  >  **Spara**.

Om du äger ett offentligt dirigerbart domän namn för miljön där den virtuella FortiGate-datorn distribueras, skapar du en värd (A) post för den virtuella datorn. Den här posten mappar till föregående offentliga IP-adress som har tilldelats statiskt.

### <a name="create-a-new-inbound-network-security-group-rule-for-tcp-port-8443"></a>Skapa en ny regel för inkommande nätverks säkerhets grupp för TCP-port 8443

1. Gå till Azure Portal och öppna inställningarna för den virtuella datorn FortiGate.
2. Välj **nätverk** i menyn till vänster. Nätverks gränssnittet visas och reglerna för inkommande port visas.
3. Välj **Lägg till regel för inkommande port**.
4. Skapa en ny regel för inkommande portar för TCP 8443.

    ![Skärm bild av Lägg till inkommande säkerhets regel.](port-rule.png)

5. Välj **Lägg till**.

## <a name="create-a-second-virtual-nic-for-the-vm"></a>Skapa ett andra virtuellt nätverkskort för den virtuella datorn

För att interna resurser ska kunna göras tillgängliga för användare måste ett andra virtuellt nätverkskort läggas till i den virtuella FortiGate-datorn. Virtual Network i Azure som det virtuella NÄTVERKSKORTet finns på måste ha en dirigerbart anslutning till dessa interna resurser.

1. Gå till Azure Portal och öppna inställningarna för den virtuella datorn FortiGate.
2. Om den virtuella datorn FortiGate inte redan har stoppats väljer du **stoppa** och vänta tills den virtuella datorn har stängts av.
3. Välj **nätverk** i menyn till vänster.
4. Välj **bifoga nätverks gränssnitt**.
5. Välj **skapa och bifoga nätverks gränssnitt**.
6. Konfigurera egenskaper för det nya nätverks gränssnittet och välj sedan **skapa**.

    ![Skärm bild av skapa nätverks gränssnitt.](new-network-interface.png)

7. Starta den virtuella FortiGate-datorn.


## <a name="configure-the-fortigate-vm"></a>Konfigurera den virtuella FortiGate-datorn

I följande avsnitt lär du dig hur du konfigurerar den virtuella FortiGate-datorn.

### <a name="install-the-license"></a>Installera licensen

1. Gå till `https://<address>`. Här `<address>` är FQDN eller den offentliga IP-adress som tilldelats den virtuella Fortigate-datorn.

2. Fortsätt förbi eventuella certifikat fel.
3. Logga in med de administratörsautentiseringsuppgifter som angavs under distributionen av virtuella FortiGate-datorer.
4. Om distributionen använder sig av en egen-licens modell visas en uppfråga om att ladda upp en licens. Välj licens filen som skapades tidigare och ladda upp den. Välj **OK** och starta om den virtuella Fortigate-datorn.

    ![Skärm bild av FortiGate VM-licens.](license.png)

5. Efter omstarten loggar du in igen med administratörsautentiseringsuppgifter för att validera licensen.

### <a name="update-firmware"></a>Uppdatera den inbyggda programvaran

1. Gå till `https://<address>`. Här `<address>` är FQDN eller den offentliga IP-adress som tilldelats den virtuella Fortigate-datorn.

2. Fortsätt förbi eventuella certifikat fel.
3. Logga in med de administratörsautentiseringsuppgifter som angavs under distributionen av virtuella FortiGate-datorer.
4. På den vänstra menyn väljer du **systemets**  >  **inbyggda program vara**.
5. I **hantering av inbyggd program vara** väljer du **Bläddra** och väljer den inbyggda program varan som hämtades tidigare.
6. Ignorera varningen och välj **config och Upgrade (säkerhetskopiera**).

    ![Skärm bild av hantering av inbyggd program vara.](backup-configure-upgrade.png)

7. Välj **Fortsätt**.
8. När du uppmanas att spara FortiGate-konfigurationen (som en. conf-fil) väljer du **Spara**.
9. Vänta tills den inbyggda program varan har laddats upp och tillämpats. Vänta tills den virtuella FortiGate-datorn har startats om.
10. När FortiGate VM har startats om loggar du in igen med administratörs behörigheterna.
11. När du uppmanas att ställa in instrument panelen väljer du **senare**.
12. Välj **OK** när video kursen börjar.

### <a name="change-the-management-port-to-tcp-8443"></a>Ändra hanterings porten till TCP 8443

1. Gå till `https://<address>`. Här `<address>` är FQDN eller den offentliga IP-adress som tilldelats den virtuella Fortigate-datorn.

2. Fortsätt förbi eventuella certifikat fel.
3. Logga in med de administratörsautentiseringsuppgifter som angavs under distributionen av virtuella FortiGate-datorer.
4. På den vänstra menyn väljer du **system**.
5. Ändra HTTPS-porten till **8443** under **administrations inställningar** och välj **Använd**.
6. När ändringen har tillämpats försöker webbläsaren att läsa in administrations sidan igen, men den Miss lyckas. Från och med nu är administrations sidans adress `https://<address>:8443` .

    ![Skärm bild av Ladda upp fjärrcertifikat.](certificate.png)

### <a name="upload-the-azure-ad-saml-signing-certificate"></a>Ladda upp Azure AD-certifikatet för SAML-signering

1. Gå till `https://<address>:8443`. Här `<address>` är FQDN eller den offentliga IP-adress som tilldelats den virtuella Fortigate-datorn.

2. Fortsätt förbi eventuella certifikat fel.
3. Logga in med de administratörsautentiseringsuppgifter som angavs under distributionen av virtuella FortiGate-datorer.
4. På den vänstra menyn väljer du **system**  >  **certifikat**.
5. Välj **Importera**  >  **fjärrcertifikat**.
6. Bläddra till det certifikat som har hämtats från FortiGate-distributionen för anpassad app i Azure-klienten. Markera den och välj **OK**.

### <a name="upload-and-configure-a-custom-ssl-certificate"></a>Ladda upp och konfigurera ett anpassat SSL-certifikat

Du kanske vill konfigurera den virtuella datorn FortiGate med ditt eget SSL-certifikat som har stöd för det FQDN som du använder. Om du har åtkomst till ett SSL-certifikat som paketeras med den privata nyckeln i PFX-format kan det användas för detta ändamål.

1. Gå till `https://<address>:8443`. Här `<address>` är FQDN eller den offentliga IP-adress som tilldelats den virtuella Fortigate-datorn.

2. Fortsätt förbi eventuella certifikat fel.
3. Logga in med de administratörsautentiseringsuppgifter som angavs under distributionen av virtuella FortiGate-datorer.
4. På den vänstra menyn väljer du **system**  >  **certifikat**.
5. Välj **Importera**  >    >  **PKCS #12-certifikat** för lokal certifikat.
6. Bläddra till. PFX-fil som innehåller SSL-certifikatet och den privata nyckeln.
7. Ange. PFX-lösenord och ett beskrivande namn för certifikatet. Välj sedan **OK**.
8. På den vänstra **menyn väljer du**  >  **Systeminställningar**.
9. Under **administrations inställningar** expanderar du listan bredvid **https-servercertifikat** och väljer det SSL-certifikat som importerades tidigare.
10. Välj **Använd**.
11. Stäng webbläsarfönstret och gå till `https://<address>:8443` .
12. Logga in med autentiseringsuppgifterna för FortiGate-administratören. Nu bör du se rätt SSL-certifikat som används.

### <a name="configure-authentication-timeout"></a>Konfigurera tids gräns för autentisering

1. Gå till Azure Portal och öppna inställningarna för den virtuella datorn FortiGate.
2. På den vänstra menyn väljer du **serie konsol**.
3. Logga in i serie konsolen med autentiseringsuppgifterna för FortiGate VM-administratörer.
4. Kör följande kommandon i serie konsolen:

    ```
    config system global
    set remoteauthtimeout 60
    end
    ```

### <a name="ensure-network-interfaces-are-obtaining-ip-addresses"></a>Se till att nätverks gränssnitten erhåller IP-adresser

1. Gå till `https://<address>:8443`. Här `<address>` är FQDN eller den offentliga IP-adress som tilldelats den virtuella Fortigate-datorn.

2. Logga in med de administratörsautentiseringsuppgifter som angavs under distributionen av virtuella FortiGate-datorer.
3. Välj **nätverk** i menyn till vänster.
4. Under nätverk väljer du **gränssnitt**.
5. Undersök PORT1 (externt gränssnitt) och PORT2 (internt gränssnitt) för att se till att de får en IP-adress från rätt Azure-undernät.
    a. Om någon av portarna inte får en IP-adress från under nätet (via DHCP) högerklickar du på porten och väljer **Redigera**.
    b. Se till att **DHCP** är markerat bredvid adress läge.
    c. Seelct **OK**.

    ![Skärm bild av nätverks gränssnittets adressering.](addressing.png)

### <a name="ensure-fortigate-vm-has-correct-route-to-on-premises-corporate-resources"></a>Se till att FortiGate VM har rätt väg till lokala företags resurser

Virtuella Azure-datorer med flera hem har alla nätverks gränssnitt i samma virtuella nätverk (men kanske separata undernät). Det innebär ofta att båda nätverks gränssnitten har en anslutning till de lokala företags resurser som publiceras via FortiGate. Därför är det nödvändigt att skapa anpassade väg poster som garanterar att trafiken avslutas från rätt gränssnitt när begär Anden för lokala företags resurser görs.

1. Gå till `https://<address>:8443`. Här `<address>` är FQDN eller den offentliga IP-adress som tilldelats den virtuella Fortigate-datorn.

2. Logga in med de administratörsautentiseringsuppgifter som angavs under distributionen av virtuella FortiGate-datorer.
3. Välj **nätverk** i menyn till vänster.
4. Under nätverk väljer du **statiska vägar**.
5. Välj **Skapa ny**.
6. Bredvid mål väljer du **undernät**.
7. Under undernät anger du under nätet den plats information där de lokala företags resurserna finns (t. ex. 10.1.0.0/255.255.255.0)
8. Bredvid Gateway-adress anger du den gateway i Azure-undernätet där PORT2 är anslutet (t. ex. vanligt vis i 1 som 10.6.1.1)
9. Bredvid gränssnitt väljer du det interna nätverks gränssnittet, PORT2
10. Välj **OK**.

    ![Skärm bild som visar hur du konfigurerar en väg.](route.png)

## <a name="configure-fortigate-ssl-vpn"></a>Konfigurera FortiGate SSL VPN

Följ stegen som beskrivs i https://docs.microsoft.com/azure/active-directory/saas-apps/fortigate-ssl-vpn-tutorial
