if(NOT WINDOWS OR NOT MSVC)
    message(FATAL_ERROR "KRKR2_SHINKI_EMU currently requires Windows x64/MSVC")
endif()
add_library(shinki_emu_kirikiroid2_web SHARED NativeOwner.cpp NativeSession.cpp NativeInput.cpp)
target_include_directories(shinki_emu_kirikiroid2_web PRIVATE
    "$<TARGET_PROPERTY:krkr2,INCLUDE_DIRECTORIES>"
    "${CMAKE_CURRENT_SOURCE_DIR}/include")
target_compile_definitions(shinki_emu_kirikiroid2_web PRIVATE
    "$<TARGET_PROPERTY:krkr2,COMPILE_DEFINITIONS>")
target_compile_options(shinki_emu_kirikiroid2_web PRIVATE
    "$<TARGET_PROPERTY:krkr2,COMPILE_OPTIONS>")
target_link_libraries(shinki_emu_kirikiroid2_web PRIVATE
    "$<TARGET_PROPERTY:krkr2,LINK_LIBRARIES>")
target_link_options(shinki_emu_kirikiroid2_web PRIVATE
    "$<TARGET_PROPERTY:krkr2,LINK_OPTIONS>")
set_target_properties(shinki_emu_kirikiroid2_web PROPERTIES
    OUTPUT_NAME "shinki_emu_kirikiroid2_web"
    RUNTIME_OUTPUT_DIRECTORY "${CMAKE_CURRENT_BINARY_DIR}/$<0:>"
    ARCHIVE_OUTPUT_DIRECTORY "${CMAKE_CURRENT_BINARY_DIR}/$<0:>")
cocos_copy_target_dll(shinki_emu_kirikiroid2_web)
add_custom_target(shinki_emu_kirikiroid2_web_resources
    COMMAND ${CMAKE_COMMAND} -E copy_directory
        "${GAME_RES_FOLDER}" "$<TARGET_FILE_DIR:shinki_emu_kirikiroid2_web>/Resources"
    VERBATIM)
add_dependencies(shinki_emu_kirikiroid2_web shinki_emu_kirikiroid2_web_resources)
# Test-only static library: C entry points over NativeLifecycle.h for the Rust
# crate's unit tests. It is never linked into the core DLL; it lands next to the
# import library, where the crate's link search finds it.
add_library(shinki_emu_kirikiroid2_web_lifecycle_test STATIC NativeLifecycleTest.cpp)
target_include_directories(shinki_emu_kirikiroid2_web_lifecycle_test PRIVATE
    "${CMAKE_CURRENT_SOURCE_DIR}/include")
target_compile_features(shinki_emu_kirikiroid2_web_lifecycle_test PRIVATE cxx_std_17)
set_target_properties(shinki_emu_kirikiroid2_web_lifecycle_test PROPERTIES
    ARCHIVE_OUTPUT_DIRECTORY "${CMAKE_CURRENT_BINARY_DIR}/$<0:>")
add_dependencies(shinki_emu_kirikiroid2_web shinki_emu_kirikiroid2_web_lifecycle_test)
