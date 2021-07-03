---
# Feel free to add content and custom Front Matter to this file.
# To modify the layout, see https://jekyllrb.com/docs/themes/#overriding-theme-defaults

layout: home
---

I am a software developer, with experience in frontend and backend development.

{% comment %}
This bit is just an experiment. I really like the idea of organizing posts, or
showing related posts at the end of a page. Who knows.
{% endcomment %}

## Post categories:

{% for tag in site.tags %}
### {{ tag[0] }}

{% for post in tag[1] %}
* [{{ post.title }}]({{ post.url }})
{% endfor %}

{% endfor %}
