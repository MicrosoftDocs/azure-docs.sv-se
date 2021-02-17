---
title: För hands versions certifikat för Azure Firewall Premium
description: För att kunna konfigurera TLS-inspektion på Azure Firewall Premium Preview måste du konfigurera och installera mellanliggande CA-certifikat.
author: vhorne
ms.service: firewall
services: firewall
ms.topic: conceptual
ms.date: 02/16/2021
ms.author: victorh
ms.openlocfilehash: 3914a82903c293cf1a8306b5ecc1f542fef83e72
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/16/2021
ms.locfileid: "100549912"
---
# <a name="azure-firewall-premium-preview-certificates"></a>För hands versions certifikat för Azure Firewall Premium 

> [!IMPORTANT]
> Azure Firewall Premium är för närvarande en offentlig för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

 Om du vill konfigurera Azure Firewall Premium-förhands granskning TLS-inspektion måste du ange ett giltigt mellanliggande CA-certifikat och sätta det i Azure Key Vault.

## <a name="certificates-used-by-azure-firewall-premium-preview"></a>Certifikat som används av Azure Firewall Premium Preview

Det finns tre typer av certifikat som används i en typisk distribution:

- **Mellanliggande CA-certifikat (CA-certifikat)**

   En certifikat utfärdare (CA) är en organisation som är betrodd att signera digitala certifikat. En certifikat utfärdare verifierar identiteten och giltighet för ett företag eller enskilda som begär ett certifikat. Om verifieringen lyckas utfärdar certifikat utfärdaren ett signerat certifikat. När servern visar certifikatet för klienten (till exempel din webbläsare) under en SSL/TLS-handskakning försöker klienten verifiera signaturen mot en lista över *kända fungerande* signaturer. Webbläsare levereras normalt med listor över de ca: er som de implicit litar på för att identifiera värdar. Om utfärdaren inte finns med i listan, som på vissa platser som signerar sina egna certifikat, varnar webbläsaren användaren om att certifikatet inte har signerats av en känd auktoritet och ber användaren om att de vill fortsätta med kommunikationen med overifierade webbplatser.

- **Server certifikat (webbplats certifikat)**

   Ett certifikat som är kopplat till ett angivet domän namn. Om en webbplats har ett giltigt certifikat, innebär det att en certifikat utfärdare har vidtagit åtgärder för att kontrol lera att webb adressen verkligen tillhör organisationen. När du skriver en URL eller följer en länk till en säker webbplats kontrollerar webbläsaren certifikatet efter följande egenskaper:
   - Webb adressen matchar adressen i certifikatet.
   - Certifikatet är signerat av en certifikat utfärdare som webbläsaren känner igen som *betrodd* utfärdare.
   
   Ibland kan användare ansluta till en server med ett ej betrott certifikat. Azure-brandväggen tar bort anslutningen som om servern avslutade anslutningen.

- **Rot certifikat för certifikat UTFÄRDAre (rot certifikat)**

   En certifikat utfärdare kan utfärda flera certifikat i form av en träd struktur. Ett rot certifikat är det översta certifikatet i trädet.

För hands versionen av Azure Firewall Premium kan fånga utgående HTTP/S-trafik och automatiskt generera ett Server certifikat för `www.website.com` . Det här certifikatet genereras med det mellanliggande CA-certifikat som du anger. Slutanvändarens webbläsare och klient program måste ha förtroende för din organisations rotcertifikatutfärdarcertifikat eller mellanliggande CA-certifikat för att den här proceduren ska fungera. 

:::image type="content" source="media/premium-certificates/certificate-process.png" alt-text="Certifikat process":::

## <a name="intermediate-ca-certificate-requirements"></a>Krav för mellanliggande certifikat UTFÄRDAre

Se till att CA-certifikatet uppfyller följande krav:

- När du distribueras som en Key Vault hemlighet måste du använda lösen ords lös PFX (PKCS12) med ett certifikat och en privat nyckel.

- Det måste vara ett enskilt certifikat och bör inte innehålla hela kedjan med certifikat.  

- Det måste vara giltigt i ett år framåt.  

- Det måste vara en privat RSA-nyckel med en minimal storlek på 4096 byte.  

- Det måste ha `KeyUsage` tillägget markerat som kritiskt med `KeyCertSign` flaggan (RFC 5280; 4.2.1.3 Key Usage).

- Tillägget måste vara `BasicContraints` markerat som kritiskt (RFC 5280; 4.2.1.9 Basic-begränsningar).  

- `CA`Flaggan måste anges till true.

- Sök vägens längd måste vara större än eller lika med en.

## <a name="azure-key-vault"></a>Azure Key Vault

[Azure Key Vault](../key-vault/general/overview.md) är ett plattforms hanterat hemligt arkiv som du kan använda för att skydda hemligheter, nycklar och TLS/SSL-certifikat. Azure Firewall Premium stöder integrering med Key Vault för Server certifikat som är kopplade till en brand Väggs princip.
 
Så här konfigurerar du nyckel valvet:

- Du måste importera ett befintligt certifikat med dess nyckel par till nyckel valvet. 
- Du kan också använda en nyckel valvs hemlighet som lagras som en lösen ords fri, bas-64-kodad PFX-fil.  En PFX-fil är ett digitalt certifikat som innehåller både privat nyckel och offentlig nyckel.
- Vi rekommenderar att du använder en import av CA-certifikat eftersom du kan konfigurera en avisering baserat på certifikatets förfallo datum.
- När du har importerat ett certifikat eller en hemlighet måste du definiera åtkomst principer i nyckel valvet för att ge identiteten behörighet att få åtkomst till certifikatet/hemligheten.
- Det angivna CA-certifikatet måste vara betrott av din Azure-arbetsbelastning. Se till att de distribueras korrekt.

Du kan antingen skapa eller återanvända en befintlig användare som tilldelats en hanterad identitet, som Azure-brandväggen använder för att hämta certifikat från Key Vault åt dig. Mer information finns i [Vad är hanterade identiteter för Azure-resurser?](../active-directory/managed-identities-azure-resources/overview.md) 

## <a name="configure-a-certificate-in-your-policy"></a>Konfigurera ett certifikat i principen

Om du vill konfigurera ett CA-certifikat i brand Väggs Premium principen väljer du din princip och väljer sedan **TLS-kontroll (för hands version)**. Välj **aktive rad** på sidan **TLS-kontroll** . Välj sedan certifikat UTFÄRDARens certifikat i Azure Key Vault, som du ser i följande figur:

:::image type="content" source="media/premium-certificates/tls-inspection.png" alt-text="Översikts diagram över Azure Firewall Premium":::
 
> [!IMPORTANT]
> Om du vill se och konfigurera ett certifikat från Azure Portal måste du lägga till ditt Azure-användarkonto i Key Vault åtkomst princip. Ge ditt användar konto **Get** och **lista** under **hemliga behörigheter**.
   :::image type="content" source="media/premium-certificates/secret-permissions.png" alt-text="Azure Key Vault åtkomst princip":::


## <a name="troubleshooting"></a>Felsökning

Om CA-certifikatet är giltigt, men du inte kan komma åt FQDN eller URL: er under TLS-kontroll, kontrollerar du följande objekt:

- Se till att webb Server certifikatet är giltigt.  

- Se till att rot certifikat utfärdarens certifikat är installerat på klientens operativ system.  

- Se till att webbläsaren eller HTTPS-klienten innehåller ett giltigt rot certifikat. Firefox och vissa andra webbläsare kan ha särskilda certifierings principer.  

- Se till att URL-måltypen i din program regel täcker rätt sökväg och eventuella andra hyperlänkar som är inbäddade på HTML-sidan för målet. Du kan använda jokertecken för att enkelt täcka hela den obligatoriska URL-sökvägen.  


## <a name="next-steps"></a>Nästa steg

- [Läs mer om Azure Firewall Premium-funktioner](premium-features.md)
