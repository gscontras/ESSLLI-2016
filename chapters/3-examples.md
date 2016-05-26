---
layout: chapter
title: Combining RSA and compositional semantics
description: "Jointly inferring parameters and interpretations"
---

### Day 3: Jointly inferring parameters and interpretations

  - Scope phenomena as uncertainty
  - Quantification and inference
  - Rational domain restriction

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
  return Enumerate(function() {
    var state = statePrior();
    var qState = QUDFun(QUD,state)
    condition(meaning(utterance,state,inverse));
    return qState;
  });
});

// Speaker (S)
var speaker = cache(function(inverse,state,QUD) {
  return Enumerate(function() {
    var utterance = utterancePrior();
    var qState = QUDFun(QUD,state);
    factor(alpha * literal(utterance,inverse,QUD).score([],qState));
    return utterance;
  });
});

// Pragmatic listener (L1)
var listener = cache(function(utterance) {
  return Enumerate(function() {
    var state = statePrior();
    var inverse = flip(0.5);
    var QUD = QUDPrior();
    factor(speaker(inverse,state,QUD).score([],utterance));
    return [inverse,state];
  });
});

viz.hist(listener("all-not"));

~~~~

Here we link to the [next chapter](4-composition.html).