---
layout: post
title:  Incrementar y decrementar números con Vim
image:
  path: https://upload.wikimedia.org/wikipedia/commons/thumb/9/9f/Vimlogo.svg/544px-Vimlogo.svg.png
  thumbnail: https://upload.wikimedia.org/wikipedia/commons/thumb/9/9f/Vimlogo.svg/240px-Vimlogo.svg.png
  caption: "User:D0ktorz, GPL <http://www.gnu.org/licenses/gpl.html>, via Wikimedia Commons"
date: 2022-11-13 10:00:00 +0100
author: Quique Porta
comments: true
share: true
categories:
    - Vim
tags:
    - vim
    - tips
---

Esta semana en [Agroptima](https://www.agroptima.com) tuvismo que crear un archivo con unos rangos de números que iban incrementando de 500000 en 500000.

{% highlight vim linenos%}
0
500000
1000000
1500000
2000000
2500000
{% endhighlight %}

Para hacer esto **Vim** tiene dos comandos que nos pueden facilitar esta tarea.

* `Ctrl+a` incrementa el número encima del cursor o el más próximo a la derecha del cursor.
* `Ctrl+x` decrementa el número encima del cursor o el más próximo a la derecha del cursor.


Si esto lo juntamos con la posibilidad de crear `macros` con **Vim**, pues lo tenemos fácil.

Primero insertamos un 0 en el documento.

{% highlight vim linenos%}
0
{% endhighlight %}

Luego empezamos a grabar una macro en el registro `a`. Se hace pulsando las teclas `qa`.

Ahora solo nos queda ejecutar los pasos para copiar ese número e incrementarlo.

* Pulsamos `yy` para copiar la línea sobre la que estamos.
* Pulsamos `p` para pegar la línea justo debajo.
* Luego escribimos el número `500000`, que es la cantidad que queremos incrementar.
* Pulsamos `Ctrl+a` y veremos como se ha incrementado el número.
* Por último pulsamos de nuevo la `q` para dejar de grabar la macro.

Este será el resultado de la macro grabada (lo puedes visualizar escribiendo `:registers a`).
```
yyp500000^A
```

Y en pantalla nos tiene que quedar algo así.

{% highlight vim linenos%}
0
500000
{% endhighlight %}

Pues nada, si ahora ejecutamos la macro `a` veremos como se nos crea una línea con el número incrementado.
Para ejecutar la macro se hace con `@a`.

{% highlight vim linenos%}
0
500000
1000000
{% endhighlight %}

Si queremos repetir esto 100 veces pues pulsamos `100@a`.

{% highlight vim linenos%}
0
500000
1000000
1500000
2000000
2500000
3000000
# ...
{% endhighlight %}

Pues esto es todo, recuerda que puedes incrementar/decrementar números en vim con `Ctrl+a`/`Ctrl+x`.
