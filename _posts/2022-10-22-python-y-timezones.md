---
layout: post
title:  Python y timezones
image:
  path: https://upload.wikimedia.org/wikipedia/commons/8/8f/Classic_alarm_clock_20180513.jpg
  thumbnail: https://upload.wikimedia.org/wikipedia/commons/8/8f/Classic_alarm_clock_20180513.jpg
  caption: "Santeri Viinamäki, CC BY-SA 4.0 <https://creativecommons.org/licenses/by-sa/4.0>, via Wikimedia Commons"
date: 2022-10-22 17:00:00 +0200
author: Quique Porta
comments: true
share: true
categories:
    - Programacion
tags:
    - python
    - programacion
---

¿Qué hora es?

Pues depende de dónde te encuentres 🤪


Trabajar con datetimes en Python a veces puede ser una quebradero de cabeza si no lo haces correctamente. Digamos que hay una serie de cosas que deberias evitar a la hora de trabajar con datetimes en Python.

## No trabajes con naive datetimes

¿Qué es esto de **naive**?

Pues son datetimes que no tienen asociada ninguna zona horaria.

{% highlight python linenos%}
{% raw %}
from datetime import datetime
datetime(2022, 10, 22, 17, 5, 30)  # naive datetime
{% endraw %}
{% endhighlight %}

Los datetimes de tipo **naive** representan el concepto de una fecha y hora.

### Trabaja con aware datetimes

Deberias trabajar siempre con **aware** datetimes. Son datetimes con una zona horaria establecida.

{% highlight python linenos%}
{% raw %}
from datetime import (
    datetime,
    timezone
)
datetime(2022, 10, 22, 17, 5, 30, tzinfo=timezone.utc)  # aware datetime
{% endraw %}
{% endhighlight %}

### Cuidado con datetime.now() y datetime.utcnow()

El resultado de **datetime.now()** depende de la hora del sistema y es un datetime **naive**.

{% highlight python linenos%}
{% raw %}
from datetime import datetime
datetime.now()
>>> datetime.datetime(2022, 10, 22, 19, 40, 52, 113623)
{% endraw %}
{% endhighlight %}

Si tu sistema tiene la hora establecida con, por ejemplo, CEST, te devolverá la hora CEST.

Pero si tu sistema tiene la hora establecida a UTC, te devolverá la hora UTC.

Recuerda que la devolverá sin *tzinfo* (naive).

El resultado de **datetime.utcnow()** te devolverá la hora UTC pero en un datetime **naive**.

En nuestro caso, que ahora estamos con una diferencia horaria de 2 horas, pues nos devolverá la hora UTC que son dos horas menos. Fijate la diferencia con el ejemplo anterior.

{% highlight python linenos%}
{% raw %}
from datetime import datetime
datetime.utcnow()
>>> datetime.datetime(2022, 10, 22, 17, 40, 52, 113623)
{% endraw %}
{% endhighlight %}

### Cuidado con date.today()

Igual que con datetime.now() y datetime.utcnow(), date.today() devolverá la fecha dependiendo de la hora que esté establecida el servidor.

{% highlight python linenos%}
{% raw %}
from datetime import date
date.today()
>>> datetime.date(2022, 10, 22)
{% endraw %}
{% endhighlight %}

Con un servidor con la hora establecida en *America/La_Paz* date.today() puede que te devuleva la fecha de ayer dependiendo de la hora en la que la pidas.

{% highlight python linenos%}
{% raw %}
from datetime import date
date.today()  # Servidor con la hora en America/La_Paz
>>> datetime.date(2022, 10, 21)
{% endraw %}
{% endhighlight %}


### ¿Cómo convertir una datetime naive a aware?

Utiliza la función **.astimezone()**. Te devolverá un datetime aware con la zona horaria que le indiques.

{% highlight python linenos%}
{% raw %}
from datetime import datetime
a_naive = datetime.now()
a_naive
>>> datetime.datetime(2022, 10, 22, 20, 8, 29, 808737)
a_naive.astimezone(timezone.utc)
>>> datetime.datetime(2022, 10, 22, 18, 8, 29, 808737, tzinfo=datetime.timezone.utc)
{% endraw %}
{% endhighlight %}

Siempre para convertir una **naive** datetime a un **aware** utiliza **.astimezone()**.


## Siempre trabaja con UTC

> El tiempo universal coordinado o UTC es el principal estándar de tiempo por el cual el mundo regula los relojes y el tiempo.
>
> Es uno de los varios sucesores estrechamente relacionados con el tiempo medio de Greenwich (GMT). Para la mayoría de los propósitos comunes, UTC es sinónimo de GMT, ya que GMT ya no es el estándar definido para la comunidad científica.
>
> <footer><strong>Wikipedia</strong> https://es.wikipedia.org/wiki/Tiempo_universal_coordinado</footer>

Todos los timezones están definidos en relación a UTC.

Asi que, siempre, siempre, siempre, trabaja con UTC.


## Las operaciones de timedelta siempre con UTC

Las operaciones que relaices con **timedelta** para sumar o restar dias, horas, etc, siempre hazlo con datetimes en UTC.

{% highlight python linenos%}
{% raw %}
from datetime import (
    datetime,
    timedelta,
    timezone
)
now = datetime.now(timezone.utc)
now
>>> datetime.datetime(2022, 10, 22, 14, 57, 14, 925555, tzinfo=datetime.timezone.utc)
tomorrow = now + timedelta(days=1)
tomorrow
>>> datetime.datetime(2022, 10, 23, 14, 57, 14, 925555, tzinfo=datetime.timezone.utc)
{% endraw %}
{% endhighlight %}


## Deja de utilizar pytz

La libreria estandar de Python (a partir de la versión 3.9) ya incorpora una utilidad para gestionar las zonas horarias **ZoneInfo**.

{% highlight python linenos%}
{% raw %}
from zoneinfo import ZoneInfo
datetime(2022, 10, 22, 20, 0, tzinfo=ZoneInfo('Europe/Madrid'))
>>> datetime.datetime(2022, 10, 22, 20, 0, tzinfo=zoneinfo.ZoneInfo(key='Europe/Madrid'))
{% endraw %}
{% endhighlight %}


## ¿Cómo obtener la hora actual?

{% highlight python linenos%}
{% raw %}
from datetime import (
    datetime,
    timezone
)
datetime.now(timezone.utc)
>>> datetime.datetime(2022, 10, 22, 17, 29, 30, 959193, tzinfo=datetime.timezone.utc) ✅
{% endraw %}
{% endhighlight %}


## ¿Cómo obtener la fecha en una zona horaria concreta?

{% highlight python linenos%}
{% raw %}
from datetime import (
    date,
    datetime
)
from zoneinfo import ZoneInfo

datetime.now(ZoneInfo('America/Los_Angeles')).date()
>>> datetime.date(2022, 10, 22) ✅
{% endraw %}
{% endhighlight %}


## ¿Cómo convertir una cadena de texto en formato ISO-8601 a un datetime en UTC?

{% highlight python linenos%}
{% raw %}
from datetime import (
    datetime,
    timezone
)

date_string = '2022-10-22T18:40:04.247201+02:00'  # Europe/Madrid

datetime.fromisoformat(date_string).astimezone(timezone.utc)
>>> datetime.datetime(2022, 10, 22, 16, 40, 4, 247201, tzinfo=datetime.timezone.utc) ✅
{% endraw %}
{% endhighlight %}


Recuerda

- Convierte siempre tus datetimes sin timezone (naive) a uno con timezone (aware) con **astimezone**
- Las operaciones con **timedelta** siempre con datetimes en UTC.
- Los datetimes guardados en base de datos **SIEMPRE** en UTC. Luego ya se los mostrarás al usuario en su timezone con **astimezone**.
- Empieza a utilizar ZoneInfo en vez de pytz


Bueno, espero que te haya quedado claro como trabajar con los datetimes en Python.
