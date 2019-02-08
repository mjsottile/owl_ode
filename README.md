# Owl-ODE - Ordinary Differential Equation Solvers

Please refer to the [Project Page](http://ocaml.xyz/project/proposal.html#project-13-differential-equation-solvers) for details.

You can run the current example with `dune exec examples/van_der_pol.exe`,  `dune exec examples/damped.exe`.


## NOTES

The main idea is develop a uniform interface to integrate ODE solvers (and in the future finite element methods) into Owl.
Currently there are three options available, providing incompatible underlying representations:

- [sundialsml](https://github.com/inria-parkas/sundialsml), providing a wrapper over Sundials

- [ocaml-odepack](https://github.com/Chris00/ocaml-odepack), providing bindings for ODEPACK (same solvers used by scipy's old interface `scipy.integrate.odeint`)

- [gsl-ocaml](https://github.com/mmottl/gsl-ocaml), providing bindings for GSL, in particular the ODE integrator bindings are here [mmottl/gsl-ocaml/src/odeiv.mli](https://github.com/mmottl/gsl-ocaml/blob/master/src/odeiv.mli)

Of course such an interface could provide additional purely OCaml functionalities, like robust native implementations of

- [x] standard fixed-step ode solvers, like Euler, Midpoint, Runge-Kutta 4

- [ ] standard adaptive solvers, say rk2(3), and rk4(5) or [Tsit5](http://users.ntua.gr/tsitoura/RK54_new_v2.pdf) (in progress)

- [x] symplectic ode solvers, like Störmer-Verlet, Forest-Ruth or Yoshida

- [ ] sundialsml interface (already partially implemented)

and implementations leveraging Owl's specific capabilities, like an implementation of the Taylor integrator built upon Algodiff.
Albeit relatively old and standard, a good starting point could be the two references from [TaylorSeries.jl](https://github.com/JuliaDiff/TaylorSeries.jl), namely:

- W. Tucker, Validated numerics: A short introduction to rigorous computations, Princeton University Press (2011).

- A. Haro, Automatic differentiation methods in computational dynamical systems: Invariant manifolds and normal forms of vector fields at fixed points, preprint.

Some important points to address for this are:

- provide a uniform type safe interface, capable of accepting pluggable new engines and dealing with the different sets of configuration options of each of them (maybe extensible types or GADTs can help in this regard more than Functors?)

- full Owl types interoperability

- ease of use (compared to JuliaDiffEq and Scipy)

- make the native implementations robust (right now they are naive OCaml implementations)

- ...


It would be interesting to design an interface that allows to implement the [Neural ODE](https://arxiv.org/abs/1806.07366) idea in a natural way also in Owl.


## Further comments

We could provide two interfaces, one takes a stepper function and performs just a step, and can be iterated manually (like `odeint` in the current sundials implementation, or the integrators in the current ocaml implementation), and a lower level one mimicking sundials and odepack, that only performs each integration step separately.

We currently cannot have implicit methods for the lack of vector-valued root finding functions. We should add implementations for those, and then introduce some implicit methods (e.g. the implicit Stoermer-Verlet is much more robust and works nicely for non-separable Hamiltonians). At least we can use Sundials for now `:-)`

It would also be nice to provide a function that takes the pair (t, y) and returns the interpolated function.

We should make the integrators more robust and with better failure modes, we could take inspiration from the very readable scipy implementation [https://github.com/scipy/scipy/blob/v1.2.0/scipy/integrate/_ivp/rk.py#L15].
