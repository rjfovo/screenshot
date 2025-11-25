cmake_minimum_required(VERSION 3.21)

project(cutefish-screenshot LANGUAGES CXX)

set(CMAKE_INCLUDE_CURRENT_DIR ON)

set(CMAKE_AUTOUIC ON)
set(CMAKE_AUTOMOC ON)
set(CMAKE_AUTORCC ON)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

# 查找Qt6组件
find_package(Qt6 COMPONENTS Core DBus Gui Widgets Quick LinguistTools REQUIRED)

# 设置项目源文件
set(PROJECT_SOURCES
    src/main.cpp
    src/screenshotview.cpp
    qml.qrc
)

# 创建可执行文件
add_executable(cutefish-screenshot
  ${PROJECT_SOURCES}
)

# 链接Qt6库
target_link_libraries(cutefish-screenshot
  PRIVATE
  Qt6::Core
  Qt6::DBus
  Qt6::Gui
  Qt6::Widgets
  Qt6::Quick
)

# 处理翻译文件 - Qt6方式
file(GLOB TS_FILES translations/*.ts)

# 创建更新翻译目标 - 修复：添加了 SOURCES 参数
qt_add_lupdate(
    cutefish-screenshot
    SOURCES ${PROJECT_SOURCES}
    TS_FILES ${TS_FILES}
)

# 创建发布翻译目标
qt_add_lrelease(
    cutefish-screenshot
    TS_FILES ${TS_FILES}
    QM_FILES_OUT QM_FILES
)

# 将生成的质量管理文件添加为依赖项
add_dependencies(cutefish-screenshot cutefish-screenshot_lrelease)

# 安装质量管理文件
install(FILES ${QM_FILES} DESTINATION /usr/share/cutefish-screenshot/translations)

# 安装可执行文件
install(TARGETS cutefish-screenshot RUNTIME DESTINATION ${CMAKE_INSTALL_BINDIR})

# 安装桌面文件
install(FILES
    cutefish-screenshot.desktop
    DESTINATION /usr/share/applications/
    COMPONENT Runtime
)