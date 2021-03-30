---
title: Felsök – Azure Event Grid IoT Edge | Microsoft Docs
description: Fel sökning i Event Grid på IoT Edge.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: 0196522618d4b61f615f7cc6faeacbe9a8c7c5b4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "86171354"
---
# <a name="common-issues"></a>Vanliga problem

Om du får problem med att använda Azure Event Grid på IoT Edge i din miljö kan du använda den här artikeln som en guide för fel sökning och lösning.

## <a name="view-event-grid-module-logs"></a>Visa Event Grid-modul loggar

Om du vill felsöka kan du behöva komma åt Event Grid-modulens loggar. Det gör du genom att köra följande kommando på den virtuella datorn där modulen har distribuerats:

I Windows,

```sh
docker -H npipe:////./pipe/iotedge_moby_engine container logs eventgridmodule
```

I Linux,

```sh
sudo docker logs eventgridmodule
```

## <a name="unable-to-make-https-requests"></a>Det går inte att göra HTTPS-begäranden

* Kontrol lera först att Event Grid modul har **inkommande: serverAuth: tlsPolicy** inställd på **strict** eller **Enabled**.

* Om dess modul-till-modul-kommunikation sker kontrollerar du att anropet görs på port **4438** och att namnet på modulen matchar vad som distribueras. 

  Till exempel, om Event Grid modul har distribuerats med namnet **eventgridmodule** ska din URL vara **https://eventgridmodule:4438** . Kontrol lera att Skift läge och port nummer är korrekta.
    
* Om det är från en modul som inte är IoT kontrollerar du att Event Grid porten är mappad till värddatorn under distributionen, till exempel

    ```json
    "HostConfig": {
                "PortBindings": {
                  "4438/tcp": [
                    {
                        "HostPort": "4438"
                    }
                  ]
                }
     }
    ```

## <a name="unable-to-make-http-requests"></a>Det går inte att göra HTTP-begäranden

* Se först till Event Grid modul har **inkommande: serverAuth: tlsPolicy** inställt på **aktive rad** eller **inaktive** rad.

* Om dess modul-till-modul-kommunikation sker kontrollerar du att anropet görs på port **5888** och att namnet på modulen matchar vad som distribueras. 

  Till exempel, om Event Grid modul har distribuerats med namnet **eventgridmodule** ska din URL vara **http://eventgridmodule:5888** . Kontrol lera att Skift läge och port nummer är korrekta.
    
* Om det är från en modul som inte är IoT kontrollerar du att Event Grid porten är mappad till värddatorn under distributionen, till exempel

    ```json
    "HostConfig": {
                "PortBindings": {
                  "5888/tcp": [
                    {
                        "HostPort": "5888"
                    }
                  ]
                }
    }
    ```

## <a name="certificate-chain-was-issued-by-an-authority-thats-not-trusted"></a>Certifikat kedjan utfärdades av en utfärdare som inte är betrodd

Som standard konfigureras Event Grid-modulen att autentisera klienter med certifikat som utfärdats av IoT Edge Security daemon. Kontrol lera att klienten presenterar ett certifikat som är kopplat till den här kedjan.

**IoTSecurity** -klassen i [https://github.com/Azure/event-grid-iot-edge](https://github.com/Azure/event-grid-iot-edge) visar hur du hämtar certifikat från IoT Edge Security daemon och använder det för att konfigurera utgående samtal.

Om den inte är en produktions miljö har du möjlighet att inaktivera klientautentisering. Information om hur du gör detta finns i [säkerhet och autentisering](security-authentication.md) .

## <a name="debug-events-not-received-by-subscriber"></a>Fel söknings händelser som inte tagits emot av prenumeranten

Vanliga orsaker till detta är:

* Händelsen har aldrig publicerats. En HTTP-StatusCode på 200 (OK) ska tas emot vid bokföring av en händelse till Event Grid modul.

* Kontrol lera händelse prenumerationen för att verifiera:
    * Slut punktens URL är giltig
    * Eventuella filter i prenumerationen orsakar inte att händelsen "släpps".

* Verifiera att Subscriber-modulen körs

* Logga in på den virtuella datorn där Event Grid modul distribueras och visa dess loggar.

* Aktivera loggning per leverans genom att ställa in **Service Broker: logDeliverySuccess = True** och omdistribuera Event Grid-modulen och försöka utföra begäran igen. Att aktivera loggning per leverans kan påverka data flödet och svars tiden, så när fel sökningen är klar är vår rekommendation att aktivera **Service Broker igen: logDeliverySuccess = false**  och omdistribution av event Grid modul.

* Aktivera mått genom att ange **mått: reportertype = konsol** och omdistribuera Event Grid modul. Alla åtgärder efter detta leder till att måtten loggas i konsolen för Event Grid modul, som kan användas för att felsöka ytterligare. Vi rekommenderar att du aktiverar mått endast för fel sökning och när du har slutfört den genom att ange **mått: reportertype = ingen** och omdistribuera Event Grid modul.

## <a name="next-steps"></a>Nästa steg

Rapportera eventuella problem, förslag med att använda Event Grid på IoT Edge på [https://github.com/Azure/event-grid-iot-edge/issues](https://github.com/Azure/event-grid-iot-edge/issues) .