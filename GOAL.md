Create a RISC OS Module which implements the interfaces in the RISC OS PRM chapter 'NetStatus'. Commit code at useful marker points.
Create BASIC program called `TestProgram,fd1` which issues `SYS "OS_CallAVector",<reason>,<r1>,,,,,,,,EconetV` calls for each of the cases we support.
Run the BASIC program with `riscos-build-run rm32/NetStatus,ffa TestProgram,ffa --command "rmload NetStatus" --command "TestProgram"` and check that the hourglass changes state as expected for each case.
Create a README.md to describe the project.

