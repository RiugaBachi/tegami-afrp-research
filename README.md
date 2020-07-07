# Tegami AFRP Research

This repository serves as a compilation of research, thoughts, and discussions into the topic of AFRP for use in an upcoming library `tegami`. Perhaps this resource can be useful for those looking to get into AFRP implementation in the future, giving them insight into implementation issues and helping them avoid common implementation pitfalls. 

This compilation does not aim to be academically rigorous.

# Table of Contents
1. [Philosophy](#philosophy)
2. [Prelude](#prelude)
3. [Subnetting](#subnetting)
4. [Lazy Suspension](#lazy-suspension)
5. [Reactimation Frameworks](#reactimation-frameworks)

## Philosophy
At its core, `tegami` aims to be an AFRP implementation designed around real-world practicality.

## Prelude
### Standard Encoding
Most AFRP implementations use the same two encoding. Yampa in particular uses a GADT, however if you were to simplify said GADT down to its bare bones it would ultimately be the same encoding:

```hs
newtype X a b = X { step :: a -> (b, X a b) }
```

Notice that we are talking about encoding, not definition. From this encoding, a valid signal function can be constructed as follows:

```hs
newtype SF a b = SF { step :: a -> Double -> (b, SF a b) }
```

Ah, but can we not also define another possible SF from the same encoding?

```hs
newtype GeneralizedSF t a b = GSF { step :: a -> t -> (b, GeneralizedSF t a b) }
```

This is the importance of differentiating between the definition and encoding for arrow primitives in AFRP. There can be many valid definitions which all use the same encoding, however the encoding is important as a standalone concept in optimizing arrow performance and memory semantics.

By convention, the standard encoding `X` above will be referred to as _automaton encoding_. Why, you ask? From `Control.Arrow.Transformer.Automaton`:

```
newtype Automaton a b c = Automaton (a b (c, Automaton a b c))
```

If you specialize this to `Automaton (->)`, you get:

```
newtype Automaton' b c = Automaton' (b -> (c, Automaton' b c))
```

A common derivative of standard automaton encoding is _monadic automaton encoding_:

```hs
newtype Y m a b = Y { step :: a -> m (b, Y m a b) }
```

This is the encoding behind dunai's `MSF`, varying's `VarT`, etc.

These two encodings, `X` and `Y`, generalize the vast majority of AFRP arrow primitives encountered in the wild.

### Illegality of ArrowApply
## Subnetting
### Defintion
### Applications
### Implementation and Optimization Issues
### Parallelization
## Lazy Suspension
## Reactimation Frameworks
