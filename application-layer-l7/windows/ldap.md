# LDAP

Поднять LDAP сервер (на порту 1389):

```
1. install nvm
2. $ nvm install 15.12.0
3. $ nvm use v15.12.0
4. $ npm install ldapjs --save
5. $ node ldap_server.js
```

ldap\_server.js

```typescript
const ldap = require('ldapjs')
const server = ldap.createServer()
const port = 1389

server.search('', (req, res, next) => {
  // Print request attributes to the console
  console.log(req.baseObject.rdns[0].attrs.q);

  // Dummy response
  res.send({
    dn: '',
    attributes: {}
  })

  res.end()
})

server.listen(port, () => {
  console.log(`LDAP server listening at ${server.url}`)
})
```
