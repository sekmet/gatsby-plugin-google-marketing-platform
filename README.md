# Google Marketing Platform for Gatsby

Easily add these GMP products in a cohesive way to your Gatsby site:
* Google Tag Manager
* Google Analytics
* Google Optimize

The installation is based on Google's recommendations and best practices.

### What This Does
This provides the ability to configure the Google Marketing Platform base tools, including Google Analytics, Google Optimize, and Google Tag Manager.

This plugin also allows the ability to preset `dataLayer` variables. This is useful in cases where you need GA, configured within GTM, to fire with particular variables on the initial pageview, such as dynamically setting your Google Analytics Property ID, instead of requiring to maintain it in multiple locations.

### What this does NOT do
This will not provide any functionality around specific tracking or conversion tags. **These responsibilities should be handled in Google Tag Manager**.


## Getting Started

### Install

```
yarn add gatsby-plugin-google-marketing-platform
```
or
```
npm install gatsby-plugin-google-marketing-platform
```

### Basic Setup
```javascript
// In your gatsby-config.js
plugins: [
  {
    resolve: 'gatsby-plugin-google-marketing-platform',
    options: {
      dataLayer: {
        gaPropertyId: '[Google Analytics ID]',
      },
      tagmanager: {
        id: '[Google Tag Manager ID]'
      },
      analytics: {
        id: '[Google Analytics ID]'
      }
    },
  }
]
```

## Configuration

| Key                                       | Type    | Default  | Description                                  |
| ----------------------------------------- | ------- | ---------| -------------------------------------------- |
| [dataLayer](#user-content-data-layer)     | object  | `{}`     | [See Below](#user-content-data-layer)        |
| [tagmanager](#user-content-tag-manager)   | object  | `{}`     | [See Below](#user-content-tag-manager)       |
| [analytics](#user-content-analytics)      | object  | `{}`     | [See Below](#user-content-analytics)         |
| [optimize](#user-content-optimize)        | object  | `{}`     | [See Below](#user-content-optimize)          |
| includeInDevelopment                      | boolean | `false`  | Scripts will be bundlded in development mode |

### Data Layer
The `dataLayer` key is a freeform object that will allow you to pass data through to the globally available `dataLayer` array that Google Tag Manager utilizes to store and watch data. There is no set functionality for this, but you can see below for some usage examples.

#### Examples
| Key                       | Type    | Default  |
| ------------------------- | ------- | ---------|
| gaPropertyId              | string  | None     |

### Tag Manager
Configuration specifics for Google Tag Manager.

| Key                | Type   | Default  | Description                                         |
| ------------------ | ------ | -------- | --------------------------------------------------- |
| id                 | string | None     | Google Tag Manager ID                               |
| params             | object | `{}`     | Additional parameters to be added on GTM script URL |

### Analytics
Configuration specifics for Google Analytics.

| Key                | Type   | Default  | Description                                         |
| ------------------ | ------ | -------- | --------------------------------------------------- |
| id                 | string | None     | Google Analytics ID                                 |
| config             | object | `{}`     | [Custom GA configuration][1]                        |

### Optimize
Configuration specifics for Google Optimize.

| Key                | Type   | Default  | Description                                                      |
| ------------------ | ------ | -------- | ---------------------------------------------------------------- |
| id                 | string | None     | Google Optimize ID                                               |
| timeout            | number | None     | Number of milliseconds Optimize waits to try to load - [Docs][2] |
| activateOn         | string | None     | [Activation Method](#user-content-activation-method)             |

#### Activation Method
By default, this plugin doesn't push any activation events to Google Tag Manager for single page apps. The `activateOn` property allows configuration to provide different methods of activation.

Activation events refers to Google's way of handling re-activating Optimize on DOM change.

https://support.google.com/optimize/answer/7008840?hl=en

##### Options
* `observer`: Utilizes [MutationObserver][3] to watch for DOM changes

## Examples

### Google Tag Manager with Google Analytics and Google Optimize

```javascript
// In your gatsby-config.js
plugins: [
  {
    resolve: 'gatsby-plugin-google-marketing-platform',
    options: {
      dataLayer: {
        // Preset dataLayer values
        gaPropertyId: '[Google Analytics ID]',
      },
      tagmanager: {
        id: '[Google Tag Manager ID]',
        params: {
          // GTM URL Parameters
          // Ex: https://www.googletagmanager.com/gtm.js?id=[ID]&gtm_cookies_win=x
          gtm_cookies_win: 'x'
        }
      },
      analytics: {
        id: '[Google Analytics ID]',
      },
      optimize: {
        id: '[Google Optimize ID]',
      }
    }
  }
]
```
## What That Will Add
Note: the below will be minified along with the rest of the code and not include comments
```
...
<head>
  ...

  <!-- Set up dataLayer with data if provided -->
  <script>
  window.dataLayer = window.dataLayer || [{"gaPropertyId":"TEST-1234"}];
  </script>

  <!-- Initialized GTM via gtag -->
  <script>
  function gtag(){dataLayer.push(arguments);}
  gtag('js', new Date());
  </script>

  <!-- Google Optimize async hide class -->
  <style>.async-hide { opacity: 0 !important }</style>

  <!-- Google Optimize async hide script -->
  <script>
  (function(a,s,y,n,c,h,i,d,e){s.className+=' '+y;h.start=1*new Date;
  h.end=i=function(){s.className=s.className.replace(RegExp(' ?'+y),'')};
  (a[n]=a[n]||[]).hide=h;setTimeout(function(){i();h.end=null},c);h.timeout=c;
  })(window,document.documentElement,'async-hide','dataLayer',500,{'ASDF-4321':true});
  </script>

  <!-- Configure GTM given GA and GO IDs -->
  <script>
  gtag('config', 'TEST-1234', {"optimize_id":"ASDF-4321"});
  </script>

  <!-- Configure and initialize GTM -->
  <script>
  (function(w,d,s,l,i){w[l]=w[l]||[];w[l].push({'gtm.start':
  new Date().getTime(),event:'gtm.js'});var f=d.getElementsByTagName(s)[0],
  j=d.createElement(s),dl=l!='dataLayer'?'&l='+l:'';j.async=true;j.src=
  'https://www.googletagmanager.com/gtm.js?id='+i+dl+'&gtm_cookies_win=x';f.parentNode.insertBefore(j,f);
  })(window,document,'script','dataLayer', 'TESTASDF-12345678');
  </script>
  ...
</head>
...
<body>
  <!-- GTM noscript for when JS isn't available -->
  <noscript><iframe src="https://www.googletagmanager.com/ns.html?id=TESTASDF-12345678&gtm_cookies_win=x" height="0" width="0" style="display: none; visibility: hidden ></iframe></noscript>
```

[1]: https://developers.google.com/analytics/devguides/collection/analyticsjs
[2]: https://developers.google.com/optimize#changing_the_timeout
[3]: https://developer.mozilla.org/en-US/docs/Web/API/MutationObserver