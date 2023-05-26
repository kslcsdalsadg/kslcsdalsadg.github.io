---
layout: splash 
title:  "Añadir consentimiento de cookies en Jekyll"
date:   2023-05-26 15:11:04 +0200
categories: configuraciones 
header:
  overlay_image: /assets/images/cookies--banner.jpg
  overlay_filter: 0.5 
excerpt: |
  ¿Qué son las cookies y por qué tengo que pedir consentimiento para usarlas?
---
A parte de ser una fuente de azúcar, las cookies son pedazos de texto que los sitios web almacenan en la memoria de los navegadores y/o en sus propios servidores y que les permiten recordar información sobre tus visitas anteriores, lo que 
permite personalizar tu experiencia de navegación. 

De entrada no suena mal pero desgraciadamente los sitios web utilizan esta información para otras cosas, como por ejemplo personalizar la propaganda, lo que favorece que tus datos de navegación se envíen a terceros, se pierda la privacidad 
y se puedan usar tus datos de navegación para, por ejemplo, evaluar perfiles de navegación.

Hace ya algún tiempo la UE aprobó, no sin bastantes problemas, una directiva que obliga a los sitios web a obtener el consentimiento expreso de los usuarios para poder instalar cookies en sus navegadores y/o para realizar seguimiento online.

Así, todos los sitios web, independientemente de que sean gestionados por empresas o particulares, están obligados a obtener este consentimiento si usan cookies en sus sitios web o si usan herramientas que las utilizan, como por ejemplo Google Analytics.

Existen multitud de herramientas que permiten gestionar este consentimiento pero personalmente utilizo [Glow Cookies](https://github.com/manucaralmo/GlowCookies), porque es muy fácil de integrar en cualquier sitio web y porque usa la [licencia GPL 3.0](https://es.wikipedia.org/wiki/GNU_General_Public_License).

La integración de Glow Cookies en un sitio web es muy sencilla, ya que simplemente deberemos incluir la siguiente porción de código en todas las páginas de nuestro sitio:

{% highlight html %}
<script src="https://cdn.jsdelivr.net/gh/manucaralmo/GlowCookies@3.1.8/src/glowCookies.min.js"></script>

<script>
    glowCookies.start('es', {
        style: 1,
        analytics: 'G-XXXXXXXXXXXXXXX',
        policyLink: 'https://link-to-your-policy.com',
        hideAfterClick: true,
        position: 'right'
    });
</script>
{% endhighlight %}

En mi caso uso el Jekyll y el tema Minimal Mistakes, por lo que la integración es áun más sencilla, pues basta incluir esta porción de código en el archivo *_includes/head/custom.html*, que queda como sigue:

{% highlight html %}
<!-- start custom head snippets -->

<script src="https://cdn.jsdelivr.net/gh/manucaralmo/GlowCookies@3.1.8/src/glowCookies.min.js"></script>
<script>
    glowCookies.start('es', {
        style: 1,
        analytics: 'G-XXXXXXXXXXXXXXX',
        policyLink: 'https://link-to-your-policy.com',
        hideAfterClick: true,
        position: 'right'
    });
</script>

<!-- insert favicons. use https://realfavicongenerator.net/ -->

<!-- end custom head snippets -->
{% endhighlight %}
