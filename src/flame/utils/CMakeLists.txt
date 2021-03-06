cmake_minimum_required(VERSION 3.10)

project(utils LANGUAGES CXX)

include(cotire)

set(HEADERS_DIR ${CMAKE_CURRENT_LIST_DIR}/../../../include)
set(SOURCES_DIR ${CMAKE_CURRENT_LIST_DIR}/)

set(${PROJECT_NAME}_HEADERS
	${HEADERS_DIR}/flame/utils/Timer.hpp
)

set(${PROJECT_NAME}_SOURCES
	${SOURCES_DIR}/Timer.cpp
)

set(${PROJECT_NAME}_MISC
	${SOURCES_DIR}/pch.hpp
)

add_library(${PROJECT_NAME} SHARED
		${${PROJECT_NAME}_HEADERS}
		${${PROJECT_NAME}_SOURCES}
		${${PROJECT_NAME}_MISC}
)

add_library(flame::utils ALIAS ${PROJECT_NAME})

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

target_include_directories(${PROJECT_NAME}
	PUBLIC
		${HEADERS_DIR}
)

set(COTIRE_MINIMUM_NUMBER_OF_TARGET_SOURCES 1)
set_target_properties(${PROJECT_NAME}
	PROPERTIES
		COTIRE_CXX_PREFIX_HEADER_INIT ${SOURCES_DIR}/pch.hpp)
cotire(${PROJECT_NAME})
