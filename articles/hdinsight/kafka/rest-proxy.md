---
title: Apache Kafka REST-proxy – Azure HDInsight
description: Lär dig hur du gör Apache Kafka åtgärder med en Kafka REST-proxy på Azure HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: has-adal-ref, devx-track-python
ms.date: 04/03/2020
ms.openlocfilehash: a9a007d33226c508e193368b08b189001bf53401
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98944070"
---
# <a name="interact-with-apache-kafka-clusters-in-azure-hdinsight-using-a-rest-proxy"></a>Interagera med Apache Kafka kluster i Azure HDInsight med hjälp av en REST-proxy

Med Kafka REST proxy kan du interagera med ditt Kafka-kluster via en REST API över HTTPS. Den här åtgärden innebär att dina Kafka-klienter kan vara utanför det virtuella nätverket. Klienter kan göra enkla, säkra HTTPS-anrop till Kafka-klustret, i stället för att förlita dig på Kafka-bibliotek. I den här artikeln visas hur du skapar ett Kafka-kluster med REST proxy. Innehåller också en exempel kod som visar hur du gör anrop till REST-proxy.

## <a name="rest-api-reference"></a>Referens för REST-API

För åtgärder som stöds av Kafka-REST API, se [HDInsight KAFKA rest proxy API-referens](/rest/api/hdinsight-kafka-rest-proxy).

## <a name="background"></a>Bakgrund

![Kafka REST-proxy design](./media/rest-proxy/rest-proxy-architecture.png)

En fullständig specifikation av de åtgärder som stöds av API: et finns [Apache KAFKA rest proxy API](/rest/api/hdinsight-kafka-rest-proxy).

### <a name="rest-proxy-endpoint"></a>REST proxy-slutpunkt

När du skapar ett HDInsight Kafka-kluster med REST proxy skapas en ny offentlig slut punkt för klustret, som du kan hitta i HDInsight-klustrets **Egenskaper** på Azure Portal.

### <a name="security"></a>Säkerhet

Åtkomst till Kafka REST proxy hanteras med Azure Active Directory säkerhets grupper. När du skapar Kafka-klustret anger du Azure AD-säkerhetsgruppen med REST Endpoint Access. Kafka-klienter som behöver åtkomst till REST-proxyn ska registreras till den här gruppen av grupp ägaren. Grupp ägaren kan registreras via portalen eller via PowerShell.

Klient program bör hämta en OAuth-token för REST-slut punkts begär Anden. Token används för att verifiera medlemskap i säkerhets grupper. Hitta ett [exempel på klient program](#client-application-sample) nedan som visar hur du skaffar en OAuth-token. Klient programmet skickar OAuth-token i HTTPS-begäran till REST-proxyn.

> [!NOTE]
> Mer information om AAD-säkerhetsgrupper finns i [Hantera app-och resurs åtkomst med hjälp av Azure Active Directory grupper](../../active-directory/fundamentals/active-directory-manage-groups.md). Mer information om hur OAuth-token fungerar finns i [ge åtkomst till Azure Active Directory webb program med hjälp av OAuth 2,0 kod tilldelnings flödet](../../active-directory/azuread-dev/v1-protocols-oauth-code.md).

## <a name="kafka-rest-proxy-with-network-security-groups"></a>Kafka REST-proxy med nätverks säkerhets grupper
Om du tar med ditt eget VNet och kontrollerar nätverks trafiken med nätverks säkerhets grupper tillåter du **inkommande** trafik på port **9400** förutom port 443. Detta säkerställer att Kafka REST-proxyservern kan kontaktas.

## <a name="prerequisites"></a>Förutsättningar

1. Registrera ett program med Azure AD. Klient programmen som du skriver för att interagera med Kafka REST-proxyn använder programmets ID och hemlighet för att autentisera till Azure.

1. Skapa en Azure AD-säkerhetsgrupp. Lägg till det program som du har registrerat med Azure AD i säkerhets gruppen som **medlem** i gruppen. Den här säkerhets gruppen används för att kontrol lera vilka program som får interagera med REST-proxyn. Mer information om hur du skapar Azure AD-grupper finns i [skapa en grundläggande grupp och lägga till medlemmar med hjälp av Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

    Verifiera att gruppen är av typen **säkerhet**.
    ![Säkerhets grupp](./media/rest-proxy/rest-proxy-group.png)

    Verifiera att programmet är medlem i gruppen.
    ![Kontrol lera medlemskap](./media/rest-proxy/rest-proxy-membergroup.png)

## <a name="create-a-kafka-cluster-with-rest-proxy-enabled"></a>Skapa ett Kafka-kluster med REST proxy aktiverat

I stegen nedan används Azure Portal. Ett exempel på hur du använder Azure CLI finns i [skapa Apache KAFKA rest-proxy-kluster med Azure CLI](tutorial-cli-rest-proxy.md).

1. Under arbets flödet för skapande av Kafka-kluster går du till fliken **säkerhet + nätverk** och markerar alternativet **Aktivera Kafka rest proxy** .

     ![Skärm bild som visar sidan Skapa en insikts kluster med säkerhet och nätverk valt.](./media/rest-proxy/azure-portal-cluster-security-networking-kafka-rest.png)

1. Klicka på **Välj säkerhets grupp**. I listan över säkerhets grupper väljer du den säkerhets grupp som du vill ha åtkomst till REST-proxyn. Du kan använda sökrutan för att hitta rätt säkerhets grupp. Klicka på knappen **Välj** längst ned.

     ![Skärm bild som visar sidan Skapa D D-insikts kluster med alternativet att välja en säkerhets grupp.](./media/rest-proxy/azure-portal-cluster-security-networking-kafka-rest2.png)

1. Slutför de återstående stegen för att skapa klustret enligt beskrivningen i [skapa Apache Kafka kluster i Azure HDInsight med Azure Portal](./apache-kafka-get-started.md).

1. När klustret har skapats går du till kluster egenskaperna för att registrera Kafka REST proxy-URL: en.

     ![Visa URL för REST-proxy](./media/rest-proxy/apache-kafka-rest-proxy-view-proxy-url.png)

## <a name="client-application-sample"></a>Exempel på klient program

Du kan använda python-koden nedan för att interagera med REST-proxyn på ditt Kafka-kluster. Följ dessa steg om du vill använda kod exemplet:

1. Spara exempel koden på en dator med python installerat.
1. Installera nödvändiga python-beroenden genom att köra `pip3 install msal` .
1. Ändra kod avsnittet **Konfigurera dessa egenskaper** och uppdatera följande egenskaper för din miljö:

    |Egenskap |Beskrivning |
    |---|---|
    |Klient-ID:t|Azure-klienten där din prenumeration är.|
    |Klient-ID|ID för programmet som du registrerade i säkerhets gruppen.|
    |Client Secret (Klienthemlighet)|Hemligheten för det program som du registrerade i säkerhets gruppen.|
    |Kafkarest_endpoint|Hämta det här värdet från fliken **Egenskaper** i kluster översikten enligt beskrivningen i [avsnittet distribution](#create-a-kafka-cluster-with-rest-proxy-enabled). Det ska ha följande format – `https://<clustername>-kafkarest.azurehdinsight.net`|

1. Kör python-filen från kommando raden genom att köra `sudo python3 <filename.py>`

Den här koden utför följande åtgärd:

1. Hämtar en OAuth-token från Azure AD.
1. Visar hur du gör en begäran till Kafka REST proxy.

Mer information om hur du hämtar OAuth-tokens i python finns i [python AuthenticationContext-klass](/python/api/adal/adal.authentication_context.authenticationcontext). Du kan se en fördröjning när den `topics` inte skapas eller tas bort via KAFKA rest-proxyn visas där. Den här fördröjningen beror på cache-uppdateringen. **Värde** fältet i Producer-API har förbättrats. Nu accepterar den JSON-objekt och eventuell serialiserad form.

```python
#Required python packages
#pip3 install msal

import json
import msal
import random
import requests
import string
import sys
import time

def get_random_string():
    letters = string.ascii_letters
    random_string = ''.join(random.choice(letters) for i in range(7))

    return random_string


#--------------------------Configure these properties-------------------------------#
# Tenant ID for your Azure Subscription
tenant_id = 'ABCDEFGH-1234-1234-1234-ABCDEFGHIJKL'
# Your Client Application Id
client_id = 'XYZABCDE-1234-1234-1234-ABCDEFGHIJKL'
# Your Client Credentials
client_secret = 'password'
# kafka rest proxy -endpoint
kafkarest_endpoint = "https://<clustername>-kafkarest.azurehdinsight.net"
#--------------------------Configure these properties-------------------------------#

# Get access token
# Scope
scope = 'https://hib.azurehdinsight.net/.default'
#Authority
authority = 'https://login.microsoftonline.com/' + tenant_id

app = msal.ConfidentialClientApplication(
    client_id , client_secret, authority,
    #cache - For details on how look at this example: https://github.com/Azure-Samples/ms-identity-python-webapp/blob/master/app.py
)

# The pattern to acquire a token looks like this.
result = None
result = app.acquire_token_for_client(scopes=[scope])
accessToken = result['access_token']
verify_https = True
request_timeout = 10

# Print access token
print("Access token: " + accessToken)

# API format
api_version = 'v1'
api_format = kafkarest_endpoint + '/{api_version}/{rest_api}'
get_topic_api = 'metadata/topics'
topic_api_format = 'topics/{topic_name}'
producer_api_format = 'producer/topics/{topic_name}'
consumer_api_format = 'consumer/topics/{topic_name}/partitions/{partition_id}/offsets/{offset}?count={count}'  # by default count = 1

# Request header
headers = {
    'Authorization': 'Bearer ' + accessToken,
    'Content-type': 'application/json'          # set Content-type to 'application/json'
}

# New topic
new_topic = 'hello_topic_' + get_random_string()
print("Topic " + new_topic + " is going to be used for demo.")

topics = []

# Create a  new topic
# Example of topic config
topic_config = {
    "partition_count": 1,
    "replication_factor": 1,
    "topic_properties": {
        "retention.ms": 604800000,
        "min.insync.replicas": "1"
    }
}

create_topic_url = api_format.format(api_version=api_version, rest_api=topic_api_format.format(topic_name=new_topic))
response = requests.put(create_topic_url, headers=headers, json=topic_config, timeout=request_timeout, verify=verify_https)
print(response.content)

if response.ok:
    while new_topic not in topics:
        print("The new topic " + new_topic + " is not visible yet. sleep 30 seconds...")
        time.sleep(30)
        # List Topic
        get_topic_url = api_format.format(api_version=api_version, rest_api=get_topic_api)

        response = requests.get(get_topic_url, headers={'Authorization': 'Bearer ' + accessToken}, timeout=request_timeout, verify=verify_https)
        topic_list = response.json()
        topics = topic_list.get("topics", [])
else:
    print("Topic " + new_topic + " was created. Exit.")
    sys.exit(1)

# Produce messages to new_topic
# Example payload of Producer REST API
payload_json = {
    "records": [
        {
            "key": "key1",
            "value": "**********"         # A string                              
        },
        {
            "partition": 0,
            "value": 5                    # An integer
        },
        {
            "value": 3.14                 # A floating number
        },
        {
            "value": {                    # A JSON object
                "id": 1,
                "name": "HDInsight Kafka REST proxy"
            }
        },
        {
            "value": [                    # A list of JSON objects
                {
                    "id": 1,
                    "name": "HDInsight Kafka REST proxy 1"
                },
                {
                    "id": 2,
                    "name": "HDInsight Kafka REST proxy 2"
                },
                {
                    "id": 3,
                    "name": "HDInsight Kafka REST proxy 3"
                }
            ]
        },
        {
            "value": {                  # A nested JSON object
                "group id": 1,
                "HDI Kafka REST": {
                    "id": 1,
                    "name": "HDInsight Kafka REST proxy 1"
                },
                "HDI Kafka REST server info": {
                    "id": 1,
                    "name": "HDInsight Kafka REST proxy 1",
                    "servers": [
                        {
                            "server id": 1,
                            "server name": "HDInsight Kafka REST proxy server 1"
                        },
                        {
                            "server id": 2,
                            "server name": "HDInsight Kafka REST proxy server 2"
                        },
                        {
                            "server id": 3,
                            "server name": "HDInsight Kafka REST proxy server 3"
                        }
                    ]
                }
            }
        }
    ]
}

print("Payloads in a Producer request: \n", payload_json)
producer_url = api_format.format(api_version=api_version, rest_api=producer_api_format.format(topic_name=new_topic))
response = requests.post(producer_url, headers=headers, json=payload_json, timeout=request_timeout, verify=verify_https)
print(response.content)

# Consume messages from the topic
partition_id = 0
offset = 0
count = 2

while True:
    consumer_url = api_format.format(api_version=api_version, rest_api=consumer_api_format.format(topic_name=new_topic, partition_id=partition_id, offset=offset, count=count))
    print("Consuming " + str(count) + " messages from offset " + str(offset))

    response = requests.get(consumer_url, headers=headers, timeout=request_timeout, verify=verify_https)

    if response.ok:
        messages = response.json()
        print("Consumed messages: \n" + json.dumps(messages, indent=2))
        next_offset = response.headers.get("NextOffset")
        if offset == next_offset or not messages.get("records", []):
            print("Consumer caught up with producer. Exit for now...")
            break

        offset = next_offset

    else:
        print("Error " + str(response.status_code))
        break
        
# List partitions
get_partitions_url = api_format.format(api_version=api_version, rest_api=partitions_api_format.format(topic_name=new_topic))
print("Fetching partitions from  " + get_partitions_url)

response = requests.get(get_partitions_url, headers={'Authorization': 'Bearer ' + accessToken}, timeout=request_timeout, verify=verify_https)
partition_list = response.json()
print("Partition list: \n" + json.dumps(partition_list, indent=2))

# List a partition
get_partition_url = api_format.format(api_version=api_version, rest_api=partition_api_format.format(topic_name=new_topic, partition_id=partition_id))
print("Fetching metadata of a partition from  " + get_partition_url)

response = requests.get(get_partition_url, headers={'Authorization': 'Bearer ' + accessToken}, timeout=request_timeout, verify=verify_https)
partition = response.json()
print("Partition metadata: \n" + json.dumps(partition, indent=2))

```

Det finns ett annat exempel på hur du hämtar en token från Azure för REST-proxy med hjälp av ett spiral kommando. **Observera att vi behöver det `scope=https://hib.azurehdinsight.net/.default` angivna när du hämtar en token.**

```cmd
curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -d 'client_id=<clientid>&client_secret=<clientsecret>&grant_type=client_credentials&scope=https://hib.azurehdinsight.net/.default' 'https://login.microsoftonline.com/<tenantid>/oauth2/v2.0/token'
```

## <a name="next-steps"></a>Nästa steg

* [Kafka REST proxy API-referens dokument](/rest/api/hdinsight-kafka-rest-proxy/)
