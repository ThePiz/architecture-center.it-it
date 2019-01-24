---
title: Progettare per le modifiche
titleSuffix: Azure Application Architecture Guide
description: Una progettazione in grado di evolversi è fondamentale per l'innovazione continua.
author: MikeWasson
ms.date: 08/30/2018
ms.topic: guide
ms.service: architecture-center
ms.subservice: reference-architecture
ms.custom: seojan19
ms.openlocfilehash: 4a1ed92f70660f16c07b4b472c3ef358af4319c9
ms.sourcegitcommit: 1b50810208354577b00e89e5c031b774b02736e2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2019
ms.locfileid: "54481746"
---
# <a name="design-for-evolution"></a>Progettare per l'evoluzione

## <a name="an-evolutionary-design-is-key-for-continuous-innovation"></a>Una progettazione in grado di evolversi è fondamentale per l'innovazione continua

Tutte le applicazioni di successo cambiano nel tempo, per correggere i bug, aggiungere nuove funzionalità, introdurre nuove tecnologie o rendere più scalabili e resilienti i sistemi esistenti. Se tutte le parti di un'applicazione sono strettamente collegate, diventa molto difficile apportare modifiche al sistema. Una modifica in una parte dell'applicazione potrebbe interrompere il funzionamento di un'altra parte oppure comportare la propagazione delle modifiche nell'intera base di codice.

Il problema non è limitato alle applicazioni monolitiche. Un'applicazione può essere costituita da più servizi, ma presentare comunque stretti collegamenti che rendono il sistema rigido e vulnerabile. Quando invece i servizi sono progettati per evolversi, i team possono innovare e distribuire continuamente nuove funzionalità.

I microservizi stanno diventando un modo comune per ottenere una progettazione in grado di evolversi, perché soddisfano molti dei requisiti illustrati in questo argomento.

## <a name="recommendations"></a>Consigli

**Applicare coesione elevata e regime di controllo libero**. Un servizio è *coesivo* se fornisce funzionalità correlate logicamente. I servizi sono *a regime di controllo libero* se è possibile modificare un servizio senza modificare l'altro. La coesione elevata significa in genere che le modifiche in una funzione richiederanno modifiche in altre funzioni correlate. Se quando si aggiorna un servizio sono necessari aggiornamenti coordinati ad altri servizi, i servizi potrebbero non essere coesivi. Uno degli obiettivi della progettazione basata su dominio è quello di identificare tali confini.

**Incapsulare le informazioni di dominio**. Quando un client utilizza un servizio, la responsabilità di applicare le regole business del dominio non deve cadere sul client. Il servizio deve invece incapsulare tutte le informazioni di dominio che ricadono sotto la sua responsabilità. In caso contrario, ogni client dovrebbe applicare le regole business e le informazioni di dominio si troverebbero sparse in diverse parti dell'applicazione.

**Usare la messaggistica asincrona**. La messaggistica asincrona consente di disaccoppiare il producer del messaggio dal consumer. Il producer non dipende dal consumer per la risposta al messaggio o per un'azione specifica. Con un'architettura di pubblicazione/sottoscrizione, il producer potrebbe non conoscere chi sta utilizzando il messaggio. I nuovi servizi possono utilizzare facilmente i messaggi senza apportare alcuna modifica al producer.

**Non integrare le informazioni di dominio in un gateway**. I gateway possono essere utili in un'architettura di microservizi, per operazioni come il routing delle richieste, la conversione del protocollo, il bilanciamento del carico o l'autenticazione. Il gateway deve tuttavia essere usato solo per queste funzionalità dell'infrastruttura. Non deve implementare informazioni di dominio, per evitare di costituire una forte dipendenza.

**Esporre interfacce aperte**. Evitare di creare livelli di conversione personalizzati tra servizi. Un servizio deve invece esporre un'API con un contratto API ben definito. L'API deve consentire il controllo delle versioni, affinché sia possibile aggiornarla mantenendo la compatibilità con le versioni precedenti. In questo modo, è possibile aggiornare un servizio senza coordinare gli aggiornamenti per tutti i servizi upstream che dipendono da esso. I servizi pubblici devono esporre un'API RESTful tramite HTTP. I servizi back-end possono usare un protocollo di messaggistica di tipo RPC per motivi di prestazioni.

**Progettare e testare in base ai contratti di servizio**. Quando i servizi espongono API ben definite, è possibile sviluppare e testare in base a tali API. In questo modo, è possibile sviluppare e testare un singolo servizio senza avviare tutti i servizi dipendenti. Naturalmente, i test di integrazione e carico devono comunque essere eseguiti in base ai servizi reali.

**Astrarre l'infrastruttura dalla logica di dominio**. Non confondere la logica di dominio con le funzionalità correlate all'infrastruttura, come la messaggistica o la persistenza. In caso contrario, le modifiche alla logica di dominio richiederanno aggiornamenti ai livelli di infrastruttura e viceversa.

**Spostare le problematiche trasversali in un servizio distinto**. Se, ad esempio, diversi servizi necessitano di autenticare le richieste, è possibile spostare questa funzionalità in un servizio distinto. Sarà quindi possibile aggiornare il servizio di autenticazione, ad esempio aggiungendo un nuovo flusso di autenticazione, senza toccare i servizi che lo usano.

**Distribuire i servizi in modo indipendente**. Quando il team DevOps può distribuire un singolo servizio indipendentemente dagli altri servizi nell'applicazione, gli aggiornamenti possono avvenire in modo più rapido e sicuro. Le nuove funzionalità e le correzioni di bug possono essere implementate a cadenza più regolare. Progettare sia l'applicazione che il processo di rilascio per supportare gli aggiornamenti indipendenti.
