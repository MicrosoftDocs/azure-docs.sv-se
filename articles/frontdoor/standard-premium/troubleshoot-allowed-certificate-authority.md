---
title: Tillåtna certifikat utfärdare för Azure front dörr standard/Premium (för hands version)
description: I den här artikeln visas alla certifikat utfärdare som tillåts när du skapar ett eget certifikat.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: troubleshooting
ms.date: 02/18/2021
ms.author: qixwang
ms.openlocfilehash: 35c1e4f6c700333c72b97289cda1772335037211
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/18/2021
ms.locfileid: "101100584"
---
# <a name="allowed-certificate-authorities-for-azure-front-door-standardpremium-preview"></a>Tillåtna certifikat utfärdare för Azure front dörr standard/Premium (för hands version)

När du aktiverar HTTPS-funktionen med hjälp av ditt eget certifikat för en anpassad Azure front dörr standard/Premium-domän. Du behöver en tillåten certifikat utfärdare (CA) för att skapa TLS/SSL-certifikatet. Annars, om du använder en icke-tillåten CA eller ett självsignerat certifikat, kommer din begäran att avvisas.

Följande ca: er tillåts när du skapar ett eget certifikat:

- AddTrust extern CA-rot
- AlphaSSL rot certifikat utfärdare
- AMN, CA 01
- AMN: CA 02
- Ameroot
- APCA-DM3P
- Atos TrustedRoot 2011
- Rot certifikat utfärdare för autopilot
- Baltimore CyberTrust-rot
- Klass 3 offentlig primär certifikat utfärdare
- COMODO RSA-certifikatutfärdare
- COMODO RSA-domän validering säker server-CA
- D – förtroende rot klass 3 CA 2 2009
- DigiCert Cloud Services CA-1
- DigiCert global rot certifikat utfärdare
- DigiCert global root G2
- DigiCert-certifikat med hög säkerhet – 3
- DigiCert med hög säkerhet och EV-rot certifikat utfärdare
- DigiCert SHA2 utökad validering Server CA
- DigiCert SHA2-server med hög säkerhet
- DigiCert SHA2 Secure Server CA
- DST-rot CA-x3
- D – förtroende rot klass 3 CA 2 2009
- Kryptering överallt i DV TLS CA
- Delegera rot certifikat utfärdare
- Delegera rot certifikat utfärdare – G2
- Entrust.net-certifikatutfärdare (2048)
- Global certifikat utfärdare för opålitlig
- Primär certifikat utfärdare för den betrodda domänen
- Primär certifikat utfärdare för långsiktigt förtroende-G2
- Säkerhets-RSA CA 2018
- GlobalSign
- GlobalSign Extended Validation CA-SHA256-G2
- GlobalSign organisation validering CA-G2
- GlobalSign rot certifikat utfärdare
- Go-Daddy rot certifikat utfärdare-G2
- Go Daddy Secure Certificate Authority-G2
- Vi ska kryptera auktoritets x3
- Mikrosek e-Szigno rot certifikat utfärdare 2009
- QuoVadis root CA2 G3
- RapidSSL RSA CA 2018
- RootCA1 för säkerhets kommunikation
- RootCA2 för säkerhets kommunikation
- RootCA3 för säkerhets kommunikation
- Symantec klass 3 EV SSL CA-G3
- Symantec klass 3 säker server CA-G4
- Symantec Enterprise Mobile root för Microsoft
- Thawte primär rot certifikat utfärdare
- Thawte primär rot certifikat utfärdare-G2
- Thawte primär rot certifikat utfärdare-G3
- Thawte RSA CA 2018
- Thawte tidsstämplar CA
- TrustAsia TLS RSA CA
- VeriSign-klass 3 utökad verifiering av SSL-certifikatutfärdare
- VeriSign klass 3 utökad verifiering SSL SGC CA
- VeriSign-klass 3 offentlig primär certifikat utfärdare – G5
- VeriSign International Server CA-klass 3
- VeriSign-tids stämpling, rot
- VeriSign Universal Root Certification Authority
