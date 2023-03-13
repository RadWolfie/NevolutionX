cmake_minimum_required(VERSION 3.7)

project(NevolutionX)

include(FindPkgConfig)

find_package(Threads REQUIRED)

# NOTE: install sdl2-dev, then it is happy
find_package(SDL2 REQUIRED)

# NOTE: install sdl2_ttf-dev, then it is happy
pkg_search_module(SDL2TTF REQUIRED SDL2_ttf)
# NOTE: install sdl2_image-dev, then it is happy
pkg_search_module(SDL2IMAGE REQUIRED SDL2_image)

include_directories(
  ${CMAKE_SOURCE_DIR}
  ${SDL2_INCLUDE_DIRS}
  ${THREAD_INCLUDE_DIRS}
  ${SDL2TTF_INCLUDE_DIRS}
  ${SDL2IMAGE_INCLUDE_DIRS}
  ${PROJECT_SOURCE_DIR}/Includes
)

if(NXDK)
# additional source files
set(XBOX_SOURCE_FILES
 Sources/networking.cpp
)

# custom configs
# Set a different xbe title other than executable name
#set(XBE_TITLE "<EXE_NAME>")
# Set name of your iso output
set(GEN_XISO "${PROJECT_NAME}.iso")
set(BIN_DIR "/bin")
endif()

add_executable(${PROJECT_NAME}
  main.cpp
  Sources/audioMenu.cpp
  Sources/config.cpp
  Sources/font.cpp
  Sources/ftpConnection.cpp
  Sources/ftpServer.cpp
  Sources/infoLog.cpp
  Sources/langMenu.cpp
  Sources/logViewer.cpp
  Sources/logViewerMenu.cpp
  Sources/menu.cpp
  Sources/networkManager.cpp
  Sources/renderer.cpp
  Sources/settingsMenu.cpp
  Sources/sntpClient.cpp
  Sources/subAppRouter.cpp
  Sources/subsystems.cpp
  Sources/timing.cpp
  Sources/timeMenu.cpp
  Sources/videoMenu.cpp
  Sources/wipeCache.cpp
  Sources/xbeLauncher.cpp
  Sources/xbeScanner.cpp
  3rdparty/SDL_FontCache/SDL_FontCache.c
  ${XBOX_SOURCE_FILES}
)

set_property(TARGET ${PROJECT_NAME} PROPERTY CXX_STANDARD 17)

target_link_libraries(${PROJECT_NAME}
  ${CMAKE_THREAD_LIBS_INIT}
  SDL2
  ${SDL2TTF_LIBRARIES}
  ${SDL2IMAGE_LIBRARIES}
)

file(GLOB RESOURCE_FILES
  Resources/*
)

file(COPY ${RESOURCE_FILES} DESTINATION ${${PROJECT_NAME}_BINARY_DIR}${BIN_DIR})
file(COPY_FILE sampleconfig.json ${${PROJECT_NAME}_BINARY_DIR}${BIN_DIR}/config.json)

if(MSVC)
  target_compile_options(${PROJECT_NAME} PRIVATE /W4 /WX)
else()
  target_compile_options(${PROJECT_NAME} PRIVATE -Wall -Wextra -pedantic)
endif()

#set (CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS_DEBUG} -fno-omit-frame-pointer -fsanitize=address")
#set (CMAKE_LINKER_FLAGS "${CMAKE_LINKER_FLAGS_DEBUG} -fno-omit-frame-pointer -fsanitize=address")

# Finally, why did nxdk's CXX compiler failing???
#[[ From the log:
[cmake] -- Detecting CXX compiler ABI info
[cmake] -- Detecting CXX compiler ABI info - failed
[cmake] -- Check for working CXX compiler: /usr/src/nxdk/bin/nxdk-cxx
[cmake] -- Check for working CXX compiler: /usr/src/nxdk/bin/nxdk-cxx - works
[cmake] -- Detecting CXX compile features
[cmake] -- Detecting CXX compile features - failed
#]]
