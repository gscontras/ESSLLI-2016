---
layout: chapter
title: Generic language
description: "Extending and sythensizing extant models of predication"
---

### Day 4: Pragmatics rescues underspecified semantics 

## The philosophical problem

Generic language (e.g. *Swans are white.*) is a simple and ubiquitous way to communicate generalizations about categories.  Linguists, philosophers, and psychologists have scratched their collective heads for decades, trying to figure out what makes a generic sentence true or false. At first glance, generics feel like universally-quantified statements as in *All swans are white*.  Unlike universals, however, generics are resilient to counter-examples (e.g., *Swans are white* even though there are black swans).  Our intuitions then fall back to something more vague like *Swans, in general, are white* because indeed most swans are white. But mosquitos, in general, do not carry malaria, yet everyone agrees *Mosquitos carry malaria*.

Indeed, it appears that any truth conditions stated in terms of how common the property is within the kind violates intuitions. Consider the birds: for a bird, being female practically implies you will lay eggs (the properties are present in the same proportion), yet we say things like *Birds lay eggs* and we do not say things like *Birds are female*.

reft:tessler2016manuscript propose that the core meaning of a generic statement is simple, but underspecified, and that general principles of communication may be used to resolve precise meaning in context. In particular, they developed a model that describes pragmatic reasoning about the degree of prevalence required to assert the generic.

#### A pragmatic model of generic language

This is a model of generic language used in reft:tessler2016manuscript .

The model takes the generic $$[\![\text{K has F}]\!]$$ to mean the prevalence of property F within kind K (i.e., $$P(F \mid K)$$) is above some threshold. This threshold is thought to be in general unknown (`threshold = uniform(0,1)`) and must be inferred in context. 

Context here takes the form of the listener and speakers shared beliefs about the property in question. The shape of this distribution affects the listener's interpretation, because the threshold must be calibrated to make utterances truthful and informative. The shape of this distribution varies significantly among different properties (e.g. *lays eggs*, *carries malaria*), and may be the result of a deeper conceptual model of the world. For instance, if speakers and listeners believe that some kinds have a causal mechanism that could give rise to the property, while others do not, then we would expect the prior to be structured as a mixture distribution (Cf. Griffiths & Tenenbaum, 2005). 

First, let's try to understand the prior.

#### Prior model

The following model `structuredPriorModel` formalizes the above ideas.
`potential` is a mixture parameter that governs how the property's potential to be present in a kind (or, the frequency of a property across kinds). For example, "is female" has a high potential to be present in a kind; while "lays eggs" has less potential (owing to the fact that a lot of animals do not have any members who lay eggs). 
"Carries malaria" has a very potential to be present.
`prevalenceWhenPresent` is the *mean prevelence when the property is present*.
Knowing that the property is present in a kind, what % of the kind do you expect to have it? 

- Learning that one creature (e.g., a fep) has wings, what % of feps do you think have wings? 
- Learning that a fep is female, what % of feps do you think are female?

Finally, `concentrationWhenPresent` is the concentration (conceptually, the inverse variance) of that prevalence when present.

It is high for properties that present in almost every kind in exactly the same proportion (e.g. "is female"). 
It is lower when there is more uncertainty about exactly how many within a kind are expected to have the property.


~~~~
///fold:
// discretized range between 0 - 1
var bins = _.range(0.01, 1, 0.025);
///

// function returns a discretized Beta PDF
var discretizeBeta = function(g, d){
  var shape_alpha =  g * d
  var shape_beta = (1-g) * d
  var betaPDF = function(x){
    return Math.pow(x,shape_alpha-1)*Math.pow((1-x),shape_beta-1)
  }
  return map(betaPDF, bins)
}

var structuredPriorModel = function(params){
  Infer({method: "enumerate"}, function(){

    // unpack parameters
    var potential = params["potential"]
    var g = params["prevalenceWhenPresent"]
    var d = params["concentrationWhenPresent"]
    
    var propertyIsPresent = flip(potential)
    var prevalence = propertyIsPresent ? 
          categorical(discretizeBeta(g,d), bins) : 
          0

    return {prevalence: prevalence}
  })
}

// e.g. "Has Wings"
viz.auto(structuredPriorModel({potential: 0.3, 
                               prevalenceWhenPresent: 0.99, 
                               concentrationWhenPresent: 10}))
~~~~

> **Exercises:**

> 1. What does this picture represent? If you drew a sample from this distribution, what (in the world) would that correspond to?
> 2. Try to construct priors for other properties. Some possibilities include: lays eggs, are female, carry malaria, attack swimmers, are full-grown. Or choose your favorite property.

#### Generics model

Meaning function

~~~~
///fold:
// discretized range between 0 - 1
var bins = _.range(0.01, 1, 0.025);
var thresholdBins = _.range(0, 1, 0.025);

// function returns a discretized Beta PDF
var discretizeBeta = function(g, d){
  var shape_alpha =  g * d
  var shape_beta = (1-g) * d
  var betaPDF = function(x){
    return Math.pow(x,shape_alpha-1)*Math.pow((1-x),shape_beta-1)
  }
  return map(betaPDF, bins)
}

var structuredPriorModel = function(params){
  Infer({method: "enumerate"}, function(){

    // unpack parameters
    var potential = params["potential"]
    var g = params["prevalenceWhenPresent"]
    var d = params["concentrationWhenPresent"]
    
    var propertyIsPresent = flip(potential)
    var prevalence = propertyIsPresent ? 
          categorical(discretizeBeta(g,d), bins) : 
          0

    return prevalence
  })
}
///

var utterances = ["generic", "silence"];

var thresholdPrior = function() { return uniformDraw(thresholdBins) };
var utterancePrior = function() { return uniformDraw(utterances) };

var meaning = function(utterance, state, threshold) {
  return (utterance == 'generic') ? state > threshold : true
}

var threshold = thresholdPrior()

print(threshold)
meaning("generic", 0.5, threshold)
~~~~

Let's now add in RSA.

~~~~
///fold:
// discretized range between 0 - 1
var bins = _.range(0.01, 1, 0.025);
var thresholdBins = _.range(0, 1, 0.025);

// function returns a discretized Beta PDF
var discretizeBeta = function(g, d){
  var shape_alpha =  g * d
  var shape_beta = (1-g) * d
  var betaPDF = function(x){
    return Math.pow(x,shape_alpha-1)*Math.pow((1-x),shape_beta-1)
  }
  return map(betaPDF, bins)
}

var structuredPriorModel = function(params){
  Infer({method: "enumerate"}, function(){

    // unpack parameters
    var potential = params["potential"]
    var g = params["prevalenceWhenPresent"]
    var d = params["concentrationWhenPresent"]
    
    var propertyIsPresent = flip(potential)
    var prevalence = propertyIsPresent ? 
          categorical(discretizeBeta(g,d), bins) : 
          0

    return prevalence
  })
}
///

var alpha_1 = 5;
var alpha_2 = 1;

var utterances = ["generic", "silence"];

var thresholdPrior = function() { return uniformDraw(thresholdBins) };
var utterancePrior = function() { return uniformDraw(utterances) }

var meaning = function(utterance, state, threshold) {
  return (utterance == 'generic') ? state > threshold : true
}

var literalListener = cache(function(utterance, threshold, statePrior) {
  Infer({method: "enumerate"}, function(){
    var state = sample(statePrior)

    var m = meaning(utterance, state, threshold)
    condition(m)
    
    return state
  })
})

var speaker1 = cache(function(state, threshold, statePrior) {
  Infer({method: "enumerate"}, function(){
    var utterance = utterancePrior()
    
    var L0 = literalListener(utterance, threshold, statePrior)
    factor( alpha_1*L0.score(state) )
    
    return utterance
  })
})

var pragmaticListener = function(utterance, statePrior) {
  Infer({method: "enumerate"}, function(){
    var state = sample(statePrior)
    var threshold = thresholdPrior()

    var S1 = speaker1(state, threshold, statePrior)
    observe(S1, utterance)
    
    return {prevalence: state}
  })
}

// "Feps have wings."
var hasWingsPrior = structuredPriorModel({
  potential: 0.3, 
  prevalenceWhenPresent: 0.99, 
  concentrationWhenPresent: 10
})
                    
var fepsHaveWings = pragmaticListener("generic", hasWingsPrior);
print("Listener interpretation of 'feps have wings'")
viz.auto(fepsHaveWings)

// "Wugs carry malaria."
var carriesMalariaPrior = structuredPriorModel({
  potential: 0.01, 
  prevalenceWhenPresent: 0.1, 
  concentrationWhenPresent: 5
})

var fepsCarryMalaria = pragmaticListener("generic", carriesMalariaPrior);
print('Listener interpretation of "wugs carry malaria"')
viz.auto(fepsCarryMalaria)

~~~~

> **Exercise:** Test pragmatic listener interpretations of generics about different properties (hence, different priors).

So we have a model that can interpret generic language (with a very simple semantics). We can now imagine a speaker who thinks about this type of listener, and decides if a generic utterance is a good thing to say. If we specificy the alternative utterance to be a *null* utterance (or, *silence)


~~~~
///...

var speaker1 = cache(function(state, threshold) {
  Infer({method: "enumerate"}, function(){
    var utterance = utterancePrior()
    
    var L0 = literalListener(utterance, threshold)
    factor( alpha_1*L0.score(state) )
    
    return utterance
  })
})

///...

var speaker2 = function(state){
  Infer({method: "enumerate"}, function(){
    var utterance = utterancePrior()

    var L1 = pragmaticListener(utterance);  
    factor( alpha_2 * L1.score(state) )
  
    return utterance
  })
}
~~~~

Let's put speaker2 on top of the pragmatic listener.

~~~~
///fold:
// discretized range between 0 - 1
var bins = _.range(0.01, 1, 0.025);
var thresholdBins = _.range(0, 1, 0.025);

// function returns a discretized Beta PDF
var discretizeBeta = function(g, d){
  var shape_alpha =  g * d
  var shape_beta = (1-g) * d
  var betaPDF = function(x){
    return Math.pow(x,shape_alpha-1)*Math.pow((1-x),shape_beta-1)
  }
  return map(betaPDF, bins)
}

var structuredPriorModel = function(params){
  Infer({method: "enumerate"}, function(){

    // unpack parameters
    var potential = params["potential"]
    var g = params["prevalenceWhenPresent"]
    var d = params["concentrationWhenPresent"]
    
    var propertyIsPresent = flip(potential)
    var prevalence = propertyIsPresent ? 
          categorical(discretizeBeta(g,d), bins) : 
          0

    return prevalence
  })
}

var alpha_1 = 5;
var alpha_2 = 1;

var thresholdPrior = function() { return uniformDraw(thresholdBins) };

var utterancePrior = function() {
  var utterances = ["generic", "silence"];
  return uniformDraw(utterances);
}

var meaning = function(utt,state, threshold) {
  return utt == 'generic' ? state > threshold :
         true
}

var literalListener = cache(function(utterance, threshold, statePrior) {
  Infer({method: "enumerate"}, function(){
    var state = sample(statePrior)
    var m = meaning(utterance, state, threshold)
    condition(m)
    return state
  })
})

var speaker1 = cache(function(state, threshold, statePrior) {
  Infer({method: "enumerate"}, function(){
    var utterance = utterancePrior()
    var L0 = literalListener(utterance, threshold, statePrior)
    factor( alpha_1*L0.score(state) )
    return utterance
  })
})
///

var pragmaticListener = cache(function(utterance, statePrior) {
  Infer({method: "enumerate"}, function(){
    var state = sample(statePrior);
    var threshold = thresholdPrior();
    var S1 = speaker1( state, threshold, statePrior );
    observe(S1, utterance);
    return state
  })
})

var speaker2 = function(state, statePrior){
  Infer({method: "enumerate"}, function(){
    var utterance = utterancePrior();
    var L1 = pragmaticListener(utterance, statePrior);
    factor( alpha_2 * L1.score(state) )
    return utterance
  })
}

var prevalence = 0.01

var carriesMalariaPrior = structuredPriorModel({
  potential: 0.01, 
  prevalenceWhenPresent: 0.01, 
  concentrationWhenPresent: 5
})

print('Prior on "carries malaria"')
viz.density(carriesMalariaPrior)

print('Truth judgment of "Mosquitos carry malaria"')
print('...assuming (the speaker believes) ' + prevalence * 100 + '% of mosquitos carry malaria.')
viz.auto(speaker2(prevalence, carriesMalariaPrior))

~~~~

> **Exercises:**

> 1. Test *Birds lay eggs* vs. *Birds are female*.
> 2. Come up with other generic sentences. Hypothesize what the prior might be, and what the prevalence might be, and test the model on it.


#### Extension: Generics with gradable adjectives


First, a world with entities.

~~~~
var altBeta = function(g, d){
  var a =  g * d;
  var b = (1-g) * d;
  return beta(a, b)
}

var fep = function() {
  return {
    kind: "fep", 
    wings: flip(0.5), 
    legs: flip(0.01), 
    claws: flip(0.01), 
    height: altBeta(0.5, 10)
  }
}

var wug = function() {
  return {
    kind: "wug", 
    wings: flip(0.5), 
    legs: flip(0.99), 
    claws: flip(0.3), 
    height: altBeta(0.2, 10)
  }
}

var glippet = function() {
  return {
    kind: "glippet", 
    wings: flip(0.5), 
    legs: flip(0.99), 
    claws: flip(0.2), 
    height: altBeta(0.8, 10)
  }
}

var theWorld = _.flatten([repeat(10, fep), repeat(10, wug), repeat(10, glippet)])

var kinds = _.uniq(_.pluck(theWorld, "kind"));

print('height distribution over all creatures')
viz.density(_.pluck(theWorld, "height"))

var rs = map(function(k){
  print('height distribution for ' + k)
  viz.density(_.pluck(_.where(theWorld,{kind: k}), "height"), {bounds:[0,1]})
}, kinds)

print('')
~~~~




~~~~
///fold:
// discretized range between 0 - 1
var stateBins = [0.01,0.1,0.2,0.3,0.4,0.5,0.6,0.7,0.8,0.9,1]
var thresholdBins = [0,0.1,0.2,0.3,0.4,0.5,0.6,0.7,0.8,0.9]
var alpha_1 = 5

var round = function(x){
  var rounded = Math.round(10*x)/10
  return rounded == 0 ? 0.01 : rounded
}

var makeHistogram = function(prevalences){
  return map(function(s){
    return reduce(function(x, i){
      var k = x == s ? 1 : 0
      return i + k
    }, 0.001, prevalences)
  }, stateBins)
}

var altBeta = function(g, d){
  var a =  g * d;
  var b = (1-g) * d;
  return beta(a, b)
}

var fep = function() {
  return {
    kind: "fep", 
    wings: flip(0.5), 
    legs: flip(0.01), 
    claws: flip(0.01), 
    height: round(altBeta(0.5, 10))
  }
}

var wug = function() {
  return {
    kind: "wug", 
    wings: flip(0.5), 
    legs: flip(0.99), 
    claws: flip(0.3), 
    height: round(altBeta(0.2, 10))
  }
}

var glippet = function() {
  return {
    kind: "glippet", 
    wings: flip(0.5), 
    legs: flip(0.99), 
    claws: flip(0.2), 
    height: round(altBeta(0.8, 10))
  }
}
///

var theWorld = _.flatten([repeat(10, fep), repeat(10, wug), repeat(10, glippet)])
var allKinds = _.uniq(_.pluck(theWorld, "kind"))

var propertyDegrees = {
  wings: "wings",
  legs: "legs",
  claws: "claws",
  tall:" height"
}

var hasF = function(thing, property){
  return thing[property]
}

var prevalence = function(world, kind, property){
  var members = _.where(world, {kind: kind})
  return round(listMean(_.pluck(members, property)))
}

var prevalencePrior = function(property, world){
  var p =  map(function(k){return prevalence(world, k, property)}, allKinds)
  return makeHistogram(p)
}

var scalePrior = function(property){
  var p = _.pluck(theWorld, property)
  return makeHistogram(p)
}

var statePrior = function(probs){ return categorical(probs, stateBins) }
var thresholdPrior = function() { return uniformDraw(thresholdBins) }

var utterancePrior = function(scale) {
  var utterances = scale == "height" ? 
      ["tall", "null"] :
  ["generic", "null"]
  return uniformDraw(utterances)
}

var meaning = function(utt, state, threshold) {
  return utt == "generic" ? state > threshold :
  utt == "tall" ? state > threshold :
  true
}

var literalListener = cache(function(utterance, threshold, stateProbs) {
  Infer({method: "enumerate"}, function(){

    var state = statePrior(stateProbs)
    var m = meaning(utterance, state, threshold)

    condition(m)

    return state
  })
})

var speaker1 = cache(function(state, threshold, stateProbs, predicate) {
  Infer({method: "enumerate"}, function(){

    var utterance = utterancePrior(predicate)
    var L0 = literalListener(utterance, threshold, stateProbs)

    factor(alpha_1 * L0.score(state))

    return utterance

  })
})

var pragmaticListener = cache(function(utterance, scale, world) {
  Infer({method: "enumerate"}, function(){

    var stateProbs = scale == "height" ? 
        scalePrior(scale) : 
    prevalencePrior(scale, world)

    var state = statePrior(stateProbs)
    var threshold = thresholdPrior()

    var S1 = speaker1(state, threshold, stateProbs, scale)

    observe(S1, utterance)

    return state
  })
})

var worldWithTallness = map(function(thing){
  var tallDistribution = Infer({method: "enumerate"}, function(){
    var utterance = utterancePrior("height")
    factor(pragmaticListener(utterance, "height").score(thing.height))
    return utterance
  })
  return _.extend(thing, 
                  {tall: Math.exp(tallDistribution.score("tall"))})
}, theWorld)

var speaker2 = function(k, f){
  Infer({method: "enumerate"}, function(){

    var property = f.split(' ')[1]
    var degree = propertyDegrees[property]

    var world = _.isNumber(theWorld[0][degree]) ? 
        worldWithTallness :
    theWorld

    var prev = prevalence(world, k, property)

    var utterance = utterancePrior(property)

    var L1 = pragmaticListener(utterance, property, world)
//     print(prev)
    factor(2*L1.score(prev))

    return utterance=="generic" ? 
      k + "s " + f :
    "don't think so"
  })
}

viz.auto(speaker2("glippet", "are tall"))
~~~~

References:

- Cite:tessler2016manuscript
