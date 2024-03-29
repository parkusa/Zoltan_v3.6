####  Main Zoltan CTEST File
####  This file can be copied and modified for new Zoltan test directories.
####  It runs script zoltan/test/ctest_zoltan.pl.
####  See ctest_zoltan.pl for the list of arguments to provide to it.
####
####  To use this file in new test directories, copy it to the new test
####  directory.  In the copy, modify the number of processors to use in MPI
####  runs, ZOLTANNUMPROCS, in the SET command below.
####  Also modify the test name (e.g., substitute the new directory name
####  for "hg_diag500_4" in all lines below.)

# Number of processors to use in MPI runs and test directory.
SET(ZOLTANNUMPROCS 4)

# Test directory info.
SET(ZZNAME diag500_4)
SET(ZZDIRNAME hg_${ZZNAME})

#### For most test directories, nothing further needs to be changed to change
#### the test.  (Any directory where the input graph/coords/etc. do not begin
#### with ${ZZNAME} is an exception; the copy macro at the end of this file
#### must be modified.)

INCLUDE(PackageAddTest)
INCLUDE(PackageAddAdvancedTest)
INCLUDE(PackageCopyFilesToBinaryDir)

# All data files needed for tested (*.graph, *.mtx, *.mtxp, *.coords, *.hg)
FILE(GLOB DATA_INP RELATIVE ${CMAKE_CURRENT_SOURCE_DIR} ${ZZNAME}.*)

# All zdrive.inp* and hier*inp files needed for testing.
FILE(GLOB ZDRIVE_INP RELATIVE ${CMAKE_CURRENT_SOURCE_DIR} zdrive.inp.*)
FILE(GLOB HIER_INP RELATIVE ${CMAKE_CURRENT_SOURCE_DIR} hier*.inp)

# All accepted answer files needed for testing.
FILE(GLOB ANSWERS RELATIVE ${CMAKE_CURRENT_SOURCE_DIR} answers/${ZZNAME}.*)

# Build test arguments
SET(ZOLTAN_TEST_ARGS --np ${ZOLTANNUMPROCS} --debug --mpiexec ${MPI_EXEC} --mpiexecarg ${MPI_EXEC_NUMPROCS_FLAG} --pkg )

# Parallel Tests
PACKAGE_ADD_ADVANCED_TEST(
  ${ZZDIRNAME}_zoltan_parallel
  # Test Native Zoltan algorithms in parallel
  TEST_0 ${ZOLTAN_TEST_DRIVER} ${ZOLTAN_TEST_ARGS} Zoltan 
  CATEGORIES NIGHTLY PERFORMANCE
  COMM mpi
  OVERALL_NUM_MPI_PROCS ${ZOLTANNUMPROCS} 
  FINAL_FAIL_REGULAR_EXPRESSION "FAILED"
)

SET(ZOLTAN_SERIAL_TEST_ARGS --np 1 --debug --pkg )

# Serial Tests
PACKAGE_ADD_ADVANCED_TEST(
  ${ZZDIRNAME}_zoltan_serial
  # Test Native Zoltan algorithms in serial
  TEST_0 ${ZOLTAN_TEST_DRIVER} ${ZOLTAN_SERIAL_TEST_ARGS} Zoltan
  CATEGORIES NIGHTLY PERFORMANCE
  COMM serial
  FINAL_FAIL_REGULAR_EXPRESSION "FAILED"
  )

# Command that copies files to the executable directory.
PACKAGE_COPY_FILES_TO_BINARY_DIR(Zoltan_${ZZDIRNAME}_copy_files
  SOURCE_FILES
    ${DATA_INP}
    ${ZDRIVE_INP}
    ${HIER_INP}
    ${ANSWERS}
  EXEDEPS zdrive
  NOEXEPREFIX
)
