cmake_minimum_required(VERSION 2.8.5)

fips_begin_sharedlib(SDL2_gpu)

fips_deps(SDL2)

set(SDL_gpu_SRCS
    SDL_gpu/src/SDL_gpu.c
    SDL_gpu/src/SDL_gpu_matrix.c
    SDL_gpu/src/SDL_gpu_renderer.c
    SDL_gpu/src/SDL_gpu_shapes.c
    SDL_gpu/src/renderer_OpenGL_1_BASE.c
    SDL_gpu/src/renderer_OpenGL_1.c
    SDL_gpu/src/renderer_OpenGL_2.c
    SDL_gpu/src/renderer_OpenGL_3.c
    SDL_gpu/src/renderer_OpenGL_4.c
    SDL_gpu/src/renderer_GLES_1.c
    SDL_gpu/src/renderer_GLES_2.c
    SDL_gpu/src/renderer_GLES_3.c
)

set(SDL_gpu_HDRS
    SDL_gpu/include/SDL_gpu.h
    SDL_gpu/include/SDL_gpu_RendererImpl.h
    SDL_gpu/include/SDL_gpu_OpenGL_1_BASE.h
    SDL_gpu/include/SDL_gpu_OpenGL_1.h
    SDL_gpu/include/SDL_gpu_OpenGL_2.h
    SDL_gpu/include/SDL_gpu_OpenGL_3.h
    SDL_gpu/include/SDL_gpu_OpenGL_4.h
    SDL_gpu/include/SDL_gpu_GLES_1.h
    SDL_gpu/include/SDL_gpu_GLES_2.h
    SDL_gpu/include/SDL_gpu_GLES_3.h
    SDL_gpu/src/renderer_GL_common.inl
    SDL_gpu/src/renderer_shapes_GL_common.inl
)

# GLES enabled only for Android
if(FIPS_ANDROID)
    find_library( ANDROID_LOG_LIBRARY log )
    find_library( ANDROID_GLES2_LIBRARY GLESv2 )
    find_library( ANDROID_GLES1_LIBRARY GLESv1_CM )
    link_libraries(
        ${ANDROID_LOG_LIBRARY}
        ${ANDROID_GLES2_LIBRARY}
        ${ANDROID_GLES1_LIBRARY}
    )
	add_definitions("-DSDL_GPU_DISABLE_OPENGL")
else()
	find_package(OpenGL REQUIRED)
	include_directories(${OPENGL_INCLUDE_DIR})
    set(SDL_gpu_GL_LIBRARIES ${OPENGL_LIBRARIES})
	add_definitions("-DSDL_GPU_DISABLE_GLES")
endif()

# target_link_libraries(SDL_gpu_shared ${SDL_gpu_GL_LIBRARIES})
fips_libs(${SDL_gpu_GL_LIBRARIES})


# Using bundled glew
SET(SDL_gpu_SRCS ${SDL_gpu_SRCS} SDL_gpu/src/externals/glew/glew.c)
SET(SDL_gpu_HDRS ${SDL_gpu_HDRS} SDL_gpu/src/externals/glew/GL/glew.h SDL_gpu/src/externals/glew/GL/glxew.h SDL_gpu/src/externals/glew/GL/wglew.h)
fips_include_directories(SDL_gpu/src/externals/glew/GL)
add_definitions("-DGLEW_STATIC")

# Using bundlew stbi
SET(SDL_gpu_SRCS ${SDL_gpu_SRCS} SDL_gpu/src/externals/stb_image/stb_image.c)
SET(SDL_gpu_HDRS ${SDL_gpu_HDRS} SDL_gpu/src/externals/stb_image/stb_image.h)
SET(SDL_gpu_SRCS ${SDL_gpu_SRCS} SDL_gpu/src/externals/stb_image_write/stb_image_write.c)
SET(SDL_gpu_HDRS ${SDL_gpu_HDRS} SDL_gpu/src/externals/stb_image_write/stb_image_write.h)
fips_include_directories(SDL_gpu/src/externals/stb_image)
fips_include_directories(SDL_gpu/src/externals/stb_image_write)

# Various definitions
add_definitions("-Wall -std=c99 -pedantic")

fips_files(${SDL_gpu_SRCS} ${SDL_gpu_HDRS})

fips_end_sharedlib()
