[TerraFERMA](http://terraferma.github.io) input files for:

* Wilson, Cian R., Spiegelman, Marc, van Keken, Peter E., Hacker, Bradley R., "Fluid flow in subduction zones: The role of solid rheology
and compaction pressure", EPSL, 2014, [doi:10.1016/j.epsl.2014.05.052](http://dx.doi.org/10.1016/j.epsl.2014.05.052)

All files are contained in the `data` directory.  Assuming a fully installed copy of TerraFERMA (and its companion utilities
repository) is available then all simulations may be run using:

    tfsimulationharness --run -r -- *.shml

NOTE: this may take some time and the simulations are currently configured to run on 16 processes.