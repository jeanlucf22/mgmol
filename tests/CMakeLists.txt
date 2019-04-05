find_package(PythonInterp 3.2 REQUIRED)

add_executable(testHDF5P
               ${CMAKE_SOURCE_DIR}/tests/testHDF5P.cc)
add_executable(testDirectionalReduce
               ${CMAKE_SOURCE_DIR}/tests/testDirectionalReduce.cc
               ${CMAKE_SOURCE_DIR}/src/sparse_linear_algebra/DirectionalReduce.cc
               ${CMAKE_SOURCE_DIR}/src/pb/PEenv.cc)
add_executable(testAndersonMix
               ${CMAKE_SOURCE_DIR}/tests/Anderson/testAndersonMix.cc
               ${CMAKE_SOURCE_DIR}/tests/Anderson/Solution.cc
               ${CMAKE_SOURCE_DIR}/src/AndersonMix.cc
               ${CMAKE_SOURCE_DIR}/src/tools/Timer.cc)

target_compile_definitions(testAndersonMix PUBLIC TESTING)

add_test(NAME testHDF5P
         COMMAND ${MPIEXEC} ${MPIEXEC_NUMPROC_FLAG} 4
                 ${CMAKE_CURRENT_BINARY_DIR}/testHDF5P)
add_test(NAME testDirectionalReduce
         COMMAND ${MPIEXEC} ${MPIEXEC_NUMPROC_FLAG} 4
                 ${CMAKE_CURRENT_BINARY_DIR}/testDirectionalReduce)
add_test(NAME testAndersonMix
         COMMAND ${CMAKE_CURRENT_BINARY_DIR}/testAndersonMix 20 2)

add_test(NAME testSiH4
         COMMAND ${PYTHON_EXECUTABLE} ${CMAKE_CURRENT_SOURCE_DIR}/SiH4/testSiH4.py
         ${MPIEXEC} ${MPIEXEC_NUMPROC_FLAG} 4
         ${CMAKE_CURRENT_BINARY_DIR}/../src/mgmol-opt
         ${CMAKE_CURRENT_SOURCE_DIR}/SiH4/mgmol.cfg
         ${CMAKE_CURRENT_SOURCE_DIR}/SiH4/sih4.xyz
         ${CMAKE_CURRENT_SOURCE_DIR}/../potentials)
add_test(NAME testCl2
         COMMAND ${PYTHON_EXECUTABLE} ${CMAKE_CURRENT_SOURCE_DIR}/Cl2_ONCVPSP_LDA/test.py
         ${MPIEXEC} ${MPIEXEC_NUMPROC_FLAG} 4
         ${CMAKE_CURRENT_BINARY_DIR}/../src/mgmol-opt
         ${CMAKE_CURRENT_SOURCE_DIR}/Cl2_ONCVPSP_LDA/mgmol.cfg
         ${CMAKE_CURRENT_SOURCE_DIR}/Cl2_ONCVPSP_LDA/coords.in
         ${CMAKE_CURRENT_SOURCE_DIR}/../potentials)

target_include_directories(testHDF5P PRIVATE ${HDF5_INCLUDE_DIRS})
target_include_directories(testAndersonMix PRIVATE
                           ${CMAKE_SOURCE_DIR}/tests/Anderson)

target_link_libraries(testHDF5P ${HDF5_LIBRARIES})
target_link_libraries(testAndersonMix  ${BLAS_LIBRARIES} ${Boost_LIBRARIES})

set_tests_properties(testSiH4 PROPERTIES REQUIRED_FILES
                     ${CMAKE_SOURCE_DIR}/potentials/pseudo.Si)