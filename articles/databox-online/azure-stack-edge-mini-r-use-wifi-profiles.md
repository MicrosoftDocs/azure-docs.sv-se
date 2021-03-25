---
title: Använda Wi-Fi profiler med Azure Stack Edge Mini R-enheter
description: Beskriver hur du skapar Wi-Fi profiler för Azure Stack Edge-Mini R-enheter i nätverk med hög säkerhet och personliga nätverk.
services: databox
author: v-dalc@microsoft.com
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/24/2021
ms.author: alkohli
ms.openlocfilehash: 90c7c238cef104eae78618e51fa4b284adcc8f42
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2021
ms.locfileid: "105050673"
---
# <a name="use-wi-fi-profiles-with-azure-stack-edge-mini-r-devices"></a>Använda Wi-Fi profiler med Azure Stack Edge Mini R-enheter

Den här artikeln beskriver hur du använder profiler för trådlösa nätverk (Wi-Fi) med dina Azure Stack Edge Mini R-enheter.

Hur du förbereder Wi-Fi profilen beror på typen av trådlöst nätverk:

- På ett Wi-Fi skyddat Access 2 (WPA2)-personligt nätverk, till exempel ett hem nätverk eller Wi-Fi öppen hotspot, kan du hämta och använda en befintlig trådlös profil med samma lösen ord som du använder med andra enheter.

- I en företags miljö med hög säkerhet får du åtkomst till din enhet via ett nätverk i WPA2-Enterprise. På den här typen av nätverk har varje klient dator en distinkt Wi-Fi profil och autentiseras via certifikat. Du måste arbeta med nätverks administratören för att avgöra vilken konfiguration som krävs.

Vi diskuterar profil krav för båda typerna av nätverk ytterligare senare.

I båda fallen är det mycket viktigt att se till att profilen uppfyller organisationens säkerhets krav innan du testar eller använder profilen med din enhet.

## <a name="about-wi-fi-profiles"></a>Om Wi-Fi profiler

En Wi-Fi profil innehåller SSID (Service Set Identifier, eller **nätverks namn**), lösen ords nyckel och säkerhets information som krävs för att ansluta din Azure Stack Edge-Mini R-enhet till ett trådlöst nätverk.

I följande kod exempel visas grundläggande inställningar för en profil som ska användas med ett typiskt trådlöst nätverk:

* `SSID` är nätverks namnet.

* `name` är användarvänligt namn för Wi-Fi anslutningen. Det är det namn som användarna ser när de söker efter tillgängliga anslutningar på sina enheter.

* Profilen är konfigurerad att automatiskt ansluta datorn till det trådlösa nätverket när den är inom räckhåll för nätverket ( `connectionMode`  =  `auto` ).

```html
<?xml version="1.0"?>
<WLANProfile xmlns="http://www.contoso.com/networking/WLAN/profile/v1">
    <name>ContosoWIFICORP</name>
    <SSIDConfig>
        <SSID>
            <hex>1A234561234B5012</hex>
        </SSID>
        <nonBroadcast>false</nonBroadcast>
    </SSIDConfig>
    <connectionType>ESS</connectionType>
    <connectionMode>auto</connectionMode>
    <autoSwitch>false</autoSwitch>
```

Mer information om Wi-Fi profil inställningar finns i **företags profil** i [Lägg till Wi-Fi inställningar för Windows 10 och nyare enheter](/mem/intune/configuration/wi-fi-settings-windows#enterprise-profile), och se [Konfigurera Cisco Wi-Fi Profile](azure-stack-edge-mini-r-manage-wifi.md#configure-cisco-wi-fi-profile).

Om du vill aktivera trådlösa anslutningar på en Azure Stack Edge Mini R-enhet konfigurerar du Wi-Fi porten på enheten och lägger sedan till Wi-Fi-profil (er) på enheten. I ett företags nätverk laddar du också upp certifikat till enheten. Sedan kan du ansluta till ett trådlöst nätverk från det lokala webb gränssnittet för enheten. Mer information finns i [Hantera trådlös anslutning på Azure Stack Edge Mini R](./azure-stack-edge-mini-r-manage-wifi.md).

## <a name="profile-for-wpa2---personal-network"></a>Profil för WPA2-personligt nätverk

På ett Wi-Fi skyddat Access 2 (WPA2) – personligt nätverk, till exempel ett hem nätverk eller Wi-Fi öppen hotspot, kan flera enheter använda samma profil och samma lösen ord. I ditt hem nätverk använder din mobil telefon och bärbara dator samma trådlösa profil och lösen ord för att ansluta till nätverket.

En Windows 10-klient kan till exempel skapa en körnings profil åt dig. När du loggar in på det trådlösa nätverket uppmanas du att ange Wi-Fi lösen ordet och när du har angett lösen ordet är du ansluten. Det behövs inget certifikat i den här miljön.

I den här typen av nätverk kan du kanske exportera en Wi-Fi-profil från din bärbara dator och sedan lägga till den i Azure Stack Edge-Mini R-enheten. Anvisningar finns i [Exportera en Wi-Fi profil](#export-a-wi-fi-profile)nedan.

> [!IMPORTANT]
> Innan du skapar en Wi-Fi-profil för din Azure Stack Edge Mini R-enhet kontaktar du nätverks administratören för att få reda på organisationens säkerhets krav för trådlösa nätverk. Du bör inte testa eller använda någon Wi-Fi-profil på enheten förrän du vet att det trådlösa nätverket uppfyller kraven.

## <a name="profiles-for-wpa2---enterprise-network"></a>Profiler för WPA2-företags nätverk

I ett nätverk med trådlösa skyddade Access 2 (WPA2)-företags nätverk måste du arbeta med nätverks administratören för att få nödvändig Wi-Fi profil och certifikat för att ansluta din Azure Stack Edge-Mini R-enhet till nätverket.

För mycket säkra nätverk kan Azure-enheten använda PEAP (Protected Extensible Authentication Protocol) med utöknings bar autentisering Protocol-Transport Layer Security (EAP-TLS). PEAP med EAP-TLS använder datorautentisering: klienten och servern använder certifikat för att verifiera sina identiteter på varandra.

> [!NOTE]
> * Användarautentisering med PEAP Microsoft Challenge Handshake Authentication Protocol version 2 (PEAP MSCHAPv2) stöds inte på Azure Stack Edge-Mini R-enheter.
> * EAP-TLS-autentisering krävs för att få åtkomst till Azure Stack Edge Mini R-funktionen. En trådlös anslutning som du ställer in med Active Directory fungerar inte.

Nätverks administratören kommer att generera en unik Wi-Fi profil och ett klient certifikat för varje dator. Nätverks administratören bestämmer om du ska använda ett separat certifikat för varje enhet eller ett delat certifikat.

Om du arbetar på fler än en fysisk plats på arbets platsen kan nätverks administratören behöva tillhandahålla mer än en platsspecifika Wi-Fi profil och certifikat för dina trådlösa anslutningar.

I ett företags nätverk rekommenderar vi att du inte ändrar inställningarna i de Wi-Fi profiler som din nätverks administratör tillhandahåller. Den enda justering du kanske vill göra är till inställningarna för automatisk anslutning. Mer information finns i [grundläggande profil](/mem/intune/configuration/wi-fi-settings-windows#basic-profile) i Wi-Fi inställningar för Windows 10 och nyare enheter.

I en företags miljö med hög säkerhet kan du använda en befintlig profil för trådlöst nätverk som mall:

* Du kan ladda ned företagets trådlösa nätverks profil från din arbets dator. Anvisningar finns i [Exportera en Wi-Fi profil](#export-a-wi-fi-profile)nedan.

* Om andra i din organisation redan ansluter till sina Azure Stack Edge Mini R-enheter över ett trådlöst nätverk kan de Ladda ned Wi-Fi profilen från enheten. Instruktioner finns i [hämta Wi-Fi profil](azure-stack-edge-mini-r-manage-wifi.md#download-wi-fi-profile).

## <a name="export-a-wi-fi-profile"></a>Exportera en Wi-Fi-profil

Gör så här om du vill exportera en profil för Wi-Fi gränssnittet på datorn:

1. Om du vill se de trådlösa profilerna på datorn går du till **Start** -menyn, öppnar **kommando tolken** (cmd.exe) och anger det här kommandot:

   `netsh wlan show profiles`

   Utdata ser ut ungefär så här:

   ```dos
   Profiles on interface Wi-Fi:

   Group policy profiles (read only)
   ---------------------------------
       <None>

   User profiles
   -------------
       All User Profile     : ContosoCORP
       All User Profile     : ContosoFTINET
       All User Profile     : GusIS2809
       All User Profile     : GusGuests
       All User Profile     : SeaTacGUEST
       All User Profile     : Boat
   ```

2. Om du vill exportera en profil anger du följande kommando:

   `netsh wlan export profile name=”<profileName>” folder=”<path>\<profileName>"`

   Följande kommando sparar till exempel ContosoFTINET-profilen i XML-format till mappen Hämtade filer för användaren med namnet `gusp` .

   ```dos
   C:\Users\gusp>netsh wlan export profile name="ContosoFTINET" folder=c:Downloads

   Interface profile "ContosoFTINET" is saved in file "c:Downloads\ContosoFTINET.xml" successfully.
   ```

## <a name="add-certificate-wi-fi-profile-to-device"></a>Lägg till certifikat, Wi-Fi profil till enhet

När du har Wi-Fi profiler och certifikat som du behöver, gör du följande för att konfigurera Azure Stack Edge-Mini R-enheten för trådlösa anslutningar:

1. För ett WPA2-företags nätverk laddar du upp de certifikat som krävs till enheten enligt anvisningarna i [Ladda upp certifikat](./azure-stack-edge-gpu-manage-certificates.md#upload-certificates).

1. Ladda upp Wi-Fi profil (er) till Mini R-enheten och Anslut sedan till den genom att följa anvisningarna i [Lägg till, Anslut till Wi-Fi profil](./azure-stack-edge-mini-r-manage-wifi.md#add-connect-to-wi-fi-profile).

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [konfigurerar nätverk för Azure Stack Edge Mini R](azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy.md).
- Lär dig hur du [hanterar Wi-Fi på Azure Stack Edge Mini R](azure-stack-edge-mini-r-manage-wifi.md).
