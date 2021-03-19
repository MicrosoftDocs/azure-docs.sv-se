---
title: 'Snabb start: skicka händelser med hjälp av C-Azure Event Hubs'
description: 'Snabb start: den här artikeln innehåller en genom gång av hur du skapar ett C-program som skickar händelser till Azure Event Hubs.'
ms.topic: quickstart
ms.date: 06/23/2020
ms.openlocfilehash: bfe1ca1a45f7b33d7431aed13446d8d72f79fb90
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "85315662"
---
# <a name="quickstart-send-events-to-azure-event-hubs-using-c"></a>Snabb start: skicka händelser till Azure Event Hubs med C

## <a name="introduction"></a>Introduktion
Azure Event Hubs är en strömningstjänst för stordata och händelseinmatningstjänst som kan ta emot och bearbeta flera miljoner händelser per sekund. Event Hubs kan bearbeta och lagra händelser, data eller telemetri som producerats av distribuerade program och enheter. Data som skickas till en händelsehubb kan omvandlas och lagras med valfri provider för realtidsanalys eller batchbearbetnings-/lagringsadapter. En detaljerad översikt över Event Hubs finns i [Översikt över Event Hubs](event-hubs-about.md) och [Event Hubs-funktioner](event-hubs-features.md).

I den här självstudien beskrivs hur du skickar händelser till en Event Hub med ett konsol program i C. 

## <a name="prerequisites"></a>Förutsättningar
För att göra den här självstudien behöver du följande:

* En C-utvecklings miljö. Den här självstudien förutsätter gcc-stacken på en virtuell Azure Linux-dator med Ubuntu 14,04.
* [Microsoft Visual Studio](https://www.visualstudio.com/).
* **Skapa ett Event Hubs-namnområde och en Event Hub**. Använd [Azure Portal](https://portal.azure.com) för att skapa ett namn område av typen Event Hubs och hämta de autentiseringsuppgifter som programmet behöver för att kommunicera med händelsehubben. Om du behöver skapa ett namnområde och en händelsehubb följer du anvisningarna i [den här artikeln](event-hubs-create.md). Hämta värdet för åtkomst nyckeln för händelsehubben genom att följa anvisningarna i artikeln: [Hämta anslutnings sträng](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Du använder åtkomst nyckeln i koden som du skriver senare i den här självstudien. Standard nyckel namnet är: **RootManageSharedAccessKey**.

## <a name="write-code-to-send-messages-to-event-hubs"></a>Skriva kod för att skicka meddelanden till Event Hubs
I det här avsnittet visas hur du skriver en C-app för att skicka händelser till händelsehubben. Koden använder Proton AMQP-biblioteket från [Apache qpid-projektet](https://qpid.apache.org/). Detta är detsamma som att använda Service Bus köer och ämnen med AMQP från C som du ser [i det här exemplet](https://code.msdn.microsoft.com/Using-Apache-Qpid-Proton-C-afd76504). Mer information finns i dokumentationen för [qpid Proton](https://qpid.apache.org/proton/index.html).

1. På [sidan QPID AMQP Messenger](https://qpid.apache.org/proton/messenger.html)följer du anvisningarna för att installera qpid Proton, beroende på din miljö.
2. För att kompilera Proton-biblioteket installerar du följande paket:
   
    ```shell
    sudo apt-get install build-essential cmake uuid-dev openssl libssl-dev
    ```
3. Hämta [qpid Proton-biblioteket](https://qpid.apache.org/proton/index.html)och extrahera det, t. ex.:
   
    ```shell
    wget https://archive.apache.org/dist/qpid/proton/0.7/qpid-proton-0.7.tar.gz
    tar xvfz qpid-proton-0.7.tar.gz
    ```
4. Skapa en build-katalog, kompilera och installera:
   
    ```shell
    cd qpid-proton-0.7
    mkdir build
    cd build
    cmake -DCMAKE_INSTALL_PREFIX=/usr ..
    sudo make install
    ```
5. I arbets katalogen skapar du en ny fil med namnet **Sender. c** med följande kod. Kom ihåg att ersätta värdena för din SAS-nyckel/namn, händelsehubben och namn område. Du måste också ersätta en URL-kodad version av nyckeln för **SendRule** som skapades tidigare. Du kan URL-koda den [här](https://www.w3schools.com/tags/ref_urlencode.asp).
   
    ```c
    #include "proton/message.h"
    #include "proton/messenger.h"
   
    #include <getopt.h>
    #include <proton/util.h>
    #include <sys/time.h>
    #include <stddef.h>
    #include <stdio.h>
    #include <string.h>
    #include <unistd.h>
    #include <stdlib.h>
   
    #define check(messenger)                                                     \
      {                                                                          \
        if(pn_messenger_errno(messenger))                                        \
        {                                                                        \
          printf("check\n");                                                     \
          die(__FILE__, __LINE__, pn_error_text(pn_messenger_error(messenger))); \
        }                                                                        \
      }
   
    pn_timestamp_t time_now(void)
    {
      struct timeval now;
      if (gettimeofday(&now, NULL)) pn_fatal("gettimeofday failed\n");
      return ((pn_timestamp_t)now.tv_sec) * 1000 + (now.tv_usec / 1000);
    }  
   
    void die(const char *file, int line, const char *message)
    {
      printf("Dead\n");
      fprintf(stderr, "%s:%i: %s\n", file, line, message);
      exit(1);
    }
   
    int sendMessage(pn_messenger_t * messenger) {
        char * address = (char *) "amqps://{SAS Key Name}:{SAS key}@{namespace name}.servicebus.windows.net/{event hub name}";
        char * msgtext = (char *) "Hello from C!";
   
        pn_message_t * message;
        pn_data_t * body;
        message = pn_message();
   
        pn_message_set_address(message, address);
        pn_message_set_content_type(message, (char*) "application/octect-stream");
        pn_message_set_inferred(message, true);
   
        body = pn_message_body(message);
        pn_data_put_binary(body, pn_bytes(strlen(msgtext), msgtext));
   
        pn_messenger_put(messenger, message);
        check(messenger);
        pn_messenger_send(messenger, 1);
        check(messenger);
   
        pn_message_free(message);
    }
   
    int main(int argc, char** argv) {
        printf("Press Ctrl-C to stop the sender process\n");
   
        pn_messenger_t *messenger = pn_messenger(NULL);
        pn_messenger_set_outgoing_window(messenger, 1);
        pn_messenger_start(messenger);
   
        while(true) {
            sendMessage(messenger);
            printf("Sent message\n");
            sleep(1);
        }
   
        // release messenger resources
        pn_messenger_stop(messenger);
        pn_messenger_free(messenger);
   
        return 0;
    }
    ```
6. Kompilera filen, förutsatt att **gcc**:
   
    ```
    gcc sender.c -o sender -lqpid-proton
    ```

    > [!NOTE]
    > I den här koden används ett utgående fönster på 1 för att framtvinga meddelanden så snart som möjligt. Vi rekommenderar att ditt program försöker att använda batch-meddelanden för att öka data flödet. Se [sidan QPID AMQP Messenger](https://qpid.apache.org/proton/messenger.html) för information om hur du använder qpid Proton-biblioteket i den här miljön och i andra miljöer och från plattformar där bindningar tillhandahålls (för närvarande perl, php, python och ruby).

Kör programmet för att skicka meddelanden till händelsehubben. 

Grattis! Du har nu skickat meddelanden till en händelsehubb.

## <a name="next-steps"></a>Nästa steg
Läs följande artiklar:

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Funktioner och terminologi i Azure Event Hubs](event-hubs-features.md).


<!-- Images. -->
[21]: ./media/event-hubs-c-ephcs-getstarted/run-csharp-ephcs1.png
[24]: ./media/event-hubs-c-ephcs-getstarted/receive-eph-c.png
