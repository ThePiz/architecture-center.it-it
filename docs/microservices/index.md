---
title: Creazione di microservizi in Azure
description: 'Progettazione, creazione e gestione di architetture di microservizi in Azure'
ms.date: 03/07/2019
layout: LandingPage
ms.topic: landing-page
---

# <a name="building-microservices-on-azure"></a>Creazione di microservizi in Azure

<!-- markdownlint-disable MD033 -->

<img src="../_images/microservices.svg" style="float:left; margin-top:8px; margin-right:8px; max-width: 80px; max-height: 80px;"/>

I microservizi sono uno stile di architettura diffuso per la creazione di applicazioni che offrono resilienza, scalabilità elevata, possibilità di distribuzione indipendente e capacità di evolversi rapidamente. Ma un'architettura di microservizi efficiente richiede un approccio diverso alla progettazione e allo sviluppo di applicazioni.

<ul  class="panelContent cardsZ">
<li style="display: flex; flex-direction: column;">
    <a href="./introduction.md" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardText">
                        <h3>Cosa sono i microservizi?</h3>
                        <p>Quali sono le differenze tra i microservizi e altre architetture e quando è consigliabile usarli?</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
<li style="display: flex; flex-direction: column;">
    <a href="../guide/architecture-styles/microservices.md" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardText">
                        <h3>Stile di architettura di microservizi</h3>
                        <p>Panoramica dettagliata dello stile di architettura di microservizi</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
</ul>

## <a name="examples-of-microservices-architectures"></a>Esempi di architetture di microservizi

<ul  class="panelContent cardsZ">
<li style="display: flex; flex-direction: column;">
    <a href="../example-scenario/infrastructure/service-fabric-microservices.md" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardText">
                        <h3>Usare Service Fabric per scomporre applicazioni monolitiche</h3>
                        <p>Un approccio iterativo alla scomposizione di un sito Web ASP.NET in microservizi.</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
<li style="display: flex; flex-direction: column;">
    <a href="../example-scenario/data/ecommerce-order-processing.md" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardText">
                        <h3>Elaborazione degli ordini scalabile in Azure</h3>
                        <p>Elaborazione degli ordini con un modello di programmazione funzionale implementato tramite microservizi.</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
</ul>

## <a name="build-a-microservices-application"></a>Creare applicazioni di microservizi

<ul  class="panelContent cardsZ">
<li style="display: flex; flex-direction: column;">
    <a href="./model/domain-analysis.md" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardText">
                        <h3>Usare l'analisi del dominio per modellare i microservizi</h3>
                        <p>Per evitare le comuni insidie associate alla progettazione di microservizi, usare l'analisi del dominio per definirne i limiti.</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
<li style="display: flex; flex-direction: column;">
    <a href="../reference-architectures/microservices/aks.md" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardText">
                        <h3>Architettura di riferimento per il servizio Azure Kubernetes (AKS)</h3>
                        <p>Questa architettura di riferimento mostra una configurazione di base di AKS che può essere usata come punto di partenza per la maggior parte delle distribuzioni.</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
<li style="display: flex; flex-direction: column;">
    <a href="../reference-architectures/microservices/service-fabric.md" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardText">
                        <h3>Architettura di riferimento per Azure Service Fabric</h3>
                        <p>Questa architettura di riferimento mostra la configurazione consigliata che può essere usata come punto di partenza per la maggior parte delle distribuzioni.</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
<li style="display: flex; flex-direction: column;">
    <a href="./design/index.md" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardText">
                        <h3>Progettare un'architettura di microservizi</h3>
                        <p>Questi articoli offrono informazioni dettagliate su come sviluppare un'applicazione di microservizi, in base a un'implementazione di riferimento che usa il servizio Azure Kubernetes (AKS).</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
<li style="display: flex; flex-direction: column;">
    <a href="./design/patterns.md" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardText">
                        <h3>Modelli di progettazione</h3>
                        <p>Un set di utili modelli di progettazione per i microservizi.</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
</ul>

## <a name="operate-microservices-in-production"></a>Gestire i microservizi in produzione

<ul  class="panelContent cardsZ">
<li style="display: flex; flex-direction: column;">
    <a href="./logging-monitoring.md" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardText">
                        <h3>Registrazione e monitoraggio</h3>
                        <p>Considerando la natura distribuita delle architetture di microservizi, le attività di registrazione e monitoraggio sono particolarmente critiche.</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
<li style="display: flex; flex-direction: column;">
    <a href="./ci-cd.md" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardText">
                        <h3>Integrazione e distribuzione continue</h3>
                        <p>Integrazione continua e recapito continuo (CI/CD) sono un requisito chiave per la riuscita dei microservizi.</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
</ul>
