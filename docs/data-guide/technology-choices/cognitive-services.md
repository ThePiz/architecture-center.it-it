---
title: Scelta di una tecnologia di servizi cognitivi
description: ''
author: zoinerTejada
ms.date: 02/12/2018
ms.openlocfilehash: 13d510056e4b1ce6eeec603427658215691e48ab
ms.sourcegitcommit: 1f4cdb08fe73b1956e164ad692f792f9f635b409
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/08/2019
ms.locfileid: "54110987"
---
# <a name="choosing-a-microsoft-cognitive-services-technology"></a>Scelta di una tecnologia di servizi cognitivi Microsoft

I servizi cognitivi Microsoft sono API basate sul cloud che è possibile usare in applicazioni e flussi di dati di intelligenza artificiale. Forniscono modelli già sottoposti a training da usare nell'applicazione, che non richiedono dati o l'esecuzione del training del modello da parte dell'utente. I servizi cognitivi sono sviluppati dal team di ricerca e intelligenza artificiale Microsoft e sfruttano i più recenti algoritmi di apprendimento avanzato. Vengono utilizzati nelle interfacce HTTP REST e sono disponibili SDK per numerosi framework di sviluppo di applicazioni comuni.

I servizi cognitivi includono:

- Analisi del testo
- Visione artificiale
- Analisi di video
- Riconoscimento e generazione vocale
- Comprensione del linguaggio naturale
- Ricerca intelligente

Vantaggi principali:

- Semplificazione delle attività di sviluppo per i servizi di intelligenza artificiale all'avanguardia.
- Integrazione semplificata nelle app tramite interfacce REST HTTP.
- Supporto integrato per l'utilizzo dei servizi cognitivi in Azure Data Lake Analytics.

Considerazioni:

- Disponibili solo nel Web. È in genere necessaria la connettività Internet. Un'eccezione è il Servizio visione artificiale personalizzato, il cui modello con training può essere esportato per eseguire stime nei dispositivi e in IoT Edge.

- Nonostante un ampio supporto per la personalizzazione, è possibile che i servizi disponibili non soddisfino tutti i requisiti di analisi predittiva.

<!-- markdownlint-disable MD026 -->

## <a name="what-are-your-options-when-choosing-amongst-the-cognitive-services"></a>Opzioni disponibili per la scelta di servizi cognitivi

<!-- markdownlint-disable MD026 -->

In Azure sono disponibili numerosi servizi cognitivi. L'elenco aggiornato di questi servizi è disponibile in una directory classificata in base all'area funzionale supportata:

- [Visione artificiale](https://azure.microsoft.com/services/cognitive-services/directory/vision/)
- [Sintesi vocale](https://azure.microsoft.com/services/cognitive-services/directory/speech/)
- [Conoscenza](https://azure.microsoft.com/services/cognitive-services/directory/know/)
- [Ricerca](https://azure.microsoft.com/services/cognitive-services/directory/search/)
- [Lingua](https://azure.microsoft.com/services/cognitive-services/directory/lang/)

## <a name="key-selection-criteria"></a>Criteri di scelta principali

Per limitare le possibilità di scelta, rispondere prima di tutto a queste domande:

- Quali tipi di dati vengono utilizzati? Limitare la scelta in base al tipo di dati di input utilizzati. Ad esempio, se l'input è un testo, selezionare i servizi che hanno questo tipo di input.

- Sono disponibili dati per eseguire il training del modello? In caso affermativo, prendere in considerazione i servizi personalizzati che consentono di eseguire il training dei modelli sottostanti con i dati forniti, per prestazioni migliori e maggiore precisione.

## <a name="capability-matrix"></a>Matrice delle funzionalità

Le tabelle seguenti contengono un riepilogo delle differenze principali in termini di funzionalità.

### <a name="uses-prebuilt-models"></a>Uso di modelli predefiniti

|                                                   |             Tipo di input              |                                                                                Vantaggi principali                                                                                |
|---------------------------------------------------|-------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|                API Analisi del testo                 |                Text                 |                                                       Valutare il sentiment e il rilevamento degli argomenti per identificare le opinioni degli utenti.                                                        |
|                API Collegamento entità                 |                Text                 |                                               Potenziare i collegamenti dati dell'app con il riconoscimento e la disambiguazione delle entità denominate.                                               |
| Language Understanding Intelligent Service (LUIS) |                Text                 |                                                          Insegnare alle app come riconoscere i comandi degli utenti.                                                          |
|                 Servizio QnA Maker                 |                Text                 |                                             Estrarre informazioni in formato di domande frequenti per ottenere risposte discorsive e facili da analizzare.                                              |
|              API Analisi linguistica              |                Text                 |                                                            Semplificare concetti linguistici complessi e analizzare il testo.                                                             |
|           Knowledge Exploration Service           |                Text                 |                                          Abilitare esperienze di ricerca interattive su dati strutturati tramite input in linguaggio naturale.                                          |
|              Web Language Model API               |                Text                 |                                                         Usare modelli di linguaggio predittivi con training su dati Web.                                                         |
|              API Academic Knowledge               |                Text                 |                                        Sfruttare l'ampia varietà di contenuti accademici disponibile in Microsoft Academic Graph popolata da Bing.                                         |
|               API Suggerimenti automatici Bing                |                Text                 |                                                        Implementare nell'app opzioni di suggerimento automatico intelligenti per l'esecuzione di ricerche.                                                        |
|               API Controllo ortografico Bing                |                Text                 |                                                             Rilevare e correggere errori di ortografia nell'app.                                                             |
|                API Traduzione testuale                |                Text                 |                                                                           Usare un sistema di traduzione automatica.                                                                            |
|                API Consigli                |                Text                 |                                                             Prevedere e consigliare gli articoli più appetibili per i clienti.                                                              |
|              API Ricerca entità Bing               |       Testo (query di ricerca Web)       |                                                           Identificare e ampliare le informazioni sulle entità ricavate dal Web.                                                           |
|               API Ricerca immagini Bing               |       Testo (query di ricerca Web)       |                                                                            Eseguire la ricerca di immagini.                                                                             |
|               API Ricerca notizie Bing                |       Testo (query di ricerca Web)       |                                                                             Eseguire la ricerca di notizie.                                                                              |
|               API Ricerca video Bing               |       Testo (query di ricerca Web)       |                                                                            Eseguire la ricerca di video.                                                                             |
|                API Ricerca Web Bing                |       Testo (query di ricerca Web)       |                                                        Ottenere risultati di ricerca migliori da miliardi di documenti Web.                                                        |
|                  API Riconoscimento vocale Bing                  |           Testo o sintesi vocale            |                                                                  Convertire l'input vocale in testo e viceversa.                                                                   |
|              API Riconoscimento voce              |               Sintesi vocale                |                                                       Usare la sintesi vocale per identificare e autenticare singole voci.                                                        |
|               API Traduzione vocale               |               Sintesi vocale                |                                                                   Eseguire traduzioni vocali in tempo reale.                                                                   |
|                API Visione artificiale                |    Immagini (o fotogrammi da video)    | Estrarre informazioni utili da immagini, creare automaticamente descrizioni di foto, eseguire la derivazione di tag, riconoscere celebrità, estrarre testo e creare anteprime accurate. |
|                 Content Moderator                 |        Testo, immagini o video        |                                                               Sfruttare funzionalità di moderazione automatizzata di immagini, testo e video.                                                                |
|                    API Emozioni                    | Immagini (foto con persone) |                                                              Identificare le emozioni delle persone.                                                               |
|                     API Viso                      | Immagini (foto con persone) |                                                       Rilevare, identificare, analizzare, organizzare e contrassegnare con tag i visi nelle foto.                                                       |
|                   Video Indexer                   |                Video                |                        Approfondimenti video, ad esempio sentiment, trascrizione vocale, convertitore vocale, riconoscimento di volti ed emozioni ed estrazione di parole chiave.                         |

### <a name="trained-with-custom-data-you-provide"></a>Training con dati personalizzati forniti dall'utente

| | Tipo di input | Vantaggi principali |
| --- | --- | --- |
| Servizio visione artificiale personalizzato | Immagini (o fotogrammi da video) | Personalizzare i modelli di visione artificiale. |
| Servizio di riconoscimento vocale personalizzato | Sintesi vocale | Superare gli ostacoli al riconoscimento vocale, come modo di parlare, rumore di fondo e vocabolario. |
| Servizio decisionale personalizzato | Contenuto Web (ad esempio, feed RSS) | Usare l'apprendimento automatico per selezionare automaticamente il contenuto appropriato per la home page. |
| API Ricerca personalizzata Bing | Testo (query di ricerca Web) | Strumento di ricerca di livello commerciale. |
