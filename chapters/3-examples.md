---
layout: chapter
title: Combining RSA and compositional semantics
description: "Jointly inferring parameters and interpretations"
---

### Day 3: Jointly inferring parameters and interpretations

<!--   - Scope phenomena as uncertainty
  - Quantification and inference
  - Rational domain restriction -->

In the last chapter, we looked at a model of semantic parsing in probablistic language understanding built on combinatory categorial grammar. The model constructed literal interpretations and verifying worlds from the semantic atoms of sentences. However, whereas the model explicitly targeted comositional semantics, it stopped at the level of the literal listener, the base of RSA reasoning. Next, we consider a different approach to approximating compositional semantics within the RSA framework.

What we want is a way for our models of language understanding to target sub-propositional aspects of meaning. We might wind up going the route of the fully-compositional but admittedly-unwieldy CCG semantic parser, but for present purposes an easier path presents itself: parameterizing our meaning function so that conversational agents can reason jointly over interpretations and the parameters that fix them. To see how this move serves our aims, consider the case of quantifier scope ambiguities.


~~~~
// Here is the code for the quantifier scope model

// speaker optimality
var alpha = 1;

// possible utterances
var utterances = ["null","all-not"];
var cost = function(utterance) {
  return 1;
};
var utterancePrior = function() {
  	return utterances[discrete(map(function(u) {
  	  return Math.exp(-cost(u));
  	}, utterances))];
};

// possible world states
var states = [0,1,2];
var statePrior = function() {
  uniformDraw(states);
}

// QUDs
var QUDs = ["null","succeed?","fail?"];
var QUDPrior = function() {
  uniformDraw(QUDs);
}
var QUDFun = function(QUD,state) {
  return QUD == "succeed?" ? state == 2 :
  QUD == "fail?" ? state == 0 :
  state;
};

// meaning function
var meaning = function(utterance,state,inverse) {
  return utterance == "all-not" ?
    inverse ? state < 2 : 
  state == 0 :
  true;
};

// Literal listener (L0)
var literal = cache(function(utterance,inverse,QUD) {
  return Infer({method:"enumerate"},
  function(){
    var state = statePrior();
    var qState = QUDFun(QUD,state)
    condition(meaning(utterance,state,inverse));
    return qState;
  });
});

// Speaker (S)
var speaker = cache(function(inverse,state,QUD) {
  return Infer({method:"enumerate"},
  function(){
    var utterance = utterancePrior();
    var qState = QUDFun(QUD,state);
    factor(alpha * literal(utterance,inverse,QUD).score(qState));
    return utterance;
  });
});

// Pragmatic listener (L1)
var listener = cache(function(utterance) {
  return Infer({method:"enumerate"},
  function(){
    var state = statePrior();
    var inverse = flip(0.5);
    var QUD = QUDPrior();
    factor(speaker(inverse,state,QUD).score(utterance));
    return [inverse,state];
  });
});

viz.hist(listener("all-not"));

~~~~




Here we link to the [next chapter](4-ontology.html).