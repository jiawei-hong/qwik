---
title: Resumable
---

# Resumable

When an SSR/SSG application boots up on a client, it requires that the framework on the client restore two pieces of information:

1. Frameworks need to locate event listeners and install them on the DOM nodes to make the application interactive;
2. Frameworks need to build up an internal data structure representing the application component tree.
3. Frameworks need to restore the application state.

Collectively, this is known as hydration. All current generations of frameworks require this step to make the application interactive.

The above step is expensive for two reasons:

1. The frameworks have to download all of the component code associated with the current page.
2. The frameworks have to execute the templates associated with the components on the page to rebuild the listener location and the internal component tree.

The above describes why the current generation of frameworks have an expensive startup which is proportional to the complexity of the application which is being hydrated. The more complex the application is, the more expensive the hydration cost, both in terms of the code to download and the code to execute.

Hydration is a fundamental property of the current generation of frameworks.

Qwik is different because it does not require hydration to resume an application on the client. Not requiring hydration is what makes the Qwik application startup instantaneous. Qwik needs to solve the event listener and component tree state in a way that is compatible with a no-code startup.

## Event Listeners

DOM without event listeners is just a static page; it is not an application. Today's standard for all sites is quite a high level of interactivity, so even the most static-looking sites are full of event listeners. These include menus, hovers, expanding details, or even full-on interactive apps.

Existing frameworks solve the event listener by downloading the components and executing their templates to collect event listeners that are then attached to the DOM. The current approach has these issues:

1. Requires the template code to be eagerly downloaded;
2. Requires template code to be eagerly executed;
3. Requires the event handler code to be downloaded eagerly (to be attached.)

The above approach does not scale. As the application becomes more complicated, the amount of code needed to download eagerly and execute grows proportionally with the size of the application. This negatively impacts the application startup performance and hence the user experience.

Qwik solves the above issue by serializing the event listens into DOM like so:

```htmlembedded=
<button on:click="./chunk.js#handler_symbol">click me</button>
```

Qwik still needs to collect the listener information, but this step is done as part of the SSR/SSG. The results of SSR/SSG are then serialized into HTML so that the browser does not need to do anything to resume the execution. Notice that the `on:click attribute contains all of the information to resume the application without doing anything eagerly.

1. Qwikloader sets up a single global listener instead of many individual listeners per DOM element. This step can be done with no application code present.
2. The HTML contains a URL to the chunk and symbol name. The attribute tells Qwikloader which code chunk to download and which symbol to retrieve from the chunk.
3. Finally, to make all of the above possible, Qwik's event processing implementation understands asynchronicity which allows insertion of asynchronous lazy loading.

## Component Trees

Frameworks work with component trees. To that end, frameworks need a complete understanding of the component trees to know which components need to be rerendered and when. If you look into the existing framework SSR/SSG output, the component boundary information has been destroyed. There is no way of knowing where component boundaries are by looking at the resulting HTML. To recreate this information, frameworks re-execute the component templates and memoize the component boundary location. Re-execution is what hydration is. Hydration is expensive as it requires both the download of component templates and their execution.

Qwik collects component boundary information as part of the SSR/SSG and then serializes that information into HTML. The result is that Qwik can:

1. Rebuild the component hierarchy information without the component code actually being present. The component code can remain lazy.
2. Qwik can do this lazily only for the components which need to be rerendered rather than all upfront.
3. Qwik collects relationship information between stores and components. This creates a subscription model that informs Qwik which components need to be re-rendered as a result of state change. The subscription information also gets serialized into HTML.

### Application State

Existing frameworks usually have a way of serializing the application state into HTML so that the state can be restored as part of hydration. In this way, they are very similar to Qwik. However, Qwik has state management more tightly integrated into the lifecycle of the components. In practice, this means that component can be delayed loaded independently from the state of the component. This is not easily achievable in existing frameworks because component props are usually created by the parent component. This creates a chain reaction. In order to restore component X, its parents need to be restored as well. Qwik allows any component to be resumed without the parent component code being present.

## Resumability

The above describes the differences that make Qwik resumable. Qwik startup performance is fast because there is no startup. There is nothing that Qwik needs to do eagerly to resume the application.

A good mental model is that Qwik applications at any point in their lifecycle can be serialized and moved to a different VM instance. (Server to browser). There, the application simply resumes where the serialization stopped. No hydration is required. This is why we say that Qwik applications don't hydrate; they resume.