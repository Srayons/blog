# admins

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

const createURL = (emoji1, emoji2) => {
let u1 = emoji1[0].map(c => "u" + c.toString(16)).join("-");
let u2 = emoji2[0].map(c => "u" + c.toString(16)).join("-");
let url = "./?emoji=" + `${codePointsToEmoji(emoji1[0])}+${codePointsToEmoji(emoji2[0])}`;
history.replaceState(undefined, "", url);
return `${API}${emoji1[2]}/${u1}/${u1}_${u2}.png`;
};

let emoji1 = [

]