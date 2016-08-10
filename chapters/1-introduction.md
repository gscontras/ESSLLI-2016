---
layout: chapter
title: Introducing the Rational Speech-Act framework
description: "An introduction to language understanding as Bayesian inference"
---

### Day 1: Language understanding as Bayesian inference

<!--   - Gricean pragmatics, probability theory, and utility functions 
  - The basic Rational Speech-Acts framework
  - Background knowledge in language understanding -->


<!-- One of the most remarkable aspects of natural language is its compositionality: speakers generate arbitrarily complex meanings by stitching together their smaller, meaning-bearing parts. The compositional nature of language has served as the bedrock of semantic (indeed, linguistic) theory since its modern inception; \cite{montague1973} builds this principle into the bones of his semantics, demonstrating with his fragment how meaning gets constructed from a lexicon and some rules of composition. Since then, compositionality has continued to guide semantic inquiry: what are the meaning of the parts, and what is the nature of the mechanism that composes them? Put differently, what are the representations of the language we use, and what is the nature of the computational system that manipulates them? -->

The Rational Speech-Act (RSA) framework views communication as recursive reasoning between a speaker and a listener. The listener interprets the speaker’s utterance by reasoning about a cooperative speaker trying to inform a naive listener about some state of affairs. Using Bayesian inference, the listener infers what the state of the world is likely to be given that a speaker produced some utterance, knowing that the speaker is reasoning about how a listener is most likely to interpret that utterance. Thus, we have at least three levels of inference. At the top, the sophisticated, **pragmatic listener**, $$L_{1}$$, reasons about the **pragmatic speaker**, $$S_{1}$$, and infers the state of the world $$s$$ given that the speaker chose to produce the utterance $$u$$. The speaker chooses $$u$$ by maximizing the probability that a naive, **literal listener**, $$L_{0}$$, would correctly infer the state of the world $$s$$ given the literal meaning of $$u$$.

At the base of this reasoning, the naive, literal listener $$L_{0}$$ interprets an utterance according to its meaning. That is, $$L_{0}$$ computes the probability of $$s$$ given $$u$$ according to the semantics of $$u$$ and the prior probability of $$s$$. A standard view of the semantic content of an utterance suffices: a mapping from states of the world to truth values.

<!-- <center>The literal listener: P<sub>L<sub>0</sub></sub>(s|u) ∝ ⟦u⟧(s) · P(s)</center> -->

$$P_{L_{0}}(s\mid u) \propto [[u]](s) \cdot P(s)$$

<!-- \mid -->

~~~~
// literal listener
var literalListener = function(utterance){
  Infer({method:"enumerate"},
        function(){
    var world = worldPrior();
    condition(meaning(utterance, world))
    return world
  })
}
~~~~

The speaker $$S_{1}$$ desires to choose an utterance $$u$$ that would most effectively communicate some state of the world $$s$$ to the hypothesized literal listener $$L_{0}$$. In other words, $$S_{1}$$ wants to minimize the effort $$L_{0}$$ would need to arrive at $$s$$ from $$u$$, all while being efficient at communicating. This trade-off between efficacy and efficiency is not trivial: speakers could always use minimal ambiguity, but unambiguous utterances tend toward the unwieldy, and, very often, unnecessary. $$S_{1}$$ thus seeks to minimize the surprisal of $$s$$ given $$u$$ for the literal listener $$L_{0}$$, while bearing in mind the utterance cost, $$C(u)$$.

Speakers act in accordance with the speaker’s utility function $$U_{S_{1}}$$: utterances are more useful at communicating about some state as surprisal and utterance cost decrease.

$$U_{S_{1}}(u; s) = log(L_{0}(s\mid u)) - C(u)$$

With this utility function in mind, $$S_{1}$$ computes the probability of an utterance $$u$$ given some state $$s$$ in proportion to the speaker’s utility function $$U_{S_{1}}$$. The term $$\alpha > 0$$ controls the speaker’s optimality, that is, the speaker’s rationality in choosing utterances. ($$\alpha$$ corresponds to the temperature parameter of $$S_{1}$$’s soft-max optimization.)

<!-- <center>The pragmatic speaker: P<sub>S<sub>1</sub></sub>(u|s) ∝ exp(αU<sub>S<sub>1</sub></sub>(u;s))</center> -->

$$P_{S_{1}}(u\mid s) \propto exp(\alpha U_{S_{1}}(u; s))$$

~~~~
// pragmatic speaker
var speaker = function(world){
  Infer({method:"enumerate"},
        function(){
    var utterance = utterancePrior();
    factor(literalListener(utterance).score(world))
    return utterance
  })
}
~~~~

The pragmatic listener $$L_{1}$$ computes the probability of a state $$s$$ given some utterance $$u$$. By reasoning about the speaker $$S_{1}$$, this probability is proportional to the probability that $$S_{1}$$ would choose to utter $$u$$ to communicate about the state $$s$$, together with the prior probability of $$s$$ itself.

<!-- <center>The pragmatic listener: P<sub>L<sub>1</sub></sub>(s|u) ∝ P<sub>S<sub>1</sub></sub>(u|s) · P(s)</center> -->

$$P_{L_{1}}(s\mid u) \propto P_{S_{1}} \cdot P(s)$$

~~~~
// pragmatic listener
var pragmaticListener = function(utterance){
  Infer({method:"enumerate"},
        function(){
    var world = worldPrior();
    factor(speaker(world).score(utterance))
    return world
  })
}
~~~~



Within the RSA framework, communication is thus modeled as in Fig. 1, where $$L_{1}$$ reasons about $$s_{1}$$’s reasoning about a hypothetical $$L_{0}$$.

<img src="../images/rsa_schema.pdf" alt="Fig. 1: Graphical representation of the Bayesian RSA model." style="width: 400px;"/>
<center>Fig. 1: Bayesian RSA schema.</center>


#### Simple referential communication

In its initial formulation, reft:frankgoodman2012 use the basic RSA framework to model referent choice in efficient communication. To see the mechanism at work, imagine a referential communication game with three objects, as in Fig. 2. 

<img src="../images/rsa_scene.pdf" alt="Fig. 2: Example referential communication scenario from Frank & Goodman (2012). Speakers choose a single word, $$u$$, to signal an object, $$s$$." style="width: 400px;"/>
<center>Fig. 2: Example referential communication scenario from Frank and Goodman. Speakers choose a single word, <i>u</i>, to signal an object, <i>s</i>.</center>

Suppose a speaker wants to signal an object, but only has a single word with which to do so. Applying the RSA model schematized in Fig. 1 to the communication scenario in Fig. 2, the speaker $$S_{1}$$ chooses a word $$u$$ to best signal an object $$s$$ to a literal listener $$L_{0}$$, who interprets $$u$$ in proportion to the prior probability of naming objects in the scenario (i.e., to an object’s salience, P(s)). The pragmatic listener $$L_{1}$$ reasons about the speaker’s reasoning, and interprets $$u$$ accordingly. By formalizing the contributions of salience and efficiency, the RSA framework provides an information-theoretic definition of informativeness in pragmatic inference. <!-- This definition will prove crucial in understanding the contribution of contextual pre- dictability of collective properties in the interpretation of plural predication. -->

~~~~
// Here is the code from the Frank and Goodman RSA model

// possible states of the world
var worlds = [
  {obj: "square", color: "blue"},
  {obj: "circle", color: "blue"},
  {obj: "square", color: "green"}
]

// possible one-word utterances
var utterances = ["blue","green","square","circle"]

// meaning funtion to interpret the utterances
var meaning = function(utterance, world){
  return utterance == "blue" ? utterance==world.color :
  utterance == "green" ? utterance==world.color :
  utterance == "circle" ? utterance==world.obj :
  utterance == "square" ? utterance==world.obj :
  true
}

// literal listener
var literalListener = function(utterance){
  Infer({method:"enumerate"},
        function(){
    var world = uniformDraw(worlds)
    condition(meaning(utterance, world))
    return world
  })
}

// pragmatic speaker
var speaker = function(world){
  Infer({method:"enumerate"},
        function(){
    var utterance = uniformDraw(utterances)
    factor(literalListener(utterance).score(world))
    return utterance
  })
}

// pragmatic listener
var pragmaticListener = function(utterance){
  Infer({method:"enumerate"},
        function(){
    var world = uniformDraw(worlds)
    factor(speaker(world).score(utterance))
    return world
  })
}

print("literal listener's interpretation of 'blue':")
viz.table(literalListener( "blue"))
print("speaker's utterance distribution for a blue circle:")
viz.table(speaker({obj:"circle", color: "blue"}))
print("pragmatic listener's interpretation of 'blue':")
viz.table(pragmaticListener("blue"))

~~~~


In the [next chapter](2-pragmatics.html), we'll see how RSA models have been developed to model more complex cases of language understanding.
