---
title: Gransknings loggar för Azure-attestering
description: Beskriver fullständiga gransknings loggar som samlas in för Azure-attestering
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: reference
ms.date: 11/23/2020
ms.author: mbaldwin
ms.openlocfilehash: 1fa4a458a4e3e1df1d84c343a32e3a41a4a25e75
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "95759026"
---
# <a name="audit-logs-for-azure-attestation"></a>Gransknings loggar för Azure-attestering

Gransknings loggar är säkra, oföränderliga och tidsstämplade poster med diskreta händelser som har hänt över tid. Dessa loggar fångar viktiga händelser som kan påverka funktionerna i din attesterings instans.

Azure-attestering hanterar attesterings instanser och de principer som är kopplade till dem. Åtgärder som är kopplade till instans hantering och princip ändringar granskas och loggas.

Den här artikeln innehåller information om de händelser som loggas, vilken information som samlas in och var dessa loggar finns.

## <a name="about-audit-logs"></a>Om gransknings loggar

Azure-attestering använder kod för att skapa gransknings loggar för händelser som påverkar hur attesteringen utförs. Detta kokar vanligt vis ned till hur eller när princip ändringar görs i din attesterings instans samt vissa administratörs åtgärder.

### <a name="auditable-events"></a>Gransknings bara händelser
Här följer några av de gransknings loggar som vi samlar in:

|     Event/API                              |     Händelsebeskrivning                                                                         |
|--------------------------------------------|-----------------------------------------------------------------------------------------------|
|     Skapa instans                        |     Skapar en ny instans av en attesterings tjänst. |
|     Förstör instans                       |     Förstör en instans av en attesterings tjänst. |
|     Lägg till princip certifikat                 |     Tillägg av ett certifikat till den aktuella uppsättningen princip hanterings certifikat. |
|     Ta bort princip certifikat              |     Ta bort ett certifikat från den aktuella uppsättningen princip hanterings certifikat. |
|     Ange aktuell princip                     |     Anger attesterings principen för en specifik TEE-typ. |
|     Återställ attesterings princip               |     Återställer attesterings principen för en specifik TEE-typ. |
|     Förbered för att uppdatera princip               |     Förbered för att uppdatera attesterings principen för en specifik TEE-typ. |
|     Torkade klient organisationer efter haveri beredskap       |     Återskapar alla attesterings klienter i den här instansen av attesterings tjänsten. Detta kan endast utföras av administratörer för attesterings tjänsten. |

### <a name="collected--information"></a>Samlad information
Azure-attestering samlar in följande information för var och en av dessa händelser:

- Åtgärds namn
- Åtgärden lyckades
- Åtgärds anrop, som kan vara något av följande:
    - Azure AD UPN
    - Objekt-ID
    - Certifikat
    - Azure AD-klient-ID
- Åtgärds mål, som kan vara något av följande:
    - Miljö
    - Tjänste region
    - Tjänst roll
    - Tjänst roll instans
    - Resurs-ID
    - Resurs region

### <a name="sample-audit-log"></a>Exempel på Gransknings logg

Gransknings loggar finns i JSON-format. Här är ett exempel på hur en Gransknings logg kan se ut.

```json
{"operationName":"SetCurrentPolicy","resultType":"Success","resultDescription":null,"auditEventCategory":["ApplicationManagement"],"nCloud":null,"requestId":null,"callerIpAddress":null,"callerDisplayName":null,"callerIdentities":[{"callerIdentityType":"ObjectID","callerIdentity":"<some object ID>"},{"callerIdentityType":"TenantId","callerIdentity":"<some tenant ID>"}],"targetResources":[{"targetResourceType":"Environment","targetResourceName":"PublicCloud"},{"targetResourceType":"ServiceRegion","targetResourceName":"EastUS2"},{"targetResourceType":"ServiceRole","targetResourceName":"AttestationRpType"},{"targetResourceType":"ServiceRoleInstance","targetResourceName":"<some service role instance>"},{"targetResourceType":"ResourceId","targetResourceName":"/subscriptions/<some subscription ID>/resourceGroups/<some resource group name>/providers/Microsoft.Attestation/attestationProviders/<some instance name>"},{"targetResourceType":"ResourceRegion","targetResourceName":"EastUS2"}],"ifxAuditFormat":"Json","env_ver":"2.1","env_name":"#Ifx.AuditSchema","env_time":"2020-11-23T18:23:29.9427158Z","env_epoch":"MKZ6G","env_seqNum":1277,"env_popSample":0.0,"env_iKey":null,"env_flags":257,"env_cv":"##00000000-0000-0000-0000-000000000000_00000000-0000-0000-0000-000000000000_00000000-0000-0000-0000-000000000000","env_os":null,"env_osVer":null,"env_appId":null,"env_appVer":null,"env_cloud_ver":"1.0","env_cloud_name":null,"env_cloud_role":null,"env_cloud_roleVer":null,"env_cloud_roleInstance":null,"env_cloud_environment":null,"env_cloud_location":null,"env_cloud_deploymentUnit":null}
```

## <a name="access-audit-logs"></a>Åtkomst gransknings loggar

Dessa loggar lagras i Azure och går inte att komma åt direkt. Om du behöver åtkomst till dessa loggar kan du läsa in ett support ärende. Mer information finns i [kontakta Microsoft Support](https://azure.microsoft.com/support/options/). 

När support ärendet har arkiverats kommer Microsoft att hämta och ge dig åtkomst till dessa loggar.
