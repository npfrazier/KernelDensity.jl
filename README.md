# KernelDensity.jl

[![Build Status](https://travis-ci.org/JuliaStats/KernelDensity.jl.svg?branch=master)](https://travis-ci.org/JuliaStats/KernelDensity.jl)
[![KernelDensity](http://pkg.julialang.org/badges/KernelDensity_release.svg)](http://pkg.julialang.org/?pkg=KernelDensity&ver=release)

Kernel density estimators for julia.

## Usage

### Univariate
The main accessor function is `kde`:

```
kde(data)
```

will construct a `UnivariateKDE` object from the real vector `data`. The
optional keyword arguments are
* `boundary`: the lower and upper limits of the kde as a tuple. Due to the
  fourier transforms used internally, there should be sufficient spacing to
  prevent wrap-around at the boundaries.
* `npoints`: the number of interpolation points to use. The function uses
  fast Fourier transforms (FFTs) internally, so for optimal efficiency this
  should be a power of 2 (default = 2048).
* `kernel`: the distributional family from
  [Distributions.jl](https://github.com/JuliaStats/Distributions.jl) to use as
  the kernel (default = `Normal`). To add your own kernel, extend the internal
  `kernel_dist` function.
* `bandwidth`: the bandwidth of the kernel. Default is to use Silverman's
  rule.

A related function

``` kde_lscv(data) ```

will construct a `UnivariateKDE` object, with the bandwidth selected by
least-squares cross validation. It accepts the above keyword arguments, except
`bandwidth`.


There are also some slightly more advanced interfaces:
```
kde(data, midpoints::Range)
```
allows specifying the internal grid to use. Optional keyword arguments are
`kernel` and `bandwidth`.

```
kde(data, dist::Distribution)
```
allows specifying the exact distribution to use as the kernel. Optional
keyword arguments are `boundary` and `npoints`.

```
kde(data, midpoints::Range, dist::Distribution)
```
allows specifying both the distribution and grid.

### Bivariate

The usage mirrors that of the univariate case, except that `data` is now
either a tuple of vectors
```
kde((xdata, ydata))
```
or a matrix with two columns
```
kde(datamatrix)
```
Similarly, the optional arguments all now take tuple arguments:
e.g. `boundary` now takes a tuple of tuples `((xlo,xhi),(ylo,yhi))`.

### Interpolation

The KDE objects are stored as gridded density values, with attached
coordinates. These are typically sufficient for plotting (see below), but
intermediate values can be interpolated using the
[Grid.jl](https://github.com/timholy/Grid.jl) package via the `pdf` method
(extended from Distributions.jl).

```
pdf(k::UnivariateKDE, x)
pdf(k::BivariateKDE, x, y)
```

where `x` and `y` are real numbers or arrays.

If you are making multiple calls to `pdf`, it will be more efficient to
construct an intermediate `InterpKDE` to store the intermediate `InterpGrid`
object:

```
ik = InterpKDE(k)
pdf(ik, x)
```

`InterpKDE` can also take additional arguments specifying the
`BoundaryCondition` (default=`BCnan`) and `InterpType` (default=
`InterpQuadratic`).


## Plotting

The [Winston.jl](https://github.com/nolta/Winston.jl) and
[PyPlot.jl](https://github.com/stevengj/PyPlot.jl) plotting packages are
currently supported. See the ijulia notebooks:
* [Winston](http://nbviewer.ipython.org/github/JuliaStats/KernelDensity.jl/blob/master/examples/Winston.ipynb)
* [PyPlot](http://nbviewer.ipython.org/github/JuliaStats/KernelDensity.jl/blob/master/examples/PyPlot.ipynb)

We plan to include support for other plotting packages: please file an issue
if your favourite one is not yet available.
