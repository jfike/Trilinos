
TRIBITS_ADD_EXECUTABLE_AND_TEST(
  ImportExport_UnitTests
  SOURCES
    ImportExport_UnitTests
    ${TEUCHOS_STD_UNIT_TEST_MAIN}
  COMM serial mpi
  STANDARD_PASS_OUTPUT
  )

TRIBITS_ADD_EXECUTABLE_AND_TEST(
  Import_Union
  SOURCES
    Import_Union
    ${TEUCHOS_STD_UNIT_TEST_MAIN}
  COMM serial mpi
  ARGS "--globally-reduce-test-result --output-show-proc-rank --output-to-root-rank-only=-1"
  STANDARD_PASS_OUTPUT
  )

TRIBITS_ADD_EXECUTABLE_AND_TEST(
  SubmapExportTests
  SOURCES
    SubmapExport
    ${TEUCHOS_STD_UNIT_TEST_MAIN}
  COMM mpi
  NUM_MPI_PROCS 2
  STANDARD_PASS_OUTPUT
  )

TRIBITS_ADD_EXECUTABLE_AND_TEST(
  SubmapImportTests
  SOURCES
    SubmapImport
    ${TEUCHOS_STD_UNIT_TEST_MAIN}
  COMM mpi
  NUM_MPI_PROCS 2
  STANDARD_PASS_OUTPUT
  )

IF (${PROJECT_NAME}_ENABLE_Epetra)
  IF(NOT Trilinos_NO_32BIT_GLOBAL_INDICES AND Tpetra_INST_INT_INT)
    # Tpetra bug 5430:
    # https://software.sandia.gov/bugzilla/show_bug.cgi?id=5430
    #
    # The bug only manifests with 4 MPI processes.  The test currently
    # requires Epetra; it compares Epetra and Tpetra results.
    TRIBITS_ADD_EXECUTABLE_AND_TEST(
      ImportBug5430
      SOURCES
      ImportBug5430Test.cpp
      COMM mpi
      STANDARD_PASS_OUTPUT
      NUM_MPI_PROCS 4
      )
  ENDIF ()
ENDIF ()

IF (Tpetra_INST_INT_LONG_LONG OR Tpetra_INST_INT_LONG OR Tpetra_INST_INT_INT OR Tpetra_INST_INT_UNSIGNED_LONG OR Tpetra_INST_INT_UNSIGNED)
  # Test whether Export to a CrsMatrix with a static graph works.
  TRIBITS_ADD_EXECUTABLE_AND_TEST(
    ExportToStaticGraphCrsMatrix
    SOURCES
      ExportToStaticGraphCrsMatrix.cpp
    COMM serial mpi
    STANDARD_PASS_OUTPUT
    NUM_MPI_PROCS 4
    )
ENDIF()

# Test Tpetra::Details::makeOptimizedColMap and
# Tpetra::Details::makeOptimizedColMapAndImport.
#
# This test really only makes sense with an MPI build, since
# optimizing the column Map involves reordering remote indices.  The
# test already exercises the case where there are no remote indices.
TRIBITS_ADD_EXECUTABLE_AND_TEST(
  MakeOptColMap
  SOURCES
    MakeOptColMap.cpp
    ${TEUCHOS_STD_UNIT_TEST_MAIN}
  COMM mpi
  STANDARD_PASS_OUTPUT
  NUM_MPI_PROCS 4
  )

# mfh 18 Jul 2014: Bug 6170 test.  Thanks to Clark Dohrmann for
# contributing this test.  I modified it slightly to avoid file I/O
# and to catch and print exception messages on all processes.  The
# test must be run with exactly 8 MPI processes.
#
# NOTE: The default behavior of the Trilinos test framework is to
# exclude tests that require more than 4 MPI processes.  Thus, this
# test will NOT run by default.  To change this, set the CMake option
# MPI_EXEC_MAX_NUMPROCS to 8 or more in your Trilinos configuration.
TRIBITS_ADD_EXECUTABLE_AND_TEST(
  Bug6170
  SOURCES
    Bug6170.cpp
  COMM mpi
  STANDARD_PASS_OUTPUT
  NUM_MPI_PROCS 8
  )

TRIBITS_ADD_EXECUTABLE_AND_TEST(
  ReverseCommunication_Issue227
  SOURCES
    ReverseCommunication_Issue227.cpp
    ${TEUCHOS_STD_UNIT_TEST_MAIN}
  COMM mpi
  STANDARD_PASS_OUTPUT
  NUM_MPI_PROCS 2
  )

IF (Tpetra_INST_INT_INT)
  # mfh 17 May 2016: Test for Github Issue #114.
  # Test originally written by Chris Siefert.
  TRIBITS_ADD_EXECUTABLE_AND_TEST(
    Issue_114
    SOURCES Issue_114.cpp
    COMM mpi
    STANDARD_PASS_OUTPUT
    NUM_MPI_PROCS 4
    )
ENDIF ()
