set(flatbuffers_PREFIX ${CMAKE_CURRENT_BINARY_DIR}/external/flatbuffers)
set(flatbuffers_INCLUDE ${flatbuffers_PREFIX}/include)
ExternalProject_Add(
        flatbuffers_dep
        PREFIX ${flatbuffers_PREFIX}
        URL "https://github.com/google/flatbuffers/archive/v1.10.0.tar.gz"
        CMAKE_ARGS -DCMAKE_INSTALL_PREFIX=${flatbuffers_PREFIX} -DCMAKE_BUILD_TYPE=Release
        LOG_UPDATE ON
        LOG_CONFIGURE ON
        LOG_BUILD ON
)

set(flatbuffers_GENERATOR ${flatbuffers_PREFIX}/bin/flatc)
set(flatbuffers_SOURCE_DIR ${CMAKE_SOURCE_DIR}/flatbuffers)
set(flatbuffers_SCHEMA_FILE ${flatbuffers_SOURCE_DIR}/monster.fbs)
set(flatbuffers_GENERATED_FILE ${flatbuffers_SOURCE_DIR}/monster_generated.h)
add_custom_command(
        DEPENDS ${flatbuffers_SCHEMA_FILE}
        COMMAND ${flatbuffers_GENERATOR}
        ARGS --cpp -o ${flatbuffers_SOURCE_DIR}/ ${flatbuffers_SCHEMA_FILE}
        OUTPUT ${flatbuffers_GENERATED_FILE}
        COMMENT "Executing FlatBuffers compiler")
set_source_files_properties(${flatbuffers_GENERATED_FILE} PROPERTIES GENERATED TRUE)


add_executable(flatbuffers flatbuffers.cpp ${SOURCE_FILES} ${flatbuffers_GENERATED_FILE})
add_dependencies(flatbuffers flatbuffers_dep)
target_include_directories(flatbuffers PUBLIC ${flatbuffers_INCLUDE})
target_link_libraries(flatbuffers PRIVATE Testing::core)
add_test(NAME test_flatbuffers COMMAND flatbuffers)
