---
layout: post
title:  2FA con Python
date: 2022-10-19 19:00:00 +0200
author: Quique Porta
comments: true
share: true
categories:
    - Programación
tags:
    - python
    - programación
---

![](https://upload.wikimedia.org/wikipedia/commons/thumb/8/81/Electronic_Frontier_Foundation_video_conferencing_background_01.png/512px-Electronic_Frontier_Foundation_video_conferencing_background_01.png)

Esta semana en [Agroptima](https://www.agroptima.com) hemos tenido que implementar 2FA (two-factor authentication) para algunos usuarios. La verdad es que no ha sido muy complicado integrarlo en nuestra página web.

Hemos utilizado la libreria de Python [PyOTP](https://github.com/pyauth/pyotp).
Es una libreria **muy sencilla** para generar y verificar one-time passwords.

[PyOTP Documentation](https://pyauth.github.io/pyotp/)

La implementación que hemos escogido es la de Time-based OTPs. Esta implementación consiste en que en base a un *secret* y un *timestamp*, el sistema genera un número válido para unos pocos segundos.
Este número es el que te aparece en la aplicación de OTP que tengas en tu movil.
Lo que debes hacer es comparar el número que te envia el usuario con el que te ha generado el sistema y si coinciden, pues adelante.

## Generar secretos

Por cada usuario tienes que generar un *secret* distinto que será el que se utilizará para las aplicaciones de OTP como [andOTP](https://github.com/andOTP/andOTP) o Authy.

Para generar un *secret* solo tienes que ejecutar esto:
{% highlight python linenos%}
import pyotp
secret = pyotp.random_base32()  # 'SLB3AFCLV7A3RLG7P33ILK2RHNKLN2ZK'
{% endhighlight %}


El usuario, en su aplicación de OTP, debe agregar una cuenta con ese código y a partir de ese momento, la aplicación le irá ofreciendo números aleatorios cada 30 segundos.

## Generar código QR

Introducir ese secreto en las aplicaciones OTP puede ser un poco pesado, así que para facilitar la tarea a los usuarios puedes generar una imagen de un código QR con los datos necesarios.

Para generar la imagen del código QR utilizamos la libreria [qrcode](https://github.com/lincolnloop/python-qrcode).

{% highlight python linenos%}
import base64
from io import BytesIO
import pyotp
import qrcode

def generate_base64_qr_image(self, secret: str, email: str, company: str):
    totp_uri = pyotp.totp.TOTP(secret).provisioning_uri(
        name=email, issuer_name=company
    )

    image = qrcode.make(totp_uri)

    buffered = BytesIO()

    image.save(buffered, format="PNG")

    return base64.b64encode(buffered.getvalue()).decode('utf-8')
{% endhighlight %}

El resultado de esta función, que es una imagen codificada en base64, puedes utilizarlo en una página HTML de este modo.

{% highlight html linenos%}
{% raw %}
<img src="data:image/jpg;base64,{{ base64_qr_image }}">
{% endraw %}
{% endhighlight %}

## Verificar one-time password

Pues ya solo queda implementar en el proceso de login de tu sitio web algo como esto:

{% highlight python linenos%}
{% raw %}
# ...
if pyotp.TOTP(SECRETO_USUARIO).verify(CODIGO_ENVIADO_POR_EL_USUARIO):
    # Autenticado
else:
    # No autenticado
# ...
{% endraw %}
{% endhighlight %}

**Importante**: Recuerda guardar los secretos de los usuarios encriptados de alguna manera para que no sean facilmente accesibles por gente no deseada.

[^1]: <http://en.wikipedia.org/wiki/Syntax_highlighting>
