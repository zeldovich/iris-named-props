# Named propsitions for Iris

[![CI](https://github.com/tchajed/iris-named-props/workflows/CI/badge.svg)](https://github.com/tchajed/iris-named-props/actions?query=workflow%3ACI)

Named propositions are an extension to the Iris Proof Mode (IPM) that allow you
to embed names for conjuncts within a definition and then use those names to
introduce or destruct the definition. See the header comment in
[named_props.v](src/named_props.v) for a detailed explanation with the entire
API.

```coq
From iris.proofmode Require Import tactics.
From iris_named_props Require Import named_props.

Definition foo_rep :=
 ("HP" ∷ P ∗
  "HR" ∷ R)%I.

Theorem foo_rep_read_P :
  foo_rep -∗ P.
Proof.
 iIntros "H".
 iNamed "H".
 (* at this point we have a context of

 "HP" : P
 "HR" : R
 --------------------------------------∗
 P

 *)
 iExact "HP".
Qed.
```

Putting the names in the definition avoids repeating the same intro patterns
over and over in a proof. Not repeating yourself makes things easier to change
when the definition changes - for example, reordering and adding new conjuncts
will have minimal impact on proof scripts.

The "names" in named propositions are not actually just names, but Iris intro
patterns; for example `"#H"`, `"%H"` (using Iris's recent support for Coq names
in intro patterns), and `"?"` are all potentially useful.

One application of this feature implemented in the library is a tactic
`iNamedAccu` which is like `iAccu` but remembers the names used. If you haven't
used `iAccu`, it solves a goal which is an evar with the conjunction of the
entire context (this kind of situation arises when you have a proof rule that
allows saving the entire context and then restoring it elsewhere). `iNamedAccu`
is just like `iAccu`, but it adds names to the conjuncts so that the result can
easily be restored with the same names later.