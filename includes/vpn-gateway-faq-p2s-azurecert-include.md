---
title: ta med fil
description: ta med fil
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 08/14/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 5ef67580928a45609f50d3fe798eb9d054265c0a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "93375786"
---
[!INCLUDE [P2S FAQ All](vpn-gateway-faq-p2s-all-include.md)]

### <a name="what-should-i-do-if-im-getting-a-certificate-mismatch-when-connecting-using-certificate-authentication"></a>Vad gör jag om jag får ett certifikat matchnings fel när jag ansluter med certifikatautentisering?

Avmarkera **"verifiera serverns identitet genom att verifiera certifikatet"** eller **Lägg till Server-FQDN tillsammans med certifikatet när du** skapar en profil manuellt. Du kan göra detta genom att köra **Rasphone** från en kommando tolk och välja profilen från List rutan.

Att kringgå Server identitets verifiering rekommenderas inte i allmänhet, men med Azure certifikatautentisering används samma certifikat för Server verifiering i protokollet IKEv2/SSTP (VPN Tunneling Protocol) och EAP-protokollet. Eftersom Server certifikatet och FQDN redan har verifierats av VPN tunneling-protokollet, är det redundant att verifiera samma igen i EAP.

![punkt-till-plats-autentisering](./media/vpn-gateway-faq-p2s-all-include/servercert.png "Server certifikat")

### <a name="can-i-use-my-own-internal-pki-root-ca-to-generate-certificates-for-point-to-site-connectivity"></a>Kan jag använda min egen interna PKI-rotcertifikatutfärdare för att generera certifikat för punkt-till-plats-anslutning?

Ja. Tidigare kunde bara självsignerade rotcertifikat användas. Du kan fortfarande överföra 20 rotcertifikat.

### <a name="can-i-use-certificates-from-azure-key-vault"></a>Kan jag använda certifikat från Azure Key Vault?

Nej.

### <a name="what-tools-can-i-use-to-create-certificates"></a>Vilka verktyg kan jag använda för att skapa certifikat?

Du kan använda lösningen för företags-PKI (din interna PKI), Azure PowerShell, MakeCert och OpenSSL.

### <a name="are-there-instructions-for-certificate-settings-and-parameters"></a><a name="certsettings"></a>Finns det anvisningar för certifikatinställningar och -parametrar?

* **Lösning för intern PKI/företags-PKI:** Se hur du kan [generera certifikat](../articles/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#generatecert).

* **Azure PowerShell:** Se anvisningarna i artikeln om [Azure PowerShell](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md).

* **MakeCert:** Se anvisningarna i artikeln om [MakeCert](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-makecert.md).

* **OpenSSL** 

    * Se till att konvertera rotcertifikatet till Base64 när du exporterar certifikat.

    * För klientcertifikatet:

      * Ange längden 4096 när du skapar den privata nyckeln.
      * För parametern *-tillägg* anger du *usr_cert* när du skapar certifikatet.
