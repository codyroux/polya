In order to run Polya, import run_code.py, and then:
>  x,y,z = Var('x'), Var('y'), Var('z')
>  run_heuristic_on_hypotheses([x>y,y>z,z>x])

There are currently five modules that can be enabled by adding appropriate lines
in run_code.py.

- addition_module 
This is the original Fourier-Motzkin additive elimination routine.
At the current state of the project, this module is inferior in all respects
to poly_add_module. It currently learns redundant comparisons, ie, 1 > -x
where x is known to be positive.

- poly_add_module
This is a replacement for the above. It works by using lrs to construct
a vertex representation of the given inequalities, then projects these vertices
to the i-j plane for each pair i,j. From there it infers the appropriate comparisons.
Much of the code for this process is in lrs_polyhedron_util, which depends on lrs.
It also depends on cdd to format the information passed to lrs.
polyhedron_module_cdd is a somewhat slower alternative that does not rely on lrs.

- multiplication_module
This is the original Fourier-Motzkin multiplicative elimination routine.
As in the FM additive routine, redundant comparisons that do not affect the
result are often larned. Currently, this is somewhat faster than the polyhedron
multiplicative routine.

- poly_mult_module
This is a replacement for the above. It is currently slower than the FM version.
The constant term c in c*t > 1 is converted via prime factorization to a number
of additional variables p1...pn. Since in order to find the correct comparisons
between a_i and a_j, these variables must be present, we cannot use the 2d algorithm
in lrs_polyhedron_util, but instead use cdd to obtain an h-representation from
the vertices projected to the i-j-p1-...-pn plane. cdd accomplishes this faster
than lrs at the moment. lrs is used for the initial h-to-v conversion.

- function_module
This module has been put on the backburner for the time being. In systems with
uninterpreted functional terms, it uses provided information such as monotonicity
to infer comparisons.


In order to use any of the polyhedron modules, cdd and pycddlib must be installed.
See: https://pycddlib.readthedocs.org/en/1.0.4/index.html. In order to use any module
dependent on lrs, lrs must be executable from the run directory, or lrs.py must be
modified to point to the lrs binary. Note that it is simple to change
lrs_polyhedron_util to use cdd instead of lrs, but this will affect performance.