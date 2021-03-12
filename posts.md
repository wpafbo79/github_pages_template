---
layout: enhanced_architect
---
# Posts

{% for post in site.posts %}
 -  [{{ post.title }}]({{ site.baseurl }}{{ post.url }}) : {{ post.date }}
{% endfor %}
