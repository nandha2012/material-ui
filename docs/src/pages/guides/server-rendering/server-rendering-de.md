# Server-Rendering

<p class="description">Der gebräuchlichste Anwendungsfall für das serverseitige Rendern ist das anfängliche Rendern, wenn ein Benutzer (oder Suchmaschinen-Crawler) Ihre App zum ersten Mal anfordert.</p>

Wenn der Server die Anforderung empfängt, stellt er die erforderlichen Komponenten in einem HTML-String dar und sendet sie als Antwort an den Client. Ab diesem Zeitpunkt übernimmt der Client die Rendering-Aufgaben.

## Material-UI auf dem Server

Die Client-Seite ist unkompliziert. Wir müssen nur das serverseitig erzeugte CSS entfernen. Let's take a look at the client file:

1. Eine neue [`ServerStyleSheets`](/styles/api/#serverstylesheets) Instanz bei jede Anfrage erstellen.
2. Den React-Baum mit dem serverseitigen Collector rendern.
3. Das CSS herausziehen.
4. Das CSS zum Client weiterleiten.

Auf der Clientseite wird das CSS ein zweites Mal eingefügt, bevor das serverseitige injizierte CSS entfernt wird.

## Installation

Im folgenden Rezept wird beschrieben, wie das serverseitige Rendering eingerichtet wird.

### Das Theme

Create a theme that will be shared between the client and the server:

`theme.js`

```js
import { createTheme } from '@material-ui/core/styles';
import red from '@material-ui/core/colors/red';

// Erstellen Sie eine Theme-Instanz.
const theme = createTheme({
  palette: {
    primary: {
      main: '#556cd6',
    },
    secondary: {
      main: '#19857b',
    },
    error: {
      main: red.A400,
    },
    background: {
      default: '#fff',
    },
  },
});

export default theme;
```

### Die Server-Seite

The following is the outline for what the server-side is going to look like. We are going to set up an [Express middleware](https://expressjs.com/en/guide/using-middleware.html) using [app.use](https://expressjs.com/en/api.html) to handle all requests that come in to the server. If you're unfamiliar with Express or middleware, just know that the handleRender function will be called every time the server receives a request.

`server.js`

```js
const css = sheets.toString();

  // Zurücksenden der gerenderten Seite an den Client.
function renderFullPage(html, css) {
  /* ... */
}

function handleRender(req, res) {
  /* ... */
}

const app = express();

// Isso é acionado toda vez que o servidor recebe uma solicitação.
*/
}

const app = express();

// Isso é acionado toda vez que o servidor recebe uma solicitação.
```

### Verarbeiten der Anfrage

Als Erstes müssen wir bei jeder Anfrage ein neues `ServerStyleSheets` erstellen.

When rendering, we will wrap `App`, the root component, inside a [`StylesProvider`](/styles/api/#stylesprovider) and [`ThemeProvider`](/styles/api/#themeprovider) to make the style configuration and the `theme` available to all components in the component tree.

The key step in server-side rendering is to render the initial HTML of the component **before** we send it to the client side. Dazu verwenden wir [ReactDOMServer.renderToString()](https://reactjs.org/docs/react-dom-server.html).

We then get the CSS from the `sheets` using `sheets.toString()`. As we are also using emotion as our default styled engine, we need to extract the styles from the emotion instance as well. For this we need to share the same cache definition for both the client and server:

`cache.js`

```js
import createCache from '@emotion/cache';

const cache = createCache({ key: 'css' });

export default cache;
```

With this we are creating new Emotion server instance and using this to extract the critical styles for the html as well.

We will see how this is passed along in the `renderFullPage` function.

```jsx
import express from 'express';
import * as React from 'react';
import ReactDOMServer from 'react-dom/server';
import { ServerStyleSheets, ThemeProvider } from '@material-ui/core/styles';
import createEmotionServer from '@emotion/server/create-instance';
import App from './App';
import theme from './theme';
import cache from './cache';

const { extractCritical } = createEmotionServer(cache);

function handleRender(req, res) {
  const sheets = new ServerStyleSheets();

  // Render the component to a string.
  const html = ReactDOMServer.renderToString(
    sheets.collect(
      <CacheProvider value={cache}>
        <ThemeProvider theme={theme}>
          <App />
        </ThemeProvider>
      </CacheProvider>,
    ),
  );

  // Grab the CSS from the sheets.
  const css = sheets.toString();

  // Grab the CSS from emotion
  const styles = extractCritical(html);

  // Send the rendered page back to the client.
  res.send(renderFullPage(html, `${css} ${styles.css}`));
}

const app = express();

app.use('/build', express.static('build'));

// This is fired every time the server-side receives a request.
*/
}

const app = express();

// Isso é acionado toda vez que o servidor recebe uma solicitação.
```

### Injizieren der ursprüngliche HTML Komponente und CSS

The final step on the server-side is to inject the initial component HTML and CSS into a template to be rendered on the client side.

```js
function renderFullPage(html, css) {
  return `
    <!DOCTYPE html>
    <html>
      <head>
        <title>My page</title>
        <style id="jss-server-side">${css}</style>
      </head>
      <body>
        <div id="root">${html}</div>
      </body>
    </html>
  `;
}
```

### Die Client-Seite

Die Client-Seite ist unkompliziert. Wir müssen nur das serverseitig erzeugte CSS entfernen. Let's take a look at the client file:

`client.js`

```jsx
import * as React from 'react';
import ReactDOM from 'react-dom';
import { ThemeProvider } from '@material-ui/core/styles';
import { CacheProvider } from '@emotion/react';
import App from './App';
import theme from './theme';
import cache from './cache';

function Main() {
  React.useEffect(() => {
    const jssStyles = document.querySelector('#jss-server-side');
    if (jssStyles) {
      jssStyles.parentElement.removeChild(jssStyles);
    }
  }, []);

  return (
    <CacheProvider value={cache}>
      <ThemeProvider theme={theme}>
        <App />
      </ThemeProvider>
    </CacheProvider>
  );
}

ReactDOM.hydrate(<Main />, document.querySelector('#root'));
```

## Referenzimplementierungen

Wir bieten verschiedene Referenzimplementierungen an, die Sie im [GitHub-Repository](https://github.com/mui-org/material-ui) finden können unter dem [`/examples`](https://github.com/mui-org/material-ui/tree/next/examples) Ordner:

- [Die Referenzimplementierung dieses Tutorials](https://github.com/mui-org/material-ui/tree/next/examples/ssr)
- [Gatsby](https://github.com/mui-org/material-ui/tree/next/examples/gatsby)
- https://github.com/mui-org/material-ui/tree/master/examples/nextjs

## Problemlösungen

Check out the FAQ answer: [My App doesn't render correctly on the server](/getting-started/faq/#my-app-doesnt-render-correctly-on-the-server).
