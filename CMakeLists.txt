cmake_minimum_required(VERSION 2.8.10)

set(COCOS_SOURCE_DIR ${CMAKE_CURRENT_SOURCE_DIR}/cocos2dx/cocos2dx)

INCLUDE_DIRECTORIES("${COCOS_SOURCE_DIR}"
					"${COCOS_SOURCE_DIR}/include" 
					"${COCOS_SOURCE_DIR}/kazmath/include")
						
file( GLOB COCOS2DX_SRCS 
"${COCOS_SOURCE_DIR}/*.c*" 
"${COCOS_SOURCE_DIR}/actions/*.c*"
"${COCOS_SOURCE_DIR}/base_nodes/*.c*"		
"${COCOS_SOURCE_DIR}/cocoa/*.c*"
"${COCOS_SOURCE_DIR}/effects/*.c*"
"${COCOS_SOURCE_DIR}/extensions/CCBReader/*.c*"
"${COCOS_SOURCE_DIR}/extensions/CCControlExtension/*.c*"
"${COCOS_SOURCE_DIR}/extensions/CCListView/*.c*"
"${COCOS_SOURCE_DIR}/extensions/CCNotificationCenter/*.c*"
"${COCOS_SOURCE_DIR}/extensions/CCScrollView/*.c*"
"${COCOS_SOURCE_DIR}/extensions/CCTextureWatcher/*.c*"
"${COCOS_SOURCE_DIR}/kazmath/src/*.c*"
"${COCOS_SOURCE_DIR}/kazmath/src/GL/*.c*"
"${COCOS_SOURCE_DIR}/keypad_dispatcher/*.c*"
"${COCOS_SOURCE_DIR}/label_nodes/*.c*"
"${COCOS_SOURCE_DIR}/layers_scenes_transitions_nodes/*.c*"
"${COCOS_SOURCE_DIR}/menu_nodes/*.c*"
"${COCOS_SOURCE_DIR}/misc_nodes/*.c*"
"${COCOS_SOURCE_DIR}/particle_nodes/*.c*"
"${COCOS_SOURCE_DIR}/platform/*.c*"
"${COCOS_SOURCE_DIR}/script_support/*.c*"
"${COCOS_SOURCE_DIR}/shaders/*.c*"
"${COCOS_SOURCE_DIR}/sprite_nodes/*.c*"
"${COCOS_SOURCE_DIR}/support/*.c*"
"${COCOS_SOURCE_DIR}/support/data_support/*.c*"
"${COCOS_SOURCE_DIR}/support/image_support/*.c*"
"${COCOS_SOURCE_DIR}/support/zip_support/*.c*" #?todo
"${COCOS_SOURCE_DIR}/text_input_node/*.c*"
"${COCOS_SOURCE_DIR}/textures/*.c*"
"${COCOS_SOURCE_DIR}/tileMap_parallax_nodes/*.c*"
"${COCOS_SOURCE_DIR}/touch_dispatcher/*.c*"
)
				
IF(ANDROID)

	ADD_DEFINITIONS(-DUSE_FILE32API)
	set(CMAKE_LIBRARY_OUTPUT_DIRECTORY "${CMAKE_CURRENT_BINARY_DIR}/${ANDROID_ABI}")
	set( CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -Wall -pedantic -fPIC -std=c99" )
	set( CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS}" )

	set( PREBUILT_DIR "${COCOS_SOURCE_DIR}/platform/third_party/android/prebuilt" )

	INCLUDE_DIRECTORIES(
					${COCOS_SOURCE_DIR}/platform/android 
					${PREBUILT_DIR}/libcurl/include
					${PREBUILT_DIR}/libjpeg/include
					${PREBUILT_DIR}/libpng/include
					${PREBUILT_DIR}/libprofiler/include
					${PREBUILT_DIR}/libtiff/include
					${PREBUILT_DIR}/libxml2/include
				)
	LINK_DIRECTORIES(
					${PREBUILT_DIR}/libcurl/libs/${ANDROID_NDK_ABI_NAME}
					${PREBUILT_DIR}/libjpeg/libs/${ANDROID_NDK_ABI_NAME}
					${PREBUILT_DIR}/libpng/libs/${ANDROID_NDK_ABI_NAME}
					${PREBUILT_DIR}/libprofiler/libs/${ANDROID_NDK_ABI_NAME}
					${PREBUILT_DIR}/libtiff/libs/${ANDROID_NDK_ABI_NAME}
					${PREBUILT_DIR}/libxml2/libs/${ANDROID_NDK_ABI_NAME}
				)
	set(EXTERNAL_LIBS egl curl jpeg png andprof tiff xml2 GLESv2 z log)

	file( GLOB PLATFORM_SRCS 
	"${COCOS_SOURCE_DIR}/platform/android/*.c*" 
	"${COCOS_SOURCE_DIR}/platform/android/jni/*.c*")

		add_library(cocos2dx STATIC ${COCOS2DX_SRCS} ${PLATFORM_SRCS})
					
ELSEIF(WIN32)
	set( PREBUILT_DIR "${COCOS_SOURCE_DIR}/platform/third_party/win32" )
	ADD_DEFINITIONS (-DUNICODE -D_UNICODE -D_USRDLL -DCOCOS2DXWIN32_EXPORTS -DGL_GLEXT_PROTOTYPES )
	
	IF(MSVC)
		#SET( CMAKE_DEBUG_POSTFIX "d" )
		ADD_DEFINITIONS(-D_CRT_SECURE_NO_WARNINGS 
						-D_SCL_SECURE_NO_WARNINGS 
						-wd4251 -wd4244 -wd4334 -wd4005)
    ENDIF(MSVC)
	
	if(NOT MSVC)
	set( CMAKE_C_FLAGS "${CMAKE_C_FLAGS}" )
	set( CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -fpermissive" )
  endif()
  
	INCLUDE_DIRECTORIES(${COCOS_SOURCE_DIR}/platform/win32 
							${GLEW_PATH}
							${XML2_PATH}
							${ZLIB_PATH}
							${PNG_PATH}
							${JPEG_PATH}
							${TIFF_PATH}
							${WINICONV_PATH})
					
	LINK_DIRECTORIES(${PROJECT_SOURCE_DIR}/external/lib)

	add_definitions( "-DPTW32_STATIC_LIB" "-DLIBXML_STATIC")
	set( EXTERNAL_LIBS curl glew32 iconv jpeg opengl32 png pthread tiff ws2_32 xml2 zlib )
	
	file( GLOB PLATFORM_SRCS 
	"${COCOS_SOURCE_DIR}/platform/win32/*.c*" )
	
	add_library(cocos2dx SHARED ${COCOS2DX_SRCS} ${PLATFORM_SRCS} )

ENDIF()

target_link_libraries(cocos2dx  ${EXTERNAL_LIBS})