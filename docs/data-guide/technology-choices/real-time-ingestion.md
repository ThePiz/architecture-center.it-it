---
title: Scelta di una tecnologia di inserimento di messaggi in tempo reale
description: 
author: zoinerTejada
ms:date: 02/12/2018
ms.openlocfilehash: 2e6578b779950b5ef11bda7b8ba1fb2e45e09f4e
ms.sourcegitcommit: 3d9ee03e2dda23753661a80c7106d1789f5223bb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/23/2018
---
# <a name="choosing-a-real-time-message-ingestion-technology-in-azure"></a>Scelta di una tecnologia di inserimento di messaggi in tempo reale in Azure

L'elaborazione in tempo reale riguarda i flussi di dati acquisiti in tempo reale ed elaborati con latenza minima. Molte soluzioni di elaborazione in tempo reale richiedono che un archivio di inserimento dei messaggi funga da buffer per i messaggi e supporti l'elaborazione scale-out, il recapito affidabile e altri tipi di semantica di accodamento dei messaggi. 

## <a name="what-are-your-options-for-real-time-message-ingestion"></a>Opzioni per l'inserimento di messaggi in tempo reale

- [Hub eventi di Azure](/azure/event-hubs/)
- [Hub IoT Azure](/azure/iot-hub/)
- [Kafka in HDInsight](/azure/hdinsight/kafka/apache-kafka-get-started)

## <a name="azure-event-hubs"></a>Hub eventi di Azure

[Hub eventi di Azure](/azure/event-hubs/) è una piattaforma di streaming di dati e un servizio di inserimento degli eventi estremamente scalabile, che consente di ricevere ed elaborare milioni di eventi al secondo. Hub eventi consente di elaborare e archiviare eventi, dati o dati di telemetria generati dal software distribuito e dai dispositivi. I dati inviati a un hub eventi possono essere trasformati e archiviati usando qualsiasi provider di analisi in tempo reale o adattatori di invio in batch/archiviazione. Hub eventi offre funzionalità di pubblicazione-sottoscrizione a bassa latenza su larga scala, pertanto è appropriato per scenari con Big Data.

## <a name="azure-iot-hub"></a>Hub IoT Azure

L'[hub IoT di Azure](/azure/iot-hub/) è un servizio gestito che consente comunicazioni bidirezionali affidabili e sicure tra milioni di dispositivi IoT e un back-end basato sul cloud.

Le funzionalità dell'hub IoT sono:

* Più opzioni per la comunicazione da dispositivo a cloud e da cloud a dispositivo. Queste opzioni includono la messaggistica unidirezionale, il trasferimento di file e i metodi di richiesta-risposta.
* Routing dei messaggi ad altri servizi di Azure.
* Archivio in cui eseguire query sui metadati e sulle informazioni di stato sincronizzate del dispositivo.
* Comunicazioni sicure e controllo di accesso tramite chiavi di sicurezza per dispositivo o certificati X.509.
* Monitoraggio per gli eventi relativi alla connettività dei dispositivi e alla gestione delle identità dei dispositivi.

In termini di inserimento dei messaggi, l'hub IoT è simile a Hub eventi. È stato tuttavia progettato specificatamente per la gestione della connettività dei dispositivi IoT, non solo per l'inserimento dei messaggi. Per altre informazioni, vedere [Confronto tra l'hub IoT e Hub eventi di Azure](/azure/iot-hub/iot-hub-compare-event-hubs). 

## <a name="kafka-on-hdinsight"></a>Kafka in HDInsight

[Apache Kafka](https://kafka.apache.org/) è una piattaforma open source distribuita per lo streaming, che consente di creare applicazioni in streaming e pipeline di dati in tempo reale. Kafka offre anche una funzionalità di broker di messaggi simile a una coda di messaggi, dove è possibile pubblicare e sottoscrivere flussi dei dati denominati. È scalabile orizzontalmente, a tolleranza di errore ed estremamente veloce. [Kafka in HDInsight](/azure/hdinsight/kafka/apache-kafka-get-started) offre Kafka come servizio gestito, a scalabilità e disponibilità elevate in Azure. 

Ecco alcuni casi di uso comuni per Kafka:

* **Messaggistica**. Poiché supporta il modello di pubblicazione-sottoscrizione dei messaggi, Kafka viene usato spesso come broker di messaggi.
* **Rilevamento delle attività**. Poiché Kafka offre la registrazione ordinata dei record, può essere usato per tenere traccia delle attività, come le azioni dell'utente in un sito Web, e per ricrearle.
* **Aggregazione**. Usando l'elaborazione dei flussi, è possibile aggregare informazioni da flussi diversi per combinarle e centralizzarle in dati operativi.
* **Trasformazione**. Usando l'elaborazione dei flussi, è possibile combinare e arricchire dati da più argomenti di input in uno o più argomenti di output.

## <a name="key-selection-criteria"></a>Criteri di scelta principali

Per limitare le possibilità di scelta, rispondere prima di tutto a queste domande:

- È necessaria la comunicazione bidirezionale tra i dispositivi IoT e Azure? Se sì, scegliere l'hub IoT.

- È necessario gestire l'accesso per i singoli dispositivi e poter revocare l'accesso a un dispositivo specifico? Se sì, scegliere l'hub IoT.

## <a name="capability-matrix"></a>Matrice delle funzionalità

Le tabelle seguenti contengono un riepilogo delle differenze principali in termini di funzionalità. 

| | Hub IoT | Hub eventi | Kafka in HDInsight |
| --- | --- | --- | --- |
| Comunicazioni da cloud a dispositivo | Sì | No  | No  |
| Caricamento di file avviati da dispositivo | Sì | No  | No  |
| Informazioni sullo stato dei dispositivi | [Dispositivi gemelli](/azure/iot-hub/iot-hub-devguide-device-twins) | No  | No  |
| Supporto dei protocolli | MQTT, AMQP, HTTPS <sup>1</sup> | AMQP, HTTPS | [Protocollo Kafka](https://cwiki.apache.org/confluence/display/KAFKA/A+Guide+To+The+Kafka+Protocol) |
| Sicurezza | Identità per dispositivo, controllo di accesso revocabile. | Criteri di accesso condivisi, revoca limitata tramite criteri di pubblicazione. | Autenticazione tramite SASL, autorizzazione modulare, integrazione con servizi di autenticazione esterna supportata. |

[1] È anche possibile usare il [gateway del protocollo Azure IoT](/azure/iot-hub/iot-hub-protocol-gateway) come gateway personalizzato per consentire l'adattamento del protocollo per l'hub IoT.

Per altre informazioni, vedere [Confronto tra l'hub IoT e Hub eventi di Azure](/azure/iot-hub/iot-hub-compare-event-hubs).
