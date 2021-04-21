---
title: Om Azure Key Vault åtkomstkontroll för certifikat
description: Översikt över åtkomstkontroll Azure Key Vault certifikat
services: key-vault
author: sebansal
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: overview
ms.date: 10/12/2020
ms.author: sebansal
ms.openlocfilehash: 54874f30384d7f4827b13a597a469bfc67bc8fd2
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107752488"
---
# <a name="certificate-access-control"></a>Certifikat Access Control

 Åtkomstkontroll för certifikat hanteras av Key Vault och tillhandahålls av Key Vault som innehåller dessa certifikat. Principen för åtkomstkontroll för certifikat skiljer sig från åtkomstkontrollprinciperna för nycklar och hemligheter i samma Key Vault. Användare kan skapa ett eller flera valv för att innehålla certifikat för att upprätthålla lämplig segmentering och hantering av certifikat i scenariot.  

 Följande behörigheter kan användas, per huvudnamn, i åtkomstkontrollposten för hemligheter i ett nyckelvalv och speglar noggrant de åtgärder som tillåts för ett hemligt objekt:  

- Behörigheter för certifikathanteringsåtgärder
  - **get**: Hämta den aktuella certifikatversionen eller någon version av ett certifikat
  - **lista:** Lista över aktuella certifikat eller versioner av ett certifikat  
  - **update**: Uppdatera ett certifikat
  - **skapa:** Skapa ett Key Vault certifikat
  - **import**: Importera certifikatmaterial till ett Key Vault certifikat
  - **delete**: Ta bort ett certifikat, dess princip och alla dess versioner  
  - **recover**: Återställa ett borttagna certifikat
  - **säkerhetskopiering:** Säkerhetskopiera ett certifikat i ett nyckelvalv
  - restore :Restore a backed-up certificate to a key vault **(Återställa** ett säkerhetskopierat certifikat till ett nyckelvalv)
  - **managecontacts:** Hantera Key Vault certifikatkontakter  
  - **manageissuers:** Hantera Key Vault certifikatutfärdare/utfärdare
  - **getissuers:** Hämta ett certifikats utfärdare/utfärdare
  - **listissuers:** Lista ett certifikats utfärdare/utfärdare  
  - **setissuers:** Skapa eller uppdatera Key Vault för certifikatutfärdare/utfärdare  
  - **deleteissuers:** Ta Key Vault ett certifikats utfärdare/utfärdare  
 
- Behörigheter för privilegierade åtgärder
  - **rensa:** Rensa (ta bort permanent) ett borttagna certifikat

Mer information finns i [Certifikatåtgärder i Key Vault REST API referens](/rest/api/keyvault). Information om hur du etablerar behörigheter finns [i Valv – Uppdatera åtkomstprincip.](/rest/api/keyvault/vaults/updateaccesspolicy)

## <a name="troubleshoot"></a>Felsöka
Du kan få ett felmeddelande på grund av att åtkomstprincipen saknas. Om du ```Error type : Access denied or user is unauthorized to create certificate``` till exempel vill lösa det här felet måste du lägga till certifikat/skapa-behörighet.

## <a name="next-steps"></a>Nästa steg

- [Om Key Vault](../general/overview.md)
- [Om nycklar, hemligheter och certifikat](../general/about-keys-secrets-certificates.md)
- [Autentisering, begäranden och svar](../general/authentication-requests-and-responses.md)
- [Utvecklarguide för Key Vault](../general/developers-guide.md)
