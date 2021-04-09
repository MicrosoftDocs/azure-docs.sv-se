---
title: Konfidentiella behållare på Azure Kubernetes service (AKS)
description: Läs mer om stöd för ej modifierade behållare på konfidentiella behållare.
services: container-service
author: agowdamsft
ms.topic: article
ms.date: 2/11/2020
ms.author: amgowda
ms.service: container-service
ms.subservice: confidential-computing
ms.openlocfilehash: 280f75e8d18d16dd76d0730a90755774af34d6f6
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105933584"
---
# <a name="confidential-containers"></a>Konfidentiella behållare

## <a name="overview"></a>Översikt

Gör det möjligt för utvecklare att använda ett **befintligt Docker-program (ny eller befintlig)** och köra det på ett säkert sätt på Azure Kubernetes service (AKS) via stöd för konfidentiella databeräknings noder.

Konfidentiella behållare hjälper till att skydda:

- data integritet 
- data sekretess
- kod integritet
- skydd av container kod genom kryptering
- maskin varubaserade garantier
- Tillåt körning av befintliga appar
- skapa förtroende för maskin varu roten
- ta bort värd administratör, Kubernetes-administratör, hypervisor från förtroende gränser

En maskinvarubaserad betrodd körnings miljö (TEE) är en viktig komponent som används för att ge starka garantier genom maskinvaru-och program varu mått från TCB-komponenter (Trusted Computing Base). Kontroller av dessa mått hjälper till att verifiera den förväntade beräkningen och verifiera all manipulering av behållar apparna.

Konfidentiella behållare stöder anpassade program som har utvecklats med **python, Java, Node JS osv. eller paketerade behållar program som nginx, Redis cache, memcache** och så vidare, som ska köras oförändrade på AKS med konfidentiella data behandlings noder.

Konfidentiella behållare är den snabbaste sökvägen till behållar sekretess och behöver bara paketera om de befintliga Docker-behållar programen och kommer inte att kräva ändringar i program koden. Konfidentiella behållare är Docker-behållar program som är paketerade för körning i en TEE. Vissa konfidentiella behållar tjänster erbjuder också behållare kryptering som kan hjälpa till att skydda behållar koden under lagring och transport och samtidigt som den monteras på värden. Med behållarens kryptering kan du gå vidare och skydda den kod/modell som paketeras i behållaren med dess krypterings nyckel som är kopplad till TEE.

Nedan följer processen för konfidentiella behållare från utveckling till distribution ![ av den konfidentiella behållaren för att bearbeta.](./media/confidential-containers/how-to-confidential-container.png)

## <a name="confidential-container-enablers"></a>Konfidentiella behållar aktiveringar
För att köra en befintlig Docker-behållare kräver en SGX-programvara så att programmet kan använda särskilda CPU-instruktioner som görs tillgängliga för att minska området för att bifoga yta och inte ta hänsyn till något beroende på gäst operativ system. När de har omslutits med program vara för SGX-körtid startar behållare automatiskt i den skyddade enclaves, vilket innebär att gäst operativ systemet, värd-OS eller hypervisor-program från förtroende gränser tas bort. Den isolerade körningen i en nod (virtuell dator) med i minnes data kryptering som backas upp av maskin varan minskar de övergripande angrepps områdena och minskar sårbarheterna med operativ system eller hypervisor-lager.

Konfidentiella behållare stöds fullt ut på AKS och aktive ras via Azure-partner och Open-Source OSS-projekt (program vara). Utvecklare kan välja program varu leverantörer baserat på funktioner, integrering med Azure-tjänster och stöd för verktyg.

## <a name="partner-enablers"></a>Partner aktiveringar
> [!NOTE]
> Nedanstående lösningar erbjuds via Azure-partner och kan medföra licens avgifter. Verifiera partner program villkoren oberoende av varandra. 

### <a name="anjuna"></a>Anjuna

[Anjuna](https://www.anjuna.io/) tillhandahåller SGX-plattforms program vara som gör att du kan köra oförändrade behållare på AKS. Läs mer om funktionerna och kolla in exempel programmen [här](https://www.anjuna.io/microsoft-azure-confidential-computing-aks-lp).

Kom igång med ett exempel Redis Cache och python-anpassat program [här](https://www.anjuna.io/microsoft-azure-confidential-computing-aks-lp)

![Anjuna process](./media/confidential-containers/anjuna-process-flow.png)

### <a name="fortanix"></a>Fortanix

[Fortanix](https://www.fortanix.com/) erbjuder utvecklare ett val av en portal och CLI-baserad upplevelse för att ta med sina program i behållare och konvertera dem till SGX-kompatibla konfidentiella behållare utan att behöva ändra eller kompilera om programmet. Fortanix ger flexibiliteten att köra och hantera den bredaste uppsättningen program, inklusive befintliga program, nya enklaven program och förpaketerade program. Användare kan börja med [konfidentiellt data behandlings](https://em.fortanix.com/) hanterings gränssnitt eller [REST-API: er](https://www.fortanix.com/api/em/) för att skapa konfidentiella behållare genom att följa [Snabbstart](https://support.fortanix.com/hc/en-us/articles/360049658291-Fortanix-Confidential-Container-on-Azure-Kubernetes-Service) guide för Azure Kubernetes service.

![Distributions process för Fortanix](./media/confidential-containers/fortanix-confidential-containers-flow.png)

### <a name="scone-scontain"></a>Scone (Scontain)

[Scone](https://scontain.com/index.html?lang=en) stöder säkerhets principer som kan generera certifikat, nycklar och hemligheter och säkerställer att de endast visas för bestyrkade tjänster i ett program. På så sätt bekräftar tjänsterna i ett program automatiskt varandra via TLS – utan att behöva ändra programmen eller TLS. Detta beskrivs med hjälp av ett enkelt kolv program här: https://sconedocs.github.io/flask_demo/  

SCONE kan konvertera befintliga binärfiler till program som körs i enclaves utan att behöva ändra programmet eller kompilera om programmet. SCONE skyddar också tolkade språk som python genom att **kryptera** både datafiler och python-Koda filer. Med hjälp av en SCONE säkerhets princip kan en skydda de krypterade filerna mot obehöriga åtkomster, ändringar och återställningar. Hur du "sconify" ett befintligt python-program beskrivs [här](https://sconedocs.github.io/sconify_image/)

![Scontain-flöde](./media/confidential-containers/scone-workflow.png)

Scone-distributioner på konfidentiella databeräknings noder med AKS stöds helt och är integrerade med andra Azure-tjänster. Kom igång med ett exempel program här https://sconedocs.github.io/aks/


## <a name="oss-enablers"></a>OSS-aktiveringar 
> [!NOTE]
> Nedanstående lösningar erbjuds genom Open-Source projekt och är inte direkt kopplade till Azures konfidentiella data behandling (ACC) eller Microsoft.  

### <a name="graphene"></a>Graphene

[Graphene](https://grapheneproject.io/) är ett enkelt gäst operativ system som är utformat för att köra ett enda Linux-program med minimala krav på värden. Graphene kan köra program i en isolerad miljö med fördelar som är jämförbara med att köra ett fullständigt operativ system och har stöd för att konvertera befintliga Docker-behållar program till Graphene-avskärmade behållare (generalsekretariatet).

Kom igång med ett exempel program och en distribution på AKS [här](https://graphene.readthedocs.io/en/latest/cloud-deployment.html#azure-kubernetes-service-aks)

### <a name="occlum"></a>Occlum
[Occlum](https://occlum.io/) är ett minnes säkert, LibOS (Multi-process Library OS) för Intel SGX. Det gör att äldre program kan köras på SGX med liten till inga ändringar i käll koden. Occlum skyddar mot konfidentialiteten för användar arbets belastningar samtidigt som du kan enkelt lyfta och byta till befintliga Docker-program.

Occlum stöder AKS-distributioner. Följ distributions anvisningarna med olika exempel program [här](https://github.com/occlum/occlum/blob/master/docs/azure_aks_deployment_guide.md)


## <a name="confidential-containers-demo"></a>Demo om konfidentiella behållare
Visa demon för konfidentiell sjukvård med konfidentiella behållare. Exempel finns [här](/azure/architecture/example-scenario/confidential/healthcare-inference). 

> [!VIDEO https://www.youtube.com/embed/PiYCQmOh0EI]


## <a name="get-in-touch"></a>Kontakta touch

Har du frågor om implementeringen eller vill bli en aktiverings fråga? Skicka ett e-postmeddelande till produkt teamet **acconaks@microsoft.com**

## <a name="reference-links"></a>Referens länkar

[Microsoft Azure Attestation](../attestation/overview.md)

[DCsv2 Virtual Machines](virtual-machine-solutions.md)

[Azure Kubernetes Service (AKS)](../aks/intro-kubernetes.md)
