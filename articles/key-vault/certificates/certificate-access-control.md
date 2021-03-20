---
title: Om åtkomst kontroll för Azure Key Vault certifikat
description: Översikt över åtkomst kontroll för Azure Key Vault certifikat
services: key-vault
author: sebansal
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: overview
ms.date: 10/12/2020
ms.author: sebansal
ms.openlocfilehash: 1308debb34d724f93526b776f19e0cbf1914d945
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92128665"
---
# <a name="certificate-access-control"></a>Certifikat Access Control

 Åtkomstkontroll för certifikat hanteras av Key Vault och tillhandahålls av Key Vault som innehåller dessa certifikat. Åtkomst kontroll principen för certifikat skiljer sig från principerna för åtkomst kontroll för nycklar och hemligheter i samma Key Vault. Användare kan skapa ett eller flera valv för att lagra certifikat, för att upprätthålla lämplig segmentering och hantering av certifikat.  

 Följande behörigheter kan användas, per huvud konto, i åtkomst kontroll posten hemligheter i ett nyckel valv och speglar noggrant de åtgärder som tillåts för ett hemligt objekt:  

- Behörigheter för certifikat hanterings åtgärder
  - **Hämta**: hämta den aktuella certifikat versionen eller en version av ett certifikat
  - **lista**: visar aktuella certifikat eller versioner av ett certifikat  
  - **Uppdatera**: uppdatera ett certifikat
  - **skapa**: skapa ett Key Vault certifikat
  - **Importera**: Importera certifikat material till ett Key Vault certifikat
  - **ta bort**: ta bort ett certifikat, dess princip och alla dess versioner  
  - **återställa**: Återställ ett borttaget certifikat
  - **säkerhetskopiera**: säkerhetskopiera ett certifikat i ett nyckel valv
  - **återställa**: Återställ ett säkerhetskopierat certifikat till ett nyckel valv
  - **managecontacts**: hantera Key Vault certifikats kontakter  
  - **manageissuers**: hantera Key Vault certifikat utfärdare/utfärdare
  - **getissuers**: Hämta ett certifikats myndigheter/utfärdare
  - **listissuers**: Visa en lista över certifikatets myndigheter/utfärdare  
  - **setissuers**: skapa eller uppdatera ett Key Vault certifikats myndigheter/utfärdare  
  - **deleteissuers**: ta bort en Key Vault certifikatets myndigheter/utfärdare  
 
- Behörigheter för privilegierade åtgärder
  - **Rensa**: Rensa (ta bort permanent) ett borttaget certifikat

Mer information finns i [certifikat åtgärderna i Key Vault REST API referens](/rest/api/keyvault). Information om hur du etablerar behörigheter finns i [valv – uppdatera åtkomst princip](/rest/api/keyvault/vaults/updateaccesspolicy).

## <a name="troubleshoot"></a>Felsöka
Du kan se felet på grund av en åtkomst princip som saknas. Om du till exempel ```Error type : Access denied or user is unauthorized to create certificate``` vill lösa det här felet måste du lägga till certifikat/skapa behörighet.

## <a name="next-steps"></a>Nästa steg

- [Om Key Vault](../general/overview.md)
- [Om nycklar, hemligheter och certifikat](../general/about-keys-secrets-certificates.md)
- [Autentisering, begär Anden och svar](../general/authentication-requests-and-responses.md)
- [Utvecklarguide för Key Vault](../general/developers-guide.md)
