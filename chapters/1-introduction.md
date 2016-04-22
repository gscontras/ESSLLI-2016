---
layout: chapter
title: Introduction
description: "An introductiion to the language understanding as probabilistic inference"
---

Day 1: Language understanding as probabilistic inference
  - Gricean pragmatics, probability theory, and utility functions 
  - The Rational Speech-Acts framework
  - Semantic underspecification as lexical uncertainty
    - Scalar adjectives


One of the most remarkable aspects of natural language is its compositionality: speakers generate arbitrarily complex meanings by stitching together their smaller, meaning-bearing parts. The compositional nature of language has served as the bedrock of semantic (indeed, linguistic) theory since its modern inception; \cite{montague1973} builds this principle into the bones of his semantics, demonstrating with his fragment how meaning gets constructed from a lexicon and some rules of composition. Since then, compositionality has continued to guide semantic inquiry: what are the meaning of the parts, and what is the nature of the mechanism that composes them? Put differently, what are the representations of the language we use, and what is the nature of the computational system that manipulates them?

Much work in formal, compositional semantics follows the tradition of positing systematic but inflexible theories of meaning. However, in practice, the meaning we derive from language is heavily dependent on nearly all aspects of context, both linguistic and situational. To formally explain these nuanced aspects of meaning and better understand the compositional mechanism that delivers them, recent work in formal pragmatics recognizes semantics not as one of the final steps in meaning calculation, but rather as one of the first. For example, within the Bayesian Rational Speech-Acts framework \citep{frankgoodman2012,goodmanstuhlmuller2013}, speakers and listeners reason about each other's reasoning about the literal interpretation of utterances. The resulting interpretation necessarily depends on the literal interpretation of an utterance, but is not necessarily wholly determined by it. This move---reasoning about likely interpretations---provides ready explanations for complex phenomena ranging from metaphor and hyperbole \citep{kaoetal2014metaphor,kaoetal2014} to the specification of thresholds in degree semantics \citep{lassitergoodman2013}.

The probabilistic pragmatics approach leverages the tools of structured probabilistic models formalized in a stochastic $\lambda$-calculus to develop and refine a general theory of communication. The framework synthesizes the knowledge and approaches from diverse areas---formal semantics, Bayesian models of inference, formal theories of measurement, philosophy of language, etc.---into an articulated theory of language in practice. These new tools yield broader empirical coverage and richer explanations for linguistic phenomena through the recognition of language as a means of communication, not merely a vacuum-sealed formal system. By subjecting the heretofore off-limits land of pragmatics to articulated formal models, the rapidly growing body of research both informs pragmatic phenomena and enriches theories of semantics. Still, by operating primarily at the level of propositions, this approach necessarily eschews much of the compositional machinery that generates those propositions in the first place.

The present course serves to demonstrate that this semantic leveling is unnecessary; our models of meaning not only can, but should take into account the rich compositionality of the communicative system they are meant to characterize. The many sources of uncertainty in semantic composition are ripe for a probabilistic treatment, and we now have the tools to deliver one.




A code box example:

~~~~
// Using the stochastic function `flip` we build a function that
// returns 'H' and 'T' with equal probability:

var coin = function(){
  return flip(.5) ? 'H' : 'T';
};

var flips = [coin(), coin(), coin()];
print("Some coin flips: " + flips);


// We now use `flip` to define a sampler for the geometric distribution:

var geometric = function(p) {
  return flip(p) ? 1 + geometric(p) : 1
};

var boundedGeometric = Enumerate(
  function(){ return geometric(0.5); }, 
  20);

print('Histogram of (bounded) Geometric distribution');
viz.auto(boundedGeometric);
~~~~

Here we link to the [next chapter](2-uncertainty.html).
