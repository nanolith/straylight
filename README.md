straylight
==========

straylight is a utility for extracting meaningful data out of a C source file
for use with formal analysis.  Currently, straylight is implemented in Haskell,
but in the future, it will be implemented in formally verified C.

This utility has two uses, but currently only the first is implemented. In
the first use, a C function can be extracted from a source file. This function
definition is stripped of all comments and all other information in the C source
file, so that it can be easily compared with an extracted function from a
proof assistant. In order to ensure that I can make a 1-1 comparison, this
function is checked to ensure that it follows a basic set of rules. These rules
will be formalized, but for now, they are fairly arbitrary.

First, all variables and constants needed for this function are declared at the
start of the function, like old-school K&R C. Then, constants are assigned their
values. There are a limited number of looping idioms supported. All mathematical
expressions follow a simple form of `var1 = var2 op var3;`.  This ensures that
implementations using an abstract machine for evaluation can implement
equivalent math instructions.

In the second use, I will formalize an abstract machine model for importing a C
function to a proof assistant. This use case requires configuration so that the
import can choose correct instructions for dealing with custom types. This use
requires building code as complex as a dynamically retargetable compiler, and
must be formally verified. As such, I will only implement the 1-1 comparison in
the Haskell implementation.

Motivation
----------

The motivation for this tool is to provide a rational separation point between
the process of formally verifying software and the process of writing software.
The abstract machine model I have developed is quite cumbersome for regular
development processes. Software development tends to operate in phases. First,
there is a period of rapid change and protocycling. Then, there is a period of
testing and maturation. Finally, there is a period of optimization and interface
freezing.

There are good insertion points for formal methods throughout this process. For
instance, when initially designing a system, that is a great time to write
formal specifications. During implementation, discoveries will be made that may
require enhancements to these specifications. As the software implementation
matures, that is the best time to start building equivalence proofs to verify
that the implementation matches the formal specification and has all desired
safety, security, and performance properties.

Developers of a software application or users of a software library are greatly
convenienced by having well documented source code. Furthermore, there are
additional tools such as a model checker, which can be used to perform static
analysis and function contract enforcement on this source code. There are also
style and maintenance rules that are often imposed on source code in large
projects.

Thus, it makes sense to maintain separate source files instead of extracting
these on the fly from the formal specifications. However, this leads to a
problem: how can we verify that the proofs that we write are relevant to the
actual source code being used in the system?  This is where the first use case
of straylight comes in: if we strip the whitespace, comments, and other cruft,
we are left with an examination of function and data structure definitions that
we can compare with the versions extracted from the formal specifications. If
these do not match, then the implementation and specification has drifted, and
the two must be brought back together. If they match exactly, then any
properties proven in the specification must hold.

Obviously, this isn't perfect. It's possible for hackery in the preprocessor to
subtly change the meaning of types or expressions. It's possible for the formal
specification to be incorrect. These accuracy issues are outside of the scope of
straylight. The point of this tool is to provide a stable means to compare
implementation to extracted source code from the specification. It is up to the
user of this tool and the writer of the formal specifications to take efforts to
ensure that this comparison is fair and accurate.
