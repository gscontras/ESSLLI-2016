---
layout: chapter
title: Modeling semantics
description: "Building the literal interpretations"
---

### Day 2: Modeling compsitional semantics

<!--   - Building the literal interpretations
  - Compositional mechanisms and semantic types
    - Functional Application; Predicate Modification 
  - The compositional semantics example from DIPPL -->


One of the most remarkable aspects of natural language is its compositionality: speakers generate arbitrarily complex meanings by stitching together their smaller, meaning-bearing parts. The compositional nature of language has served as the bedrock of semantic (indeed, linguistic) theory since its modern inception; Montague builds this principle into the bones of his semantics, demonstrating with his fragment how meaning gets constructed from a lexicon and some rules of composition. Since then, compositionality has continued to guide semantic inquiry: what are the meaning of the parts, and what is the nature of the mechanism that composes them? Put differently, what are the representations of the language we use, and what is the nature of the computational system that manipulates them?

So far, the models we have considered operate at the level of full utterances.  These models assume conversational agents who reason over propositional content to arrive at enriched interpretations: "I want the blue one," "Some of the apples are red," "XXX irony," etc. Now, let's approach meaning from the opposite direction: building the literal interpretations (and our model of the world that verifies them) from the bottom up: [semantic parsing](http://dippl.org/examples/semanticparsing.html).

In the [next chapter](3-examples.html), we consider a different approach to approximating semantic composition within the RSA framework.