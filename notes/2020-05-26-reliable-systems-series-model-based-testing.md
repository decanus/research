# Notes: [Reliable Systems Series: Model-Based Testing](https://medium.com/@tylerneely/reliable-systems-series-model-based-property-testing-e89a433b360)

**Models** - In this context not anything formal, just simple code that behaves in a way that can track our expectations. We run automated tests against the model and an implementation, if they diverge, we found a bug.

[QuickCheck](https://hackage.haskell.org/package/QuickCheck), is a property testing library. Property testing checks if a given property holds for a set of inputes.

This post is meant to summarize how to apply some of the techniques found in these papers:
 - [Experiences with QuickCheck](https://www.cs.tufts.edu/~nr/cs257/archive/john-hughes/quviq-testing.pdf)
 - [Testing Monadic Code with QuickCheck](http://www.cse.chalmers.se/~rjmh/Papers/QuickCheckST.ps)

The goals are to create a toy model of how something should work, and then test identical behaviour between the actual implementation and the toy model. If they diverge, the model or the implementation or both could be wrong.

Models can be a cognitive aid for reasoning about what your system actually does. It can help new developers, they can look at a simplified model to grasp what the system does overall.

Models are good to write before the actual implementation, its essentially TDD. By letting the model guide the implementation there is a single method to test your assumptions.

Sometimes it is hard to build a model of everything, this may just end up looking like a second implementation so it may be easier to track specific system behaviours you want to ensure.

Additionally, you may only need to check few invariants at times. 

> If it’s hard to model or check specific invariants of a system, it could be a symptom that the architecture is unsound, leading to unreliable implementations and high human costs for debugging over time. If you can’t describe it in a model or set of invariants, good luck getting humans to understand what should be happening.

Build systems in a way that they can be single-stepped and then replayed, this restricts nondeterminism and increases the observability of causal relationships.

Model based tests can eliminate the need for hand-written unit tests with far less code and far more coverage.

Model based testing tests far more possibilities than normal tests and avoids the biases of the creators. Generating of regresssion tests saves time trying to reproduce subtle issues.

