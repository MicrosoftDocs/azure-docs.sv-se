---
title: Översikt över Azure attestering
description: En översikt över Microsoft Azure attestering, en lösning för att intyga betrodda körnings miljöer (TEEs)
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.custom: references_regions
ms.openlocfilehash: 0d1b2bd039f3e110c83d7ad4bf32f1e53e4c7a79
ms.sourcegitcommit: 8a74ab1beba4522367aef8cb39c92c1147d5ec13
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/20/2021
ms.locfileid: "98610156"
---
# <a name="microsoft-azure-attestation-preview"></a>Microsoft Azure Attestation (förhandsversion)

Microsoft Azure attestering (för hands version) är en enhetlig lösning för att fjärrverifiera tillförlitlighet för en plattform och integritet för de binärfiler som körs inuti den. Tjänsten har stöd för attestering av de plattformar som stöds av Trusted Platform Module (TPM) tillsammans med möjligheten att intyga status för betrodda körnings miljöer (TEEs) som [Intel® Software Guard-tillägg](https://www.intel.com/content/www/us/en/architecture-and-technology/software-guard-extensions.html) (SGX) Enclaves och [virtualiseringsbaserad säkerhet](/windows-hardware/design/device-experiences/oem-vbs) (VBS) enclaves. 

Attestering är en process för att demonstrera att binärfiler för program vara korrekt instansierades på en betrodd plattform. Fjärranslutna förlitande parter kan sedan få förtroende för att endast sådan avsedd program vara körs på betrodd maskin vara. Azure-attestering är en enhetlig kund riktad tjänst och ramverk för attestering.

Azure-attestering möjliggör nyskapande säkerhets paradigm, till exempel [Azures konfidentiella data behandling](../confidential-computing/overview.md) och intelligent Edge-skydd. Kunder har begärt möjligheten att oberoende kontrol lera platsen för en dator, position för en virtuell dator (VM) på den datorn och miljön där enclaves körs på den virtuella datorn. Azure-attesteringen ger dig befogenhet att göra detta och många ytterligare kund förfrågningar.

Azure-attesteringen tar emot bevis från beräknings enheter, förvandlar dem till en uppsättning anspråk, validerar dem mot konfigurerbara principer och ger kryptografiska bevis för anspråksmedvetna program (till exempel förlitande parter och gransknings myndigheter).

## <a name="use-cases"></a>Användningsfall

Azure-attestering tillhandahåller omfattande attesterings tjänster för flera miljöer och olika användnings fall.

### <a name="sgx-attestation"></a>SGX-attestering

SGX syftar på isolering av maskin varu klass, vilket stöds på vissa modeller för Intel-processorer. SGX gör det möjligt att köra kod i sanerade utrymmen som kallas SGX-enclaves. Åtkomst-och minnes behörigheter hanteras sedan av maskin vara för att säkerställa en minimal attack yta med korrekt isolering.

Klient program kan utformas för att dra nytta av SGX-enclaves genom att delegera säkerhets känsliga uppgifter som ska äga rum inuti dessa enclaves. Dessa program kan sedan använda Azure-attestering för att rutinmässigt upprätta förtroende i enklaven och dess möjlighet att komma åt känsliga data.

### <a name="open-enclave"></a>Öppna enklaven
[Open enklaven](https://openenclave.io/sdk/) (OE) är en samling bibliotek som är riktade till att skapa en enda enhetlig enclaving-abstraktion för utvecklare för att skapa tee-baserade program. Den erbjuder en universell säker app-modell som minimerar plattforms information. Microsoft visar den som en viktig steg-vid-bricka mot democratizing maskinvarubaserade enklaven-tekniker som SGX och ökar sin upptagning på Azure.

OE standardiserar särskilda krav för verifiering av enklaven-bevis. Detta kvalificerar OE som en mycket anpassningsbar förbrukare konsument för Azure-attestering.

### <a name="tpm-attestation"></a>TPM-attestering 

Trusted Platform Module (TPM)-baserad attestering är avgörande för att ge bevis på en plattforms status. TPM fungerar som roten av förtroende och säkerhets bearbetaren för att tillhandahålla kryptografisk giltighet för mätningarna (bevis). Enheter med TPM kan förlita sig på attestering för att bevisa att start integritet inte komprometteras tillsammans med anspråken för att identifiera aktivering av funktions tillstånd under start. 

Klient program kan utformas för att dra nytta av TPM-attestering genom att delegera säkerhets känsliga uppgifter till att endast utföras efter att en plattform har verifierats för att vara säker. Dessa program kan sedan använda Azure-attestering för att rutinmässigt upprätta förtroende i plattformen och dess möjlighet att komma åt känsliga data.

## <a name="azure-attestation-can-run-in-a-tee"></a>Azure-attestering kan köras i en TEE

Azure-attestering är avgörande för konfidentiella beräknings scenarier, eftersom det utför följande åtgärder:

- Kontrollerar om enklaven-beviset är giltigt.
- Utvärderar enklaven-beviset mot en kunddefinierad princip.
- Hanterar och lagrar principer för innehavaradministration.
- Genererar och signerar en token som används av förlitande parter för att interagera med enklaven.

Azure-attestering har skapats för att köras i två typer av miljöer:
- Azure-attestering som körs i en SGX-aktiverad TEE.
- Azure-attestering körs i en icke-TEE.

Kunder med Azure-attestering har utgivit ett krav för att Microsoft ska kunna utföra åtgärder från den betrodda beräknings basen (TCB). Detta är att förhindra att Microsoft-entiteter som VM-administratörer, värd administratörer och Microsoft-utvecklare kan ändra begäran om attestering, principer och Azure-attestering – utfärdade tokens. Azure-attestering är också utformat för att köras i TEE, där funktionerna i Azure-attestering som offert validering, token-generering och Token-signering flyttas till en SGX-enklaven.

## <a name="why-use-azure-attestation"></a>Varför använda Azure-attestering

Azure-attestering är det bästa valet för att intyga TEEs eftersom det ger följande fördelar: 

- Enhetligt ramverk för att intyga flera miljöer, till exempel TPM, SGX enclaves och VBS enclaves 
- Tjänst för flera innehavare som gör det möjligt att konfigurera anpassade leverantörer och principer för utfärdande av token
- Erbjuder standardproviders som kan attestera utan konfiguration från användare
- Skyddar dess data vid användning med implementering i en SGX-enklaven
- Tjänst med hög tillgänglighet 

## <a name="business-continuity-and-disaster-recovery-bcdr-support"></a>Support för affärs kontinuitet och haveri beredskap (BCDR)

[Verksamhets kontinuitet och haveri beredskap](../best-practices-availability-paired-regions.md) (BCDR) för Azure-attestering gör det möjligt att minska tjänst störningar som orsakas av betydande tillgänglighets problem eller katastrof händelser i en region.

Kluster som distribueras i två regioner kommer att fungera oberoende under normala förhållanden. Om det uppstår ett fel eller avbrott i en region sker följande:

- Azure attestering BCDR ger sömlös redundans där kunder inte behöver göra något extra steg för att återställa
- [Azure-Traffic Manager](../traffic-manager/index.yml) för regionen identifierar att hälso avsökningen är försämrad och växlar slut punkten till kopplad region
- Befintliga anslutningar fungerar inte och får ett internt Server fel eller tids gräns problem
- Alla kontroll Plans åtgärder kommer att blockeras. Kunder kommer inte att kunna skapa attesterings leverantörer och uppdatera principer i den primära regionen
- Alla data Plans åtgärder, inklusive attesterings anrop, fortsätter att fungera i primär region

## <a name="next-steps"></a>Nästa steg
- Lär dig mer om [grundläggande koncept för Azure-attestering](basic-concepts.md)
- [Så här skapar och signerar du en policy för attestering](author-sign-policy.md)
- [Konfigurera Azure-attestering med PowerShell](quickstart-powershell.md)
