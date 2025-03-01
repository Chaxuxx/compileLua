cmake_minimum_required(VERSION 3.10)

# 设置项目名称和版本
project(lua VERSION 5.4.7)

# 查找源文件并将其添加到构建中
file(GLOB SOURCES src/*.c)
add_library(lua STATIC ${SOURCES})

# 包含头文件目录
target_include_directories(lua PUBLIC ${PROJECT_SOURCE_DIR}/src)
if(LINUX)
    target_link_libraries(lua m)
endif()

# 定义安装路径变量
set(INSTALL_BIN "${PROJECT_SOURCE_DIR}/bin")
set(INSTALL_INC "${PROJECT_SOURCE_DIR}/include")
set(INSTALL_LIB "${PROJECT_SOURCE_DIR}/lib")
# set(INSTALL_MAN "${CMAKE_INSTALL_PREFIX}/man/man1")
# set(INSTALL_LMOD "${PROJECT_SOURCE_DIR}/share/lua/${PROJECT_VERSION_MAJOR}.${PROJECT_VERSION_MINOR}")
# set(INSTALL_CMOD "${PROJECT_SOURCE_DIR}/lib/lua/${PROJECT_VERSION_MAJOR}.${PROJECT_VERSION_MINOR}")

# 安装目标
install(TARGETS lua 
        ARCHIVE DESTINATION ${INSTALL_LIB}
        LIBRARY DESTINATION ${INSTALL_LIB}
        RUNTIME DESTINATION ${INSTALL_BIN})

install(FILES ${PROJECT_SOURCE_DIR}/src/lua.h 
              ${PROJECT_SOURCE_DIR}/src/luaconf.h 
              ${PROJECT_SOURCE_DIR}/src/lualib.h 
              ${PROJECT_SOURCE_DIR}/src/lauxlib.h 
              ${PROJECT_SOURCE_DIR}/src/lua.hpp 
        DESTINATION ${INSTALL_INC})

# 创建lua_cli可执行文件，并链接到lua库
add_executable(lua_cli src/lua.c)
target_link_libraries(lua_cli PRIVATE lua) # 连接lua静态库

# 创建luac可执行文件，并链接到lua库
add_executable(luac src/luac.c)
target_link_libraries(luac PRIVATE lua) # 连接lua静态库

install(TARGETS luac RUNTIME DESTINATION ${INSTALL_BIN})
install(TARGETS lua_cli
        RUNTIME DESTINATION ${INSTALL_BIN}
        RENAME lua) # 在安装时重命名为lua

# 创建必要的目录结构用于安装
# file(MAKE_DIRECTORY ${PROJECT_BINARY_DIR}/man)
# install(DIRECTORY ${PROJECT_SOURCE_DIR}/doc/ DESTINATION ${INSTALL_MAN} FILES_MATCHING PATTERN "*.1")

# 设置默认安装前缀
# if(CMAKE_INSTALL_PREFIX_INITIALIZED_TO_DEFAULT)
#    set(CMAKE_INSTALL_PREFIX "/usr/local" CACHE PATH "Install top-level directory (default /usr/local)" FORCE)
# endif()

# 输出配置信息
message(STATUS "Lua version: ${PROJECT_VERSION}")
message(STATUS "Installation paths:")
message(STATUS "  Binaries: ${INSTALL_BIN}")
message(STATUS "  Headers: ${INSTALL_INC}")
message(STATUS "  Libraries: ${INSTALL_LIB}")
message(STATUS "  Manuals: ${INSTALL_MAN}")
