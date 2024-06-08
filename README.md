# TypeScript App connected to TypeScript UI Library

[![License Status][license-image]][license-url]

The project show-cases the [relationship between applications and libraries](#connecting-application-and-library) built with TypeScript. In addition, also the [consumption of npm packages](#consuming-npm-packages-in-your-application) is demonstrated. This repository has the following structure:

```sh
<root>
├── com.myorg.myapp   // the TypeScript application
└── com.myorg.mylib   // the TypeScript UI library
```

The projects have been scaffolded using the [Easy UI5 (`easy-ui5`) generator](https://github.com/SAP/generator-easy-ui5/). The community generator comes with several templates for creating UI5 applications or libraries in JavaScript or TypeScript and also other things around. The templates are maintained by the UI5 community in the GitHub organizaton [`ui5-community`](https://github.com/ui5-community/).

In the following, the steps to create this repository are explained.

## Connecting application and library

### Step 1: Create the application

The application can be created with the following command:

```sh
yo easy-ui5 ts-app
```

Just use the defaults (without initializing the Git repo - for sure you can do this if you want).

### Step 2: Create the UI library

Next to the application, just create the UI library with the following command (it should be side-by-side with the application):

```sh
yo easy-ui5 ts-library
```

Just use the defaults (without initializing the Git repo - for sure you can do this if you want).

### Step 3: Use the UI library in the application

To connect the UI library with the application, switch into the `com.myorg.myapp` folder and run the following command:

```sh
npm install com.myorg.mylib@../com.myorg.mylib
```

This installs the local file dependency to the library in the application. The string after the `@` describes the local path of the UI library. For sure, this could be also omitted to consume the library from e.g. `npmjs.org` or via `npm link`.

But that's all needed to make the resources from the UI libarary available in your application, so you can start to use it!

To complete the connection to the UI library, we need to ensure that code completion works. For that we need to add the library to the `types` of the application. Therefore, open the `tsconfig.json` in `com.myorg.myapp`:

```json
{
	"compilerOptions": {
	  [...]
		"types": ["@openui5/types", "@types/qunit", "com.myorg.mylib"],
	  [...]
	}
}
```

> :sparkles: **Tip**
> When the code completion for the UI library is not working, please make sure that the UI library has been built once. You can do so by running the command `ui5 build` or `npm run build` in the library project.

### Step 4: Using the Control of the library in the application

Open the `view/Main.view.xml` in the application and add the `xmlns` to the XMLView:

```xml
<mvc:View
	[...]
	xmlns:lib="com.myorg.mylib"
  >
```

Now the XML namespace `lib` can be used to include the Controls from the library. The library scaffolded by Easy UI5 typically contains an `Example` control.

```xml
  <lib:Example text="Hello World" />
```

If you now save, the `Example` control should be visible in your application.

> :sparkles: **Tip**
> If the XMLView contains the `IllustratedMessage` just remove it and put the `Example` control directly into the content aggregation of the `Page`.

### Step 5: Distribute the library with the application

To distribute the library with the application, you need to include the library into the applications' build. You do so by adding the following configuration into the `ui5.yaml` of your application:

```yaml
builder:
  settings:
    includeDependency:
      - "com.myorg.mylib"
```

If you now run the applications' build, you will also find the resources of the library in the `dist` folder:

```sh
npm run build
```

Now open `com.myorg.myapp/dist/resources/com/myorg/mylib`. Here the built variant of the library is located.

### Step 6: Celebrate yourself and enjoy the dev experience

If you are now changing either code in your application or in your library, the application refreshes automatically. This is managed by the [`ui5-middleware-livereload`](https://www.npmjs.com/package/ui5-middleware-livereload) which is automatically configured in your projects when using the Easy UI5 templates to create them.

## Consuming npm packages in your application

To consume any npm packages (which is also made for the browser usage) in your application, you need to install the dependency to the Ui5 tooling extension: [`ui5-tooling-modules`](https://www.npmjs.com/package/ui5-tooling-modules):

```sh
npm install ui5-tooling-modules --save-dev
```

Afterwards, the tooling extension needs to be configured in the `ui5.yaml`:

```yaml
builder:
  customTasks:
    - name: ui5-tooling-modules-task
      afterTask: replaceVersion
server:
  customMiddleware:
    - name: ui5-tooling-modules-middleware
      afterMiddleware: compression
```

This is the minimal configuration for `ui5-tooling-modules`.

### Consuming the npm package in JS/TS

With that you can now simply require modules from npm packages, e.g.:

```js
sap.ui.define(["moment"], function(moment) {
  [...];
});
```

or:

```ts
import moment from "moment";
```

This now just works like in any other framework.

> :sparkles: **Tip**
> If you want to include assets from npm packages, use the following syntax:
> ```js
> sap.ui.require(["sap/ui/dom/includeStylesheet"], function (includeStylesheet) {
>    includeStylesheet(sap.ui.require.toUrl("npm-package/dist/style.css"));
> });
> ```
> This ensures that the assets are loaded via the `ui5-tooling-modules`.

### Including the npm package modules into the build output

The `ui5-tooling-modules` comes with a nice feature to include the modules into the namespace of your application or library. Therefore, you can use the option `addToNamespace`:

```yaml
builder:
  customTasks:
    - name: ui5-tooling-modules-task
      afterTask: replaceVersion
      configuration:
        addToNamespace: true
```

> :sparkles: **Tip**
> With this option, the included modules are copied into the namespace of the libarry into the `thirdparty` folder. All `imports` or `requires` are rewritten to point to the `thirdparty` folder to make those dependencies private exclusive! Also the exports of the modules not exposed globally anymore.

## Support

Please use the GitHub bug tracking system to post questions, bug reports or to create pull requests.

## Contributing

We welcome any type of contribution (code contributions, pull requests, issues) to this generator equally.

## License

This project is licensed under the Apache Software License, version 2.0 except as noted otherwise in the LICENSE file.

[license-image]: https://img.shields.io/github/license/petermuessig/ui5con2024-byods.svg
[license-url]: https://github.com/petermuessig/ui5con2024-byods/blob/main/LICENSE
