# Lakas
Game parameter optimizer using [nevergrad](https://github.com/facebookresearch/nevergrad) framework.

Explore on sample [interactive plot](https://fsmosca.github.io/Lakas/) once an optimization session is done.

![Sample plot](https://i.imgur.com/fVPN366.png)

### Optimization overview
We run an engine vs engine match of 1000 games or so, one engine is called the `test_engine` and the other is called `base_engine`. The base_engine will use the default parameter values while the test_engine will use the parameter values that are suggested by the optimizer. Match result is (wins + draws/2) / games. If the result is 0.55 in favor of the test_engine, that means that the parameters suggested by the optimizer is better than the default. We then report back this result to the optimizer as `1 - 0.55` or
0.45 since we are minimizing. All match result should be reported to the optimizer. In the next match we will ask another parameter values to try for the test_engine then play it against the base_engine. This process continues until we reach the maximum budgets. The parameter values that will be recommended by the optimizer at the end of the optimization will be our best parameter values.


## A. Setup
### Specific installation example
A guide to setup Lakas on [virtual environment for windows 10](https://github.com/fsmosca/Lakas/wiki/Windows-10-setup).

### General installation guide
* Install python 3.8 or later.  
* Install nevergrad.  
  * pip install nevergrad
* Install hiplot for plotting.  
  * pip install hiplot
* Install psutil to log cpu and memory usage of python.  
  * pip install psutil  
* Download [Lakas](https://github.com/fsmosca/Lakas/archive/main.zip)
  
## B. Command line
See also the examples folder.  

```
python lakas.py --match-manager-path c:/chess/cutechess/cutechess-cli.exe --concurrency 1 --optimizer bayesopt --bo-utility-kind ucb --output-data-file bayesopt_ucb.dat --input-data-file bayesopt_ucb.dat --optimizer-log-file opt_log_plot_ucb.txt --base-time-sec 5 --inc-time-sec 0.05 --budget 100 --games-per-budget 200 --engine ./engines/stockfish-modern/stockfish.exe --input-param "{'RazorMargin': {'init':527, 'lower':100, 'upper':700}, 'KingAttackWeights[2]': {'init':81, 'lower':0, 'upper':150}, 'eMobilityBonus[0][7]': {'init':20, 'lower':-20, 'upper':50}, 'mMobilityBonus[1][7]': {'init':63, 'lower':-50, 'upper':150}, 'eMobilityBonus[3][21]': {'init':168, 'lower':50, 'upper':250}, 'eThreatByRook[1]': {'init':46, 'lower':0, 'upper':150}, 'mThreatByMinor[3]': {'init':77, 'lower':0, 'upper':150}, 'eThreatByKing': {'init':89, 'lower':10, 'upper':150}, 'eThreatByPawnPush': {'init':39, 'lower':0, 'upper':100}}" --opening-file ./start_opening/ogpt_chess_startpos.epd
```

### Help
`python lakas.py -h`  
See [help page](https://github.com/fsmosca/Lakas/wiki/Help) in the wiki.

### Saving hiplot plot
`python lakas.py --optimizer-log-file opt_oneplusone.txt ...`  
After the optimization, plots will be saved in `opt_oneplusone.txt.html file.`

### Parameter Input
Given a uci engine with uci option:  
  * option name mThreatByKing type spin default 24 min 0 max 400
  * option name mThreatByPawnPush type spin default 48 min 0 max 400

```
python lakas.py --input-param "{'mThreatByKing': {'init': 24, 'lower': 0, 'upper': 400}, 'mThreatByPawnPush': {'init': 48, 'lower': 0, 'upper': 400}}" ...
```

If there is a space in the parameter name like `option name Futility Margin type spin default 100 min 0 max 200`  
`--input-param "{'\"Futility Margin\"': {'init': 24, 'lower': 0, 'upper': 400}}"`

#### Parameter Categorical and float values
Given `option name mThreatByKing type spin default 24 min 0 max 400`  
`--input-param "{'mThreatByKing': [24, 16, 20, 28, 32]}"`  
Note the first value should be the default, in this case its 24.  

Or with true or false.  
`--input-param "{'MultiGather': ['false', 'true']}"`  

or with float value  
`--input-param "{'CPuct': [1.745, 1.675, 1.875]}"` 

can also be  
`--input-param "{'CPuct': {'init': 1.745, 'lower': 1.0, 'upper': 3.0}}"`  

or a combination  
`--input-param "{'MultiGather': ['false', 'true'], 'CPuct': {'init': 1.745, 'lower': 1.0, 'upper': 3.0}}"`

## C. Supported NeverGrad Optimizers
* [OnePlusOne](https://facebookresearch.github.io/nevergrad/optimizers_ref.html#nevergrad.optimization.optimizerlib.ParametrizedOnePlusOne)
* [TBPSA](https://facebookresearch.github.io/nevergrad/optimizers_ref.html#nevergrad.optimization.optimizerlib.ParametrizedTBPSA) (Test-based population-size adaptation)
* [Bayessian Optimization](https://facebookresearch.github.io/nevergrad/optimizers_ref.html?highlight=logger#nevergrad.optimization.optimizerlib.ParametrizedBO)
* [SPSA](https://facebookresearch.github.io/nevergrad/optimizers_ref.html?highlight=spsa#nevergrad.optimization.optimizerlib.SPSA) (Simultaneous Perturbation Stochastic Approximation)
* [CMAES](https://facebookresearch.github.io/nevergrad/optimizers_ref.html#nevergrad.optimization.optimizerlib.ParametrizedCMA) (Covariance Matrix Adaptation Evolution Strategy)
* [NGOpt](https://facebookresearch.github.io/nevergrad/optimizers_ref.html#nevergrad.optimization.optimizerlib.NGOpt) and [article](https://arxiv.org/pdf/2004.14014.pdf)

`--optimizer oneplusone ...`  
`--optimizer tbpsa ...`  
`--optimizer bayesopt ...`  
`--optimizer spsa ...`  
`--optimizer cmaes ...`  
`--optimizer ngopt ...`

## D. Sample optimization sessions
* [Search and Evaluation optimization](https://github.com/fsmosca/Lakas/wiki/Search-and-Evaluation-parameter-Optimization)
* [Optimization comparison](https://github.com/fsmosca/Lakas/wiki/Optimization-Comparison)


## E. Resuming a cancelled optimization
Use the option  
`--output-data-file myopt.dat ...`  
to save the optimization data into the file `myopt.dat`. You may resume the optimization by using the option  
`--input-data-file myopt.dat ...`

#### Note
If you use oneplusone optimizer, you should use it to resume the oneplusone optimization.  
Example:  
`python lakas.py --output-data-file oneplusone.dat --optimizer oneplusone ...`  

After 2 budgets or so, optimization is cancelled. To resume:  
`python lakas.py --input-data-file oneplusone.dat --output-data-file oneplusone.dat --optimizer oneplusone ...`  

The 2 budgets stored in `oneplusone.dat` are still there and new budgets will be added on that same data file.

If your optimizer is tbpsa save it to a different file.  
`python lakas.py --output-data-file tbpsa.dat --optimizer tbpsa ...`  

## F. Credits
* [NeverGrad](https://github.com/facebookresearch/nevergrad)
* [Cutechess](https://github.com/cutechess/cutechess)
* [Stockfish](https://stockfishchess.org/)
* [HiPlot](https://github.com/facebookresearch/hiplot)
