if(NOT WINDOWS OR NOT MSVC)
    message(FATAL_ERROR "KRKR2_ASTRA_EMU currently requires Windows x64/MSVC")
endif()
add_library(astra_emu_kirikiroid2_web SHARED NativeOwner.cpp NativeSession.cpp NativeInput.cpp)
target_include_directories(astra_emu_kirikiroid2_web PRIVATE
    "$<TARGET_PROPERTY:krkr2,INCLUDE_DIRECTORIES>"
    "${CMAKE_CURRENT_SOURCE_DIR}/include")
target_compile_definitions(astra_emu_kirikiroid2_web PRIVATE
    "$<TARGET_PROPERTY:krkr2,COMPILE_DEFINITIONS>")
target_compile_options(astra_emu_kirikiroid2_web PRIVATE
    "$<TARGET_PROPERTY:krkr2,COMPILE_OPTIONS>")
target_link_libraries(astra_emu_kirikiroid2_web PRIVATE
    "$<TARGET_PROPERTY:krkr2,LINK_LIBRARIES>")
target_link_options(astra_emu_kirikiroid2_web PRIVATE
    "$<TARGET_PROPERTY:krkr2,LINK_OPTIONS>")
set_target_properties(astra_emu_kirikiroid2_web PROPERTIES
    OUTPUT_NAME "astra_emu_kirikiroid2_web"
    RUNTIME_OUTPUT_DIRECTORY "${CMAKE_RUNTIME_OUTPUT_DIRECTORY}")
cocos_copy_target_dll(astra_emu_kirikiroid2_web)
add_custom_command(TARGET astra_emu_kirikiroid2_web POST_BUILD
    COMMAND ${CMAKE_COMMAND} -E copy_directory
        "${GAME_RES_FOLDER}" "$<TARGET_FILE_DIR:astra_emu_kirikiroid2_web>/Resources"
    VERBATIM)
# Test-only static library: C entry points over NativeLifecycle.h for the Rust
# crate's unit tests. It is never linked into the core DLL; it lands next to the
# import library, where the crate's link search finds it.
add_library(astra_emu_kirikiroid2_web_lifecycle_test STATIC NativeLifecycleTest.cpp)
target_include_directories(astra_emu_kirikiroid2_web_lifecycle_test PRIVATE
    "${CMAKE_CURRENT_SOURCE_DIR}/include")
target_compile_features(astra_emu_kirikiroid2_web_lifecycle_test PRIVATE cxx_std_17)
add_dependencies(astra_emu_kirikiroid2_web astra_emu_kirikiroid2_web_lifecycle_test)
