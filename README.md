## Example app

Example application demonstrating `@ngxs/store` regression for configuration `strictContentSecurityPolicy`.

### Reproducing the issue

Under [app.module.ts](https://github.com/aaronfay/ngxs-sample-app/blob/stable/src/app/app.module.ts#L29) we can see that `ngxs` is configured [according to the documentation](https://www.ngxs.io/advanced/options):

```js
    NgxsModule.forRoot(
      [],
      {
        developmentMode: false,
        compatibility: {
          strictContentSecurityPolicy: true,
        },
      }
    ),
```

After running `npm run build` in production mode, we can see there are still instances of unsafe JS:

```
$ npm run build
> angular.io-example@0.0.0 build
> ng build --configuration production
...

$ grep 'new Func' ./dist/**/*.js

./dist/main.4db11c14788aa303.js:  const fn = new Function('store', 'return ' + expr + ';');
./dist/main.4db11c14788aa303.js:    return new Function(...args);
./dist/main.4db11c14788aa303.js:    return new Function(...args);
./dist/main.4db11c14788aa303.js:  // To completely mimic the behavior of calling "new Function", two more
./dist/main.4db11c14788aa303.js:  // return new Function(...args.map(a => trustedScriptFromString(a)));
```

In fact, toggling the `strictContentSecurityPolicy` seems to have no effect at all.