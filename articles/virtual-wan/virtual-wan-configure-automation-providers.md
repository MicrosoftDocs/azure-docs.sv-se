---
title: Automatiserings rikt linjer för Azure Virtual WAN-partners | Microsoft Docs
description: Konfigurera en Automation-miljö för att ansluta och konfigurera en lokal VPN-eller SD-WAN-CPE eller en gren enhet för Azure Virtual WAN.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: cherylmc
ms.openlocfilehash: 29fff3a6a430e3bc1a0b3a13876b55d22f7cb545
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94566477"
---
# <a name="automation-guidelines-for-virtual-wan-partners"></a>Automatiseringsriktlinjer för Virtual WAN-partner

Den här artikeln hjälper dig att förstå hur du konfigurerar Automation-miljön för att ansluta och konfigurera en grenad enhet (en lokal VPN-enhet eller SDWAN CPE) för Azure Virtual WAN. Om du är en provider som tillhandahåller avdelnings enheter som kan hantera VPN-anslutning via IPsec/IKEv2 eller IPsec/IKEv1, är den här artikeln för dig.

En grenen het (en lokal VPN-enhet eller SDWAN CPE) använder vanligt vis en styrenhets-eller enhets instrument panel för att tillhandahållas. Administratörer för SD-WAN-lösningar kan ofta använda en hanterings konsol för att företablera en enhet innan den kopplas till nätverket. Den här VPN-kompatibla enheten får kontroll Plans logik från en kontroll enhet. VPN-enheten eller SD-WAN-styrenheten kan använda Azure API: er för att automatisera anslutningen till Azure Virtual WAN. Den här typen av anslutning kräver att den lokala enheten har tilldelats en externt riktad offentlig IP-adress.

## <a name="before-you-begin-automating"></a><a name ="before"></a>Innan du börjar automatisera

* Kontrol lera att enheten har stöd för IPsec IKEv1/IKEv2. Se [standard principer](#default).
* Visa de [REST-API: er](#additional) som du använder för att automatisera anslutningen till Azure Virtual WAN.
* Testa Portal upplevelsen av Azure Virtual WAN.
* Bestäm sedan vilken del av anslutnings stegen du vill automatisera. Vi rekommenderar minst att du automatiserar:

  * Åtkomstkontroll
  * Ladda upp information om gren enhet till Azure Virtual WAN
  * Hämta Azure-konfiguration och konfigurera anslutning från gren enheten till Azure Virtual WAN

### <a name="additional-information"></a><a name ="additional"></a>Ytterligare information

* [REST API](/rest/api/virtualwan/virtualhubs) att automatisera skapandet av virtuella nav
* [REST API](/rest/api/virtualwan/vpngateways) att automatisera Azure VPN-gateway för virtuellt WAN
* [REST API](/rest/api/virtualwan/vpnconnections) att ansluta en VPNSite till en Azure VPN-hubb
* [Standard-IPsec-principer](#default)

## <a name="customer-experience"></a><a name ="ae"></a>Kund upplevelse

Förstå den förväntade kund upplevelsen tillsammans med Azure Virtual WAN.

  1. Vanligt vis startar en virtuell WAN-användare processen genom att skapa en virtuell WAN-resurs.
  2. Användaren konfigurerar en tjänstens huvud namns-baserade resurs grupp åtkomst för det lokala systemet (din gren kontroll eller VPN-enhetens etablerings program) för att skriva informations Grens information i Azure Virtual WAN.
  3. Användaren kan välja att logga in på ditt användar gränssnitt och konfigurera autentiseringsuppgifterna för tjänstens huvud namn. När den är klar ska din kontrollant kunna ladda upp information om grenen med den automatisering som du kommer att tillhandahålla. Den manuella motsvarigheten till det här på Azure-sidan är "Skapa webbplats".
  4. När informationen om plats (gren enhet) är tillgänglig i Azure kommer användaren att ansluta platsen till en hubb. En virtuell hubb är ett Microsoft-hanterat virtuellt nätverk. Navet innehåller olika tjänstslutpunkter för anslutning från ditt lokala nätverk (vpnsite). Navet är kärnan i ditt nätverk i en region. Det kan bara finnas en hubb per Azure-region och VPN-slutpunkten (vpngateway) i den skapas under den här processen. VPN-gatewayen är en skalbar Gateway vars storlek är lämplig baserat på bandbredd och anslutnings behov. Du kan välja att automatisera virtuell hubb och vpngateway skapande från din avdelnings styrenhets instrument panel.
  5. När den virtuella hubben är kopplad till platsen genereras en konfigurations fil för att användaren ska kunna hämta den manuellt. Det är här som din automatisering kommer i och gör användar upplevelsen sömlös. I stället för att användaren måste hämta och konfigurera gren enheten manuellt, kan du ställa in automatiseringen och tillhandahålla minimala klickningar i användar gränssnittet, vilket minskar vanliga anslutnings problem, t. ex. delade nycklar, IPSec-parameter matchning, konfigurations filens läsbarhet osv.
  6. I slutet av det här steget i lösningen kommer användaren att ha en sömlös plats-till-plats-anslutning mellan gren enheten och den virtuella hubben. Du kan också konfigurera ytterligare anslutningar över andra hubbar. Varje anslutning är en aktiv-aktiv-tunnel. Kunden kan välja att använda en annan Internet leverantör för var och en av länkarna för tunneln.
  7. Överväg att tillhandahålla fel söknings-och övervaknings funktioner i hanterings gränssnittet för CPE. Vanliga scenarier är "kunder som inte kan komma åt Azure-resurser på grund av ett CPE-problem", "Visa IPsec-parametrar på CPE-sidan" osv.

## <a name="automation-details"></a><a name ="understand"></a>Information om Automation

###  <a name="access-control"></a><a name="access"></a>Åtkomstkontroll

Kunderna måste kunna ställa in lämplig åtkomst kontroll för virtuellt WAN-nätverk i enhetens användar gränssnitt. Detta rekommenderas med hjälp av ett huvud namn för Azure-tjänsten. Tjänstens huvud namn (SPN) tillhandahåller den enhets styrenhet som är lämplig autentisering för att överföra information om grenen. Mer information finns i [skapa tjänstens huvud namn](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal). Även om den här funktionen ligger utanför det virtuella WAN-erbjudandet i Azure finns en lista under de vanligaste stegen för att ställa in åtkomst i Azure efter vilken relevant information finns i listan på instrument panelen för enhets hantering

* Skapa ett Azure Active Directory-program för din lokala enhets styrenhet.
* Hämta program-ID och autentiseringsnyckel
* Hämta klientorganisations-ID
* Tilldela program till rollen "deltagare"

###  <a name="upload-branch-device-information"></a><a name="branch"></a>Ladda upp information om gren enhet

Du bör utforma användar upplevelsen för att ladda upp information om grenen (lokal plats) till Azure. Du kan använda [REST-API: er](/rest/api/virtualwan/vpnsites) för VPNSite för att skapa plats informationen i virtuella WAN-nätverk. Du kan tillhandahålla alla gren-SDWAN/VPN-enheter eller välja enhets anpassningar efter behov.

### <a name="device-configuration-download-and-connectivity"></a><a name="device"></a>Hämtning och anslutning av enhets konfiguration

Det här steget innebär att ladda ned Azure-konfiguration och konfigurera anslutning från gren enheten till Azure Virtual WAN. I det här steget laddar en kund som inte använder en provider manuellt Azure-konfigurationen och tillämpar den på den lokala SDWAN/VPN-enheten. Som en provider bör du automatisera det här steget. Se Hämta [REST-API: er](/rest/api/virtualwan/vpnsitesconfiguration/download) för ytterligare information. Enhets styrenheten kan anropa ' GetVpnConfiguration ' REST API för att ladda ned Azure-konfigurationen.

**Konfigurations anmärkningar**

  * Om Azure-virtuella nätverk är anslutna till den virtuella hubben visas de som ConnectedSubnets.
  * VPN-anslutningar använder väg-baserad konfiguration och stöder både IKEv1-och IKEv2-protokoll.

## <a name="device-configuration-file"></a><a name="devicefile"></a>Enhets konfigurations fil

Konfigurationsfilen för enheten innehåller de inställningarna du ska använda när du konfigurerar den lokala VPN-enheten. När du visar den här filen ser du följande information:

* **vpnSiteConfiguration** I det här avsnittet anges enhetsinformation konfigurerad som en plats som ansluter till det virtuella WAN-nätverket. Det omfattar namn och offentlig IP-adress för grenenheten.
* **vpnSiteConnections** – Det här avsnittet innehåller information om följande:

    * **Adress utrymme** för virtuella hubbar VNet.<br>Exempel:
 
        ```
        "AddressSpace":"10.1.0.0/24"
        ```
    * **Adress utrymmet** för de virtuella nätverk som är anslutna till hubben.<br>Exempel:

         ```
        "ConnectedSubnets":["10.2.0.0/16","10.3.0.0/16"]
         ```
    * **IP-adresser** till vpngateway för den virtuella hubben. Eftersom varje anslutning för vpngateway består av två tunnlar i aktiv-aktiv konfiguration ser du båda IP-adresserna listade i den här filen. I det här exemplet ser du "Instance0" och "Instance1" för varje plats.<br>Exempel:

        ``` 
        "Instance0":"104.45.18.186"
        "Instance1":"104.45.13.195"
        ```
    * **Vpngateway anslutnings konfigurations information** som BGP, i förväg delad nyckel osv. PSK är den i förväg delade nyckeln som skapas automatiskt åt dig. Du kan alltid redigera anslutningen på översiktssidan för en anpassad PSK.
  
**Konfigurationsfil för exempelenhet**

  ```
  { 
      "configurationVersion":{ 
         "LastUpdatedTime":"2018-07-03T18:29:49.8405161Z",
         "Version":"r403583d-9c82-4cb8-8570-1cbbcd9983b5"
      },
      "vpnSiteConfiguration":{ 
         "Name":"testsite1",
         "IPAddress":"73.239.3.208"
      },
      "vpnSiteConnections":[ 
         { 
            "hubConfiguration":{ 
               "AddressSpace":"10.1.0.0/24",
               "Region":"West Europe",
               "ConnectedSubnets":[ 
                  "10.2.0.0/16",
                  "10.3.0.0/16"
               ]
            },
            "gatewayConfiguration":{ 
               "IpAddresses":{ 
                  "Instance0":"104.45.18.186",
                  "Instance1":"104.45.13.195"
               }
            },
            "connectionConfiguration":{ 
               "IsBgpEnabled":false,
               "PSK":"bkOWe5dPPqkx0DfFE3tyuP7y3oYqAEbI",
               "IPsecParameters":{ 
                  "SADataSizeInKilobytes":102400000,
                  "SALifeTimeInSeconds":3600
               }
            }
         }
      ]
   },
   { 
      "configurationVersion":{ 
         "LastUpdatedTime":"2018-07-03T18:29:49.8405161Z",
         "Version":"1f33f891-e1ab-42b8-8d8c-c024d337bcac"
      },
      "vpnSiteConfiguration":{ 
         "Name":" testsite2",
         "IPAddress":"66.193.205.122"
      },
      "vpnSiteConnections":[ 
         { 
            "hubConfiguration":{ 
               "AddressSpace":"10.1.0.0/24",
               "Region":"West Europe"
            },
            "gatewayConfiguration":{ 
               "IpAddresses":{ 
                  "Instance0":"104.45.18.187",
                  "Instance1":"104.45.13.195"
               }
            },
            "connectionConfiguration":{ 
               "IsBgpEnabled":false,
               "PSK":"XzODPyAYQqFs4ai9WzrJour0qLzeg7Qg",
               "IPsecParameters":{ 
                  "SADataSizeInKilobytes":102400000,
                  "SALifeTimeInSeconds":3600
               }
            }
         }
      ]
   },
   { 
      "configurationVersion":{ 
         "LastUpdatedTime":"2018-07-03T18:29:49.8405161Z",
         "Version":"cd1e4a23-96bd-43a9-93b5-b51c2a945c7"
      },
      "vpnSiteConfiguration":{ 
         "Name":" testsite3",
         "IPAddress":"182.71.123.228"
      },
      "vpnSiteConnections":[ 
         { 
            "hubConfiguration":{ 
               "AddressSpace":"10.1.0.0/24",
               "Region":"West Europe"
            },
            "gatewayConfiguration":{ 
               "IpAddresses":{ 
                  "Instance0":"104.45.18.187",
                  "Instance1":"104.45.13.195"
               }
            },
            "connectionConfiguration":{ 
               "IsBgpEnabled":false,
               "PSK":"YLkSdSYd4wjjEThR3aIxaXaqNdxUwSo9",
               "IPsecParameters":{ 
                  "SADataSizeInKilobytes":102400000,
                  "SALifeTimeInSeconds":3600
               }
            }
         }
      ]
   }
  ```

## <a name="connectivity-details"></a><a name="default"></a>Anslutnings information

Din lokala SDWAN/VPN-enhet eller SD-WAN-konfiguration måste matcha eller innehålla följande algoritmer och parametrar, som du anger i Azure IPsec/IKE-principen.

* IKE-krypteringsalgoritm
* IKE-integritetsalgoritm
* DH-grupp
* IPsec-krypteringsalgoritm
* IPsec-integritetsalgoritm
* PFS-grupp

### <a name="default-policies-for-ipsec-connectivity"></a><a name="default"></a>Standardprinciper för IPsec-anslutning

[!INCLUDE [IPsec Default](../../includes/virtual-wan-ipsec-include.md)]

### <a name="custom-policies-for-ipsec-connectivity"></a><a name="custom"></a>Anpassade principer för IPsec-anslutning

[!INCLUDE [IPsec Custom](../../includes/virtual-wan-ipsec-custom-include.md)]

## <a name="next-steps"></a>Nästa steg

Mer information om virtuellt WAN finns i [om Azure Virtual WAN](virtual-wan-about.md) och [vanliga frågor och svar om Azure Virtual WAN](virtual-wan-faq.md).

Om du vill ha ytterligare information kan du skicka ett e-postmeddelande till <azurevirtualwan@microsoft.com> . Ange ditt företagsnamn inom [ ] i ämnesraden.