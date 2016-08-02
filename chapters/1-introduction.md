---
layout: chapter
title: Introducing the Rational Speech-Acts framework
description: "An introduction to language understanding as Bayesian inference"
---

### Day 1: Language understanding as Bayesian inference

  - Gricean pragmatics, probability theory, and utility functions 
  - The basic Rational Speech-Acts framework
  - Background knowledge in language understanding


<!-- One of the most remarkable aspects of natural language is its compositionality: speakers generate arbitrarily complex meanings by stitching together their smaller, meaning-bearing parts. The compositional nature of language has served as the bedrock of semantic (indeed, linguistic) theory since its modern inception; \cite{montague1973} builds this principle into the bones of his semantics, demonstrating with his fragment how meaning gets constructed from a lexicon and some rules of composition. Since then, compositionality has continued to guide semantic inquiry: what are the meaning of the parts, and what is the nature of the mechanism that composes them? Put differently, what are the representations of the language we use, and what is the nature of the computational system that manipulates them? -->

Much work in formal, compositional semantics follows the tradition of positing systematic but inflexible theories of meaning. However, in practice, the meaning we derive from language is heavily dependent on nearly all aspects of context, both linguistic and situational. To formally explain these nuanced aspects of meaning and better understand the compositional mechanism that delivers them, recent work in formal pragmatics recognizes semantics not as one of the final steps in meaning calculation, but rather as one of the first. For example, within the Bayesian Rational Speech-Acts framework ([Frank & Goodman, 2012](../papers/frankgoodman2012.pdf); [Goodman & Stuhmüller, 2013](../papers/goodmanstuhlmuller2013.pdf)), speakers and listeners reason about each other's reasoning about the literal interpretation of utterances. The resulting interpretation necessarily depends on the literal interpretation of an utterance, but is not necessarily wholly determined by it. This move---reasoning about likely interpretations---provides ready explanations for complex phenomena ranging from metaphor and hyperbole ([Kao et al., 2014a](../papers/kaoetal2014metaphor.pdf); [Kao et al., 2014b](../papers/kaoetal2014.pdf)) to the specification of thresholds in degree semantics ([Lassiter & Goodman, 2013](../papers/lassitergoodman2013.pdf)).

The probabilistic pragmatics approach leverages the tools of structured probabilistic models formalized in a stochastic 𝞴-calculus to develop and refine a general theory of communication. The framework synthesizes the knowledge and approaches from diverse areas---formal semantics, Bayesian models of inference, formal theories of measurement, philosophy of language, etc.---into an articulated theory of language in practice. These new tools yield broader empirical coverage and richer explanations for linguistic phenomena through the recognition of language as a means of communication, not merely a vacuum-sealed formal system. By subjecting the heretofore off-limits land of pragmatics to articulated formal models, the rapidly growing body of research both informs pragmatic phenomena and enriches theories of semantics. Still, by operating primarily at the level of propositions, this approach necessarily eschews much of the compositional machinery that generates those propositions in the first place.

The present course serves to demonstrate that this semantic leveling is unnecessary; our models of meaning not only can, but should take into account the rich compositionality of the communicative system they are meant to characterize. The many sources of uncertainty in semantic composition are ripe for a probabilistic treatment, and we now have the tools to deliver one.




Basic Frank and Goodman (2012) RSA model:

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
    var L0 = literalListener(utt)
    // condition (L0 == world)
    factor(L0.score(world)) // alpha * log p(w | u)
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
