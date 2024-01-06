<h1 align="center">Angular platforms</h1>

<img src="img/title.png" style="display:block; margin:auto;" width="500px">

<span style="background-color: coral;font-weight:bold;color:#2d2d2d; padding: 5px; border-radius: 3px; display:inline-block; margin: 5px 0 0 5px;">#appBootstrap </span>
<span style="background-color: coral;font-weight:bold;color:#2d2d2d; padding: 5px; border-radius: 3px; display:inline-block; margin: 5px 0 0 5px;">#lView</span>
<span style="background-color: coral;font-weight:bold;color:#2d2d2d; padding: 5px; border-radius: 3px; display:inline-block; margin: 15px 0 0 5px;">#renderer3</span>
<span style="background-color: coral;font-weight:bold;color:#2d2d2d; padding: 5px; border-radius: 3px; display:inline-block; margin: 5px 0 0 5px;">#platformNativeScriptDynamic</span>
<span style="background-color: coral;font-weight:bold;color:#2d2d2d; padding: 5px; border-radius: 3px; display:inline-block; margin: 5px 0 0 5px;">#platformApi</span>
<span style="background-color: coral;font-weight:bold;color:#2d2d2d; padding: 5px; border-radius: 3px; display:inline-block; margin: 5px 0 0 5px;">#ɵDomRendererFactory2</span>
<span style="background-color: coral;font-weight:bold;color:#2d2d2d; padding: 5px; border-radius: 3px; display:inline-block; margin: 5px 0 0 5px;">#ivy</span>
<span style="background-color: coral;font-weight:bold;color:#2d2d2d; padding: 5px; border-radius: 3px; display:inline-block; margin: 5px 0 0 5px;">#platformTerminalDynamic</span>
<span style="background-color: coral;font-weight:bold;color:#2d2d2d; padding: 5px; border-radius: 3px; display:inline-block; margin: 5px 0 0 5px;">#angularCore</span>
<span style="background-color: coral;font-weight:bold;color:#2d2d2d; padding: 5px; border-radius: 3px; display:inline-block; margin: 5px 0 0 5px;">#ɵɵlistener</span>
<span style="background-color: coral;font-weight:bold;color:#2d2d2d; padding: 5px; border-radius: 3px; display:inline-block; margin: 5px 0 0 5px;">#templateInstructions</span>

---

Should Write about app boostraping first
Shorty write why you should always use the renderer for dom manipulation

### Introduction

The Platform API in Angular is a module set that provide basic services for the browser and. These modules include `BrowserModule`, `CommonModule`, and others essentials for creating Angular applications that can run in a browser environment.

It is important to note that Angular supports other platforms besides `platformBrowserDynamic` such as:

- Mobile using `platformNativeScriptDynamic` with [NativeScript](https://nativescript.org/).
- Server Side Rendering using `platformServer` with [AngularUniversal](https://blog.angular-university.io/angular-universal/).
- Linux terminal using `platformTerminalDynamic` with [Platform-terminal](https://github.com/Tibing/platform-terminal/tree/master) etc.

### How can Angular become platform-agnostic?

The Angular framework is comprised of two main components:

- **Angular Core** the heart of any Angular application. It provides fundamental services, including dependency injection, lifecycle hooks, data binding, and more. The Angular Core library is responsible for bootstrapping and launching the application.
- **Renderer** is an engine that convert HTML templates into views. The rendering process involves parsing the template, checking for errors, and producing a view object. The `Renderer` interface is used to manipulate the UI programmatically.

This separation of concerns is what allows Angular to become platform agostic, as Angular core doesn't directly use any web APIs, delegating all of the actual view / html creation to the `Render`.

### What is the renderer?

The `Renderer` is an `Ijectable` provided by the platform at application boostrap in the `main.ts` file.

`Renderer` provides an API for UI manipulations. It acts as an abstraction layer over direct DOM manipulations, allowing developers to write code that can be executed in environments that may not have access to the DOM, such as on the server, in a web worker, or on native mobile and desktop environments.

**`Renderer` vs `Renderer3`**<br>
The `Renderer2` and `Renderer3` interfaces in Angular both provide a way to interact with the DOM.
The main difference consists of security concerns: `Renderer2` was introduced in Angular 4 and `Renderer3` is an upgraded version of `Renderer2`.<br>
`Renderer3` was introduced in Angular 9 and it reduces the risk of XSS attacks because it does not use the `innerHTML` property, which is known to be a potential security vulnerability. Instead, it uses safer alternatives like `textContent` or `setAttribute`.

_It's important to note that as of Angular 12, `Renderer` has been deprecated and Renderer3 is now the standard for DOM manipulation in Angular._

## Renderer deep dive

To understand better what the Angular `Renderer` does behind the scenes lets take a look at what methods it implements:

```typescript
export interface Renderer {
  destroy(): void;
  createComment(value: string): RComment;
  createElement(name: string, namespace?: string | null): RElement;
  createText(value: string): RText;
  destroyNode?: ((node: RNode) => void) | null;
  appendChild(parent: RElement, newChild: RNode): void;
  insertBefore(
    parent: RNode,
    newChild: RNode,
    refChild: RNode | null,
    isMove?: boolean
  ): void;
  removeChild(parent: RElement, oldChild: RNode, isHostElement?: boolean): void;
  selectRootElement(
    selectorOrNode: string | any,
    preserveContent?: boolean
  ): RElement;

  parentNode(node: RNode): RElement | null;
  nextSibling(node: RNode): RNode | null;

  setAttribute(
    el: RElement,
    name: string,
    value: string | TrustedHTML | TrustedScript | TrustedScriptURL,
    namespace?: string | null
  ): void;
  removeAttribute(el: RElement, name: string, namespace?: string | null): void;
  addClass(el: RElement, name: string): void;
  removeClass(el: RElement, name: string): void;
  setStyle(
    el: RElement,
    style: string,
    value: any,
    flags?: RendererStyleFlags2
  ): void;
  removeStyle(el: RElement, style: string, flags?: RendererStyleFlags2): void;
  setProperty(el: RElement, name: string, value: any): void;
  setValue(node: RText | RComment, value: string): void;

  listen(
    target: GlobalTargetName | RNode,
    eventName: string,
    callback: (event: any) => boolean | void
  ): () => void;
}
```

[Source: angular/packages/core/src/render3/interfaces/renderer.ts](https://github.com/angular/angular/blob/a5a9b408e2eb64dcf1d3ca16da4897649dd2fc34/packages/core/src/render3/interfaces/renderer.ts#L33)

Everything Angular does regarding rendering the component (creating elements, setting attributes, subscribing to events, etc.) goes through that renderer object.

At application bootstrap, the `ɵDomRendererFactory2` internal factory is used to create the default DOM renderer that then gets provided in the environment injector (platform).
Angular uses this factory because different view encapsulations require different renderers. For example `view.encapsulation.shadow` uses the `ShadowDomRenderer``.

```typescript
@Injectable()
export class DomRendererFactory2 implements RendererFactory2, OnDestroy {
  //...
  private getOrCreateRenderer(element: any, type: RendererType2): Renderer2 {
    //...
    switch (type.encapsulation) {
      case ViewEncapsulation.Emulated:
        renderer = new EmulatedEncapsulationDomRenderer2(...);
        break;
      case ViewEncapsulation.ShadowDom:
        return new ShadowDomRenderer(...);
      default:
        renderer = new NoneEncapsulationDomRenderer(...);
        break;
    }
  }
  //...
}
```

[Source: angular/packages/platform-browser/src/dom/dom_renderer.ts](https://github.com/angular/angular/blob/a5a9b408e2eb64dcf1d3ca16da4897649dd2fc34/packages/platform-browser/src/dom/dom_renderer.ts#L63)

The `Renderer` can also be provided in several places:

- inside the `providers` array at bootstrap (default).
- inside the `router providers`, this enables us to use different renderers on different routes.
- using the `viewContainerRef`. At component creation, the component gets its own environment. injector we can use to provide the `Renderer`` just for that component and its sub-tree.

During the creation of the view, Angular uses the `Renderer` service to create and configure elements according to the view definition.
<br>

For example, if the view definition specifies that a `<div>` element should be created, Angular uses the `createElement` method of `Renderer` to create this element. Similarly, if the view definition specifies that a certain element should be added to the DOM, Angular uses the `appendChild` method of `Renderer` to add this element.
<br>

```ts
class DefaultDomRenderer2 implements Renderer2 {
  createElement(name: string, namespace?: string): any {
    constructor(private readonly doc: Document){}
    if (namespace) {
      return this.doc.createElementNS(NAMESPACE_URIS[namespace] || namespace, name);
    }
    return this.doc.createElement(name);
  }
}
```

[Source: angular/packages/platform-browser/src/dom/dom_renderer.ts](https://github.com/angular/angular/blob/a5a9b408e2eb64dcf1d3ca16da4897649dd2fc34/packages/platform-browser/src/dom/dom_renderer.ts#L146)

After the initial rendering of the view, Angular uses the `Renderer` service to update the view whenever the data bound to the view changes. Whenever a data-bound property changes, Angular uses `Renderer` to update the corresponding element in the DOM.
<br>

Finally, when a view is destroyed, Angular uses the `Renderer` service to clean up any resources associated with the view. This includes removing the view's root element from the DOM and cleaning up any event listeners attached to elements in the view.

### Other platforms move to the end

As we can see Angular can be configured to run on multiple platforms by implementing a custom Renderer for that platform. This is a very powerful feature of Angular as it allows us to leverage all the tools provided by the framework such as dependency injection, services, observables, data binding, etc.

<!-- spell checked until here -->

## How Angular parses templates and renders components

At the beginig of the compilation stage the component gets transformed into template instruction.These generated temaplate instructions call render specific methods and the rendere interacts with the platform.

![](img/platform.png)

The renderer is the bridge between your template and the platform that you are working with representig the platform and decumpling angular core form any direct ui manipulation.

![](img/bridge.png)

Our simple example app-component.ts:

```ts
@Component({
  template: `
    <div>This is inside the div</div>
    <app-parent />
  `,
  imports: [ParentComponent],
})
export class AppComponent {
  title = "ng-renderer";
}
```

Will generate the fallowing template instructions after compilation:

```js
class AppComponent {
  constructor() {
    this.title = 'ng-renderer';
  }
}
AppComponent.ɵfac = function AppComponent_Factory(t) {
  return new (t || AppComponent)();
};
AppComponent.ɵcmp = /*@__PURE__*/_angular_core__WEBPACK_IMPORTED_MODULE_1__["ɵɵdefineComponent"]({
  type: AppComponent,
  selectors: [["app-root"]],
  standalone: true,
  features: [_angular_core__WEBPACK_IMPORTED_MODULE_1__["ɵɵStandaloneFeature"]],
  decls: 3,
  vars: 0,
  template: function AppComponent_Template(rf, ctx) {
    if (rf & 1) {
      _angular_core__WEBPACK_IMPORTED_MODULE_1__["ɵɵelementStart"](0, "div");
      _angular_core__WEBPACK_IMPORTED_MODULE_1__["ɵɵtext"](1, "This is inside the div");
      _angular_core__WEBPACK_IMPORTED_MODULE_1__["ɵɵelementEnd"]();
      _angular_core__WEBPACK_IMPORTED_MODULE_1__["ɵɵelement"](2, "app-parent");
    }
  },
  dependencies: [_parent_component__WEBPACK_IMPORTED_MODULE_0__.Parent],
  encapsulation: 2
});`
```

For now we will focus on the `template` function.As we can see it recives two parameters:

- `ctx` represents the context object for the component. It holds the data and methods that are available for use in the template and provides a way for the template to interact with the component's properties and methods. Since a component can have multimple rendered instances this context also alows Angular to bind event listeners and properties to the propper instance.
- `rf` The rf (render flags) is a bitmask that helps Angular optimize rendering during change detection. It determins if the component is in the creation or the update phase.If the creation phase all the components contexnt and depency injection is rezolved.

`ɵɵelementStart` is part of Angular's renderer and is used to create and an HTML element in the component's view.

```js
_angular_core__WEBPACK_IMPORTED_MODULE_1__["ɵɵelementStart"](0, "div");
```

When we as a developer write a `div` in an angular .html template file we know that we refeer to the `div` element of the hmtl browser.
But if we look into the template function there is nothing that tells Angular that `div` is a html `div` an element.To angular core (the template fct) our `div` is just an generic element with the tag name`div`.

Lets take a close look at the `ɵɵelementStart` method:

```js
function ɵɵelementStart(index, name, attrsIndex, localRefsIndex) {
  /* ... */
  const renderer = lView[RENDERER];
  const native = (lView[adjustedIndex] = createElementNode(
    renderer,
    name,
    getNamespace$1()
  ));
  /* ... */
  return ɵɵelementStart;
}

/* ... */

function createElementNode(renderer, name, namespace) {
  ngDevMode && ngDevMode.rendererCreateElement++;
  // looks familiar?
  return renderer.createElement(name, namespace);
}
```

[Source:angular/packages/core/src/render3
/node_manipulation.ts](https://github.com/angular/angular/blob/a5a9b408e2eb64dcf1d3ca16da4897649dd2fc34/packages/core/src/render3/node_manipulation.ts#L123)

In our case `ɵɵelementStart` gets called with index is _0_ and name is _div_ since the `div` is the first element of our app-component template.

```js
const renderer = lView[RENDERER];
```

This is the line where out renderer is provided by the platform injector

```js
return renderer.createElement(name, namespace);
```

This is the first place where Angular knows that div refers to the an actual html div element.

**Event and property binding**

```html
<div [id]="title" title="my-title" (click)="onClick()">
  This is inside the div
</div>
```

If we add the onClick envent listner a id and a title property binding to our div in app component the recompiled template instructions will look like this:

```typescript
AppComponent.ɵcmp = /*@__PURE__*/ _angular_core__WEBPACK_IMPORTED_MODULE_1__[
  "ɵɵdefineComponent"
]({
  /* ... */
  consts: [["title", "my-title", 3, "id"]],
  template: function AppComponent_Template(rf, ctx) {
    if (rf & 1) {
      /* ... */
      _angular_core__WEBPACK_IMPORTED_MODULE_1__["ɵɵelementStart"](0, "div", 0);
      _angular_core__WEBPACK_IMPORTED_MODULE_1__["ɵɵlistener"](
        "click",
        function AppComponent_Template_div_click_0_listener() {
          return ctx.onClick();
        }
      );
      /* ... */
    }
    if (rf & 2) {
      _angular_core__WEBPACK_IMPORTED_MODULE_1__["ɵɵproperty"]("id", ctx.title);
    }
  },
  /* ... */
});
```

We can see that we have a couple of new fuctions. Firstly the bindings get added tp the const array. Secondaly a new function `ɵɵlistener` is genereted for the onclick event listener.

```js
_angular_core__WEBPACK_IMPORTED_MODULE_1__["ɵɵproperty"]("id", ctx.title);
```

The `ɵɵproperty` is binding the input `id`` using the ctx, the component instance, we can also see that it runs in the update phase of the component (rf & 2) this makes sence since inputs get rezolved in ngOnInit not in the consturctor().

```javascript
function ɵɵlistener(eventName, listenerFn, useCapture, eventTargetResolver) {
  const lView = getLView();
  const tView = getTView();
  const tNode = getCurrentTNode();
  listenerInternal(...);
  return ɵɵlistener;
}
```

If we take a close look at the `ɵɵlistener` method we can see that it also delegates the event listener to the renderer by calling `listenerInternal()` wich in turn calls `renderer.listen()`

```js
function listenerInternal(tView, lView, renderer, tNode, eventName, listenerFn, eventTargetResolver) {
    //...
    if (existingListener !== null) {
        //...
    } else {
    listenerFn = wrapListener(...);
    const cleanupFn = renderer.listen(target, eventName, listenerFn); // <- call to renderer
    ngDevMode && ngDevMode.rendererAddEventListener++;
    lCleanup.push(listenerFn, cleanupFn);
    tCleanup &&
        tCleanup.push(
        eventName,
        idxOrTargetGetter,
        lCleanupIndex,
        lCleanupIndex + 1
        );
    }
}
```
