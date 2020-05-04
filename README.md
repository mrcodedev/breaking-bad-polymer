# Breaking Bad Polymer

Creación de una app con Polymer para el uso en cells.

## Introducción

Este proyecto utilizará todo lo aprendido en los ejercicios realizados en este curso (Exercism). Lo más importante es el uso práctico de los principios que pueden ayudar a un estudiante en el desarrollo del día a día.

Se utiliza una API pública muy completa para ayudar al estudiante en el uso de los servicios REST (https://breakingbadapi.com/).

Hay objetivos que cumplir y reglas a seguir, el estudiante debe seguir las pautas para completar el proyecto.

## Normas

- **NO** se permiten bibliotecas de terceros.
- **NO** se permiten componentes de Polymer de terceros.
- Los estilos CSS de terceros **NO** están permitidos.
- Deberías usar Javascript nativo para el desarrollo de Polymer.
- Debe usar estilos CSS propios para el desarrollo de aplicaciones.
- El código debe tener al menos el 80% de la cobertura del código (valor de ramas) en las pruebas unitarias.

## Objetivos

1. Crear un proveedor de datos (componente de datos).
2. Crear un administrador de datos (componente de datos).
3. Crear un administrador (componente de datos).
4. Crear un componente de tarjeta (componente visual).
5. Crear un componente de lista de tarjetas (componente de regla empresarial).
6. Crear un componente de paginación (componente de mezcla).
7. Crear un componente de búsqueda (componente de mezcla).
8. Crear un componente de detalle de tarjeta (componente de mezcla).

## Recursos

Polymer Official - [Documentación](https://polymer-library.polymer-project.org/2.0/docs/devguide/feature-overview).
Breaking Bad - [API](https://breakingbadapi.com/)

## Objetivo 1 - Proveedor de datos

El proveedor de datos es el componente que se conectará a la API y recibirá los datos.

Este componente necesita las propiedades suficientes y necesarias para conectarse a todo tipo de API. Como anfitrión, ruta, params...

La documentación oficial del componente indica cómo se usa genéricamente. Deberán configurarse las siguientes propiedades:

- host: dominio al que desea realizar la solicitud.
- path: el punto final.
- params: params de consulta.
- body: cuerpo de la solicitud. Usualmente se usa para los métodos POST y PUT.
- headers: encabezados.
- method: tipo de solicitud (GET, POST, PUT, ...).

Para recopilar la respuesta de la solicitud, se utilizarán estos eventos:

- request-success: respuesta de servicio correcta.
- request-error: error en la respuesta del servicio.
- fetch: request.abort.

Su código html tendrá un aspecto similar al siguiente:

```html
<cells-generic-dp
  id="myDataProvider"
  host="https://www.breakingbadapi.com"
  path="/api/characters"
  headers=""
  params=""
  method="GET"
>
</cells-generic-dp>
```

El código tendrá que implementar los métodos para manejar el error o el éxito de la solicitud, y el método `generateRequest()` deberá usarse en el punto donde desea enviar la solicitud configurada con el código anterior.

## Objetivo 2 - Data Manager

El administrador de datos es un código que funciona con los datos recibidos por el proveedor de datos y se transforma de acuerdo con las reglas comerciales.

Representa el MODELO, está vinculado al proveedor de datos que prepara los datos (mapeo) para enviarlos al servicio REST, así como la recepción de los datos por el servicio REST y su mapeo para el administrador.

El manager es llamado por llamada de eventos.

Un ejemplo básico:

```html
<dom-module id="object-dm">
  <template>
    <cells-generic-dp
      id="dpid"
      host="https://www.breakingbadapi.com"
      params="[[ params ]]"
      headers="[[ headers ]]"
      method="[["
      method
      ]]
      path="[[ path ]]"
      handle-as="json"
      on-request-error="_onRequestError"
      on-request-success="_onRequestOK"
    ></cells-generic-dp>
  </template>
  <script>
    {
       class ObjectDM  {
          static get is() {
            return 'object-dm';
          }

          static get properties() {
            return {
                params: String,
                headers: String,
                method: {
                    value:'GET',
                    type: String
                },
                path: {
                    value:'/api/characters',
                    type: String
                }
            }
          }

          _onRequestOK(event) {
            ...
            // send result to manager using event
            this.dispatchEvent(new CustomEvent('page-list-event', {
                detail: { data },
                bubbles: true,
                composed: true
            }));
          }

          _onRequestError(event) {
            ...
            // send result to manager using event
            this.dispatchEvent(new CustomEvent('page-list-event-error', {
                detail: { data },
                bubbles: true,
                composed: true
            }));
          }
        }
     }
  </script>
  <\dom-module></dom-module
>
```

Tendrá un parámetro de entrada de datos y un evento de fin de transformación.

Los datos devueltos por el proveedor de datos, incluido el encabezado y la respuesta intacta, se transforman para generar una lista (en el caso de JavaScript, una matriz multidimensional de objetos), el objeto final será:

- id
- name
- image
- nickname
- birthday
- status
- occupation
- playedBy
- sessions

## Objetivo 3 - El manager

Funciona como un CONTROLADOR, recibirá los datos del administrador de datos enviados por eventos, el administrador debe tener oyentes de eventos que reciben los datos.

Ejemplo:

```html
<dom-module id="object-manager">
  <template>
    <object-dm
      id="my-data-manager"
      params="[[ _params ]]"
      headers="[[ _headers ]]"
      path="[[ _path ]]"
    ></object-dm>
    <page-list data="[[ _result ]]"></page-list>
  </template>
  <script>
    {
       class ObjectManager  {
          static get is() {
            return 'object-manager';
          }

          constructor() {
            super();
            this._params = "";
            this._headers = "";
            this._path = "";
            this._result = "";
          }

          ready() {
              super.ready();
              this.addEventListener('page-list-event', event => this._pageList(event));
              this.addEventListener('page-list-event-error', event => this._pageListError(event));
           }

          _pageList(event) {
            ...
            // Process the data
          }

          _pageListError(event) {
            ...
            // Process the data
          }
        }
     }
  </script>
  <\dom-module></dom-module
>
```

## Objetivo 4 - Componente Tarjeta

Todos los estilos y el diseño de los componentes deben ser creados por el usuario, las bibliotecas o el código de terceros no están permitidos.

Este es un componente visual.

## Objetivo 5 - Componente Lista de Tarjetas

Este es un componente que recibe una matriz con los datos de la tarjeta y utiliza este componente para mostrar el resultado.

Este es un componente con reglas comerciales que utilizan un componente visual.

Los estilos y diseños deben estar en el componente secundario, solo se puede escribir una pieza mínima de código de estilo y diseño en este componente.

## Objetivo 6 - Componente Paginación

Esta es una mezcla de reglas de negocio y componente visual, recibe una matriz con los datos del resultado y se representa como una paginación.

Los estilos y el diseño de los componentes deben ser creados por el usuario, las bibliotecas o el código de terceros no están permitidos.

## Objetivo 7 - Componente Búsqueda

Esta es una mezcla de reglas de negocio y componentes visuales, recibe una entrada que se envía a la API y recibe una matriz como resultado.

Los estilos y el diseño de los componentes deben ser creados por el usuario, las bibliotecas o el código de terceros no están permitidos.

## Objetivo 8 - Componente Detalle Tarjeta

El componente de la tarjeta debe tener un refactor para implementar su componente, es expandir los detalles en el componente de la tarjeta cuando el usuario hace clic en un botón en el interior, la expansión de detalles tendrá la función de animación CSS.

Este componente no tiene ejemplo porque es un desafío para el estudiante, ya que evalúa el criterio personal sobre cómo crear este componente.

## Cobertura Unit Testing

Primero instala a través de npm:

```
npm install web-component-tester --save-dev
```

```
npm install wct-istanbub --save-dev
```

Crea un archivo llamado `wct.conf.json` en la raíz del proyecto, agregua este código de inicio:

```json
{
  "suites": ["test/index.html"],
  "plugins": {
    "istanbub": {
      "local": {
        "browsers": ["chrome"]
      },
      "dir": "./coverage",
      "reporters": ["text-summary", "lcov"],
      "include": ["**/*.html"],
      "exclude": ["**/test/**", "**/bower_components/**"]
    }
  }
}
```

Ejecuta el comando para lanzar el test en chrome (un ejemplo):

```
wct -l chrome
```

Lo más importante es el valor de "Ramas", debe ser más del 80%

Se genera una carpeta llamada `coverage`, dentro hay un archivo `index.html` que proporciona un informe detallado del código.
