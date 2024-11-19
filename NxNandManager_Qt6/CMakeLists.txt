cmake_minimum_required(VERSION 3.29)
project(NxNandManager)

# 设置 C++ 标准和 Qt 配置
set(CMAKE_CXX_STANDARD 17)
set(CMAKE_AUTOMOC ON)
set(CMAKE_AUTORCC ON)
set(CMAKE_AUTOUIC ON)
set(QT_STATIC ON)

# 设置 Qt 安装路径
set(CMAKE_PREFIX_PATH "C:/Qt/build-static")

find_package(Qt6 COMPONENTS
        Core
        Gui
        Widgets
        Network
        REQUIRED)

# 收集源文件
file(GLOB main *.cpp *.h)
file(GLOB gui gui/*.cpp gui/*.h gui/*.ui)
file(GLOB res res/*.cpp res/*.h)
file(GLOB ziplib
        lib/ZipLib/*.cpp
        lib/ZipLib/*.h
        lib/ZipLib/detail/*.cpp
        lib/ZipLib/detail/*.h
        lib/ZipLib/extlibs/bzip2/*.c
        lib/ZipLib/extlibs/bzip2/*.h
        lib/ZipLib/extlibs/lzma/*.c
        lib/ZipLib/extlibs/lzma/*.h
        lib/ZipLib/extlibs/zlib/*.c
        lib/ZipLib/extlibs/zlib/*.h
        lib/ZipLib/utils/*.h)
file(GLOB fatfs lib/fatfs/*)

# 资源文件
qt_add_resources(NxNandManager_RESOURCES
    gui/application.qrc
)

# 添加虚拟文件系统源文件
file(GLOB virtual_fs_sources
    virtual_fs/*.cpp
    virtual_fs/*.h
)

# 创建可执行文件
add_executable(NxNandManager
        ${main}
        ${gui}
        ${res}
        ${ziplib}
        ${fatfs}
        ${NxNandManager_RESOURCES}
        ${virtual_fs_sources}
        gui/appicon.rc
)

# 链接 Qt 库
target_link_libraries(NxNandManager
        Qt::Core
        Qt::Gui
        Qt::Widgets
        Qt::Network
)

# 链接其他依赖库
target_link_libraries(NxNandManager
        dokan1        # Dokan 库
        crypto        # Crypto 库
)

# 设置库文件的查找路径
target_link_directories(NxNandManager PRIVATE
    virtual_fs/dokan/x64/lib        # Dokan 库目录
    C:/OpenSSL/OpenSSL_mingw64/lib  # OpenSSL 库目录
)

# 设置头文件的查找路径
target_include_directories(NxNandManager PRIVATE
    C:/OpenSSL/OpenSSL_mingw64/include
    virtual_fs/dokan/include
    gui/include
)

# 注释掉与动态库复制相关的部分
# 这些是为动态构建准备的，静态构建时无需复制 DLL
# if (WIN32 AND NOT DEFINED CMAKE_TOOLCHAIN_FILE)
#     set(DEBUG_SUFFIX)
#     if (MSVC AND CMAKE_BUILD_TYPE MATCHES "Debug")
#         set(DEBUG_SUFFIX "d")
#     endif ()
#     set(QT_INSTALL_PATH "${CMAKE_PREFIX_PATH}")
#     if (NOT EXISTS "${QT_INSTALL_PATH}/bin")
#         set(QT_INSTALL_PATH "${QT_INSTALL_PATH}/..")
#         if (NOT EXISTS "${QT_INSTALL_PATH}/bin")
#             set(QT_INSTALL_PATH "${QT_INSTALL_PATH}/..")
#         endif ()
#     endif ()
#     if (EXISTS "${QT_INSTALL_PATH}/plugins/platforms/qwindows${DEBUG_SUFFIX}.dll")
#         add_custom_command(TARGET ${PROJECT_NAME} POST_BUILD
#                 COMMAND ${CMAKE_COMMAND} -E make_directory
#                 "$<TARGET_FILE_DIR:${PROJECT_NAME}>/plugins/platforms/")
#         add_custom_command(TARGET ${PROJECT_NAME} POST_BUILD
#                 COMMAND ${CMAKE_COMMAND} -E copy
#                 "${QT_INSTALL_PATH}/plugins/platforms/qwindows${DEBUG_SUFFIX}.dll"
#                 "$<TARGET_FILE_DIR:${PROJECT_NAME}>/plugins/platforms/")
#     endif ()
#     foreach (QT_LIB Core Gui Widgets)
#         add_custom_command(TARGET ${PROJECT_NAME} POST_BUILD
#                 COMMAND ${CMAKE_COMMAND} -E copy
#                 "${QT_INSTALL_PATH}/bin/Qt6${QT_LIB}${DEBUG_SUFFIX}.dll"
#                 "$<TARGET_FILE_DIR:${PROJECT_NAME}>")
#     endforeach (QT_LIB)
# endif ()
