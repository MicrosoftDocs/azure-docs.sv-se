---
title: 'Fjärrhantering med P2S: Azure VPN Gateway'
description: Den här sidan beskriver hur du kan utnyttja VPN Gateway för att aktivera fjärr anslutning på grund av COVID-19-Pandemic.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 09/03/2020
ms.author: alzam
ms.openlocfilehash: eb0291b18952efc643de18e111154ebe58f041af
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/27/2021
ms.locfileid: "98880092"
---
# <a name="remote-work-using-azure-vpn-gateway-point-to-site"></a>Fjärran sluten arbete med Azure VPN Gateway punkt-till-plats

>[!NOTE]
>Den här artikeln beskriver hur du kan använda Azure VPN Gateway, Azure, Microsoft Network och Azure-partnerns eko system för att arbeta med fjärrnätverket och minimera nätverks problem som du riktar sig till på grund av COVID-19-krisen.
>

I den här artikeln beskrivs de alternativ som är tillgängliga för organisationer för att ställa in fjärråtkomst för sina användare eller för att komplettera sina befintliga lösningar med ytterligare kapacitet under COVID-19-epidemin.

Azure-lösningen för punkt-till-plats är molnbaserad och kan tillhandahållas snabbt för att tillgodose det ökade efter frågan av användare att arbeta hemifrån. Det kan skalas enkelt och inaktive ras precis lika enkelt och snabbt när den ökade kapaciteten inte behövs längre.

## <a name="about-point-to-site-vpn"></a><a name="p2s"></a>Om punkt-till-plats-VPN

Med en VPN-gatewayanslutning för punkt-till-plats (P2S) kan du skapa en säker anslutning till ditt virtuella nätverk från en enskild klientdator. En P2S-anslutning upprättas genom att du startar den från klientdatorn. Den här lösningen är användbar för kunder som vill ansluta till Azure-virtuella nätverk eller lokala data Center från en annan plats, t. ex. hemifrån eller från en konferens. Den här artikeln beskriver hur du gör det möjligt för användare att arbeta fjärran slutet baserat på olika scenarier.

Tabellen nedan visar de klient operativ system och de autentiseringsalternativ som är tillgängliga för dem. Det skulle vara bra att välja autentiseringsmetoden baserat på klientens operativ system som redan används. Välj till exempel OpenVPN med certifikatbaserad autentisering om du har en blandning av klient operativ system som måste anslutas. Observera också att punkt-till-plats-VPN endast stöds på vägbaserade VPN-gatewayer.

![Skärm bild som visar klient operativ system och tillgängliga autentiseringsalternativ.](./media/working-remotely-support/ostable.png "Operativsystem")

## <a name="scenario-1---users-need-access-to-resources-in-azure-only"></a><a name="scenario1"></a>Scenario 1 – användare behöver enbart åtkomst till resurser i Azure

I det här scenariot behöver fjärran vändarna bara komma åt resurser i Azure.

![Diagram som visar ett punkt-till-plats-scenario för användare som bara behöver åtkomst till resurser i Azure.](./media/working-remotely-support/scenario1.png "Scenario 1")

På hög nivå krävs följande steg för att göra det möjligt för användare att ansluta till Azure-resurser på ett säkert sätt:

1. Skapa en virtuell nätverksgateway (om det inte finns någon).
2. Konfigurera punkt-till-plats-VPN på gatewayen.

   * För certifikatautentisering följer du [den här länken](vpn-gateway-howto-point-to-site-resource-manager-portal.md#creategw).
   * För OpenVPN följer du [den här länken](vpn-gateway-howto-openvpn.md).
   * För Azure AD-autentisering följer du [den här länken](openvpn-azure-ad-tenant.md).
   * Följ [den här länken](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)för att felsöka punkt-till-plats-anslutningar.
3. Hämta och distribuera VPN-klientkonfiguration.
4. Distribuera certifikaten (om certifikatautentisering har valts) till klienterna.
5. Anslut till Azure VPN.

## <a name="scenario-2---users-need-access-to-resources-in-azure-andor-on-prem-resources"></a><a name="scenario2"></a>Scenario 2 – användare behöver åtkomst till resurser i Azure-och/eller lokal resurser

I det här scenariot behöver fjärran vändarna åtkomst till resurser i Azure och i lokala data Center (er).

![Diagram som visar ett punkt-till-plats-scenario för användare som behöver åtkomst till resurser i Azure.](./media/working-remotely-support/scenario2.png "Scenario 2")

På hög nivå krävs följande steg för att göra det möjligt för användare att ansluta till Azure-resurser på ett säkert sätt:

1. Skapa en virtuell nätverksgateway (om det inte finns någon).
2. Konfigurera punkt-till-plats-VPN på gatewayen (se [Scenario 1](#scenario1)).
3. Konfigurera en plats-till-plats-tunnel på den virtuella Azure-Nätverksgatewayen där BGP är aktiverat.
4. Konfigurera den lokala enheten för att ansluta till Azures virtuella nätverksgateway.
5. Hämta punkt-till-plats-profilen från Azure Portal och distribuera till klienter

Information om hur du konfigurerar en plats-till-plats-VPN-tunnel finns i [den här länken](./tutorial-site-to-site-portal.md).

## <a name="faq-for-native-azure-certificate-authentication"></a><a name="faqcert"></a>Vanliga frågor och svar om intern Azure-certifikatautentisering

[!INCLUDE [vpn-gateway-point-to-site-faq-include](../../includes/vpn-gateway-faq-p2s-azurecert-include.md)]

## <a name="faq-for-radius-authentication"></a><a name="faqradius"></a>Vanliga frågor och svar om RADIUS-autentisering

[!INCLUDE [vpn-gateway-point-to-site-faq-include](../../includes/vpn-gateway-faq-p2s-radius-include.md)]

## <a name="next-steps"></a>Nästa steg

* [Konfigurera en P2S-anslutning – Azure AD-autentisering](openvpn-azure-ad-tenant.md)

* [Konfigurera en P2S-anslutning – RADIUS-autentisering](point-to-site-how-to-radius-ps.md)

* [Konfigurera en P2S-anslutning – autentisering med internt Azure-certifikat](vpn-gateway-howto-point-to-site-rm-ps.md)

**"OpenVPN" är ett varumärke som tillhör OpenVPN Inc.**