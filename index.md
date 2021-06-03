## Project 3

``` markdown
Goals and Outcomes

  - Gain experience interpreting functional descriptions and specifications to complete an assignment
  - Gain experience breaking a project into manageable components
  - Gain experience writing and executing non-web server Node.js JavaScript code using VSCode
  - Practice creating and using code modules
  - Practice refactoring using modern JavaScript syntax
  - Gain experience writing and executing web server Node.js JavaScript code using VSCode
  - Gain experience using Fastify with the GET verb, routes, and query parameters
  - Gain experience loading a file and using as a web page

```

coinage index.html

```rouge
 `<!DOCTYPE html>
<html>
<head>
  <meta charset="utf-8">
  <title>Coinage</title>

  <style>
  </style>
</head>

<body>
    <h1>Welcome to Coinage!</h1>
    <ul>
        <li><a href="/coin?denom=25&count=3">3 x 25 coin = 75</a></li>
        <li><a href="/coins?option=1">Option 1 = 35</a></li>
        <li><a href="/coins?option=2">Option 2 = 57</a></li>
        <li><a href="/coins?option=3">Option 3 = 57 (Extra Credit)</a></li>
    </ul>
</body>
</html>`

```


p3-server.js

```rouge
  `const fs = require('fs');
const fastify = require('fastify')();
const p3 = require('./p3-module');

fastify.get('/', (request, reply) => {
    fs.readFile(__dirname + '/index.html', (err, data) => {
        if (err) {
            reply
                .code(500);
        }
        else {
            reply
                .code(200)
                .header('Content-Type', 'text/html; charset=utf-8')
                .send(data);
        }
    });
});

fastify.get('/coin', (request, reply) => {

    const coins = {denom: parseInt(request.query.denom), count: parseInt(request.query.count)};
    const coinValue = p3.coinCount(coins);
    reply
      .code(200)
      .header("Content-Type", "text/html; charset=utf-8")
      .send(`<h2>Value of ${coins.count} of ${coins.denom} is ${coinValue}</h2><br /><a href="/">Home</a>`);
})

fastify.get('/coins', (request, reply) => {
    const coins = [{denom: 25, count: 2},{denom: 1, count: 7}];
    switch (request.query.option) {
        case '1':
            coinValue = p3.coinCount({ denom: 5, count: 3 }, { denom: 10, count: 2 });
            break;
        case '2':
            coinValue = p3.coinCount(...coins);
            break;
        case '3':
            coinValue = p3.coinCount(coins);
            break;
        default:
            coinValue = 0;
            break;
    }
    reply
      .code(200)
      .header("Content-Type", "text/html; charset=utf-8")
      .send(`<h2>Option ${request.query.option} value is ${coinValue}</h2><br /><a href="/">Home</a>`);

})

const listenIP = 'localhost';
const listenPort = 8080;

fastify.listen(listenPort, listenIP, (err, address) => {
    if (err) {
       console.log(err);
       process.exit(1); 
    }
    console.log(`Server listening on ${address}`);
});
`
```

p3-module.js

```rouge
  `/*
    CIT 281 Project 3
    Name: Emily Deng
*/

function validDenomination(coin) {
    const validCoins = [1, 5, 10, 25, 50, 100];
    return (validCoins.indexOf(coin) !== -1);
}

function valueFromCoinObject(obj) {
    //denom: Coin denomination, must be either 1, 5, 10, 25, 50, or 100
    //count: The number of the specified coin

    const denom = obj.denom;
    const count = obj.count;
    return denom*count;

}

function valueFromArray(arr) {
    const reducer = (total, current) => {
        if (Array.isArray(current)) {
            return total + current.reduce(reducer, total);
        }
        return total + valueFromCoinObject(current);
    };
    return arr.reduce(reducer, 0);
}

exports.coinCount = (...coinage) => {
    return valueFromArray(coinage);
}

/*
console.log("{}", coinCount({denom: 5, count: 3}));
console.log("{}s", coinCount({denom: 5, count: 3},{denom: 10, count: 2}));
const coins = [{denom: 25, count: 2},{denom: 1, count: 7}];
console.log("...[{}]", coinCount(...coins));
console.log("[{}]", coinCount(coins));  // Extra credit
*/`

```
