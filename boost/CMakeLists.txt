
if( UNIX )
    set( boost_CMDbootstrap bootstrap.sh )
    set( boost_CMDb2 b2 )
else()
    message(FATAL_ERROR "boost building implemented only on unix")
endif()

########################################################################
#
# boost lib
#
########################################################################
find_package(Boost REQUIRED COMPONENTS serialization)
include_directories( include ${Boost_INCLUDE_DIRS} )
link_libraries( ${Boost_LIBRARIES} )

add_executable(boost boost.cpp)
target_link_libraries(boost PRIVATE Testing::core)
add_test(NAME test_boost COMMAND boost)
