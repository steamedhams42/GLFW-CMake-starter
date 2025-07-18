cmake_minimum_required( VERSION 3.10 )

project( LearnOpenGL )

find_package( OpenGL REQUIRED )

include_directories( ${OPENGL_INCLUDE_DIRS} )
include_directories(glad/include)

set( GLFW_BUILD_DOCS OFF CACHE BOOL  "GLFW lib only" )
set( GLFW_INSTALL OFF CACHE BOOL  "GLFW lib only" )
set( GLAD_GL "" )

add_subdirectory( glfw )

## Turn ON if intending to use gl.h from glfw.
## ON does not work with LearnOpenGL. Had to add "glad/include" manually.
option( GLFW-CMAKE-STARTER-USE-GLFW-GLAD "Use GLAD from GLFW" OFF )
if( GLFW-CMAKE-STARTER-USE-GLFW-GLAD )
    include_directories("${GLFW_SOURCE_DIR}/deps")
    set( GLAD_GL "${GLFW_SOURCE_DIR}/deps/glad/gl.h" )
endif()

if( MSVC )
    SET( CMAKE_EXE_LINKER_FLAGS "${CMAKE_EXE_LINKER_FLAGS} /ENTRY:mainCRTStartup" )
endif()

### Compile all files under src/
file(GLOB_RECURSE LearnOpenGL-src "src/*")

### Compiler warnings
if (CMAKE_CXX_COMPILER_ID STREQUAL "GNU")
    add_compile_options(-Wmaybe-uninitialized) # Variable improperly initialized
    add_compile_options(-Wunused-variable) # Variable is defined but unused
endif()

add_executable( LearnOpenGL WIN32 ${LearnOpenGL-src} ${GLAD_GL} )
target_link_libraries( LearnOpenGL ${OPENGL_LIBRARIES} glfw )
# Allows std::cout to work in terminal
target_link_options(LearnOpenGL PRIVATE -Wl,--subsystem,console)
if( MSVC )
    if(${CMAKE_VERSION} VERSION_LESS "3.6.0")
        message( "\n\t[ WARNING ]\n\n\tCMake version lower than 3.6.\n\n\t - Please update CMake and rerun; OR\n\t - Manually set 'LearnOpenGL' as StartUp Project in Visual Studio.\n" )
    else()
        set_property( DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR} PROPERTY VS_STARTUP_PROJECT LearnOpenGL )
    endif()
endif()
