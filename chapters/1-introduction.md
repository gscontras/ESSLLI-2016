---
layout: chapter
title: Introducing the Rational Speech-Acts framework
description: "An introduction to language understanding as Bayesian inference"
---

### Day 1: Language understanding as Bayesian inference

<!--   - Gricean pragmatics, probability theory, and utility functions 
  - The basic Rational Speech-Acts framework
  - Background knowledge in language understanding -->


<!-- One of the most remarkable aspects of natural language is its compositionality: speakers generate arbitrarily complex meanings by stitching together their smaller, meaning-bearing parts. The compositional nature of language has served as the bedrock of semantic (indeed, linguistic) theory since its modern inception; \cite{montague1973} builds this principle into the bones of his semantics, demonstrating with his fragment how meaning gets constructed from a lexicon and some rules of composition. Since then, compositionality has continued to guide semantic inquiry: what are the meaning of the parts, and what is the nature of the mechanism that composes them? Put differently, what are the representations of the language we use, and what is the nature of the computational system that manipulates them? -->

The Rational Speech-Act (RSA) framework views communication as recursive reasoning between a speaker and a listener. The listener interprets the speaker’s utterance by reasoning about a cooperative speaker trying to inform a naive listener about some state of affairs. Using Bayesian inference, the listener infers what the state of the world is likely to be given that a speaker produced some utterance, knowing that the speaker is reasoning about how a listener is most likely to interpret that utterance. Thus, we have at least three levels of inference. At the top, the sophisticated, **pragmatic listener**, *L<sub>1</sub>*, reasons about the **pragmatic speaker**, *S<sub>1</sub>*, and infers the state of the world *s* given that the speaker chose to produce the utterance *u*. The speaker chooses *u* by maximizing the probability that a naive, **literal listener**, *L<sub>0</sub>*, would correctly infer the state of the world *s* given the literal meaning of *u*.

In more detail, the pragmatic listener *L<sub>1</sub>* computes the probability of a state *s* given some utterance *u*. By reasoning about the speaker *S<sub>1</sub>*, this probability is proportional to the probability that *S<sub>1</sub>* would choose to utter *u* to communicate about the state *s*, together with the prior probability of *s* itself.

 <center>The pragmatic listener: *P<sub>L<sub>1</sub></sub>(s|u) ∝ P<sub>S<sub>1</sub></sub> (u|s) · P(s)*</center>

The speaker *S<sub>1</sub>* desires to choose an utterance *u* that would most effectively communicate some state *s* to a hypothesized literal listener *L<sub>0</sub>*. In other words, *S<sub>1</sub>* wants to minimize the effort *L<sub>0</sub>* would need to arrive at *s* from *u*, all while being efficient at communicating. This trade-off between efficacy and efficiency is not trivial: speakers could always use minimal ambiguity, but unambiguous utterances tend toward the unwieldy, and, very often, unnecessary. *S<sub>1</sub>* thus seeks to minimize the surprisal of *s* given *u* for the literal listener *L<sub>0</sub>*, while bearing in mind the utterance cost, *C(u)*.

Speakers act in accordance with the speaker’s utility function *U<sub>S<sub>1</sub></sub>*: utterances are more useful at communicating about some state as surprisal and utterance cost decrease.

 <center>The speaker’s utility function: *U<sub>S<sub>1</sub></sub>(u;s) = log(L<sub>0</sub>(s|u)) − C(u)*</center>

With this utility function in mind, *S<sub>1</sub>* computes the probability of an utterance *u* given some state *s* in proportion to the speaker’s utility function *US<sub>1</sub>*. The term $α > 0$ controls the speaker’s optimality, that is, the speaker’s rationality in choosing utterances. (*α* corresponds to the temperature parameter of S<sub>1</sub>’s soft-max optimization.)

<center>The pragmatic speaker: *P<sub>S<sub>1</sub></sub>(u|s) ∝ exp(αU<sub>S<sub>1</sub></sub>(u;s))*</center>

At the base of this reasoning, the naive, literal listener *L<sub>0</sub>* interprets an utterance according to its meaning. That is, *L<sub>0</sub>* computes the probability of *s* given *u* according to the semantics of *u* and the prior probability of *s*. A standard view of the semantic content of an utterance suffices: a mapping from states of the world to truth values.

<center>The literal listener: *P<sub>L<sub>0</sub></sub>(s|u) ∝ ⟦u⟧(s) · P(s)*</center>

Within the RSA framework, communication is thus modeled as in Fig. 1, where L<sub>1</sub> reasons about S<sub>1</sub>’s reasoning about a hypothetical L<sub>0</sub>.

![Fig. 1: Graphical representation of the Bayesian RSA model.](../images/rsa_schema.pdf)
<center>Fig. 1: Graphical representation of the Bayesian RSA model.</center>

In its initial formulation, [Frank and Goodman (2012)](../papers/frankgoodman2012.pdf) use the basic RSA framework to model referent choice in efficient communication. To see the mechanism at work, imagine a referential communication game with three objects, as in Fig. 2. 

![Fig. 2: Example referential communication scenario from Frank & Goodman (2012). Speakers choose a single word, *u*, to signal an object, *s*.](../images/rsa_scene.pdf)
<center>Fig. 2: Example referential communication scenario from Frank & Goodman (2012). Speakers choose a single word, *u*, to signal an object, *s*.</center>

Suppose a speaker wants to signal an object, but only has a single word with which to do so. Applying the RSA model schematized in Fig. 1 to the communication scenario in Fig. 2, the speaker *S<sub>1</sub>* chooses a word *u* to best signal an object *s* to a literal listener *L<sub>0</sub>*, who interprets *u* in proportion to the prior probability of naming objects in the scenario (i.e., to an object’s salience, *P(s)*). The pragmatic listener *L<sub>1</sub>* reasons about the speaker’s reasoning, and interprets *u* accordingly. By formalizing the contributions of salience and efficiency, the RSA framework provides an information-theoretic definition of informativeness in pragmatic inference. <!-- This definition will prove crucial in understanding the contribution of contextual pre- dictability of collective properties in the interpretation of plural predication. -->

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
var meaning = function(u, w){
  return u == "blue" ? u==w.color :
  u == "green" ? u==w.color :
  u == "circle" ? u==w.obj :
  u == "square" ? u==w.obj :
  true
}

// literal listener
var literalListener = function(utt){
  Infer({method:"enumerate"},
        function(){
    var world = uniformDraw(worlds)
    condition(meaning(utt, world))
    return world
  })
}

// pragmatic speaker
var speaker = function(world){
  Infer({method:"enumerate"},
        function(){
    var utt = uniformDraw(utterances)
    var L<sub>0</sub> = literalListener(utt)
    // condition (L<sub>0</sub> == world)
    factor(L<sub>0</sub>.score(world)) // alpha * log p(w | u)
    return utt
  })
}

// pragmatic listener
var pragmaticListener = function(utt){
  Infer({method:"enumerate"},
        function(){
    var world = uniformDraw(worlds)
    var s1 = speaker(world)
    factor(5*s1.score(utt))
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

XXX something about scalar implicature
Goodman and Stuhlmüller (2013) basic Scalar Implicature model:

~~~~
// Here is the code from the Goodman and Stuhlmüller basic SI model

// speaker optimality parameter
var alpha = 2;

// possible states of the world
var statePrior = function() {
  return uniformDraw([0, 1, 2, 3]);
};

// possible utterances
var utterancePrior = function() {
  return uniformDraw(['all', 'some', 'none']);
};

// meaning funtion to interpret the utterances
var literalMeanings = {
  all: function(state) { return state === 3; },
  some: function(state) { return state > 0; },
  none: function(state) { return state === 0; }
};

// literal listener
var literalListener = cache(function(utt) {
  return Infer({method:"enumerate"},
  function(){
    var state = statePrior();
    var meaning = literalMeanings[utt];
    condition(meaning(state));
    return state;
  })
});

// pragmatic speaker
var speaker = cache(function(state) {
  return Infer({method:"enumerate"},
  function(){
    var utt = utterancePrior();
    factor(alpha * literalListener(utt).score(state));
    return utt;
  });
});

// pragmatic listener
var pragmaticListener = cache(function(utt) {
  return Infer({method:"enumerate"},
  function(){
    var state = statePrior();
    factor(speaker(state).score(utt));
    return state;
  })
});

print("pragmatic listener's interpretation of 'some':")
viz.auto(pragmaticListener('some'));

~~~~

Goodman and Stuhlmüller (2013) speaker-access Scalar Implicature model:

~~~~
// Here is the code from the Goodman and Stuhlmüller speaker-access SI model

// red apple base rate
var baserate = 0.8

// state builder
var substatePriors = function() {
  var s1 = flip(baserate);
  var s2 = flip(baserate);
  var s3 = flip(baserate);
  return [s1,s2,s3];
}

// speaker belief function
var belief = function(actualState, access) {
  var fun = function(access,state,prior) {
    var sp = (substatePriors());
    return access ? state : _.sample(sp)
  }
  return map2(fun,access,actualState);
}

// state prior
var statePrior = function() {
  return substatePriors();
} 

// utterance prior
var utterancePrior = function() {
  uniformDraw(['all','some','none']);
}

// meaning funtion to interpret utterances
var literalMeanings = {
  all: function(state) { return all(function(s){s},state); },
  some: function(state) { return any(function(s){s},state); },
  none: function(state) { return all(function(s){s==false},state); }
};

// literal listener
var literalListener = cache(function(utt) {
  return Infer({method:"enumerate"},
               function(){
    var state = statePrior();
    var meaning = literalMeanings[utt];
    condition(meaning(state));
    return state;
  })
});

// pragmatic speaker
var speaker = cache(function(access,state) {
  return Infer({method:"enumerate"},
               function(){
    var utt = utterancePrior();
    var beliefState = belief(state,access);
    factor(literalListener(utt).score(beliefState));
    return utt;
  });
});

// tally up the state
var numTrue = function(state) {
  var fun = function(x) {
    x ? 1 : 0;
  }
  return sum(map(fun,state));
}

// pragmatic listener
var pragmaticListener = cache(function(access,utt) {
  return Infer({method:"enumerate"},
               function(){
    var state = statePrior();
    factor(speaker(access,state).score(utt));
    return numTrue(state);
  })
});

print("pragmatic listener for a partial-access speaker:")
viz.auto(pragmaticListener([true,true,false],'some'))
print("pragmatic listener for a full-access speaker:")
viz.auto(pragmaticListener([true,true,true],'some'))

~~~~


Here we link to the [next chapter](2-uncertainty.html).
