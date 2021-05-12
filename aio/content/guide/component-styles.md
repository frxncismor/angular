# Estilos de componentes

Las aplicaciones de Angular son estilizadas con CSS estandar. Esto significa que usted puede aplicar todo lo que sabe sobre hojas de estilo CSS, selectores, reglas y media queries directamente a las aplicaciones de Angular.

Adicionalmente, Angular puede agrupar _estilos de componentes_ con componentes,
permitiendo un diseño mas modular que las hojas de estilo regulares.

Esta página describe como cargar y aplicar estos estilos de componentes.

Puede ejecutar el <live-example></live-example> en Stackblitz y descargar el codigo desde ahí.

## Usando estilos de componentes

Para cada componente Angular que escribe, puede definir no solo una plantilla HTML, sino también los estilos CSS que van con esa plantilla,
especificando los selectores, reglas y media queries que necesite.

Una forma de hacer esto es estableciendo la propiedad `styles` en la metadata del componente.
La propiedad `styles` toma un array de strings que contiene el codigo CSS.
Usualmente usted le da una cadena de caracteres, como en el siguiente ejemplo:

<code-example path="component-styles/src/app/hero-app.component.ts" header="src/app/hero-app.component.ts"></code-example>

## Alcance del estilo

<div class="alert is-critical">

Los estilos especificados en la metadata de `@Component` _aplican solo en la plantilla de ese componente_.

</div>

_No son heredados_ por ningún componente anidado dentro de la plantilla ni por ningún contenido proyectado en el componente.

En este ejemplo, el estilo `h1` aplica solo a `HeroAppComponent`,
no al `HeroMainComponent` anidado ni a las etiquetas `<h1>` en cualquier otro lugar de la aplicación.

Esta restricción de alcance es una **_característica modular de estilo_**.

- Puede utilizar los selectores nombres de clase CSS que tengan más sentido en el contexto de cada componente.

- Los nombres de clase y los selectores son locales del componente y no chocan con
  clases y selectores utilizados en otras partes de la aplicación.

- Los cambios de estilos en otras partes de la aplicación no afectan los estilos del componente.

- Puede ubicar el código CSS de cada componente con el código TypeScript y HTML del componente,
  lo que conduce a una estructura de proyecto ordenada y limpa.

- Puede cambiar o eliminar el código CSS del componente sin buscar en el
  toda la aplicación para encontrar en dónde más se usa el código.

{@a special-selectors}

## Selectores especiales

Los estilos de componentes tienen algunos _selectores_ especiales del mundo del alcance de estilos DOM de sombra (Shadow DOM)
(descritos en la página [CSS Scoping Module Level 1](https://www.w3.org/TR/css-scoping-1) en el sitio [W3C](https://www.w3.org))

Las siguientes secciones describen estos selectores.
### :host

Utilice el selector de pseudo clase `:host` para seleccionar estilos en el elemento que _aloja_ el componente (opuesto a los elementos seleccionados _dentro_ de la plantilla del componente).

<code-example path="component-styles/src/app/hero-details.component.css" region="host" header="src/app/hero-details.component.css"></code-example>

El selector `:host` es la unica manera de seleccionar el elemento anfitrión. No puede alcanzar el elemento anfitrión desde adentro del componente con otros selectores porque no es parte de la propia plantilla del componente. El elemento anfitrión está en la plantilla de un componente principal.

Utilice el _formulario de función_ para aplicar estilos de anfitrión condicionalmente incluyendo otro selector entre paréntesis después de `:host`

El siguiente ejemplo selecciona el elemento anfitrión otra vez, pero solo cuando también tiene la clase CSS `active`.

<code-example path="component-styles/src/app/hero-details.component.css" region="hostfunction" header="src/app/hero-details.component.css"></code-example>

### :host-context

A veces es útil aplicar estilos basados en una condición _fuera_ de la vista de un componente.
Por ejemplo, una clase de tema CSS puede ser aplicada al elemento del documento `<body>`, y usted quiere cambiar como su componente se ve en base a eso.

Utilice el selector de la pseudo-clase `:host-context()`, el cual trabaja como la función de forma de `:host()`. El selector `:host-context()` busca una clase CSS en cualquier antepasado del elemento host del componente,
hasta la raíz del documento. El selector `: host-context ()` es útil cuando se combina con otro selector.

El siguiente ejemplo aplica un estilo `background-color` a todos los elementos `<h2>` _dentro_ del componente, solo si algún elemento ancestro tiene la clase CSS `theme-light`.

<code-example path="component-styles/src/app/hero-details.component.css" region="hostcontext" header="src/app/hero-details.component.css"></code-example>

### (obsoleto) `/deep/`, `>>>`, and `::ng-deep`

Los estilos de componentes normalmente aplican solo al HTML en la propia plantilla del componente.

La acplicación de la pseudo-clase `::ng-deep` a cualquier regla de CSS deshabilita completamente la encapsulación de vistas para esa regla. Cualquier estilo con `::ng-deep` aplicado se convierte en un estilo global. Para alcanzar el estilo especificado al componente actual y todos sus descendientes, asegúrese de incluir el selector `:host` antes de `::ng-deep`. Si el combinador `:: ng-deep` se usa sin el selector de pseudoclase`: host`, el estilo puede salirse a otros componentes.

El siguiente ejemplo apunta a todos los elementos `<h3>`, desde el elemento host hacia abajo
a través de este componente a todos sus elementos hijos en el DOM.

<code-example path="component-styles/src/app/hero-details.component.css" region="deep" header="src/app/hero-details.component.css"></code-example>

El combinador `/deep/` también tiene los alias `>>>` y `:: ng-deep`.

<div class="alert is-important">

Utilice `/deep/`, `>>>` y `::ng-deep` solo con la encapsulación de vista _emulada_.

Emulada es la encapsulación de vista predeterminada y más utilizada. Para obtener más información, consulte la sección [Controlar la encapsulación de vistas](guide/component-styles#view-encapsulation) 

</div>

<div class="alert is-important">

El combinador descendiente que perfora las sombras está en desuso y se está eliminando el [soporte de los principales navegadores](https://www.chromestatus.com/features/6750456638341120) y herramientas.

Como tal, planeamos eliminar el soporte en Angular (para los 3 de `/ deep /`, `>>>` y `:: ng-deep`).
Hasta entonces, se debería preferir `:: ng-deep` para una compatibilidad más amplia con las herramientas.

</div>

{@a loading-styles}

## Loading component styles

There are several ways to add styles to a component:

- By setting `styles` or `styleUrls` metadata.
- Inline in the template HTML.
- With CSS imports.

The scoping rules outlined earlier apply to each of these loading patterns.

### Styles in component metadata

You can add a `styles` array property to the `@Component` decorator.

Each string in the array defines some CSS for this component.

<code-example path="component-styles/src/app/hero-app.component.ts" header="src/app/hero-app.component.ts (CSS inline)">
</code-example>

<div class="alert is-critical">

Reminder: these styles apply _only to this component_.
They are _not inherited_ by any components nested within the template nor by any content projected into the component.

</div>

The Angular CLI command [`ng generate component`](cli/generate) defines an empty `styles` array when you create the component with the `--inline-style` flag.

<code-example language="sh" class="code-shell">
ng generate component hero-app --inline-style
</code-example>

### Style files in component metadata

You can load styles from external CSS files by adding a `styleUrls` property
to a component's `@Component` decorator:

<code-tabs>
  <code-pane header="src/app/hero-app.component.ts (CSS in file)" path="component-styles/src/app/hero-app.component.1.ts"></code-pane>
  <code-pane header="src/app/hero-app.component.css" path="component-styles/src/app/hero-app.component.css"></code-pane>
</code-tabs>

<div class="alert is-critical">

Reminder: the styles in the style file apply _only to this component_.
They are _not inherited_ by any components nested within the template nor by any content projected into the component.

</div>

<div class="alert is-helpful">

You can specify more than one styles file or even a combination of `styles` and `styleUrls`.

</div>

When you use the Angular CLI command [`ng generate component`](cli/generate) without the `--inline-style` flag, it creates an empty styles file for you and references that file in the component's generated `styleUrls`.

<code-example language="sh" class="code-shell">
ng generate component hero-app
</code-example>

### Template inline styles

You can embed CSS styles directly into the HTML template by putting them
inside `<style>` tags.

<code-example path="component-styles/src/app/hero-controls.component.ts" region="inlinestyles" header="src/app/hero-controls.component.ts">
</code-example>

### Template link tags

You can also write `<link>` tags into the component's HTML template.

<code-example path="component-styles/src/app/hero-team.component.ts" region="stylelink" header="src/app/hero-team.component.ts">
</code-example>

<div class="alert is-critical">

When building with the CLI, be sure to include the linked style file among the assets to be copied to the server as described in the [CLI wiki](https://github.com/angular/angular-cli/wiki/stories-asset-configuration).

<!-- 2018-10-16: The link above is still the best source for this information. -->

Once included, the CLI will include the stylesheet, whether the link tag's href URL is relative to the application root or the component file.

</div>

### CSS @imports

You can also import CSS files into the CSS files using the standard CSS `@import` rule.
For details, see [`@import`](https://developer.mozilla.org/en/docs/Web/CSS/@import)
on the [MDN](https://developer.mozilla.org) site.

In this case, the URL is relative to the CSS file into which you're importing.

<code-example path="component-styles/src/app/hero-details.component.css" region="import" header="src/app/hero-details.component.css (excerpt)">
</code-example>

### External and global style files

When building with the CLI, you must configure the `angular.json` to include _all external assets_, including external style files.

Register **global** style files in the `styles` section which, by default, is pre-configured with the global `styles.css` file.

See the [CLI wiki](https://github.com/angular/angular-cli/wiki/stories-global-styles) to learn more.

<!-- 2018-10-16: The link above is still the best source for this information. -->

### Non-CSS style files

If you're building with the CLI,
you can write style files in [sass](http://sass-lang.com/), [less](http://lesscss.org/), or [stylus](http://stylus-lang.com/) and specify those files in the `@Component.styleUrls` metadata with the appropriate extensions (`.scss`, `.less`, `.styl`) as in the following example:

<code-example>
@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.scss']
})
...
</code-example>

The CLI build process runs the pertinent CSS preprocessor.

When generating a component file with `ng generate component`, the CLI emits an empty CSS styles file (`.css`) by default.
You can configure the CLI to default to your preferred CSS preprocessor
as explained in the [CLI wiki](https://github.com/angular/angular-cli/wiki/stories-css-preprocessors "CSS Preprocessor integration").

<!-- 2018-10-16: The link above is still the best source for this information. -->

<div class="alert is-important">

Style strings added to the `@Component.styles` array _must be written in CSS_ because the CLI cannot apply a preprocessor to inline styles.

</div>

{@a view-encapsulation}

## View encapsulation

As discussed earlier, component CSS styles are encapsulated into the component's view and don't
affect the rest of the application.

To control how this encapsulation happens on a _per
component_ basis, you can set the _view encapsulation mode_ in the component metadata.
Choose from the following modes:

- `ShadowDom` view encapsulation uses the browser's native shadow DOM implementation (see
  [Shadow DOM](https://developer.mozilla.org/en-US/docs/Web/Web_Components/Shadow_DOM)
  on the [MDN](https://developer.mozilla.org) site)
  to attach a shadow DOM to the component's host element, and then puts the component
  view inside that shadow DOM. The component's styles are included within the shadow DOM.

- `Native` view encapsulation uses a now deprecated version of the browser's native shadow DOM implementation - [learn about the changes](https://hayato.io/2016/shadowdomv1/).

- `Emulated` view encapsulation (the default) emulates the behavior of shadow DOM by preprocessing
  (and renaming) the CSS code to effectively scope the CSS to the component's view.
  For details, see [Inspecting generated CSS](guide/component-styles#inspect-generated-css) below.

- `None` means that Angular does no view encapsulation.
  Angular adds the CSS to the global styles.
  The scoping rules, isolations, and protections discussed earlier don't apply.
  This is essentially the same as pasting the component's styles into the HTML.

To set the components encapsulation mode, use the `encapsulation` property in the component metadata:

<code-example path="component-styles/src/app/quest-summary.component.ts" region="encapsulation.native" header="src/app/quest-summary.component.ts"></code-example>

`ShadowDom` view encapsulation only works on browsers that have native support
for shadow DOM (see [Shadow DOM v1](https://caniuse.com/#feat=shadowdomv1) on the
[Can I use](http://caniuse.com) site). The support is still limited,
which is why `Emulated` view encapsulation is the default mode and recommended
in most cases.

{@a inspect-generated-css}

## Inspecting generated CSS

When using emulated view encapsulation, Angular preprocesses
all component styles so that they approximate the standard shadow CSS scoping rules.

In the DOM of a running Angular application with emulated view
encapsulation enabled, each DOM element has some extra attributes
attached to it:

<code-example format="">
  &lt;hero-details _nghost-pmm-5>
    &lt;h2 _ngcontent-pmm-5>Mister Fantastic&lt;/h2>
    &lt;hero-team _ngcontent-pmm-5 _nghost-pmm-6>
      &lt;h3 _ngcontent-pmm-6>Team&lt;/h3>
    &lt;/hero-team>
  &lt;/hero-detail>

</code-example>

There are two kinds of generated attributes:

- An element that would be a shadow DOM host in native encapsulation has a
  generated `_nghost` attribute. This is typically the case for component host elements.
- An element within a component's view has a `_ngcontent` attribute
  that identifies to which host's emulated shadow DOM this element belongs.

The exact values of these attributes aren't important. They are automatically
generated and you never refer to them in application code. But they are targeted
by the generated component styles, which are in the `<head>` section of the DOM:

<code-example format="">
  [_nghost-pmm-5] {
    display: block;
    border: 1px solid black;
  }

h3[_ngcontent-pmm-6] {
background-color: white;
border: 1px solid #777;
}
</code-example>

These styles are post-processed so that each selector is augmented
with `_nghost` or `_ngcontent` attribute selectors.
These extra selectors enable the scoping rules described in this page.
