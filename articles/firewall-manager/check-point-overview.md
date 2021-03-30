---
title: Skydda virtuella Azure-nav med Check Point Cloudguard Connect
description: Lär dig mer om Check Point CloudGuard Connect för att skydda virtuella Azure-nav
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: conceptual
ms.date: 10/30/2020
ms.author: victorh
ms.openlocfilehash: 3c61dc689d19e1a7d6f9b6dbefae846e9458d750
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "93146866"
---
# <a name="secure-virtual-hubs-using-check-point-cloudguard-connect"></a>Skydda virtuella hubbar med Check Point Cloudguard Connect

Check Point CloudGuard Connect är en betrodd säkerhets partner i Azure Firewall Manager. Det skyddar globalt distribuerade avdelnings kontor till Internet (B2I) eller virtuella nätverk till Internet (V2I) med avancerat skydd mot hot. 

Med en enkel konfiguration i Azure Firewall Manager kan du dirigera gren hubb och virtuella nätverks anslutningar till Internet via CloudGuard Connect Security as a Service (SECaaS). Trafiken skyddas under överföring från hubben till kontroll punktens moln tjänst i IPsec VPN-tunnlar.

När du aktiverar automatisk synkronisering i Check Point-portalen skyddas alla resurser som är markerade som *skyddade* i Azure Portal automatiskt. Du behöver inte hantera dina till gångar två gånger. Du väljer bara att skydda dem en gång i Azure Portal.

Check Point förenar flera säkerhets tjänster under ett paraply. Integrerad säkerhets trafik dekrypteras en gång och inspekteras i ett enda steg. Program kontroll, URL-filtrering och innehålls medvetenhet (DLP) upprätthåller säker webb användning och skyddar dina data. IP-adresser och Antivirus skydd skyddar användare från kända nätverks sårbarheter. Mot bot blockerar anslutningar till kommando-och kontroll servrar och varnar dig om en värd är infekterad.

Hot emulering (sandbox) skyddar användare från okända och noll-dagars hot. Check Point SandBlast Zero-Day Protection är en Boxing teknik som är värd för en där filerna snabbt placeras i karantän och inspekteras. Den körs i en virtuell Sand låda för att identifiera skadligt beteende innan den går in i nätverket. Det förhindrar hot innan skadan görs för att spara värdefull tid för personal som svarar på hot. 

## <a name="deployment-example"></a>Exempel på distribution

Titta på följande video för att se hur du distribuerar Check Point CloudGuard Connect som en betrodd Azure-säkerhetspartner.

> [!VIDEO https://www.youtube.com/embed/C8AuN76DEmU]

## <a name="next-steps"></a>Nästa steg

- [Distribuera en säkerhetspartnerprovider](deploy-trusted-security-partner.md)