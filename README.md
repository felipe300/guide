# GUIA

Pasos basicos para crear un proyecto con `nodejs`, `express`, `postgres` y `express-handlebars`

TODO:

- [ ]: add: mejorar el README.md
- [ ]: add: mejorar la vista del modelo
- [ ]: add: componentizar utilizando bootstrap y handlebars, ejemplos `Card`, `List`, `Footer`, components etc.

DONE:

- [x]: DONE: added bcrypt
- [x]: DONE: added a `seeds.js` file for mock data
- [x]: DONE: update login, signup and navbar handlebars
- [x]: DONE: fix express upload file
- [x]: DONE: user basic signup
```json
{
	"username": "felipe",
	"email": "felipe300@dev.com",
	"password": "123"
}
```

### scripts

Use `npm run COMMAND`

```json
"scripts": {
	"test": "echo \"Error: no test specified\" && exit 1",
	"dev": "export NODE_ENV=development && nodemon index.js",
	"seed": "export NODE_ENV=development && nodemon seeds.js",
	"start": "node index.js",
	"prod": "export NODE_ENV=production && npm run start"
}
```
