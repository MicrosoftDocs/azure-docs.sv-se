---
title: Edge-säkrade certifierings krav för kärnor
description: Krav för Edge Secure-Core certifierings program
author: cbroad
ms.author: cbroad
ms.topic: conceptual
ms.date: 03/15/2021
ms.custom: Edge Secured-core Certification Requirements
ms.service: certification
ms.openlocfilehash: 5bb02f939bb63fd1c6365fd4570996f09119e958
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2021
ms.locfileid: "106166912"
---
# <a name="azure-certified-device---edge-secured-core-preview"></a>Azure Certified Device – Edge-Edge Secure-Core (för hands version) #

## <a name="edge-secured-core-certification-requirements"></a>Gräns Secured-Core certifierings krav ##

Det här dokumentet beskriver enhetens specifika funktioner och krav som ska uppfyllas för att slutföra certifieringen och lista en enhet i Azure IoT-katalogen med etiketten Edge Secure-Core.

### <a name="program-purpose"></a>Program syfte ###
Edge Secure-Core är en stegvis certifiering i programmet Azure Certified Device för IoT-enheter som kör ett fullständigt operativ system, till exempel Linux eller Windows 10 IoT. det här programmet gör det möjligt för enhets partner att särskilja sina enheter genom att uppfylla ytterligare en uppsättning säkerhets kriterier. Enheter som uppfyller detta kriterium möjliggör följande löfte:
1. Maskinvarubaserad enhets identitet 
2. System integritet kan genomdrivas 
3. Hålls uppdaterat och kan fjärrhanteras
4. Tillhandahåller skydd för data i vila
5. Tillhandahåller data överförings skydd
6. Inbyggd säkerhets agent och härdning
### <a name="requirements"></a>Krav ###

---
|Name|SecuredCore. inbyggd. säkerhet|
|:---|:---|
|Status|Krävs|
|Beskrivning|Syftet med testet är att se till att enheterna kan rapportera säkerhets information och händelser genom att skicka data till Azure Defender för IoT.|
|Tillgänglighet för mål|2021|
|Gäller för|Valfri enhet|
|Operativsystem|Oberoende|
|Validerings typ|Manuella/verktyg|
|Validering |Enheten måste generera säkerhets loggar och aviseringar. Enhets loggar och aviserings meddelanden till Azure Security Center.<ol><li>Hämta och distribuera säkerhets agent från GitHub</li><li>Verifiera aviserings meddelande från Azure Defender för IoT.</li></ol>|
|Resurser|[Azure-dok IoT Defender för IoT](../defender-for-iot/how-to-configure-agent-based-solution.md)|

---
|Name|SecuredCore. encryption. Storage|
|:---|:---|
|Status|Krävs|
|Beskrivning|Syftet med testet för att verifiera att känsliga data kan krypteras på icke-flyktig lagring.|
|Tillgänglighet för mål|2021|
|Gäller för|Valfri enhet|
|Operativsystem|Oberoende|
|Validerings typ|Manuella/verktyg|
|Validering|Enheten ska verifieras via verktyg för att säkerställa att lagrings kryptering är aktiverat och Standardalgoritmen är XTS-AES, med nyckel längden 128 bitar eller högre.|
|Resurser||

---
|Name|SecuredCore. Hardware. SecureEnclave|
|:---|:---|
|Status|Valfritt|
|Beskrivning|Syftet med testet för att verifiera att det finns en säker enklaven och att enklaven kan nås från en säker agent.|
|Tillgänglighet för mål|2021|
|Gäller för|Valfri enhet|
|Operativsystem|Oberoende|
|Validerings typ|Manuella/verktyg|
|Validering|Enhet som ska verifieras via verktyg för att säkerställa att Azure-Säkerhetsagenten kan kommunicera med säkra enklaven|
|Resurser|https://github.com/openenclave/openenclave/blob/master/samples/BuildSamplesLinux.md|

---
|Name|SecuredCore. maskin vara. identitet|
|:---|:---|
|Status|Krävs|
|Beskrivning|Syftet med testet är att verifiera att enhets identifieringen är rotad i maskin vara.|
|Tillgänglighet för mål|2021|
|Gäller för|Valfri enhet|
|Operativsystem|Oberoende|
|Validerings typ|Manuella/verktyg|
|Validering|Enhet som ska verifieras via verktygs uppsättningen för att säkerställa att enheten har en TPM och att den kan tillhandahållas via IoT Hub att använda TPM-bekräftelse nyckeln.|
|Resurser|[Konfigurera automatisk etablering med DPS](../iot-dps/quick-setup-auto-provision.md)|

---
|Name|SecuredCore. Update|
|:---|:---|
|Status|Krävs|
|Beskrivning|Syftet med testet är att verifiera att enheten kan ta emot och uppdatera sin inbyggda program vara och program vara.|
|Tillgänglighet för mål|2021|
|Gäller för|Valfri enhet|
|Operativsystem|Oberoende|
|Validerings typ|Manuella/verktyg|
|Validering|Partner bekräftelse på att de kunde skicka en uppdatering till enheten via Microsoft Update, Azures enhets uppdatering eller andra godkända tjänster.|
|Resurser|[Enhets uppdatering för IoT Hub](../iot-hub-device-update/index.yml)|

---
|Name|SecuredCore.Manageability.Configuration|
|:---|:---|
|Status|Krävs|
|Beskrivning|Syftet med testet är att verifiera att enheterna stöder fjärran sluten säkerhets hantering.|
|Tillgänglighet för mål|2021|
|Gäller för|Valfri enhet|
|Operativsystem|Oberoende|
|Validerings typ|Manuella/verktyg|
|Validering|Enhet som ska verifieras via verktygs uppsättningen för att säkerställa att enheten stöder möjlighet att fjärrhantera och särskilt säkerhetskonfigurationer. Och statusen rapporteras tillbaka till IoT Hub/Azure Defender för IoT.|
|Resurser||

---
|Name|SecuredCore. hanterbarhet. Återställ|
|:---|:---|
|Status|Krävs|
|Beskrivning|Syftet med det här testet är att validera enheten mot två användnings fall: a) möjlighet att utföra en återställning (ta bort användar data, ta bort användar konfiguration), b) Återställ enheten till senast fungerande om en uppdatering orsakar problem.|
|Tillgänglighet för mål|2021|
|Gäller för|Valfri enhet|
|Operativsystem|Oberoende|
|Validerings typ|Manuella/verktyg|
|Validering|Enhet som ska verifieras via en kombination av verktyg och skickad dokumentation om att enheten stöder den här funktionen. Enhets tillverkaren kan avgöra om du ska implementera dessa funktioner som stöd för fjärråterställning eller endast lokal återställning.|
|Resurser||

---
|Name|SecuredCore. updates. duration|
|:---|:---|
|Status|Krävs|
|Beskrivning|Syftet med den här principen är att säkerställa att enheten förblir säker.|
|Tillgänglighet för mål|2021|
|Gäller för|Valfri enhet|
|Operativsystem|Oberoende|
|Validerings typ|Manuell|
|Validering|Åtagande från överföring av enheter som är certifierade krävs för att hålla enheterna uppdaterade i 60 månader från inlämnings datum. Specifikationer som är tillgängliga för inköparen och enheterna på något sätt bör ange hur länge program varan ska uppdateras.|
|Resurser||

---
|Name|SecuredCore. policy. vuln. avslöjande|
|:---|:---|
|Status|Krävs|
|Beskrivning|Syftet med den här principen är att se till att det finns en mekanism för att samla in och distribuera rapporter om sårbarheter i produkten.|
|Tillgänglighet för mål|2021|
|Gäller för|Valfri enhet|
|Operativsystem|Oberoende|
|Validerings typ|Manuell|
|Validering|Dokumentation om processen för att skicka och ta emot sårbarhets rapporter för de certifierade enheterna kommer att granskas.|
|Resurser||

---
|Name|SecuredCore. policy. vuln. fixes|
|:---|:---|
|Status|Krävs|
|Beskrivning|Syftet med den här principen är att säkerställa att säkerhets risker som är höga/kritiska (med CVSS 3,0) åtgärdas inom 180 dagar från det att korrigeringen är tillgänglig.|
|Tillgänglighet för mål|2021|
|Gäller för|Valfri enhet|
|Operativsystem|Oberoende|
|Validerings typ|Manuell|
|Validering|Dokumentation om processen för att skicka och ta emot sårbarhets rapporter för de certifierade enheterna kommer att granskas.|
|Resurser||


---
|Name|SecuredCore. encryption. TLS|
|:---|:---|
|Status|Krävs|
|Beskrivning|Syftet med testet är att validera stöd för nödvändiga TLS-versioner och chiffersviter.|
|Tillgänglighet för mål|2021|
|Gäller för|Valfri enhet|
|Operativsystem|Oberoende|
|Validerings typ|Manuella/verktyg|
Validering|Enhet som ska verifieras via verktygs uppsättningen för att säkerställa att enheten stöder en lägsta TLS-version på 1,2 och stöder följande obligatoriska TLS-chiffersviter.<ul><li>TLS_RSA_WITH_AES_128_GCM_SHA256</li><li>TLS_RSA_WITH_AES_128_CBC_SHA256</li><li>TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256</li><li>TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256</li><li>TLS_DHE_RSA_WITH_AES_128_GCM_SHA256</li><li>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256</li><li>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256</li></ul>|
|Resurser| [TLS-stöd i IoT Hub](../iot-hub/iot-hub-tls-support.md) <br /> [TLS-chiffersviter i Windows 10](https://docs.microsoft.com/windows/win32/secauthn/tls-cipher-suites-in-windows-10-v1903) |

---
|Name|SecuredCore. Protection. SignedUpdates|
|:---|:---|
|Status|Krävs|
|Beskrivning|Syftet med testet är att validera att uppdateringar måste signeras.|
|Tillgänglighet för mål|2021|
|Gäller för|Valfri enhet|
|Operativsystem|Oberoende|
|Validerings typ|Manuella/verktyg|
|Validering|Enhet som ska verifieras via verktygs uppsättningen för att säkerställa att uppdateringar av operativ systemet, driv rutiner, program vara, bibliotek, paket och inbyggd program vara inte tillämpas om de inte är korrekt signerade och verifierade.
|Resurser||

---
|Name|SecuredCore. firmware. SecureBoot|
|:---|:---|
|Status|Krävs|
|Beskrivning|Syftet med testet är att verifiera enhetens start integritet.|
|Tillgänglighet för mål|2021|
|Gäller för|Valfri enhet|
|Operativsystem|Oberoende|
|Validerings typ|Manuella/verktyg|
|Validering|Enhet som ska verifieras via verktyg för att säkerställa att den inbyggda program varan och kernel-signaturer verifieras varje gång enheten startas. <ul><li>UEFI: säker start är aktiverat</li><li>Uboot: verifierad start är aktive rad</li></ul>|
|Resurser||

---
|Name|SecuredCore. Protection. CodeIntegrity|
|:---|:---|
|Status|Krävs|
|Beskrivning|Syftet med det här testet är att kontrol lera att kod integriteten är tillgänglig på den här enheten.|
|Tillgänglighet för mål|2021|
|Gäller för|Valfri enhet|
|Operativsystem|Oberoende|
|Validerings typ|Manuella/verktyg|
|Validering|Enhet som ska verifieras via verktygs uppsättningen för att säkerställa att kod integritet är aktiverat. </br> Windows: begärda HVCI </br> Linux: DM-varlighetsgrad och IMA|
|Resurser||

---
|Name|SecuredCore. Protection. NetworkServices|
|:---|:---|
|Status|Krävs|
|Beskrivning|Syftet med testet är att validera att program som accepterar indatamängden från nätverket inte körs med utökade privilegier.|
|Tillgänglighet för mål|2021|
|Gäller för|Valfri enhet|
|Operativsystem|Oberoende|
|Validerings typ|Manuella/verktyg|
|Validering|Enhet som ska verifieras via verktygs uppsättningen för att säkerställa att tjänster som accepterar nätverks anslutningar inte körs med SYSTEM-eller rot behörigheter.|
|Resurser||

---
|Name|SecuredCore. Protection. bas linjer|
|:---|:---|
|Status|Krävs|
|Beskrivning|Syftet med testet är att kontrol lera att systemet överensstämmer med en grundläggande säkerhets konfiguration.|
|Tillgänglighet för mål|2021|
|Gäller för|Valfri enhet|
|Operativsystem|Oberoende|
|Validerings typ|Manuella/verktyg|
|Validering|Enhet som ska verifieras via verktygs uppsättningen för att säkerställa att de benchmark-system konfigurationerna för Defender IOT-system har körts.|
|Resurser| https://techcommunity.microsoft.com/t5/microsoft-security-baselines/bg-p/Microsoft-Security-Baselines <br> https://www.cisecurity.org/cis-benchmarks/ |

---
|Name|SecuredCore. firmware. Protection|
|:---|:---|
|Status|Krävs|
|Beskrivning|Syftet med testet är att säkerställa att enheten har lämpliga åtgärder mot hot från inbyggd program vara.|
|Tillgänglighet för mål|2021|
|Gäller för|Valfri enhet|
|Operativsystem|Oberoende|
|Validerings typ|Manuella/verktyg|
|Validering|Enhet som ska verifieras via verktygs uppsättningen för att bekräfta att den skyddas från hot från inbyggd program vara via en av följande metoder: <ul><li>DRTM + hanterings läges begränsningar i UEFI</li><li>Härdning av DRTM + hanterings läge för UEFI</li><li>Godkänt VB som gör SRTM + program vara för inbyggd program vara</li></ul> |
|Resurser| https://trustedcomputinggroup.org/ |

---
|Name|SecuredCore. firmware. attestering|
|:---|:---|
|Status|Krävs|
|Beskrivning|Syftet med testet är att se till att enheten kan verifiera tjänsten för Microsoft Azure attestering via fjärr anslutning.|
|Tillgänglighet för mål|2021|
|Gäller för|Valfri enhet|
|Operativsystem|Oberoende|
|Validerings typ|Manuella/verktyg|
|Validering|Enhet som ska verifieras via verktyg för att säkerställa att plattforms start loggar och mätningar av start aktivitet kan samlas in och fjärrautentiseras till tjänsten Microsoft Azure attestering.|
|Resurser| [Microsoft Azure Attestation](../attestation/index.yml) |

---
|Name|SecuredCore. Hardware. MemoryProtection|
|:---|:---|
|Status|Krävs|
|Beskrivning|Syftet med testet är att verifiera att DMA inte har Aktiver ATS på externa tillgängliga portar.|
|Tillgänglighet för mål|2021|
|Gäller för|Valfri enhet|
|Operativsystem|Oberoende|
|Validerings typ|Manuella/verktyg|
|Validering|Om det finns DMA-kompatibla externa portar på enheten för att kontrol lera att IOMMU eller SMMU har Aktiver ATS och kon figurer ATS för dessa portar.|
|Resurser||

---
|Name|SecuredCore. Protection. debug|
|:---|:---|
|Status|Krävs|
|Beskrivning|Syftet med testet är att verifiera att fel söknings funktionen på enheten är inaktive rad.|
|Tillgänglighet för mål|2021|
|Gäller för|Valfri enhet|
|Operativsystem|Oberoende|
|Validerings typ|Manuella/verktyg|
|Validering|Enhet som ska verifieras via verktyg för att se till att fel söknings funktionen kräver behörighet att aktivera.|
|Resurser||
