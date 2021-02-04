---
title: Förhindra fel konfiguration med Azure Security Center
description: Lär dig hur du använder Security Center alternativen "påtvinga" och "Neka" på rekommendations informations sidorna
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 02/04/2021
ms.author: memildin
ms.openlocfilehash: a3da9cdea543894aa7aec66112e28658beac84b5
ms.sourcegitcommit: f82e290076298b25a85e979a101753f9f16b720c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/04/2021
ms.locfileid: "99558194"
---
# <a name="prevent-misconfigurations-with-enforcedeny-recommendations"></a>Förhindra felkonfiguration med Enforce/Deny-rekommendationer

Fel konfiguration av säkerhet är en stor orsak till säkerhets incidenter. Security Center kan nu hjälpa till *att förhindra* fel konfiguration av nya resurser med avseende på särskilda rekommendationer. 

Den här funktionen kan hjälpa till att hålla arbets belastningarna säkra och stabilisera dina säkra poäng.

Att framtvinga en säker konfiguration, som baseras på en viss rekommendation, erbjuds i två lägen:

- Genom att använda Azure Policy **neka** kan du förhindra att resurser som inte är felfria skapas
- Med alternativet **tillämpa** kan du dra nytta av Azure policys **DeployIfNotExist** -påverkan och automatiskt reparera icke-kompatibla resurser när de skapas

Du hittar det här längst upp på sidan resursinformation för de valda säkerhets rekommendationerna (se [rekommendationer med alternativen neka/tillämpa](#recommendations-with-denyenforce-options)).

## <a name="prevent-resource-creation"></a>Förhindra att resurser skapas

1. Öppna rekommendationen som dina nya resurser måste uppfylla och välj knappen **neka** överst på sidan.

    :::image type="content" source="./media/security-center-remediate-recommendations/recommendation-deny-button.png" alt-text="Sidan rekommendation med knappen neka markerad":::

    Konfigurations fönstret öppnas och visar omfångs alternativen. 

1. Ange omfånget genom att välja den relevanta prenumerationen eller hanterings gruppen.

    > [!TIP]
    > Du kan använda de tre punkterna i slutet av raden för att ändra en enskild prenumeration eller använda kryss rutorna för att välja flera prenumerationer eller grupper. Välj sedan **ändra till neka**.

    :::image type="content" source="./media/security-center-remediate-recommendations/recommendation-prevent-resource-creation.png" alt-text="Ange omfånget för Azure Policy neka":::


## <a name="enforce-a-secure-configuration"></a>Framtvinga en säker konfiguration

1. Öppna rekommendationen att distribuera en mall distribution för om nya resurser inte uppfyller den, och välj knappen **Använd** överst på sidan.

    :::image type="content" source="./media/security-center-remediate-recommendations/recommendation-enforce-button.png" alt-text="Sidan rekommendation med knappen tillämpa markerad":::

    Konfigurations fönstret öppnas med alla princip konfigurations alternativ. 

    :::image type="content" source="./media/security-center-remediate-recommendations/recommendation-enforce-config.png" alt-text="Tvinga konfigurations alternativ":::

1. Ange omfång, tilldelnings namn och andra relevanta alternativ.

1. Välj **Granska + skapa**.

## <a name="recommendations-with-denyenforce-options"></a>Rekommendationer med alternativen neka/Använd

Dessa rekommendationer kan användas med alternativet **neka** :

- Åtkomst till lagrings konton med brand väggar och virtuella nätverkskonfigurationer bör begränsas
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
- Lagrings konton ska migreras till nya Azure Resource Manager resurser
- Lagrings konton bör begränsa nätverks åtkomsten med hjälp av regler för virtuella nätverk
- Användning av värd nätverk och portar ska begränsas
- Användningen av Pod HostPath-volym monteringar bör begränsas till en känd lista för att begränsa åtkomsten till noden från komprometterade behållare
- Giltighets tiden för certifikat som lagras i Azure Key Vault får inte överstiga 12 månader
- Virtuella datorer ska migreras till nya Azure Resource Manager-resurser
- Brand vägg för webbaserade program (WAF) måste vara aktive rad för Application Gateway
- Brand vägg för webbaserade program (WAF) måste vara aktive rad för tjänsten Azure frontend dörr service

Dessa rekommendationer kan användas med alternativet **tillämpa** :

- Granskning på SQL Server måste vara aktiverat
- Azure Backup ska vara aktiverat för virtuella datorer
- Azure Defender för SQL ska vara aktiverat på dina SQL-servrar
- Diagnostikloggar i Azure Stream Analytics ska vara aktive rad
- Diagnostikloggar i batch-konton måste vara aktiverade
- Diagnostikloggar i Data Lake Analytics ska vara aktive rad
- Diagnostikloggar i Händelsehubben måste vara aktive rad
- Diagnostikloggar i Key Vault ska vara aktive rad
- Diagnostikloggar i Logic Apps ska vara aktive rad
- Diagnostikloggar i search Services ska vara aktive rad
- Diagnostikloggar i Service Bus ska vara aktive rad