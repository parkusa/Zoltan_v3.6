INCLUDE(PackageAddExecutableAndTest)
INCLUDE(PackageCopyFilesToBinaryDir)


PACKAGE_ADD_EXECUTABLE_AND_TEST(
  simpleRCB_f90
  SOURCES simpleRCB.f90 zoltanRCBmod.f90
  ARGS 
  COMM mpi
  NUM_MPI_PROCS 1-4
  )

PACKAGE_COPY_FILES_TO_BINARY_DIR(FortranExamplesCopyFiles
  SOURCE_FILES mesh.txt 
 )
