---
title: Azure Stack Edge Pro R-säkerhet | Microsoft Docs
description: Beskriver säkerhets-och sekretessfunktioner som skyddar dina Azure Stack Edge Pro R-och Azure Stack Edge-Mini R-enheter, tjänster och data lokalt och i molnet.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 04/09/2021
ms.author: alkohli
ms.openlocfilehash: f4f7e5f69e6b496395b74dbdcd58b3ada0a7f349
ms.sourcegitcommit: c6a2d9a44a5a2c13abddab932d16c295a7207d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/09/2021
ms.locfileid: "107285222"
---
# <a name="security-and-data-protection-for-azure-stack-edge-pro-r-and-azure-stack-edge-mini-r"></a>Säkerhet och data skydd för Azure Stack Edge Pro R och Azure Stack Edge Mini R

[!INCLUDE [applies-to-pro-r-and-mini-r--skus](../../includes/azure-stack-edge-applies-to-pro-r-mini-r-sku.md)]

Säkerhet är ett viktigt problem när du använder en ny teknik, särskilt om tekniken används med konfidentiell eller patentskyddad information. Azure Stack Edge Pro R och Azure Stack Edge Mini R hjälper dig att se till att endast auktoriserade entiteter kan visa, ändra eller ta bort dina data.

Den här artikeln beskriver de Azure Stack Edge Pro R-och Azure Stack Edge Mini R-säkerhetsfunktionerna som skyddar varje lösnings komponent och de data som lagras i dem.

Lösningen består av fyra huvud komponenter som interagerar med varandra:

- **Azure Stack Edge service, som finns i Azures offentliga eller Azure Government moln**. Hanterings resursen som du använder för att skapa enhets ordningen, konfigurera enheten och sedan spåra beställningen.
- **Azure Stack Edge robust enhet**. Den robusta och fysiska enhet som skickas till dig så att du kan importera dina lokala data till Azures offentliga eller Azure Government moln. Enheten kan vara Azure Stack Edge Pro R eller Azure Stack Edge Mini R.
- **Klienter/värdar som är anslutna till enheten**. Klienterna i din infrastruktur som ansluter till enheten och innehåller data som behöver skyddas.
- **Moln lagring**. Platsen i Azure Cloud Platform där data lagras. Den här platsen är vanligt vis det lagrings konto som är kopplat till Azure Stack Edge-resurs som du skapar.

## <a name="service-protection"></a>Tjänst skydd

Tjänsten Azure Stack Edge är en hanterings tjänst som finns i Azure. Tjänsten används för att konfigurera och hantera enheten.

[!INCLUDE [azure-stack-edge-gateway-data-rest](../../includes/azure-stack-edge-gateway-service-protection.md)]

## <a name="device-protection"></a>Enhetsskydd

Den robusta enheten är en lokal enhet som hjälper till att transformera dina data genom att bearbeta den lokalt och sedan skicka den till Azure. Din enhet:

- Behöver en aktiverings nyckel för att få åtkomst till tjänsten Azure Stack Edge.
- Skyddas hela tiden av ett enhets lösen ord.
- Är en låst enhet. Enhetens BMC (Baseboard Management Controller) och BIOS är lösenordsskyddad. BMC skyddas av begränsad användar åtkomst.
- Har säker start aktiverat som säkerställer att enheten bara startas med den betrodda program vara som tillhandahålls av Microsoft.
- Kör Windows Defender Application Control (WDAC). Med WDAC kan du bara köra betrodda program som du definierar i dina kod integritets principer.
- Har en Trusted Platform Module (TPM) som utför maskinvarubaserade och säkerhetsrelaterade funktioner. Mer specifikt hanterar TPM och skyddar hemligheter och data som måste bevaras på enheten.
- Endast de portar som krävs öppnas på enheten och alla andra portar är blockerade. Mer information finns i listan med [port krav för enhet](azure-stack-edge-pro-r-system-requirements.md) .
- All åtkomst till enhetens maskin vara och program vara loggas. 
    - För enhets program varan samlas standard brand Väggs loggar in för inkommande och utgående trafik från enheten. De här loggarna paketeras i support paketet.
    - För enhetens maskin vara loggas alla enhets chassi händelser, till exempel öppning och stängning av enhetens chassi, i enheten.

    Mer information om de speciella loggar som innehåller intrångs händelser för maskin vara och program vara och hur du hämtar loggarna finns i [samla in avancerade säkerhets loggar](azure-stack-edge-gpu-troubleshoot.md).


### <a name="protect-the-device-via-activation-key"></a>Skydda enheten via aktiverings nyckeln

Endast en auktoriserad Azure Stack Edge Pro R-eller Azure Stack Edge-Mini R-enhet får ansluta till den Azure Stack Edge-tjänst som du skapar i din Azure-prenumeration. Om du vill auktorisera en enhet måste du använda en aktiverings nyckel för att aktivera enheten med Azure Stack Edge-tjänsten.

[!INCLUDE [azure-stack-edge-gateway-data-rest](../../includes/azure-stack-edge-gateway-activation-key.md)]

Mer information finns i [Hämta en aktiverings nyckel](azure-stack-edge-pro-r-deploy-prep.md#get-the-activation-key).

### <a name="protect-the-device-via-password"></a>Skydda enheten via lösen ord

Lösen ord se till att endast behöriga användare kan komma åt dina data. Azure Stack Edge Pro R-enheter startar i låst tillstånd.

Du kan:

- Anslut till det lokala webb gränssnittet på enheten via en webbläsare och ange ett lösen ord för att logga in på enheten.
- Fjärrans luta till enhetens PowerShell-gränssnitt över HTTP. Fjärrhantering är aktiverat som standard. Fjärrhantering är också konfigurerat för att endast använda tillräckligt med administration (JEA) för att begränsa vad användarna kan göra. Du kan sedan ange enhetens lösen ord för att logga in på enheten. Mer information finns i fjärrans [luta till din enhet](azure-stack-edge-gpu-connect-powershell-interface.md).
- Den lokala Edge-användaren på enheten har begränsad åtkomst till enheten för inledande konfiguration och fel sökning. De beräknings arbets belastningar som körs på enheten, data överföring och lagrings utrymmet kan nås från Azures offentliga eller myndighets Portal för resursen i molnet.

[!INCLUDE [azure-stack-edge-gateway-data-rest](../../includes/azure-stack-edge-gateway-password-best-practices.md)]
- Använd det lokala webb gränssnittet för att [ändra lösen ordet](azure-stack-edge-gpu-manage-access-power-connectivity-mode.md#change-device-password). Om du ändrar lösen ordet ska du se till att meddela alla användare av fjärråtkomst så att de inte har problem med att logga in.

### <a name="establish-trust-with-the-device-via-certificates"></a>Upprätta förtroende med enheten via certifikat

Med Azure Stack Edge robust enhet kan du ta med dina egna certifikat och installera dem som ska användas för alla offentliga slut punkter. Mer information finns i [Ladda upp ditt certifikat](azure-stack-edge-gpu-manage-certificates.md#upload-certificates). En lista över alla certifikat som kan installeras på enheten finns i [Hantera certifikat på enheten](azure-stack-edge-gpu-manage-certificates.md).

- När du konfigurerar Compute på enheten skapas en IoT-enhet och en IoT Edge enhet. De här enheterna tilldelas automatiskt symmetriska åtkomst nycklar. Som en säkerhets åtgärd roteras dessa nycklar regelbundet via IoT Hub tjänsten.

## <a name="protect-your-data"></a>Skydda dina data

I det här avsnittet beskrivs de säkerhetsfunktioner som skyddar data som överförs och lagras.

### <a name="protect-data-at-rest"></a>Skydda data i vila

Alla data i vila på enheten är dubbelt krypterad, åtkomsten till data styrs och när enheten har inaktiverats tas data bort på ett säkert sätt av data diskarna.

#### <a name="double-encryption-of-data"></a>Dubbel kryptering av data

Data på diskarna skyddas av två krypterings lager:

- Det första krypterings lagret är BitLocker-XTS-AES 256-bitars kryptering på data volymerna.
- Andra lagret är de hård diskar som har inbyggd kryptering.
- Operativ system volymen har BitLocker som ett enda krypterings lager.

> [!NOTE]
> OS-disken har en enda Layer BitLocker XTS-AES-256-programkryptering.

Innan du aktiverar enheten måste du konfigurera kryptering vid vila på enheten. Detta är en obligatorisk inställning och tills den har kon figurer ATS kan du inte aktivera enheten. 

När enheterna är avbildade på fabriken är volym nivå BitLocker-kryptering aktive rad. När du har fått enheten måste du konfigurera kryptering vid vila. Lagringspoolen och volymer återskapas och du kan ange BitLocker-nycklar för att aktivera kryptering vid vila och därmed skapa ytterligare ett krypterings lager för dina data i vila. 

Kryptering vid vila-nyckeln är en 32 tecken lång bas-64-kodad nyckel som du anger och den här nyckeln används för att skydda den faktiska krypterings nyckeln. Microsoft har inte åtkomst till den här krypteringen – vila-nyckeln som skyddar dina data. Nyckeln sparas i en nyckel fil på sidan med **moln information** när enheten har Aktiver ATS.

När enheten är aktive rad uppmanas du att spara nyckel filen som innehåller återställnings nycklar som hjälper till att återställa data på enheten om enheten inte startar. Vissa återställnings scenarier kommer att uppmana dig att ange nyckel filen som du har sparat. Nyckel filen innehåller följande återställnings nycklar:

- En nyckel som låser upp det första krypterings lagret.
- En nyckel som låser upp maskin varu krypteringen i data diskarna.
- En nyckel som hjälper till att återställa enhets konfigurationen på OS-volymerna.
- En nyckel som skyddar data som flödar genom Azure-tjänsten.

> [!IMPORTANT]
> Spara nyckel filen på en säker plats utanför själva enheten. Om enheten inte startar, och du inte har nyckeln, kan det innebära att data går förlorade.



#### <a name="restricted-access-to-data"></a>Begränsad åtkomst till data

Åtkomst till data som lagras i resurser och lagrings konton är begränsade.

- SMB-klienter som har åtkomst till resurs data måste ha användarautentiseringsuppgifter kopplade till resursen. Autentiseringsuppgifterna definieras när resursen skapas.
- NFS-klienter som har åtkomst till en resurs måste ha sin IP-adress uttryckligen när resursen har skapats.
- De gräns lagrings konton som skapas på enheten är lokala och skyddas av krypteringen på data diskarna. De Azure Storage-konton som de här gräns lagrings kontona mappas till skyddas av prenumerations-och 2 512-bitars lagrings åtkomst nycklar som är associerade med gräns lagrings kontot (dessa nycklar skiljer sig från de som är associerade med dina Azure Storage-konton). Mer information finns i [skydda data i lagrings konton](#protect-data-in-storage-accounts).
- BitLocker-XTS-AES 256-bit Encryption används för att skydda lokala data.

#### <a name="secure-data-erasure"></a>Skydda data radering

När enheten genomgår en hård återställning utförs en säker rensning på enheten. Den skyddade rensningen utför data radering på diskarna med NIST SP-88r1 rensa.

### <a name="protect-data-in-flight"></a>Skydda data i flygningen

[!INCLUDE [azure-stack-edge-gateway-data-rest](../../includes/azure-stack-edge-gateway-data-flight.md)]

### <a name="protect-data-in-storage-accounts"></a>Skydda data i lagrings konton

[!INCLUDE [azure-stack-edge-gateway-data-rest](../../includes/azure-stack-edge-gateway-protect-data-storage-accounts.md)]
- Rotera och [Synkronisera dina lagrings konto nycklar](azure-stack-edge-gpu-manage-storage-accounts.md) regelbundet för att hjälpa till att skydda ditt lagrings konto från obehöriga användare.

## <a name="manage-personal-information"></a>Hantera personlig information

Tjänsten Azure Stack Edge samlar in personlig information i följande scenarier:

[!INCLUDE [azure-stack-edge-gateway-data-rest](../../includes/azure-stack-edge-gateway-manage-personal-data.md)]

Om du vill visa en lista över användare som har åtkomst till eller tar bort en resurs följer du stegen i [Hantera resurser på Azure Stack Edge](azure-stack-edge-gpu-manage-shares.md).

Mer information hittar du i sekretess policyn för Microsoft på [säkerhets Center](https://www.microsoft.com/trustcenter).

## <a name="next-steps"></a>Nästa steg

[Distribuera din Azure Stack Edge Pro R-enhet](azure-stack-edge-gpu-deploy-prep.md)
