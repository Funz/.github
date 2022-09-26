
# TL;DR

Parametric scientific computing ?

* Take a "parametrized" __input file__ (contains `$x`, `$y`,... replacing some numerical values),
* Define values taken by these __parameter__ (or let a dedicated algorithm do),
* Run calculations on __remote__ ressources,
* Parse results & __analyse__.

Funz eases this "functional" wrapping of your simulation.


# Examples

Funz makes your simulations callable as functions for any of cmd.exe / bash / python / R / Java:

* From Excel spreadsheet "sheet.xlsx", get cell commented with "=total" for each 10 different values of cell commented with "$income": \
    ```cmd
    Funz.bat Run -m Excel -if sheet.xlsx -iv income=10,20,30,35,40,42,45,47,50,52 -oe total`
    ```
* Search convection coefficient which leads to a minimal temperature of 25.5 degrees with OpenModelica PDE solver: \
    ```bash
    Funz.sh RunDesign -m OpenModelica -if NewtonCooling.mo -iv convection=[0.5,1.0] -oe "min(T)" -d Brent -do ytarget=25.5`
    ```
* Build a response surface of MySimulator for x1 in [-5,5] and x2 in [-5,5]:
    ```python
    # pip install Funz
    import Funz
    X = {'x1':np.arange(-5, 5, 0.25),'x2':np.arange(-5, 5, 0.25)}
    y = Funz.Run(model="MySimulator",input_files=["inputfile1","inputfile2"],input_variables= X ,all_combinations=True)['y']
    # mplt.plot_surface(X['x1'], X['x2'], y)
    ```
* Propagate x1 as uniform random and x2 as normal random uncertainties through MySimulator:
    ```r
    # devtools::install_github("Funz/Funz.R")
    library(Funz)
    hist( Funz::Run(model="MySimulator",input.files=c("inputfile1","inputfile2"),input.variables= list(x1=runif(100),x2=rnorm(100)) )['y'] )
    ```
* Chain simulations:
    ```python
    # pip install Funz
    import Funz
    X = {'x1':..., 'x2':...}
    # first simulator, inputfile1 and inputfile2 contain 'x1' and 'x2' variables
    y1 = Funz.Run(model="MySimulator1",input_files=["inputfile1","inputfile2"],input_variables= X)['y']
    # second simulator, depends on results of first one also, inputfile3 contains 'x1', 'x2' and 'y1' variables
    y2 = Funz.Run(model="MySimulator2",input_files=["inputfile3"],input_variables= X.update(y1))['z']
    # post-processing in Excel (postrpro.xlsx sheet contains cells commented by '$x1' or '$x2'), returns cell value commented as "=result"
    result  = Funz.Run(model="Excel",input_files=["postpro.xlsx"],input_variables= X.update(y2))['result']
    ```
