---
title: Översikt över fristående Service Fabric-kluster
description: Service Fabric kluster som körs på Windows Server och Linux, vilket innebär att du kan distribuera och vara värd för Service Fabric program överallt där du kan köra Windows Server eller Linux.
ms.topic: conceptual
ms.date: 02/01/2019
ms.openlocfilehash: a3162a3d02510ce8efab6c5bcac0f1fdd2b2539b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94684006"
---
# <a name="overview-of-service-fabric-standalone-clusters"></a>Översikt över Service Fabric fristående kluster

Ett Service Fabric kluster är en nätverksansluten uppsättning virtuella eller fysiska datorer som dina mikrotjänster distribueras och hanteras i. En dator eller en virtuell dator som ingår i ett kluster kallas för en klusternod. Kluster kan skalas till tusentals noder. Om du lägger till nya noder i klustret, Service Fabric balanseringen av tjänste partitionens repliker och instanser över det ökade antalet noder. Övergripande program prestanda förbättras och konkurrens för åtkomst till minnes minskningar. Om noderna i klustret inte används effektivt kan du minska antalet noder i klustret. Service Fabric åter balanserar partitionens repliker och instanser över det minskade antalet noder för att bättre kunna använda maskin varan på varje nod.

En nodtyp definierar storlek, antal och egenskaper för en uppsättning noder i klustret. Varje nodtyp kan sedan skalas upp eller ned oberoende av de andra, ha olika portar öppna och ha olika kapacitet. Nodtyper används till att definiera roller för en uppsättning klusternoder, till exempel en ”klientdel” eller ”serverdel”. Klustret kan innehålla fler än en nodtyp, men den primära nodtypen måste innehålla minst fem virtuella datorer för produktionskluster (eller minst tre virtuella datorer för testkluster). [Service Fabric-systemtjänster](service-fabric-technical-overview.md#system-services) placeras på noderna med den primära nodtypen.

Processen för att skapa ett Service Fabric-kluster lokalt liknar processen för att skapa ett kluster i valfritt moln som du väljer med en uppsättning virtuella datorer. De första stegen för att etablera de virtuella datorerna styrs av den moln leverantör eller lokala miljö som du använder. När du har en uppsättning virtuella datorer med aktive rad nätverks anslutning kan du konfigurera Service Fabric-paketet, redigera kluster inställningarna och köra kluster skapande-och hanterings skripten är identiska. Detta säkerställer att din kunskap och erfarenhet av drift och hantering av Service Fabric kluster kan överföras när du väljer att rikta in nya värd miljöer.

## <a name="cluster-security"></a>Klustersäkerhet

Ett Service Fabric-kluster är en resurs som du äger.  Det är ditt ansvar att skydda dina kluster så att obehöriga användare kan ansluta till dem. Ett säkert kluster är särskilt viktigt när du kör produktions arbets belastningar i klustret.

> [!NOTE]
> Windows-autentisering baseras på Kerberos. NTLM stöds inte som autentiseringstyp.
>
> När det är möjligt ska du använda 509 certifikatautentisering för Service Fabric kluster.

### <a name="node-to-node-security"></a>Säkerhet från nod till nod

Säkerhet från nod till nod skyddar kommunikationen mellan de virtuella datorerna eller datorerna i ett kluster. Det här säkerhets scenariot säkerställer att endast datorer som har behörighet att ansluta till klustret kan delta i värdbaserade program och tjänster i klustret. Service Fabric använder X. 509-certifikat för att skydda ett kluster och tillhandahålla funktioner för program säkerhet.  Ett kluster certifikat krävs för att skydda kluster trafik och tillhandahålla kluster-och serverautentisering.  Självsignerade certifikat kan användas för test kluster, men ett certifikat från en betrodd certifikat utfärdare bör användas för att skydda produktions kluster.

Windows-säkerhet kan också aktive ras för ett fristående Windows-kluster. Om du har Windows Server 2012 R2 och Windows Active Directory rekommenderar vi att du använder Windows-säkerhet med grupphanterade tjänst konton. Annars använder du Windows-säkerhet med Windows-konton.

Mer information finns i [nod-till-nod-säkerhet](service-fabric-cluster-security.md#node-to-node-security)

### <a name="client-to-node-security"></a>Säkerhet från klient till nod

Säkerhet mellan klienter autentiserar klienter och skyddar kommunikationen mellan en klient och enskilda noder i klustret. Den här typen av säkerhet hjälper till att säkerställa att endast behöriga användare kan komma åt klustret och de program som distribueras i klustret. Klienterna identifieras unikt genom antingen deras X. 509-certifikat säkerhets uppgifter. Valfritt antal valfria klient certifikat kan användas för att autentisera administratörs-eller användar klienter med klustret.

Förutom klient certifikat kan Azure Active Directory också konfigureras för att autentisera klienter med klustret.

Mer information finns i [klient-till-nod-säkerhet](service-fabric-cluster-security.md#client-to-node-security)

### <a name="service-fabric-role-based-access-control"></a>Service Fabric rollbaserad åtkomst kontroll
Service Fabric stöder också åtkomst kontroll för att begränsa åtkomsten till vissa kluster åtgärder för olika användar grupper. Detta gör klustret säkrare. Två åtkomst kontroll typer stöds för klienter som ansluter till ett kluster: administratörs roll och användar roll.  

Mer information finns i [Service Fabric rollbaserad åtkomst kontroll](service-fabric-cluster-security.md#service-fabric-role-based-access-control).

## <a name="scaling"></a>Skalning

Program krav ändras med tiden. Du kan behöva öka kluster resurserna för att möta ökad program belastning eller nätverks trafik eller minska kluster resurserna när efter frågan går vidare. När du har skapat ett Service Fabric-kluster kan du skala klustret vågrätt (ändra antalet noder) eller lodrätt (ändra resurserna för noderna). Du kan skala klustret när som helst, även när arbets belastningar körs på klustret. När klustret skalas, skalas programmen automatiskt.

Mer information finns i [skala fristående kluster](service-fabric-cluster-scaling-standalone.md).

## <a name="upgrading"></a>Uppgradera

Ett fristående kluster är en resurs som du helt äger. Du ansvarar för korrigering av underliggande operativ system och att initiera Fabric-uppgraderingar. Du kan ange att klustret ska ta emot automatiska körnings uppgraderingar, när Microsoft släpper en ny version eller väljer att välja en version som stöds för körning. Förutom Fabric-uppgraderingar kan du också korrigera operativ systemet och uppdatera kluster konfigurationen, till exempel certifikat eller program portar. 

Mer information finns i [Uppgradera fristående kluster](service-fabric-cluster-upgrade-standalone.md).

## <a name="supported-operating-systems"></a>Operativsystem som stöds

Du kan skapa kluster på virtuella datorer eller datorer som kör dessa operativ system (Linux stöds ännu inte):

* Windows Server 2012 R2
* Windows Server 2016 
* Windows Server 2019

## <a name="next-steps"></a>Nästa steg

Läs mer om att [skydda](service-fabric-cluster-security.md), [skala](service-fabric-cluster-scaling-standalone.md)och [Uppgradera](service-fabric-cluster-upgrade-standalone.md) fristående kluster.

Läs mer om [Service Fabric support alternativ](service-fabric-support.md).
