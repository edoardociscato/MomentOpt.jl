

# MOpt.jl: Moment Optimization Library for Julia

This package implements several MCMC algorithms to optimize a non-differentiable objective function, which depends on a set of parameter values and a set of data moments. In general, this implements *Simulated Method of Moments*. The library is targeted to run MCMC on an SGE cluster, where each node is a chain.

For an R implementation which is the basis of this package, see [https://github.com/tlamadon/mopt](https://github.com/tlamadon/mopt)

## API

* the custom type is called `Moptim`
* the module is called `Mopt`

### Objective Function

you need an objective function that has an input/output structure as follows:

#### Input: 

1. parameter vector `p`: parameterized as a `Dict`. Thought about custom types (hard to implement because I'd have to guess what exactly that type looks like), and simple (unnamed) vectors. Took `Dict` because closest to `R list()`
2. data moments. 
3. `whichmom`: `Array{ASCIIString}` of moment names to which you want the objective function subset to in the current estimation

#### Output: 
1. function value (model deviation from data moments - metric up to the user): `Float64`
2. custom model info (user specific. could be a `Dict`)

### Initiating the `Moptim` Object

set up a `Moptim` object by calling the constructor. there is a list of **compulsory arguments**, which must be supplied in the following **order**: 

1. `p`: the initial value of the full parameter vector. (Note: your objective function must be callable as `objfunc(p,moments,whichmom)`).
2. `params_to_sample`: a `DataFrame` with as many rows as parameters you want to sample. (all other parameters remain fixed). There **must** be three columns called `name` (name of param to sample), `lb` (lower bound) and `ub` (upper bound)
3. `objfunc`: `ASCIIString` containing the name of your objective function
4. `moments`: a `DataFrame` with 3 columns, `name`, `data` and `sd`. This is the complete set of moments that you have in the data / that is produced by your model

Then there is a list of **optional arguments** which have to be supplied by *keyword*. If you don't supply them, we'll take a default value:

* `moments_to_use`: a vector of moment names you want to use in the current estimatiom. This subsets `moments`. Default=all
* `shock_var`: variance of shocks. Default=1 
* `np_shock`: new parameter shock. Default=1
* `save_freq`: after how many iterations to save to disk. Default=25
* `N`: Number of chains. If `mode=="mpi"`, choose `N==numberOfNodes`. Default=3
* `n_untempered`: number of untempered chains. Default =1
* `mode`: "serial" or "mpi". Default="serial"
* `paths`: a `Dict` with keys
	* `chain`: filename of where to save chain data. Default=`"."`
	* `lastparam`: filename of where to save last param. Default=`"."`
	* `errorparam`: filename of where to save errors. Default=`"."`
	* `wd`: working directory. Default=`"."`
	* `source_on_nodes`: file to source on workers. Default="workers.jl"
	* `logdir`: directory where logs are saved. Default="./log"



### Running the estimation

call `runMOpt(mymopt)`.





