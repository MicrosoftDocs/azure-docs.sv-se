---
title: Översikt över Azure konfidentiella data behandling
description: Översikt över Azures konfidentiella data behandling (ACC)
services: virtual-machines
author: JBCook
ms.service: virtual-machines
ms.subservice: confidential-computing
ms.topic: overview
ms.date: 09/22/2020
ms.author: JenCook
ms.openlocfilehash: c3ef6a764123f52583f081a3b152651b9bb8b8b7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102554198"
---
# <a name="confidential-computing-on-azure"></a>Konfidentiell data behandling i Azure

Med Azures konfidentiella data behandling kan du isolera känsliga data medan de bearbetas i molnet. Många branscher använder konfidentiell data behandling för att skydda sina data genom att använda konfidentiell dator användning för att:

- Säkra finansiella data
- Skydda patient information
- Köra Machine Learning-processer med känslig information
- Utföra algoritmer på krypterade data uppsättningar från flera källor


## <a name="overview"></a>Översikt
<p><p>


> [!VIDEO https://www.youtube.com/embed/rT6zMOoLEqI]

Vi vet att det är viktigt att skydda dina moln data. Vi hör dina problem. Här är några frågor som våra kunder kan ha när de flyttar känsliga arbets belastningar till molnet: 

- Hur gör jag för att se till att Microsoft inte kan komma åt data som inte är krypterade?
- Hur gör jag för att att förhindra säkerhetshot från privilegierade administratörer i mitt företag?
- Vilka är fler sätt att förhindra att tredje part får åtkomst till känsliga kund data?

Microsoft Azure hjälper dig att minimera din attack yta så att du får bättre data skydd. Azure erbjuder redan många verktyg för att skydda [**data i vila**](../security/fundamentals/encryption-atrest.md) genom modeller som kryptering på klient sidan och kryptering på Server sidan. Dessutom erbjuder Azure mekanismer för att kryptera [**data under överföring**](../security/fundamentals/data-encryption-best-practices.md#protect-data-in-transit) via säkra protokoll som TLS och https. På den här sidan introduceras en tredje del av data krypteringen – kryptering av **data som används**.

## <a name="introduction-to-confidential-computing"></a>Introduktion till konfidentiell dator användning 

Konfidentiell data behandling är en bransch term som definieras av kopia ( [konfidentiell Computing Consortium](https://confidentialcomputing.io/) ) – en grund som är dedikerad för att definiera och påskynda införandet av konfidentiell dator användning. KOPIA definierar konfidentiell bearbetning som: det skydd av data som används genom att utföra beräkningar i en maskinvarubaserad betrodd körnings miljö (TEE).

En TEE är en miljö som tillämpar körning av enbart auktoriserad kod. Alla data i TEE kan inte läsas eller manipuleras av någon kod utanför miljön. 

### <a name="lessen-the-need-for-trust"></a>Minskar behovet av förtroende
Att köra arbets belastningar i molnet kräver förtroende. Du ger förtroendet till olika providrar som aktiverar olika komponenter i ditt program.


Program **varu leverantörer**: lita på program vara genom att distribuera lokal, med hjälp av öppen källkod eller genom att skapa interna program program.

**Maskin varu leverantörer**: betrodd maskin vara genom att använda lokal maskin vara eller intern maskin vara. 

**Infrastruktur leverantörer**: lita på moln leverantörer eller hantera dina egna lokala data Center.


Med Azures konfidentiella data behandling blir det enklare att lita på moln leverantören genom att minska behovet av förtroende mellan olika aspekter av beräknings molnets infrastruktur. Azures konfidentiella data behandling minimerar förtroende för värd operativ systemets kernel, hypervisor, VM-administratör och värd administratör.

### <a name="reducing-the-attack-surface"></a>Minska attack ytan
Den betrodda beräknings basen (TCB) syftar på alla Systems maskin vara, inbyggd program vara och program varu komponenter som tillhandahåller en säker miljö. Komponenterna inuti TCB betraktas som "kritiska". Om en komponent inuti TCB komprometteras kan hela systemets säkerhet äventyras. 

En lägre TCB innebär högre säkerhet. Det finns mindre risk för exponering för olika sårbarheter, skadlig kod, attacker och skadliga personer. Med Azures konfidentiella data behandling kan du sänka TCB för dina moln arbets belastningar genom att erbjuda TEEs. TEEs minska dina TCB till binärfiler, kod och bibliotek för betrodda körningar. När du använder Azure-infrastruktur och tjänster för konfidentiell data behandling kan du ta bort alla Microsoft från din TCB.


## <a name="using-azure-for-cloud-based-confidential-computing"></a>Använda Azure för molnbaserad konfidentiell data behandling <a id="cc-on-azure"></a>

Med Azures konfidentiella data behandling kan du utnyttja funktioner för konfidentiell användning i en virtualiserad miljö. Nu kan du använda verktyg, program och moln infrastruktur för att bygga ovanpå säker maskin vara.  

**Förhindra obehörig åtkomst**: kör känsliga data i molnet. Lita på att Azure ger bästa möjliga data skydd, med liten till ingen ändring från vad som gjorts idag.

**Regelefterlevnad: Migrera** till molnet och behåll fullständig kontroll över data för att uppfylla myndighets bestämmelser för att skydda personlig information och säkra organisations-IP.

**Säkert och ej betrott samarbete**: ta itu med problem med arbets skala genom att kamma data mellan organisationer, även konkurrenter, för att låsa upp breda data analyser och djupare insikter.

**Isolerad bearbetning**: erbjud en ny våg med produkter som tar bort ansvar för privata data med blind bearbetning. Användar data kan inte till och med hämtas av tjänst leverantören. 

## <a name="get-started"></a>Kom igång
### <a name="azure-compute"></a>Azure Compute
Bygg program ovanpå konfidentiella Compute IaaS-erbjudanden i Azure.
- Virtual Machines (VM): [DCsv2-serien](confidential-computing-enclaves.md)
- Azure-Kubernetes (AKS): [dirigera konfidentiella behållare](confidential-nodes-aks-overview.md)

### <a name="azure-security"></a>Säkerhet i Azure 
Se till att dina arbets belastningar är säkra genom verifierings metoder och maskin vara-baserad nyckel hantering. 
- Attestering: [Microsoft Azure attestering (förhands granskning)](../attestation/overview.md)
- Nyckel hantering: hanterad-HSM (för hands version)

### <a name="develop"></a>Utveckla
Börja använda utveckla enklaven-medvetna program och distribuera konfidentiella algoritmer med hjälp av det konfidentiella inferencing-ramverket.
- Skriv program som ska köras på virtuella DCsv2-datorer: [Open-ENKLAVEN SDK](https://github.com/openenclave/openenclave)
- Konfidentiella ML-modeller i ONNX Runtime: [konfidentiell inferencing (beta)](https://aka.ms/confidentialinference)

## <a name="next-steps"></a>Nästa steg

Distribuera en DCsv2-Series virtuell dator och installera OE SDK på den.

> [!div class="nextstepaction"]
> [Distribuera en konfidentiell data behandlings dator i Azure Marketplace](quick-create-marketplace.md)