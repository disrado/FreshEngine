cmake_minimum_required(VERSION 3.10)

project(db LANGUAGES CXX)

include(cotire)

set(HEADERS_DIR ${CMAKE_CURRENT_LIST_DIR}/../../../include)
set(SOURCES_DIR ${CMAKE_CURRENT_LIST_DIR}/)

set(${PROJECT_NAME}_HEADERS
	${HEADERS_DIR}/flame/db/Aliases.hpp
	${HEADERS_DIR}/flame/db/Pool.hpp
	${HEADERS_DIR}/flame/db/Worker.hpp
)

set(${PROJECT_NAME}_SOURCES
	${SOURCES_DIR}/Pool.cpp
	${SOURCES_DIR}/Worker.cpp
)

set(${PROJECT_NAME}_MISC
	${SOURCES_DIR}/pch.hpp
)

add_library(${PROJECT_NAME} SHARED
		${${PROJECT_NAME}_SOURCES}
		${${PROJECT_NAME}_WORKERS_SOURCES}
		${${PROJECT_NAME}_MISC}
)

add_library(flame::db ALIAS ${PROJECT_NAME})

target_compile_features(${PROJECT_NAME} PUBLIC cxx_std_17)

target_compile_options(${PROJECT_NAME}
	PRIVATE
		$<$<CXX_COMPILER_ID:MSVC>:
			/MP /W3 /Zf
			$<$<CONFIG:Debug>:/MTd>
			$<$<CONFIG:Release>:/MT>>
		$<$<OR:$<CXX_COMPILER_ID:Clang>,$<CXX_COMPILER_ID:GNU>>:
			-Wall -Wextra -Werror -Wpedantic -pedantic-errors -pipe>
)

target_link_libraries(${PROJECT_NAME}
	PUBLIC
		${CONAN_LIBS}
		pqxx_shared
		flame::utils
)

target_include_directories(${PROJECT_NAME}
	PUBLIC
		${CONAN_LIBS}
		${HEADERS_DIR}
)

set(COTIRE_MINIMUM_NUMBER_OF_TARGET_SOURCES 1)
set_target_properties(${PROJECT_NAME}
	PROPERTIES
		COTIRE_CXX_PREFIX_HEADER_INIT ${SOURCES_DIR}/pch.hpp)
cotire(${PROJECT_NAME})
