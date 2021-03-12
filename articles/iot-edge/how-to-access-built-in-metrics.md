---
title: Komma åt inbyggda mått – Azure IoT Edge
description: Fjärråtkomst till inbyggda mått från IoT Edge körnings komponenter
author: v-tcassi
manager: philmea
ms.author: v-tcassi
ms.date: 10/05/2020
ms.topic: conceptual
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 1a78db821c0fab01ad5d6752216a8f7682fb2c46
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/12/2021
ms.locfileid: "103200501"
---
# <a name="access-built-in-metrics"></a>Komma åt inbyggda mått

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

IoT Edge körnings komponenter, IoT Edge Hub och IoT Edge agent, skapar inbyggda mått i [formatet Prometheus](https://prometheus.io/docs/instrumenting/exposition_formats/). Få åtkomst till dessa mått via fjärr anslutning för att övervaka och förstå hälsan hos en IoT Edge enhet.

Från och med versions 1.0.10 visas måtten automatiskt som standard på **port 9600** i **EdgeHub** -och **edgeAgent** -modulerna ( `http://edgeHub:9600/metrics` och `http://edgeAgent:9600/metrics` ). De är inte port mappade till värden som standard.

Få åtkomst till mått från värden genom att exponera och mappa måtten portarna från modulen `createOptions` . Exemplet nedan mappar standard mått porten till port 9601 på värden:

```
{
  "ExposedPorts": {
    "9600/tcp": {}
  },
  "HostConfig": {
    "PortBindings": {
      "9600/tcp": [
        {
          "HostPort": "9601"
        }
      ]
    }
  }
}
```

Välj olika och unika värd port nummer om du mappar både edgeHub och edgeAgents Mät slut punkter.

> [!NOTE]
> Miljövariabeln `httpSettings__enabled` ska inte anges för att `false` inbyggda mått ska vara tillgängliga för insamling.
>
> Miljövariabler som kan användas för att inaktivera mått visas i [Azure/iotedge lagrings platsen-dokumentet](https://github.com/Azure/iotedge/blob/master/doc/EnvironmentVariables.md).

## <a name="available-metrics"></a>Tillgängliga mått

Mått innehåller taggar som hjälper dig att identifiera vilken typ av mått som samlas in. Alla mått innehåller följande Taggar:

| Tagg | Beskrivning |
|-|-|
| iothub | Den hubb som enheten pratar med |
| edge_device | Den aktuella enhetens ID |
| instance_number | Ett GUID som representerar den aktuella körnings miljön. Vid omstart återställs alla mått. Detta GUID gör det lättare att stämma av omstarter. |

I formatet Prometheus-återställnings format finns fyra typer av kärn mått: räknare, mätare, histogram och sammanfattning. Mer information om de olika mått typerna finns i dokumentationen för [Prometheus Metric types](https://prometheus.io/docs/concepts/metric_types/).

Quantiles som tillhandahålls för det inbyggda histogrammet och sammanfattnings måtten är 0,1, 0,5, 0,9 och 0,99.

**EdgeHub** -modulen genererar följande mått:

| Name | Dimensioner | Beskrivning |
|-|-|-|
| `edgehub_gettwin_total` | `source` (åtgärds källa)<br> `id` (modul-ID) | Typ: räknare<br> Totalt antal GetTwin-anrop |
| `edgehub_messages_received_total` | `route_output` (utdata som har skickat meddelande)<br> `id` | Typ: räknare<br> Totalt antal meddelanden som tagits emot från klienter |
| `edgehub_messages_sent_total` | `from` (meddelande källa)<br> `to` (meddelande mål)<br>`from_route_output`<br> `to_route_input` (inmatade meddelande mål)<br> `priority` (meddelande prioritet till mål) | Typ: räknare<br> Totalt antal meddelanden som skickats till klienter eller uppladdning<br> `to_route_input` är tom när `to` är $upstream |
| `edgehub_reported_properties_total` | `target`(uppdatera mål)<br> `id` | Typ: räknare<br> Totalt antal rapporterade begärda egenskaps uppdateringar |
| `edgehub_message_size_bytes` | `id`<br> | Typ: Sammanfattning<br> Meddelande storlek från klienter<br> Värden kan rapporteras som `NaN` om inga nya mätningar rapporteras under en viss tids period (för närvarande 10 minuter). för `summary` typ kommer motsvarande `_count` och `_sum` räknare att genereras. |
| `edgehub_gettwin_duration_seconds` | `source` <br> `id` | Typ: Sammanfattning<br> Åtgången tid för att hämta dubbla åtgärder |
| `edgehub_message_send_duration_seconds` | `from`<br> `to`<br> `from_route_output`<br> `to_route_input` | Typ: Sammanfattning<br> Åtgången tid för att skicka ett meddelande |
| `edgehub_message_process_duration_seconds` | `from` <br> `to` <br> `priority` | Typ: Sammanfattning<br> Åtgången tid för att bearbeta ett meddelande från kön |
| `edgehub_reported_properties_update_duration_seconds` | `target`<br> `id` | Typ: Sammanfattning<br> Åtgången tid för att uppdatera rapporterade egenskaper |
| `edgehub_direct_method_duration_seconds` | `from` anroparen<br> `to` mottagare | Typ: Sammanfattning<br> Åtgången tid för att lösa ett direkt meddelande |
| `edgehub_direct_methods_total` | `from`<br> `to` | Typ: räknare<br> Totalt antal skickade direkta meddelanden |
| `edgehub_queue_length` | `endpoint` (meddelande källa)<br> `priority` (köprioritet) | Typ: mätare<br> Aktuell längd på edgeHub för en viss prioritet |
| `edgehub_messages_dropped_total` | `reason` (no_route ttl_expiry)<br> `from` <br> `from_route_output` | Typ: räknare<br> Totalt antal meddelanden som tagits bort på grund av en orsak |
| `edgehub_messages_unack_total` | `reason` (storage_failure)<br> `from`<br> `from_route_output` | Typ: räknare<br> Totalt antal meddelanden som inte har bekräftats på grund av ett lagrings problem |
| `edgehub_offline_count_total` | `id` | Typ: räknare<br> Totalt antal gånger som edgeHub gick offline |
| `edgehub_offline_duration_seconds`| `id` | Typ: Sammanfattning<br> Tids gräns hubben var offline |
| `edgehub_operation_retry_total` | `id`<br> `operation` (åtgärds namn) | Typ: räknare<br> Totalt antal gånger som edgeHub åtgärder har gjorts om |
| `edgehub_client_connect_failed_total` | `id` <br> `reason` (inte autentiserad)<br> | Typ: räknare<br> Totalt antal gånger som klienterna inte kunde ansluta till edgeHub |

**EdgeAgent** -modulen genererar följande mått:

| Name | Dimensioner | Beskrivning |
|-|-|-|
| `edgeAgent_total_time_running_correctly_seconds` | `module_name` | Typ: mätare<br> Hur lång tid modulen angavs i distributionen och var i körnings tillstånd |
| `edgeAgent_total_time_expected_running_seconds` | `module_name` | Typ: mätare<br> Hur lång tid modulen angavs i distributionen |
| `edgeAgent_module_start_total` | `module_name`, `module_version` | Typ: räknare<br> Antal gånger edgeAgent frågade Docker för att starta modulen |
| `edgeAgent_module_stop_total` | `module_name`, `module_version` | Typ: räknare<br> Antal gånger edgeAgent frågade Docker för att stoppa modulen |
| `edgeAgent_command_latency_seconds` | `command` | Typ: mätare<br> Hur lång tid det tog att köra det aktuella kommandot. Möjliga kommandon är: skapa, uppdatera, ta bort, starta, stoppa, starta om |
| `edgeAgent_iothub_syncs_total` | | Typ: räknare<br> Antal gånger edgeAgent försökte synkronisera sin dubbla med iotHub, både lyckad och misslyckad. Det här antalet inkluderar båda agenten som begär en dubbel-och Hub-avisering om en dubbel uppdatering |
| `edgeAgent_unsuccessful_iothub_syncs_total` | | Typ: räknare<br> Antal gånger edgeAgent kunde inte synkronisera sin dubbla med iotHub. |
| `edgeAgent_deployment_time_seconds` | | Typ: räknare<br> Hur lång tid det tog att slutföra en ny distribution efter att ha fått en ändring. |
| `edgeagent_direct_method_invocations_count` | `method_name` | Typ: räknare<br> Antal gånger som en inbyggd edgeAgent Direct-metod anropas, t. ex. ping eller omstart. |
| `edgeAgent_host_uptime_seconds` | | Typ: mätare<br> Hur länge värden har varit på |
| `edgeAgent_iotedged_uptime_seconds` | | Typ: mätare<br> Hur länge iotedged har körts |
| `edgeAgent_available_disk_space_bytes` | `disk_name`, `disk_filesystem`, `disk_filetype` | Typ: mätare<br> Utrymmet kvar på disken |
| `edgeAgent_total_disk_space_bytes` | `disk_name`, `disk_filesystem`, `disk_filetype`| Typ: mätare<br> Diskens storlek |
| `edgeAgent_used_memory_bytes` | `module_name` | Typ: mätare<br> Mängden RAM-minne som används av alla processer |
| `edgeAgent_total_memory_bytes` | `module_name` | Typ: mätare<br> Tillgängligt RAM-minne |
| `edgeAgent_used_cpu_percent` | `module_name` | Typ: histogram<br> Procent andel CPU som används av alla processer |
| `edgeAgent_created_pids_total` | `module_name` | Typ: mätare<br> Antalet processer eller trådar som containern har skapat |
| `edgeAgent_total_network_in_bytes` | `module_name` | Typ: mätare<br> Antalet byte som tagits emot från nätverket |
| `edgeAgent_total_network_out_bytes` | `module_name` | Typ: mätare<br> Antalet byte som skickats till nätverket |
| `edgeAgent_total_disk_read_bytes` | `module_name` | Typ: mätare<br> Antalet byte som lästs från disken |
| `edgeAgent_total_disk_write_bytes` | `module_name` | Typ: mätare<br> Antalet byte som skrivs till disk |
| `edgeAgent_metadata` | `edge_agent_version`, `experimental_features`, `host_information` | Typ: mätare<br> Allmänna metadata om enheten. Värdet är Always 0 och informationen kodas i taggarna. Observera `experimental_features` och `host_information` är JSON-objekt. `host_information` ser ut så här ```{"OperatingSystemType": "linux", "Architecture": "x86_64", "Version": "1.0.10~dev20200803.4", "Provisioning": {"Type": "dps.tpm", "DynamicReprovisioning": false, "AlwaysReprovisionOnStartup": true}, "ServerVersion": "19.03.6", "KernelVersion": "5.0.0-25-generic", "OperatingSystem": "Ubuntu 18.04.4 LTS", "NumCpus": 6, "Virtualized": "yes"}``` . Obs! `ServerVersion` Docker-versionen och `Version` är versionen av IoT Edge Security daemon. |

## <a name="next-steps"></a>Nästa steg

* [Förstå Azure IoT Edge Runtime och dess arkitektur](iot-edge-runtime.md)
* [Egenskaperna för IoT Edge agenten och IoT Edge Hub-modulen är dubbla](module-edgeagent-edgehub.md)
