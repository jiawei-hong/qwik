---
title: Overview
---

# Overview

Qwik is a new kind of web framework that takes a fundamentally different approach to deliver near-instant web applications. (Lot of evidence shows that fast web app startup leads to more satisfied users leads to better conversions and thus profits)

**Qwik is:**

- **General-purpose web application framework**: It can be used to build any web applications or websites that are currently built using other popular frameworks.
- **Resumable**: Rusumabilty is a unique property of Qwik, that allows a Qwik application to have instant-on interactivity. A Qwik application can (1) execute on the server, (2) serialize its state into HTML (3) and continue execution on the client (browser). Qwik applications do not have to perform client-side hydration (attaching DOM listeners and rebuilding application state as other frameworks require.) We call this instant-on capability resumability.
- **Lazy Loadable**: Qwik applications are intrinsically lazy-loadable. Qwik takes lazy-loading to the extreme. Every listener, rendering function, initialization code, style, etc. can be lazy-loaded by the framework without any special ceremony by the developer.