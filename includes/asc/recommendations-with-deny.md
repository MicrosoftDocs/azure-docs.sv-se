---
author: memildin
ms.service: security-center
ms.topic: include
ms.date: 02/16/2021
ms.author: memildin
ms.custom: generated
ms.openlocfilehash: fce7ba090d5aa86c85452ca41ce606d2fa52a6ee
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100645309"
---
- Åtkomst till lagrings konton med brand väggar och virtuella nätverkskonfigurationer bör begränsas
- Variabler för Automation-konton ska vara krypterade
- Azure cache för Redis bör finnas i ett virtuellt nätverk
- Azure Cosmos DB konton ska använda Kundhanterade nycklar för att kryptera data i vila
- Azure Machine Learning-arbetsytor bör krypteras med en kundhanterad nyckel (CMK)
- Azure våren Cloud ska använda nätverks inmatning
- Cognitive Services konton ska aktivera data kryptering med en kundhanterad nyckel (CMK)
- CPU-och minnes gränser för containern ska tillämpas
- Behållar avbildningar bör endast distribueras från betrodda register
- Behållar register ska krypteras med en kundhanterad nyckel (CMK)
- Container med behörighets eskalering bör undvikas
- Behållare som delar känsliga värd namn områden bör undvikas
- Behållare ska bara lyssna på tillåtna portar
- Oföränderligt (skrivskyddat) rot fil system ska tillämpas för behållare
- Key Vault nycklar ska ha ett utgångs datum
- Key Vault hemligheter bör ha ett utgångs datum
- Rensnings skydd måste vara aktiverat för nyckel valv
- Mjuk borttagning är aktiverat för nyckel valv
- Minst privilegierade Linux-funktioner bör tillämpas för behållare
- Endast säkra anslutningar till din Redis Cache ska vara aktiverade
- Åsidosättning eller inaktive ring av behållare AppArmor-profilen bör vara begränsad
- Privilegierade behållare bör undvikas
- Att köra behållare som rot användare bör undvikas
- Säker överföring till lagringskonton ska vara aktiverat
- Service Fabric-kluster ska ha egenskapen ClusterProtectionLevel inställd på EncryptAndSign
- Service Fabric kluster bör endast använda Azure Active Directory för klientautentisering
- Tjänsterna ska bara lyssna på tillåtna portar
- Lagrings kontots offentliga åtkomst ska inte tillåtas
- Lagrings konton ska migreras till nya Azure Resource Manager resurser
- Lagrings konton bör begränsa nätverks åtkomsten med hjälp av regler för virtuella nätverk
- Användning av värd nätverk och portar ska begränsas
- Användningen av Pod HostPath-volym monteringar bör begränsas till en känd lista för att begränsa åtkomsten till noden från komprometterade behållare
- Giltighets tiden för certifikat som lagras i Azure Key Vault får inte överstiga 12 månader
- Virtuella datorer ska migreras till nya Azure Resource Manager-resurser
- Brand vägg för webbaserade program (WAF) måste vara aktive rad för Application Gateway
- Brand vägg för webbaserade program (WAF) måste vara aktive rad för tjänsten Azure frontend dörr service

