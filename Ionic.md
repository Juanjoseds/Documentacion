# Ionic Framework

Este documento tiene como objetivo almacenar comandos y código de interés para funciones específicas para Ionic.

## Antes de empezar

Comprobamos que node.js está instalado en su versión mas reciente.

En **PowerShell** en modo **administrador**:

```Powershell
Set-ExecutionPolicy Unrestricted -Scope CurrentUser -Force
npm install -g npm-windows-upgrade
npm-windows-upgrade
```

Comprobamos que git está en su versión mas reciente:

```terminal
git update-git-for-windows
```

Instalar **angular** en su versión mas reciente:

```terminal
npm install -g @angular/cli
```

Instalar las dependencias de **Ionic** previamente:

```terminal
npm install -g @ionic/cli native-run cordova-res
```

Deberemos tener instalado **Android Studio** en su versión más reciente.

## Inicio del proyecto

Al comenzar es importante asegurarse de que las dependencias estén actualizadas a las versiones más recientes.

Creamos el nuevo proyecto:

```terminal
ionic start miCarrito sidemenu
```

Dentro del directorio de la aplicación, comprobamos si hay dependencias desactualizadas y si fuese el caso, las actualizamos:

```terminal
npm outdated
npx npm-check-updates -u
npm install
ng update
```

## Generar vistas o componentes

Para generas vistas se puede usar *page* y para crear componentes empleamos *component*:

```terminal
ionic generate page contact
ionic generate component contact/form
```

## Git

Para ignorar archivos al subirlo al git (como enviroments) deberemos ejecutar el siguiente comando:

```terminal
git rm --cached -r ./src/environments/
```

## Bootstrap

Para añadir bootstrap al proyecto y que funcione correctamente deberemos ejecutar:

```terminal
npm install bootstrap
```

Posteriormente deberemos añadir en *angular.json* que se encuentra en el directorio raíz la siguiente línea:

```json
"styles": [
            {...},
            {
              "input": "node_modules/bootstrap/dist/css/bootstrap.min.css"
            }
          ],
```

## Firebase

Para instalar firebase en el proyecto:

```terminal
npm install firebase @angular/fire --save
```

En `/src/enviroments/enviroment.ts` es importante que añadas la siguiente función que deberás obtener de firebase al hacer click en crear un proyecto **web**:

```typescript
config: {
  apiKey: "XXX",
  authDomain: "XXX",
  databaseURL: "XXX",
  projectId: "XXX",
  storageBucket: "XXX",
  messagingSenderId: "XXX",
  appId: "XXX"
}
```

Para que funcione correctamente y totalmente integrado, deberemos añadir en **app.module.ts** en la sección de imports con sus correspondientes importaciones:

```typescript
AngularFireAuthModule,
AngularFireModule.initializeApp(environment.config)
```

Algunas funciones interesantes para llevar a cabo con firebase:

### Ejemplos con firebase

- Realizar un login del usuario

```typescript
public loginEmailAndPassword(email, password):Observable <any>{
    return fromPromise(this.fauth.signInWithEmailAndPassword(email, password).then(() => {
            this.router.navigateByUrl('/Inicio');
        }).catch(function (error) {
            console.log('Auth Error', error);
        }));
}
```

- Realizar un logout del usuario

```typescript
public logout(){
    auth().signOut().then(function() {
        console.log('Logout sucessfully');
    }).catch(function(error) {
        console.log('Error in logout');
    });
}
```

- Para estar subscrito al login del usuario y recuperar siempre los datos de la sesión del usuario si se mantiene activa

En **firebase.service.ts**:

```typescript
constructor(private fauth: AngularFireAuth, private afs: AngularFirestore, private router: Router) {
        this.loginUser();
}

 public loginUser(){
    this.login = this.fauth.authState.subscribe(
        (data) => {
            // No realizamos acciones si no hay usuario conectado (pero seguimos escuchando por si se loguea)
            if(data !== null){
                console.log('logged in:', data);
                this.email = data.email;
                //Acciones (como cargar items de firebase) cuando el login es satisfactorio
            }
        },
        (error) => console.log('error', error),
        () => console.log('auth complete')
    );
}
```

Como se puede comprobar, `data.email` obtiene el correo electronico del usuario.

- Para obtener un **colección**

```typescript
getUserItems(): Observable<any> {
    return this.afs.collection('User/'+auth().currentUser.email+'/items').snapshotChanges()
        .pipe(
            map(actions =>
            actions.map(a => {
                const data = a.payload.doc.data() as any;
                const id = a.payload.doc.id;
                return {id, ...data};
            }))
        )
    }
```

Suponiendo que tenemos una colección ```User``` con documentos cuya ID es el correo del usuario logueado, que tiene una colección de ```items```. En este caso en ```items``` los documentos son mapas.

- Cambiar un valor de un **campo** en un mapa

En el siguiente ejemplo, se cambia el valor ```bought``` de un mapa.

```typescript
public async onChecked(item) {
    const namebought = item.name + '.bought';
    await this.afs.doc('User/' + auth().currentUser.email + '/items/' + item.name).update({
        [namebought]: !item.bought
    })
    console.log(item.name + ' Bought', !item.bought);
}
```

>_Es importante recordar que para modificar un campo tienes que asociarlo con ```item.bought```. Para poder usar el nombre que nos proporciona la variable, concatenamos en la variable ```namebought``` el nombre del item con el campo a cambiar. Para poder usar esta variable como variable y no como textual hay que usarla mediante [] corchetes._

## Enrutamiento

A partir de `Angular 7.2` y `Ionic 4` los parametros por enrutamiento se envían mediante ```extras state```. Esto permite un alto nivel de seguridad ya que el parametro/s a pasar al componente destino no será visible por el usuario en la query, permitiendo así pasar una gran cantidad de datos si se necesitase.

En ```routing.module``` preparamos el ```path``` al que se va a redirigir:

```typescript
{
    path: '/edit/:id',
    component: EditComponent
}
```

En el método que va a enrutar con el componente destino:

```typescript
const navigationExtras: NavigationExtras = { state: {name: 'Ejemplo'}};
this.router.navigate(['/edit'], navigationExtras);
```

En el componente destino declaramos lo siguiente en el `ngOnInit()`

```typescript
ngOnInit() {
    this.activatedRoute.queryParams.subscribe(params => {
        if(this.router.getCurrentNavigation().extras.state) {
        this.product = this.router.getCurrentNavigation().extras.state.name;
        }
    })
}
```

Esto recuperará de forma segura en este caso la variable `name`, sin estar presente en la query del navegador.

## Build

Para compilar la aplicación como `.apk` es necesario hacer unas configuraciones previas

- Instalar [Gradle](https://gradle.org/install/)

Nos descargamos el `.zip` y lo descomprimimos en `c:\Gradle`.

En Variables del sistema deberemos añadir en `path`:

```file
C:\Gradle\gradle-6.4.1\bin
```

- Instalar [JDK 8](https://www.oracle.com/java/technologies/javase/javase-jdk8-downloads.html)

Tras descargar e instalar JDK, deberemos configurar las variables de entorno:

En variables para el usuario:

```variables
JAVA_HOME = D:\Archivos de programa\Java\jdk1.8.0_251
PATH += D:\Archivos de programa\Java\jdk1.8.0_251\bin
```

Hay que tener en cuenta que en el path se añade una línea más.

- Instalar [Android Studio](https://developer.android.com/studio)

Esto instalará un IDE y las SDK Tools de Android para el funcionamiento, posteriormente deberemos configurar las variables de entorno:

```variables
ANDROID_HOME = C:\Users\Usuario\AppData\Local\Android\Sdk
ANDROID_SDK_ROOT = D:\Archivos de programa\Android
```

En el `path` deberemos añadir las siguientes líneas:

```variables
C:\Users\Usuario\AppData\Local\Android\Sdk\platform-tools
C:\Users\Usuario\AppData\Local\Android\Sdk\tools
```

- Aceptar las licencias

Antes de nada hay que aceptar las licencias de Android SDK.

Ejecutamos en el `cmd` el siguiente comando:

```terminal
%ANDROID_HOME%/tools/bin/sdkmanager --licenses
```

y aceptamos con `y` todas las licencias.

¡Y ya podemos ejecutar el comando para generar el `.apk`

```terminal
ionic cordova build --release android
```
