# Ferryman Problem

### Problem
There is a ferryman, goat, cabbage and a wolf on one side of a river. The ferryman can cross a river with at most one passenger in his boat. There is a behavioural conflict between
1. the goat and the cabbage; and
2. the goat and the wolf;

if they are on the same side of the river bank but the ferryman crosses the river or stays on the other bank.

**Can ferryman transport all goods to the other side, without any conflicts occuring?**

### Solution
This is a *planning problem* but can be solved using **model checking**. 

We describe a transition system in which the states represent which
goods are at which side of the river. Then we ask if the goal state is reachable
from the initial state: *Is there a path from the initial state such that it
has a state along it at which all the goods are on the other side, and during
the transitions to that state the goods are never left in an unsafe, conflicting
situation?*

We model all possible behaviour (including that which results in conflicts)
as a NuSMV program. The location of each agent is modelled
as a boolean variable: `FALSE` denotes that the agent is on the initial bank, and
`TRUE` the destination bank. Thus, `ferryman = FALSE` means that the ferryman is
on the initial bank, `ferryman = TRUE` that he is on the destination bank, and
similarly for the variables `goat`, `cabbage` and `wolf`. 

The variable carry takes a value indicating whether the goat, cabbage,
wolf or nothing is carried by the ferryman. The definition of `next(carry)`
works as follows. It is non-deterministic, but the set from which a value is
non-deterministically chosen is determined by the values of ferryman, goat, etc., and always includes `FALSE`. If `ferryman = goat` (i.e., they are on the same
side) then `g` is a member of the set from which `next(carry)` is chosen. The
situation for cabbage and wolf is similar. Thus, if `ferryman = goat = wolf !=
cabbage` then that set is `{g, w, 0}`. The next value assigned to `ferryman` is
non-deterministic: he can choose to cross or not to cross the river. But the
next values of `goat`, `cabbage` and `wolf` are deterministic, since whether they
are carried or not is determined by the ferryman’s choice, represented by the
non-deterministic assignment to `carry`; these values follow the same pattern.

### Instructions to run

```bash
./nusmv.exe ferryman.smv
```

### Sample Solution Path
```
*** This is NuSMV 2.6.0 (compiled on Wed Oct 14 15:37:51 2015)
*** Enabled addons are: compass
*** For more information on NuSMV see <http://nusmv.fbk.eu>
*** or email to <nusmv-users@list.fbk.eu>.
*** Please report bugs to <Please report bugs to <nusmv-users@fbk.eu>>

*** Copyright (c) 2010-2014, Fondazione Bruno Kessler

*** This version of NuSMV is linked to the CUDD library version 2.4.1
*** Copyright (c) 1995-2004, Regents of the University of Colorado

*** This version of NuSMV is linked to the MiniSat SAT solver.
*** See http://minisat.se/MiniSat.html
*** Copyright (c) 2003-2006, Niklas Een, Niklas Sorensson
*** Copyright (c) 2007-2010, Niklas Sorensson

-- specification !(((goat = cabbage | goat = wolf) -> goat = ferryman) U (((cabbage & goat) & wolf) & ferryman))  is false
-- as demonstrated by the following execution sequence
Trace Description: LTL Counterexample
Trace Type: Counterexample
  -- Loop starts here
  -> State: 1.1 <-
    ferryman = FALSE
    goat = FALSE
    cabbage = FALSE
    wolf = FALSE
    carry = none
  -> State: 1.2 <-
    ferryman = TRUE
    goat = TRUE
    carry = g
  -> State: 1.3 <-
    ferryman = FALSE
    carry = none
  -> State: 1.4 <-
    ferryman = TRUE
    wolf = TRUE
    carry = w
  -> State: 1.5 <-
    ferryman = FALSE
    goat = FALSE
    carry = g
  -> State: 1.6 <-
    ferryman = TRUE
    cabbage = TRUE
    carry = c
  -> State: 1.7 <-
    ferryman = FALSE
    carry = none
  -> State: 1.8 <-
    ferryman = TRUE
    goat = TRUE
    carry = g
  -> State: 1.9 <-
    ferryman = FALSE
    wolf = FALSE
    carry = w
  -> State: 1.10 <-
    ferryman = TRUE
    carry = none
  -> State: 1.11 <-
    ferryman = FALSE
    cabbage = FALSE
    carry = c
  -> State: 1.12 <-
    ferryman = TRUE
    carry = none
  -> State: 1.13 <-
    ferryman = FALSE
    goat = FALSE
    carry = g
  -> State: 1.14 <-
    ferryman = TRUE
    carry = none
  -> State: 1.15 <-
    ferryman = FALSE
```

> Invoking **bounded model checking** will produce the shortest possible path
to violate the property.

### References
*Michael Huth* and *Mark Ryan*. 2004. **Logic in Computer Science: Modelling and Reasoning about Systems**. <br>
Cambridge University Press, USA.