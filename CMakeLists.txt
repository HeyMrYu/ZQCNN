cmake_minimum_required(VERSION 3.0)

project(ZQCNN)

set(CMAKE_CXX_STANDARD 11)

if(CMAKE_SIZEOF_VOID_P EQUAL 8)
	set(archbit "x64")
else()
	set(archbit "x86")
endif()
if(WIN32)
	set(archos "win32")
elseif(UNIX)
	set(archos "unix")
endif()

set(CMAKE_RUNTIME_OUTPUT_DIRECTORY  ${CMAKE_CURRENT_LIST_DIR}/cmake-out-${archos}-${archbit}/${CMAKE_BUILD_TYPE})
#message("CMAKE_RUNTIME_OUTPUT_DIRECTORY  ${CMAKE_RUNTIME_OUTPUT_DIRECTORY}")
file(MAKE_DIRECTORY ${CMAKE_RUNTIME_OUTPUT_DIRECTORY})

if(WIN32)
	execute_process(COMMAND "MKLINK" /D ${CMAKE_CURRENT_LIST_DIR}/data ${CMAKE_RUNTIME_OUTPUT_DIRECTORY}/data)
	execute_process(COMMAND "MKLINK" /D ${CMAKE_CURRENT_LIST_DIR}/model ${CMAKE_RUNTIME_OUTPUT_DIRECTORY}/model)
elseif(UNIX)
	execute_process(COMMAND "ln" -nsf ${CMAKE_CURRENT_LIST_DIR}/data ${CMAKE_RUNTIME_OUTPUT_DIRECTORY}/data)
	execute_process(COMMAND "ln" -nsf ${CMAKE_CURRENT_LIST_DIR}/model ${CMAKE_RUNTIME_OUTPUT_DIRECTORY}/model)
endif()

if((CMAKE_CXX_COMPILER_ID MATCHES "GNU") OR (CMAKE_CXX_COMPILER_ID MATCHES "Clang"))
		add_compile_options(-mavx2)
elseif(CMAKE_CXX_COMPILER_ID MATCHES "Intel")
		add_compile_options(/QxAVX2)
elseif(CMAKE_CXX_COMPILER_ID MATCHES "MSVC")
		add_compile_options(/arch:AVX2)
endif()

set(ZQCNN_INCLUDE_DIRS ${CMAKE_CURRENT_LIST_DIR}/ZQ_GEMM
                        ${CMAKE_CURRENT_LIST_DIR}/ZQCNN
                        ${CMAKE_CURRENT_LIST_DIR}/3rdparty/include                        
                        CACHE INTERNAL "ZQCNN include dirs")            
set(ZQCNN_LIBRARY_DIR ${CMAKE_CURRENT_LIST_DIR}/3rdparty/lib)                                    

add_subdirectory(ZQ_GEMM)
add_subdirectory(ZQCNN)
add_subdirectory(SamplesZQCNN)
add_subdirectory(SamplesZQlibFaceID)
