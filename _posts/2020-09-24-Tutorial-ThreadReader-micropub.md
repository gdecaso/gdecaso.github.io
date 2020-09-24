---
title: "Tutorial: cómo publicar tus hilos de Twitter automáticamente a tu blog"
category: Tutoriales
related_image: /images/2020-09-24/01-overview.png
---

En este tutorial te voy a contar cómo podés hacer para poder pasar los hilos que escribís en Twitter directamente a tu blog hosteado en GitHub Pages.

Este blog se va a orientar a pasar hilos de Twitter a GitHub Pages. Igual si tu blog está hosteado en otra tecnología, probablemente puedas aprovechar gran parte de lo que aquí te cuento.

Una vez más, este tutorial se basa en una recopilación de distintas guías oficiales y no oficiales, posts en StackOverflow y otras yerbas. Es el tutorial que me hubiera gustado tener hace una semana cuando sufrí todo esto en carne propia y me golpeé con cada posible piedra del camino.

Antes de arrancar, quiero agradecer a la gente de ThreadReaderApp, quienes fueron muy gentiles y corrigieron en tiempo record 2 ó 3 bugs que les reporté. 🙌

# Prerequisitos
1. Tener una cuenta de Twitter.
1. Tener un blog ya funcionando en GitHub Pages. Si aún no lo tenés, te recomiendo ir a mis tutoriales al respecto: [parte 1]({% post_url 2020-09-20-Tutorial-blog-1 %}), [parte 2]({% post_url 2020-09-21-Tutorial-blog-2 %}).

# Objetivos
Al finalizar este tutorial, vas a tener un proceso 99% automático para convertir un hilo de Twitter en un hermoso post en tu blog.

Vamos con un diagrama conceptual de cómo fluirá la información:
![Objetivo](/images/2020-09-24/01-overview.png)
1. Vos, o sea, el autor o autora de un hilo, publicás algo en Twitter.
2. [ThreadReaderApp](https://threadreaderapp.com/) convertirá tus hilos en una página estática.
3. Luego usaremos el protocolo Micropub para convertir esta página estática en un un post de nuestro blog en GitHub pages.

Este diagrama es puramente ilustrativo. En la práctica habrá otros sistemas que darán soporte a este flujo de información... ya lo veremos en el resto de este tutorial.

Sin más, ¡manos a la obra!

# Paso 1. Creando una cuenta en ThreadReaderApp
ThreadReaderApp es un bot de Twitter al que podemos invocar **al final** de nuestros hilos mandando el siguiente Tweet:
![Invocando al bot](/images/2020-09-24/02-bot-invoke.png)

Es importante que sigamos al bot, porque sino no nos dará bola 😛. Al instante, recibiremos una respuesta como esta:
![El bot nos responde](/images/2020-09-24/03-bot-response.png)

En otras palabras, el bot ha generado una página estática, hosteada en `threadreaderapp.com`, con todo el contenido de nuestro hilo. Algo como esto:
<span class="entity-video"><video autoplay muted loop controls poster="/images/2020-09-24/04-unrolled-thread.png"><source src="/images/2020-09-24/04-unrolled-thread.mp4" type="video/mp4"><img alt="Video Poster" src="/images/2020-09-24/04-unrolled-thread.png"></video></span>

Un tiempo atrás, ThreadReaderApp agregó la opción de loguearse a dicha app usando tu cuenta de Twitter. Para eso vamos a ir a su [página de login](https://threadreaderapp.com/login) y vamos a hacer click en la primera opción:
![Login with Twitter](/images/2020-09-24/05-threadreaderapp-login.png)

Una vez que estamos logueados, vamos a poder acceder a dos pestañas:
* *"My Threads"* tendrá todos los hilos **que le pedimos al bot**, ya sean escritos por nosotros o por terceros.
* *"My Authored Threads"* contendrá todos los hilos que **escribimos nosotros**, ya sea que se lo hayamos pedido al bot nosotros o alguna otra persona.

Ambas pestañas son muy parecidas, pero la segunda ofrece la funcionalidad de publicar directo a un blog. Podremos ver un botón `Publish to Blog` en cada uno de nuestros threads:
![My authored threads](/images/2020-09-24/06-my-authored-threads.png)

Ya volveremos a este punto, pero antes tenemos que hablar de Micropub.

# Paso 2. Creando un servidor Micropub
El sistema que usa ThreadReaderApp para publicar en nuestro blog es el protocolo [Micropub](https://www.w3.org/TR/micropub/). Micropub le permite a un cliente enviarle un blog post a un servidor para su publicación. Es decir, el servidor sería nuestro propio blog, o algún tipo de proxy del mismo. Micropub es muy completo, y tiene APIs para crear, editar y borrar posteos. En este tutorial sólo nos ocuparemos de la creación.

En este paso vamos a enseñarle a nuestro blog a hablar Micropub. Como nuestro blog está hosteado en GitHub, lo que haremos es levantar un nuevo servicio en Heroku a modo de proxy. Al recibir un nuevos post, este servicio generará un commit en GitHub, efectivamente creando una nueva entrada en nuestro blog de GitHub Pages.

![Objetivo paso 2](/images/2020-09-24/07-paso-2.png)

## Paso 2.1. Forkeando el servicio Micropub
Podríamos arrancar de cero... ¿pero para qué si alguien ya resolvió esto? Vamos a hacer un fork del repositorio [`lildude/micropub-github-pages`](https://github.com/lildude/micropub-github-pages).

Sobre nuestro fork, haremos algunos ajustes.

Primero que nada, borraremos esta línea del `.gitignore`:
```diff
  coverage/
  env.rb
- /config.yml
  vendor/gems/
```

Como segundo paso, copiaremos `config-example.yml` a `config.yml` y lo editaremos de la siguiente manera:
```yml
sites:
  mysite:
    # Aquí pondremos el repo de nuestro GitHub Page
    # en formato [usuario]/[usuario].github.io
    github_repo: fulano/fulano.github.io # ejemplo!

    # El formato con el que se construye el permalink
    # en tu GitHub Page. 
    # Si no lo conocen, no es importante, sólo se usa
    # para responderle a ThreadReaderApp con el URL
    # del post que se acaba de crear
    permalink_style: "/:year/:month/:day/:title/"

    # La URL de su blog hosteado en GitHub Pages.
    # Sería https://[usuario].github.io a menos que usen
    # un dominio propio
    site_url: "https://miespectacularblog.com.ar" # ejemplo!

    # Dejamos estas dos así como estan
    full_image_urls: false
    image_dir: "images"

# Esta también la dejaremos así
download_photos: false

# Aquí configuraremos SOLO nuestro usuario de GitHub.
# NO COMPLETEN EL TOKEN AQUI.
github:
  token:  # Dejarlo vacío, por el amor de Bohr
  username: fulano # ejemplo!

# Aquí configuraremos qué servicio usar para autenticar 
# a ThreadReaderApp
micropub:
  # Usaremos IndieAuth, en un ratito les explico bien
  # IMPORTANTE: esta URL *no* es un ejemplo, usen esta
  token_endpoint: "https://tokens.indieauth.com/token"
  # Aquí ponen de nuevo la URL de su blog. Ya sea en github.io
  # o su dominio propio, si usan uno.
  token_me: "https://miespectacularblog.com.ar" # ejemplo!
```
Revisemos todo por favor 3 veces, habiendo cambiado los valores que están como ejemplo.

Finalmente, vamos a editar `templates/note.liquid`, poniendo el siguiente contenido:
{% raw %}
```
---
title: ___TODO
published: false
---

{{ content }}
```
{% endraw %}
Este template es el que se usará para generar el archivo en el directorio `posts/` de su GitHub Page. Lo más importante de este cambio es que al usar `published: false` marcamos el post como borrador. Esto nos permite revisarlo y agregarle un título (y/o categorías, tags, etc...) antes de que sea visible. 

El `___TODO` es simplemente para recordar agregarle un título a cada post que entre antes de pasarlo a `published: true`, ya que Micropub genera unos títulos numéricos que no son muy buenos para nosotros los humanos.

Ahora estamos listos para crear un commit en nuestro fork con nuestros cambios en `.gitignore`, `config.yml` y `templates/note.liquid`.

**Tip:** si quieren hacer más customizaciones, como por ejemplo autogenerar el título a partir del contenido del thread o alguna otra locura así, pueden editar `app.rb`. Yo no soy un gran developer Ruby, pero me las arreglé para hacer algunos pequeños ajustes que no vienen al caso para este tutorial.

## Paso 2.2. Ajustando la autenticación en nuestro blog
Como les mencionaba antes, necesitamos establecer un mecanismo para que ThreadReaderApp se autentique contra nuestro servicio Micropub. La forma que propongo en este tutorial es usar [IndieAuth](https://indieauth.com/).

IndieAuth es un servicio que te permite autenticarte no como una persona, sino como el dueño de un dominio. Para que IndieAuth confíe que nosotros somos dueños de un dominio `foo.com` que cumplir dos cosas:
1. Nuestro perfil de GitHub tiene como homepage a `https://foo.com`.
1. El sitio web hosteado en `https://foo.com` tiene un tag que apunta a nuestro perfil de GitHub.

En otras palabras, IndieAuth nos va a confiar que somos dueños de `foo.com` si logramos editar el contenido su web para que apunte a nuestro perfil de GitHub, y logramos editar nuestro perfil de Github para que apunte a su web. Si alguno de estos dos puntos no se cumple, IndieAuth no nos creerá.

¡Manos a la obra entonces!

### Paso 2.2.1. Apuntando nuestro perfil de GitHub a nuestro blog
Este paso es muy sencillo. Iremos a nuestro perfil de GitHub y lo apuntaremos para que apunte a nuestro blog. En esta imagen pueden ver el mío como ejemplo. Esto también funciona si usan GitHub Pages, pero en mi caso lo tengo apuntado a un dominio custom.
![Perfil de GitHub](/images/2020-09-24/08-perfil-github.png)

### Paso 2.2.2. Apuntando nuestro blog a nuestro perfil de GitHub
En este paso iremos al repositorio que contiene nuestra página web y agregaremos un link a nuestro perfil de GitHub.

Hay dos variantes. Si ya tenían un hipervínculo `<a>` a su perfil de GitHub, entonces simplemente deben agregarle el atributo `rel="me"`. Por ejemplo:
```html
<a href="https://github.com/[usuario]" rel="me">Mi perfil en GitHub</a>
```
Recuerden remplazar `[usuario]` por el suyo.

En cambio, si no quieren tener un hipervínculo visible a su perfil de GitHub, pueden simplemente agregar esto en el head de su HTML:
```html
<html>
  <head>
    ...
    <link href="https://github.com/[usuario]" rel="me">
  </head>
  ...
```
Recuerden remplazar `[usuario]` por el suyo.

### Paso 2.2.3. Realizando otros cambios en nuestro blog
IndieAuth y MicroPub necesitan un poco más de metadatos adicionales en nuestro blog para funcionar correctamente. 

Vamos nuevamente al head del HTML de nuestro blog y agregamos:
```html
<html>
  <head>
    ...
    <link rel="token_endpoint" href="https://tokens.indieauth.com/token">
    <link rel="authorization_endpoint" href="https://indieauth.com/auth">
  </head>
  ...
```
De esta manera, estamos indicando a todo el mundo que el mecanismo de autenticación que usaremos será el servicio de IndieAuth.

### Paso 2.2.4. Probando que la autenticación funciona
Antes de continuar, vamos a verificar que la autenticación funciona. Para esto visitaremos [IndieAuth.com](https://indieauth.com/) y buscaremos la sección que dice `Try It!`.
![Try it](/images/2020-09-24/09-try-it.png)

Ingresamos nuestro dominio y luego IndieAuth verificará si nuestro sitio apunta algún perfil soportado (por ejemplo GitHub), que además linkea de nuevo hacia nuestro sitio. Si todo sale bien veremos algo como esto:
![Sign in](/images/2020-09-24/10-sign-in.png)

## Paso 2.3. Deployando nuestro servidor Micropub
Con la autenticación ya funcionando, estamos listos para deployar nuestro servicio Micropub.

En mi caso, yo lo deployé en Heroku, ya que es simple y gratis. Pero nada impide usar otros proveedores de nube, o su propia infraestructura si así lo desearas.

### Paso 2.3.1. Creando una nueva app en Heroku
En este paso asumiré que ya tenés una cuenta en Heroku. Si no, crear una es muy sencillo desde [su página de registración](https://signup.heroku.com/).

Vamos a crear una nueva *app* desde la [pantalla principal de Heroku](https://dashboard.heroku.com/apps).
![Lista de apps](/images/2020-09-24/11-heroku-list.png)

Le damos un nombre, cualquiera siempre y cuando no esté tomado.
![Creando app](/images/2020-09-24/12-heroku-create.png)

Una vez creada. vamos a conectar nuestra *app* con nuestro fork de `lildude/micropub-github-pages` que hicimos antes.
![Link a GitHub](/images/2020-09-24/13-heroku-github.png)

Y luego activaremos los deploys automáticos desde el branch `master`.
![Deploy auto](/images/2020-09-24/14-heroku-auto-deploy.png)

Recuerdan que al hacer el fork les dije que NO pongan su token de GitHub en el `config.yml`... bueno, ahora es el momento de configurarlo en su entorno de Heroku. Creamos un nuevo token en [esta página](https://github.com/settings/tokens/new). El único permiso que le daremos es el de `repo`.
![Creando un nuevo token](/images/2020-09-24/15-github-token.png)

**Importante:** Una vez creado, recuerden copiar el valor de su token y anotarlo en algún lado seguro, como por ejemplo un *password manager* ya que GitHub no se los mostrará **nunca más**.

Iremos a la configuración de nuestra *app* en Heroku, en la pestaña `Settings` y haremos clíck en el botón `Reveal Config Vars`.
![Mostrando la configuración](/images/2020-09-24/16-revelar-vars.png)

Agregamos una nueva variable, llamada `GITHUB_ACCESS_TOKEN` y le ponemos como valor el token que copiamos en el paso anterior.
![Agregando configuración](/images/2020-09-24/17-set-vars.png)

Con esto listo, estamos preparados para hacer el *deploy* de nuestra *app*. Vamos a la pestaña `Deploy` en Heroku. Abajo de todo habrá una opción para hacer un *deploy* manual desde un branch.
![Heroku deploy](/images/2020-09-24/17b-heroku-deploy.png)

Ahora que el servidor Micropub está corriendo, debemos configurar nuestro blog para indicarle la ubicación de este servidor. Volvemos al head del HTML de nuestro blog y agregamos un `<link>` más:
```html
<html>
  <head>
    ...
    <link rel="micropub" href="https://[mi-servicio-micropub].herokuapp.com/micropub/mysite">
  </head>
  ...
```
Remplazando `[mi-servicio-micropub]` por el nombre de su *app* de Heroku. 

**Importante:** el resto de la URL, incluyendo el `/micropub/mysite` la debemos dejar así como está. El valor `mysite` coincide con la configuración del archivo `config.yml` que hicimos al principio de este tutorial.

# Paso 3. Cargando nuestro blog en ThreadReaderApp
Ya casi estamos. Con todo esto funcionando, el paso que falta es volver a ThreadReaderApp y dar de alta nuestro blog.

Volvemos a [ThreadReaderApp](https://threadreaderapp.com/account/author) en la pestaña `My Authored Threads`. Y haremos clíck en el botón para configurar nuestro blog.
![Configurando el blog](/images/2020-09-24/18-blog-settings-1.png)
![Configurando el blog](/images/2020-09-24/19-blog-settings-2.png)

Cargamos la URL de nuestro blog. ThreadReaderApp encontrará a su servicio Micropub mediante el `<link rel="micropub" ...>` que pusimos en el head del HTML.
![Configurando el blog](/images/2020-09-24/20-blog-settings-3.png)

ThreadReaderApp nos pedirá autenticarse contra nuestro blog con el *scope* de `create`, que le permitirá crear nuevas entradas.
![Login al blog](/images/2020-09-24/21-blog-login.png)

Este paso lo haremos una única vez. Ahora sí ya estamos listos para postear.

# Paso 4. Publicando un hilo de ThreadReaderApp a nuestro blog
Supongamos que hicimos un hilo buenísimo y ahora lo queremos poner en nuestro blog. 

Lo primero será generar el hilo en ThreadReaderApp mediante su bot. Una vez que el hilo esté disponible en nuestra pestaña `My Authored Threads`, haremos click en el botón de `Publish N tweets to blog`.
![Posteando](/images/2020-09-24/22-blog-publish-1.png)

Esperamos un ratito... sobre todo si Heroku mandó nuestra *app* a dormir...
![Posteando](/images/2020-09-24/23-blog-publish-2.png)

¡Y listo!
![Posteando](/images/2020-09-24/24-blog-publish-3.png)

El blog post ya estará cargado en nuestro repo de GitHub Pages. Recordar que **no** estará visible porque pusimos `published: false` en nuestro template.
![Git pull](/images/2020-09-24/25-git-pull.png)

Notar en mi caso se creó un archivo `posts/2020-09-24-75129.md` con los contenidos del hilo. A esta altura ya puedo editarle el título, renombrarlo (opcional), quitarle el `published: false` y hacer un commit. Ya nuestro hilo estará disponible en nuestro blog.

# Conclusión
Bueno... quedó larguísimo el tutorial, pero la idea era explicar bien el paso a paso, porque creo que el resultado vale la pena.

Hace una semana que lo estoy usando y con muy buenos resultados. Tuve que hacer algunos ajustes en el CSS de mi blog para que el resultado que venía de ThreadReaderApp se vea bonito, pero nada demasiado grave.

Espero que les sea útil, y cualquier cosa que no les funciona me escriben por Twitter 👇.
