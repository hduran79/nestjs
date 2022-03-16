# NESTJS

Es un marco para crear aplicaciones del lado del servidor Node.js eficientes y escalables . Utiliza JavaScript moderno, está construido con TypeScript (preserva la compatibilidad con JavaScript puro) y combina elementos de OOP (Programación orientada a objetos), FP (Programación funcional) y FRP (Programación reactiva funcional).

# DOCUMENTACIÓN
[NESTJS](https://github.com/nestjs/nest)

## Install

```sh 
$ node --version
$ npm i -g @nestjs/cli
$ nest --version
$ nest --help
```

## Buenas practicas
Instalar Extensiones en VsCode
- Eslint
- Prettier
- Editor Config for vs Code
- NestJs/CLI te inicializa también un repositorio, pero lo hace creando la rama principal como **_master_**.
  - Esto puede ser un problema si queremos subir nuestro repositorio a plataformas como GitHub, ya que en ellos la rama principal es main.
  - La solución es muy sencilla, solo debes hacer commit de los archivos, y después cambiar el nombre de la rama con el siguiente comando
    - git branch -m master main

## Crear un proyecto

```sh
$ nest new your-name-project
$ cd your-name-project
$ npm run start
#  check http://localhost:3000/
```
## Estructura del proyecto

```sh
├── dist -> Proyecto compilado (Donde se encuentra todo el código que enviaremos a producción.)
├── node_modules -> Modulos o dependencias
├── src -> Código fuente (backend)
├── test
.eslintrc.js -> Herramienta de análisis de código estático para identificar patrones problemáticos encontrados en el código JavaScript.
.gitignore
.prettierrc -> Herramienta que se usa para dar formato a tu código.
.editorconfig -> Manejar el mismo estándar en los editores. (Lo que perimte es que sin importar el editor respeta la configuración de los archivos.)
nest-cli.json -> Herramienta de interfaz de línea de comandos que lo ayuda a inicializar, desarrollar y mantener sus aplicaciones Nest. 
package.json -> Dentro del cual se encuentran todos los comandos y dependencias que utiliza Nest.js
package-lock.json
README.md -> Documentar el proyecto.
tsconfig.build.json
tsconfig.json -> Indica en un proyecto que se está trabajando con TypeScript y transpile a javaScript
```

**Nota**: Crear archivo .editorconfig

```javascript
# Editor configuration, see https://editorconfig.org
root = true

[*]
charset = utf-8
indent_style = space
indent_size = 2
insert_final_newline = true
trim_trailing_whitespace = true

[*.ts]
quote_type = single

[*.md]
max_line_length = off
trim_trailing_whitespace = false
```

## PROYECTO ECOMMERCE
Un API ecommerce con:
- Entidades
  - Ordenes de compra
  - Usuarios
  - Categorías
  - Productos
  - Clientes
  - Marcas

## Controller
Son los encargados de **recibir el request** de la APP, la responsabilidad es manipular, validar, tipos de datos, permisos de usuarios sean correctos y todo esta OK se conecta a la capa del services.

Verbos HTTP
  - get: consultar
  - post: Crear
  - put: Modificar
  - delete: Borrar

Se debe definir el decorador @controller para que nestJs sepa que es un controller.
Utilizar plural para los endpoints


## Controllers
[Documentación Controller](https://progressivecoder.com/creating-your-first-nestjs-controller-learn-nestjs-series-part-2/)

Crear controller modo command
nest generate controller products
nest g controller controllers/products (crear una carpeta controller, subcarpeta products y la clase products.controller)
nest g controller controllers/products --flat (crea una carpeta controller y la clase products.controller)

```sh
Ejemplo
nest g co controllers/products --flat
nest g co controllers/categories --flat
nest g co controllers/brands --flat
nest g co controllers/users --flat
nest g co controllers/costumers --flat
nest g co controllers/orders --flat
```

## Services
Patrón inyección de dependencia (Decorador @Injectable)

El decorador @injectable permite inyectarse en los controladores, todo servicio debe tener este decorador antes de la declaración de la clase que lo implementa para poder usar la inyección de dependencias

Los servicios son una pieza esencial de las aplicaciones realizadas con el framework NestJS

Están pensados para proporcionar una capa de acceso a los datos que necesitan las aplicaciones para funcionar.

Un servicio tiene la responsabilidad de gestionar el trabajo con los datos de la aplicación, de modo que realiza las operaciones para obtener esos datos, modificarlos, etc.

Con los servicios podemos:

```sh
nest g s services/products --flat
```

Aislar la lógica de negocio en una clase aparte
Reutilizar fácilmente el código de trabajo con los datos a lo largo de varios controladores
Para construir un servicio podemos usar el CLI de Nest. Para crear la clase de un servicio lanzamos el siguiente comando:

## Manejo de errores con throw y NotFoundException
[Documentación](https://docs.nestjs.com/exception-filters)

Agregar la dependencia *NotFoundException* para el manejo de errores

Nota: HttpCode será nuestra respuesta por defecto, la cual esperamos sea positiva siempre, por ello se coloca como principal, sin embargo, con lo de las excepciones este httpCode será reemplazado por el error que hayamos lanzado.

Ejemplo:

```javascript
import { ..., NotFoundException } from '@nestjs/common';

@Injectable()
export class ProductsService {
  ...

  findOne(id: number) {
    const product = this.products.find((item) => item.id === id);
    if (!product) {
      throw new NotFoundException(`Product #${id} not found`);
    }
    return product;
  }
}
```

## Introducción a pipes: usa tu primer pipe
[Documentación # 1](https://github.com/nestjs/nest/tree/master/packages/common/pipes)
[Documentación # 2](https://docs.nestjs.com/pipes)
[Documentación # 3](https://progressivecoder.com/how-to-configure-a-custom-nestjs-pipe-from-scratch/)

Los pipes tiene dos grandes usos:
1. Transformar: Transforma los datos de entrada a la forma deseada (por ejemplo, de cadena a entero)
2. Validar información: Evalúe los datos de entrada y, si son válidos, simplemente páselos sin cambios; de lo contrario, lanza una excepción cuando los datos son incorrectos

> Nota: Los datos que viajan por la URL siempre llegan como String

Ejemplo:

```javascript
// src/controllers/products.controller.ts
import {..., ParseIntPipe} from '@nestjs/common';

@Get(':id')
get(@Param('id', ParseIntPipe) id: number) {
  return this.productsService.findOne(id);
}
```
## Crea tu propio pipe

```sh
$ nest g pipe common/parse-int
```
## Data Transfers Objects (DTO)

[Documentación](https://martinfowler.com/bliki/LocalDTO.html)

Los DTO se denominan objetos de transferencia de datos porque su único propósito es transferir datos en costosas llamadas remotas. Forman parte de la implementación de una interfaz de grano grueso que una interfaz remota necesita para el rendimiento.

Funciones de los DTOs:
* Proteger los datos
* Validar y tipar datos
* Evita hacer referencia a datos que no existan
* Readonly: Indica que el campo no podrá ser editado.

```javascript
// src/dtos/products.dtos.ts
export class CreateProductDto {
  readonly name: string;
  readonly description: string;
  readonly price: number;
  readonly stock: number;
  readonly image: string;
}

// ? indica que el parámetro es opcional
export class UpdateProductDto {
  readonly name?: string;
  readonly description?: string;
  readonly price?: number;
  readonly stock?: number;
  readonly image?: string;
}

// src/controllers/products.controller.ts
export class ProductsController {
  @Post()
  create(@Body() payload: CreateProductDto) { // 👈 Dto
    ...
  }

  @Put(':id')
  update(
    @Param('id') id: string,
    @Body() payload: UpdateProductDto  // 👈 Dto
   ) { 
   ...
  }

}

// src/services/products.service.ts
export class ProductsService {
  create(payload: CreateProductDto) { // 👈 Dto
    ...
  }

  update(id: number, payload: UpdateProductDto) { // 👈 Dto
    ...	
  }

}
```

## Validando parámetros con class-validator y mapped-types

[GitHub - typestack/class-validator: Decorator-based property validation for classes](https://github.com/typestack/class-validator)
[Introducing Mapped Types for NestJS - Trilon Consulting](https://trilon.io/blog/introducing-mapped-types-for-nestjs)

Validar que los tipos coincidan con los DTOs se deben instalar las siguientes dependencias:

```sh
$ npm install @nestjs/mapped-types class-validator class-transformer
$ npm i @nestjs/mapped-types
```
> **NOTA**: PartialType: Extiende del DTO base y coloca como opcional todos los campos, toma las mismas validaciones pero el campo es opcional.

## Evitando parámetros incorrectos

Para evitar guardar campos que no tenemos mapeados en los DTOs tenemos que configurar en el main.ts el ValidationPipes las propiedad whitelist en true y si queremos ser restrictivos, tenemos que setear forbidNonWhitelisted en true.

whitelist: Elimina los parámetros recibidos que no estén definidos en los DTO.
forbidNonWhitelisted: Informar a la API que se está enviando un atributo no válido.

```javascript
whitelist: true,
forbidNonWhitelisted: true,

```
Ejemplo:

```javascript
 // src/main.ts
app.useGlobalPipes(
  new ValidationPipe({
    whitelist: true,
    forbidNonWhitelisted: true,
  }),
);
```

# NestJS Modular

```sh
$ git clone https://github.com/platzi/nestjs-modular.git
$ cd nestjs-modular
$ npm install
```

## Encapsular lógica en módulos

* No es buena practica tener toda la configuración en el archivo app.modules
* Modularidad: Es el proceso de dividir el sistema en varios módulos capa por capa de arriba a abajo cuando se resuelve un problema complejo.
* Los módulos funcionan como una isla, es decir cada modulo tendrá sus propios controller, services.
* Un modulo tiene el decorador @Module

Comando para crear módulos:

```sh
$ nest g mo products
$ nest g mo users
```

## Interacción entre módulos
Para lograr usar el Servicio A que hace parte de Módulo A en el Módulo B debemos exportar el Servicio A y luego importar el  Módulo A en el Módulo B

Si alguna vez mientras desarrollan en NestJS y se encuentran con que a pesar de estar exportados e importados los módulos/servicios, y Nest les sigue pidiendo que los añadan, seguramente es porque ambos servicios están siendo llamados el uno al otro, en otras palabras, un círculo vicioso. Para esto Nest implementó la siguiente solución:

```javascript
import { forwardRef } from '@nestjs/common';
import { ModuloUno } from 'modules/moduloUno.module';

@Module({
  imports: [
    forwardRef(() => ModuloUno), // fordwardRef nos ayuda a que ambos servicios sean llamados recíprocamente
  ]})
```
> Nota: Recuerden utilizar esto para ambos módulos, y también para ambos servicios.

## Entendiendo la inyección de dependencias

Patrón de Inyección de dependencias:

- Es un principio de arquitectura donde nos permite desacoplar las cosas y simplemente un controlador por medio de su constructor puede decir que utiliza el servicio A o el servicio B

- Un controlador puede inyectar mas de un servicio, tantos como quiera

- Esto se logra a través del patrón singleton, lo que nos permite que una vez creada nuestra clase (servicio) la instancia de nuestro servicio se pueda utilizar para los demás controladores, sin necesidad de crear varias instancias del mismo servicio.

- Decorador @inyectable: Para que lo anterior funcione en NestJS todos nuestros controladores deben tener el decorador @inyectable que le indica a nestJSque debe manejar esto como una dependencia y cumplir con el patrón singleton

- Evitar referecnias circular.

- Un servicio solo pertenece a un Modulo

[<img src="./images/skeleton.webp" width="400"/>](./images/InyeccionDependencias.png)


## Provider useValue y useClass

**useClass** -> Internamente, nest js rescribe esta sintaxis de la siguiente forma:
```javascript
@Module({
  ...
  providers: [..., 
		{
			provide: ProducsService, // Nombre con el que haremos referencia a ella
			useClass: ProducsService // Nombre de la clase que se usara
		}
	], // Aqui por ejemplo
  ...
})
```

**useValues** -> Además de poder inyectar clases también podemos inyectar valores. Esto es util para compartir valores que globales de la aplicación y evita que estos se guarden en memoria cada vez que se requieran.

```javascript
// src/app.module.ts
...
const API_KEY = '12345634';
const API_KEY_PROD = 'PROD1212121SA';

@Module({
  imports: [UsersModule, ProductsModule],
  controllers: [AppController],
  providers: [
    AppService, // useClass nestJs lo desacopla en 2 atributos provide y useClass, de esta forma crea una sola instancia de las clases que le pida esa inyeccion, para evitar este código largo solo se envía el nombre de la clase.
    {
      provide: 'API_KEY',
      useValue: process.env.NODE_ENV === 'prod' ? API_KEY_PROD : API_KEY,
    },
  ],
})
export class AppModule {}

// src/app.controller.ts

@Controller()
export class AppController {

  @Get()
  getHello(): string { // 👈 new enpoint
    return this.appService.getHello();
  }

}

// src/app.service.ts
import { Injectable, Inject } from '@nestjs/common';

@Injectable()
export class AppService {
  constructor(@Inject('API_KEY') private apiKey: string) {} // 👈 Inject API_KEY
  getHello(): string {
    return `Hello World! ${this.apiKey}`;
  }
}

```

Al momento de ejecutar y compilar el proyecto agregamos la variable de entorno.
```sh
 $ NODE_ENV=prod npm run start:dev
```

## Variables de Entrono 
[Ver documentación](https://docs.nestjs.com/techniques/configuration)


## Provider useFactory 
[Documentation](https://docs.nestjs.com/techniques/http-module#http-module)

[Descargar proyecto](https://github.com/platzi/nestjs-modular/tree/5-step)

useFactory -> Permite crear valores dinámicamente. El valor real del provider ( sean valores, objetos, clases) será proporcionado por lo que se devuelva en una función fabricadora, aun si el valor se obtenga de forma asíncrona. La función fabricadora puede recibir parámetros.

Atributos claves del provider **useFactory**
- Es asyncrono 
- Recibe inyecciones en la misma fabrica

Cuando se utilzia **useFactory** para conexiones de base de datos.
No utilizar **useFactory** para conexiones externas como APIs.

```javascript
// src/app.module.ts
import { Module, HttpModule, HttpService } from '@nestjs/common';  // 👈 imports

@Module({
  imports: [HttpModule, UsersModule, ProductsModule],
  controllers: [AppController],
  providers: [
    imports: [HttpModule, UsersModule, ProductsModule], // 👈 add HttpModule
    ...,
    {
      provide: 'TASKS',
      useFactory: async (http: HttpService) => { // 👈 implement useFactory
        const tasks = await http
          .get('https://jsonplaceholder.typicode.com/todos')
          .toPromise();
        return tasks.data;
      },
      inject: [HttpService],
    },
  ],
})
export class AppModule {}

// src/app.service.ts
  
import { Injectable, Inject } from '@nestjs/common';

@Injectable()
export class AppService {
  constructor(
    @Inject('API_KEY') private apiKey: string,
    @Inject('TASKS') private tasks: any[], // 👈 inject TASKS
  ) {}
  getHello(): string {
    console.log(this.tasks); // 👈 print TASKS
    return `Hello World! ${this.apiKey}`;
  }
}
```

## Global Module

Un modulo global será instanciado en todos los demás módulos si que nosotros tengamos que hacerlo explícitamente.

Creando un modulo global, para ello creamos un modulo
```sh
$ nest g mo database
```
Al modulo le agregaremos el decorador global

```javaScript
// src/database/database.module.ts

import { Module, Global } from '@nestjs/common';

const API_KEY = '12345634';
const API_KEY_PROD = 'PROD1212121SA';

@Global()
@Module({
  providers: [
    {
      provide: 'API_KEY',
      useValue: process.env.NODE_ENV === 'prod' ? API_KEY_PROD : API_KEY,
    },
  ],
  exports: ['API_KEY'],
})
export class DatabaseModule {}

// src/app.module.ts
...
import { DatabaseModule } from './database/database.module';

@Module({
  imports: [
    HttpModule,
    UsersModule,
    ProductsModule,
    DatabaseModule // 👈 Use DatabaseModule like global Module
   ], 
  ...
})
export class AppModule {}

// src/users/services/users.service.ts

import { Injectable, NotFoundException, Inject } from '@nestjs/common';
...

@Injectable()
export class UsersService {
  constructor(
    private productsService: ProductsService,
    @Inject('API_KEY') private apiKey: string, // 👈 Inject API_KEY
  ) {}

}
```

## Configuración por ambientes
Crear archivos env para los distintos ambientes, agregar el siguiente package

```sh
$ npm i --save @nestjs/config
```

Configurarlos archivos **.env** que contiene el valor de las variables de entorno para el servidor de pruebas, produccion y local.

```javaScript
// .stag.env
DATABASE_NAME=my_db_stag
API_KEY=333

// .prod.env
DATABASE_NAME=my_db_prod
API_KEY=999
```

### ¿Cómo elegir dinámicamente el archivo env?
Nuestro código debe elegir automáticamente que archivo .env debe tomar según en el servidor en el que se este ejecutando. Para ello debemos crear un objeto en donde listaremos todos los ambientes que existen y los ligaremos con su archivo correspondiente.

El API se podrá consumir desde Android o cualquier aplicación Frontend como Angular, React, etc...

```javaScript
// src/enviroments.ts
export const enviroments = {
  dev: '.env',
  stag: '.stag.env',
  prod: '.prod.env',
};
```

Ahora solo debemos agregar la linea mágica para que el archivo .env correspondiente se inyecte en el código.

```javaScript
// src/app.module.ts
...

import { enviroments } from './enviroments'; // 👈

@Module({
  imports: [
    ConfigModule.forRoot({
      envFilePath: enviroments[process.env.NODE_ENV] || '.env', // 👈
      isGlobal: true,
    }),
    ...
  ],
  ...
})
export class AppModule {}
```

Llamar a las variables de entorno
Listo, ahora solo debemos preocuparnos por obtener las variables de entorno.

```javaScript
// src/app.service.ts
import { ConfigService } from '@nestjs/config'; // 👈

@Injectable()
export class AppService {
  constructor(
    @Inject('TASKS') private tasks: any[],
    private config: ConfigService,  // 👈
  ) {}
  getHello(): string {
    const apiKey = this.config.get<string>('API_KEY');  // 👈
    const name = this.config.get('DATABASE_NAME');  // 👈
    return `Hello World! ${apiKey} ${name}`;
  }
}
```
Especificar ambiente
Podemos indicar en que ambiente se esta trabajando usando las siguientes lineas.

```javaScript

NODE_ENV=prod npm run start:dev
NODE_ENV=stag npm run start:dev

Si estas usando el powershell de windos

$env:NODE_ENV="prod"; npm run start:dev
$env:NODE_ENV="stag"; npm run start:dev
$env:NODE_ENV="dev"; npm run start:dev
```

## Tipado en config
Tipar nuestro archivo de configuración le dará el poder a typescript de ayudarnos a evitar errores triviales como escribir mal el nombre de una variable o no definir una variable en los archivos .env

Tipando el archivo de configuración
Para tipar el archivo de configuración debemos hacerlo con ayuda de la funcion **registerAs** incluida en el paquete de **_'@nestjs/config'_**

```javaScript
// .env
DATABASE_NAME=my_db_prod
API_KEY=999
DATABASE_PORT=8091 //

// .stag.env
DATABASE_NAME=my_db_stag
API_KEY=333
DATABASE_PORT=8091 //

// .prod.env
DATABASE_NAME=my_db_prod
API_KEY=999
DATABASE_PORT=8091 //
```

Agregar el siguiente archivo **src/config.ts**:

```javaScript
// src/config.ts // 👈 new file
import { registerAs } from '@nestjs/config';

export default registerAs('config', () => { // 👈 export default
  return { 
    database: {
      name: process.env.DATABASE_NAME,
      port: process.env.DATABASE_PORT,
    },
    apiKey: process.env.API_KEY,
  };
});
```

En el **app.module.ts** agregar el archivo anterior
```javaScript
// src/app.module.ts
import config from './config'; // 👈

@Module({
  imports: [
    ConfigModule.forRoot({
      envFilePath: enviroments[process.env.NODE_ENV] || '.env',
      load: [config], // 👈
      isGlobal: true,
    }),
    ...
  ],
  ...
})
export class AppModule {}
```

En la clase donde se necesite agregar la variable global agregar el archivo **config**
```javaScript
// src/app.service.ts
import { ConfigType } from '@nestjs/config'; // 👈 Import ConfigType 
import config from './config'; // 👈 config file

@Injectable()
export class AppService {
  constructor(
    @Inject('TASKS') private tasks: any[],
    @Inject(config.KEY) private configService: ConfigType<typeof config>, // 👈
  ) {}
  getHello(): string {
    const apiKey = this.configService.apiKey; // 👈
    const name = this.configService.database.name; // 👈
    return `Hello World! ${apiKey} ${name}`;
  }
}
```



Documentar la API REST con Swagger.
