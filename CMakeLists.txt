cmake_minimum_required(VERSION 3.10)
project(FacExtractor)

# Configura vcpkg
set(CMAKE_TOOLCHAIN_FILE "C:/users/Shadow/vcpkg/scripts/buildsystems/vcpkg.cmake")
set(CMAKE_PREFIX_PATH "C:/users/Shadow/vcpkg/installed/x64-windows")

# Trova package
find_package(dlib REQUIRED)
find_package(Qt5 COMPONENTS Widgets REQUIRED)
find_package(OpenCV REQUIRED)

set(CMAKE_WINDOWS_EXPORT_ALL_SYMBOLS TRUE)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

set(CMAKE_AUTOMOC ON)


# Specifica i file sorgente, header e UI
set(SOURCE_FILES
    main.cpp
    mainwindow.cpp
    # Aggiungi qui altri file sorgente
)
set(HEADER_SET
    mainwindow.h
    ui_mainwindow.h
    # Aggiungi qui altri file header
)
set(UI_FILES
    mainwindow.ui
    # Aggiungi qui altri file UI
)


# Aggiungi il tuo eseguibile
add_executable(FacExtractor ${SOURCE_FILES} ${HEADER_SET} ${UI_HEADERS})

# Link dlib e OpenCV
target_link_libraries(FacExtractor Qt5::Widgets dlib::dlib ${OpenCV_LIBS})

# Funzione per copiare i file DLL nella directory di output
function(copy_qt_dlls TARGET_NAME)
    get_target_property(Qt5Core_location Qt5::Core LOCATION)
    get_target_property(Qt5Widgets_location Qt5::Widgets LOCATION)

    add_custom_command(TARGET ${TARGET_NAME} POST_BUILD
        COMMAND ${CMAKE_COMMAND} -E copy_if_different
            ${Qt5Core_location}
            ${Qt5Widgets_location}
            $<TARGET_FILE_DIR:${TARGET_NAME}>
    )
endfunction()

function(copy_opencv_dlls TARGET_NAME)
    foreach(opencv_lib ${OpenCV_LIBS})
        get_target_property(opencv_dll ${opencv_lib} LOCATION)
        if(opencv_dll)
            add_custom_command(TARGET ${TARGET_NAME} POST_BUILD
                COMMAND ${CMAKE_COMMAND} -E copy_if_different
                    ${opencv_dll}
                    $<TARGET_FILE_DIR:${TARGET_NAME}>
            )
        endif()
    endforeach()
endfunction()

# Copia le DLL dopo la compilazione
copy_qt_dlls(FacExtractor)
copy_opencv_dlls(FacExtractor)