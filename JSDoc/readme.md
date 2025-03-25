## Installaton

```
npm install -g jsdoc
```
or
```
npm install --save-dev jsdoc
```

## to generate docs
```
jsdoc path/to/my/file.js
```

or execute via npm
```
npm run generate-docs
```
NOTE:
You need to add the run script on package.json
```
"scripts": {
    "generate-docs": "jsdoc -c /path/to/my/conf.js"
}
```
