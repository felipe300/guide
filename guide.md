# Basic Guide

In the folder where you want to create the project, create a terminal and do this:

1.- Create folder and enter into it:
```bash
mkdir YOUR_PROJECT_NAME && cd $_
```

2.- npm init and change your `package.json`
```bash
npm init -y
```

```json
{
  "name": "YOUR_PROJECT_NAME",
  "version": "1.0.0",
  "description": "",
  "type": "module",
  "main": "index.js",
  "scripts": {
	 "test": "echo \"Error: no test specified\" && exit 1"
  },
  "keywords": ["nodejs", "express", "express-handlebars", "Bootstrap"],
  "author": "Felipe Gutiérrez",
  "license": "ISC",
```

3.- Create basic project structure
```bash
touch index.js README.md .gitignore .env .env.example
mkdir src
mkdir public
mkdir src/config src/models src/controllers src/routes src/middlewares
touch src/app.js
```

- create a `public` folder and add a `favicon` image
```bash
mkdir public
```

4.- create git
```bash
git init
```

and add to `.gitignore`

```
node_modules
.env
package-lock.json
```

5.- add libraries

```bash
npm install express pg pg-hstore sequelize cors dotenv express-handlebars jsonwebtoken morgan bcrypt -E
```

add "development" libraries

```bash
npm i nodemon -D -E
```

in your `package.json` update "scripts":

```json
"scripts": {
	"test": "echo \"Error: no test specified\" && exit 1",
	"dev": "export NODE_ENV=development && nodemon index.js",
	"start": "node index.js",
	"prod": "export NODE_ENV=production && npm run start"
},
```

6.- in your `.env` and `.env.example` add

```js
POSTGRES_DB="<YOUR_CREDENTIALS>"
POSTGRES_USER="<YOUR_CREDENTIALS>"
POSTGRES_PASS="<YOUR_CREDENTIALS>"
POSTGRES_HOST="<YOUR_CREDENTIALS>"
PORT="<YOUR_CREDENTIALS>"
SECRET_TOKEN="<YOUR_CREDENTIALS>"
```

**In your `.env`, DO NOT USE `""`**

7.- **OPTIONAL**, add linter
```bash
npm init @eslint/config
```

**Chose: import, node, no typescript, force style, standard, install dependencies, save as json format**

In your `.eslintrc.json` add

```json
"rules":{
	"indent": [
		2,
		"tab"
	],
	"no-tabs": 0
}
```

8.- update readme

9.- git add and commit `chore: initial commit`

10.- create db in `Postgres` or `PgAdmin` and change your environment variables in `.env`. Now, create a new file for your db connection
```bash
touch ./src/config/db.config.js
```

```js
import Sequelize from 'sequelize'
import * as path from 'path'
import { fileURLToPath } from 'url'
import * as dotenv from 'dotenv'
import pg from 'pg'

const __dirname = path.dirname(fileURLToPath(import.meta.url))
let dialectOptions = null

if (process.env.NODE_ENV === 'development') {
	dotenv.config({
		path: path.join(__dirname, '../../.env')
	})
} else {
	dotenv.config({
		path: path.join(__dirname, `../../.env.${process.env.NODE_ENV}`)
	})
	dialectOptions = {
		ssl: {
			require: true,
			rejectUnauthorized: false
		}
	}
}

const sequelize = new Sequelize(
	process.env.POSTGRES_DB,
	process.env.POSTGRES_USER,
	process.env.POSTGRES_PASS,
	{
		host: process.env.POSTGRES_HOST,
		port: process.env.DB_PORT || 5432,
		dialect: 'postgres',
		pool: {
			max: 5,
			min: 0,
			acquire: 20000,
			idle: 5000
		},
		dialectOptions,
		dialectModule: pg
	}
)

export default sequelize
```

11.- update `index.js`
```js
import sequelize from './src/config/db.config.js'
import app from './src/app.js'
import 'dotenv/config'

const PORT = process.env.PORT || 5000

const main = async () => {
	try {
		await sequelize.authenticate()
		await sequelize.sync({ force: true, alter: true })
		app.listen(PORT, () =>
			console.log(`Server Listening on port => ${PORT}!🔥🔥🔥`)
		)
	} catch (err) {
		console.log(`Something went wrong, Error => ${err.message}`)
	}
}

main()
```

**KEEP `{ force: false }` TO MAINTAIN DB DATA, AND `{ force: true }` TO DROP DB AND TEST**

13.- create `express-handlebars` folder structure
```bash
mkdir src/views
mkdir src/views/layouts src/views/partials

touch src/views/home.handlebars src/views/signup.handlebars src/views/login.handlebars src/views/layouts/main.handlebars src/views/partials/navbar.handlebars
```

in `main.handlebars` add:

```html
<!DOCTYPE html>
<html lang="en">

	<head>
		<meta charset="UTF-8">
		<meta name="viewport" content="width=device-width, initial-scale=1.0">
		<link rel="icon" type="image/x-icon" href="/public/favicon.ico">
		<title>CHANGE_ME</title>
		{{!-- BOOTSTRAP --}}
		<link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css" rel="stylesheet"
			integrity="sha384-9ndCyUaIbzAi2FUVXJi0CjmCapSmO7SnpJef0486qhLnuZ2cdeRhO02iuK6FUUVM" crossorigin="anonymous">
		<script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/js/bootstrap.bundle.min.js"
			integrity="sha384-geWF76RCwLtnZ8qwWowPQNguL3RmwHVBC9FhGdlKrxdiJJigb/j/68SIy3Te4Bkz"
			crossorigin="anonymous"></script>
	</head>

	<body>
		<main>
			{{> navbar}}
			{{{body}}}
		</main>
	</body>

</html>
```

**HERE, YOU ADDED BOOTSTRAP THROUGH CDN CHANGE ALSO THE TITLE**

in `navbar.handlebars` add:

```html
<nav class="navbar navbar-expand-lg bg-body-tertiary">
	<div class="container-fluid">
		<a class="navbar-brand" href="#">Navbar</a>
		<button class="navbar-toggler" type="button" data-bs-toggle="collapse" data-bs-target="#navbarNav"
			aria-controls="navbarNav" aria-expanded="false" aria-label="Toggle navigation">
			<span class="navbar-toggler-icon"></span>
		</button>
		<div class="collapse navbar-collapse" id="navbarNav">
			<ul class="navbar-nav">
				<li class="nav-item">
					<a class="nav-link active" aria-current="page" id="home" href="/">Home</a>
				</li>
				<li class="nav-item">
					<a class="nav-link" id="products" href="/products">Products</a>
				</li>
				<li class="nav-item">
					<a class="nav-link" id="profile" href="/profile">Profile</a>
				</li>
				<li class="nav-item">
					<a class="nav-link" id="login" href="/login">Login</a>
				</li>
				<li class="nav-item">
					<a class="nav-link" id="logout" href="/logout">Logout</a>
				</li>
			</ul>
		</div>
	</div>
</nav>
```

**HERE, CHANGE THE LINKS ACCORDING YOUR PROJECT**

in `home.handlebars` add:

```html
<h1>home</h1>
```

in `signup.handlebars` add:

```html
<header>
	<div>
		<h1 class="text-center">SIGNUP</h1>
		<a href="/home" class="d-flex justify-content-center ">Go home</a>
		<a href="/login" class="d-flex justify-content-center ">Go to login</a>
	</div>
</header>

<form>
	<h2>Sign up</h2>
	<div>
		<label for="username">Nombre</label>
		<input type="text" name="username" required />
		<div class="username error"></div>
	</div>

	<div>
		<label for="email">Correo</label>
		<input type="email" name="email" required />
		<div class="email error"></div>
	</div>

	<div>
		<label for="password">Contraseña</label>
		<input type="password" name="password" required />
		<div class="password error"></div>
	</div>

	<button>Registrar</button>
</form>

<script>
	const form = document.querySelector('form')
	const emailError = document.querySelector('.email .error')
	const passwordError = document.querySelector('.password .error')

	form.addEventListener('submit', async (e) => {
		e.preventDefault()

		const username = form.username.value
		const email = form.email.value
		const password = form.password.value

		try {
			const res = await fetch('/api/v1/users/signup', {
				method: 'POST',
				body: JSON.stringify({
					username,
					email,
					password,
				}),
				headers: {
					'Content-Type': 'application/json'
				}
			})

			const result = await res.json()

			if (result.code === 201) {
				location.href = '/login'
			}

		} catch (err) {
			console.log(err)
		}
	})
</script>
```

in `login.handlebars` add:

```html
<header>
	<div>
		<h1 class="text-center">Login</h1>
		<a href="/home" class="d-flex justify-content-center ">Go home</a>
		<a href="/signup" class="d-flex justify-content-center ">Go to signup</a>
	</div>
</header>

<form>
	<h2>Log in</h2>
	<div>
		<label for="email">Correo</label>
		<input type="email" name="email" required />
		<div class="email error"></div>
	</div>

	<div>
		<label for="password">Contraseña</label>
		<input type="password" name="password" required />
		<div class="password error"></div>
	</div>

	<button>Ingresar</button>
</form>

<script>
	const form = document.querySelector('form')
	const emailError = document.querySelector('.email.error')
	const passwordError = document.querySelector('.passwor.error')

	form.addEventListener('submit', async (e) => {
		e.preventDefault()

		const email = form.email.value
		const password = form.password.value

		try {
			const res = await fetch('/api/v1/users/login', {
				method: 'POST',
				body: JSON.stringify({
					email,
					password,
				}),
				headers: {
					'Content-Type': 'application/json'
				}
			})

			let result = await res.json()

			if (result.code == 200) {
				localStorage.setItem("token", result.token)
				localStorage.setItem("user", JSON.stringify(result.user))
				location.href = "/home"
			} else {
				console.log(result.message)
			}

		} catch (err) {
			console.log(err)
		}
	})
</script>
```

14.- update `app.js`

```js
import express from 'express'
import morgan from 'morgan'
import cors from 'cors'
import { create } from 'express-handlebars'

// ROUTES
// import routerUsers from './routes/users.routes.js'

import * as path from 'path'
import { fileURLToPath } from 'url'

const __dirname = path.dirname(fileURLToPath(import.meta.url))
const app = express()

// MIDDLEWARES
const hbs = create({
	partialsDir: [path.resolve(__dirname, './views/partials')]
})

app.engine('handlebars', hbs.engine)
app.set('view engine', 'handlebars')
app.set('views', path.resolve(__dirname, './views'))

app.use(express.json())
app.use(cors())
app.use(morgan('tiny'))
app.use(express.urlencoded({ extended: true }))

app.use('/public', express.static(path.join(__dirname, '../public')))

// REPLACE app.get '/' && 'home' for
// app.use('/api/v1/users', routerUsers)
app.get('/', (req, res) => {
	res.send({ message: 'ok' })
})

app.get('/home', async (req, res) => {
	res.render('home')
})

export default app
```

**TEST YOUR APP, YOU SHOULD GET**

```bash
Executing (default): SELECT 1+1 AS result
Executing (default): SELECT 1+1 AS result
Server Listening on port => 3000!🔥🔥🔥
```

in `http://localhost:3000/`, you should get a `json` message
```json
{ "message": "ok" }
```

in `http://localhost:3000/home`, you should get a `h1` tag with a `HOME` text centered

15.- git add and commit `feat: basic structure`


16.- create `User` flow
```bash
touch src/models/User.model.js src/routes/users.router.js src/controllers/users.controller.js
```

- `User.model.js`

```js
import { DataTypes } from 'sequelize'
import sequelize from '../config/db.config.js'

const User = sequelize.define(
	'users',
	{
		username: {
			type: DataTypes.STRING,
			allowNull: false,
			validate: {
				notNull: {
					msg: 'El campo no puede ser nulo'
				},
				isAlpha: {
					args: true,
					msg: 'El nombre solo puede contener letras'
				},
				len: {
					args: [3, 255],
					msg: 'El nombre tiene que ser entre 3 y 255 caracteres'
				}
			}
		},
		email: {
			type: DataTypes.STRING,
			allowNull: false,
			unique: true,
			isEmail: {
				args: true,
				msg: 'El campo tiene que ser un correo valido'
			}
		},
		password: {
			type: DataTypes.STRING,
			allowNull: false
		},
		age: {
			type: DataTypes.INTEGER,
			validate: {
				isInt: {
					args: true,
					msg: 'La edad tiene que ser un numero'
				},
				min: {
					args: 1,
					msg: 'La edad tiene que ser mayor o igual que uno'
				},
				max: {
					args: 150,
					msg: 'La edad tiene que ser real'
				}
			}
		},
		isAdmin: {
			type: DataTypes.INTEGER,
			defaultValue: 0
		}
	},
	{
		timestamps: true,
		tableName: 'users'
	}
)

export default User
```

- `index.js` 
```js
import './src/models/User.model.js'
```

- `users.router.js`

```js
import { Router } from 'express'
import { addUser, getAllUsers, getUserById, updateUserById, deleteUserById } from '../controllers/users.controller.js'

const router = Router()

router.post('/', addUser)
router.get('/', getAllUsers)
router.get('/:id', getUserById)
router.put('/:id', updateUserById)
router.delete('/:id', deleteUserById)

export default router
```

in `app.js` add
```js
// ROUTES
import userRouter from './routes/user.router.js'
```

- `users.controller.js`

```js
export const addUser = async (req, res) => {
	res.send({ message: 'create' })
}

export const getAllUsers = async (req, res) => {
	res.send({ message: 'get all' })
}

export const getUserById = async (req, res) => {
	res.send({ message: 'by id' })
}

export const updateUserById = async (req, res) => {
	res.send({ message: 'create' })
}

export const deleteUserById = async (req, res) => {
	res.send({ message: 'create' })
}
```

17.- Test yout endpoints in `postman`

18.- git add and commit

19.- `View` flow
```bash
touch src/routes/views.router.js src/controllers/views.controller.js
```

- `views.router.js`

```js
import { Router } from 'express'
import { home, login, signup } from '../controllers/views.controller.js'

const router = Router()

router.get(['/home', '/'], home)
router.get('/login', login)
router.get('/signup', signup)

export default router
```

in `app.js` add
```js
// ROUTES
import routerUsers from './routes/users.router.js'
import routerViews from './routes/views.router.js'

...

app.use('/api/v1/users', routerUsers)
app.use('/', routerViews)
```

- `users.controller.js`

```js
export const home = async (req, res) => {
	res.render('home')
}

export const signup = async (req, res) => {
	res.render('signup')
}

export const login = async (req, res) => {
	res.render('login')
}
```

20.- add `emitToken` and `verifyToken` function in `auth.moddleware.js`
```js
import jwt from 'jsonwebtoken'
import User from '../models/User.model.js'
import 'dotenv'

export const emitToken = async (req, res, next) => {
	try {
		const { email, password } = req.body
		const user = await User.findOne({
			where: { email, password },
			attributes: ['id', 'username', 'email']
		})

		if (user === null) {
			res.status(400).send({ code: 400, message: 'authentication error' })
		}

		const token = jwt.sign({
			exp: Math.floor(Date.now() / 1000) + 60 * 10,
			data: user
		}, process.env.SECRET_TOKEN)

		req.token = token
		req.user = user

		next()
	} catch (err) {
		return console.log(`Error to generate token. ${err.message}`)
	}
}

export const verifyToken = async (req, res, next) => {
	try {
		let { token } = req.query
		if (!token) {
			token = req.headers.authorization
			if (!token) {
				return res
					.status(400)
					.send({
						code: 400,
						message: 'Protected route, you have to give a token'
					})
			}

			token = token.split(' ')[1]
			if (token.length === 0) {
				throw new Error('There is no token')
			}
		}

		jwt.verify(token, process.env.SECRET_TOKEN, async (err, decoded) => {
			if (err) {
				return res.status(401).send({
					code: 401,
					message: `Must be provide a valid token! ${err.message}`
				})
			}

			const user = await User.findByPk(decoded.data.id, {
				attributes: ['id', 'username', 'email']
			})

			if (!user) {
				return res.status(400).json({
					code: 400,
					message: `User does not belongs to the system anymore! ${err.message}`
				})
			}

			req.user = user
			next()
		})
	} catch (err) {
		return res.status(401).send({
			code: 401,
			message: `Error to generate token. ${err.message}`
		})
	}
}
```

update `user.router.js`

```js
import { emitToken } from '../middlewares/auth.middleware.js'

const router = Router()

router.post('/login', emitToken, loginUser)

export default router
```

21.- create backend user crud
**CHANGE THE MODEL AND CRUD ACCORDING YOUR NEEDS**

### V1
```js
import User from '../models/User.model.js'

export const addUser = async (req, res) => {
	try {
		const { username, email, password } = req.body
		const [user, created] = await User.findOrCreate({
			where: { email },
			defaults: {
				username, email, password
			}
		})

		if (!created) {
			return res.status(400).send({
				code: 400,
				message: `User '${user.email}' already exists!`
			})
		}

		res.status(201).send({
			code: 201,
			message: 'User created successfully'
		})
	} catch (err) {
		return res.status(400).send({
			code: 400,
			message: `💩, Error to create a new User. ${err}`
		})
	}
}

export const loginUser = async (req, res) => {
	try {
		res.status(200).send({
			code: 200,
			token: req.token,
			user: req.user.toJSON()
		})
	} catch (err) {
		return res.status(400).send({
			code: 400,
			message: `💩, Error to login User. ${err.message}`
		})
	}
}

export const getAllUsers = async (req, res) => {
	res.send({ message: 'get all' })
}

export const getUserById = async (req, res) => {
	res.send({ message: 'by id' })
}

export const updateUserById = async (req, res) => {
	res.send({ message: 'create' })
}

export const deleteUserById = async (req, res) => {
	res.send({ message: 'create' })
}
```

### V2
```js
import User from '../models/User.model.js'

export const addUser = async (req, res) => {
	try {
		const { username, age, isAdmin, email, password } = req.body

		const [user, created] = await User.findOrCreate({
			where: { email },
			defaults: {
				username, age, isAdmin, email, password
			}
		})

		if (!created) {
			return res.status(400).send({
				code: 400,
				message: `User '${user.email}' already exists!`
			})
		}

		res.status(201).send({
			code: 201,
			message: 'User created successfully'
		})
	} catch (err) {
		return res.status(400).send({
			code: 400,
			message: `💩, Error to create a new User. ${err.message}`
		})
	}
}

export const getAllUsers = async (req, res) => {
	try {
		const users = await User.findAll({
			attributes: {
				exclude: ['password', 'createdAt', 'updatedAt', 'isAdmin']
			},
			order: [
				['id', 'ASC']
			]
		})

		res.status(201).send({
			code: 201,
			message: users.length === 0 ? 'There are no users' : 'Get all users successfully',
			data: users
		})
	} catch (err) {
		return res.status(400).send({
			code: 400,
			message: `💩, Error to get all users. ${err.message}`
		})
	}
}

export const getUserById = async (req, res) => {
	try {
		const { id } = req.params
		const found = await User.findByPk(id)

		if (!found) {
			return res
				.status(404)
				.send({ code: 404, message: `User with '${id}' not found!` })
		}

		res.status(201).send({
			code: 201,
			message: `Get user '${found.username}', successfully`,
			data: { username: found.username, age: found.age, email: found.email }
		})
	} catch (err) {
		return res.status(400).send({
			code: 400,
			message: `💩, Error to find user by id. ${err.message}`
		})
	}
}

export const getUserByEmail = async (req, res) => {
	try {
		const { email } = req.body
		const user = await User.findOne({
			where: {
				email
			},
			attributes: {
				exclude: ['password', 'createdAt', 'updatedAt', 'isAdmin']
			}
		})

		res.status(201).send({
			code: 201,
			message: `Get user '${user.username}', successfully`,
			data: user
		})
	} catch (err) {
		return res.status(400).send({
			code: 400,
			message: `💩, Error to get user by email. ${err.message}`
		})
	}
}

export const updateUserById = async (req, res) => {
	try {
		const { id } = req.params
		const { username, age, isAdmin, email, password } = req.body
		const found = await User.findByPk(id)

		if (!found) {
			return res
				.status(404)
				.send({ code: 404, message: `User with '${id}' not found!` })
		}

		const newUser = await found.update({
			username,
			age,
			isAdmin,
			email,
			password
		}, { where: { id } })

		console.log(newUser)

		res.status(201).send({
			code: 201,
			message: `User '${username}' updated successfully`,
			data: {
				username: newUser.username,
				age: newUser.age,
				email: newUser.email
			}
		})
	} catch (err) {
		return res.status(400).send({
			code: 400,
			message: `💩, Error to update user by id. ${err.message}`
		})
	}
}

export const deleteUserById = async (req, res) => {
	try {
		const { id } = req.params
		const found = await User.findByPk(id)

		if (!found) {
			return res
				.status(404)
				.send({ code: 404, message: `User with '${id}' not found!` })
		}

		await User.destroy({ where: { id } })

		res.status(201).send({
			code: 201,
			message: found === undefined
				? 'User do not exists'
				: `User '${found.email}' deleted successfully`
		})
	} catch (err) {
		return res.status(400).send({
			code: 400,
			message: `💩, Error to delete user by id. ${err.message}`
		})
	}
}
```

22.- add `upload.middleware.js`
```js
import * as path from "path";
import { fileURLToPath } from "url";
const __dirname = path.dirname(fileURLToPath(import.meta.url));

const uploadFiles = (req, res, next) => {
    try {
        let imagen = req.files.imagen;
        let formatosPermitidos = ["jpeg", "png", "webp", "gif", "svg"];
        let extension = `${imagen.mimetype.split("/")[1]}`;

        if (!formatosPermitidos.includes(extension)) {
            return res.status(400).json({
                code: 400,
                message: `Formato no permitido ${extension}, formatos permitidos(${formatosPermitidos.join(
                    " - "
                )})`,
            });
        }
        let nombreFoto = `${Date.now()}-img.${extension}`;
        let pathDestino = path.resolve(__dirname, "../../public/uploads/" + nombreFoto);

        imagen.mv(pathDestino, async (error) => {
            if (error) {
                console.log(error);
                return res.status(500).json({
                    code: 500,
                    message:
                        "Error al subir la imagen en proceso de creación de producto.",
                });
            }

            req.nombreImagen = nombreFoto;
            req.pathImagen = pathDestino;
            next();
        });
    } catch (error) {
        console.log(error);
        return res
            .status(500)
            .json({ code: 500, message: "Error al procesar solicitud." });
    }
};

export default uploadFiles;
```

- add to `.gitignore` `public/uploads/*`

23.- Test your app

24.- git add and commit