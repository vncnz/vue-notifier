# vue-notifier

Vuejs 2.X component to show sequential notifications via bus events. Notification component is /src/Notifier.vue and in this example project /src/eventBus.js is the bus Notifier is listening on.

Notifier accepts events called `notify:error`, `notify:warning`, `notify:info` and `notify:success`, event object can be a string or a complex object as following:
```
{
    text: String    // required, the text of notification
    key: Any        // optional, can be used for notification replacement,
    timeout: Number // optional, default 5000 ms if type is warning and 3000 ms if type is success or info
}
```

See the [DEMO](https://codepen.io/vncnz/pen/yGvbJE).

## Project setup
```
npm install
```

### Compiles and hot-reloads for development
```
npm run serve
```

### Compiles and minifies for production
```
npm run build
```

### Run your tests
```
npm run test
```

### Lints and fixes files
```
npm run lint
```

### Customize configuration
See [Configuration Reference](https://cli.vuejs.org/config/).
