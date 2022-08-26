cmake_minimum_required(VERSION 3.16)
project(playerdemo VERSION 1.0 LANGUAGES CXX)

set(CMAKE_INCLUDE_CURRENT_DIR ON)
set(CMAKE_AUTOUIC ON)

find_package(QT NAMES Qt5 Qt6 REQUIRED COMPONENTS Core)
find_package(Qt${QT_VERSION_MAJOR} REQUIRED COMPONENTS Gui Widgets)

qt_standard_project_setup()
qt_add_executable(playerdemo WIN32 MACOSX_BUNDLE
    src/CustomSlider.cpp src/CustomSlider.h
    src/about.cpp src/about.h src/about.ui
    src/ctrlbar.cpp src/ctrlbar.h src/ctrlbar.ui
    src/customthread.cpp src/customthread.h
    src/datactl.h
    src/globalhelper.cpp src/globalhelper.h
    src/main.cpp
    src/mainwid.cpp src/mainwid.h src/mainwid.ui
    src/medialist.cpp src/medialist.h
    src/playlist.cpp src/playlist.h src/playlist.ui
    src/settingwid.cpp src/settingwid.h src/settingwid.ui
    src/show.cpp src/show.h src/show.ui
    src/title.cpp src/title.h src/title.ui
    src/videoctl.cpp src/videoctl.h
)
target_link_libraries(playerdemo PRIVATE
    Qt::Core
    Qt::Gui
    Qt::Widgets
)


# Resources:
set(mainwid_resource_files
    "src/res/Player.ico"
    "src/res/fa-solid-900.ttf"
    "src/res/fontawesome-webfont.ttf"
    "src/res/player.png"
    "src/res/qss/about.css"
    "src/res/qss/ctrlbar.css"
    "src/res/qss/mainwid.css"
    "src/res/qss/playlist.css"
    "src/res/qss/show.css"
    "src/res/qss/title.css"
)

qt_add_resources(playerdemo "mainwid"
    PREFIX
        "/"
    BASE
        "src"
    FILES
        ${mainwid_resource_files}
)

if(WIN32)
    target_include_directories(playerdemo PRIVATE
        lib/ffmpeg/include
        lib/sdl/include
        src
    )

    target_link_directories(playerdemo PRIVATE
        ${CMAKE_CURRENT_SOURCE_DIR}/lib/ffmpeg/lib
        ${CMAKE_CURRENT_SOURCE_DIR}/lib/sdl/lib
    )

    target_link_libraries(playerdemo PRIVATE
        # Remove: L${CMAKE_CURRENT_SOURCE_DIR}/lib/ffmpeg/lib
        # Remove: L${CMAKE_CURRENT_SOURCE_DIR}/lib/sdl/lib
        SDL2
        avcodec
        avdevice
        avfilter
        avformat
        avutil
        swresample
        swscale
    )
endif()

if(UNIX)
    target_link_libraries(playerdemo PRIVATE
        SDL2
        avcodec
        avdevice
        avfilter
        avformat
        avutil
        swresample
        swscale
    )
endif()

install(TARGETS playerdemo
    BUNDLE DESTINATION .
    RUNTIME DESTINATION ${CMAKE_INSTALL_BINDIR}
)

qt_generate_deploy_app_script(
    TARGET playerdemo
    FILENAME_VARIABLE deploy_script
    NO_UNSUPPORTED_PLATFORM_ERROR
)
install(SCRIPT ${deploy_script})
