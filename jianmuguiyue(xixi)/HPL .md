# HPL的输出结果

```
--------------------------------------------------------------------------
By default, for Open MPI 4.0 and later, infiniband ports on a device
are not used by default.  The intent is to use UCX for these devices.
You can override this policy by setting the btl_openib_allow_ib MCA parameter
to true.

  Local host:              login
  Local adapter:           ibp134s0
  Local port:              1

--------------------------------------------------------------------------
--------------------------------------------------------------------------
WARNING: There was an error initializing an OpenFabrics device.

  Local host:   login
  Local device: ibp134s0
--------------------------------------------------------------------------
================================================================================
HPLinpack 2.3  --  High-Performance Linpack benchmark  --   December 2, 2018
Written by A. Petitet and R. Clint Whaley,  Innovative Computing Laboratory, UTK
Modified by Piotr Luszczek, Innovative Computing Laboratory, UTK
Modified by Julien Langou, University of Colorado Denver
================================================================================

An explanation of the input/output parameters follows:
T/V    : Wall time / encoded variant.
N      : The order of the coefficient matrix A.
NB     : The partitioning blocking factor.
P      : The number of process rows.
Q      : The number of process columns.
Time   : Time in seconds to solve the linear system.
Gflops : Rate of execution for solving the linear system.

The following parameter values will be used:

N      :    3000
NB     :     256
PMAP   : Row-major process mapping
P      :       1
Q      :       1
PFACT  :    Left    Crout    Right
NBMIN  :       2        4
NDIV   :       2
RFACT  :    Left    Crout    Right
BCAST  :   1ring
DEPTH  :       0
SWAP   : Mix (threshold = 64)
L1     : transposed form
U      : transposed form
EQUIL  : yes
ALIGN  : 8 double precision words

--------------------------------------------------------------------------------

- The matrix A is randomly generated for each test.
- The following scaled residual check will be computed:
      ||Ax-b||_oo / ( eps * ( || x ||_oo * || A ||_oo + || b ||_oo ) * N )
- The relative machine precision (eps) is taken to be               1.110223e-16
- Computational tests pass if scaled residuals are less than                16.0

================================================================================
T/V                N    NB     P     Q               Time                 Gflops
--------------------------------------------------------------------------------
WR00L2L2        3000   256     1     1               0.22             8.1351e+01
HPL_pdgesv() start time Wed Feb 19 22:32:06 2025

HPL_pdgesv() end time   Wed Feb 19 22:32:06 2025

--------------------------------------------------------------------------------
||Ax-b||_oo/(eps*(||A||_oo*||x||_oo+||b||_oo)*N)=   3.52656083e-03 ...... PASSED
================================================================================
T/V                N    NB     P     Q               Time                 Gflops
--------------------------------------------------------------------------------
WR00L2L4        3000   256     1     1               0.20             8.8592e+01
HPL_pdgesv() start time Wed Feb 19 22:32:08 2025

HPL_pdgesv() end time   Wed Feb 19 22:32:08 2025

--------------------------------------------------------------------------------
||Ax-b||_oo/(eps*(||A||_oo*||x||_oo+||b||_oo)*N)=   4.42512389e-03 ...... PASSED
[login:1088026] 39 more processes have sent help message help-mpi-btl-openib.txt / ib port not selected
[login:1088026] Set MCA parameter "orte_base_help_aggregate" to 0 to see all help / error messages
[login:1088026] 39 more processes have sent help message help-mpi-btl-openib.txt / error in device init
================================================================================
T/V                N    NB     P     Q               Time                 Gflops
--------------------------------------------------------------------------------
WR00L2C2        3000   256     1     1               0.21             8.5469e+01
HPL_pdgesv() start time Wed Feb 19 22:32:11 2025

HPL_pdgesv() end time   Wed Feb 19 22:32:11 2025

--------------------------------------------------------------------------------
||Ax-b||_oo/(eps*(||A||_oo*||x||_oo+||b||_oo)*N)=   3.52656083e-03 ...... PASSED
================================================================================
T/V                N    NB     P     Q               Time                 Gflops
--------------------------------------------------------------------------------
WR00L2C4        3000   256     1     1               0.21             8.6867e+01
HPL_pdgesv() start time Wed Feb 19 22:32:13 2025

HPL_pdgesv() end time   Wed Feb 19 22:32:13 2025

--------------------------------------------------------------------------------
||Ax-b||_oo/(eps*(||A||_oo*||x||_oo+||b||_oo)*N)=   4.42512389e-03 ...... PASSED
================================================================================
T/V                N    NB     P     Q               Time                 Gflops
--------------------------------------------------------------------------------
WR00L2R2        3000   256     1     1               0.21             8.5439e+01
HPL_pdgesv() start time Wed Feb 19 22:32:15 2025

HPL_pdgesv() end time   Wed Feb 19 22:32:15 2025

--------------------------------------------------------------------------------
||Ax-b||_oo/(eps*(||A||_oo*||x||_oo+||b||_oo)*N)=   4.30504331e-03 ...... PASSED
================================================================================
T/V                N    NB     P     Q               Time                 Gflops
--------------------------------------------------------------------------------
WR00L2R4        3000   256     1     1               0.22             8.3244e+01
HPL_pdgesv() start time Wed Feb 19 22:32:17 2025

HPL_pdgesv() end time   Wed Feb 19 22:32:17 2025

--------------------------------------------------------------------------------
||Ax-b||_oo/(eps*(||A||_oo*||x||_oo+||b||_oo)*N)=   3.66281630e-03 ...... PASSED
================================================================================
T/V                N    NB     P     Q               Time                 Gflops
--------------------------------------------------------------------------------
WR00C2L2        3000   256     1     1               0.22             8.1395e+01
HPL_pdgesv() start time Wed Feb 19 22:32:20 2025

HPL_pdgesv() end time   Wed Feb 19 22:32:20 2025

--------------------------------------------------------------------------------
||Ax-b||_oo/(eps*(||A||_oo*||x||_oo+||b||_oo)*N)=   3.52656083e-03 ...... PASSED
================================================================================
T/V                N    NB     P     Q               Time                 Gflops
--------------------------------------------------------------------------------
WR00C2L4        3000   256     1     1               0.22             8.1676e+01
HPL_pdgesv() start time Wed Feb 19 22:32:22 2025

HPL_pdgesv() end time   Wed Feb 19 22:32:22 2025

--------------------------------------------------------------------------------
||Ax-b||_oo/(eps*(||A||_oo*||x||_oo+||b||_oo)*N)=   4.42512389e-03 ...... PASSED
================================================================================
T/V                N    NB     P     Q               Time                 Gflops
--------------------------------------------------------------------------------
WR00C2C2        3000   256     1     1               0.22             8.3049e+01
HPL_pdgesv() start time Wed Feb 19 22:32:24 2025

HPL_pdgesv() end time   Wed Feb 19 22:32:24 2025

--------------------------------------------------------------------------------
||Ax-b||_oo/(eps*(||A||_oo*||x||_oo+||b||_oo)*N)=   3.52656083e-03 ...... PASSED
================================================================================
T/V                N    NB     P     Q               Time                 Gflops
--------------------------------------------------------------------------------
WR00C2C4        3000   256     1     1               0.22             8.0704e+01
HPL_pdgesv() start time Wed Feb 19 22:32:26 2025

HPL_pdgesv() end time   Wed Feb 19 22:32:27 2025

--------------------------------------------------------------------------------
||Ax-b||_oo/(eps*(||A||_oo*||x||_oo+||b||_oo)*N)=   4.42512389e-03 ...... PASSED
================================================================================
T/V                N    NB     P     Q               Time                 Gflops
--------------------------------------------------------------------------------
WR00C2R2        3000   256     1     1               0.19             9.2390e+01
HPL_pdgesv() start time Wed Feb 19 22:32:29 2025

HPL_pdgesv() end time   Wed Feb 19 22:32:29 2025

--------------------------------------------------------------------------------
||Ax-b||_oo/(eps*(||A||_oo*||x||_oo+||b||_oo)*N)=   4.30504331e-03 ...... PASSED
================================================================================
T/V                N    NB     P     Q               Time                 Gflops
--------------------------------------------------------------------------------
WR00C2R4        3000   256     1     1               0.19             9.6493e+01
HPL_pdgesv() start time Wed Feb 19 22:32:31 2025

HPL_pdgesv() end time   Wed Feb 19 22:32:31 2025

--------------------------------------------------------------------------------
||Ax-b||_oo/(eps*(||A||_oo*||x||_oo+||b||_oo)*N)=   3.66281630e-03 ...... PASSED
================================================================================
T/V                N    NB     P     Q               Time                 Gflops
--------------------------------------------------------------------------------
WR00R2L2        3000   256     1     1               0.23             7.9803e+01
HPL_pdgesv() start time Wed Feb 19 22:32:33 2025

HPL_pdgesv() end time   Wed Feb 19 22:32:34 2025

--------------------------------------------------------------------------------
||Ax-b||_oo/(eps*(||A||_oo*||x||_oo+||b||_oo)*N)=   3.52656083e-03 ...... PASSED
================================================================================
T/V                N    NB     P     Q               Time                 Gflops
--------------------------------------------------------------------------------
WR00R2L4        3000   256     1     1               0.24             7.5700e+01
HPL_pdgesv() start time Wed Feb 19 22:32:36 2025

HPL_pdgesv() end time   Wed Feb 19 22:32:36 2025

--------------------------------------------------------------------------------
||Ax-b||_oo/(eps*(||A||_oo*||x||_oo+||b||_oo)*N)=   4.42512389e-03 ...... PASSED
================================================================================
T/V                N    NB     P     Q               Time                 Gflops
--------------------------------------------------------------------------------
WR00R2C2        3000   256     1     1               0.21             8.5512e+01
HPL_pdgesv() start time Wed Feb 19 22:32:38 2025

HPL_pdgesv() end time   Wed Feb 19 22:32:38 2025

--------------------------------------------------------------------------------
||Ax-b||_oo/(eps*(||A||_oo*||x||_oo+||b||_oo)*N)=   3.52656083e-03 ...... PASSED
================================================================================
T/V                N    NB     P     Q               Time                 Gflops
--------------------------------------------------------------------------------
WR00R2C4        3000   256     1     1               0.21             8.4349e+01
HPL_pdgesv() start time Wed Feb 19 22:32:40 2025

HPL_pdgesv() end time   Wed Feb 19 22:32:40 2025

--------------------------------------------------------------------------------
||Ax-b||_oo/(eps*(||A||_oo*||x||_oo+||b||_oo)*N)=   4.42512389e-03 ...... PASSED
================================================================================
T/V                N    NB     P     Q               Time                 Gflops
--------------------------------------------------------------------------------
WR00R2R2        3000   256     1     1               0.22             8.0695e+01
HPL_pdgesv() start time Wed Feb 19 22:32:42 2025

HPL_pdgesv() end time   Wed Feb 19 22:32:43 2025

--------------------------------------------------------------------------------
||Ax-b||_oo/(eps*(||A||_oo*||x||_oo+||b||_oo)*N)=   4.30504331e-03 ...... PASSED
================================================================================
T/V                N    NB     P     Q               Time                 Gflops
--------------------------------------------------------------------------------
WR00R2R4        3000   256     1     1               0.24             7.4466e+01
HPL_pdgesv() start time Wed Feb 19 22:32:45 2025

HPL_pdgesv() end time   Wed Feb 19 22:32:45 2025

--------------------------------------------------------------------------------
||Ax-b||_oo/(eps*(||A||_oo*||x||_oo+||b||_oo)*N)=   3.66281630e-03 ...... PASSED
================================================================================

Finished     18 tests with the following results:
             18 tests completed and passed residual checks,
              0 tests completed and failed residual checks,
              0 tests skipped because of illegal input values.
--------------------------------------------------------------------------------

End of Tests.
================================================================================
```
