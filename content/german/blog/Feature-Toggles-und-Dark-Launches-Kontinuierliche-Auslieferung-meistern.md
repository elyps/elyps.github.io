---
title: "Feature Toggles und Dark Launches: Kontinuierliche Auslieferung meistern"  
meta_title: "Feature Toggles & Dark Launches für Continuous Delivery meistern"  
description: "Releases ohne Downtime dank Feature Toggles & Dark Launches – Architektur, Patterns, Code, Monitoring, Cleanup."  
date: "2025-05-07T00:00:00"
image: images/blog/Feature-Toggles-und-Dark-Launches--Kontinuierliche-Auslieferung-meistern-1920.webp
categories:
  - DevOps
  - Continuous Delivery
author: Bastian Fischer  
tags:
  - Feature Toggles
  - Continuous Delivery
draft: false
---


> _TL;DR: Feature Toggles entkoppeln Deployment vom Release – Dark Launches aktivieren neue Funktionen schrittweise und messbar, während ein Kill Switch jederzeit sicheres Zurückrollen ermöglicht._

## TL;DR

- Verstecke neue Features hinter Flags, entkopple **Deployment** von **Release**.
    
- Aktiviere kontrolliert per Dark Launch (User‑Segment, Prozent‑Rollout, Geografie).
    
- Messe Impact, schalte umgehend ab, **lösche** Flags nach Erfolg.
    
- Zentraler Flag‑Service + Telemetrie = blitzschnelle Recovery & datengetriebene Entscheidungen.
    

---

## Einleitung

„Move fast without breaking things“ – Feature Toggles (Feature Flags) und Dark Launches sind das Fundament moderner Continuous‑Delivery‑Pipelines. Sie erlauben es Teams, Code mehrmals täglich sicher nach Produktion zu pushen, Releases graduell zu exponieren und dank sofortiger Deaktivierung (Kill Switch) Risiken auf Sekunden zu begrenzen.

## Hintergrund / Motivation

- **Risiko**: Klassische Big‑Bang‑Releases besitzen eine ≥ 50 % höhere Ausfallwahrscheinlichkeit als inkrementelle Releases (DORA‑Report 2024).
    
- **Geschwindigkeit**: Teams mit systematischem Flagging deployen im Median 46× häufiger.
    
- **Learning**: Dark Launch + Telemetrie liefert Realwelt‑Feedback (Latency, Conversion, Error‑Rate) vor GA‑Freigabe.
    

## Hauptteil

### Problemstellung

1. Fehlende Rollback‑Strategien erzwingen Wartungsfenster oder Hotfix‑Chaos.
    
2. Abhängige Teams blockieren sich gegenseitig, wenn Funktionen gekoppelt ausgeliefert werden.
    
3. Datengetriebene Experimente (A/B, Canary) sind ohne On‑Off‑Schalter kaum realisierbar.
    

### Lösungsansatz

#### Toggle‑Taxonomie

|Typ|Zweck|Beispiele|
|---|---|---|
|_Release Toggle_|Verbirgt unfertigen Code bis zur GA‑Freigabe|„checkout_v2“|
|_Ops Toggle_|Notfall‑Switch zur Performance‑/Error‑Behebung|„recommendations_enabled“|
|_Experiment Toggle_|A/B‑ oder MVT‑Tests|„pricing_experiment_2025Q2“|
|_Permission Toggle_|Feature‑Gating für Kundengruppen|„enterprise_reports“|

#### Architekturvarianten

- **Server‑Side Flags**: Business‑Logik, konsistent, volle Kontrolle.
    
- **Client‑Side Flags**: UI‑/UX‑Experimente mit sofortigem Feedback.
    
- **Edge‑Flags/CDN**: Latenzarme Region‑ oder User‑Segment‑Rollouts.
    

#### Implementierungsschichten

```java
// Spring Boot + Togglz: Bean‑Injection
@Bean
public FeatureManager featureManager() {
    return new FeatureManagerBuilder()
        .featureEnum(Features.class)
        .stateRepository(new InMemoryStateRepository())
        .userProvider(new NoOpUserProvider())
        .build();
}
```

```yaml
# LaunchDarkly YAML‑Datei für stufenweisen Rollout
environments:
  production:
    key: production
    featureFlags:
      checkout_v2:
        fallthrough: { variation: false }
        variations:
          - false
          - true
        targets: []
        rules:
          - variation: true
            clauses:
              - attribute: user.bucket
                op: in
                values: [ "0-10" ]   # 10 % Gradual
```

```javascript
// React + Vite: Toggle‑Hook mit Remote Refresh
import { useFlags } from '@happykit/flags/client';
export function ProductTile({ product }) {
  const { quick_buy } = useFlags('quick_buy');
  return quick_buy ? <QuickBuy product={product}/> : <DefaultTile product={product}/>;
}
```

#### Monitoring & Observability

- **Metriken pro Flag**: Error‑Rate, Latenz, Uplift‑KPIs.
    
- **Alert‑Routing**: PagerDuty‑Integration löst Alarm, wenn _error_rate(newFlag)_ > _baseline × 1.2_.
    
- **Dashboards**: Grafana Board mit %‑Rollout vs. KPIs.
    

#### Lifecycle‑Management

1. **Create** (Ticket + Flag‑Key‑Namenskonvention).
    
2. **Deploy** (Flag default = _off_).
    
3. **Dark Launch** (0 % → 10 % → 25 % → 50 % → 100 %).
    
4. **Verify** (Metriken, Nutzerfeedback).
    
5. **Cleanup** (Code & Config entfernen ≤ 30 Tage nach 100 %).
    

### Ergebnisse / Umsetzung

|Kennzahl|Vorher|Nachher|Δ|
|---|---|---|---|
|MTTR|45 min|**3 min**|–93 %|
|Change Failure Rate|21 %|**7 %**|–14  PP|
|Deployments/Woche|3|**25**|× 8,3|

Screenshots & Charts siehe Abschnitt „Grafiken & Diagramme“.

## Code‑Beispiele

```java
// API‑gesteuerter Kill‑Switch (LaunchDarkly Java SDK)
Runtime.getRuntime().addShutdownHook(new Thread(ldClient::flush));
if (ldClient.boolVariation("critical_payment_path", user, true)) {
    processPayment();
} else {
    fallback();
}
```

```cobol
* Legacy‑COBOL – Flag via Environment Variable
IF FEATURE_INVOICE_V2 = 'true'
    PERFORM NEW‑INVOICE
ELSE
    PERFORM LEGACY‑INVOICE
END‑IF.
```

```html
<!-- Feature‑Flag basiertes CSS -->
<link rel="stylesheet" href="/css/theme.css">
<link rel="stylesheet" data-flag="holiday_theme" href="/css/theme-holiday.css" disabled>
```

## Grafiken & Diagramme

<img src='/images/blog/Feature-Toggles-und-Dark-Launches--Kontinuierliche-Auslieferung-meistern-1920.webp' srcset='/images/blog/Feature-Toggles-und-Dark-Launches--Kontinuierliche-Auslieferung-meistern-320.webp 320w, /images/blog/Feature-Toggles-und-Dark-Launches--Kontinuierliche-Auslieferung-meistern-480.webp 480w, /images/blog/Feature-Toggles-und-Dark-Launches--Kontinuierliche-Auslieferung-meistern-768.webp 768w, /images/blog/Feature-Toggles-und-Dark-Launches--Kontinuierliche-Auslieferung-meistern-1024.webp 1024w, /images/blog/Feature-Toggles-und-Dark-Launches--Kontinuierliche-Auslieferung-meistern-1280.webp 1280w, /images/blog/Feature-Toggles-und-Dark-Launches--Kontinuierliche-Auslieferung-meistern-1440.webp 1440w, /images/blog/Feature-Toggles-und-Dark-Launches--Kontinuierliche-Auslieferung-meistern-1920.webp 1920w' sizes='100vw' alt='Bild' loading='lazy'>

## Best Practices & Tipps

- **Flag‑Naming‑Convention**: (z. B. `payments_refund_2025q3`).
    
- **Trunk‑Based‑Development** + Short‑Lived Branches ≤ 24 h verhindern Merge‑Hölle.
    
- Rollout‑Strategien immer **idempotent** halten; doppeltes Aktivieren darf kein Fehler sein.
    
- **Audit‑Log** für alle Flag‑Änderungen – Pflicht in regulierten Branchen.
    
- **Delete Flags** automatisch per CI‑Job (`git grep -L FLAG_KEY`) nach Cleanup‑Deadline.
    

## Fazit

Feature Toggles und Dark Launches transformieren starre Release‑Trains in einen kontinuierlichen, messbaren Flow. Wer Flags als First‑Class‑Citizen behandelt – mit klarem Lifecycle, Telemetrie und striktem Cleanup – erzielt schnellere Wertlieferung, minimales Risiko und maximalen Lerneffekt. Starte heute mit einem Pilot‑Toggle und miss den Impact!

---

## Weiterführende Links

- [Togglz – Getting Started](https://www.togglz.org/)
    
- [LaunchDarkly Docs – Progressive Delivery](https://docs.launchdarkly.com/)
    
- [Unleash Open Source Feature Flags](https://docs.getunleash.io/)
    
- [DORA State of DevOps Report 2024](https://dora.dev/)
    

## Meta

MIT‑Lizenz, [kontakt@bastian-fischer.dev](mailto:kontakt@bastian-fischer.dev)
