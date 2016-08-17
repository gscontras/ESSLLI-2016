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

## Course description

Much work in formal, compositional semantics follows the tradition of positing systematic but inflexible theories of meaning. However, in practice, the meaning we derive from language is heavily dependent on nearly all aspects of context, both linguistic and situational. To formally explain these nuanced aspects of meaning and better understand the compositional mechanism that delivers them, recent work in formal pragmatics recognizes semantics not as one of the final steps in meaning calculation, but rather as one of the first. For example, within the Bayesian Rational Speech-Acts framework refp:frankgoodman2012, speakers and listeners reason about each other's reasoning about the literal interpretation of utterances. The resulting interpretation necessarily depends on the literal interpretation of an utterance, but is not necessarily wholly determined by it. This move---reasoning about likely interpretations---provides ready explanations for complex phenomena ranging from metaphor refp:kaoetal2014metaphor and hyperbole refp:kaoetal2014 to the specification of thresholds in degree semantics refp:lassitergoodman2013.

The probabilistic pragmatics approach leverages the tools of structured probabilistic models formalized in a stochastic 𝞴-calculus to develop and refine a general theory of communication. The framework synthesizes the knowledge and approaches from diverse areas---formal semantics, Bayesian models of inference, formal theories of measurement, philosophy of language, etc.---into an articulated theory of language in practice. These new tools yield broader empirical coverage and richer explanations for linguistic phenomena through the recognition of language as a means of communication, not merely a vacuum-sealed formal system. By subjecting the heretofore off-limits land of pragmatics to articulated formal models, the rapidly growing body of research both informs pragmatic phenomena and enriches theories of semantics. Still, by operating primarily at the level of propositions, this approach necessarily eschews much of the compositional machinery that generates those propositions in the first place.

The present course serves to demonstrate that this semantic leveling is unnecessary; our models of meaning not only can, but should take into account the rich compositionality of the communicative system they are meant to characterize. The many sources of uncertainty in semantic composition are ripe for a probabilistic treatment, and we now have the tools to deliver one.

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

## Useful resources

- [Probabilistic Models of Cognition](https://probmods.org): An introduction to computational cognitive science and the probabilistic programming language Church
- [The Design and Implementation of Probabilistic Programming Languages](http://dippl.org): An introduction to probabilistic programming languages, WebPPL in particular
- [webppl.org](http://webppl.org): An online editor for WebPPL
- [WebPPL documentation](http://webppl.readthedocs.io/en/master/)
- [WebPPL-viz](http://probmods.github.io/webppl-viz/): A summary of the vizualization options in WebPPL
- [Forest](http://forestdb.org): A Repository for probabilistic models
- [RWebPPL](https://github.com/mhtess/rwebppl): If you would rather use WebPPL within R
