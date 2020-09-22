---
title: "Tutorial: cómo armar un blog con GitHub pages (parte 2)"
category: Tutoriales
---

Esta es la segunda parte del tutorial para armar tu blog usando GitHub Pages. En esta parte nos enfocaremos en registrar un dominio en [nic.ar](https://nic.ar) y en configurar nuestro blog para que lo pueda usar.

En [la parte 1 de este tutorial]({% post_url 2020-09-20-Tutorial-blog-1 %}) vimos cómo armar un blog usando GitHub Pages y con el dominio `[usuario].github.io`. Si aún no seguiste los pasos de la primer parte, te sugiero que empieces por allí y luego vuelvas a esta segunda parte.

# Prerequisitos
1. Clave fiscal de nivel 2 o superior en AFIP.

# Paso 2. Registrando un dominio en nic.ar
En este tutorial vamos a registrar un dominio `.com.ar` o simplemente `.ar` en [nic.ar](https://nic.ar). Si preferís registrar un dominio `.com` o de otro país (por ej: `.cl`, `.mx`, `.pe`, `.es`), entonces te recomiendo saltar directo al paso 3 de este tutorial.

## Paso 2.1. Buscando si el dominio está disponible
El primer paso es visitar el sitio [nic.ar](https://nic.ar) y verificar si el dominio que nos interesa está disponible o no. Por ejemplo supongamos que el dominio que nos interesa es:
```
undominiointeresante.com.ar
```
![Búsqueda](/images/2020-09-21/01-busqueda.png)

Si tenemos algo de suerte, nuestro dominio estará disponible. Si no tendremos que probar con otro.
![Disponible](/images/2020-09-21/02-disponible.png)

## Paso 2.2. Registrando el dominio
Vamos a hacer click en `QUIERO REGISTRARLO`. El sistema nos ofrecerá login via nuestra clave fiscal de AFIP. Para extrajeros hay una opción de login con usuario y contraseña, pero no la probé.
![Operar](/images/2020-09-21/03-operar.png)

Una vez logueado, el sitio te mostrará nuevamente la consulta del dominio que te interesa. Y te confirmará la disponibilidad. 
![Confirmar consulta](/images/2020-09-21/04-consulta-bis.png)

Vamos a hacer click en `REGISTRAR`. Si esta es tu primera registración de dominio, es posible que te pida que completes tus datos personales. Luego le daremos click a `CONFIRMAR TRÁMITE`.
![Completar datos](/images/2020-09-21/05-datos.png)

## Paso 2.3. Pagando por el dominio
Si todo sale bien, el navegador nos redigirá a una página de pago. El monto para registrar un dominio `.com.ar` a la fecha de hoy (septiembre de 2020) es de $270 pesos argentinos por un año. Los dominios `.ar` salen el doble.
![Pago](/images/2020-09-21/06-pago.png)

Hay varias opciones de pago, aunque tuve bastantes problemas con este paso. Intenté pagar con tarjeta de crédito, pero me aparecía un cartel diciendo que el "Gateway de pago" estaba caído. Si les pasa esto, pueden probar al día siguiente. 

*Importante:* A esta altura el trámite ya está en curso, y la mejor manera de continuarlo es loguearte con tu clave de AFIP desde el [portal de trámites a distancia](https://tramitesadistancia.gob.ar/tramitesadistancia/inicio-publico). **No generes un nuevo trámite**, ya que probablemente el dominio te figure como pre-registrado debido al trámite anterior.

Una vez que realicemos el pago y recibamos un e-mail de confirmación en nuestra casilla, ya somos los orgullosos dueños de nuestro dominio web. Para administrarlo hay que buscar un link medio perdido en el pie de página de [nic.ar](https://nic.ar).
![Navegación nic.ar](/images/2020-09-21/07-navegacion.png)

## Paso 2.4. Confirmando que el dominio ya es nuestro
Desde allí, tras autenticarnos nuevamente vía AFIP, podremos la nuestra lista de nuestros dominios.
![Administrar dominio](/images/2020-09-21/08-administrar.png)

Si el dominio figura con estado `Registrado`, entonces ya podemos pasar al paso 3 de este tutorial.

**Ojo:** Si el estado dice `Pendiente de pago`, vas a tener que ir [portal de trámites a distancia](https://tramitesadistancia.gob.ar/tramitesadistancia/inicio-publico) y buscar el trámite en curso para terminar de pagarlo.

# Paso 3. Configurando el DNS con Namecheap
Lamentablemente, la registración del dominio por sí misma no es suficiente. Vamos a necesitar un servicio adicional para que administre el DNS de nuestro dominio.

Por suerte hay varios servicios para elegir, algunos de ellos gratuitos. El que propongo en este tutorial es Namecheap. 

## Paso 3.1. Creando una cuenta en Namecheap
Namecheap ofrece servicios de DNS de todo tipo. Los servicios que usaremos nosotros son todos gratuitos. Vamos a [registrarnos como usuario en Namecheap](https://www.namecheap.com/myaccount/signup/).

## Paso 3.2. Registrando nuestro dominio con FreeDNS
Una vez que estamos registrados y logueados en Namecheap, vamos a buscar el servicio FreeDNS en la navegación superior.
![Buscando FreeDNS](/images/2020-09-21/09-freedns.png)

Vamos a ingresar nuestro dominio en el campo de consulta.
![Consultando dominio FreeDNS](/images/2020-09-21/10-consulta-freedns.png)

Hacemos click en `Get DNS` y llegamos a una especie de carrito de compras.
![Carrito FreeDNS antes](/images/2020-09-21/11-carrito-1.png)

Hacemos click en el botón cuadrado que agrega el servicio al carrito. Y luego, confirmando que el total sigue siendo `$0.00`, hacemos click en `Set up DNS`.
![Carrito FreeDNS después](/images/2020-09-21/12-carrito-2.png)

Vamos a ver un mensaje que nos indica los servidores que debemos indicarle a `nic.ar`. Tenerlos a mano para el próximo paso.
![FreeDNS servers](/images/2020-09-21/13-servers.png)

## Paso 3.3. Apuntando nic.ar a Namecheap
Vamos a volver a la pantalla de administración de dominios en [nic.ar](https://nic.ar) y vamos a hacer click en `DELEGAR`.
![Delegar](/images/2020-09-21/14-delegar.png)

Vamos a hacer click en `Agregar una nueva delegación` y cargar el primer servidor que nos dió Namecheap en el campo `Host`. Realizamos lo mismo 5 veces hasta que cargamos los 5 servidores, que se los dejo aquí por si los quieren copiar/pegar:
```
freedns1.registrar-servers.com
freedns2.registrar-servers.com
freedns3.registrar-servers.com
freedns4.registrar-servers.com
freedns5.registrar-servers.com
``` 
![Delegaciones](/images/2020-09-21/15-delegaciones.png)

**Importante:** Una vez cargadas las 5 delegaciones, no te olvides de hacer click en `EJECUTAR CAMBIOS`.
![Ejecutar cambios](/images/2020-09-21/16-ejecutar-cambios.png)

**Atención:** Los cambios aplicados en `nic.ar` pueden tomar algunas horas en aplicarse y propagarse por el sistema distribuido global que es DNS.

## Paso 3.4. Configurando registros DNS en Namecheap
En este paso volveremos a Namecheap y vamos a buscar nuestro dominio en la lista de productos.
![Navegar a domain list](/images/2020-09-21/17-domain-list-nav.png)

**Ojo:** Me ha pasado más de una vez que no pude encontrar mi dominio en la lista de productos de Namecheap. Si te pasa podés usar directamente [este link](https://ap.www.namecheap.com/Domains/DomainList).

Deberíamos ver a nuestro dominio en la lista, y una indicación que dice que aún no está validado "FreeDNS is pending authorization". Esto es normal, ya que pueden pasar unas horas hasta que Namecheap detecte los cambios que hicimos en el paso anterior en `nic.ar`. No es un problema, podemos avanzar con este paso de todas maneras.
![Domain list](/images/2020-09-21/18-domain-list.png)

Hacemos click en el botón `MANAGE` y nos llevará a esta pantalla.
![Administrar dominio](/images/2020-09-21/19-manage.png)

Desde allí hacemos click en la pestaña `Advanced DNS` y nos llevará a una pantalla donde podremos cargar registros DNS.
![Administrar dominio](/images/2020-09-21/20-records-empty.png)

Usando el botón `ADD NEW RECORD`, cargaremos 5 registros de la siguiente manera:

<table>
  <thead>
    <tr>
      <th>Record type</th>
      <th>Host</th>
      <th>Value</th>
      <th>TTL</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>A Record</td>
      <td>@</td>
      <td>185.199.108.153</td>
      <td>Automatic</td>
    </tr>
    <tr>
      <td>A Record</td>
      <td>@</td>
      <td>185.199.109.153</td>
      <td>Automatic</td>
    </tr>
    <tr>
      <td>A Record</td>
      <td>@</td>
      <td>185.199.110.153</td>
      <td>Automatic</td>
    </tr>
    <tr>
      <td>A Record</td>
      <td>@</td>
      <td>185.199.111.153</td>
      <td>Automatic</td>
    </tr>
    <tr>
      <td>CNAME Record</td>
      <td>www</td>
      <td>[usuario].github.io.</td>
      <td>Automatic</td>
    </tr>
  </tbody>
</table>

**Importante 1**: En el último registro, reemplazar `[usuario]` por tu nombre de usuario de GitHub.

**Importante 2**: En el último registro, el valor termina con un punto final luego del `.io`, esto *NO* es un error de tipeo.

Con todo completado, debería quedar así:
![Administrar dominio](/images/2020-09-21/21-records-full.png)

# Paso 4. Configurando GitHub Pages
Ya casi lo tenemos, falta un pasito muy cortito.

Volvemos a nuestro repositorio en GitHub y vamos a editar algunos archivos.

En el archivo `CNAME` vamos a poner nuestro dominio "a secas", **SIN** el www. Por ejemplo:
```
undominiointeresante.com.ar
```

En el archivo `_config.yml` vamos a poner nuestro dominio, completo con `https://` en el campo `url`:
```yaml
# Este valor se usa para generar la navegación interna del sitio
url: https://undominiointeresante.com.ar
```

Hacemos un commit con estos cambios y lo pusheamos a GitHub... ¡y listo! 💪

Ahora deberíamos poder navegar a `https://undominiointeresante.com.ar` y ver nuestro blog en toda su gloria.

**Importante 1:** Recordá que si hiciste todos los pasos uno atrás del otro, probablemente la propagación de los valores de `nic.ar` aún no haya terminado. Si algo no funciona, recomiendo esperar unas 24 horas más. 

**Importante 2:** Si al navegar a tu dominio propio con HTTP todo funciona, pero con HTTPS no te funciona, esto es normal por las primeras horas con una GitHub Page con dominio propio. Dale a GitHub un par de horas y tu navegación con HTTPS debería funcionar perfecto.

# Conclusión
Parece un tutorial bastante largo pero se puede seguir paso a paso y no es para nada difícil... y vale la pena. Además de los 270 pesos argentinos por el dominio, no hay otros costos.

Si te surge alguna duda contactame vía Twitter 👇 e intentaré darte una mano.
