---
layout: default
---

## Composition in Probabilistic Language Understanding

**Greg Scontras** -- [*g.scontras@uci.edu*](mailto:g.scontras@uci.edu)

**Michael Henry Tessler** -- [*mtessler@stanford.edu*](mailto:mtessler@stanford.edu)

  - Area: *Language and Logic*
  - Level: *Advanced*
  - Week: *2*
  - Time: *09:00-10:30*
  - Location: *TBD*

**Summary:** Recent advances in computational cognitive science (i.e., simulation-based probabilistic programs) have paved the way for significant progress in formal, implementable models of pragmatics. Rather than describing a pragmatic reasoning process, these models articulate and implement one, deriving both qualitative and quantitative predictions of human behavior---predictions that consistently prove correct, demonstrating the viability and value of the framework. However, many of these models operate at the utterance level, taking as their starting point whatever the compositional semantics delivers to them as the meaning of a proposition; the models deliberately avoid the composition of the literal interpretations over which they operate. We aim to change that, further shrinking the theoretical and practical distance between semantics and pragmatics by incorporating *both* within a single model of meaning in language. To that end, this course examines the ways that a semantic compositional mechanism may be modeled dynamically and probabilistically, within the broader framework of computational cognitive science.

## Course outline

{% assign sorted_pages = site.pages | sort:"name" %}

{% for p in sorted_pages %}
    {% if p.hidden %}
    {% else %}
        {% if p.layout == 'chapter' %}
1. **<a class="chapter-link" href="{{ site.baseurl }}{{ p.url }}">{{ p.title }}</a>**<br>
        <em>{{ p.description }}</em>
        {% endif %}
    {% endif %}
{% endfor %}
