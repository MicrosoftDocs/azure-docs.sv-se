---
title: Hantera säker åtkomst till resurser i eker-virtuella nätverk för P2S-klienter
titleSuffix: Azure Virtual WAN
description: Den här artikeln hjälper dig att använda Azures virtuella WAN-och Azure brand Väggs regler för att hantera säker åtkomst till virtuella nätverk för användares VPN-klienter (punkt-till-plats).
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 03/03/2021
ms.author: cherylmc
ms.openlocfilehash: 751d11fcd4b5d4c33145ee7f2b7b49971b8927ae
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102048274"
---
# <a name="manage-secure-access-to-resources-in-spoke-vnets-for-user-vpn-clients"></a>Hantera säker åtkomst till resurser i eker-virtuella nätverk för användares VPN-klienter

Den här artikeln visar hur du använder virtuella WAN-och Azure brand Väggs regler och filter för att hantera säker åtkomst för anslutningar till dina resurser i Azure över punkt-till-plats IKEv2 eller öppna VPN-anslutningar. Den här konfigurationen är användbar om du har fjärranslutna användare för vilka du vill begränsa åtkomsten till Azure-resurser, eller för att skydda dina resurser i Azure.

Stegen i den här artikeln hjälper dig att skapa arkitekturen i följande diagram så att användare av VPN-klienter får åtkomst till en speciell resurs (VM1) i ett eker VNet som är anslutet till den virtuella hubben, men inte andra resurser (VM2). Använd det här arkitektur exemplet som en grundläggande rikt linje.

:::image type="content" source="./media/manage-secure-access-resources-spoke-p2s/diagram.png" alt-text="Diagram: skyddad virtuell hubb" :::

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [Prerequisites](../../includes/virtual-wan-before-include.md)]

* Du har de värden som är tillgängliga för den konfiguration av autentisering som du vill använda. Till exempel en RADIUS-server, Azure Active Directory autentisering eller [skapa och exportera certifikat](../vpn-gateway/vpn-gateway-certificates-point-to-site.md).

## <a name="create-a-virtual-wan"></a>Skapa ett virtuellt WAN

[!INCLUDE [Create virtual WAN](../../includes/virtual-wan-create-vwan-include.md)]

## <a name="define-p2s-configuration-parameters"></a><a name= "p2s-config"></a>Definiera konfigurations parametrar för P2S

Konfigurationen för punkt-till-plats (P2S) definierar parametrar för att ansluta fjärrklienter. Det här avsnittet hjälper dig att definiera konfigurations parametrar för P2S och sedan skapa den konfiguration som ska användas för VPN-klientens profil. De instruktioner som du följer beror på vilken autentiseringsmetod du vill använda.

### <a name="authentication-methods"></a>Autentiseringsmetoder

När du väljer autentiseringsmetoden har du tre alternativ. Varje metod har särskilda krav. Välj en av följande metoder och slutför sedan stegen.

* **Azure Active Directory autentisering:** Hämta följande:

   * **Program-ID** för Azure VPN-Enterprise-programmet som registrerats i din Azure AD-klient.
   * **Utfärdaren**. Exempel: `https://sts.windows.net/your-Directory-ID`.
   * **Azure AD-klienten**. Exempel: `https://login.microsoftonline.com/your-Directory-ID`.

* **RADIUS-baserad autentisering:** Hämta RADIUS-serverns IP-adress, RADIUS-server hemlighet och certifikat information.

* **Azure-certifikat:** För den här konfigurationen krävs certifikat. Du måste antingen generera eller skaffa certifikat. Ett klient certifikat krävs för varje klient. Dessutom måste rot certifikats informationen (offentlig nyckel) överföras. Mer information om de certifikat som krävs finns i [skapa och exportera certifikat](../vpn-gateway/vpn-gateway-certificates-point-to-site.md).

[!INCLUDE [Define parameters](../../includes/virtual-wan-p2s-configuration-include.md)]

## <a name="create-the-hub-and-gateway"></a><a name="hub"></a>Skapa hubben och gatewayen

I det här avsnittet skapar du den virtuella hubben med en punkt-till-plats-Gateway. När du konfigurerar kan du använda följande exempel värden:

* **Hubb privat IP-adressutrymme:** 10.0.0.0/24
* **Klient-adresspool:** 10.5.0.0/16
* **Anpassade DNS-servrar:** Du kan lista upp till fem DNS-servrar

[!INCLUDE [Create hub](../../includes/virtual-wan-p2s-hub-include.md)]

## <a name="generate-vpn-client-configuration-files"></a><a name="generate"></a>Generera VPN-klientens konfigurationsfiler

I det här avsnittet genererar och laddar du ned filerna för konfigurations profilen. De här filerna används för att konfigurera den inbyggda VPN-klienten på klient datorn. Information om innehållet i klientens profil filer finns i [punkt-till-plats-konfiguration-certifikat](../vpn-gateway/point-to-site-vpn-client-configuration-azure-cert.md).

[!INCLUDE [Download profile](../../includes/virtual-wan-p2s-download-profile-include.md)]

## <a name="configure-vpn-clients"></a><a name="clients"></a>Konfigurera VPN-klienter

Använd den nedladdade profilen för att konfigurera fjärråtkomstklienterna. Proceduren för varje operativ system är annorlunda, följ instruktionerna som gäller för systemet.

[!INCLUDE [Configure clients](../../includes/virtual-wan-p2s-configure-clients-include.md)]

## <a name="connect-the-spoke-vnet"></a><a name="connect-spoke"></a>Anslut eker-VNet

I det här avsnittet ska du koppla det virtuella eker-nätverket för ekrar till den virtuella WAN-hubben.

[!INCLUDE [Connect spoke virtual network](../../includes/virtual-wan-connect-vnet-hub-include.md)]

## <a name="create-virtual-machines"></a><a name="create-vm"></a>Skapa virtuella datorer

I det här avsnittet skapar du två virtuella datorer i ditt VNet, VM1 och VM2. I nätverks diagrammet använder vi 10.18.0.4 och 10.18.0.5. När du konfigurerar dina virtuella datorer måste du välja det virtuella nätverk som du skapade (finns på fliken nätverk). Anvisningar för hur du skapar en virtuell dator finns i [snabb start: skapa en virtuell dator](../virtual-machines/windows/quick-create-portal.md).

## <a name="secure-the-virtual-hub"></a><a name="secure"></a>Skydda den virtuella hubben

En virtuell standard hubb har inga inbyggda säkerhets principer som skyddar resurserna i ekrar virtuella nätverk. En säker virtuell hubb använder Azure-brandväggen eller en tredjeparts-Provider för att hantera inkommande och utgående trafik för att skydda dina resurser i Azure.

Konvertera hubben till en skyddad hubb med hjälp av följande artikel: [Konfigurera Azure-brandväggen i en virtuell WAN-hubb](howto-firewall.md).

## <a name="create-rules-to-manage-and-filter-traffic"></a><a name= "create-rules"></a> Skapa regler för att hantera och filtrera trafik

Skapa regler som avgör hur Azure-brandväggen fungerar. Genom att skydda hubben ser vi till att alla paket som anger det virtuella navet omfattas av brand Väggs bearbetning innan de får åtkomst till dina Azure-resurser.

När du har slutfört de här stegen har du skapat en arkitektur som gör det möjligt för VPN-användare att komma åt den virtuella datorn med en privat IP-10.18.0.4, men **inte** åtkomst till den virtuella datorn med privata IP-10.18.0.5

1. I Azure Portal navigerar du till **brand Väggs hanteraren**.
1. Under säkerhet väljer du **Azure Firewall-principer**.
1. Välj **Skapa Azure Firewall-princip**.
1. Under **princip information** skriver du in ett namn och väljer den region som den virtuella hubben har distribuerats i.
1. Välj **Nästa: DNS-inställningar (förhands granskning)**.
1. Välj **Nästa: regler**.
1. På fliken **regler** väljer du **Lägg till en regel samling**.
1. Ange ett namn för samlingen. Ange typen som **nätverk**. Lägg till prioritet svärdet **100**.
1. Fyll i namnet på regeln, käll typen, källan, protokollet, mål portarna och måltypen, som du ser i exemplet nedan. Välj sedan **Lägg till**. Den här regeln tillåter att alla IP-adresser från VPN-klientcachen får åtkomst till den virtuella datorn med en privat IP-10.18.04, men inte någon annan resurs som är ansluten till den virtuella hubben. Skapa eventuella regler som passar dina önskade arkitektur-och behörighets regler.

   :::image type="content" source="./media/manage-secure-access-resources-spoke-p2s/rules.png" alt-text="Brandväggsregler" :::

1. Välj **Nästa: Hot information**.
1. Välj **Nästa: hubbar**.
1. På fliken **hubbar** väljer du **associera virtuella nav**.
1. Välj den virtuella hubb som du skapade tidigare och välj sedan **Lägg till**.
1. Välj **Granska + skapa**.
1. Välj **Skapa**.

Det kan ta 5 minuter eller mer för den här processen att slutföras.

## <a name="route-traffic-through-azure-firewall"></a><a name="send"></a>Dirigera trafik via Azure-brandvägg

I det här avsnittet måste du se till att trafiken dirigeras via Azure-brandväggen.

1. I portalen väljer du **skyddade virtuella hubbar** i **brand Väggs hanteraren**.
1. Välj den virtuella hubb som du skapade.
1. Under **Inställningar** väljer du **säkerhets konfiguration**.
1. Under **privat trafik** väljer du **Skicka via Azure-brandvägg**.
1. Kontrol lera att VNet-anslutningen och gren anslutningens privata trafik skyddas av Azure-brandväggen.
1. Välj **Spara**.

## <a name="validate"></a><a name="validate"></a>Verifiera

Verifiera konfigurationen av den skyddade hubben.

1. Anslut till den **säkra virtuella hubben** via VPN från klient enheten.
1. Pinga IP- **10.18.0.4** från klienten. Du bör se ett svar.
1. Pinga IP- **10.18.0.5** från klienten. Du bör inte kunna se något svar.

### <a name="considerations"></a>Överväganden

* Se till att **tabellen med effektiva vägar** på den säkra virtuella hubben har nästa hopp för privat trafik av brand väggen. Gå till den **virtuella nav** resursen för att få åtkomst till tabellen gällande vägar. Under **anslutning** väljer du **routning** och väljer sedan **effektiva vägar**. Därifrån väljer du **standard** väg tabellen.
* Kontrol lera att du har skapat regler i avsnittet [skapa regler](#create-rules) . Om de här stegen saknas kommer de regler som du har skapat inte att associeras med hubben, och routningstabellen och paket flödet använder inte Azure-brandväggen.

## <a name="next-steps"></a>Nästa steg

* Mer information om virtuellt WAN finns i [vanliga frågor och svar om virtuella](virtual-wan-faq.md)WAN.
* Mer information om Azure-brandväggen finns i [vanliga frågor och svar om Azure-brandväggen](../firewall/firewall-faq.yml).
