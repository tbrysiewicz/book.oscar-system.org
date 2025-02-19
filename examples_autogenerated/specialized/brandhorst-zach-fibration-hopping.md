---
layout: page
title: Elliptic Fibrations on Vinberg’s Most Algebraic K3 Surface
---

## Authors: Simon Brandhorst and Matthias Zach

```julia
julia> Qt, t = polynomial_ring(QQ, :t)
(Univariate polynomial ring in t over QQ, t)

julia> Qtf = fraction_field(Qt)
Fraction field
  of univariate polynomial ring in t over QQ

julia> E = elliptic_curve(Qtf, [0,0,0,0,t^5*(t-1)^2])
Elliptic curve with equation
y^2 = x^3 + t^7 - 2*t^6 + t^5

julia> j_invariant(E)
0

julia> discriminant(E)
-432*t^14 + 1728*t^13 - 2592*t^12 + 1728*t^11 - 432*t^10

julia> factor(Qt, discriminant(E))
-432 * (t - 1)^4 * t^10

julia> R = rescale(root_lattice([(:E, 8), (:E, 8), (:A, 2)]), -1);

julia> U = integer_lattice(gram=ZZ[0 1; 1 -2]);

julia> NS, _ = direct_sum([U, R]);

julia> e = matrix(ZZ,1,20,ones(Int,20));e[1,1]=51;

julia> ample = e*inv(gram_matrix(NS));

julia> minimum(rescale(orthogonal_submodule(NS, ample),-1))
4

julia> Xaut, Xchambers, Xrational_curves =  K3_surface_automorphism_group(NS, ample);

julia> length(Xrational_curves)
2

julia> length(Xchambers)
1

julia> C = Xchambers[1]
Chamber  in dimension 20 with 36 walls

julia> Xelliptic_classes = [f for f in rays(C) if 0 == f*gram_matrix(NS)*transpose(f)];

julia> Xelliptic_classes_orb = orbits(gset(matrix_group(aut(C)),(x,g)->x*matrix(g), Xelliptic_classes));

julia> length(Xelliptic_classes_orb)
6

julia> f1 = identity_matrix(ZZ, 20)[1:1,:];

julia> f1_2neighbors = [f for f in Xelliptic_classes if 2 == (f1*gram_matrix(NS)*transpose(f))[1,1]];

julia> f2, f3, _ = f1_2neighbors;

julia> f2_2neighbors = [f for f in Xelliptic_classes if 2 == (f2*gram_matrix(NS)*transpose(f))[1,1]];

julia> fibers = [f1,f2,f3];

julia> todo = [o for o in Xelliptic_classes_orb if !any(f in o for f in fibers)];

julia> while length(todo) > 0
         o = popfirst!(todo)
         f = findfirst(x-> x in o, f2_2neighbors)
         push!(fibers, f2_2neighbors[f])
       end

julia> fibers = [vec(collect(i*basis_matrix(NS))) for i in fibers]
6-element Vector{Vector{QQFieldElem}}:
 [1, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0]
 [4, 2, -4, -7, -10, -8, -6, -4, -2, -5, -2, -4, -6, -5, -4, -3, -2, -3, -1, -1]
 [4, 2, -4, -7, -10, -8, -6, -4, -2, -5, -4, -7, -10, -8, -6, -4, -2, -5, 0, 0]
 [6, 3, -5, -10, -15, -12, -9, -6, -3, -8, -5, -10, -15, -12, -9, -6, -3, -8, -1, -1]
 [10, 5, -8, -16, -24, -20, -15, -10, -5, -12, -7, -14, -21, -17, -13, -9, -5, -11, -3, -2]
 [6, 3, -4, -8, -12, -10, -8, -6, -3, -6, -4, -8, -12, -10, -8, -6, -3, -6, -2, -1]

julia> [fibration_type(NS, f) for f in fibers]
6-element Vector{Tuple{Int64, FinGenAbGroup, Vector{Tuple{Symbol, Int64}}}}:
 (0, Z/1, [(:A, 2), (:E, 8), (:E, 8)])
 (1, Z/2, [(:D, 10), (:E, 7)])
 (0, Z/2, [(:A, 2), (:D, 16)])
 (1, Z/3, [(:A, 17)])
 (0, Z/4, [(:A, 11), (:D, 7)])
 (0, Z/3, [(:E, 6), (:E, 6), (:E, 6)])

julia> F = transpose(matrix((reduce(hcat, fibers)))); F * gram_matrix(NS) * transpose(F)
[0   2   2   3   5   3]
[2   0   2   2   2   2]
[2   2   0   2   5   4]
[3   2   2   0   2   3]
[5   2   5   2   0   2]
[3   2   4   3   2   0]
```

```julia
julia> K = QQ;

julia> Kt, t = polynomial_ring(K, :t);

julia> Ktf = fraction_field(Kt);

julia> E = elliptic_curve(Ktf, [0,0,0,0,t^5*(t-1)^2]);

julia> R = rescale(root_lattice([(:E, 8), (:E, 8), (:A, 2)]), -1);

julia> U = integer_lattice(gram=ZZ[0 1; 1 -2]);

julia> NS, _ = direct_sum([U, R]);

julia> e = matrix(ZZ,1,20,ones(Int,20));e[1,1]=51;

julia> ample = e*inv(gram_matrix(NS));

julia> fibers = [vec(collect(i)) for i in [
        QQ[1   0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0;],
        QQ[4   2   -4   -7   -10 -8   -6   -4   -2   -5   -2   -4   -6   -5   -4   -3   -2   -3   -1   -1;],
        QQ[4   2   -4   -7   -10 -8   -6   -4   -2   -5   -4   -7   -10 -8   -6   -4   -2   -5   0 0;],
        QQ[6   3   -5   -10 -15   -12   -9   -6   -3   -8   -5   -10 -15   -12   -9   -6   -3   -8   -1   -1;],
        QQ[10 5   -8   -16   -24   -20 -15   -10 -5   -12   -7   -14   -21   -17   -13   -9   -5   -11   -3   -2;],
        QQ[6   3   -4   -8   -12   -10 -8   -6   -3   -6   -4   -8   -12   -10 -8   -6   -3   -6   -2   -1;]]];

julia> Y1 = elliptic_surface(E, 2)
Elliptic surface
  over rational field
with generic fiber
  -x^3 + y^2 - t^7 + 2*t^6 - t^5

julia> S = weierstrass_model(Y1)[1]
Scheme
  over rational field
with default covering
  described by patches
    1: scheme(-(x//z)^3 + (y//z)^2 - t^7 + 2*t^6 - t^5)
    2: scheme((z//x)^3*t^7 - 2*(z//x)^3*t^6 + (z//x)^3*t^5 - (z//x)*(y//x)^2 + 1)
    3: scheme((z//y)^3*t^7 - 2*(z//y)^3*t^6 + (z//y)^3*t^5 - (z//y) + (x//y)^3)
    4: scheme((x//z)^3 - (y//z)^2 + s^7 - 2*s^6 + s^5)
    5: scheme((z//x)^3*s^7 - 2*(z//x)^3*s^6 + (z//x)^3*s^5 - (z//x)*(y//x)^2 + 1)
    6: scheme((z//y)^3*s^7 - 2*(z//y)^3*s^6 + (z//y)^3*s^5 - (z//y) + (x//y)^3)
  in the coordinate(s)
    1: [(x//z), (y//z), t]
    2: [(z//x), (y//x), t]
    3: [(z//y), (x//y), t]
    4: [(x//z), (y//z), s]
    5: [(z//x), (y//x), s]
    6: [(z//y), (x//y), s]

julia> piS = weierstrass_contraction(Y1)
Composite morphism of[...]

julia> basisNSY1, gramTriv = trivial_lattice(Y1);

julia> [(i[1],i[2]) for i in reducible_fibers(Y1)]
3-element Vector{Tuple{Vector{QQFieldElem}, Tuple{Symbol, Int64}}}:
 ([1, 1], (:A, 2))
 ([0, 1], (:E, 8))
 ([1, 0], (:E, 8))

julia> basisNSY1, _, NSY1 = algebraic_lattice(Y1);

julia> basisNSY1
20-element Vector{Any}:
 Fiber over (2, 1)
 section: (0 : 1 : 0)
 component A2_1 of fiber over (1, 1)
 component A2_2 of fiber over (1, 1)
 component E8_1 of fiber over (0, 1)
 component E8_2 of fiber over (0, 1)
 component E8_3 of fiber over (0, 1)
 component E8_4 of fiber over (0, 1)
 component E8_5 of fiber over (0, 1)
 component E8_6 of fiber over (0, 1)
 component E8_7 of fiber over (0, 1)
 component E8_8 of fiber over (0, 1)
 component E8_1 of fiber over (1, 0)
 component E8_2 of fiber over (1, 0)
 component E8_3 of fiber over (1, 0)
 component E8_4 of fiber over (1, 0)
 component E8_5 of fiber over (1, 0)
 component E8_6 of fiber over (1, 0)
 component E8_7 of fiber over (1, 0)
 component E8_8 of fiber over (1, 0)

julia> basisNSY1[1]
Effective weil divisor Fiber over (2, 1)
  on elliptic surface with generic fiber -x^3 + y^2 - t^7 + 2*t^6 - t^5
with coefficients in integer Ring
given as the formal sum of
  1 * sheaf of ideals

julia> b, I = Oscar._is_equal_up_to_permutation_with_permutation(gram_matrix(NS), gram_matrix(NSY1))
(true, [1, 2, 19, 20, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18])

julia> @assert gram_matrix(NSY1) == gram_matrix(NS)[I,I]

julia> Oscar.horizontal_decomposition(Y1, fibers[2][I])[2]
Effective weil divisor
  on elliptic surface with generic fiber -x^3 + y^2 - t^7 + 2*t^6 - t^5
with coefficients in integer Ring
given as the formal sum of
  2 * component E8_7 of fiber over (0, 1)
  2 * section: (0 : 1 : 0)
  1 * component A2_0 of fiber over (1, 1)
  1 * component E8_0 of fiber over (1, 0)
  2 * component E8_4 of fiber over (0, 1)
  2 * component E8_3 of fiber over (0, 1)
  1 * component E8_2 of fiber over (0, 1)
  2 * component E8_0 of fiber over (0, 1)
  2 * component E8_6 of fiber over (0, 1)
  2 * component E8_5 of fiber over (0, 1)
  1 * component E8_8 of fiber over (0, 1)

julia> representative(elliptic_parameter(Y1, fibers[2][I]))
(x//z)//(t^3 - t^2)

julia> g, phi1 = two_neighbor_step(Y1, fibers[2][I]); g
-t^3*x^3 + t^3*x^2 - x + y^2

julia> kt2 = base_ring(parent(g)); P = kt2.([0,0]);

julia> Y2, phi2 = elliptic_surface(g, P); Y2
Elliptic surface
  over rational field
with generic fiber
  -x^3 + t^3*x^2 - t^3*x + y^2
and relatively minimal model
  scheme over QQ covered with 47 patches

julia> E2 = generic_fiber(Y2); t2 = gen(kt2);

julia> P2 = E2([t2^3, t2^3]); set_mordell_weil_basis!(Y2, [P2]);

julia> U2 = weierstrass_chart_on_minimal_model(Y2); U1 = weierstrass_chart_on_minimal_model(Y1);

julia> imgs = phi2.(phi1.(ambient_coordinates(U1))) # k(Y1) -> k(Y2)
3-element Vector{AbstractAlgebra.Generic.Frac{QQMPolyRingElem}}:
 (-(x//z)*t + t)//(x//z)^3
 ((x//z)*(y//z) - (y//z))//(x//z)^5
 1//(x//z)

julia> phi_rat = morphism_from_rational_functions(Y2, Y1, U2, U1, imgs);

julia> set_attribute!(phi_rat, :is_isomorphism=>true);

julia> pullbackDivY1 = [pullback(phi_rat, D) for D in basisNSY1];

julia> B = [basis_representation(Y2, D) for D in pullbackDivY1];
20-element Vector{Vector{QQFieldElem}}:
 [4, 2, 0, 0, 0, 0, 0, 0, 0, -4, -4, -8, -7, -6, -5, -4, -3, -2, -1, 0]
 [0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 1, 0, 0, 0, 0, 0, 0, 0, 0]
 [4, 2, -1, -2, -3, -5//2, -2, -3//2, -3//2, -3, -5//2, -5, -9//2, -4, -7//2, -3, -5//2, -2, -3//2, -1]
 [0, 0, 1, 2, 3, 5//2, 2, 3//2, 3//2, -2, -3//2, -3, -5//2, -2, -3//2, -1, -1//2, 0, 1//2, 1]
 [0, 1, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0]
 [1, 0, 0, 0, 0, 0, 0, 0, 0, -1, -1, -2, -2, -2, -2, -2, -2, -2, -1, 0]
 [0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 1, 0, 0]
 [0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 1, 0, 0, 0]
 [0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 1, 0, 0, 0, 0]
 [0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 1, 0, 0, 0, 0, 0]
 [0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 1, 0, 0, 0, 0, 0, 0]
 [0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 1, 0]
 [0, 0, 1, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0]
 [0, 0, 0, 1, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0]
 [0, 0, 0, 0, 1, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0]
 [0, 0, 0, 0, 0, 1, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0]
 [0, 0, 0, 0, 0, 0, 1, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0]
 [0, 0, 0, 0, 0, 0, 0, 1, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0]
 [2, 1, -1, -2, -3, -5//2, -2, -3//2, -3//2, -2, -5//2, -4, -7//2, -3, -5//2, -2, -3//2, -1, -1//2, 0]
 [0, 0, 0, 0, 0, 0, 0, 0, 1, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0]

julia> B = matrix(QQ, 20, 20, reduce(vcat, B)); NSY2 = algebraic_lattice(Y2)[3];

julia> NSY1inY2 = lattice(ambient_space(NSY2),B);

julia> @assert NSY1inY2 == NSY2 && gram_matrix(NSY1inY2) == gram_matrix(NSY1)

julia> fibers_in_Y2 = [f[I]*B for f in fibers]
6-element Vector{Vector{QQFieldElem}}:
 [4, 2, 0, 0, 0, 0, 0, 0, 0, -4, -4, -8, -7, -6, -5, -4, -3, -2, -1, 0]
 [1, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0]
 [5, 2, -2, -3, -4, -3, -2, -1, -2, -5, -4, -8, -7, -6, -5, -4, -3, -2, -1, 0]
 [4, 2, -2, -4, -6, -9//2, -3, -3//2, -7//2, -3, -5//2, -5, -9//2, -4, -7//2, -3, -5//2, -2, -3//2, 0]
 [2, 1, -1, -2, -3, -2, -1, 0, -2, -1, -1, -2, -2, -2, -2, -2, -2, -1, 0, 1]
 [2, 1, 0, 0, 0, 0, 0, 0, 0, -2, -2, -4, -4, -4, -4, -3, -2, -1, 0, 1]

julia> f3 = fibers[3][I]; representative(elliptic_parameter(Y1, f3))
(x//z)//t^2

julia> g3a, phi3a = two_neighbor_step(Y1, f3); g3a
-x^3 + (-t^3 + 2)*x^2 - x + y^2
```

```julia
julia> K = QQ
Rational field

julia> Kt, t = polynomial_ring(K, :t)
(Univariate polynomial ring in t over QQ, t)

julia> Ktf = fraction_field(Kt)
Fraction field
  of univariate polynomial ring in t over QQ

julia> E = elliptic_curve(Ktf, [0, -t^3, 0, t^3, 0])
Elliptic curve with equation
y^2 = x^3 - t^3*x^2 + t^3*x

julia> P = E([t^3, t^3]);

julia> Y2 = elliptic_surface(E, 2, [P]);

julia> S = weierstrass_model(Y2)[1];

julia> basisNSY2, _, NSY2 = algebraic_lattice(Y2);

julia> fibers_in_Y2 = [QQ.(vec(collect(i))) for i in [
        [4   2   0   0   0   0   0   0   0   -4   -4   -8   -7   -6   -5   -4   -3   -2   -1   0],
        [1   0   0   0   0   0   0   0   0   0   0   0   0   0   0   0   0   0   0   0],
        [5   2   -2   -3   -4   -3   -2   -1   -2   -5   -4   -8   -7   -6   -5   -4   -3   -2   -1   0],
        [4   2   -2   -4   -6   -9//2   -3   -3//2   -7//2   -3   -5//2   -5   -9//2   -4   -7//2   -3   -5//2   -2   -3//2   0],
        [2   1   -1   -2   -3   -2   -1   0   -2   -1   -1   -2   -2   -2   -2   -2   -2   -1   0   1],
        [2   1   0   0   0   0   0   0   0   -2   -2   -4   -4   -4   -4   -3   -2   -1   0   1]
       ]]
6-element Vector{Vector{QQFieldElem}}:
 [4, 2, 0, 0, 0, 0, 0, 0, 0, -4, -4, -8, -7, -6, -5, -4, -3, -2, -1, 0]
 [1, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0]
 [5, 2, -2, -3, -4, -3, -2, -1, -2, -5, -4, -8, -7, -6, -5, -4, -3, -2, -1, 0]
 [4, 2, -2, -4, -6, -9//2, -3, -3//2, -7//2, -3, -5//2, -5, -9//2, -4, -7//2, -3, -5//2, -2, -3//2, 0]
 [2, 1, -1, -2, -3, -2, -1, 0, -2, -1, -1, -2, -2, -2, -2, -2, -2, -1, 0, 1]
 [2, 1, 0, 0, 0, 0, 0, 0, 0, -2, -2, -4, -4, -4, -4, -3, -2, -1, 0, 1]

julia> @assert all(inner_product(ambient_space(NSY2), i,i) == 0 for i in fibers_in_Y2)

julia> [representative(elliptic_parameter(Y2, f)) for f in fibers_in_Y2[4:6]]
3-element Vector{AbstractAlgebra.Generic.FracFieldElem{QQMPolyRingElem}}:
 (y//z)//((x//z)*t)
 ((y//z) + t^3)//((x//z)*t - t^4)
 ((y//z) + t^3)//((x//z) - t^3)

julia> g,_ = two_neighbor_step(Y2, fibers_in_Y2[4]);g
-1//4*x^4 - 1//2*t^2*x^3 - 1//4*t^4*x^2 + x + y^2

julia> R = parent(g); K_t = base_ring(R);

julia> (x,y) = gens(R); P = K_t.([0,0]); # rational point

julia> g, _ = transform_to_weierstrass(g, x, y, P);

julia> E4 = elliptic_curve(g, x, y)
Elliptic curve with equation
y^2 = x^3 + 1//4*t^4*x^2 - 1//2*t^2*x + 1//4

julia> g,_ = two_neighbor_step(Y2, fibers_in_Y2[5]);g
t^2*x^3 + (-1//4*t^4 + 2*t)*x^2 + x + y^2

julia> R = parent(g); K_t = base_ring(R);

julia> (x,y) = gens(R); P = K_t.([0,0]); # rational point

julia> g, _ = transform_to_weierstrass(g, x, y, P);

julia> E5 = elliptic_curve(g, x, y)
Elliptic curve with equation
y^2 = x^3 + (1//4*t^4 - 2*t)*x^2 + t^2*x

julia> g,_ = two_neighbor_step(Y2, fibers_in_Y2[6]);g
(t^2 + 2*t + 1)*x^3 + y^2 - 1//4*t^4

julia> R = parent(g); K_t = base_ring(R); t = gen(K_t);

julia> (x,y) = gens(R); P = K_t.([0,1//2*t^2]); # rational point

julia> g, _ = transform_to_weierstrass(g, x, y, P);

julia> E6 = elliptic_curve(g, x, y)
Elliptic curve with equation
y^2 + (-t^2 - 2*t - 1)//t^4*y = x^3
```
