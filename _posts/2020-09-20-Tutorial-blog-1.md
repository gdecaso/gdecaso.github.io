---
layout: post
title: "Tutorial: cómo armar un blog con GitHub pages (parte 1)"
---

Hace pocos días que arranqué este proyecto de blog personal y durante el proceso tuve que luchar contra algunos demonios. Aquí les comparto el tutorial que me hubiera gustado encontrar antes de arrancar mi proyecto.

# Objetivos
Al final de este tutorial deberías tener un blog muy básico, servido de manera estática por GitHub, y con HTTPS.

En este post, que es primera parte, nos enfocaremos en levantar el blog en una GitHub page.

La segunda parte, que quedará para otro post, será para usar un dominio propio (por ejemplo `miblog.com.ar`).

# Prerequisitos
1. Conocimientos básicos de `git` y `github`.

Para esta primera parte no vamos a necesitar nada más 💪.

# Paso 1. Creando una GitHub page
Para este primer paso, es necesario que tengas un usuario en `GitHub`. 

Vamos a hacer un fork del repositorio [`barryclark/jekyll-now`](https://github.com/barryclark/jekyll-now).
![Fork](/images/2020-09-20/01-fork.png)

Este repositorio nos servirá como una especie de template. Pero tenemos que hacer algunos ajustes antes de que esté listo para que lo usemos.

**Importante:** Nuestro fork debe ser un repositorio público. De lo contrario no lo podremos usar para una GitHub page. Como siempre, es importante recordar la buenas práctica de no almacenar ningún secreto en nuestros repositorios 😉.

## Paso 1.1. Renombrando nuestro fork
El fork va a tener el mismo nombre de repositorio que el original, pero para que pueda ser servido como una GitHub page necesitamos que el repositorio tenga un nombre especial.

Vamos a la configuración del repositorio y buscamos la opción para renombrarlo, que debería estar arriba de todo.

Debemos elegir un nombre de repositorio que siga el patrón `*.github.io`. Por ejemplo `miblog.github.io`.
![Fork](/images/2020-09-20/02-rename.png)

**Atención:** No es tan grave si después lo quieren renombrar, pero es importante que el nombre termine en `.github.io` o sino GitHub no lo reconocerá como un repositorio de GitHub pages.

## Paso 1.2. Clonando nuestro fork
En este paso, vamos a clonar nuestro fork para tenerlo localmente en nuestra compu.
```sh
git clone git@github.com:[usuario]/[repo].github.io.git
```
Reemplazando `[usuario]` por tu username en GitHub y `[repo]` por el prefijo de nombre del repositorio que hayas elegido.

## Paso 1.3. Borrando cosas que sobran
Vamos a borrar los siguientes archivos:
* Borramos `README.md`. Aunque si quieren lo pueden reemplazar por uno con contenido propio que describa su repositorio.
* Dentro del directorio `images/` vamos a borrar todas menos `404.png`. Como lo adivinaron, esta se usará para la página de error. También la pueden reemplazar por otra distinta si quieren.

## Paso 1.4. Configuración básica del blog
El archivo `_config.yml` contiene configuraciones muy importantes para neustro futuro blog. En este paso lo editaremos para dejarlo acorde a nuestras preferencias.

Los siguientes 3 valores son obligatorios:
```yml
# El nombre de nuestro sitio
# va a mostrarse en la barra de navegación superior
name: Mi maravilloso blog

# Descripción de nuestro sitio
# va a mostrarse en la barra de navegación superior
description:
  En mi blog podrán encontrar todo tipo de información
  útil sobre cosas.

# URL del logo del blog
# va a mostrarse en la barra de navegación superior
avatar: https://raw.githubusercontent.com/[usuario]/[repo].github.io/master/images/logo.png
```

Para la URL del avatar, mi recomendación es que le deleguemos la tarea de servir las imágenes a las CDN de GitHub. Para eso, sencillamente vamos a poner nuestro logo en el directorio `images/` y con el nombre `logo.png`. 

**Importante:** Recordá reemplazar `[usuario]` y `[repo]` por los tuyos en el valor del `avatar` del ejemplo anterior.

El archivo `config.yml` también tiene varias configuraciones opcionales pero *muy importantes*.
```yml
# Por cada uno de estos valores que configuen...
# ...aparecerá un ícono correspondiente en el pie de página.
footer-links:
  dribbble:
  email:
  facebook:
  flickr:
  github: gdecaso
  instagram:
  linkedin: gdecaso
  pinterest:
  rss: # url de cualquier RSS feed
  twitter: guidodecaso
  stackoverflow: # tu perfil de StackOverflow, ej: "users/50476/bart-kiers"
  youtube: # channel/<your_long_string> o user/<user-name>
  googleplus: # ¿sigue vivo ese antro?
```

En mi caso yo sólo completé 3 campos y por eso mi pie de página se ve así:
![Fork](/images/2020-09-20/03-footer.png)

Y por último, algunos tweaks más:
```yml
# Podés poner tu shortname de Disqus (no tu username)
# para habilitar comentarios en tus posts.
# (¡yo no lo probé aún!)
disqus:

# Tu código de Google Analytics para tener métricas.
# Ojo, el que puse acá es sólo un ejemplo.
google_analytics: UA-2110908-2

# Esto se usa para tener un dominio propio.
# Dejarlo vacío por ahora.
# Lo vamos a editar en la segunda parte del tutorial.
url:

# Este dejarlo así vacío.
baseurl: ""

# Esta configuración determina el patrón de URL
# que se usará para los links a tus posts.
# Recomiendo cambiar el que viene por este otro
permalink: /:year/:month/:day/:title/
```

## Paso 1.6. Traduciendo algunas cositas
El template está en inglés, y si tu blog estará en español vas a tener que hacer algunos ajustes menores.

En `404.md` deberías cambiar el mensaje de error por uno en castellano, por ejemplo:
{% raw %}
```md
Lo siento, La página que estás buscando no existe. [Volver al home]({{ site.baseurl }}/).
```
{% endraw %}

En `index.html` vamos a traducir esta parte:
{% raw %}
```diff
- <a href="{{ site.baseurl }}{{ post.url }}" class="read-more">Read More</a>
+ <a href="{{ site.baseurl }}{{ post.url }}" class="read-more">Leer más</a>
```
{% endraw %}

En `_layouts/default.html` vamos a traducir esta parte:
{% raw %}
```diff
- <a href="{{ site.baseurl }}/about">About</a>
+ <a href="{{ site.baseurl }}/about">Acerca de mí</a>
```
{% endraw %}

Por último, en `_layouts/post.html` vamos a traducir esta parte:
{% raw %}
```diff
   <div class="date">
-    Written on {{ page.date | date: "%B %e, %Y" }}
+    Escrito el {{ page.date | date: "%d/%m/%Y" }}
   </div>
```
{% endraw %}

## Paso 1.7. Completar el acerca de mí
En este paso vamos a editar el archivo `about.md` para hacerlo propio. Esta página le proveerá de información sobre el autor o autora del blog a nuestros visitantes. Se accederá desde la navegación superior.

Nuestro archivo `about.md` deberá comenzar con un *front matter* de YAML (el segmento entre `---`) que indica cierta meta información. Aquí deberíamos traducir el `title`:
```md
---
layout: page
title: Acerca de mí
permalink: /about/
---

El autor de este blog es una persona muy reservada
y no quiere que sepan mucho de él.
```

El resto del archivo `about.md` que sigue luego del *front matter* es sencillamente un Markdown común y corriente. Pueden incluir tan poco o tan tanto como quieran.

## Paso 1.8. Editando el primer post
Le digo editando, porque para el primer post nos vamos a basar en el que ya viene en el template que forkeamos antes.

Vamos a renombrar el archivo `_posts/2014-3-3-Hello-World.md` y lo vamos a llamar `_posts/2020-09-20-Bienvenida.md`. Como lo adivinaron, el nombre de archivo arranca con la fecha y esto es *mucho muy* importante. Pueden poner la fecha que quieran, siempre que no sea un día en el futuro. El resto del nombre de archivo puede ser el que quieran.

Con respecto al contenido, nuevamente tenemos que editar el *front matter* con su título. El resto del archivo será el Markdown de nuestro primer post.

```md
---
layout: post
title: Post de bienvenida a mi blog
---

Este es mi primer post y te quiero dar la bienvenida
y muchas gracias por visitar.
```

## Paso 1.9. Probando nuestro blog
Para probar que todo funciona hay 2 opciones. Localmente, o "en producción".

### Probando localmente
Para probarlo localmente, debemos [instalar Jekyll](https://jekyllrb.com/docs/installation/). Se pueden seguir los pasos en su web, y no ahondaré en ello. Una vez instalado podemos pararnos en el directorio raíz de nuestro repo y ejecutar:
```sh
jekyll serve --watch
```
Esto servirá nuestro blog en `http://localhost:4000` y lo refrescará cada vez que toquemos algún archivo local. Es ideal para iteraciones rápidas y como entorno de prueba antes de pasar a producción.

**Recomendación:** Agregar `.jekyll-cache` a tu `.gitignore`.

### "Probando" en GitHub
Si te sentís particularmente confiado, o no tenés ganas de instalar Jekyll, podés probar directamente en GitHub pages. 

Lo único que tenés que hacer es commitear y pushear tus cambios vía `git` de la manera usual.

Una vez que tu commit ya esté en GitHub, deberías poder navegar a `https://[miblog].github.io` (o el prefijo que hayas elegido) y verás tu blog en toda su gloria.

**Importante:** Hay una pequeña demora desde que GitHub recibe tu commit y tus cambios se ven reflejados en la GitHub page. Si no los ves, esperá un poco. Si seguís sin verlos, probá abrir una ventana en modo incógnito o forzar el refresh de tu navegador.

# Conclusión de la primera parte
En este punto ya tenés un blog servido con alta disponibilidad por GitHub. Para crear nuevos posts sólo basta que crees un archivo `_posts/yyyy-mm-dd-titulo.md` con el *front matter* como vimos y el contenido en Markdown y hagas un *commit & push* a GitHub.

Si no te molesta tener un dominio como `miblog.github.io` entonces con esta primera parte del tutorial ya deberías tener todo lo necesario. En la segunda parte nos enfocaremos en configurar tu blog para que use un dominio propio como por ejemplo `miblog.com.ar` o `miblog.ar`.
